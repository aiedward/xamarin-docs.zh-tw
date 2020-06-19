---
title: 使用 [使用 Apple 登入]Xamarin.Forms
description: 瞭解如何在您的行動應用程式中使用 Apple 執行登入 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 2E47E7F2-93D4-4CA3-9E66-247466D25E4D
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fb37f8fb2d01154bf2e749e685c4e96c12d6bc5e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139486"
---
# <a name="use-sign-in-with-apple-in-xamarinforms"></a>使用 [使用 Apple 登入]Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/signinwithapple/)

使用 Apple 登入適用于 iOS 13 上所有使用協力廠商驗證服務的新應用程式。 IOS 和 Android 之間的執行詳細資料非常不同。 本指南會逐步解說如何在中執行這項操作 Xamarin.Forms 。

在本指南和範例中，會使用特定的平臺服務來處理 Apple 的登入：

- Android 使用一般 web 服務與 OpenID/OpenAuth 的 Azure Functions 交談
- ios 會使用原生 API 在 iOS 13 上進行驗證，並切換回適用于 iOS 12 和以下的一般 web 服務

## <a name="a-sample-apple-sign-in-flow"></a>範例 Apple 登入流程

這個範例會提供固定的執行方式，讓 Apple 登入在您的 Xamarin.Forms 應用程式中工作。

我們會使用兩個 Azure Functions 來協助驗證流程：

1. `applesignin_auth`-產生 Apple Sign In 授權 URL，並重新導向至它。  我們會在伺服器端執行此動作，而不是行動裝置應用程式，因此我們可以快取， `state` 並在 Apple 的伺服器傳送回呼時加以驗證。
2. `applesignin_callback`-處理來自 Apple 的 POST 回呼，並安全地交換存取權杖和識別碼權杖的授權碼。  最後，它會重新導向回應用程式的 URI 配置，並傳回 URL 片段中的權杖。

行動應用程式會自行註冊，以處理我們選取的自訂 URI 配置（在此案例中為 `xamarinformsapplesignin://` ），讓函式 `applesignin_callback` 可以將權杖轉送回它。

當使用者啟動驗證時，會發生下列步驟：

1. 行動應用程式會產生 `nonce` 和 `state` 值，並將它們傳遞至 Azure 函式 `applesignin_auth` 。
2. `applesignin_auth`Azure 函式會產生 Apple Sign In 授權 URL （使用提供的 `state` 和 `nonce` ），並將行動裝置應用程式瀏覽器重新導向至其中。
3. 使用者安全地在裝載于 Apple 伺服器上的 Apple 登入授權頁面中輸入其認證。
4. Apple 登入流程在 Apple 的伺服器上完成後，Apple 會重新導向至， `redirect_uri` 而這將會是 Azure 函式 `applesignin_callback` 。
5. 已驗證從 Apple 傳送至函式的要求 `applesignin_callback` ，以確保傳回正確的 `state` ，而且識別碼權杖宣告是有效的。
6. `applesignin_callback`Azure 函 `code` 式會針對_存取權杖_、重新整理_權杖_和_識別碼權杖_（其中包含有關使用者識別碼、名稱和電子郵件的宣告），交換 Apple 張貼的資料。
7. Azure 函式 `applesignin_callback` 最後會重新導向回到應用程式的 uri 配置（ `xamarinformsapplesignin://` ），並將 URI 片段附加至權杖（例如 `xamarinformsapplesignin://#access_token=...&refresh_token=...&id_token=...` ）。
8. 行動應用程式會將 URI 片段剖析成 `AppleAccount` ，並驗證所收到的宣告是否 `nonce` 符合 `nonce` 流程開始時所產生的。
9. 行動應用程式現在已通過驗證！

## <a name="azure-functions"></a>Azure Functions

這個範例會使用 Azure Functions。 或者，ASP.NET Core 控制器或類似的 web 伺服器解決方案可以提供相同的功能。

### <a name="configuration"></a>組態

使用 Azure Functions 時，需要設定數個應用程式設定：

- `APPLE_SIGNIN_KEY_ID`-這是您稍 `KeyId` 早的。
- `APPLE_SIGNIN_TEAM_ID`-這通常是您在[成員資格設定檔](https://developer.apple.com/account/#/membership)中找到的_小組識別碼_
- `APPLE_SIGNIN_SERVER_ID`：這是 `ServerId` 先前的。  這*不*是您的_應用程式套件組合識別碼，_ 而是您所建立之*服務 ID*的*識別碼*。
- `APPLE_SIGNIN_APP_CALLBACK_URI`-這是您想要使用重新導向至應用程式的自訂 URI 配置。  在此範例中 `xamarinformsapplesignin://` ，會使用。
- `APPLE_SIGNIN_REDIRECT_URI`-您在*Apple 登入*設定一節中建立*服務識別碼*時，所安裝的重新*導向 URL* 。  若要進行測試，它可能看起來像這樣：`http://local.test:7071/api/applesignin_callback`
- `APPLE_SIGNIN_P8_KEY`-檔案的文字內容 `.p8` ，其中所有的分行符號都 `\n` 已移除，因此它是一個長字串

### <a name="security-considerations"></a>安全性考量

**絕對不要**將您的 P8 金鑰儲存在應用程式的程式碼中。 應用程式代碼很容易下載和拆解。 

它也被視為不正確的作法，使用 `WebView` 來裝載驗證流程，以及攔截 URL 導覽事件以取得授權碼。 目前沒有任何完全安全的方式可處理非 iOS13 + 裝置上的 Apple 登入，而不需要在伺服器上裝載一些程式碼來處理權杖交換。 建議您在伺服器上裝載授權 url 產生程式碼，以便在 Apple 發出 POST 回呼給您的伺服器時，快取狀態並加以驗證。

## <a name="a-cross-platform-sign-in-service"></a>跨平臺登入服務

使用 Xamarin.Forms DependencyService，您可以建立不同的驗證服務，以使用 iOS 上的平臺服務，以及適用于 Android 的一般 web 服務和以共用介面為基礎的其他非 iOS 平臺。

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

編譯旗標 `__IOS__13` 是用來提供 iOS 13 的支援，以及回溯至一般 web 服務的舊版版本。

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

## <a name="summary"></a>摘要

本文說明設定以 Apple 登入以用於應用程式的必要步驟 Xamarin.Forms 。

## <a name="related-links"></a>相關連結

- [XamarinFormsAppleSignIn （範例）](https://github.com/Redth/Xamarin.AppleSignIn.Sample)
- [使用 Apple 指導方針登入](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
