---
title: 驗證和授權
description: 本章說明 eShopOnContainers 行動應用程式如何針對容器化微服務執行驗證和授權。
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b5d52f92b88a3b0dbcd21a64b647c6d75e6c6ca1
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374936"
---
# <a name="authentication-and-authorization"></a>驗證和授權

> [!NOTE]
> 此電子書已在2017的春季發行，且自那時起尚未更新。 本書中有很多工具價值，但有些材質已過期。

驗證是取得身分識別認證的程式，例如使用者的名稱和密碼，以及對授權單位驗證這些認證。 如果認證有效，提交認證的實體會被視為已驗證的身分識別。 一旦身分識別經過驗證，授權程式就會判斷該身分識別是否可存取指定的資源。

有許多方法可將驗證和授權整合至 Xamarin.Forms 與 ASP.NET MVC web 應用程式通訊的應用程式中，包括使用 ASP.NET Core 身分識別、外部驗證提供者（例如 Microsoft、Google、Facebook 或 Twitter）和驗證中介軟體。 EShopOnContainers 行動應用程式會使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 行動裝置應用程式會向 IdentityServer 要求安全性權杖，以驗證使用者或存取資源。 若要讓 IdentityServer 代表使用者發出權杖，使用者必須登入 IdentityServer。 但是，IdentityServer 不會提供使用者介面或資料庫進行驗證。 因此，在 eShopOnContainers 參考應用程式中，ASP.NET Core 身分識別會用於此用途。

## <a name="authentication"></a>驗證

當應用程式需要知道目前使用者的身分識別時，需要進行驗證。 ASP.NET Core 用來識別使用者的主要機制是 ASP.NET Core 身分識別成員資格系統，會將使用者資訊儲存在開發人員所設定的資料存放區中。 一般來說，此資料存放區將會是 EntityFramework 存放區，但自訂存放區或協力廠商套件可用來將身分識別資訊儲存在 Azure 儲存體、Azure Cosmos DB 或其他位置。

若為使用本機使用者資料存放區的驗證案例，以及透過 cookie 在要求之間保存身分識別資訊 (如 ASP.NET MVC web 應用程式) 中一般，ASP.NET Core 身分識別是適合的解決方案。 不過，cookie 並非一律是保存和傳輸資料的自然方式。 例如，公開從行動裝置應用程式存取之 RESTful 端點的 ASP.NET Core web 應用程式通常需要使用持有人權杖驗證，因為在此案例中無法使用 cookie。 不過，您可以輕鬆地取出持有人權杖，並將其包含在從行動裝置應用程式提出的 web 要求的授權標頭中。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>使用 IdentityServer 4 發佈持有人權杖

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4) 是適用于 ASP.NET Core 的開放原始碼 OpenID Connect 和 OAuth 2.0 架構，可用於許多驗證和授權案例，包括為本機 ASP.NET Core 身分識別使用者發出安全性權杖。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 都非常類似，但有不同的責任。

OpenID Connect 是在 OAuth 2.0 通訊協定之上的驗證層。 OAuth 2 是一種通訊協定，可讓應用程式向安全性權杖服務要求存取權杖，並使用它們來與 Api 通訊。 這項委派可降低用戶端應用程式和 Api 的複雜度，因為驗證和授權可以集中進行。

OpenID Connect 和 OAuth 2.0 的組合結合了驗證和 API 存取的兩個基本安全性考慮，而 IdentityServer 4 是這些通訊協定的執行。

在使用直接用戶端對微服務通訊的應用程式（例如 eShopOnContainers 參考應用程式）中，做為安全性權杖服務的專用驗證微服務 (STS) 可以用來驗證使用者，如圖9-1 所示。 如需有關直接用戶端對微服務通訊的詳細資訊，請參閱 [用戶端與微服務之間的通訊](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication-between-client-and-microservices)。

![專用驗證微服務的驗證](authentication-and-authorization-images/authentication.png)

**圖9-1：** 專用驗證微服務的驗證

EShopOnContainers 行動裝置應用程式會與身分識別微服務通訊，此身分識別會使用 IdentityServer 4 來執行驗證和 Api 的存取控制。 因此，行動應用程式會要求來自 IdentityServer 的權杖，以驗證使用者或存取資源：

