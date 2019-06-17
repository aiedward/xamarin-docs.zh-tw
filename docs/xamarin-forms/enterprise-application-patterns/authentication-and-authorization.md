---
title: 驗證與授權
description: 本章說明驗證和授權，對容器化微服務，eShopOnContainers 的行動裝置應用程式的執行方式。
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 9db9902dfbf602ba21b353f3a17920dc37b03ee5
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382269"
---
# <a name="authentication-and-authorization"></a>驗證與授權

驗證是從使用者取得識別的認證，例如名稱和密碼和驗證授權單位針對那些認證的程序。 如果是有效的認證，提交此憑證的實體視為已驗證的身分識別。 身分識別具有經過驗證後，授權程序會判斷該身分識別是否有指定資源的存取權。

有許多方法可以整合到 Xamarin.Forms 應用程式的 ASP.NET MVC web 應用程式，包括使用 ASP.NET Core 身分識別，例如 Google、 Microsoft 外部驗證提供者與通訊的驗證和授權Facebook、 Twitter 或驗證中介軟體。 EShopOnContainers 的行動裝置應用程式會執行驗證與授權使用 IdentityServer 4 的容器化的身分識別微服務。 行動裝置應用程式會從 IdentityServer，要求安全性權杖來驗證使用者或存取資源。 針對 IdentityServer 來發行權杖，代表使用者，使用者必須登入 IdentityServer。 不過，IdentityServer 不提供使用者介面或資料庫進行驗證。 因此，eShopOnContainers 參考應用程式中 ASP.NET Core 身分識別就會用於此用途。

## <a name="authentication"></a>驗證

應用程式必須知道目前使用者的身分識別時，需要驗證。 ASP.NET Core 識別使用者的主要機制是 ASP.NET Core 識別成員資格系統，為開發人員所設定的資料存放區中儲存使用者資訊。 一般而言，此資料存放區會 EntityFramework 存放區中，雖然自訂存放區或第三方封裝可用於將身分識別資訊儲存在 Azure 儲存體、 Azure Cosmos DB 或其他位置。

驗證案例，請使用本機使用者資料存放區，且已存在要求透過 cookie （跟一般在 ASP.NET MVC web 應用程式） 之間的識別資訊，ASP.NET Core Identity 是適合的解決方案。 不過，cookie 不一定保存及傳輸資料的自然方式。 例如，會公開 rest 式端點從行動裝置應用程式存取的 ASP.NET Core web 應用程式通常需要使用 bearer 權杖驗證，因為 cookie 不能用在此案例中。 不過，持有人權杖就可以輕鬆地擷取和包含在從行動裝置應用程式所做的 web 要求的 authorization 標頭。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>發出的持有人權杖使用 IdentityServer 4

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4)適用於 ASP.NET Core，可以使用許多驗證和授權案例，包括發行本機 ASP.NET Core 識別使用者的安全性權杖是一種開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 都很相似，同時擁有不同的責任。

OpenID Connect 是一個驗證層，在 OAuth 2.0 通訊協定之上。 OAuth 2 是一種通訊協定，可讓應用程式，從安全性權杖服務要求存取權杖，然後使用它們來進行通訊與 Api。 這個委派會減少用戶端應用程式和 Api 的複雜性，因為可以集中式驗證和授權。

OpenID Connect 和 OAuth 2.0 的組合結合的驗證和 API 存取的兩個基本的安全性考量，IdentityServer 4 是這些通訊協定的實作。

使用直接用戶端與微服務通訊，例如 eShopOnContainers 參考應用程式，應用程式中做為安全性權杖服務 (STS) 的專用的驗證微服務可用來驗證使用者，如圖所示9-1。 如需有關直接用戶端與微服務通訊的詳細資訊，請參閱[用戶端之間的通訊和微服務](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices)。

![](authentication-and-authorization-images/authentication.png "專用的驗證微服務的驗證")

**圖 9-1:** 專用的驗證微服務的驗證

與 IdentityServer 4 用以執行驗證，以及 Api 的存取控制的身分識別微服務通訊，eShopOnContainers 的行動裝置應用程式。 因此，行動裝置應用程式要求權杖從 IdentityServer，用於驗證的使用者或存取資源：

