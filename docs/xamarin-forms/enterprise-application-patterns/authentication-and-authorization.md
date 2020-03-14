---
title: 驗證和授權
description: 本章說明 eShopOnContainers mobile 應用程式如何對容器化微服務執行驗證和授權。
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 528ccd66cc013f83752d93251cb9714115b29819
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305182"
---
# <a name="authentication-and-authorization"></a>驗證和授權

驗證是從使用者取得身分識別認證（例如名稱和密碼），以及向授權單位驗證那些認證的程式。 如果認證有效，則提交認證的實體會被視為已驗證的身分識別。 驗證身分識別之後，授權程式會判斷該身分識別是否可存取指定的資源。

有許多方法可以將驗證和授權整合到與 ASP.NET MVC web 應用程式通訊的 Xamarin Forms 應用程式中，包括使用 ASP.NET Core 身分識別、外部驗證提供者（例如 Microsoft、Google、Facebook、Twitter 和驗證中介軟體。 EShopOnContainers 行動應用程式會透過使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 行動應用程式會從 IdentityServer 要求安全性權杖，以驗證使用者或存取資源。 若要讓 IdentityServer 代表使用者發出權杖，使用者必須登入 IdentityServer。 不過，IdentityServer 不會提供用於驗證的使用者介面或資料庫。 因此，在 eShopOnContainers 參考應用程式中，ASP.NET Core 身分識別會用於此用途。

## <a name="authentication"></a>驗證

當應用程式需要知道目前使用者的身分識別時，就需要進行驗證。 ASP.NET Core 用來識別使用者的主要機制是 ASP.NET Core 身分識別成員資格系統，其會將使用者資訊儲存在開發人員所設定的資料存放區中。 一般來說，此資料存放區會是 EntityFramework 存放區，不過，您可以使用自訂商店或協力廠商套件，將身分識別資訊儲存在 Azure 儲存體、Azure Cosmos DB 或其他位置。

若為使用本機使用者資料存放區的驗證案例，以及透過 cookie 在要求之間保存身分識別資訊（如同在 ASP.NET MVC web 應用程式中一般），則 ASP.NET Core 身分識別是合適的解決方案。 不過，cookie 不一定是保存和傳輸資料的自然方式。 例如，公開從行動應用程式存取之 RESTful 端點的 ASP.NET Core web 應用程式通常需要使用持有人權杖驗證，因為在此案例中無法使用 cookie。 不過，您可以輕鬆地抓取持有人權杖，並將其包含在從行動應用程式提出之 web 要求的 authorization 標頭中。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>使用 IdentityServer 4 發行持有人權杖

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4)是開放原始碼 OpenID connect 和適用于 ASP.NET Core 的 OAuth 2.0 架構，可用於許多驗證和授權案例，包括為本機 ASP.NET Core 身分識別使用者發出安全性權杖。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 非常類似，但有不同的責任。

OpenID Connect 是 OAuth 2.0 通訊協定之上的驗證層。 OAuth 2 是一種通訊協定，可讓應用程式從 Security Token Service 要求存取權杖，並使用它們來與 Api 通訊。 此委派可降低用戶端應用程式和 Api 的複雜性，因為驗證和授權可以集中在一起。

OpenID Connect 和 OAuth 2.0 的組合結合了驗證和 API 存取的兩個基本安全性考慮，而 IdentityServer 4 則是這些通訊協定的執行。

在使用直接用戶端對微服務通訊的應用程式（例如 eShopOnContainers 參考應用程式）中，可使用專用的驗證微服務做為安全性權杖服務（STS）來驗證使用者，如圖所示9-1。 如需直接用戶端對微服務通訊的詳細資訊，請參閱[用戶端與微服務之間的通訊](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices)。

![](authentication-and-authorization-images/authentication.png "Authentication by a dedicated authentication microservice")

**圖9-1：** 專用驗證微服務的驗證

EShopOnContainers 行動應用程式會與身分識別微服務通訊，其使用 IdentityServer 4 來執行驗證和 Api 的存取控制。 因此，行動應用程式會從 IdentityServer 要求權杖，以驗證使用者或存取資源：