- 使用 IdentityServer 來驗證使用者是由要求身分 *識別* 權杖的行動應用程式所達成，這代表驗證程式的結果。 它最少包含使用者的識別碼，以及使用者驗證方式和時間的相關資訊。 它也可以包含其他身分識別資料。
- 使用 IdentityServer 存取資源是由要求 *存取* 權杖的行動應用程式所完成，以允許存取 API 資源。 用戶端會要求存取權杖，並將其轉送至 API。 存取權杖包含用戶端的相關資訊，以及使用者 (（如果有) ）。 Api 接著會使用該資訊來授權其資料的存取權。

> [!NOTE]
> 用戶端必須先向 IdentityServer 註冊，才能要求權杖。

### <a name="adding-identityserver-to-a-web-application"></a>將 IdentityServer 新增至 Web 應用程式

為了讓 ASP.NET Core 的 web 應用程式使用 IdentityServer 4，必須將它加入至 web 應用程式的 Visual Studio 解決方案。 如需詳細資訊，請參閱 IdentityServer 檔中的 [總覽](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html) 。

一旦 IdentityServer 包含在 web 應用程式的 Visual Studio 方案中，就必須將其新增至 web 應用程式的 HTTP 要求處理管線，如此它才能提供 OpenID Connect 和 OAuth 2.0 端點的要求。 這可在 `Configure` web 應用程式類別的方法中完成 `Startup` ，如下列程式碼範例所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

在 web 應用程式的 HTTP 要求處理管線中，排序是很重要的。 因此，必須在執行登入畫面的 UI 架構之前，將 IdentityServer 新增至管線。

### <a name="configuring-identityserver"></a>設定 IdentityServer

IdentityServer 應該在 `ConfigureServices` web 應用程式類別的方法中設定，方法是 `Startup` 呼叫 `services.AddIdentityServer` 方法，如下列來自 eShopOnContainers 參考應用程式的程式碼範例所示：

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

呼叫方法之後 `services.AddIdentityServer` ，會呼叫額外的流暢 api 來設定下列各項：

- 用於簽署的認證。
- 使用者可能要求存取的 API 和身分識別資源。
- 將連接到要求權杖的用戶端。
- ASP.NET Core 身分識別。

> [!TIP]
> 動態載入 IdentityServer 4 設定。 IdentityServer 4 的 Api 可讓您從設定物件的記憶體內部清單設定 IdentityServer。 在 eShopOnContainers 參考應用程式中，這些記憶體中的集合都已硬式編碼到應用程式中。 不過，在實際執行案例中，可以從設定檔或資料庫動態載入它們。

