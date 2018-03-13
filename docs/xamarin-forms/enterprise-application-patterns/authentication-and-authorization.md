---
title: "驗證和授權"
ms.topic: article
ms.prod: xamarin
ms.assetid: e3f27b4c-f7f5-4839-a48c-30bcb919c59e
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/08/2017
ms.openlocfilehash: 5f27c1acefa63ee26184b8997594630e24cb0acc
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="authentication-and-authorization"></a>驗證和授權

驗證是來自使用者、 取得識別認證，例如名稱和密碼，並確認憑證授權單位符合的處理程序。 如果認證有效，提交此憑證的實體會被視為已通過。 一旦已驗證識別，授權程序會判斷該身分識別是否已指定資源的存取權。

有許多方法可以整合到 Xamarin.Forms 應用程式的 ASP.NET MVC web 應用程式，包括使用 ASP.NET Core 身分識別，例如 Google、 Microsoft 外部驗證提供者與通訊的驗證和授權Facebook、 Twitter 或驗證中介軟體。 EShopOnContainers 行動裝置應用程式會執行驗證和授權的方式使用 IdentityServer 4 進行容器化的身分識別微服務。 行動裝置應用程式會從 IdentityServer，要求安全性權杖來驗證使用者或存取資源。 如 IdentityServer 以簽發權杖，代表使用者，使用者必須登入 IdentityServer。 不過，IdentityServer 不提供使用者介面或資料庫進行驗證。 因此，eShopOnContainers 參考應用程式中 ASP.NET Core 身分識別就會用於此用途。

## <a name="authentication"></a>驗證

應用程式需要知道目前使用者的身分識別時，需要驗證。 ASP.NET Core 識別使用者的主要機制是 ASP.NET Core 識別成員資格系統，為開發人員所設定的資料存放區中儲存使用者資訊。 通常，這個資料存放區會執行 EntityFramework 存放區中，雖然自訂存放區或協力廠商封裝可用於將身分識別資訊儲存在 Azure 儲存體、 DocumentDB 或其他位置。

驗證案例，請使用本機使用者資料存放區，且已存在要求透過 cookie （跟一般在 ASP.NET MVC web 應用程式） 之間的識別資訊，ASP.NET Core Identity 是適合的解決方案。 不過，cookie 不一定自然的保存及傳輸資料。 例如，會公開 rest 式端點從行動裝置應用程式存取的 ASP.NET Core web 應用程式通常需要使用 bearer 權杖驗證，因為 cookie 不能用在此案例中。 不過，持有者權杖可輕鬆擷取及包含在從行動裝置應用程式進行的 web 要求的授權標頭。

### <a name="issuing-bearer-tokens-using-identityserver-4"></a>發行使用 IdentityServer 4 的持有人權杖

