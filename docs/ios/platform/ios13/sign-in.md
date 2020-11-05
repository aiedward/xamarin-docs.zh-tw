---
title: 在 Xamarin 中使用 Apple 登入
description: 使用 Apple 登入可在使用協力廠商驗證服務時提供身分識別保護。
ms.prod: xamarin
ms.assetid: CDA59BBF-AAE1-4D4F-B4AE-DB37220D41EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
ms.openlocfilehash: 5cbe3f36d1aeb12be671b14a4f76c79764e814e6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375001"
---
# <a name="sign-in-with-apple-in-xamarinios"></a>在 Xamarin 中使用 Apple 登入

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/ios-samples/ios13-addingthesigninwithappleflowtoyourapp/)

使用 Apple 登入是一項新的服務，可為協力廠商驗證服務的使用者提供身分識別保護。 從 iOS 13 開始，Apple 要求任何使用協力廠商驗證服務的新應用程式，也應提供使用 Apple 登入。 更新的現有應用程式不需要在2020年4月之前，使用 Apple 新增登入。

本檔介紹如何使用 Apple 將登入新增至 iOS 13 應用程式。

## <a name="apple-developer-setup"></a>Apple 開發人員設定

使用 [使用 Apple 登入] 建立和執行應用程式之前，您必須完成這些步驟。 在 [Apple Developer 憑證上，識別碼 & 設定檔][5] 入口網站：

1. 建立新的 **應用程式** 識別碼識別碼。
2. 在 [ **描述** ] 欄位中設定描述。
3. 選擇 **明確** 的套件組合識別碼，並 `com.xamarin.AddingTheSignInWithAppleFlowToYourApp` 在欄位中設定。
4. 啟用 [ **使用 Apple** 功能登入]，並註冊新的身分識別。
5. 使用新的身分識別建立新的布建設定檔。
6. 下載並安裝在您的裝置上。
7. 在 Visual Studio 中，啟用 **plist** 檔案中的 [ **使用 Apple 登入** ] 功能。

## <a name="check-sign-in-status"></a>檢查登入狀態

當您的應用程式開始時，或當您第一次需要檢查使用者的驗證狀態時，請具現化 `ASAuthorizationAppleIdProvider` 並檢查目前的狀態：

```csharp
var appleIdProvider = new ASAuthorizationAppleIdProvider ();
appleIdProvider.GetCredentialState (KeychainItem.CurrentUserIdentifier, (credentialState, error) => {
    switch (credentialState) {
    case ASAuthorizationAppleIdProviderCredentialState.Authorized:
        // The Apple ID credential is valid.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.Revoked:
        // The Apple ID credential is revoked.
        break;
    case ASAuthorizationAppleIdProviderCredentialState.NotFound:
        // No credential was found, so show the sign-in UI.
        InvokeOnMainThread (() => {
            var storyboard = UIStoryboard.FromName ("Main", null);

            if (!(storyboard.InstantiateViewController (nameof (LoginViewController)) is LoginViewController viewController))
                return;

            viewController.ModalPresentationStyle = UIModalPresentationStyle.FormSheet;
            viewController.ModalInPresentation = true;
            Window?.RootViewController?.PresentViewController (viewController, true, null);
        });
        break;
    }
});
```

在此程式碼中，于期間呼叫 `FinishedLaunching` `AppDelegate.cs` ，應用程式將會在狀態為 `NotFound` 並向使用者呈現時處理 `LoginViewController` 。 如果狀態傳回 `Authorized` 或 `Revoked` ，則會向使用者顯示不同的動作。

## <a name="a-loginviewcontroller-for-sign-in-with-apple"></a>使用 Apple 登入的 LoginViewController

在 `UIViewController` Apple 中執行登入邏輯和提供登入的會需要執行， `IASAuthorizationControllerDelegate` `IASAuthorizationControllerPresentationContextProviding` 如 `LoginViewController` 下列範例所示。

```csharp
public partial class LoginViewController : UIViewController, IASAuthorizationControllerDelegate, IASAuthorizationControllerPresentationContextProviding {
    public LoginViewController (IntPtr handle) : base (handle)
    {
    }

    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        // Perform any additional setup after loading the view, typically from a nib.

        SetupProviderLoginView ();
    }

    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        PerformExistingAccountSetupFlows ();
    }

    void SetupProviderLoginView ()
    {
        var authorizationButton = new ASAuthorizationAppleIdButton (ASAuthorizationAppleIdButtonType.Default, ASAuthorizationAppleIdButtonStyle.White);
        authorizationButton.TouchUpInside += HandleAuthorizationAppleIDButtonPress;
        loginProviderStackView.AddArrangedSubview (authorizationButton);
    }

    // Prompts the user if an existing iCloud Keychain credential or Apple ID credential is found.
    void PerformExistingAccountSetupFlows ()
    {
        // Prepare requests for both Apple ID and password providers.
        ASAuthorizationRequest [] requests = {
            new ASAuthorizationAppleIdProvider ().CreateRequest (),
            new ASAuthorizationPasswordProvider ().CreateRequest ()
        };

        // Create an authorization controller with the given requests.
        var authorizationController = new ASAuthorizationController (requests);
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }

    private void HandleAuthorizationAppleIDButtonPress (object sender, EventArgs e)
    {
        var appleIdProvider = new ASAuthorizationAppleIdProvider ();
        var request = appleIdProvider.CreateRequest ();
        request.RequestedScopes = new [] { ASAuthorizationScope.Email, ASAuthorizationScope.FullName };

        var authorizationController = new ASAuthorizationController (new [] { request });
        authorizationController.Delegate = this;
        authorizationController.PresentationContextProvider = this;
        authorizationController.PerformRequests ();
    }
}
```

