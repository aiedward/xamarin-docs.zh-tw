---
title: 驗證使用者身分識別提供者
description: Xamarin.Auth 是跨平台 SDK，以驗證使用者，並儲存他們的帳戶。 它包含可支援使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter OAuth 驗證器。 本文說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 26e85a37cfd36b5d4f045273548efafccca79e1a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="authenticating-users-with-an-identity-provider"></a>驗證使用者身分識別提供者

_Xamarin.Auth 是跨平台 SDK，以驗證使用者，並儲存他們的帳戶。它包含可支援使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter OAuth 驗證器。本文說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。_

OAuth 進行驗證，一項開放標準，並提供通知資源提供者應該在與第三方存取他們的資訊，而不需要共用資源的擁有者身分識別來授與權限的資源擁有者。 這個範例會啟用通知 （例如 Google、 Microsoft、 Facebook 或 Twitter） 的身分識別提供者應該在應用程式存取其資料，而不需要共用的使用者身分識別來授與權限的使用者。 它通常用於使用者的方法登入網站和應用程式使用身分識別提供者，但是不會公開其密碼，才能在網站或應用程式。

使用的 OAuth 身分識別提供者時的驗證流程的高階概觀如下所示：

1. 應用程式瀏覽至身分識別提供者 URL 的瀏覽器。
1. 身分識別提供者會處理使用者驗證和授權碼傳回至應用程式。
1. 應用程式交換授權碼，以便從身分識別提供者的存取權杖。
1. 應用程式使用上的身分識別提供者，例如 API 針對要求基本使用者資料存取 Api 的存取權杖。

