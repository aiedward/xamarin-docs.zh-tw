---
title: Xamarin.Essentials： Web 驗證器
description: 本檔說明中的 WebAuthenticator 類別 Xamarin.Essentials ，可讓您啟動以瀏覽器為基礎的驗證流程，以接聽應用程式的回呼。
ms.assetid: 3D95371E-5D59-440E-8D31-F3C04E493DC1
author: redth
ms.author: jodick
ms.date: 03/26/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1dba93f5f6f0f81477824d22c616cd4a4c2d0b41
ms.sourcegitcommit: b6f6dd231b6bef2df8b7f8e4c530c9aead70809a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498385"
---
# <a name="xamarinessentials-web-authenticator"></a>Xamarin.Essentials： Web 驗證器

**WebAuthenticator** 類別可讓您起始以瀏覽器為基礎的流程，此流程會接聽對應用程式註冊之特定 URL 的回呼。

## <a name="overview"></a>概觀

許多應用程式都需要新增使用者驗證，這通常表示讓您的使用者能夠登入其現有的 Microsoft、Facebook、Google 和現在的 Apple 登入帳戶。

[Microsoft 驗證程式庫 (MSAL) ](/azure/active-directory/develop/msal-overview) 提供絕佳的回合金鑰解決方案，以將驗證新增至您的應用程式。 甚至還支援其用戶端 NuGet 套件中的 Xamarin 應用程式。

如果您想要使用自己的 web 服務進行驗證，可以使用 **WebAuthenticator** 來執行用戶端功能。

## <a name="why-use-a-server-back-end"></a>為何要使用伺服器後端？

許多驗證提供者都已移至只提供明確或雙腳的驗證流程，以確保更好的安全性。 這表示您需要來自提供者的「 _用戶端密碼_ 」才能完成驗證流程。 可惜的是，行動應用程式並不是儲存秘密的絕佳位置，而儲存在行動應用程式的程式碼、二進位檔中的任何事物，通常都被視為不安全。

這裡的最佳作法是在您的行動應用程式與驗證提供者之間，使用 web 後端作為中介層。

> [!IMPORTANT]
> 強烈建議您不要在驗證流程中使用較舊的僅限行動驗證程式庫和模式，因為其固有的安全性不足，無法儲存用戶端密碼。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

若要存取 **WebAuthenticator** 功能，需要下列平臺特定設定。

# <a name="android"></a>[Android](#tab/android)

Android 需要意圖篩選器設定來處理您的回呼 URI。 將類別子類別化可輕鬆完成此作業 `WebAuthenticatorCallbackActivity` ：

```csharp
const string CALLBACK_SCHEME = "myapp";

[Activity(NoHistory = true, LaunchMode = LaunchMode.SingleTop)]
[IntentFilter(new[] { Android.Content.Intent.ActionView },
    Categories = new[] { Android.Content.Intent.CategoryDefault, Android.Content.Intent.CategoryBrowsable },
    DataScheme = CALLBACK_SCHEME)]
public class WebAuthenticationCallbackActivity : Xamarin.Essentials.WebAuthenticatorCallbackActivity
{
}
```
如果您專案的目標 Android 版本設為 **android 11 (R API 30)** 您必須使用與新的 [封裝可見度需求](https://developer.android.com/preview/privacy/package-visibility)搭配使用的查詢來更新 android 資訊清單。

開啟 [Properties] 資料夾下的 **AndroidManifest.xml** 檔案並在 [manifest] 節點內新增下列內容：
```XML
<queries>
    <intent>
        <action android:name="android.support.customtabs.action.CustomTabsService" />
    </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 上，您必須將應用程式的回呼 URI 模式新增至 plist，例如：

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLName</key>
        <string>xamarinessentials</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>xamarinessentials</string>
        </array>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
    </dict>
</array>
```

您也需要覆寫您 `AppDelegate` 的 `OpenUrl` 和方法， `ContinueUserActivity` 以呼叫 Essentials：

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    if (Xamarin.Essentials.Platform.OpenUrl(app, url, options))
        return true;

    return base.OpenUrl(app, url, options);
}