- 使用 IdentityServer 來驗證使用者是由要求身分*識別*權杖的行動應用程式所達成，這代表驗證程式的結果。 它至少會包含使用者的識別碼，以及使用者驗證方式和時間的相關資訊。 它也可以包含其他身分識別資料。
- 使用 IdentityServer 存取資源是由要求*存取*權杖的行動應用程式所達成，允許存取 API 資源。 用戶端會要求存取權杖，並將其轉送至 API。 存取權杖包含用戶端的相關資訊，以及使用者（如果有的話）。 Api 接著會使用該資訊來授權其資料的存取權。

> [!NOTE]
> 用戶端必須先向 IdentityServer 註冊，才能要求權杖。

### <a name="adding-identityserver-to-a-web-application"></a>將 IdentityServer 新增至 Web 應用程式

為了讓 ASP.NET Core web 應用程式使用 IdentityServer 4，必須將它新增至 web 應用程式的 Visual Studio 方案。 如需詳細資訊，請參閱 IdentityServer 檔中的[總覽](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html)。

一旦 IdentityServer 包含在 web 應用程式的 Visual Studio 解決方案中，就必須將它新增至 web 應用程式的 HTTP 要求處理管線，讓它可以服務 OpenID Connect 和 OAuth 2.0 端點的要求。 這是在 web 應用程式的 `Startup` 類別的 `Configure` 方法中達成，如下列程式碼範例所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

在 web 應用程式的 HTTP 要求處理管線中，順序很重要。 因此，在執行登入畫面的 UI 架構之前，必須先將 IdentityServer 新增至管線。

### <a name="configuring-identityserver"></a>設定 IdentityServer

IdentityServer 應該在 web 應用程式的 `Startup` 類別的 `ConfigureServices` 方法中設定，方法是呼叫 `services.AddIdentityServer` 方法，如 eShopOnContainers 參考應用程式中的下列程式碼範例所示：

```csharp
public void ConfigureServices(IServiceCollection services)  
{  
    ...  
    services.AddIdentityServer(x => x.IssuerUri = "null")  
        .AddSigningCredential(Certificate.Get())                 
        .AddAspNetIdentity<ApplicationUser>()  
        .AddConfigurationStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .AddOperationalStore(builder =>  
            builder.UseSqlServer(connectionString, options =>  
                options.MigrationsAssembly(migrationsAssembly)))  
        .Services.AddTransient<IProfileService, ProfileService>();  
}
```

呼叫 `services.AddIdentityServer` 方法之後，會呼叫其他的流暢 Api 來設定下列各項：

- 用於簽署的認證。
- 使用者可能會要求存取的 API 和身分識別資源。
- 將連接到要求權杖的用戶端。
- ASP.NET Core 身分識別。

> [!TIP]
> 動態載入 IdentityServer 4 設定。 IdentityServer 4 的 Api 可讓您從記憶體中的設定物件清單設定 IdentityServer。 在 eShopOnContainers reference 應用程式中，這些記憶體內部集合會硬式編碼到應用程式中。 不過，在實際執行案例中，可以從設定檔或從資料庫動態載入它們。

