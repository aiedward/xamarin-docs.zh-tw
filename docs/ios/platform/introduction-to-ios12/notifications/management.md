---
title: Xamarin 中的通知管理
description: 本檔說明如何使用 Xamarin 來利用 iOS 12 中引進的新通知管理功能。
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 9189cfaee9c6cdebc8e269537993bc797509d9a4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435196"
---
# <a name="notification-management-in-xamarinios"></a>Xamarin 中的通知管理

在 iOS 12 中，作業系統可以從通知中心和設定應用程式深層連結至應用程式的通知管理畫面。 此畫面應可讓使用者加入宣告並退出應用程式傳送的各種類型通知。

## <a name="sample-app-redgreennotifications"></a>範例應用程式： RedGreenNotifications

若要查看通知管理如何運作的範例，請查看 [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) 範例應用程式。

此範例應用程式會傳送兩種類型的通知（紅色和綠色），並提供可讓使用者加入宣告或退出任一個類型的畫面。

本指南中的程式碼片段來自此範例應用程式。

## <a name="notification-management-screen"></a>通知管理畫面

在範例應用程式中， `ManageNotificationsViewController` 會定義使用者介面，以允許使用者獨立啟用和停用紅色通知和綠色通知。 它是標準的 [`UIViewController`](xref:UIKit.UIViewController)
包含 [`UISwitch`](xref:UIKit.UISwitch) 每個通知類型的。 切換開關的其中一種類型的通知會儲存，使用者預設值是該類型通知的使用者喜好設定：

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> 通知管理畫面也會檢查使用者是否已完全停用應用程式的通知。 如果是，則會隱藏個別通知類型的切換。 若要這樣做，通知管理畫面：
>
> - 呼叫 [`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) 並檢查 [`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus) 屬性。
> - 如果應用程式已完全停用通知，則隱藏個別通知類型的切換。
> - 重新檢查每次應用程式移至前景時是否已停用通知，因為使用者可以隨時啟用/停用 iOS 設定中的通知。

範例應用程式的 `ViewController` 類別會傳送通知，先檢查使用者的喜好設定再傳送本機通知，以確保通知的類型是使用者實際想要接收的類型：

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>深層連結

iOS 深層連結至應用程式的通知管理畫面，從通知中心和應用程式在 [設定] 應用程式的通知設定。 為了加速此情況，應用程式必須：

- 藉由傳遞 `UNAuthorizationOptions.ProvidesAppNotificationSettings` 至應用程式的通知授權要求，表示有通知管理畫面可供使用。
- 從執行 `OpenSettings` 方法 [`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate) 。

### <a name="authorization-request"></a>授權要求

若要向作業系統指出通知管理畫面可供使用，應用程式應該將 `UNAuthorizationOptions.ProvidesAppNotificationSettings` 選項 (連同任何其他需要) 至方法的通知傳遞選項一起傳遞 `RequestAuthorization` `UNUserNotificationCenter` 。

例如，在範例應用程式中 `AppDelegate` ：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    // Request authorization to send notifications
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.ProvidesAppNotificationSettings | UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) =>
    {
        // ...
```

### <a name="opensettings-method"></a>OpenSettings 方法

`OpenSettings`系統會呼叫此方法以深層連結至應用程式的通知管理畫面，應該直接將使用者流覽至該畫面。

在範例應用程式中，如果有必要，這個方法會執行 segue 至 `ManageNotificationsViewController` ：

```csharp
[Export("userNotificationCenter:openSettingsForNotification:")]
public void OpenSettings(UNUserNotificationCenter center, UNNotification notification)
{
    var navigationController = Window.RootViewController as UINavigationController;
    if (navigationController != null)
    {
        var currentViewController = navigationController.VisibleViewController;
        if (currentViewController is ViewController)
        {
            currentViewController.PerformSegue(ManageNotificationsViewController.ShowManageNotificationsSegue, this);
        }

    }
}
```

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017) ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 產生遠端通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)