public override bool ContinueUserActivity(UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    if (Xamarin.Essentials.Platform.ContinueUserActivity(application, userActivity, completionHandler))
        return true;
    return base.ContinueUserActivity(application, userActivity, completionHandler);
}
```

# <a name="uwp"></a>[UWP](#tab/uwp)

針對 UWP，您必須在檔案中宣告您的回呼 URI `Package.appxmanifest` ：

```xml
<Applications>
    <Application Id="App" Executable="$targetnametoken$.exe" EntryPoint="MyApp.App">
        <Extensions>
            <uap:Extension Category="windows.protocol">
            <uap:Protocol Name="myapp">
                <uap:DisplayName>My App</uap:DisplayName>
            </uap:Protocol>
            </uap:Extension>
        </Extensions>
    </Application>
</Applications>
```

-----

## <a name="using-webauthenticator"></a>使用 WebAuthenticator

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

API 主要是由採用兩個參數的單一方法所組成 `AuthenticateAsync` ：應用來啟動網頁瀏覽器流程的 url，以及您預期流程最後回呼的 Uri，以及您的應用程式已註冊要能夠處理的 Uri。

結果是， `WebAuthenticatorResult` 其中包含任何從回呼 URI 剖析的查詢參數：

```csharp
var authResult = await WebAuthenticator.AuthenticateAsync(
        new Uri("https://mysite.com/mobileauth/Microsoft"),
        new Uri("myapp://"));

var accessToken = authResult?.AccessToken;
```

`WebAuthenticator`API 會負責在瀏覽器中啟動 url，並等候收到回呼：

![一般 Web 驗證流程](images/web-authenticator.png)

如果使用者在任何時間點取消流程， `TaskCanceledException` 就會擲回。

## <a name="platform-differences"></a>平臺差異

# <a name="android"></a>[Android](#tab/android)

使用自訂索引標籤（如果有的話），否則會針對 URL 啟動意圖。

# <a name="ios"></a>[iOS](#tab/ios)

在 iOS 12 或更高版本上， `ASWebAuthenticationSession` 則會使用。  在 iOS 11 上， `SFAuthenticationSession` 會使用。  在較舊的 iOS 版本上， `SFSafariViewController` 則會使用（如果有的話），否則會使用 Safari。

# <a name="uwp"></a>[UWP](#tab/uwp)

在 UWP 上， `WebAuthenticationBroker` 會使用（如果支援的話），否則會使用系統瀏覽器。

-----

## <a name="apple-sign-in"></a>Apple 登入

根據 [Apple 的審核指導方針](https://developer.apple.com/app-store/review/guidelines/#sign-in-with-apple)，如果您的應用程式使用任何社交登入服務來進行驗證，則也必須提供 Apple 登入作為選項。

若要將 Apple 登入新增至您的應用程式，您必須先將 [應用程式設定為使用 Apple 登入](../ios/platform/ios13/sign-in.md)。

針對 iOS 13 和更新版本，您需要呼叫 `AppleSignInAuthenticator.AuthenticateAsync()` 方法。 這將會在幕後使用原生 Apple 登入 API，讓您的使用者能夠在這些裝置上獲得最佳體驗。 您可以撰寫共用程式碼，在執行時間使用正確的 API，如下所示：

```csharp
var scheme = "..."; // Apple, Microsoft, Google, Facebook, etc.
WebAuthenticatorResult r = null;

if (scheme.Equals("Apple")
    && DeviceInfo.Platform == DevicePlatform.iOS
    && DeviceInfo.Version.Major >= 13)
{
    // Use Native Apple Sign In API's
    r = await AppleSignInAuthenticator.AuthenticateAsync();
}
else
{
    // Web Authentication flow
    var authUrl = new Uri(authenticationUrl + scheme);
    var callbackUrl = new Uri("xamarinessentials://");

    r = await WebAuthenticator.AuthenticateAsync(authUrl, callbackUrl);
}