![使用登入 Apple 的範例應用程式動畫](sign-in-images/sign-in-flow.png)

此範例程式碼會檢查中目前的登入狀態 `PerformExistingAccountSetupFlows` ，並連接到目前的 view 做為委派。 如果找到現有的 iCloud Keychain credential 或 Apple ID 認證，系統會提示使用者使用該認證。

Apple 提供的 `ASAuthorizationAppleIdButton` 按鈕特別適用于此用途。 當您接觸時，按鈕將會觸發方法中處理的工作流程 `HandleAuthorizationAppleIDButtonPress` 。

## <a name="handling-authorization"></a>處理授權

在中，會 `IASAuthorizationController` 執行任何自訂邏輯來儲存使用者的帳戶。 下列範例會將使用者的帳戶儲存在 Apple 本身的儲存體服務 Keychain 中。

```csharp
#region IASAuthorizationController Delegate

[Export ("authorizationController:didCompleteWithAuthorization:")]
public void DidComplete (ASAuthorizationController controller, ASAuthorization authorization)
{
    if (authorization.GetCredential<ASAuthorizationAppleIdCredential> () is ASAuthorizationAppleIdCredential appleIdCredential) {
        var userIdentifier = appleIdCredential.User;
        var fullName = appleIdCredential.FullName;
        var email = appleIdCredential.Email;

        // Create an account in your system.
        // For the purpose of this demo app, store the userIdentifier in the keychain.
        try {
            new KeychainItem ("com.example.apple-samplecode.juice", "userIdentifier").SaveItem (userIdentifier);
        } catch (Exception) {
            Console.WriteLine ("Unable to save userIdentifier to keychain.");
        }

        // For the purpose of this demo app, show the Apple ID credential information in the ResultViewController.
        if (!(PresentingViewController is ResultViewController viewController))
            return;

        InvokeOnMainThread (() => {
            viewController.UserIdentifierText = userIdentifier;
            viewController.GivenNameText = fullName?.GivenName ?? "";
            viewController.FamilyNameText = fullName?.FamilyName ?? "";
            viewController.EmailText = email ?? "";

            DismissViewController (true, null);
        });
    } else if (authorization.GetCredential<ASPasswordCredential> () is ASPasswordCredential passwordCredential) {
        // Sign in using an existing iCloud Keychain credential.
        var username = passwordCredential.User;
        var password = passwordCredential.Password;

        // For the purpose of this demo app, show the password credential as an alert.
        InvokeOnMainThread (() => {
            var message = $"The app has received your selected credential from the keychain. \n\n Username: {username}\n Password: {password}";
            var alertController = UIAlertController.Create ("Keychain Credential Received", message, UIAlertControllerStyle.Alert);
            alertController.AddAction (UIAlertAction.Create ("Dismiss", UIAlertActionStyle.Cancel, null));

            PresentViewController (alertController, true, null);
        });
    }
}

[Export ("authorizationController:didCompleteWithError:")]
public void DidComplete (ASAuthorizationController controller, NSError error)
{
    Console.WriteLine (error);
}

#endregion
```

## <a name="authorization-controller"></a>授權控制器

此實作為中的最後一個部分是 `ASAuthorizationController` 管理提供者授權要求的。

```csharp
#region IASAuthorizationControllerPresentation Context Providing

public UIWindow GetPresentationAnchor (ASAuthorizationController controller) => View.Window;

#endregion
```

## <a name="related-links"></a>相關連結

* [使用 Apple 指導方針登入](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
* [使用 Apple 權利登入。][2]
* [WWDC 2019 會話706：使用 Apple 進行登入的簡介。][3]
* [使用適用于 Xamarin 的 Apple 進行安裝程式登入][4]

[1]: https://developer.apple.com/documentation/authenticationservices/adding_the_sign_in_with_apple_flow_to_your_app
[2]: https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_applesignin
[3]: https://developer.apple.com/videos/play/wwdc19/706/
[4]: ~/xamarin-forms/platform/sign-in-with-apple/setup.md
[5]: https://developer.apple.com/account/resources/identifiers/list