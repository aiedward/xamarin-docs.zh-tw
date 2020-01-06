---
title: 使用 Xamarin 的 Touch ID 和臉部識別碼
description: 本檔提供 iOS 中生物識別驗證的高階說明。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: 744a07343b9da87f196c0664f57b7d950844ab04
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490294"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>搭配使用 Touch ID 和臉部識別碼與 Xamarin. iOS

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)

iOS 支援兩種生物識別驗證系統：

1. **TOUCH ID**會使用 [首頁] 按鈕底下的指紋感應器。
1. **臉部識別碼**會使用前方相機感應器，透過臉部掃描來驗證使用者。

Touch ID 是在 iOS 7 中引進，而在 iOS 11 中是臉部識別碼。

這些驗證系統依賴以硬體為基礎的安全性處理器，稱為_安全記憶體保護區_。 安全記憶體保護區負責加密臉部和指紋資料的數學標記法，並使用這項資訊來驗證使用者。 根據 Apple，臉部和指紋資料不會離開裝置，也不會備份到 iCloud。 應用程式會透過_本機驗證_API 與安全記憶體保護區互動，而且無法取得臉部或指紋資料，或直接存取安全的記憶體保護區。

在提供受保護內容的存取權之前，應用程式可以使用 Touch ID 和臉部識別碼來驗證使用者。

## <a name="local-authentication-context"></a>本機驗證內容

IOS 上的生物識別驗證依賴_本機驗證內容_物件，這是 `LAContext` 類別的實例。 `LAContext` 類別可讓您：

- 檢查生物識別硬體的可用性。
- 評估驗證原則。
- 評估存取控制。
- 自訂和顯示驗證提示。
- 重複使用或使驗證狀態失效。
- 管理認證。

## <a name="detect-available-authentication-methods"></a>偵測可用的驗證方法

範例專案包含由 `AuthenticationViewController`所支援的 `AuthenticationView`。 此類別會覆寫 `ViewWillAppear` 方法，以偵測可用的驗證方法：

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    public override void ViewWillAppear(bool animated)
    {
        base.ViewWillAppear(animated);
        unAuthenticatedLabel.Text = "";
    
        var context = new LAContext();
        var buttonText = "";

        // Is login with biometrics possible?
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out var authError1))
        {
            // has Touch ID or Face ID
            if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
            {
                context.LocalizedReason = "Authorize for access to secrets"; // iOS 11
                BiometryType = context.BiometryType == LABiometryType.TouchId ? "Touch ID" : "Face ID";
                buttonText = $"Login with {BiometryType}";
            }
            // No FaceID before iOS 11
            else
            {
                buttonText = $"Login with Touch ID";
            }
        }

        // Is pin login possible?
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out var authError2))
        {
            buttonText = $"Login"; // with device PIN
            BiometryType = "Device PIN";
        }

        // Local authentication not possible
        else
        {
            // Application might choose to implement a custom username/password
            buttonText = "Use unsecured";
            BiometryType = "none";
        }
        AuthenticateButton.SetTitle(buttonText, UIControlState.Normal);
    }
}
```

當 UI 即將向使用者顯示時，會呼叫 `ViewWillAppear` 方法。 這個方法會定義 `LAContext` 的新實例，並使用 `CanEvaluatePolicy` 方法來判斷是否已啟用生物識別驗證。 若是如此，它會檢查系統版本並 `BiometryType` 列舉，以判斷可用的生物特徵辨識選項。

如果未啟用生物識別驗證，應用程式會嘗試切換回 PIN 驗證。 如果生物識別或 PIN 驗證都無法使用，則裝置擁有者尚未啟用安全性功能，而且無法透過本機驗證來保護內容。

## <a name="authenticate-a-user"></a>驗證使用者

範例專案中的 `AuthenticationViewController` 包括 `AuthenticateMe` 方法，其負責驗證使用者：

```csharp
partial class AuthenticationViewController: UIViewController
{
    // ...
    string BiometryType = "";

    partial void AuthenticateMe(UIButton sender)
    {
        var context = new LAContext();
        NSError AuthError;
        var localizedReason = new NSString("To access secrets");
    
        // Because LocalAuthentication APIs have been extended over time,
        // you must check iOS version before setting some properties
        context.LocalizedFallbackTitle = "Fallback";
    
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            context.LocalizedCancelTitle = "Cancel";
        }
        if (UIDevice.CurrentDevice.CheckSystemVersion(11, 0))
        {
            context.LocalizedReason = "Authorize for access to secrets";
            BiometryType = context.BiometryType == LABiometryType.TouchId ? "TouchID" : "FaceID";
        }
    
        // Check if biometric authentication is possible
        if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = $"{BiometryType} Authentication Failed";
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthenticationWithBiometrics, localizedReason, replyHandler);
        }

        // Fall back to PIN authentication
        else if (context.CanEvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, out AuthError))
        {
            replyHandler = new LAContextReplyHandler((success, error) =>
            {
                // This affects UI and must be run on the main thread
                this.InvokeOnMainThread(() =>
                {
                    if (success)
                    {
                        PerformSegue("AuthenticationSegue", this);
                    }
                    else
                    {
                        unAuthenticatedLabel.Text = "Device PIN Authentication Failed";
                        AuthenticateButton.Hidden = true;
                    }
                });
    
            });
            context.EvaluatePolicy(LAPolicy.DeviceOwnerAuthentication, localizedReason, replyHandler);
        }

        // User hasn't configured any authentication: show dialog with options
        else
        {
            unAuthenticatedLabel.Text = "No device auth configured";
            var okCancelAlertController = UIAlertController.Create("No authentication", "This device does't have authentication configured.", UIAlertControllerStyle.Alert);
            okCancelAlertController.AddAction(UIAlertAction.Create("Use unsecured", UIAlertActionStyle.Default, alert => PerformSegue("AuthenticationSegue", this)));
            okCancelAlertController.AddAction(UIAlertAction.Create("Cancel", UIAlertActionStyle.Cancel, alert => Console.WriteLine("Cancel was clicked")));
            PresentViewController(okCancelAlertController, true, null);
        }
    } 
}
```

會呼叫 `AuthenticateMe` 方法，以回應使用者點擊**登**入按鈕。 會具現化新的 `LAContext` 物件，並檢查裝置版本，以決定要在本機驗證內容上設定的屬性。

系統會呼叫 `CanEvaluatePolicy` 方法來檢查是否已啟用生物識別驗證，並盡可能切換回 PIN 驗證，如果沒有可用的驗證，最後會提供不安全的模式。 如果有可用的驗證方法，則會使用 `EvaluatePolicy` 方法來顯示 UI，並完成驗證程式。

範例專案包含模擬資料，以及在驗證成功時顯示資料的視圖。

## <a name="related-links"></a>相關連結

- [使用 Touch ID 或臉部識別碼範例進行本機驗證](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-faceidsample/)
- 關於 support.apple.com 上的[TOUCH ID](https://support.apple.com/en-us/HT204587)
- 關於 support.apple.com 上的[臉部識別碼](https://support.apple.com/en-us/HT208108)