var authToken = string.Empty;
if (r.Properties.TryGetValue("name", out var name) && !string.IsNullOrEmpty(name))
    authToken += $"Name: {name}{Environment.NewLine}";
if (r.Properties.TryGetValue("email", out var email) && !string.IsNullOrEmpty(email))
    authToken += $"Email: {email}{Environment.NewLine}";

// Note that Apple Sign In has an IdToken and not an AccessToken
authToken += r?.AccessToken ?? r?.IdToken;
```

> [!TIP]
> 針對非 iOS 13 裝置，這會啟動 web 驗證流程，也可用來在您的 Android 和 UWP 裝置上啟用 Apple 登入。
> 您可以登入 iOS 模擬器上的 iCloud 帳戶以測試 Apple 登入。

-----

## <a name="aspnet-core-server-back-end"></a>ASP.NET core server 後端

您可以使用 `WebAuthenticator` API 搭配任何 web 後端服務。  若要搭配 ASP.NET core 應用程式使用它，您必須先使用下列步驟來設定 web 應用程式：

1. 在 ASP.NET Core web 應用程式中設定所需的 [外部社交驗證提供者](/aspnet/core/security/authentication/social/?tabs=visual-studio) 。
2. 在您的呼叫中將預設驗證配置設定為 `CookieAuthenticationDefaults.AuthenticationScheme` `.AddAuthentication()` 。
3. `.AddCookie()`在 Startup.cs 呼叫中使用 `.AddAuthentication()` 。
4. 所有提供者都必須使用進行設定 `.SaveTokens = true;` 。


```csharp
services.AddAuthentication(o =>
    {
        o.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddFacebook(fb =>
    {
        fb.AppId = Configuration["FacebookAppId"];
        fb.AppSecret = Configuration["FacebookAppSecret"];
        fb.SaveTokens = true;
    });
```

> [!TIP]
> 如果您想要包含 Apple 登入，您可以使用 `AspNet.Security.OAuth.Apple` NuGet 套件。  您可以在 Essentials GitHub 存放庫中查看完整的 [Startup.cs 範例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Startup.cs#L32-L60) 。

### <a name="add-a-custom-mobile-auth-controller"></a>新增自訂行動驗證控制器

使用行動驗證流程時，通常會想要直接對使用者所選擇的提供者起始流程 (例如，在應用程式) 的 [登入] 畫面上，按一下 [Microsoft] 按鈕。  也很重要的是，要能夠在特定的回呼 URI 將相關的資訊傳回您的應用程式，以結束驗證流程。

若要達成此目的，請使用自訂 API 控制器：

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

此控制器的目的是要推斷應用程式所要求 (提供者) 配置，並使用社交提供者起始驗證流程。 當提供者回撥至 web 後端時，控制器會剖析結果，並將參數重新導向至應用程式的回呼 URI。

有時您可能會想要將資料（例如提供者） `access_token` 傳回給應用程式，您可以透過回呼 URI 的查詢參數來完成此動作。 或者，您可能會想要改為在伺服器上建立自己的身分識別，並將您自己的權杖傳回給應用程式。 您可以自行決定如何處理此部分？

查看 Essentials 存放庫中的 [完整控制器範例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/Controllers/MobileAuthController.cs) 。

> [!NOTE]
> 上述範例示範如何從協力廠商驗證 (ie： OAuth) 提供者傳回存取權杖。 若要取得權杖，讓您可以用來授權 web 後端本身的 web 要求，您應該在 web 應用程式中建立自己的權杖，並改為傳回該權杖。  [ASP.NET core authentication 的總覽](/aspnet/core/security/authentication)具有有關 ASP.NET Core 中 advanced authentication 案例的詳細資訊。

-----
## <a name="api"></a>API

- [WebAuthenticator 來源程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/WebAuthenticator)
- [WebAuthenticator API 檔](xref:Xamarin.Essentials.WebAuthenticator)
- [ASP.NET 核心伺服器範例](https://github.com/xamarin/Essentials/blob/develop/Samples/Sample.Server.WebAuthenticator/)