[IdentityServer 4](https://github.com/IdentityServer/IdentityServer4)適用於 ASP.NET Core，可以使用許多驗證和授權案例，包括發行本機 ASP.NET Core 識別使用者的安全性權杖是一種開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

> [!NOTE]
> OpenID Connect 和 OAuth 2.0 都很相似，同時擁有不同的責任。

OpenID Connect 是驗證之上的層級的 OAuth 2.0 通訊協定。 OAuth 2 是一種通訊協定，可讓應用程式從安全性權杖服務要求存取權杖，並使用它們與應用程式開發介面進行通訊。 此委派會減少用戶端應用程式和應用程式開發介面的複雜度，因為可以集中式驗證和授權。

IdentityServer 4 是實作這些通訊協定與 OpenID Connect 和 OAuth 2.0 的組合結合兩個基本的安全性考量的驗證和應用程式開發介面存取。

使用直接用戶端的微服務通訊，例如 eShopOnContainers 參考應用程式，應用程式中做為安全性權杖服務 (STS) 的專用的驗證微服務可用來驗證使用者，如圖所示9-1。 如需直接以微服務的用戶端通訊的詳細資訊，請參閱[用戶端之間通訊和 Microservices](~/xamarin-forms/enterprise-application-patterns/containerized-microservices.md#communication_between_client_and_microservices)。

![](authentication-and-authorization-images/authentication.png "專用的驗證微服務驗證")

**圖 9-1:**專用的驗證微服務驗證

EShopOnContainers 行動裝置應用程式會識別微服務，來使用 IdentityServer 4 執行驗證，以及應用程式開發介面的存取控制與通訊。 因此，行動裝置應用程式要求語彙基元從 IdentityServer，用於驗證的使用者或存取資源：

-   驗證使用者與 IdentityServer 達成方式是行動裝置應用程式要求*識別*語彙基元，代表驗證程序的結果。 在最低限度，它包含使用者及使用者驗證的方式和時機的相關資訊的識別碼。 它也可以包含其他身分識別資料。
-   存取與 IdentityServer 資源達成方式是行動裝置應用程式要求*存取*語彙基元，可讓應用程式開發介面資源的存取權。 用戶端要求存取權杖，並將其轉寄給應用程式開發介面。 存取權杖會包含用戶端和使用者的相關資訊 （如果有的話）。 然後，應用程式開發介面會使用該資訊來授權存取其資料。

> [!NOTE]
> 必須先使用 IdentityServer 登錄用戶端，它可以要求權杖。

### <a name="adding-identityserver-to-a-web-application"></a>IdentityServer 加入 Web 應用程式

為了讓 ASP.NET Core web 應用程式使用 IdentityServer 4，它必須加入至 web 應用程式的 Visual Studio 方案。 如需詳細資訊，請參閱[安裝和概觀](https://identityserver4.readthedocs.io/en/release/quickstarts/0_overview.html)IdentityServer 文件中。

一旦 IdentityServer 包含 web 應用程式的 Visual Studio 方案中，就必須被新增至 web 應用程式的 HTTP 要求處理管線，如此才可提供對 OpenID Connect 和 OAuth 2.0 端點的要求。 這在達成`Configure`web 應用程式中的方法`Startup`類別，如下列程式碼範例所示：

```csharp
public void Configure(  
    IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)  
{  
    ...  
    app.UseIdentity();  
    ...  
}
```

順序很重要的 web 應用程式的 HTTP 要求處理管線中。 因此，IdentityServer 必須新增至管線，才 UI 架構實作登入畫面。

### <a name="configuring-identityserver"></a>設定 IdentityServer

應在中設定 IdentityServer `ConfigureServices` web 應用程式中的方法`Startup`藉由呼叫類別`services.AddIdentityServer`方法，如下列程式碼範例從 eShopOnContainers 參考應用程式中所示範：

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

在呼叫`services.AddIdentityServer`方法，其他 fluent Api 呼叫來設定下列各項：

-   用於簽署的憑證。
-   使用者可能會要求的應用程式開發介面和身分識別資源的存取權。
-   將連線要求權杖的用戶端。
-   ASP.NET Core 身分識別。

>💡 **提示**： 以動態方式載入 IdentityServer 4 組態。 IdentityServer 4 Api 可讓設定 IdentityServer 從記憶體中的組態物件清單。 在 eShopOnContainers 參考應用程式，這些記憶體中集合會是硬式編碼至應用程式。 不過，在生產案例中可以載入以動態方式從組態檔或資料庫。

如需設定為使用 ASP.NET Core 識別 IdentityServer 資訊，請參閱[使用 ASP.NET Identity Core](https://identityserver4.readthedocs.io/en/release/quickstarts/6_aspnet_identity.html) IdentityServer 文件中。

#### <a name="configuring-api-resources"></a>設定應用程式開發介面資源

設定應用程式開發介面資源時`AddInMemoryApiResources`方法預期`IEnumerable<ApiResource>`集合。 下列程式碼範例示範`GetApis`提供 eShopOnContainers 此集合的方法參考應用程式：

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

這個方法指定 IdentityServer 應該保護訂單和購物籃應用程式開發介面。 因此，IdentityServer 受管理存取權杖時必須進行這些 api 的呼叫。 如需有關`ApiResource`類型，請參閱[API 資源](https://identityserver4.readthedocs.io/en/release/reference/api_resource.html#refapiresource)IdentityServer 4 文件中。

#### <a name="configuring-identity-resources"></a>設定身分識別的資源

設定身分識別的資源時`AddInMemoryIdentityResources`方法預期`IEnumerable<IdentityResource>`集合。 識別資源是資料，例如使用者識別碼、 名稱或電子郵件地址。 每個身分識別的資源都有唯一的名稱，並任意宣告類型可以指派給它，然後將會包含在使用者的身分識別語彙基元。 下列程式碼範例示範`GetResources`提供 eShopOnContainers 此集合的方法參考應用程式：

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

OpenID Connect 規格指定某些[標準識別身分資源](https://openid.net/specs/openid-connect-core-1_0.html#ScopeClaims)。 最低需求是支援會提供給發出使用者的唯一識別碼。 這藉由公開`IdentityResources.OpenId`識別資源。

> [!NOTE]
> `IdentityResources`類別支援的所有 （openid、 電子郵件、 設定檔、 電話和位址） 的 OpenID Connect 規格中定義的範圍。

IdentityServer 也支援定義自訂身分識別的資源。 如需詳細資訊，請參閱[定義自訂身分識別資源](https://identityserver4.readthedocs.io/en/release/topics/resources.html#defining-custom-identity-resources)IdentityServer 文件中。 如需有關`IdentityResource`類型，請參閱[識別資源](https://identityserver4.readthedocs.io/en/release/reference/identity_resource.html)IdentityServer 4 文件中。

#### <a name="configuring-clients"></a>設定用戶端

用戶端是可以從 IdentityServer 要求權杖的應用程式。 一般而言，下列設定必須至少定義每個用戶端：

-   用戶端唯一識別碼。
-   允許的權杖服務 （又稱為授與類型） 互動。
-   身分識別和存取權杖傳送到其中的位置 （又稱為重新導向 URI）。
-   允許的存取權的用戶端的資源的清單 （稱為 「 範圍 」）。

設定用戶端時`AddInMemoryClients`方法預期`IEnumerable<Client>`集合。 下列程式碼範例示範 eShopOnContainers 行動裝置應用程式中的組態`GetClients`提供 eShopOnContainers 此集合的方法參考應用程式：

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

這個組態會指定下列屬性的資料：

-   `ClientId`: 用戶端的唯一識別碼。
-   `ClientName`： 用戶端顯示名稱，用於記錄和同意畫面。
-   `AllowedGrantTypes`： 指定用戶端想要與 IdentityServer 互動的方式。 如需詳細資訊，請參閱[設定驗證流程](#configuring_the_authentication_flow)。
-   `ClientSecrets`： 指定從權杖端點要求權杖時所使用的用戶端秘密認證。
-   `RedirectUris`： 指定要傳回的語彙基元或授權碼允許的 Uri。
-   `RequireConsent`： 指定是否需要同意畫面。
-   `RequirePkce`： 指定是否使用授權碼的用戶端必須傳送證明金鑰。
-   `PostLogoutRedirectUris`： 指定將在登出後重新導向至允許的 Uri。
-   `AllowedCorsOrigins`： 如此 IdentityServer 可以允許從原點的跨原始呼叫，請指定用戶端的來源。
-   `AllowedScopes`： 指定用戶端可存取的資源。 根據預設，用戶端有沒有存取權的任何資源。
-   `AllowOfflineAccess`： 指定用戶端是否可以要求重新整理權杖。

<a name="configuring_the_authentication_flow" />

#### <a name="configuring-the-authentication-flow"></a>設定的驗證流程

驗證流程用戶端與 IdentityServer 之間可以設定指定在授與類型`Client.AllowedGrantTypes`屬性。 OpenID Connect 和 OAuth 2.0 規格定義的數字的驗證流程，包括：

-   隱含的。 此流程最佳化瀏覽器型應用程式，並且應該用於驗證僅限的使用者或驗證和存取權杖的要求。 所有語彙基元是透過瀏覽器中，傳送，並因此進階功能，例如不允許重新整理權杖。
-   授權碼。 此流程會提供擷取語彙基元在返回通道，而不是瀏覽器前端通道，同時也支援用戶端驗證的能力。
-   混合式。 此流程是隱含的組合，而且授權碼授與類型。 識別權杖透過瀏覽器通道傳送，並包含帶正負號的通訊協定回應，以及其他成品，例如授權碼。 在驗證成功之後的回應後頻道應該用於擷取存取和重新整理權杖。

> [!TIP]
> 使用混合式驗證流程。 混合式驗證流程可以降低多個套用至瀏覽器通道，攻擊，並且想要擷取存取權杖 （可能是重新整理權杖） 的原生應用程式建議的流程。

如需有關驗證流程的詳細資訊，請參閱[授與類型](https://identityserver4.readthedocs.io/en/release/topics/grant_types.html)IdentityServer 4 文件中。

### <a name="performing-authentication"></a>執行驗證

如 IdentityServer 以簽發權杖，代表使用者，使用者必須登入 IdentityServer。 不過，IdentityServer 不提供使用者介面或資料庫進行驗證。 因此，eShopOnContainers 參考應用程式中 ASP.NET Core 身分識別就會用於此用途。

混合式驗證流程，說明圖 9-2 IdentityServer 驗證 eShopOnContainers 行動裝置應用程式。

![](authentication-and-authorization-images/sign-in.png "登入程序的高階概觀")

**圖 9-2:**登入程序的高階概觀

對登入的要求`<base endpoint>:5105/connect/authorize`。 驗證成功，後面 IdentityServer 會傳回包含授權碼和識別權杖的驗證回應。 授權碼然後傳送至`<base endpoint>:5105/connect/token`，它會存取、 識別和重新整理權杖以回應。

EShopOnContainers 行動裝置應用程式簽署外 IdentityServer 所要求傳送到`<base endpoint>:5105/connect/endsession`，與其他參數。 登出之後，IdentityServer 回應傳送回行動裝置應用程式後登出重新導向 URI。 圖 9-3 說明此程序。

![](authentication-and-authorization-images/sign-out.png "登出程序的高階概觀")

**圖 9-3:**登出程序的高階概觀

EShopOnContainers 行動裝置應用程式，在與 IdentityServer 通訊由執行`IdentityService`類別，它會實作`IIdentityService`介面。 這個介面會指定必須提供實作類別`CreateAuthorizationRequest`， `CreateLogoutRequest`，和`GetTokenAsync`方法。

#### <a name="signing-in"></a>登入

當使用者點選**登入**按鈕`LoginView`、`SignInCommand`中`LoginViewModel`執行類別時，它會接著執行`SignInAsync`方法。 下列程式碼範例會示範這個方法：

```csharp
private async Task SignInAsync()  
{  
    ...  
    LoginUrl = _identityService.CreateAuthorizationRequest();  
    IsLogin = true;  
    ...  
}
```

這個方法會叫用`CreateAuthorizationRequest`方法中的`IdentityService`類別，下列程式碼範例所示：

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
    dic.Add("redirect_uri", GlobalSetting.Instance.IdentityCallback);
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

這個方法會為 IdentityServer 的建立 URI[授權端點](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html)，必要參數。 授權端點位於`/connect/authorize`5105 基底的結束點公開為使用者設定的連接埠。 如需使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> 減少受攻擊面 eShopOnContainers 行動裝置應用程式的實作 OAuth 的程式碼 Exchange (PKCE) 延伸模組的證明金鑰。 PKCE 可防止授權程式碼中的使用如果遭到攔截。 這藉由用戶端產生密碼的驗證器，授權要求中傳遞的雜湊，並提供未雜湊當兌換授權碼。 如需 PKCE 的詳細資訊，請參閱[OAuth 公用的用戶端程式碼 Exchange 的證明金鑰](https://tools.ietf.org/html/rfc7636)網際網路工程任務推動小組網站上。

傳回的 URI 會儲存在`LoginUrl`屬性`LoginViewModel`類別。 當`IsLogin`屬性變成`true`、 [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)中`LoginView`隨即顯示。 `WebView`資料繫結其[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/)屬性`LoginUrl`屬性`LoginViewModel`類別，並因此 IdentityServer 就會登入要求時`LoginUrl`屬性設定為IdentityServer 的授權端點。 當 IdentityServer 收到此要求，且使用者未經過驗證，`WebView`會重新導向至設定登入頁面上，會顯示圖 9-4。

![](authentication-and-authorization-images/login.png "登入頁面顯示的網頁檢視")

**圖 9-4:** WebView 所顯示的登入頁面

一旦完成登入， [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)會重新導向至傳回 URI。 這`WebView`導覽會導致`NavigateAsync`方法中的`LoginViewModel`類別要執行下列程式碼範例所示：

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

這個方法會傳回 URI 中所包含的驗證回應，前提是有效的驗證程式碼存在於，它就會要求 IdentityServer 的[權杖端點](https://identityserver4.readthedocs.io/en/release/endpoints/token.html)，傳遞授權程式碼PKCE 密碼驗證器，以及其他必要參數。 權杖的端點位於`/connect/token`5105 基底的結束點公開為使用者設定的連接埠。 如需使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

>💡 **提示**： 驗證傳回 Uri。 雖然 eShopOnContainers 行動裝置應用程式不會驗證傳回 URI，最佳作法是確認傳回的 URI 參考到已知位置，以防止開啟重新導向攻擊。

如果權杖端點收到有效的授權碼和 PKCE 密碼驗證器，它會以存取權杖、 識別權杖和重新整理權杖來回應。 （可讓應用程式開發介面資源的存取權） 的存取權杖和身分識別語彙基元然後儲存為應用程式設定，並執行頁面巡覽。 因此，eShopOnContainers 行動應用程式中的整體影響如下： 它們假設使用者是能夠成功地驗證 IdentityServer，巡覽至`MainView` 頁面上，也就是[ `TabbedPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TabbedPage/)顯示`CatalogView`做為其選取的索引標籤。

頁面導覽的相關資訊，請參閱[瀏覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如需有關資訊[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)導覽會執行，請參閱 < 檢視模型方法[叫用的瀏覽使用行為](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 應用程式設定的相關資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 也可讓模擬的登入應用程式設定為使用中的模擬服務時`SettingsView`。 在此模式中，應用程式不會與 IdentityServer，而允許使用任何認證登入使用者進行通訊。

#### <a name="signing-out"></a>簽署外

當使用者點選**登出**按鈕`ProfileView`、`LogoutCommand`中`ProfileViewModel`執行類別時，它會接著執行`LogoutAsync`方法。 這個方法會執行頁面巡覽至`LoginView`頁面上，將傳遞`LogoutParameter`設執行個體`true`做為參數。 頁面巡覽期間傳遞參數的相關資訊，請參閱[期間瀏覽傳遞參數](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

當建立檢視並瀏覽至，`InitializeAsync`執行檢視的相關聯的檢視模型的方法，然後執行所在`Logout`方法`LoginViewModel`類別，下列程式碼範例所示：

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

這個方法會叫用`CreateLogoutRequest`方法中的`IdentityService`類別，並傳遞身分識別權杖擷取應用程式設定做為參數。 如需應用程式設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。 下列程式碼範例示範`CreateLogoutRequest`方法：

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

這個方法會建立 IdentityServer 的 URI[結束工作階段結束點](https://identityserver4.readthedocs.io/en/release/endpoints/endsession.html#refendsession)，必要參數。 結束工作階段結束點位於`/connect/endsession`5105 基底的結束點公開為使用者設定的連接埠。 如需使用者設定的詳細資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

傳回的 URI 會儲存在`LoginUrl`屬性`LoginViewModel`類別。 雖然`IsLogin`屬性是`true`、 [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)中`LoginView`為可見。 `WebView`資料繫結其[ `Source` ](https://developer.xamarin.com/api/property/Xamarin.Forms.WebView.Source/)屬性`LoginUrl`屬性`LoginViewModel`類別，並因此將登出要求對 IdentityServer 時`LoginUrl`屬性設定為IdentityServer 的結束工作階段的端點。 IdentityServer 收到此要求時，假設使用者是登入，登出時發生。 驗證是使用 cookie，由 cookie 驗證中介軟體，從 ASP.NET 核心追蹤。 因此，登出 IdentityServer 移除驗證 cookie，並將傳送 post 登出重新導向 URI 傳回至用戶端。

在行動裝置應用程式， [ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)會重新導向至 post 登出重新導向 URI。 這`WebView`導覽會導致`NavigateAsync`方法中的`LoginViewModel`類別要執行下列程式碼範例所示：

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

這個方法會清除應用程式設定的存取權杖和身分識別語彙基元，並設定`IsLogin`屬性`false`，這會導致[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)上`LoginView`變成不可見的頁面. 最後，`LoginUrl`屬性設定為 URI 的 IdentityServer 的[授權端點](https://identityserver4.readthedocs.io/en/release/endpoints/authorize.html)，必要的參數，以準備進行下一次使用者啟動的登入。

頁面導覽的相關資訊，請參閱[瀏覽](~/xamarin-forms/enterprise-application-patterns/navigation.md)。 如需有關資訊[ `WebView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.WebView/)導覽會執行，請參閱 < 檢視模型方法[叫用的瀏覽使用行為](~/xamarin-forms/enterprise-application-patterns/navigation.md#invoking_navigation_using_behaviors)。 應用程式設定的相關資訊，請參閱[Configuration Management](~/xamarin-forms/enterprise-application-patterns/configuration-management.md)。

> [!NOTE]
> EShopOnContainers 也允許模擬登出應用程式設定為使用中 SettingsView 模擬服務時。 在此模式中，應用程式不會運用通訊 IdentityServer，並改為清除 從應用程式設定任何預存的語彙基元。

<a name="authorization" />

## <a name="authorization"></a>授權

驗證後，ASP.NET Core web 應用程式開發介面通常需要授權的存取，可讓服務，讓應用程式開發介面，可以使用已驗證的使用者，但為全部。

限制對 ASP.NET Core MVC 路由的存取可藉由授權將屬性套用至控制器或動作，這會限制存取控制器或動作來驗證的使用者，如下列程式碼範例所示：

```csharp
[Authorize]  
public class BasketController : Controller  
{  
    ...  
}
```

如果未經授權的使用者嘗試存取控制器或動作標示為`Authorize`屬性，MVC framework 會傳回 401 （未經授權） HTTP 狀態碼。

> [!NOTE]
> 參數上指定`Authorize`屬性來限制特定使用者的應用程式開發介面。 如需詳細資訊，請參閱[授權](/aspnet/core/security/authorization/introduction/)。

IdentityServer 可以整合到授權工作流程，以便提供控制授權的存取權杖。 這個方法會顯示圖 9-5。

![](authentication-and-authorization-images/authorization.png "存取語彙基元所授權")

**圖 9-5:**授權的存取權杖

EShopOnContainers 行動裝置應用程式與身分識別的微服務通訊，並要求存取權杖驗證程序的一部分。 存取權杖再轉送到應用程式開發介面，排序和購物籃 microservices 所公開做為存取要求的一部分。 存取權杖包含用戶端和使用者的相關資訊。 然後，應用程式開發介面會使用該資訊來授權存取其資料。 如需如何設定 IdentityServer 來保護應用程式開發介面的資訊，請參閱[設定應用程式開發介面資源](#configuring-api-resources)。

### <a name="configuring-identityserver-to-perform-authorization"></a>設定要執行授權 IdentityServer

若要執行以 IdentityServer 授權，其授權中介軟體，必須加入至 web 應用程式的 HTTP 要求管線中。 中介軟體會新增`ConfigureAuth`web 應用程式中的方法`Startup`類別，從叫用`Configure`方法，和 eShopOnContainers 參考應用程式的下列程式碼範例所示：

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

這個方法可確保應用程式開發介面只能存取使用有效的存取權杖。 中介軟體驗證以確保它會傳送受信任的簽發者，從傳入的權杖，並驗證權杖是有效的若要搭配所收到的 API。 因此，瀏覽至排序或購物籃控制器將會傳回 401 （未經授權） HTTP 狀態碼，表示存取語彙基元為必要。

> [!NOTE]
> IdentityServer 的授權中介軟體必須加入至 web 應用程式的 HTTP 要求管線，然後再加入與 MVC`app.UseMvc()`或`app.UseMvcWithDefaultRoute()`。

### <a name="making-access-requests-to-apis"></a>應用程式開發介面進行存取的要求

提出要求來排序和購物籃 microservices，存取語彙基元，在驗證過程中，取自 IdentityServer 時必須包含在要求中，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
Order = await _ordersService.GetOrderAsync(Convert.ToInt32(order.OrderNumber), authToken);
```

存取權杖儲存為應用程式設定，從特定平台儲存體擷取且包含在呼叫`GetOrderAsync`方法中的`OrderService`類別。

同樣地，存取權杖必須包含將資料傳送至 IdentityServer 保護應用程式開發介面，如下列程式碼範例所示：

```csharp
var authToken = Settings.AuthAccessToken;  
await _basketService.UpdateBasketAsync(new CustomerBasket  
{  
    BuyerId = userInfo.UserId,   
    Items = BasketItems.ToList()  
}, authToken);
```

是從特定平台儲存體擷取存取權杖，並將其併入呼叫`UpdateBasketAsync`方法中的`BasketService`類別。

`RequestProvider` EShopOnContainers 行動應用程式，會使用的類別， `HttpClient` eShopOnContainers 參考應用程式所公開的 RESTful api 提出要求的類別。 當進行要求的順序和購物籃 Api，其需要授權，則必須包含與要求的有效存取權杖。 這藉由新增的標頭的存取權杖`HttpClient`執行個體，如下列程式碼範例所示：

```csharp
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
```

`DefaultRequestHeaders`屬性`HttpClient`類別會公開與每個要求傳送的標頭和存取權杖加入至`Authorization`加上字串的標頭`Bearer`。 當要求傳送至 rest 式 API，值`Authorization`標頭會擷取並驗證，以確保受信任的簽發者，從已傳送，以及用來判斷使用者是否有權限叫用 API 接收它。

如需如何 eShopOnContainers 行動裝置應用程式會提出 web 要求的詳細資訊，請參閱[存取遠端資料](~/xamarin-forms/enterprise-application-patterns/accessing-remote-data.md)。

## <a name="summary"></a>總結

有許多方法可以將驗證和授權整合到 Xamarin.Forms 應用程式與 ASP.NET MVC web 應用程式進行通訊。 EShopOnContainers 行動裝置應用程式會執行驗證和授權的方式使用 IdentityServer 4 進行容器化的身分識別微服務。 IdentityServer 是 ASP.NET Core 識別來執行 bearer 權杖驗證與整合的 ASP.NET Core 開放原始碼 OpenID Connect 和 OAuth 2.0 架構。

行動裝置應用程式會從 IdentityServer，要求安全性權杖來驗證使用者或存取資源。 當存取資源時，需要授權的 Api 的要求中必須包含存取權杖。 IdentityServer 的中介軟體驗證連入的存取權杖，以確保它們會從受信任的簽發者，傳送，而且它們會有效能與接收它們的 API。


## <a name="related-links"></a>相關連結

- [下載電子書 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （範例）](https://github.com/dotnet-architecture/eShopOnContainers)
