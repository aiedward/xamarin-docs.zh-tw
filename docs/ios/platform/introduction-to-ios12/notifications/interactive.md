---
title: 在 Xamarin 中的互動式通知使用者介面
description: 使用 iOS 12，可以建立本機和遠端通知的互動式使用者介面。 本指南說明如何搭配使用這些功能與 Xamarin。
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: af06aa4dc7ea836887edcc0416cfd15ce3aa1446
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433772"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>在 Xamarin 中的互動式通知使用者介面

IOS 10 引進的[通知內容延伸](~/ios/platform/user-notifications/advanced-user-notifications.md)模組可讓您建立通知的自訂使用者介面。 從 iOS 12 開始，通知使用者介面可包含互動式元素，例如按鈕和滑杆。

## <a name="sample-app-redgreennotifications"></a>範例應用程式： RedGreenNotifications

[RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)範例應用程式包含具有互動式使用者介面的通知內容延伸模組。

本指南中的程式碼片段來自此範例。

## <a name="notification-content-extension-infoplist-file"></a>通知內容延伸模組資訊 plist 檔案

在範例應用程式中， **RedGreenNotificationsContentExtension**專案中的**plist**檔案包含下列設定：

```xml
<!-- ... -->
<key>NSExtension</key>
<dict>
    <key>NSExtensionAttributes</key>
    <dict>
        <key>UNNotificationExtensionCategory</key>
        <array>
            <string>red-category</string>
            <string>green-category</string>
        </array>
        <key>UNNotificationExtensionUserInteractionEnabled</key>
        <true/>
        <key>UNNotificationExtensionDefaultContentHidden</key>
        <true/>
        <key>UNNotificationExtensionInitialContentSizeRatio</key>
        <real>0.6</real>
    </dict>
    <key>NSExtensionMainStoryboard</key>
    <string>MainInterface</string>
    <key>NSExtensionPointIdentifier</key>
    <string>com.apple.usernotifications.content-extension</string>
    <key></key>
    <true/>
</dict>
<!-- ... -->
```

請注意下列功能：

- `UNNotificationExtensionCategory`陣列會指定內容延伸模組所處理的通知類別目錄類型。
- 為了支援互動式內容，通知內容延伸會將 `UNNotificationExtensionUserInteractionEnabled` 金鑰設定為 `true` 。
- `UNNotificationExtensionInitialContentSizeRatio`金鑰會指定內容延伸模組介面的初始高度/寬度比例。

## <a name="interactive-interface"></a>互動式介面

**MainInterface**會定義通知內容延伸模組的介面，這是包含單一視圖控制器的標準分鏡腳本。 在範例應用程式中，視圖控制器的類型為 `NotificationViewController` ，且包含影像視圖、三個按鈕和一個滑杆。 腳本會將這些控制項與 **NotificationViewController.cs**中定義的處理常式產生關聯：

- **啟動應用程式**按鈕處理常式會 `PerformNotificationDefaultAction` 在上呼叫動作方法 `ExtensionContext` ，以啟動應用程式：

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    在應用程式中，使用者通知中心在 `Delegate` 範例應用程式中的 (， `AppDelegate`) 可回應方法中的互動   `DidReceiveNotificationResponse` ：

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- 關閉 **通知** 按鈕處理常式會 `DismissNotificationContentExtension` 在上呼叫 `ExtensionContext` ，以關閉通知：

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- **移除**通知按鈕處理常式會關閉通知，並將其從通知中心移除：

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- 處理滑杆上值變更的方法會更新通知介面中顯示之影像的 Alpha：

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017) ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [豐富通知 (WWDC 2017) ](https://developer.apple.com/videos/play/wwdc2017/817/)
- [ (Apple) 產生遠端通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)