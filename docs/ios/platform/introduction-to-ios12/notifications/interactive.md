---
title: Xamarin 中的互動式通知使用者介面
description: 使用 iOS 12 時, 您可以建立互動式使用者介面來進行本機和遠端通知。 本指南說明如何搭配使用這些功能與 Xamarin。
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: bc566cf3744b8d6ec05204153b7c731935f98b8a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652444"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>Xamarin 中的互動式通知使用者介面

IOS 10 引進的[通知內容延伸](~/ios/platform/user-notifications/advanced-user-notifications.md)模組, 可讓您建立自訂的使用者介面來取得通知。 從 iOS 12 開始, 通知使用者介面可以包含互動式元素, 例如按鈕和滑杆。

## <a name="sample-app-redgreennotifications"></a>範例應用程式:RedGreenNotifications

[RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)範例應用程式包含具有互動式使用者介面的通知內容延伸模組。

本指南中的程式碼片段來自此範例。

## <a name="notification-content-extension-infoplist-file"></a>通知內容延伸模組資訊. plist 檔案

在範例應用程式中, **RedGreenNotificationsContentExtension**專案中的**plist**檔案包含下列設定:

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

請注意下列功能:

- `UNNotificationExtensionCategory`陣列會指定內容延伸模組所處理的通知類別目錄類型。
- 為了支援互動式內容, 通知內容延伸會將`UNNotificationExtensionUserInteractionEnabled`金鑰設定為。 `true`
- `UNNotificationExtensionInitialContentSizeRatio`金鑰會指定內容延伸模組介面的初始高度/寬度比例。

## <a name="interactive-interface"></a>互動式介面

定義通知內容延伸模組之介面的**MainInterface**, 是包含單一 view 控制器的標準分鏡腳本。 在範例應用程式中, view 控制器的類型`NotificationViewController`為, 其中包含影像視圖、三個按鈕和一個滑杆。 此分鏡腳本會將這些控制項與**NotificationViewController.cs**中定義的處理常式產生關聯:

- [**啟動應用程式**] 按鈕處理`PerformNotificationDefaultAction`程式會呼叫`ExtensionContext`上的動作方法, 以啟動應用程式:

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    在應用程式中, 使用者通知中心的`Delegate` (在範例應用程式`AppDelegate`中) 可以回應`DidReceiveNotificationResponse`方法中的互動:

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- 關閉**通知**按鈕處理常式會`DismissNotificationContentExtension`在`ExtensionContext`上呼叫, 以關閉通知:

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- [**移除通知**] 按鈕處理常式會關閉通知, 並將其從 [通知中心] 移除:

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- 處理滑杆上值變更的方法會更新通知介面中所顯示影像的 Alpha:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [豐富通知 (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
