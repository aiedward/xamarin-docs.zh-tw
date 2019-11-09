---
title: 具有身分識別提供者的 AuthenticateUsers
description: 本文說明如何使用 Xamarin. Auth 來管理 Xamarin. Forms 應用程式中的驗證進程。
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2019
ms.openlocfilehash: 83fbad8a9bbb9afef5ee80705fe9e86e51284e7d
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842987"
---
# <a name="authenticate-users-with-an-identity-provider"></a>使用身分識別提供者驗證使用者

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)

_「驗證」是一種跨平臺的 SDK，用於驗證使用者及儲存其帳戶。其中包含的 OAuth 驗證器可支援取用身分識別提供者（例如 Google、Microsoft、Facebook 和 Twitter）。本文說明如何使用 Xamarin. Auth 來管理 Xamarin. Forms 應用程式中的驗證進程。_

OAuth 是一種用於驗證的開放式標準，可讓資源擁有者通知資源提供者，該許可權應授與協力廠商存取其資訊，而不需共用資源擁有者身分識別。 其中一個範例是讓使用者通知身分識別提供者（例如 Google、Microsoft、Facebook 或 Twitter）該許可權應授與應用程式以存取其資料，而不需要共用使用者的身分識別。 這通常是使用者使用身分識別提供者登入網站和應用程式的方法，但不會將其密碼公開給網站或應用程式。

使用 OAuth 身分識別提供者時，驗證流程的高階總覽如下所示：

1. 應用程式會將瀏覽器導覽至身分識別提供者 URL。
1. 識別提供者會處理使用者驗證，並將授權碼傳回給應用程式。
1. 應用程式會從識別提供者交換存取權杖的授權碼。
1. 應用程式會使用存取權杖來存取身分識別提供者上的 Api，例如用來要求基本使用者資料的 API。

