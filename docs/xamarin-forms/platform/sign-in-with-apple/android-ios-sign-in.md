---
title: 使用 [使用 Apple 登入] Xamarin.Forms
description: 瞭解如何在您的行動應用程式中執行使用 Apple 登入 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ca039e45bd7456f2cde2a733748777cddee0e8a8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368605"
---
# <a name="use-sign-in-with-apple-in-no-locxamarinforms"></a>在中使用 [使用 Apple 登入] Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/signinwithapple/)

使用 Apple 登入適用于 iOS 13 上所有使用協力廠商驗證服務的新應用程式。 IOS 和 Android 之間的執行詳細資料相當不同。 本指南將逐步解說如何在中執行這項作業 Xamarin.Forms 。

在本指南和範例中，會使用特定平臺服務來處理 Apple 的登入：

- 使用 OpenAuth 與 OpenID/進行 Azure Functions 的一般 web 服務的 Android
- ios 13 使用原生 API 來進行驗證，並切換回適用于 iOS 12 和以下的一般 web 服務

## <a name="a-sample-apple-sign-in-flow"></a>範例 Apple 登入流程

此範例提供固定的執行方式，讓 Apple 登入在您的 Xamarin.Forms 應用程式中運作。

我們會使用兩個 Azure Functions 來協助驗證流程：

1. `applesignin_auth` -產生 Apple Sign In 授權 URL，並重新導向至該 URL。  我們會在伺服器端（而不是行動裝置應用程式）上進行這項作業，因此我們可以在 `state` Apple 的伺服器傳送回呼時，快取並驗證。
2. `applesignin_callback` -處理來自 Apple 的 POST 回呼，並安全地交換存取權杖和識別碼權杖的授權碼。  最後，它會重新導向回到應用程式的 URI 配置，並傳回 URL 片段中的權杖。

行動裝置應用程式會自行註冊，以處理我們在此案例中選取 (的自訂 URI 配置 `xamarinformsapplesignin://`) 因此函式 `applesignin_callback` 可以將權杖轉送回該配置。

當使用者啟動驗證時，會發生下列步驟：

