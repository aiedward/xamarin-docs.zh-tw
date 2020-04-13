---
title: Xamarin.要點:網路認證器
description: 本文件介紹 Xamarin.Essentials 中的 Web 身份驗證器類,它允許您啟動基於瀏覽器的身份驗證流,這些身份驗證流偵聽對應用的回調。
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
ms.openlocfilehash: 82c136e1de7d2aa7f2d7f132b8ee1639ee44aba4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "80638161"
---
# <a name="xamarinessentials-web-authenticator"></a>Xamarin.要點:網路認證器

**Web 身份驗證器**類允許您啟動基於瀏覽器的流,這些流偵聽對註冊到應用的特定 URL 的回調。

## <a name="overview"></a>概觀

許多應用需要添加使用者身份驗證,這通常意味著允許使用者登錄其現有的 Microsoft、Facebook、Google 和現在的 Apple 登錄帳戶。

[Microsoft 身份驗證庫 (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)為向應用添加身份驗證提供了出色的交鑰匙解決方案。 甚至支援 Xamarin 應用程式在其用戶端 NuGet 包中。

如果您有興趣使用自己的 Web 服務進行身份驗證,則可以使用**Web 身份驗證器**來實現用戶端功能。

## <a name="why-use-a-server-back-end"></a>為什麼要使用伺服器後端?

許多身份驗證提供者已轉向僅提供顯式或雙腿身份驗證流,以確保更好的安全性。 這意味著您需要來自提供程式的 _「用戶端機密」_ 來完成身份驗證流。 遺憾的是,移動應用不是儲存機密的好地方,存儲在移動應用代碼、二進位檔或其他內容中的任何內容通常被認為是不安全的。

此處的最佳做法是使用 Web 後端作為行動應用程式和身份驗證供應商之間的中間層。

> [!IMPORTANT]
> 我們強烈建議不要使用較舊的僅移動身份驗證庫和模式,這些庫和模式由於存儲用戶端機密固有的安全性而在身份驗證流中不使用 Web 後端介面。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

要造**訪 Web 身份驗證器**功能,需要以下特定於平台的設置。

# <a name="android"></a>[Android](#tab/android)

Android 需要意向篩選器設置來處理回調 URI。 這可以通過對類`WebAuthenticatorCallbackActivity`進行 子類化來輕鬆完成:

> [!NOTE]
> 應考慮實現[Android 應用連結](https://developer.android.com/training/app-links/)來處理回調 URI,並確保您的應用程式是唯一可以註冊以處理回調 URI 的應用程式。

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Intent.ActionView },
    Categories = new[] { Intent.CategoryDefault, Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```

您還需要從`OnResume`中的重寫呼叫到基本要素`MainActivity`:

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume();
}
```

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 上,您需要將應用的回調 URI 模式添加到 Info.plist 中。

> [!NOTE]
> 應考慮使用[通用應用連結](https://developer.apple.com/documentation/uikit/inter-process_communication/allowing_apps_and_websites_to_link_to_your_content)註冊應用的回調 URI,作為最佳做法。

您還需要重寫您的`AppDelegate``OpenUrl`方法才能呼叫基本要素:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

對於 UWP,您需要在檔案`Package.appxmanifest`中 宣告回檔 URI:

```xml
    <Extensions>
        <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
        </uap:Extension>
    </Extensions>
```

-----

## <a name="using-webauthenticator"></a>使用 Web 身份驗證器

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

API 主要由一`AuthenticateAsync`個 方法組成,該方法採用兩個參數:應用於啟動 Web 瀏覽器流的 URL,以及您希望該流最終調用的 Uri 以及註冊到哪個應用能夠處理的 Uri。

結果是`WebAuthenticatorResult`包含從回調 URI 分析的任何查詢參數:

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

`WebAuthenticator` API 負責在瀏覽器中啟動網址 並等待收到回調:

![典型的 Web 身份驗證串流](images/web-authenticator.png)

如果使用者在任何時候取消流,則傳回`null`結果 。

## <a name="platform-differences"></a>平台差異

# <a name="android"></a>[Android](#tab/android)

自定義選項卡在可用時使用,否則將啟動 URL 的意圖。

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS`ASWebAuthenticationSession`12 或更高版本上,使用。  在 iOS`SFAuthenticationSession`11 上,使用。  在較舊的 iOS`SFSafariViewController`版本上,如果可用,則使用 Safari,否則將使用 Safari。

# <a name="uwp"></a>[UWP](#tab/uwp)

在 UWP`WebAuthenticationBroker`上,如果支援,則使用 。

-----

## <a name="apple-sign-in"></a>蘋果登錄

根據[蘋果的審查準則](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple),如果你的應用程式使用任何社交登錄服務進行身份驗證,它還必須提供蘋果登錄作為一個選項。

要將 Apple 登入到應用,首先您需要[將應用設定為使用 Apple 登入](https://docs.microsoft.com/xamarin/ios/platform/ios13/sign-in)。

對於 iOS 13 及更高版本,您`AppleSignInAuthenticator.AuthenticateAsync()`需要調用 該方法。 這將使用原生 Apple Sign 在 API 的引擎蓋下,以便您的使用者在這些設備上獲得最佳體驗。 您可以編寫共享代碼,以便在執行時使用正確的 API,如下所示:

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var accessToken = r?.AccessToken;
```

> [!TIP]
> 對於非 iOS 13 設備,這將啟動 Web 身份驗證流,該流還可用於在 Android 和 UWP 設備上啟用 Apple 登錄。

-----

## <a name="aspnet-core-server-back-end"></a>ASP.NET核心伺服器後端介面

可以將`WebAuthenticator`API 與任何 Web 後端服務一起使用。  要將其與ASP.NET核心應用一起使用,首先需要使用以下步驟配置 Web 應用:

1. 在 ASP.NET 核心 Web 應用中設定所需的[外部社交身份驗證提供者](https://docs.microsoft.com/aspnet/core/security/authentication/social/?view=aspnetcore-3.1&tabs=visual-studio)。
2. 將預設身份驗證方案設置為`CookieAuthenticationDefaults.AuthenticationScheme`呼叫`.AddAuthentication()`中。
3. 在`.AddCookies()`Startup.cs`.AddAuthentication()`呼叫中使用。
4. 必須配置`.SaveTokens = true;`所有提供程式。

> [!TIP]
> 如果您想包括 Apple 登錄,`AspNet.Security.OAuth.Apple`可以使用 NuGet 套件。  您可以在 Essentials GitHub 儲存函式庫中查看完整的[Startup.cs 範例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60)。

### <a name="add-a-custom-mobile-auth-controller"></a>新增自訂移動 auth 控制器

使用移動身份驗證流時,通常需要直接啟動流向用戶選擇的提供程式(例如,按一下應用登錄螢幕上的"Microsoft"按鈕)。  能夠在特定回調URI將相關資訊返回給應用以結束身份驗證流也很重要。

為此,請使用自訂 API 控制器:

```csharp
[Route("mobileauth")]
[ApiController]
public class AuthController : ControllerBase
{
    const string callbackScheme = "myapp";

    [HttpGet("{scheme}")] // eg: Microsoft, Facebook, Apple, etc
    public async Task Get([FromRoute]string scheme)
    {
        // 1. Initiate authentication flow with the scheme (provider)
        // 2. When the provider calls back to this URL
        //    a. Parse out the result
        //    b. Build the app callback URL
        //    c. Redirect back to the app
    }
}
```

此控制器的目的是推斷應用請求的方案(提供程式),並與社交提供程式啟動身份驗證流。 當提供程式回調到 Web 後端時,控制器會分析結果,並重定向到應用的回調 URI,並帶有參數。

有時,您可能希望將數據(如提供程式的`access_token`返回到應用)返回,您可以通過回調 URI 的查詢參數執行該操作。 或者,您可能希望在伺服器上創建自己的標識,並將自己的令牌傳回應用。 你做什麼和如何做這部分由你決定!

檢視基本基本的儲存庫中[的完整控制器範例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs)。

-----
## <a name="api"></a>API

- [Web 認證器源碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/WebAuthenticator)
- [Web 認證器 API 文件](xref:Xamarin.Essentials.WebAuthenticator)
- [ASP.NET核心伺服器範例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
