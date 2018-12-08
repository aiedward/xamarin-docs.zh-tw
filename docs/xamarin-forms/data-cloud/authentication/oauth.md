---
title: 使用身分識別提供者驗證使用者
description: 這篇文章說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: D44745D5-77BB-4596-9B8C-EC75C259157C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2017
ms.openlocfilehash: 786f1503fcc0cc07f76a7cdc55731d341607429f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053192"
---
# <a name="authenticating-users-with-an-identity-provider"></a>使用身分識別提供者驗證使用者

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)

_Xamarin.Auth 是跨平台 SDK，以驗證使用者，並儲存他們的帳戶。它包含可支援使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter OAuth 驗證器。這篇文章說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。_

OAuth 是一套開放標準進行驗證，並可讓資源擁有者，通知資源提供者應該在第三方存取他們的資訊，而不會共用資源擁有者身分識別授與權限。 這個範例會讓使用者通知 （例如 Google、 Microsoft、 Facebook 或 Twitter） 的身分識別提供者應該在應用程式來存取其資料，而不會共用使用者的身分識別授與權限。 它常用的方法，讓使用者登入網站和應用程式使用的身分識別提供者，但是不會公開其密碼，才能在網站或應用程式。

使用 OAuth 身分識別提供者時的驗證流程的概觀，如下所示：

1. 應用程式中，瀏覽至身分識別提供者 URL 的瀏覽器。
1. 身分識別提供者會處理使用者驗證和授權碼傳回到應用程式。
1. 應用程式會交換授權碼，以便從身分識別提供者的存取權杖。
1. 應用程式會使用存取權杖來存取 Api 上的身分識別提供者，例如要求基本使用者資料的 API。