-   驗證與 IdentityServer 的使用者行動裝置應用程式要求即可達成*識別*語彙基元，即表示驗證程序的結果。 最少，它會包含使用者和驗證使用者的方式和時機的相關資訊的識別項。 它也可以包含其他身分識別資料。
-   存取與 IdentityServer 的資源，行動裝置應用程式要求即可達成*存取*語彙基元，可讓您存取 API 資源。 用戶端要求存取權杖，並將其轉送至 API。 存取權杖會包含用戶端和使用者的相關資訊 （如果有的話）。 然後，Api 會使用該資訊來授權存取其資料。

> [!NOTE]
> 用戶端必須先向 IdentityServer 它可以要求權杖。

### <a name="adding-identityserver-to-a-web-application"></a>IdentityServer 加入 Web 應用程式

為了讓 ASP.NET Core web 應用程式使用 IdentityServer 4，它必須加入至 web 應用程式的 Visual Studio 方案。 如需詳細資訊，請參閱 <<c0> [ 概觀](https://identityserver4.readthedocs.io/en/latest/quickstarts/0_overview.html)IdentityServer 文件中。

一旦 IdentityServer 包含在 web 應用程式的 Visual Studio 方案中，它必須新增至 web 應用程式的 HTTP 要求處理管線，以便它可以處理 OpenID Connect 和 OAuth 2.0 端點的要求。 這在達成`Configure`web 應用程式的方法`Startup`類別，如下列程式碼範例所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

順序很重要的 web 應用程式的 HTTP 要求處理管線中。 因此，IdentityServer 必須新增至管線，才會實作登入畫面的 UI 架構。

### <a name="configuring-identityserver"></a>設定 IdentityServer

IdentityServer 應該在中設定`ConfigureServices`web 應用程式的方法`Startup`藉由呼叫類別`services.AddIdentityServer`方法，如 eShopOnContainers 參考應用程式的下列程式碼範例所示：

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

之後呼叫`services.AddIdentityServer`方法，其他的 fluent Api 會呼叫以設定下列各項：

-   用於簽章的認證。
-   使用者可能會要求您的 API 和身分識別資源的存取權。
-   將連線要求權杖的用戶端。
-   ASP.NET Core 身分識別。

>💡 **提示**:IdentityServer 4 組態以動態方式載入。 IdentityServer 4 Api 可讓設定 IdentityServer 從記憶體中清單的組態物件。 在 eShopOnContainers 參考應用程式，這些記憶體中集合會是硬式編碼到應用程式。 不過，在實際執行案例中可以載入以動態方式從組態檔或資料庫。

如需設定為使用 ASP.NET Core 識別 IdentityServer 資訊，請參閱[使用 ASP.NET Identity Core](https://identityserver4.readthedocs.io/en/latest/quickstarts/8_aspnet_identity.html) IdentityServer 文件中。

#### <a name="configuring-api-resources"></a>設定 API 資源

設定 API 資源時`AddInMemoryApiResources`方法預期`IEnumerable<ApiResource>`集合。 下列程式碼範例示範`GetApis`方法，提供這個集合，在 eShopOnContainers 參考應用程式：

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

這個方法會指定應保護 IdentityServer，訂單和購物籃的 Api。 因此，IdentityServer 受管理存取權杖時，會需要對這些 Api 進行呼叫。 如需詳細資訊`ApiResource`類型，請參閱[API 資源](https://identityserver4.readthedocs.io/en/latest/reference/api_resource.html)IdentityServer 4 文件中。

#### <a name="configuring-identity-resources"></a>設定身分識別資源

設定身分識別資源時`AddInMemoryIdentityResources`方法預期`IEnumerable<IdentityResource>`集合。 識別資源是資料，例如使用者識別碼、 名稱或電子郵件地址。 每個身分識別資源都有唯一的名稱，並且任意宣告類型可以指派給它，然後將使用者的身分識別權杖中包含的。 下列程式碼範例示範`GetResources`方法，提供這個集合，在 eShopOnContainers 參考應用程式：

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

OpenID Connect 規格指定某些[標準的身分識別資源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低需求是提供支援時，會發出使用者的唯一識別碼。 做法是藉由公開`IdentityResources.OpenId`識別資源。

> [!NOTE]
> `IdentityResources`類別支援的所有 （openid、 電子郵件、 設定檔、 電話和地址） 的 OpenID Connect 規格中定義的範圍。

IdentityServer 也支援定義自訂身分識別的資源。 如需詳細資訊，請參閱 <<c0> [ 定義自訂身分識別資源](http://docs.identityserver.io/en/latest/topics/resources.html#defining-custom-identity-resources)IdentityServer 文件中。 如需詳細資訊`IdentityResource`類型，請參閱[識別資源](https://identityserver4.readthedocs.io/en/latest/reference/identity_resource.html)IdentityServer 4 文件中。

#### <a name="configuring-clients"></a>設定用戶端

用戶端可以從 IdentityServer 要求權杖的應用程式。 一般而言，下列設定必須至少定義每個用戶端：

-   唯一的用戶端識別碼。
-   與權杖服務 （又稱為授與類型） 允許的互動。
-   身分識別和存取權杖傳送到何處的位置 （又稱為重新導向 URI）。
-   用戶端允許存取該資源的清單 （又稱為範圍）。

設定用戶端，當`AddInMemoryClients`方法預期`IEnumerable<Client>`集合。 下列程式碼範例示範 eShopOnContainers 行動應用程式中的組態`GetClients`方法，提供這個集合，在 eShopOnContainers 參考應用程式：

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

這個組態會指定下列屬性的資料：

-   `ClientId`：用戶端的唯一識別碼。
-   `ClientName`：用戶端顯示名稱，用於記錄和在同意畫面。
-   `AllowedGrantTypes`：指定用戶端想要與 IdentityServer 互動的方式。 如需詳細資訊，請參閱[設定驗證流程](#configuring_the_authentication_flow)。
-   `ClientSecrets`：指定從權杖端點要求權杖時所使用的用戶端密碼認證。
-   `RedirectUris`：指定要傳回的權杖或授權碼允許的 Uri。
-   `RequireConsent`：指定是否需要同意畫面。
-   `RequirePkce`：指定是否使用授權碼的用戶端必須傳送的證明金鑰。
-   `PostLogoutRedirectUris`：指定將在登出後重新導向至允許的 Uri。
-   `AllowedCorsOrigins`：指定來源的用戶端，如此 IdentityServer 可以允許跨原始呼叫，從原始伺服器。
-   `AllowedScopes`：指定用戶端可存取的資源。 根據預設，用戶端會有無法存取任何資源。
-   `AllowOfflineAccess`：指定用戶端是否可以要求重新整理權杖。

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>設定驗證流程

驗證流程的用戶端與 IdentityServer 之間可以設定指定在授與類型`Client.AllowedGrantTypes`屬性。 OpenID Connect 和 OAuth 2.0 規格中定義驗證流程，包括的數：

-   隱含的。 此流程適用於瀏覽器為基礎的應用程式，並應針對使用者僅限於驗證的或驗證和存取權杖的要求。 所有語彙基元是瀏覽器中，透過傳輸，以及因此進階功能，例如不允許使用重新整理權杖。
-   授權碼。 此流程可擷取權杖的返回通道，而不是瀏覽器前端通道，同時也支援用戶端驗證的能力。
-   混合式。 此流程是隱含的組合，並授權碼授與類型。 識別權杖透過瀏覽器通道傳輸，並包含帶正負號的通訊協定回應，以及其他成品，例如授權碼。 在驗證成功之後的回應中，返回通道應該用來擷取存取和重新整理權杖。

> [!TIP]
> 使用混合式驗證流程。 混合式驗證流程能降低多個套用至的瀏覽器色頻的攻擊並為要擷取存取權杖 （並可能重新整理權杖） 的原生應用程式建議的流程。

如需有關驗證流程的詳細資訊，請參閱 <<c0> [ 授與類型](https://identityserver4.readthedocs.io/en/latest/topics/grant_types.html)IdentityServer 4 文件中。

### <a name="performing-authentication"></a>執行驗證

針對 IdentityServer 來發行權杖，代表使用者，使用者必須登入 IdentityServer。 不過，IdentityServer 不提供使用者介面或資料庫進行驗證。 因此，eShopOnContainers 參考應用程式中 ASP.NET Core 身分識別就會用於此用途。

EShopOnContainers 的行動裝置應用程式會與 IdentityServer 向混合式驗證流程，說明 圖 9-2。

![](authentication-and-authorization-images/sign-in.png "登入程序的高階概觀")

**圖 9-2:** 登入程序的高階概觀

登入要求對`<base endpoint>:5105/connect/authorize`。 成功驗證之後，IdentityServer 會傳回包含授權碼和身分識別權杖的驗證回應。 授權碼然後傳送至`<base endpoint>:5105/connect/token`，存取、 身分識別，與重新整理權杖做出回應。

EShopOnContainers 行動裝置應用程式簽署外的 IdentityServer 將要求傳送至`<base endpoint>:5105/connect/endsession`，搭配其他參數。 登出，就會發生之後，IdentityServer 回應傳送給行動應用程式的後登出重新導向 URI。 圖 9-3 說明此程序。

![](authentication-and-authorization-images/sign-out.png "登出程序的高階概觀")

**圖 9-3:** 登出程序的高階概觀

在 eShopOnContainers 的行動裝置應用程式，與 IdentityServer 的通訊由執行`IdentityService`類別，它會實作`IIdentityService`介面。 這個介面會指定必須提供實作類別`CreateAuthorizationRequest`， `CreateLogoutRequest`，和`GetTokenAsync`方法。

#### <a name="signing-in"></a>登入

當使用者點選**登入**按鈕`LoginView`，則`SignInCommand`中`LoginViewModel`類別會執行，它會接著執行`SignInAsync`方法。 下列程式碼範例示範此方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

這個方法會叫用`CreateAuthorizationRequest`方法中的`IdentityService`類別，下列程式碼範例所示：

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
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
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

這個方法會建立 IdentityServer 的 URI[授權端點](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)，必要參數。 授權端點位於`/connect/authorize`連接埠上公開為使用者設定的基底端點 5105。 如需有關使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 減少受攻擊面，eShopOnContainers 的行動裝置應用程式實作 OAuth 的程式碼交換 」 (PKCE) 延伸模組的證明金鑰。 PKCE 可防止正在使用如果遭到攔截的授權碼。 做法是產生祕密的驗證器，在授權要求中傳遞的雜湊，用戶端，並提供雜湊兌換授權碼時。 如需 PKCE 的詳細資訊，請參閱[的程式碼交換 OAuth 公用用戶端的證明金鑰](https://tools.ietf.org/html/rfc7636)網際網路工程任務推動小組網站上。

傳回的 URI 會儲存在`LoginUrl`屬性`LoginViewModel`類別。 當`IsLogin`屬性會變成`true`，則[ `WebView` ](xref:Xamarin.Forms.WebView)在`LoginView`變成可見。 `WebView`資料繫結其[ `Source` ](xref:Xamarin.Forms.WebView.Source)屬性設`LoginUrl`屬性`LoginViewModel`類別，並因此會 IdentityServer 登入要求時`LoginUrl`屬性設定為IdentityServer 的授權端點。 當 IdentityServer 收到此要求，並在使用者尚未通過驗證，`WebView`將重新導向至設定登入頁面上，會顯示 圖 9-4。

![](authentication-and-authorization-images/login.png "Web 檢視所顯示的登入頁面")

**圖 9-4:** Web 檢視所顯示的登入頁面

在完成登入之後， [ `WebView` ](xref:Xamarin.Forms.WebView)將重新導向至傳回的 URI。 這`WebView`瀏覽會導致`NavigateAsync`方法中的`LoginViewModel`類別來執行，下列程式碼範例所示：

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

這個方法會剖析包含在傳回的 URI，此驗證回應，前提是有效的授權程式碼，則對 IdentityServer 的提出要求[權杖端點](https://identityserver4.readthedocs.io/en/latest/endpoints/token.html)，傳遞授權程式碼PKCE 密碼驗證器，以及其他必要的參數。 權杖的端點位於`/connect/token`連接埠上公開為使用者設定的基底端點 5105。 如需有關使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

>💡 **提示**:驗證傳回的 Uri。 雖然 eShopOnContainers 的行動裝置應用程式不會驗證傳回的 URI，最佳做法就是驗證，已知的位置，以防止開啟重新導向攻擊是指傳回的 URI。

如果權杖端點收到有效的授權碼和 PKCE 密碼驗證器，它會使用存取權杖、 識別權杖和重新整理權杖來回應。 存取權杖 （可讓 API 資源的存取權） 和識別 token 然後儲存為應用程式設定，並執行頁面導覽。 因此，在 eShopOnContainers 的行動裝置應用程式的整體影響如下： 提供的使用者能夠成功進行驗證與 IdentityServer，它們會瀏覽至`MainView`頁面上，也就是[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)顯示`CatalogView`為其選取的索引標籤。

如需頁面導覽的詳細資訊，請參閱[瀏覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如需[ `WebView` ](xref:Xamarin.Forms.WebView)導覽會讓檢視模型方法來執行，請參閱[叫用的導覽，您可以使用行為](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 應用程式設定的相關資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 也可讓模擬 （mock） 的登入應用程式設定為使用中的模擬 （mock） 服務時`SettingsView`。 在此模式中，應用程式不通訊與 IdentityServer，改為讓使用者登入使用任何認證。

#### <a name="signing-out"></a>簽署外

當使用者點選**登出**按鈕`ProfileView`，則`LogoutCommand`中`ProfileViewModel`類別會執行，它會接著執行`LogoutAsync`方法。 這個方法會執行到的頁面巡覽`LoginView`頁面上，傳遞`LogoutParameter`執行個體設定為`true`做為參數。 在頁面巡覽期間傳遞參數的相關資訊，請參閱[期間瀏覽並傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

當建立檢視後，瀏覽`InitializeAsync`執行檢視的相關聯的檢視模型的方法時，它會再執行`Logout`方法`LoginViewModel`類別，下列程式碼範例所示：

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

這個方法會叫用`CreateLogoutRequest`方法中的`IdentityService`從應用程式設定擷取做為參數的類別，並傳遞身分識別權杖。 如需有關應用程式設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下列程式碼範例示範 `CreateLogoutRequest` 方法：

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

這個方法會建立 IdentityServer 的 URI[結束工作階段的結束點](https://identityserver4.readthedocs.io/en/latest/endpoints/endsession.html#refendsession)，必要參數。 結束工作階段結束點位於`/connect/endsession`連接埠上公開為使用者設定的基底端點 5105。 如需有關使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

傳回的 URI 會儲存在`LoginUrl`屬性`LoginViewModel`類別。 雖然`IsLogin`屬性是`true`，則[ `WebView` ](xref:Xamarin.Forms.WebView)在`LoginView`為可見。 `WebView`資料繫結其[ `Source` ](xref:Xamarin.Forms.WebView.Source)屬性設`LoginUrl`屬性`LoginViewModel`類別，並因此將登出要求對 IdentityServer 時`LoginUrl`屬性設定為IdentityServer 的結束工作階段的端點。 IdentityServer 收到此要求時，假設使用者是登入，登出時發生。 驗證是使用 cookie，由 cookie 驗證中介軟體，從 ASP.NET Core 追蹤。 因此，登出 IdentityServer 移除驗證 cookie，並傳送 URI 傳回至用戶端的登出後重新導向。

在 行動裝置應用程式中， [ `WebView` ](xref:Xamarin.Forms.WebView)會被重新導向到登出後重新導向 URI。 這`WebView`瀏覽會導致`NavigateAsync`方法中的`LoginViewModel`類別來執行，下列程式碼範例所示：

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

這個方法會清除識別權杖和存取權杖，從應用程式設定，並設定`IsLogin`屬性，以`false`，會造成[ `WebView` ](xref:Xamarin.Forms.WebView)上`LoginView`變成不可見的頁面. 最後，`LoginUrl`屬性設定為 URI 的 IdentityServer 的[授權端點](https://identityserver4.readthedocs.io/en/latest/endpoints/authorize.html)，必要的參數，以準備進行下一次使用者起始的登入。

如需頁面導覽的詳細資訊，請參閱[瀏覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如需[ `WebView` ](xref:Xamarin.Forms.WebView)導覽會讓檢視模型方法來執行，請參閱[叫用的導覽，您可以使用行為](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 應用程式設定的相關資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 也可讓 mock 登出應用程式設定為在 SettingsView 中使用模擬 （mock） 的服務時。 在此模式中，應用程式不會與 IdentityServer，通訊，並改為清除任何預存的權杖，從應用程式設定。

<a name="authorization" />

## <a name="authorization"></a>Authorization

驗證後，ASP.NET Core web 應用程式開發介面通常需要授權的存取，可讓服務，讓應用程式開發介面，可以使用已驗證的使用者，但為全部。

限制對 ASP.NET Core MVC 路由的存取可藉由授權將屬性套用至控制器或動作，這會限制存取控制器或動作來驗證的使用者，如下列程式碼範例所示：

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

如果未經授權的使用者嘗試存取控制器或動作標記為`Authorize`屬性，則 MVC 架構會傳回 401 （未經授權） 的 HTTP 狀態碼。

> [!NOTE]
> 您可以指定參數上`Authorize`限制 API 僅供特定使用者的屬性。 如需詳細資訊，請參閱 <<c0> [ 授權](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以整合到授權工作流程，以便提供控制授權的存取權杖。 這個方法會顯示 圖 9-5。

![](authentication-and-authorization-images/authorization.png "授權的存取權杖")

**圖 9-5:** 授權的存取權杖

EShopOnContainers 的行動裝置應用程式與身分識別微服務進行通訊，並要求存取權杖驗證程序的一部分。 存取權杖接著轉送到做為存取要求的一部分的排序和購物籃微服務所公開的 Api。 存取權杖會包含用戶端和使用者的相關資訊。 然後，Api 會使用該資訊來授權存取其資料。 如需如何設定 IdentityServer 來保護 Api 的資訊，請參閱[設定 API 資源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>設定要執行授權 IdentityServer

若要執行與 IdentityServer 的授權，其授權中介軟體，必須加入至 web 應用程式的 HTTP 要求管線中。 中新增中介軟體`ConfigureAuth`web 應用程式的方法`Startup`類別，從叫用`Configure`方法，並從 eShopOnContainers 參考應用程式在下列程式碼範例所示：

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

這個方法可確保只可以使用有效的存取權杖存取 API。 中介軟體驗證傳入的權杖，以確保它從受信任的簽發者，傳送，並驗證權杖的有效用於接收的 API。 因此，瀏覽至 訂購或購物籃控制器會傳回 401 （未經授權的） HTTP 狀態碼，指出存取語彙基元為必要。

> [!NOTE]
> IdentityServer 的授權中介軟體必須新增到 web 應用程式的 HTTP 要求管線，才能新增與 MVC`app.UseMvc()`或`app.UseMvcWithDefaultRoute()`。

### <a name="making-access-requests-to-apis"></a>進行 Api 的存取要求

提出要求時的排序和購物籃微服務，存取權杖，從 IdentityServer 取得在驗證過程中，必須包含在要求中，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

存取權杖儲存為應用程式設定，從特定平台儲存體擷取且包含在呼叫`GetOrderAsync`方法中的`OrderService`類別。

同樣地，存取權杖必須包含時將資料傳送至 IdentityServer 受保護的 API，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

存取權杖是從特定平台儲存體擷取，而且包含在呼叫`UpdateBasketAsync`方法中的`BasketService`類別。

`RequestProvider`類別，在 eShopOnContainers 的行動應用程式中使用`HttpClient`eShopOnContainers 參考應用程式所公開的 RESTful api 提出要求的類別。 當進行要求的順序和購物籃 Api，其需要授權，則必須包含與要求有效的存取權杖。 這藉由新增的標頭的存取權杖來達成`HttpClient`執行個體，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`DefaultRequestHeaders`的屬性`HttpClient`類別會公開與每個要求一起傳送的標頭和存取權杖新增到`Authorization`加上字串的標頭`Bearer`。 當要求傳送至 RESTful API，而值`Authorization`標頭是擷取和驗證，以確保受信任的簽發者，從已傳送，以及用來判斷使用者是否具有權限叫用 API 接收它。

如需有關如何在 eShopOnContainers 的行動應用程式提出 web 要求的詳細資訊，請參閱[存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>總結

有多種方法可將驗證與授權整合至 Xamarin.Forms 應用程式與 ASP.NET MVC web 應用程式進行通訊。 EShopOnContainers 的行動裝置應用程式會執行驗證與授權使用 IdentityServer 4 的容器化的身分識別微服務。 IdentityServer 是 ASP.NET Core 識別來執行 bearer 權杖驗證與整合的 ASP.NET Core 開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

行動裝置應用程式會從 IdentityServer，要求安全性權杖來驗證使用者或存取資源。 當存取資源時，需要授權之 Api 的要求中必須包含存取權杖。 IdentityServer 的中介軟體驗證連入的存取權杖，以確保它們所傳來的受信任的簽發者，並有效搭配接收到的 API。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