1. 行動裝置應用程式會產生 `nonce` 和 `state` 值，並將其傳遞至 `applesignin_auth` Azure 函數。
2. `applesignin_auth`Azure 函式會使用提供的和) 來產生 Apple 登入授權 URL (，並將行動裝置 `state` `nonce` 應用程式瀏覽器重新導向至該 URL。
3. 使用者會在 Apple 的伺服器上裝載的 Apple 登入授權頁面中安全地輸入其認證。
4. Apple 登入流程在 Apple 伺服器上完成後，Apple 會重新導向至 `redirect_uri` 將成為 `applesignin_callback` Azure function 的。
5. 系統會驗證從 Apple 傳送至函式的要求 `applesignin_callback` ，以確保 `state` 傳回正確的，且識別碼權杖宣告有效。
6. `applesignin_callback`Azure 函 `code` 式會交換 Apple 所張貼的資料、 _存取權杖_ 、重新整理 _權杖_ 和 _識別碼權杖_ (，其中包含有關使用者識別碼、名稱和電子郵件) 的宣告。
7. Azure 函式 `applesignin_callback` 最後會重新導向回到應用程式的 uri 配置 (`xamarinformsapplesignin://`) 附加具有標記的 URI 片段 (例如 `xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...`) 。
8. 行動裝置應用程式會將 URI 片段剖析成 `AppleAccount` ，並驗證所收到的宣告 `nonce` 符合在 `nonce` 流程開始時產生的結果。
9. 行動裝置應用程式現在已通過驗證！

## <a name="azure-functions"></a>Azure Functions

這個範例會使用 Azure Functions。 或者，ASP.NET Core 控制器或類似的 web 伺服器解決方案可以提供相同的功能。

### <a name="configuration"></a>設定

使用 Azure Functions 時，必須設定數個應用程式設定：

- `APPLE_SIGNIN_KEY_ID` -這是您稍 `KeyId` 早的。
- `APPLE_SIGNIN_TEAM_ID`-這通常是您在 [成員資格設定檔](https://developer.apple.com/account/#/membership)中找到的 _團隊識別碼_
- `APPLE_SIGNIN_SERVER_ID`：這是稍 `ServerId` 早的。  它 *不* 是您的 _應用程式_ 套件組合識別碼，而是您所建立之 *服務**識別碼的識別碼* 。
- `APPLE_SIGNIN_APP_CALLBACK_URI` -這是您想要使用重新導向至應用程式的自訂 URI 配置。  在此範例中 `xamarinformsapplesignin://` ，會使用。
- `APPLE_SIGNIN_REDIRECT_URI`-您在 [ *Apple 登入* 設定] 區段中建立 *服務識別碼* 時所設定的重新 *導向 URL* 。  若要進行測試，看起來可能像這樣： `http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY` -檔案的文字內容 `.p8` ，所有的 `\n` 分行符號都已移除，因此它是一個長字串

### <a name="security-considerations"></a>安全性考量

**絕對不要** 將您的 P8 金鑰儲存在應用程式程式碼中。 應用程式程式碼很容易下載和拆解。 

使用 `WebView` 裝載驗證流程以及攔截 URL 導覽事件以取得授權碼時，也會被視為不正確的作法。 目前，目前沒有完全安全的方法可處理非 iOS13 + 裝置上的 Apple 登入，而不需要在伺服器上裝載一些程式碼來處理權杖交換。 建議您在伺服器上裝載授權 url 產生程式碼，讓您可以快取狀態，並在 Apple 發出 POST 回呼給您的伺服器時進行驗證。

## <a name="a-cross-platform-sign-in-service"></a>跨平臺登入服務

使用 Xamarin.Forms DependencyService，您可以建立個別的驗證服務，這些服務會使用 iOS 上的平臺服務，以及適用于 Android 和其他以共用介面為基礎的非 iOS 平臺的一般 web 服務。

```csharp
public interface IAppleSignInService
{
    bool Callback(string url);

    Task<AppleAccount> SignInAsync();
}
```

在 iOS 上，會使用原生 Api：

```csharp
public class AppleSignInServiceiOS : IAppleSignInService
{
#if __IOS__13
    AuthManager authManager;
#endif

    bool Is13 => UIDevice.CurrentDevice.CheckSystemVersion(13, 0);
    WebAppleSignInService webSignInService;

    public AppleSignInServiceiOS()
    {
        if (!Is13)
            webSignInService = new WebAppleSignInService();
    }

    public async Task<AppleAccount> SignInAsync()
    {
        // Fallback to web for older iOS versions
        if (!Is13)
            return await webSignInService.SignInAsync();

        AppleAccount appleAccount = default;

#if __IOS__13
        var provider = new ASAuthorizationAppleIdProvider();
        var req = provider.CreateRequest();

        authManager = new AuthManager(UIApplication.SharedApplication.KeyWindow);

        req.RequestedScopes = new[] { ASAuthorizationScope.FullName, ASAuthorizationScope.Email };
        var controller = new ASAuthorizationController(new[] { req });

        controller.Delegate = authManager;
        controller.PresentationContextProvider = authManager;

        controller.PerformRequests();

        var creds = await authManager.Credentials;

        if (creds == null)
            return null;

        appleAccount = new AppleAccount();
        appleAccount.IdToken = JwtToken.Decode(new NSString(creds.IdentityToken, NSStringEncoding.UTF8).ToString());
        appleAccount.Email = creds.Email;
        appleAccount.UserId = creds.User;
        appleAccount.Name = NSPersonNameComponentsFormatter.GetLocalizedString(creds.FullName, NSPersonNameComponentsFormatterStyle.Default, NSPersonNameComponentsFormatterOptions.Phonetic);
        appleAccount.RealUserStatus = creds.RealUserStatus.ToString();
#endif

        return appleAccount;
    }

    public bool Callback(string url) => true;
}

#if __IOS__13
class AuthManager : NSObject, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding
{
    public Task<ASAuthorizationAppleIdCredential> Credentials
        => tcsCredential?.Task;

    TaskCompletionSource<ASAuthorizationAppleIdCredential> tcsCredential;

    UIWindow presentingAnchor;

    public AuthManager(UIWindow presentingWindow)
    {
        tcsCredential = new TaskCompletionSource<ASAuthorizationAppleIdCredential>();
        presentingAnchor = presentingWindow;
    }

    public UIWindow GetPresentationAnchor(ASAuthorizationController controller)
        => presentingAnchor;

    [Export("authorizationController:didCompleteWithAuthorization:")]
    public void DidComplete(ASAuthorizationController controller, ASAuthorization authorization)
    {
        var creds = authorization.GetCredential<ASAuthorizationAppleIdCredential>();
        tcsCredential?.TrySetResult(creds);
    }

    [Export("authorizationController:didCompleteWithError:")]
    public void DidComplete(ASAuthorizationController controller, NSError error)
        => tcsCredential?.TrySetException(new Exception(error.LocalizedDescription));
}
#endif
```

編譯旗標 `__IOS__13` 是用來提供 iOS 13 以及回溯至一般 web 服務的舊版版本支援。

在 Android 上，會使用具有 Azure Functions 的一般 web 服務：

```csharp
public class WebAppleSignInService : IAppleSignInService
{
    // IMPORTANT: This is what you register each native platform's url handler to be
    public const string CallbackUriScheme = "xamarinformsapplesignin";
    public const string InitialAuthUrl = "http://local.test:7071/api/applesignin_auth";

    string currentState;
    string currentNonce;

    TaskCompletionSource<AppleAccount> tcsAccount = null;

    public bool Callback(string url)
    {
        // Only handle the url with our callback uri scheme
        if (!url.StartsWith(CallbackUriScheme + "://"))
            return false;

        // Ensure we have a task waiting
        if (tcsAccount != null && !tcsAccount.Task.IsCompleted)
        {
            try
            {
                // Parse the account from the url the app opened with
                var account = AppleAccount.FromUrl(url);

                // IMPORTANT: Validate the nonce returned is the same as our originating request!!
                if (!account.IdToken.Nonce.Equals(currentNonce))
                    tcsAccount.TrySetException(new InvalidOperationException("Invalid or non-matching nonce returned"));

                // Set our account result
                tcsAccount.TrySetResult(account);
            }
            catch (Exception ex)
            {
                tcsAccount.TrySetException(ex);
            }
        }

        tcsAccount.TrySetResult(null);
        return false;
    }

    public async Task<AppleAccount> SignInAsync()
    {
        tcsAccount = new TaskCompletionSource<AppleAccount>();

        // Generate state and nonce which the server will use to initial the auth
        // with Apple.  The nonce should flow all the way back to us when our function
        // redirects to our app
        currentState = Util.GenerateState();
        currentNonce = Util.GenerateNonce();

        // Start the auth request on our function (which will redirect to apple)
        // inside a browser (either SFSafariViewController, Chrome Custom Tabs, or native browser)
        await Xamarin.Essentials.Browser.OpenAsync($"{InitialAuthUrl}?&state={currentState}&nonce={currentNonce}",
            Xamarin.Essentials.BrowserLaunchMode.SystemPreferred);

        return await tcsAccount.Task;
    }
}
```

## <a name="summary"></a>總結

本文說明在應用程式中設定使用 Apple 進行登入所需的步驟 Xamarin.Forms 。

## <a name="related-links"></a>相關連結

- [XamarinFormsAppleSignIn (範例) ](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [使用 Apple 指導方針登入](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)