範例應用程式示範如何使用 Xamarin.Auth 實作 Google 針對原生的驗證流程。 Google 身分識別提供者，本主題中使用，而方法也同樣適用於其他身分識別提供者。 如需使用 Google OAuth 2.0 端點驗證的詳細資訊，請參閱[使用 OAuth2.0 存取 Google api](https://developers.google.com/identity/protocols/OAuth2) Google 網站上。

> [!NOTE]
> 在 iOS 9 和更新版本中，應用程式傳輸安全性 (ATS) 會強制執行安全連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，因此可以防止意外洩漏機密資訊。 由於 AT 預設會在 ios 9 建置的應用程式啟用時，所有連線將都會受限於 AT 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以超出選擇 AT`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin.Auth 來驗證使用者

Xamarin.Auth 支援兩種方法與身分識別提供者的授權端點互動的應用程式：

1. 使用內嵌的網頁檢視。 雖然這只是常見的作法，但不會再建議，原因如下：

    - 裝載 web 檢視的應用程式可以存取使用者的完整驗證認證，不只是 OAuth 授權授與應用程式之時。 這樣會違反原則的最小權限，因為應用程式具有存取比所需，可能會增加受攻擊面，應用程式的功能更強大的認證。
    - 主應用程式無法擷取使用者名稱和密碼、 自動送出表單和略過使用者同意，以及複製工作階段 cookie，並用來以使用者身分執行已驗證的動作。
    - 內嵌的網頁檢視不與其他應用程式或裝置的網頁瀏覽器，要求登入為每個授權要求會被視為較差的使用者經驗的使用者共用的驗證狀態。
    - 有些授權端點採取步驟來偵測及阻擋來自 web 檢視的授權要求。

1. 使用裝置的網頁瀏覽器，這是建議的方法。 裝置瀏覽器使用的 OAuth 要求改善應用程式的可用性，因為使用者只需要登入身分識別提供者一次每個裝置，改善應用程式中的登入和授權流程轉換率。 裝置瀏覽器也提供改善的安全性，因為應用程式都可以檢查和修改內容，在 web 檢視中，但不是在瀏覽器中顯示的內容。 這是此文件及範例應用程式中採用的方法。

下圖中顯示的範例應用程式如何使用 Xamarin.Auth 來驗證使用者，並擷取其基本資料的高階概觀：

![](oauth-images/google-auth.png "使用來向 Google Xamarin.Auth")

應用程式發出驗證要求使用 Google`OAuth2Authenticator`類別。 驗證回應會傳回，一旦使用者與 Google 成功驗證透過其登入頁面，其中包含存取權杖。 接著，應用程式提出要求 google 基本使用者資料，使用`OAuth2Request`類別，以在要求中包含存取權杖。

### <a name="setup"></a>安裝程式

Google API 主控台專案必須建立與 Xamarin.Forms 應用程式整合 Google 登入。 執行下列工作即可達成這點：

1. 移至[Google API 主控台](http://console.developers.google.com)網站，然後使用 Google 帳戶認證登入。
1. 從專案下拉式清單中，選取現有的專案，或另外新建一個。
1. 在 資訊看板 API Manager 底下，選取**認證**，然後選取**OAuth 同意畫面 索引標籤**。選擇**電子郵件地址**，指定**向使用者顯示的產品名稱**，然後按**儲存**。
1. 在**認證**索引標籤上，選取**建立認證**下拉式清單，並選擇**OAuth 用戶端識別碼**。
1. 在下**應用程式類型**，選取行動應用程式將在執行的平台 (**iOS**或**Android**)。
1. 填寫必要的詳細資料，並選取**建立** 按鈕。

> [!NOTE]
> 用戶端識別碼可讓應用程式存取啟用的 Google Api，，和行動應用程式是唯一的單一平台。 因此， **OAuth 用戶端識別碼**應建立每個平台會使用 Google 登入。

執行這些步驟之後，Xamarin.Auth，可以用來起始與 Google OAuth2 驗證流程中。

### <a name="creating-and-configuring-an-authenticator"></a>建立及設定驗證器

Xamarin.Auth 的`OAuth2Authenticator`類別會負責處理 OAuth 驗證流程。 下列程式碼範例示範的具現化`OAuth2Authenticator`類別時使用裝置的網頁瀏覽器執行驗證：

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

`OAuth2Authenticator`類別會要求數目的參數，也就是，如下所示：

- **用戶端識別碼**– 這會識別提出要求，而且可以擷取從專案中的用戶端[Google API 主控台](http://console.developers.google.com)。
- **用戶端密碼**-這應該是`null`或`string.Empty`。
- **範圍**– 這會識別應用程式所要求的應用程式開發介面存取和值會通知使用者顯示同意畫面。 如需範圍的詳細資訊，請參閱[授權 API 要求](https://developers.google.com/+/web/api/rest/oauth)Google 網站上。
- **授權 URL** – 這會識別其中將從取得授權碼的 URL。
- **重新導向 URL** – 這會識別其中將傳送回應的 URL。 此參數的值必須符合其中一個值，會出現在**認證** 索引標籤中的專案[Google 開發人員主控台](https://console.developers.google.com/)。
- **AccessToken Url** – 這會識別用來取得授權碼之後，要求存取權杖的 URL。
- **GetUserNameAsync Func** -的選擇性`Func`，將會用來為已成功驗證之後，以非同步方式擷取帳戶的使用者名稱。
- **使用原生 UI** –`boolean`值，指出是否要執行的驗證要求使用裝置的網頁瀏覽器。

### <a name="setup-authentication-event-handlers"></a>安裝程式驗證事件處理常式

再呈現使用者介面的事件處理常式`OAuth2Authenticator.Completed`事件必須註冊，如下列程式碼範例所示：

```csharp
authenticator.Completed += OnAuthCompleted;
```

當使用者成功驗證或取消的登入，就會引發這個事件。

（選擇性） 的事件處理常式`OAuth2Authenticator.Error`事件也可以註冊。

### <a name="presenting-the-sign-in-user-interface"></a>呈現登入的使用者介面

登入的使用者介面可以呈現給使用者，使用 Xamarin.Auth login 演講者，必須在每個平台專案中初始化。 下列程式碼範例示範如何初始化在登入主持人`AppDelegate`iOS 專案中的類別：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下列程式碼範例示範如何初始化在登入主持人`MainActivity`Android 專案中的類別：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

可攜式類別程式庫 (PCL) 專案可以再叫用 login 演講者，如下所示：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

請注意，引數`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login`方法`OAuth2Authenticator`執行個體。 當`Login`叫用方法時，登入的使用者介面會向使用者顯示的索引標籤中從裝置的網頁瀏覽器，其會顯示在下列螢幕擷取畫面：

![](oauth-images/login.png "Google 登入")

### <a name="processing-the-redirect-url"></a>處理重新導向 URL

使用者完成驗證程序之後，控制會傳回至應用程式，從 web 瀏覽器 索引標籤。達到此目的，請註冊自訂的 URL 配置所傳回的驗證程序，然後偵測並一旦傳送時，處理自訂的 URL 重新導向 URL。

在選擇自訂 URL 配置與應用程式時，應用程式必須使用其控制下的名稱為基礎的配置。 這可藉由使用 iOS 和 Android 上的封裝名稱上的套件組合識別碼名稱，並以 URL 配置反轉。 不過，某些身分識別提供者，例如 Google、 指派網域名稱，然後再反轉並且做為 URL 配置為基礎的用戶端識別項。 例如，如果 Google 建立的用戶端識別碼`902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`，將會是 URL 配置`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`。 請注意，只有單一`/`可以出現在結構描述元件之後。 因此，是利用自訂的 URL 配置到重新導向 URL 的完整範例`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`。

當網頁瀏覽器從身分識別提供者，其中包含自訂的 URL 配置收到回應時，它會嘗試載入的 URL，將會失敗。 相反地，自訂的 URL 配置會報告作業系統引發事件。 對於已註冊的結構描述，然後檢查作業系統和作業系統如果找到，會啟動應用程式註冊配置，來將它重新導向 URL。

向作業系統註冊自訂的 URL 配置，以及處理結構描述的機制是每個平台專屬的。

#### <a name="ios"></a>iOS

在 iOS 上，在中註冊自訂的 URL 配置**Info.plist**，如下列螢幕擷取畫面所示：

![](oauth-images/info-plist.png "URL 配置註冊")

**識別碼**值可以是任何項目，而**角色**值必須設定為**檢視器**。 **Url 配置**值開頭為`com.googleusercontent.apps`，您可以從專案的 iOS 用戶端識別碼取得上[Google API 主控台](http://console.developers.google.com)。

當身分識別提供者完成授權要求時，它會重新導向至應用程式的重新導向 URL。 因為 URL 中使用自訂配置，則會導致啟動應用程式的 iOS，在 URL 中傳遞做為啟動參數，就會處理由`OpenUrl`的應用程式的覆寫`AppDelegate`類別，下列程式碼範例所示：

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

`OpenUrl`方法會將轉換從收到的 URL `NSUrl` .net `Uri`，處理與重新導向 URL 之前`OnPageLoading`的公用方法`OAuth2Authenticator`物件。 這會導致 Xamarin.Auth 關閉 web 瀏覽器索引標籤上，，和剖析已接收的 OAuth 資料。

#### <a name="android"></a>Android

在 Android 上，藉由指定註冊自訂的 URL 配置[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)屬性`Activity`處理的配置。 當身分識別提供者完成授權要求時，它會重新導向至應用程式的重新導向 URL。 因為 URL 會使用自訂配置，則會導致啟動應用程式的 Android，在 URL 中傳遞做為啟動參數，就會處理由`OnCreate`方法`Activity`登錄來處理自訂的 URL 配置。 下列程式碼範例會顯示來自範例應用程式處理的自訂 URL 配置的類別：

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

`DataSchemes`屬性[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)必須設為反轉的用戶端識別碼是從專案的 Android 用戶端識別碼取得上[Google API 主控台](http://console.developers.google.com)。

`OnCreate`方法會將轉換從收到的 URL `Android.Net.Url` .net `Uri`，處理與重新導向 URL 之前`OnPageLoading`的公用方法`OAuth2Authenticator`物件。 這會導致 Xamarin.Auth 關閉 web 瀏覽器索引標籤，並剖析已接收的 OAuth 資料。

> [!IMPORTANT]
> 在 Android 上，使用 Xamarin.Auth`CustomTabs`彼此通訊的網頁瀏覽器和作業系統的 API。 不過，它具有不保證，`CustomTabs`相容的瀏覽器將會安裝在使用者裝置上。

### <a name="examining-the-oauth-response"></a>檢查 OAuth 回應

剖析已接收的 OAuth 資料之後, 將會引發 Xamarin.Auth`OAuth2Authenticator.Completed`事件。 此事件，事件處理常式中`AuthenticatorCompletedEventArgs.IsAuthenticated`屬性可用來識別是否驗證成功，如下列程式碼範例所示：

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

成功驗證從收集的資料位於`AuthenticatorCompletedEventArgs.Account`屬性。 這包含存取權杖，可以用來簽署身分識別提供者所提供的 api 資料要求。

### <a name="making-requests-for-data"></a>提出要求的資料

應用程式取得存取權杖之後，它用來提出要求來`https://www.googleapis.com/oauth2/v2/userinfo`API，來要求來自身分識別提供者的基本使用者資料。 Xamarin.Auth 的隨即提出此要求`OAuth2Request`類別，表示會使用從擷取的帳戶進行驗證的要求`OAuth2Authenticator`執行個體，如下列程式碼範例所示：

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

HTTP 方法和 API URL，以及`OAuth2Request`執行個體也會指定`Account`包含簽署所指定的 URL 的要求存取權杖執行個體`Constants.UserInfoUrl`屬性。 然後，身分識別提供者會傳回基本使用者資料當做 JSON 回應，前提其辨識為有效的存取權杖包含使用者名稱和電子郵件地址。 然後閱讀並還原序列化為 JSON 回應`user`變數。

如需詳細資訊，請參閱[呼叫 Google API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) Google 開發人員入口網站上。

### <a name="storing-and-retrieving-account-information-on-devices"></a>儲存和擷取裝置上的帳戶資訊

安全地儲存 Xamarin.Auth`Account`帳戶中的物件儲存，以便應用程式永遠不需要重新驗證使用者。 `AccountStore`類別會負責儲存帳戶資訊，並受到 Keychain 服務在 iOS 中，而`KeyStore`Android 中的類別。

下列程式碼範例示範如何`Account`安全地儲存物件：

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

儲存的帳戶中可唯一識別使用的帳戶所組成的索引鍵`Username`屬性和服務識別碼，也就是從帳戶存放區使用時擷取帳戶的字串。 如果`Account`先前儲存，呼叫`Save`方法一次將會覆寫它。

`Account` 物件的服務可以藉由呼叫擷取`FindAccountsForService`方法，如下列程式碼範例所示：

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

`FindAccountsForService`方法會傳回`IEnumerable`集合`Account`物件，且設定為相符的帳戶在集合中第一個項目。

## <a name="summary"></a>總結

本文說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。 提供 Xamarin.Auth`OAuth2Authenticator`和`OAuth2Request`來使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter Xamarin.Forms 應用程式所使用的類別。


## <a name="related-links"></a>相關連結

- [OAuthNativeFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [原生應用程式的 OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth2.0 存取 Google 應用程式開發介面](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