範例應用程式會示範如何使用 Xamarin.Auth 實作針對 Google 的原生驗證流程。 雖然 Google 作為身分識別提供者，本主題中，方法是同樣適用於其他識別提供者。 如需有關使用 Google OAuth 2.0 端點驗證的詳細資訊，請參閱[存取 Google api 的使用 OAuth2.0](https://developers.google.com/identity/protocols/OAuth2) Google 網站上。

> [!NOTE]
> 在 iOS 9 和更新版本中，App Transport Security (ATS) 會強制執行安全的連線 （例如應用程式的後端伺服器） 的網際網路資源與應用程式，藉此防止意外洩漏機密資訊。 針對 iOS 9 所建置的應用程式中的預設會啟用 ATS，因為所有連線將會都受限於 ATS 安全性需求。 如果連線不符合這些需求，它們將會失敗並發生例外狀況。
> 如果無法使用，可以共選擇 ATS`HTTPS`通訊協定和安全的網際網路資源的通訊。 這可以藉由更新應用程式的達成**Info.plist**檔案。 如需詳細資訊，請參閱[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)。

## <a name="using-xamarinauth-to-authenticate-users"></a>使用 Xamarin.Auth 來驗證使用者

Xamarin.Auth 支援身分識別提供者的授權端點進行互動的應用程式的兩種方法：

1. 使用內嵌的 web 檢視。 雖然這向來是常見的作法，但不會再建議，原因如下：

    - 裝載 web 檢視的應用程式可以存取使用者的完整驗證認證，不只是 OAuth 授權授與應用程式之時。 應用程式在更強大的認證比所需，可能會增加受攻擊面，應用程式的存取權，這樣會違反最低權限的原則。
    - 主應用程式無法擷取使用者名稱和密碼、 自動送出表單和略過使用者同意，並複製工作階段 cookie，並用來以使用者身分執行已驗證的動作。
    - 內嵌的 web 檢視不分享其他應用程式或裝置的網頁瀏覽器，要求會被視為較差的使用者體驗的每個授權要求的登入使用者的驗證狀態。
    - 某些授權端點會採取步驟來偵測並封鎖來自 web 檢視的授權要求。

1. 使用裝置的網頁瀏覽器，這是建議的方法。 使用 OAuth 要求的裝置瀏覽器可改善應用程式的可用性，因為使用者只需要登入身分識別提供者一次每個裝置，改善應用程式中的登入和授權流程的轉換率。 裝置的瀏覽器也提供改良的安全性，因為應用程式都能檢查及修改內容，在 web 檢視中，但不是顯示在瀏覽器的內容。 這是此文件及範例應用程式中採用的方法。

下圖顯示範例應用程式如何使用 Xamarin.Auth 來驗證使用者，並擷取其基本資料的高階概觀：

![](oauth-images/google-auth.png "使用 Xamarin.Auth 使用 Google 進行驗證")

應用程式提出驗證要求以使用 Google`OAuth2Authenticator`類別。 驗證回應會傳回之後使用者已成功使用 Google 驗證透過其登入網頁，當中包含存取權杖。 接著，應用程式對 Google 提出要求，對基本的使用者資料，使用`OAuth2Request`類別，且要求中所包含的存取權杖。

### <a name="setup"></a>安裝程式

Xamarin.Forms 應用程式與整合 Google 登入，必須建立 Google API 主控台專案。 執行下列工作即可達成這點：

1. 移至[Google API 主控台](http://console.developers.google.com)網站，並使用 Google 帳戶認證登入。
1. 從專案下拉式清單中，選取現有的專案，或建立新的帳戶。
1. 在 API 管理員 下 資訊看板，選取**認證**，然後選取**OAuth 同意畫面 索引標籤**。選擇**電子郵件地址**，指定**向使用者顯示的產品名稱**，然後按**儲存**。
1. 在 **認證**索引標籤上，選取**建立認證**下拉式清單，然後選擇**OAuth 用戶端識別碼**。
1. 底下**應用程式類型**，選取行動應用程式將會在執行的平台 (**iOS**或是**Android**)。
1. 填寫必要的詳細資料，然後選取**建立** 按鈕。

> [!NOTE]
> 用戶端識別碼可讓應用程式存取已啟用的 Google Api 和行動裝置應用程式是唯一的單一平台。 因此， **OAuth 用戶端識別碼**應該建立每個平台會使用 Google 登入。

執行這些步驟之後，Xamarin.Auth 可用來起始與 Google OAuth2 驗證流程。

### <a name="creating-and-configuring-an-authenticator"></a>建立及設定驗證器

Xamarin.Auth 的`OAuth2Authenticator`類別會負責處理 OAuth 驗證流程。 下列程式碼範例顯示的具現化`OAuth2Authenticator`類別時使用裝置的網頁瀏覽器執行驗證：

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

`OAuth2Authenticator`類別需要幾個參數，如下所示：

- **用戶端識別碼**– 這會識別提出要求，並可以擷取從專案中的用戶端[Google API 主控台](http://console.developers.google.com)。
- **用戶端祕密**-這應該`null`或`string.Empty`。
- **範圍**– 這會識別應用程式時，所要求的 API 存取權和值會通知顯示給使用者的同意畫面。 如需有關範圍的詳細資訊，請參閱 <<c0> [ 授權的 API 要求](https://developers.google.com/+/web/api/rest/oauth)Google 網站上。
- **授權 URL** – 這會識別其中將從取得授權碼的 URL。
- **重新導向 URL** – 這會識別將會傳送回應的 URL。 此參數的值必須符合其中一個值，會出現在**認證** 索引標籤中的專案[Google 開發人員主控台](https://console.developers.google.com/)。
- **AccessToken Url** – 這會識別用來取得授權碼之後，要求存取權杖的 URL。
- **GetUserNameAsync Func** – 這是選擇性`Func`，將會用來以非同步方式擷取帳戶的使用者名稱，成功驗證之後。
- **使用原生 UI** –`boolean`值，指出是否要使用裝置的網頁瀏覽器執行的驗證要求。

### <a name="setup-authentication-event-handlers"></a>設定驗證事件處理常式

開始進行使用者介面的事件處理常式之前`OAuth2Authenticator.Completed`，也必須先註冊事件，如下列程式碼範例所示：

```csharp
authenticator.Completed += OnAuthCompleted;
```

當使用者成功驗證或取消在登入時，會引發此事件。

（選擇性） 的事件處理常式`OAuth2Authenticator.Error`也可以註冊事件。

### <a name="presenting-the-sign-in-user-interface"></a>呈現登入的使用者介面

可以使用 Xamarin.Auth 登入主講人，必須先初始化每個平台專案中，將登入的使用者介面呈現給使用者。 下列程式碼範例示範如何初始化在登入主持人`AppDelegate`iOS 專案中的類別：

```csharp
global::Xamarin.Auth.Presenters.XamarinIOS.AuthenticationConfiguration.Init();
```

下列程式碼範例示範如何初始化在登入主持人`MainActivity`Android 專案中的類別：

```csharp
global::Xamarin.Auth.Presenters.XamarinAndroid.AuthenticationConfiguration.Init(this, bundle);
```

.NET Standard 程式庫專案便可以叫用登入展示者，如下所示：

```csharp
var presenter = new Xamarin.Auth.Presenters.OAuthLoginPresenter();
presenter.Login(authenticator);
```

請注意，引數`Xamarin.Auth.Presenters.OAuthLoginPresenter.Login`方法是`OAuth2Authenticator`執行個體。 當`Login`叫用方法時，登入的使用者介面時，會顯示在索引標籤中的使用者上，從裝置的網頁瀏覽器中的下列螢幕擷取畫面所示：

![](oauth-images/login.png "Google 登入")

### <a name="processing-the-redirect-url"></a>處理重新導向 URL

使用者完成驗證程序之後，控制項將會傳回給應用程式從 web 瀏覽器索引標籤。這之後，即可註冊重新導向 URL 時所傳回的驗證程序，然後偵測並處理自訂的 URL 之後它會傳送, 自訂的 URL 配置。

在選擇自訂 URL 配置應用程式相關聯時，應用程式必須使用屬於他們控制的名稱為基礎的配置。 這可藉由使用 iOS 和 Android 上的封裝名稱的套件組合識別碼名稱，並讓 URL 配置反轉。 不過，某些身分識別提供者，例如 Google、 指派網域名稱，然後反轉並做為 URL 配置為基礎的用戶端識別元。 例如，如果 Google 建立的用戶端識別碼`902730282010-ks3kd03ksoasioda93jldas93jjj22kr.apps.googleusercontent.com`，會使用 URL 配置`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr`。 請注意，只有單一`/`可以 vyskytovat po 結構描述元件。 因此，利用自訂的 URL 配置的重新導向 URL 的完整範例是`com.googleusercontent.apps.902730282010-ks3kd03ksoasioda93jldas93jjj22kr:/oauth2redirect`。

當網頁瀏覽器收到回應時從身分識別提供者，其中包含自訂的 URL 配置時，它會嘗試載入的 URL，將會失敗。 相反地，自訂的 URL 配置會藉由引發事件回報給作業系統。 對於已註冊的結構描述，然後檢查作業系統及作業系統如果有找到，會啟動應用程式註冊配置，並將它傳送重新導向 URL。

向作業系統註冊自訂的 URL 配置，以及處理結構描述的機制是每個平台專用的。

#### <a name="ios"></a>iOS

在 iOS 上，在中註冊自訂的 URL 配置**Info.plist**，如下列螢幕擷取畫面所示：

![](oauth-images/info-plist.png "註冊 URL 配置")

**識別碼**值可以是任何項目，而**角色**值必須設定為**檢視器**。 **Url 配置**開頭的值`com.googleusercontent.apps`，您可以從專案的 iOS 用戶端識別碼取得上[Google API 主控台](http://console.developers.google.com)。

授權要求的身分識別提供者完成，它會重新導向至應用程式的重新導向 URL。 因為 URL 會使用自訂配置，這樣可以在 iOS 中啟動應用程式，在 URL 中傳遞做為啟動參數，其中由處理`OpenUrl`應用程式的覆寫`AppDelegate`類別，下列程式碼範例所示：

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

`OpenUrl`方法會將轉換的已接收的 URL，從`NSUrl`.net `Uri`，再處理將重新導向 URL`OnPageLoading`方法的公用`OAuth2Authenticator`物件。 這會導致 Xamarin.Auth 關閉 web 瀏覽器索引標籤上，，和剖析已接收的 OAuth 的資料。

#### <a name="android"></a>Android

在 Android 上，藉由指定註冊自訂的 URL 配置[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)屬性`Activity`，以便處理結構描述。 授權要求的身分識別提供者完成，它會重新導向至應用程式的重新導向 URL。 因為 URL 會使用自訂配置，這樣可以在 Android 中啟動應用程式，在 URL 中傳遞做為啟動參數，其中由處理`OnCreate`方法的`Activity`已註冊要處理自訂的 URL 配置。 下列程式碼範例顯示來自範例應用程式處理自訂的 URL 配置的類別：

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

`DataSchemes`的屬性[ `IntentFilter` ](https://developer.xamarin.com/api/type/Android.App.IntentFilterAttribute/)必須設為反轉的用戶端識別碼是從專案的 Android 用戶端識別碼取得上[Google API 主控台](http://console.developers.google.com)。

`OnCreate`方法會將轉換的已接收的 URL，從`Android.Net.Url`.net `Uri`，再處理將重新導向 URL`OnPageLoading`方法的公用`OAuth2Authenticator`物件。 這會導致 Xamarin.Auth 關閉 web 瀏覽器索引標籤，並剖析接收的 OAuth 的資料。

> [!IMPORTANT]
> 在 Android 上，使用 Xamarin.Auth `CustomTabs` API 與 web 瀏覽器和作業系統進行通訊。 不過，它具有不保證，`CustomTabs`相容的瀏覽器將會安裝在使用者的裝置。

### <a name="examining-the-oauth-response"></a>檢查 OAuth 回應

在剖析已接收的 OAuth 的資料之後, 將會引發 Xamarin.Auth`OAuth2Authenticator.Completed`事件。 在此情況下，事件處理常式`AuthenticatorCompletedEventArgs.IsAuthenticated`屬性可用來識別是否驗證成功，如下列程式碼範例所示：

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

在成功驗證從蒐集的資料位於`AuthenticatorCompletedEventArgs.Account`屬性。 這包括存取權杖，可用來簽署身分識別提供者所提供的 API 資料要求。

### <a name="making-requests-for-data"></a>提出要求的資料

應用程式取得存取權杖之後，它用來提出的要求`https://www.googleapis.com/oauth2/v2/userinfo`API，要求來自身分識別提供者的基本使用者資料。 此要求對具有 Xamarin.Auth 的`OAuth2Request`類別，其代表會使用從擷取的帳戶進行驗證的要求`OAuth2Authenticator`執行個體，如下列程式碼範例所示：

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

HTTP 方法和 API 的 URL，以及`OAuth2Request`執行個體也會指定`Account`執行個體，包含存取權杖登入要求所指定的 URL 以`Constants.UserInfoUrl`屬性。 身分識別提供者則會傳回為 JSON 回應，包括使用者名稱和電子郵件地址，前提它辨識為有效的存取權杖的基本使用者資料。 然後閱讀並還原序列化為 JSON 回應`user`變數。

如需詳細資訊，請參閱 <<c0> [ 呼叫 Google API](https://developers.google.com/identity/protocols/OAuth2InstalledApp#callinganapi) Google 開發人員入口網站上。

### <a name="storing-and-retrieving-account-information-on-devices"></a>儲存和擷取在裝置上的帳戶資訊

安全地儲存 Xamarin.Auth`Account`帳戶中的物件儲存，以便應用程式永遠不需要重新驗證使用者。 `AccountStore`類別會負責儲存帳戶資訊，而 Keychain 服務在 iOS 中，做為後盾，`KeyStore`在 Android 中的類別。

下列程式碼範例示範如何`Account`安全地儲存物件：

```csharp
AccountStore.Create ().Save (e.Account, Constants.AppName);
```

儲存的帳戶中可唯一識別使用的帳戶所組成的索引鍵`Username`屬性和服務識別碼，也就是從帳戶存放區使用時擷取帳戶的字串。 如果`Account`先前儲存，呼叫`Save`方法一次將會覆寫它。

`Account` 物件服務可以藉由呼叫擷取`FindAccountsForService`方法，如下列程式碼範例所示：

```csharp
var account = AccountStore.Create ().FindAccountsForService (Constants.AppName).FirstOrDefault();
```

`FindAccountsForService`方法會傳回`IEnumerable`的集合`Account`物件，包含在集合設定為 比對的帳戶中的第一個項目。

## <a name="summary"></a>總結

這篇文章說明如何使用 Xamarin.Auth 管理驗證程序，在 Xamarin.Forms 應用程式。 提供 Xamarin.Auth`OAuth2Authenticator`和`OAuth2Request`使用身分識別提供者，例如 Google、 Microsoft、 Facebook 和 Twitter Xamarin.Forms 應用程式所使用的類別。


## <a name="related-links"></a>相關連結

- [OAuthNativeFlow （範例）](https://developer.xamarin.com/samples/xamarin-forms/WebServices/OAuthNativeFlow/)
- [原生應用程式的 OAuth 2.0](https://tools.ietf.org/html/draft-ietf-oauth-native-apps-12)
- [使用 OAuth2.0 存取 Google Api](https://developers.google.com/identity/protocols/OAuth2)
- [Xamarin.Auth (NuGet)](https://www.nuget.org/packages/xamarin.auth/)
- [Xamarin.Auth (GitHub)](https://github.com/xamarin/Xamarin.Auth)