如需將 IdentityServer 設定為使用 ASP.NET Core 身分識別的相關資訊，請參閱 IdentityServer 檔中的[使用 ASP.NET Core 身分識別](https://identityserver4.readthedocs.io/en/latest/quickstarts/6_aspnet_identity.html)。

#### <a name="configuring-api-resources"></a>設定 API 資源

設定 API 資源時，`AddInMemoryApiResources` 方法需要 `IEnumerable<ApiResource>` 集合。 下列程式碼範例顯示在 eShopOnContainers reference 應用程式中提供此集合的 `GetApis` 方法：

```csharp
public static IEnumerable<ApiResource> GetApis()  
{  
    return new List<ApiResource>  
    {  
        new ApiResource("orders", "Orders Service"),  
        new ApiResource("basket", "Basket Service")  
    };  
}
```

這個方法會指定 IdentityServer 應保護訂單和購物籃 Api。 因此，對這些 Api 進行呼叫時，將需要 IdentityServer 受控存取權杖。 如需 `ApiResource` 類型的詳細資訊，請參閱 IdentityServer 4 檔中的[API 資源](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html)。

#### <a name="configuring-identity-resources"></a>設定身分識別資源

設定身分識別資源時，`AddInMemoryIdentityResources` 方法需要 `IEnumerable<IdentityResource>` 集合。 身分識別資源是使用者識別碼、名稱或電子郵件地址等資料。 每個身分識別資源都有唯一的名稱，而且可以將任意宣告類型指派給它，這會包含在使用者的身分識別權杖中。 下列程式碼範例顯示在 eShopOnContainers reference 應用程式中提供此集合的 `GetResources` 方法：

```csharp
public static IEnumerable<IdentityResource> GetResources()  
{  
    return new List<IdentityResource>  
    {  
        new IdentityResources.OpenId(),  
        new IdentityResources.Profile()  
    };  
}
```

OpenID Connect 規格會指定一些[標準身分識別資源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低需求是提供支援來發出使用者的唯一識別碼。 這是藉由公開 `IdentityResources.OpenId` 身分識別資源來達成。

> [!NOTE]
> `IdentityResources` 類別支援 OpenID Connect 規格中定義的所有範圍（openid、電子郵件、設定檔、電話和位址）。

IdentityServer 也支援定義自訂身分識別資源。 如需詳細資訊，請參閱 IdentityServer 檔中的[定義自訂身分識別資源](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources)。 如需 `IdentityResource` 類型的詳細資訊，請參閱 IdentityServer 4 檔中的身分[識別資源](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html)。

#### <a name="configuring-clients"></a>設定用戶端

用戶端是可以向 IdentityServer 要求權杖的應用程式。 一般來說，您必須為每個用戶端定義下列設定，最低限度如下：

- 唯一的用戶端識別碼。
- 與權杖服務的允許互動（稱為「授與類型」）。
- 身分識別和存取權杖的傳送目標位置（稱為「重新導向 URI」）。
- 允許用戶端存取的資源清單（稱為「範圍」）。

設定用戶端時，`AddInMemoryClients` 方法需要 `IEnumerable<Client>` 集合。 下列程式碼範例顯示在 eShopOnContainers reference 應用程式中提供此集合的 `GetClients` 方法中，eShopOnContainers 行動應用程式的設定：

```csharp
public static IEnumerable<Client> GetClients(Dictionary<string,string> clientsUrl)
{
    return new List<Client>
    {
        ...
        new Client
        {
            ClientId = "xamarin",
            ClientName = "eShop Xamarin OpenId Client",
            AllowedGrantTypes = GrantTypes.Hybrid,
            ClientSecrets =
            {
                new Secret("secret".Sha256())
            },
            RedirectUris = { clientsUrl["Xamarin"] },
            RequireConsent = false,
            RequirePkce = true,
            PostLogoutRedirectUris = { $"{clientsUrl["Xamarin"]}/Account/Redirecting" },
            AllowedCorsOrigins = { "http://eshopxamarin" },
            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.OfflineAccess,
                "orders",
                "basket"
            },
            AllowOfflineAccess = true,
            AllowAccessTokensViaBrowser = true
        },
        ...
    };
}
```

此設定會指定下列屬性的資料：

- `ClientId`：用戶端的唯一識別碼。
- `ClientName`：用戶端顯示名稱，用於記錄和同意畫面。
- `AllowedGrantTypes`：指定用戶端想要與 IdentityServer 互動的方式。 如需詳細資訊，請參閱設定[驗證流程](#configuring_the_authentication_flow)。
- `ClientSecrets`：指定從權杖端點要求權杖時所使用的用戶端密碼認證。
- `RedirectUris`：指定允許的 Uri，以傳回權杖或授權碼。
- `RequireConsent`：指定是否需要同意畫面。
- `RequirePkce`：指定使用授權碼的用戶端是否必須傳送證明金鑰。
- `PostLogoutRedirectUris`：指定登出後要重新導向到的允許 Uri。
- `AllowedCorsOrigins`：指定用戶端的原點，讓 IdentityServer 可以允許來自來源的跨原始來源呼叫。
- `AllowedScopes`：指定用戶端可存取的資源。 根據預設，用戶端無法存取任何資源。
- `AllowOfflineAccess`：指定用戶端是否可以要求重新整理權杖。

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>設定驗證流程

用戶端和 IdentityServer 之間的驗證流程可以藉由在 `Client.AllowedGrantTypes` 屬性中指定授與類型來設定。 OpenID Connect 和 OAuth 2.0 規格會定義一些驗證流程，包括：

- 隱含。 此流程已針對以瀏覽器為基礎的應用程式優化，而且應該用於僅供使用者驗證，或用於驗證和存取權杖要求。 所有權杖都是透過瀏覽器傳輸，因此不允許重新整理權杖等 advanced 功能。
- 授權碼。 此流程可讓您在後通道上抓取權杖，而不是瀏覽器前端通道，同時也支援用戶端驗證。
- 混合： 此流程是隱含和授權程式碼授與類型的組合。 身分識別權杖會透過瀏覽器通道傳輸，並包含已簽署的通訊協定回應以及其他成品，例如授權碼。 成功驗證回應之後，應該使用後端通道來抓取存取和重新整理權杖。

> [!TIP]
> 使用混合式驗證流程。 混合式驗證流程可減少適用于瀏覽器通道的各種攻擊，而且是建議的流程，適用于想要取得存取權杖（且可能會重新整理權杖）的原生應用程式。

如需驗證流程的詳細資訊，請參閱 IdentityServer 4 檔中的[授與類型](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html)。

### <a name="performing-authentication"></a>執行驗證

若要讓 IdentityServer 代表使用者發出權杖，使用者必須登入 IdentityServer。 不過，IdentityServer 不會提供用於驗證的使用者介面或資料庫。 因此，在 eShopOnContainers 參考應用程式中，ASP.NET Core 身分識別會用於此用途。

EShopOnContainers 行動應用程式會使用 IdentityServer 與混合式驗證流程進行驗證，如圖9-2 所示。

![](authentication-and-authorization-images/sign-in.png "High-level overview of the sign-in process")

**圖9-2：** 登入程式的高階總覽

已提出登入要求以 `<base endpoint>:5105/connect/authorize`。 成功驗證之後，IdentityServer 會傳回包含授權碼和身分識別權杖的驗證回應。 授權碼接著會傳送至 `<base endpoint>:5105/connect/token`，以回應存取、身分識別和重新整理權杖。

EShopOnContainers 行動應用程式會將要求傳送至 `<base endpoint>:5105/connect/endsession`，並使用其他參數來登出 IdentityServer。 登出之後，IdentityServer 會將登出後重新導向 URI 送回行動應用程式來回應。 圖9-3 說明此程式。

![](authentication-and-authorization-images/sign-out.png "High-level overview of the sign-out process")

**圖9-3：** 登出程式的高階總覽

在 eShopOnContainers 行動應用程式中，與 IdentityServer 的通訊是由執行 `IIdentityService` 介面的 `IdentityService` 類別所執行。 這個介面會指定實作為類別必須提供 `CreateAuthorizationRequest`、`CreateLogoutRequest`和 `GetTokenAsync` 方法。

#### <a name="signing-in"></a>登入

當使用者在 `LoginView`上按下 [**登**入] 按鈕時，就會執行 `LoginViewModel` 類別中的 `SignInCommand`，然後再執行 `SignInAsync` 方法。 下列程式碼範例示範此方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

這個方法會叫用 `IdentityService` 類別中的 `CreateAuthorizationRequest` 方法，如下列程式碼範例所示：

```csharp
public string CreateAuthorizationRequest()
{
    // Create URI to authorization endpoint
    var authorizeRequest = new AuthorizeRequest(GlobalSetting.Instance.IdentityEndpoint);

    // Dictionary with values for the authorize request
    var dic = new Dictionary<string, string>();
    dic.Add("client_id", GlobalSetting.Instance.ClientId);
    dic.Add("client_secret", GlobalSetting.Instance.ClientSecret); 
    dic.Add("response_type", "code id_token");
    dic.Add("scope", "openid profile basket orders locations marketing offline_access");
    dic.Add("redirect_uri", GlobalSetting.Instance.Callback);
    dic.Add("nonce", Guid.NewGuid().ToString("N"));
    dic.Add("code_challenge", CreateCodeChallenge());
    dic.Add("code_challenge_method", "S256");

    // Add CSRF token to protect against cross-site request forgery attacks.
    var currentCSRFToken = Guid.NewGuid().ToString("N");
    dic.Add("state", currentCSRFToken);

    var authorizeUri = authorizeRequest.Create(dic); 
    return authorizeUri;
}

```

這個方法會使用必要的參數來建立 IdentityServer[授權端點](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)的 URI。 授權端點位於公開為使用者設定之基底端點的埠5105上 `/connect/authorize`。 如需使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 行動應用程式的受攻擊面會藉由將 Code Exchange （PKCE）延伸模組的證明金鑰實作為 OAuth 來降低。 PKCE 會保護在攔截的授權碼時，不要使用它們。 這是由產生密碼驗證器的用戶端所達成，這是在授權要求中傳遞的雜湊，在兌換授權碼時，會呈現未雜湊的。 如需 PKCE 的詳細資訊，請參閱網際網路工程任務推動小組網站上的[OAuth 公用用戶端程式代碼交換的證明金鑰](https://tools.ietf.org/html/rfc7636)。

傳回的 URI 會儲存在 `LoginViewModel` 類別的 `LoginUrl` 屬性中。 當 `IsLogin` 屬性變成 `true`時，`LoginView` 中的[`WebView`](xref:Xamarin.Forms.WebView)就會變成可見狀態。 `WebView` 資料會將其[`Source`](xref:Xamarin.Forms.WebView.Source)屬性系結至 `LoginViewModel` 類別的 `LoginUrl` 屬性，因此當 `LoginUrl` 屬性設定為 IdentityServer 的授權端點時，會提出登入要求來 IdentityServer。 當 IdentityServer 收到此要求且未驗證使用者時，`WebView` 會重新導向至已設定的登入頁面，如圖9-4 所示。

![](authentication-and-authorization-images/login.png "Login page displayed by the WebView")

**圖9-4：** Web 工作顯示的登入頁面

登入完成後， [`WebView`](xref:Xamarin.Forms.WebView)會重新導向至傳回 URI。 此 `WebView` 導覽會使 `LoginViewModel` 類別中的 `NavigateAsync` 方法執行，如下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    var authResponse = new AuthorizeResponse(url);  
    if (!string.IsNullOrWhiteSpace(authResponse.Code))  
    {  
        var userToken = await _identityService.GetTokenAsync(authResponse.Code);  
        string accessToken = userToken.AccessToken;  

        if (!string.IsNullOrWhiteSpace(accessToken))  
        {  
            Settings.AuthAccessToken = accessToken;  
            Settings.AuthIdToken = authResponse.IdentityToken;  

            await NavigationService.NavigateToAsync<MainViewModel>();  
            await NavigationService.RemoveLastFromBackStackAsync();  
        }  
    }  
    ...  
}
```

這個方法會剖析傳回 URI 中包含的驗證回應，並假設有有效的授權碼，它會對 IdentityServer 的[權杖端點](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)提出要求，傳遞授權碼、PKCE 密碼驗證器和其他必要的參數。 權杖端點位於公開為使用者設定之基底端點的埠5105上 `/connect/token`。 如需使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!TIP]
> 驗證傳回的 Uri。 雖然 eShopOnContainers 行動應用程式不會驗證傳回 URI，但最佳做法是驗證傳回 URI 參考已知的位置，以防止開啟重新導向攻擊。

如果權杖端點收到有效的授權碼和 PKCE 密碼驗證器，則會以存取權杖、身分識別權杖和重新整理權杖來回應。 存取權杖（允許存取 API 資源）和身分識別權杖會儲存為應用程式設定，並執行頁面導覽。 因此，eShopOnContainers 行動應用程式中的整體效果如下：如果使用者能夠使用 IdentityServer 成功進行驗證，則會流覽至 [`MainView`] 頁面，也就是顯示 `CatalogView` 做為所選索引標籤的[`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。

如需頁面導覽的詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如需[`WebView`](xref:Xamarin.Forms.WebView)導覽如何使視圖模型方法執行的相關資訊，請參閱[使用行為叫用導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 如需應用程式設定的詳細資訊，請參閱設定[管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 當應用程式設定為使用 `SettingsView`中的模擬服務時，eShopOnContainers 也會允許 mock 登入。 在此模式中，應用程式不會與 IdentityServer 通訊，而是允許使用者使用任何認證進行登入。

#### <a name="signing-out"></a>登出

當使用者在 `ProfileView`中，按 [**登出**] 按鈕時，就會執行 `ProfileViewModel` 類別中的 `LogoutCommand`，而這會接著執行 `LogoutAsync` 方法。 這個方法會執行頁面導覽至 `LoginView` 頁面，並將 `LogoutParameter` 實例設定為 `true` 做為參數。 如需在頁面導覽期間傳遞參數的詳細資訊，請參閱[導覽期間傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

建立並流覽至視圖時，會執行視圖相關聯視圖模型的 `InitializeAsync` 方法，然後執行 `LoginViewModel` 類別的 `Logout` 方法，如下列程式碼範例所示：

```csharp
private void Logout()  
{  
    var authIdToken = Settings.AuthIdToken;  
    var logoutRequest = _identityService.CreateLogoutRequest(authIdToken);  

    if (!string.IsNullOrEmpty(logoutRequest))  
    {  
        // Logout  
        LoginUrl = logoutRequest;  
    }  
    ...  
}
```

這個方法會叫用 `IdentityService` 類別中的 `CreateLogoutRequest` 方法，將從應用程式設定中抓取的識別 token 當做參數來傳遞。 如需應用程式設定的詳細資訊，請參閱設定[管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下列程式碼範例示範 `CreateLogoutRequest` 方法：

```csharp
public string CreateLogoutRequest(string token)  
{  
    ...  
    return string.Format("{0}?id_token_hint={1}&post_logout_redirect_uri={2}",   
        GlobalSetting.Instance.LogoutEndpoint,  
        token,  
        GlobalSetting.Instance.LogoutCallback);  
}
```

這個方法會使用必要的參數來建立 IdentityServer 之[結束交談端點](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)的 URI。 結束交談端點位於公開為使用者設定之基底端點的埠5105上 `/connect/endsession`。 如需使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

傳回的 URI 會儲存在 `LoginViewModel` 類別的 `LoginUrl` 屬性中。 `true``IsLogin` 屬性時，`LoginView` 中的[`WebView`](xref:Xamarin.Forms.WebView)是可見的。 `WebView` 資料會將其[`Source`](xref:Xamarin.Forms.WebView.Source)屬性系結至 `LoginViewModel` 類別的 `LoginUrl` 屬性，因此當 `LoginUrl` 屬性設定為 IdentityServer 的結束交談端點時，會提出登出要求來 IdentityServer。 當 IdentityServer 收到此要求時，如果使用者已登入，就會進行登出。 使用來自 ASP.NET Core 的 cookie 驗證中介軟體所管理的 cookie 來追蹤驗證。 因此，登出 IdentityServer 會移除驗證 cookie，並將登出後的重新導向 URI 傳送回用戶端。

在行動裝置應用程式中， [`WebView`](xref:Xamarin.Forms.WebView)會被重新導向至登出後重新導向 URI。 此 `WebView` 導覽會使 `LoginViewModel` 類別中的 `NavigateAsync` 方法執行，如下列程式碼範例所示：

```csharp
private async Task NavigateAsync(string url)  
{  
    ...  
    Settings.AuthAccessToken = string.Empty;  
    Settings.AuthIdToken = string.Empty;  
    IsLogin = false;  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    ...  
}
```

這個方法會從應用程式設定中清除識別權杖和存取權杖，並將 `IsLogin` 屬性設為 `false`，這會導致 `LoginView` 頁面上的[`WebView`](xref:Xamarin.Forms.WebView)變成隱藏。 最後，`LoginUrl` 屬性會設定為 IdentityServer 的[授權端點](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)URI，其中包含必要的參數，以便在下一次使用者起始登入時進行準備。

如需頁面導覽的詳細資訊，請參閱[導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如需[`WebView`](xref:Xamarin.Forms.WebView)導覽如何使視圖模型方法執行的相關資訊，請參閱[使用行為叫用導覽](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 如需應用程式設定的詳細資訊，請參閱設定[管理](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 當應用程式設定為使用 SettingsView 中的模擬服務時，eShopOnContainers 也會允許模擬登出。 在此模式中，應用程式不會與 IdentityServer 通訊，而是會從應用程式設定中清除任何已儲存的權杖。

<a name="authorization" />

## <a name="authorization"></a>授權

驗證之後，ASP.NET Core web Api 通常需要授與存取權，以允許服務將 Api 提供給某些已驗證的使用者，而不是全部。

將授權屬性套用至控制器或動作來限制對 ASP.NET Core MVC 路由的存取，這會將控制器或動作的存取限制為已驗證的使用者，如下列程式碼範例所示：

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

如果未經授權的使用者嘗試存取以 `Authorize` 屬性標記的控制器或動作，則 MVC 架構會傳回401（未經授權）的 HTTP 狀態碼。

> [!NOTE]
> 可以在 `Authorize` 屬性上指定參數，以將 API 限制為特定的使用者。 如需詳細資訊，請參閱[授權](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以整合到授權工作流程中，讓它提供控制授權。 這種方法如圖9-5 所示。

![](authentication-and-authorization-images/authorization.png "Authorization by access token")

**圖9-5：** 依存取權杖的授權

EShopOnContainers 行動應用程式會與身分識別微服務通訊，並在驗證過程中要求存取權杖。 存取權杖接著會轉送到訂購和購物籃微服務所公開的 Api，做為存取要求的一部分。 存取權杖包含用戶端和使用者的相關資訊。 Api 接著會使用該資訊來授權其資料的存取權。 如需有關如何設定 IdentityServer 來保護 Api 的詳細資訊，請參閱設定[Api 資源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>設定 IdentityServer 以執行授權

若要使用 IdentityServer 執行授權，必須將其授權中介軟體新增至 web 應用程式的 HTTP 要求管線。 中介軟體會加入至 web 應用程式的 `Startup` 類別中的 `ConfigureAuth` 方法，這是從 `Configure` 方法叫用，並在 eShopOnContainers reference 應用程式的下列程式碼範例中示範：

```csharp
protected virtual void ConfigureAuth(IApplicationBuilder app)  
{  
    var identityUrl = Configuration.GetValue<string>("IdentityUrl");  
    app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions  
    {  
        Authority = identityUrl.ToString(),  
        ScopeName = "basket",  
        RequireHttpsMetadata = false  
    });  
} 
```

這個方法可確保只能使用有效的存取權杖來存取 API。 中介軟體會驗證傳入的權杖，以確保它會從受信任的簽發者傳送，並驗證權杖是否有效，以搭配接收它的 API 使用。 因此，流覽至訂購或購物籃控制器將會傳回401（未經授權）的 HTTP 狀態碼，表示需要存取權杖。

> [!NOTE]
> 您必須先將 IdentityServer 的授權中介軟體新增至 web 應用程式的 HTTP 要求管線，才能使用 `app.UseMvc()` 或 `app.UseMvcWithDefaultRoute()`來新增 MVC。

### <a name="making-access-requests-to-apis"></a>對 Api 提出存取要求

對訂購和購物籃微服務提出要求時，必須將在驗證程式期間從 IdentityServer 取得的存取權杖包含在要求中，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

存取權杖會儲存為應用程式設定，並從平臺特定的儲存體中抓取，並包含在 `OrderService` 類別中的 `GetOrderAsync` 方法呼叫中。

同樣地，將資料傳送至受 IdentityServer 保護的 API 時，必須包含存取權杖，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

存取權杖是從平臺特定的儲存體抓取，並包含在 `BasketService` 類別中的 `UpdateBasketAsync` 方法呼叫中。

EShopOnContainers 行動應用程式中的 `RequestProvider` 類別，會使用 `HttpClient` 類別對 eShopOnContainers 參考應用程式所公開的 RESTful Api 提出要求。 對需要授權的訂購和購物籃 Api 提出要求時，要求中必須包含有效的存取權杖。 將存取權杖新增至 `HttpClient` 實例的標頭，即可達成此目的，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`HttpClient` 類別的 `DefaultRequestHeaders` 屬性會公開隨每個要求傳送的標頭，並將存取權杖新增至 `Authorization` 標頭，並在前面加上字串 `Bearer`。 將要求傳送至 RESTful API 時，會將 `Authorization` 標頭的值解壓縮並進行驗證，以確保它會從受信任的簽發者傳送，並用於判斷使用者是否有權叫用接收它的 API。

如需 eShopOnContainers mobile 應用程式如何提出 web 要求的詳細資訊，請參閱[存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>摘要

有許多方法可以將驗證和授權整合到與 ASP.NET MVC web 應用程式通訊的 Xamarin. Forms 應用程式中。 EShopOnContainers 行動應用程式會透過使用 IdentityServer 4 的容器化身分識別微服務來執行驗證和授權。 IdentityServer 是開放原始碼 OpenID Connect 和 OAuth 2.0 架構，適用于與 ASP.NET Core 身分識別整合以執行持有人權杖驗證的 ASP.NET Core。

行動應用程式會從 IdentityServer 要求安全性權杖，以驗證使用者或存取資源。 存取資源時，必須在需要授權的 Api 要求中包含存取權杖。 IdentityServer 的中介軟體會驗證連入存取權杖，以確保它們會從受信任的簽發者傳送，而且它們是有效的，可與接收它們的 API 搭配使用。

## <a name="related-links"></a>相關連結

- [下載電子書（2 Mb 的 PDF）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（範例）](https://github.com/dotnet-architecture/eShopOnContainers)
