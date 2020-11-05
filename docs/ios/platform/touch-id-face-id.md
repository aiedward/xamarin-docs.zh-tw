---
title: 使用 Xamarin 的 Touch ID 和臉部識別碼
description: 本檔提供 iOS 中生物特徵辨識驗證的高階描述。
ms.prod: xamarin
ms.assetid: 4BC8EFD6-52FC-4793-BA69-D6BFF850FE5F
ms.technology: xamarin-ios
author: profexorgeek
ms.author: jusjohns
ms.date: 12/16/2019
ms.openlocfilehash: a092b5f84ebf0481652f5093a4898e44a55e19f8
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369554"
---
# <a name="use-touch-id-and-face-id-with-xamarinios"></a>搭配使用 Touch ID 和臉部識別碼與 Xamarin

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/ios-samples/ios11-faceidsample/)

iOS 支援兩種生物特徵辨識驗證系統：

1. **TOUCH ID** 使用 [首頁] 按鈕下的指紋感應器。
1. **臉部識別碼** 使用正面相機感應器來驗證具有臉部掃描的使用者。

Touch ID 是在 iOS 7 和 iOS 11 中的臉部識別碼引進。

這些驗證系統依賴以硬體為基礎的安全性處理器，稱為「 _安全記憶體保護區_ 」。 安全記憶體保護區負責加密臉部和指紋資料的數學標記法，並使用這項資訊來驗證使用者。 根據 Apple，臉部和指紋資料不會離開裝置，也不會備份到 iCloud。 應用程式會透過 _本機驗證_ API 與安全記憶體保護區互動，而且無法取出臉部或指紋資料，或直接存取安全的記憶體保護區。

在提供受保護內容的存取權之前，應用程式可以使用 Touch ID 和臉部識別碼來驗證使用者。

## <a name="local-authentication-context"></a>本機驗證內容

IOS 上的生物特徵辨識驗證依賴 _本機驗證內容_ 物件，也就是類別的實例 `LAContext` 。 `LAContext`類別可讓您：

- 檢查生物特徵辨識硬體的可用性。
- 評估驗證原則。
- 評估存取控制。
- 自訂和顯示驗證提示。
- 重複使用或使驗證狀態失效。
- 管理認證。

## <a name="detect-available-authentication-methods"></a>偵測可用的驗證方法

範例專案包含的 `AuthenticationView` 支援 `AuthenticationViewController` 。 這個類別會覆寫 `ViewWillAppear` 方法來偵測可用的驗證方法：

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

`ViewWillAppear`當 UI 即將顯示給使用者時，會呼叫方法。 這個方法會定義的新實例 `LAContext` ，並使用 `CanEvaluatePolicy` 方法來判斷是否已啟用生物識別驗證。 如果是的話，它會檢查系統版本和 `BiometryType` 列舉，判斷有哪些生物特徵辨識選項可供使用。

如果未啟用生物識別驗證，應用程式會嘗試切換回 PIN 碼驗證。 如果沒有生物特徵辨識或 PIN 驗證，則裝置擁有者未啟用安全性功能，而且內容無法透過本機驗證來保護。

## <a name="authenticate-a-user"></a>驗證使用者

`AuthenticationViewController`範例專案中的 `AuthenticateMe` 會包含負責驗證使用者的方法：

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

`AuthenticateMe`呼叫方法是為了回應使用者點擊 **登** 入按鈕。 會具 `LAContext` 現化新的物件，並檢查裝置版本以判斷要在本機驗證內容上設定的屬性。

`CanEvaluatePolicy`系統會呼叫此方法以檢查是否已啟用生物識別驗證、切換回 PIN 驗證（如果可能的話），最後提供不安全的模式（如果沒有可用的驗證）。 如果有可用的驗證方法，則 `EvaluatePolicy` 會使用方法來顯示 UI 並完成驗證程式。

範例專案包含 mock 資料，以及在驗證成功時顯示資料的視圖。

## <a name="related-links"></a>相關連結

- [使用 Touch ID 或臉部識別碼的本機驗證範例](/samples/xamarin/ios-samples/ios11-faceidsample/)
- 關於 support.apple.com 上的[TOUCH ID](https://support.apple.com/en-us/HT204587)
- 關於 support.apple.com 上的[臉部識別碼](https://support.apple.com/en-us/HT208108)