範例應用程式會示範如何使用 Xamarin 來對 Google 執行原生驗證流程。 雖然在本主題中使用 Google 做為身分識別提供者，但此方法同樣適用于其他身分識別提供者。 如需使用 Google OAuth 2.0 端點進行驗證的詳細資訊，請參閱[使用 OAuth 2.0 存取](https://developers.google.com/identity/protocols/OAuth2)google 網站上的 google api。

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性（ATS）會強制執行網際網路資源（例如應用程式的後端伺服器）與應用程式之間的安全連線，藉此防止意外洩漏機密資訊。 由於預設會在針對 iOS 9 建立的應用程式中啟用 ATS，因此所有連線都將受限於 ATS 安全性需求。 如果連線不符合這些需求，則會失敗並產生例外狀況。
> 如果無法使用網際網路資源的 `HTTPS` 通訊協定和安全通訊，可以退出宣告 ATS。 這可以藉由更新應用程式的**plist**檔案來達成。 如需詳細資訊，請參閱[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin 驗證使用者

「驗證」支援兩種應用程式方法，以與身分識別提供者的授權端點進行互動：

1. 使用內嵌的 web view。 雖然這是常見的作法，但已不再建議使用，原因如下：

    - 裝載 web view 的應用程式可以存取使用者的完整驗證認證，而不只是適用于應用程式的 OAuth 授權授與。 這違反最低許可權的原則，因為應用程式可以存取比所需更強大的認證，因此可能會增加應用程式的受攻擊面。
    - 主應用程式可以捕捉使用者名稱和密碼、自動提交表單並略過使用者同意，以及複製會話 cookie，並使用它們來執行驗證的動作。
    - 內嵌的 web views 不會與其他應用程式或裝置的網頁瀏覽器共用驗證狀態，而需要使用者登入每個授權要求，這會被視為較差的使用者體驗。
    - 某些授權端點會採取步驟來偵測並封鎖來自 web views 的授權要求。

1. 使用裝置的網頁瀏覽器，這是建議的方法。 使用適用于 OAuth 要求的裝置瀏覽器可改善應用程式的可用性，因為使用者只需要每個裝置登入身分識別提供者一次，即可改善應用程式中登入和授權流程的轉換率。 裝置瀏覽器也提供改良的安全性，因為應用程式可以檢查和修改 web 視圖中的內容，而不是瀏覽器中顯示的內容。 這是本文和範例應用程式中所採用的方法。

下圖顯示範例應用程式如何使用 Xamarin 驗證使用者，並取得其基本資料的高階總覽：

![](oauth-images/google-auth.png "Using Xamarin.Auth to Authenticate with Google")

應用程式會使用 `OAuth2Authenticator` 類別向 Google 進行驗證要求。 當使用者透過其登入頁面（包含存取權杖）成功通過 Google 驗證後，會傳回驗證回應。 然後，應用程式會使用 `OAuth2Request` 類別，對 Google 提出基本使用者資料的要求，並將存取權杖包含在要求中。

### <a name="setup"></a>安裝程式

必須建立 Google API 主控台專案，才能將 Google 登入與 Xamarin. Forms 應用程式整合。 執行下列工作即可達成這點：

1. 前往[GOOGLE API 主控台](https://console.developers.google.com)網站，並使用 google 帳號憑證登入。
1. 從 [專案] 下拉式選單中，選取現有的專案，或建立新的專案。
1. 在 [API 管理員] 底下的提要欄位中，選取 [**認證**]，然後選取 [ **OAuth 同意畫面]** 索引標籤。選擇 [**電子郵件地址**]，指定**向使用者顯示的產品名稱**，然後按 [**儲存**]。
1. 在 [**認證**] 索引標籤中，選取 [**建立認證**] 下拉式清單，然後選擇 [ **OAuth 用戶端識別碼**]。
1. 在 [**應用程式類型**] 底下，選取行動應用程式將在其上執行的平臺（**iOS**或**Android**）。
1. 填入必要的詳細資料，然後選取 [**建立**] 按鈕。

> [!NOTE]
> 用戶端識別碼可讓應用程式存取已啟用的 Google Api，而行動應用程式則是單一平臺獨有的。 因此，應針對將使用 Google 登入的每個平臺建立**OAuth 用戶端識別碼**。

執行這些步驟之後，就可以使用 Xamarin 來起始 Google 的 OAuth2 驗證流程。

### <a name="creating-and-configuring-an-authenticator"></a>建立和設定驗證器

驗證的 `OAuth2Authenticator` 類別負責處理 OAuth 驗證流程。 下列程式碼範例顯示使用裝置的網頁瀏覽器執行驗證時，`OAuth2Authenticator` 類別的具現化：

```csharp
var authenticator = new OAuth2Authenticator(
    clientId,
    null,
    Constants.Scope,
    new Uri(Constants.AuthorizeUrl),
    new Uri(redirectUri),
    new Uri(Constants.AccessTokenUrl),
    null,
    true);
```

`OAuth2Authenticator` 類別需要多個參數，如下所示：

- **用戶端識別碼**–這會識別提出要求的用戶端，並可從[Google API 主控台](https://console.developers.google.com)中的專案抓取。
- **用戶端密碼**–這應該是 `null` 或 `string.Empty`。
- **範圍**–這會識別應用程式所要求的 API 存取，而值會通知向使用者顯示的同意畫面。 如需有關範圍的詳細資訊，請參閱在 Google 網站上[授權 API 要求](https://developers.google.com/+/web/api/rest/oauth)。
- **授權 url** –這會識別用來取得授權碼的 url。
- [重新**導向 url** ] –這會識別要傳送回應的 url。 此參數的值必須符合在[Google 開發人員主控台](https://console.developers.google.com/)中，專案的 [**認證**] 索引標籤中出現的其中一個值。
- **AccessToken url** –這會識別取得授權碼之後，用來要求存取權杖的 Url。
- **GetUserNameAsync Func** –選擇性的 `Func`，將在成功驗證帳戶的使用者名稱後，用來以非同步方式取出該帳戶的使用者名稱。
- **使用原生 UI** – `boolean` 值，指出是否要使用裝置的網頁瀏覽器來執行驗證要求。

### <a name="setup-authentication-event-handlers"></a>設定驗證事件處理常式

在呈現使用者介面之前，必須先註冊 `OAuth2Authenticator.Completed` 事件的事件處理常式，如下列程式碼範例所示：

```csharp
authenticator.Completed += OnAuthCompleted;
```

當使用者成功驗證或取消登入時，就會引發此事件。

或者，也可以註冊 `OAuth2Authenticator.Error` 事件的事件處理常式。

### <a name="presenting-the-sign-in-user-interface"></a>呈現登入使用者介面

登入使用者介面可以藉由使用 [Xamarin]，向使用者呈現，其必須在每個平臺專案中初始化。 下列程式碼範例示範如何初始化 iOS 專案中 `AppDelegate` 類別的登入者：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下列程式碼範例示範如何初始化 Android 專案中 `MainActivity` 類別的登入者：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

然後，.NET Standard 程式庫專案可以叫用登入者，如下所示：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

請注意，`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login` 方法的引數是 `OAuth2Authenticator` 實例。 叫用 `Login` 方法時，會在裝置的網頁瀏覽器中向使用者顯示登入使用者介面，如下列螢幕擷取畫面所示：

![](oauth-images/login.png "Google Sign-In")

### <a name="processing-the-redirect-url"></a>處理重新導向 URL

在使用者完成驗證程式之後，控制權會從 web 瀏覽器索引標籤返回應用程式。這是藉由註冊驗證程式所傳回之重新導向 URL 的自訂 URL 配置，然後在傳送後偵測並處理自訂 URL 來達成。

選擇要與應用程式建立關聯的自訂 URL 配置時，應用程式必須根據其控制項的名稱使用配置。 這可以藉由使用 iOS 上的配套識別碼名稱和 Android 上的套件名稱來達成，然後將它們反轉以進行 URL 配置。 不過，某些身分識別提供者（例如 Google）會根據功能變數名稱來指派用戶端識別碼，然後再反轉並使用這些名稱作為 URL 配置。 例如，如果 Google 建立 `902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`的用戶端識別碼，則會 `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`URL 配置。 請注意，只有單一 `/` 可以出現在配置元件之後。 因此，會 `com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`使用自訂 URL 配置的重新導向 URL 的完整範例。

當網頁瀏覽器收到來自包含自訂 URL 配置之識別提供者的回應時，它會嘗試載入 URL，這將會失敗。 相反地，自訂 URL 配置會藉由引發事件向作業系統回報。 作業系統接著會檢查是否有已註冊的配置，如果找到，作業系統將會啟動已註冊配置的應用程式，並將重新導向 URL 傳送給它。

向作業系統註冊自訂 URL 配置和處理配置的機制，是針對每個平臺所特有。

#### <a name="ios"></a>iOS

在 iOS 上，會在**plist**中註冊自訂 URL 配置，如下列螢幕擷取畫面所示：

![](oauth-images/info-plist.png "URL Scheme Registration")

[**識別碼**] 值可以是任何專案，而且 [**角色**] 值必須設定為 [**檢視器]** 。 從 `com.googleusercontent.apps`開始的**Url**配置值，可以從[Google API 主控台](https://console.developers.google.com)上專案的 iOS 用戶端識別碼取得。

當身分識別提供者完成授權要求時，它會重新導向至應用程式的重新導向 URL。 因為 URL 會使用自訂配置，所以會導致 iOS 啟動應用程式，並以啟動參數的形式傳入 URL，其中會由應用程式 `AppDelegate` 類別的 `OpenUrl` 覆寫來處理，如下列程式碼範例所示:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    // Convert NSUrl to Uri
    var uri = new Uri(url.AbsoluteString);

    // Load redirectUrl page
    AuthenticationState.Authenticator.OnPageLoading(uri);

    return true;
}
```

`OpenUrl` 方法會先將接收的 URL 從 `NSUrl` 轉換為 .NET `Uri`，然後再使用公用 `OAuth2Authenticator` 物件的 `OnPageLoading` 方法來處理重新導向 URL。 這會導致 Xamarin. Auth 關閉 web 瀏覽器索引標籤，並剖析收到的 OAuth 資料。

#### <a name="android"></a>Android

在 Android 上，自訂 URL 配置是藉由在將處理配置的 `Activity` 上指定[`IntentFilter`](xref:Android.App.IntentFilterAttribute)屬性來註冊。 當身分識別提供者完成授權要求時，它會重新導向至應用程式的重新導向 URL。 當 URL 使用自訂配置時，它會導致 Android 啟動應用程式，並以啟動參數的形式傳入 URL，其中由註冊來處理自訂 URL 配置的 `Activity` 的 `OnCreate` 方法來處理。 下列程式碼範例顯示的類別來自處理自訂 URL 配置的範例應用程式：

```csharp
[Activity(Label = "CustomUrlSchemeInterceptorActivity", NoHistory = true, LaunchMode = LaunchMode.SingleTop )]
[IntentFilter(
    new[] { Intent.ActionView },
    Categories = new [] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataSchemes = new [] { "<insert custom URL here>" },
    DataPath = "/oauth2redirect")]
public class CustomUrlSchemeInterceptorActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        // Convert Android.Net.Url to Uri
        var uri = new Uri(Intent.Data.ToString());

        // Load redirectUrl page
        AuthenticationState.Authenticator.OnPageLoading(uri);

        Finish();
    }
}
```

[`IntentFilter`](xref:Android.App.IntentFilterAttribute)的 `DataSchemes` 屬性必須設定為從[Google API 主控台](https://console.developers.google.com)上專案的 Android 用戶端識別碼取得的反向用戶端識別碼。

`OnCreate` 方法會先將接收的 URL 從 `Android.Net.Url` 轉換為 .NET `Uri`，然後再使用公用 `OAuth2Authenticator` 物件的 `OnPageLoading` 方法來處理重新導向 URL。 這會導致 Xamarin. Auth 關閉 web 瀏覽器索引標籤，並剖析收到的 OAuth 資料。

> [!IMPORTANT]
> 在 Android 上，Xamarin 會使用 `CustomTabs` API 來與網頁瀏覽器和作業系統通訊。 不過，不保證 `CustomTabs` 相容的瀏覽器將會安裝在使用者的裝置上。

### <a name="examining-the-oauth-response"></a>檢查 OAuth 回應

剖析收到的 OAuth 資料之後，Xamarin. Auth 會引發 `OAuth2Authenticator.Completed` 事件。 在此事件的事件處理常式中，可以使用 `AuthenticatorCompletedEventArgs.IsAuthenticated` 屬性來識別驗證是否成功，如下列程式碼範例所示：

```csharp
async void OnAuthCompleted(object sender, AuthenticatorCompletedEventArgs e)
{
  ...
  if (e.IsAuthenticated)
  {
    ...
  }
}
```

從成功的驗證收集的資料可在 `AuthenticatorCompletedEventArgs.Account` 屬性中取得。 這包括存取權杖，可用於將資料的要求籤署至識別提供者所提供的 API。

### <a name="making-requests-for-data"></a>提出資料要求

在應用程式取得存取權杖之後，它會用來向 `https://www.googleapis.com/oauth2/v2/userinfo` API 提出要求，以向識別提供者要求基本使用者資料。 這項要求是利用 Xamarin. Auth 的 `OAuth2Request` 類別來進行，這代表使用從 `OAuth2Authenticator` 實例取得的帳戶進行驗證的要求，如下列程式碼範例所示：

```csharp
// UserInfoUrl = https://www.googleapis.com/oauth2/v2/userinfo
var request = new OAuth2Request ("GET", new Uri (Constants.UserInfoUrl), null, e.Account);
var response = await request.GetResponseAsync ();
if (response != null)
{
  string userJson = response.GetResponseText ();
  var user = JsonConvert.DeserializeObject<User> (userJson);
}
```

除了 HTTP 方法和 API URL，`OAuth2Request` 實例也會指定包含存取權杖的 `Account` 實例，以將要求籤署至 `Constants.UserInfoUrl` 屬性所指定的 URL。 身分識別提供者接著會傳回基本的使用者資料做為 JSON 回應，包括使用者的名稱和電子郵件地址，前提是它會將存取權杖辨識為有效。 然後，JSON 回應會讀取並還原序列化為 `user` 變數。

如需詳細資訊，請參閱 Google 開發人員入口網站上的[呼叫 GOOGLE API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) 。

### <a name="storing-and-retrieving-account-information-on-devices"></a>儲存和抓取裝置上的帳戶資訊

Xamarin 會安全地將 `Account` 物件儲存在帳戶存放區中，讓應用程式不一定需要重新驗證使用者。 `AccountStore` 類別負責儲存帳戶資訊，並由 iOS 中的 Keychain 服務和 Android 中的 `KeyStore` 類別提供支援。

下列程式碼範例顯示如何安全地儲存 `Account` 物件：

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

已儲存的帳戶可使用由帳戶的 `Username` 屬性和服務識別碼所組成的金鑰來唯一識別，這是從帳戶存放區提取帳戶時所使用的字串。 如果先前已儲存 `Account`，再次呼叫 `Save` 方法將會覆寫它。

您可以藉由呼叫 `FindAccountsForService` 方法來抓取服務的 `Account` 物件，如下列程式碼範例所示：

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

`FindAccountsForService` 方法會傳回 `Account` 物件的 `IEnumerable` 集合，並將集合中的第一個專案設定為相符的帳戶。

## <a name="troubleshooting"></a>疑難排解

- 在 Android 上，如果您在驗證後關閉瀏覽器時收到快顯通知，而且想要停止快顯通知，請將下列程式碼新增至 Android 專案（在初始化 Xamarin 之後）。 Auth：

```csharp
Xamarin.Auth.CustomTabsConfiguration.CustomTabsClosingMessage = null;
```

- 在 Android 上，如果瀏覽器未自動關閉，暫時的解決方法是將 Xamarin. Auth 封裝降級為版本1.5.0.3。 然後，將 PCL 密碼編譯[v 2.0.147](https://www.nuget.org/packages/PCLCrypto/2.0.147)新增至 Android 專案。

## <a name="summary"></a>總結

本文說明如何使用 Xamarin. Auth 來管理 Xamarin. Forms 應用程式中的驗證進程。 [Xamarin] 提供 Xamarin 所使用的 `OAuth2Authenticator` 和 `OAuth2Request` 類別，以取用 Google、Microsoft、Facebook 和 Twitter 等身分識別提供者。

## <a name="related-links"></a>相關連結

- [OAuthNativeFlow （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-oauthnativeflow)
- [適用于原生應用程式的 OAuth 2。0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth 2.0 存取 Google Api](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin. Auth （NuGet）](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin. Auth （GitHub）](https://github.com/xamarin/Xamarin.Auth)
