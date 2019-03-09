---
title: 在 Xamarin.iOS 中的互動式的通知使用者介面
description: 使用 iOS 12，就可以建立本機及遠端通知的互動式使用者介面。 本指南說明如何使用 Xamarin.iOS 中使用這些功能。
ms.prod: xamarin
ms.assetid: E3562E1B-E0EF-4C99-9F51-59DE22AFDE46
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: e6dc2f14b36c9d6f67f1df5ad3d118fa423e0d4d
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669388"
---
# <a name="interactive-notification-user-interfaces-in-xamarinios"></a>在 Xamarin.iOS 中的互動式的通知使用者介面

[通知內容延伸模組](~/ios/platform/user-notifications/advanced-user-notifications.md)，iOS 10 中引進，使您能夠建立通知的自訂使用者介面。 從 iOS 12，通知使用者介面可以包含互動式元素，例如按鈕和滑桿。

## <a name="sample-app-redgreennotifications"></a>範例應用程式：RedGreenNotifications

[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)範例應用程式包含互動式使用者介面的 notification content 延伸模組。

本指南中的程式碼片段來自此範例。

## <a name="notification-content-extension-infoplist-file"></a>通知內容延伸模組 Info.plist 檔案

範例應用程式中**Info.plist**中的檔案**RedGreenNotificationsContentExtension**專案包含下列組態：

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

- `UNNotificationExtensionCategory`陣列指定類型的通知類別內容的延伸模組控制代碼。
- 若要支援互動式內容，設定 notification content 延伸模組`UNNotificationExtensionUserInteractionEnabled`機碼`true`。
- `UNNotificationExtensionInitialContentSizeRatio`索引鍵會指定內容的延伸模組介面的初始高度/寬度比例。

## <a name="interactive-interface"></a>互動式介面

**MainInterface.storyboard**，其定義的介面 notification content 延伸模組，是標準的分鏡腳本包含一個單一檢視控制器。 範例應用程式中檢視控制器是型別的`NotificationViewController`，，其中包含映像檢視中，三個按鈕和滑桿。 分鏡腳本將這些控制項關聯中所定義的處理常式**NotificationViewController.cs**:

- **啟動應用程式**按鈕處理常式會呼叫`PerformNotificationDefaultAction`上的動作方法`ExtensionContext`，這樣會啟動應用程式：

    ```csharp
    partial void HandleLaunchAppButtonTap(UIButton sender)
    {
        ExtensionContext.PerformNotificationDefaultAction();
    }
    ```

    在應用程式，使用者通知中心的`Delegate`(在範例應用程式中， `AppDelegate`) 可以回應中互動`DidReceiveNotificationResponse`方法：

    ```csharp
    [Export("userNotificationCenter:didReceiveNotificationResponse:withCompletionHandler:")]
    public void DidReceiveNotificationResponse(UNUserNotificationCenter center, UNNotificationResponse response, System.Action completionHandler)
    {
        if (response.IsDefaultAction)
        {
            Console.WriteLine("ACTION: Default");
            // ...
    ```

- **關閉通知**按鈕處理常式會呼叫`DismissNotificationContentExtension`上`ExtensionContext`，這會關閉通知：

    ```csharp
    partial void HandleDismissNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
    }
    ```

- **移除通知**按鈕處理常式關閉通知，並移除通知中心：

    ```csharp
    partial void HandleRemoveNotificationButtonTap(UIButton sender)
    {
        ExtensionContext.DismissNotificationContentExtension();
        UNUserNotificationCenter.Current.RemoveDeliveredNotifications(new string[] { notification.Request.Identifier });
    }
    ```

- 處理上滑桿的值變更的方法更新通知的介面中顯示的影像 alpha:

    ```csharp
    partial void HandleSliderValueChanged(UISlider sender)
    {
        Xamagon.Alpha = sender.Value;
    }
    ```

## <a name="related-links"></a>相關連結

- [範例應用程式 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [在 使用者通知 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知 (WWDC 2017) 在最新消息](https://developer.apple.com/videos/play/wwdc2017/708/)
- [豐富的通知 (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/817/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