如需有關設定 IdentityServer 使用 ASP.NET Core 身分識別的詳細資訊，請參閱 IdentityServer 檔中的 [使用 ASP.NET Core 身分識別](https://identityserver4.readthedocs.io/en/latest/quickstarts/6_aspnet_identity.html) 。

#### <a name="configuring-api-resources"></a>設定 API 資源

設定 API 資源時，此 `AddInMemoryApiResources` 方法需要 `IEnumerable<ApiResource>` 集合。 下列程式碼範例顯示在 `GetApis` eShopOnContainers 參考應用程式中提供此集合的方法：

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

這個方法會指定 IdentityServer 應保護 orders 和購物籃 Api。 因此，對這些 Api 進行呼叫時，將需要 IdentityServer managed 存取權杖。 如需類型的詳細資訊 `ApiResource` ，請參閱 IdentityServer 4 檔中的 [API 資源](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html) 。

#### <a name="configuring-identity-resources"></a>設定身分識別資源

設定身分識別資源時，此 `AddInMemoryIdentityResources` 方法需要 `IEnumerable<IdentityResource>` 集合。 身分識別資源是資料，例如使用者識別碼、名稱或電子郵件地址。 每個身分識別資源都有唯一的名稱，而且可以指派任意的宣告類型，然後將它包含在使用者的身分識別權杖中。 下列程式碼範例顯示在 `GetResources` eShopOnContainers 參考應用程式中提供此集合的方法：

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

OpenID Connect 規格會指定一些 [標準身分識別資源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低需求是提供支援以發出使用者的唯一識別碼。 這是藉由公開身分 `IdentityResources.OpenId` 識別資源來達成。

> [!NOTE]
> `IdentityResources`類別支援 OpenID Connect 規格中定義的所有範圍 (OpenID、電子郵件、設定檔、電話和位址) 。

IdentityServer 也支援定義自訂身分識別資源。 如需詳細資訊，請參閱 IdentityServer 檔中的 [定義自訂身分識別資源](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources) 。 如需類型的詳細資訊 `IdentityResource` ，請參閱 IdentityServer 4 檔中的身分 [識別資源](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html) 。

#### <a name="configuring-clients"></a>設定用戶端 \(英文\)

用戶端是可以從 IdentityServer 要求權杖的應用程式。 一般而言，您必須為每個用戶端定義下列設定，最少：

- 唯一的用戶端識別碼。
- 允許與權杖服務的互動 (稱為授與類型) 。
- 身分識別和存取權杖傳送到 (的位置) ，稱為重新導向 URI。
- 允許用戶端存取 (稱為範圍) 的資源清單。

設定用戶端時， `AddInMemoryClients` 方法預期會有一個 `IEnumerable<Client>` 集合。 下列程式碼範例顯示在 `GetClients` eShopOnContainers 參考應用程式中提供此集合的方法中，eShopOnContainers 行動應用程式的設定：

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

此設定會指定下列屬性的資料：

- `ClientId`：用戶端的唯一識別碼。
- `ClientName`：用戶端顯示名稱，用於記錄和同意畫面。
- `AllowedGrantTypes`：指定用戶端想要如何與 IdentityServer 互動。 如需詳細資訊，請參閱設定 [驗證流程](#configuring-the-authentication-flow)。
- `ClientSecrets`：指定從權杖端點要求權杖時所使用的用戶端密碼認證。
- `RedirectUris`：指定要傳回權杖或授權碼的允許 Uri。
- `RequireConsent`：指定是否需要同意畫面。
- `RequirePkce`：指定使用授權碼的用戶端是否必須傳送證明金鑰。
- `PostLogoutRedirectUris`：指定在登出後重新導向的允許 Uri。
- `AllowedCorsOrigins`：指定用戶端的來源，讓 IdentityServer 可以允許來自來源的跨原始來源呼叫。
- `AllowedScopes`：指定用戶端可以存取的資源。 根據預設，用戶端無法存取任何資源。
- `AllowOfflineAccess`：指定用戶端是否可以要求重新整理權杖。

#### <a name="configuring-the-authentication-flow"></a>設定驗證流程

您可以藉由在屬性中指定授與類型，來設定用戶端和 IdentityServer 之間的驗證流程 `Client.AllowedGrantTypes` 。 OpenID Connect 和 OAuth 2.0 規格會定義許多驗證流程，包括：

- 隱含。 此流程已針對瀏覽器型應用程式優化，而且應該用於僅限使用者驗證，或驗證和存取權杖要求。 所有權杖都是透過瀏覽器進行傳送，因此不允許重新整理權杖等 advanced 功能。
- 授權碼。 此流程可讓您在後端通道上取得權杖，而不是瀏覽器前端通道，同時也支援用戶端驗證。
- 混合式。 此流程是隱含和授權碼授與類型的組合。 身分識別權杖是透過瀏覽器通道傳輸，並包含已簽署的通訊協定回應以及其他成品，例如授權碼。 成功驗證回應之後，應使用後端通道來取得存取和重新整理權杖。

> [!TIP]
> 使用混合式驗證流程。 混合式驗證流程可減少一些適用于瀏覽器通道的攻擊，而且是原生應用程式的建議流程，這些應用程式想要 (取得存取權杖，並可能) 重新整理權杖。

如需驗證流程的詳細資訊，請參閱 IdentityServer 4 檔中的 [授與類型](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html) 。

### <a name="performing-authentication"></a>執行驗證

若要讓 IdentityServer 代表使用者發出權杖，使用者必須登入 IdentityServer。 但是，IdentityServer 不會提供使用者介面或資料庫進行驗證。 因此，在 eShopOnContainers 參考應用程式中，ASP.NET Core 身分識別會用於此用途。

EShopOnContainers 行動裝置應用程式會使用 IdentityServer 搭配混合式驗證流程進行驗證，如圖9-2 所示。

![登入流程的高階總覽](authentication-and-authorization-images/sign-in.png)

**圖9-2：** 登入流程的高階總覽

提出登入要求 `<base endpoint>:5105/connect/authorize` 。 成功驗證之後，IdentityServer 會傳回包含授權碼和身分識別權杖的驗證回應。 授權碼接著會傳送至 `<base endpoint>:5105/connect/token` ，以回應存取、身分識別和重新整理權杖。

EShopOnContainers 行動裝置應用程式會透過將要求傳送至 `<base endpoint>:5105/connect/endsession` ，並使用其他參數來登出 IdentityServer。 登出之後，IdentityServer 會透過將 post 登出重新導向 URI 傳送回行動裝置應用程式來回應。 圖9-3 說明此流程。

![登出流程的高階總覽](authentication-and-authorization-images/sign-out.png)

**圖9-3：** 登出流程的高階總覽

在 eShopOnContainers 行動裝置應用程式中，與 IdentityServer 的通訊是由執行介面的類別所執行 `IdentityService` `IIdentityService` 。 這個介面會指定執行類別必須提供 `CreateAuthorizationRequest` 、 `CreateLogoutRequest` 和 `GetTokenAsync` 方法。

#### <a name="signing-in"></a>登入

當使用者在上按下 [ **登** 入] 按鈕時 `LoginView` ， `SignInCommand` `LoginViewModel` 會執行類別中的，然後再執行 `SignInAsync` 方法。 下列程式碼範例示範此方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

這個方法會叫 `CreateAuthorizationRequest` 用類別中的方法 `IdentityService` ，如下列程式碼範例所示：

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.Callback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

這個方法會使用必要的參數來建立 IdentityServer [授權端點](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)的 URI。 授權端點位於 `/connect/authorize` 以使用者設定公開之基底端點的埠5105。 如需使用者設定的詳細資訊，請參閱設定 [管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 行動應用程式的受攻擊面，是藉由針對 OAuth 的程式碼交換 (PKCE) 擴充功能，來降低攻擊面。 PKCE 會防止授權碼遭到攔截，而無法使用。 這是由產生秘密驗證器的用戶端達成的，這是在授權要求中傳遞的雜湊，在兌換授權碼時，會顯示為未雜湊。 如需 PKCE 的詳細資訊，請參閱網際網路工程任務推動小組網站上的 [OAuth 公用用戶端程式代碼交換的證明金鑰](https://tools.ietf.org/html/rfc7636) 。

傳回的 URI 會儲存在 `LoginUrl` 類別的屬性中 `LoginViewModel` 。 當 `IsLogin` 屬性變成時 `true` ， [`WebView`](xref:Xamarin.Forms.WebView) 中的 `LoginView` 會變成可見。 `WebView`資料會將其 [`Source`](xref:Xamarin.Forms.WebView.Source) 屬性系結至 `LoginUrl` 類別的屬性 `LoginViewModel` ，因此會在 `LoginUrl` 屬性設定為 IdentityServer 的授權端點時，將登入要求 IdentityServer。 當 IdentityServer 收到此要求，但使用者未通過驗證時，會將重新 `WebView` 導向至已設定的登入頁面，如圖9-4 所示。

![Web 程式所顯示的登入頁面](authentication-and-authorization-images/login.png)

**圖9-4：** Web 程式所顯示的登入頁面

登入完成後， [`WebView`](xref:Xamarin.Forms.WebView) 會重新導向至傳回 URI。 此 `WebView` 導覽將會 `NavigateAsync` 執行類別中的方法 `LoginViewModel` ，如下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

這個方法會剖析傳回 URI 中包含的驗證回應，而且如果有有效的授權碼，則會向 IdentityServer 的 [權杖端點](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)提出要求，並傳遞授權碼、PKCE 秘密驗證程式，以及其他必要的參數。 權杖端點位於 `/connect/token` 以使用者設定公開之基底端點的埠5105上。 如需使用者設定的詳細資訊，請參閱設定 [管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!TIP]
> 驗證傳回 Uri。 雖然 eShopOnContainers 的行動應用程式不會驗證傳回 URI，但最佳做法是驗證傳回 URI 參考已知的位置，以防止開啟重新導向攻擊。

如果權杖端點收到有效的授權碼和 PKCE 秘密驗證程式，則會以存取權杖、身分識別權杖和重新整理權杖來回應。 存取權杖 (允許存取 API 資源) 和身分識別權杖接著會儲存為應用程式設定，並執行頁面流覽。 因此，eShopOnContainers 行動裝置應用程式中的整體效果如下：如果使用者能夠成功地向 IdentityServer 進行驗證，則會將他們流覽至 `MainView` 頁面，也就是將 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 顯示 `CatalogView` 為其選取索引標籤的。

如需頁面導覽的詳細資訊，請參閱 [導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如 [`WebView`](xref:Xamarin.Forms.WebView) 需導覽如何讓視圖模型方法執行的詳細資訊，請參閱 [使用行為叫用導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking-navigation-using-behaviors)。 如需應用程式設定的相關資訊，請參閱設定 [管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 當應用程式設定為使用中的模擬服務時，eShopOnContainers 也會允許 mock 登入 `SettingsView` 。 在此模式中，應用程式不會與 IdentityServer 通訊，而是允許使用者使用任何認證來登入。

#### <a name="signing-out"></a>登出

當使用者在中按下 [ **登出** ] 按鈕時 `ProfileView` ， `LogoutCommand` 會執行類別中的，然後 `ProfileViewModel` 再執行 `LogoutAsync` 方法。 這個方法會在頁面上執行頁面導覽 `LoginView` ，並 `LogoutParameter` 將實例設定為 `true` 參數。 如需在頁面流覽期間傳遞參數的詳細資訊，請參閱 [導覽期間傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing-parameters-during-navigation)。

建立視圖並流覽至時， `InitializeAsync` 會執行視圖相關聯的視圖模型的方法，然後執行 `Logout` 類別的方法 `LoginViewModel` ，如下列程式碼範例所示：

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

這個方法會 `CreateLogoutRequest` 叫用類別中的方法 `IdentityService` ，將從應用程式設定取出的身分識別 token 傳遞為參數。 如需應用程式設定的詳細資訊，請參閱設定 [管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下列程式碼範例示範 `CreateLogoutRequest` 方法：

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

這個方法會使用必要的參數來建立 IdentityServer [端交談端點](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)的 URI。 結束交談端點位於 `/connect/endsession` 以使用者設定公開之基底端點的埠5105。 如需使用者設定的詳細資訊，請參閱設定 [管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

傳回的 URI 會儲存在 `LoginUrl` 類別的屬性中 `LoginViewModel` 。 當 `IsLogin` 屬性為時 `true` ， [`WebView`](xref:Xamarin.Forms.WebView) 中的 `LoginView` 會是可見的。 `WebView`資料會將它的屬性系結 [`Source`](xref:Xamarin.Forms.WebView.Source) 至 `LoginUrl` 類別的屬性 `LoginViewModel` ，因此會在 `LoginUrl` 屬性設定為 IdentityServer 的結束交談端點時，進行 IdentityServer 的登出要求。 當 IdentityServer 收到此要求時，如果使用者已登入，就會登出。 驗證會使用 cookie 驗證中介軟體所管理的 cookie，從 ASP.NET Core 進行追蹤。 因此，登出 IdentityServer 會移除驗證 cookie，並將 post 登出重新導向 URI 傳送回用戶端。

在行動應用程式中， [`WebView`](xref:Xamarin.Forms.WebView) 將會重新導向至 post 登出重新導向 URI。 此 `WebView` 導覽將會 `NavigateAsync` 執行類別中的方法 `LoginViewModel` ，如下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

這個方法會從應用程式設定中清除識別權杖和存取權杖，並將 `IsLogin` 屬性設定為 `false` ，讓 [`WebView`](xref:Xamarin.Forms.WebView) 頁面上的 `LoginView` 成為隱藏。 最後， `LoginUrl` 屬性會設定為 IdentityServer 的 [授權端點](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)URI （具有必要參數），以在使用者下次起始登入時進行準備。

如需頁面導覽的詳細資訊，請參閱 [導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如 [`WebView`](xref:Xamarin.Forms.WebView) 需導覽如何讓視圖模型方法執行的詳細資訊，請參閱 [使用行為叫用導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking-navigation-using-behaviors)。 如需應用程式設定的相關資訊，請參閱設定 [管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 當應用程式設定為使用 SettingsView 中的模擬服務時，eShopOnContainers 也允許模擬登出。 在此模式中，應用程式不會與 IdentityServer 通訊，而是會從應用程式設定中清除任何已儲存的權杖。

## <a name="authorization"></a>授權

驗證之後，ASP.NET Core web Api 通常需要授權存取權，讓服務能夠將 Api 提供給某些已驗證的使用者，而不是全部。

您可以藉由將授權屬性套用至控制器或動作來限制存取 ASP.NET Core MVC 路由，這會限制對驗證使用者的控制器或動作的存取，如下列程式碼範例所示：

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

如果未經授權的使用者嘗試存取以屬性標記的控制器或動作 `Authorize` ，MVC framework 會傳回 401 (未授權) HTTP 狀態碼。

> [!NOTE]
> 您可以在屬性上指定參數 `Authorize` ，以限制特定使用者的 API。 如需詳細資訊，請參閱[授權](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以整合到授權工作流程中，讓它提供控制授權的存取權杖。 這種方法會顯示在圖9-5 中。

![依存取權杖的授權](authentication-and-authorization-images/authorization.png)

**圖9-5：** 依存取權杖的授權

EShopOnContainers 行動應用程式會與身分識別微服務通訊，並在驗證過程中要求存取權杖。 存取權杖接著會轉送至排序和購物籃微服務所公開的 Api，作為存取要求的一部分。 存取權杖包含用戶端和使用者的相關資訊。 Api 接著會使用該資訊來授權其資料的存取權。 如需有關如何設定 IdentityServer 來保護 Api 的詳細資訊，請參閱設定 [Api 資源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>設定 IdentityServer 以執行授權

若要使用 IdentityServer 執行授權，必須將其授權中介軟體新增至 web 應用程式的 HTTP 要求管線。 中介軟體會新增 `ConfigureAuth` 至 web 應用程式類別中的方法 `Startup` ，該類別是從方法叫用 `Configure` ，並在下列來自 eShopOnContainers 參考應用程式的程式碼範例中示範：

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

這個方法可確保 API 只能透過有效的存取權杖來存取。 中介軟體會驗證連入權杖，以確保其會從信任的簽發者傳送，並驗證權杖是否有效，以與接收它的 API 搭配使用。 因此，流覽至訂購或購物籃控制器將會傳回 401 (未授權) HTTP 狀態碼，表示需要存取權杖。

> [!NOTE]
> 在使用或加入 MVC 之前，必須先將 IdentityServer 的授權中介軟體新增至 web 應用程式的 HTTP 要求管線 `app.UseMvc()` `app.UseMvcWithDefaultRoute()` 。

### <a name="making-access-requests-to-apis"></a>對 Api 提出存取要求

對訂購和購物籃微服務提出要求時，在驗證程式期間從 IdentityServer 取得的存取權杖必須包含在要求中，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

存取權杖會儲存為應用程式設定，而且會從平臺特定的儲存體中取出，並包含在 `GetOrderAsync` 類別中的方法呼叫中 `OrderService` 。

同樣地，將資料傳送到受 IdentityServer 保護的 API 時，必須包含存取權杖，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

存取權杖是從平臺特定的儲存體中取出，並包含在 `UpdateBasketAsync` 類別中的方法呼叫中 `BasketService` 。

EShopOnContainers 行動裝置 `RequestProvider` 應用程式中的類別會使用類別，對 `HttpClient` eShopOnContainers 參考應用程式所公開的 RESTful api 提出要求。 對需要授權的訂購和購物籃 Api 提出要求時，要求中必須包含有效的存取權杖。 將存取權杖新增至實例的標頭即可達成此目的 `HttpClient` ，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`DefaultRequestHeaders`類別的屬性會 `HttpClient` 公開隨每個要求傳送的標頭，並將存取權杖新增至前置詞為 `Authorization` 字串的標頭 `Bearer` 。 將要求傳送至 RESTful API 時，會將標頭的值 `Authorization` 解壓縮並進行驗證，以確保它是從信任的簽發者傳送，並用來判斷使用者是否有權叫用接收它的 API。

如需 eShopOnContainers mobile 應用程式如何提出 web 要求的詳細資訊，請參閱 [存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>總結

有許多方法可將驗證和授權整合至 Xamarin.Forms 與 ASP.NET MVC web 應用程式通訊的應用程式。 EShopOnContainers 行動應用程式會使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 IdentityServer 是開放原始碼 OpenID Connect 和 OAuth 2.0 架構，適用于與 ASP.NET Core 身分識別整合以執行持有人權杖驗證的 ASP.NET Core。

行動裝置應用程式會向 IdentityServer 要求安全性權杖，以驗證使用者或存取資源。 存取資源時，必須在需要授權的 Api 要求中包含存取權杖。 IdentityServer 的中介軟體會驗證連入存取權杖，以確保這些權杖會從信任的簽發者傳送，而且它們都有效，可與接收它們的 API 搭配使用。

## <a name="related-links"></a>相關連結

- [下載電子書 (2Mb PDF) ](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub)  (範例) ](https://github.com/dotnet-architecture/eShopOnContainers)
