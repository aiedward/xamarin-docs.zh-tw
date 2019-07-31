---
title: Xamarin 中的通知管理
description: 本檔說明如何使用 Xamarin 來利用 iOS 12 中引進的新通知管理功能。
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 69b6876a22e511d1c14a795d7b81c3a638492468
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652392"
---
# <a name="notification-management-in-xamarinios"></a>Xamarin 中的通知管理

在 iOS 12 中, 作業系統可以從 [通知中心] 和 [設定] 應用程式深入連結至應用程式的 [通知管理] 畫面。 此畫面可讓使用者加入宣告或退出應用程式所傳送的各種類型通知。

## <a name="sample-app-redgreennotifications"></a>範例應用程式:RedGreenNotifications

若要查看通知管理如何運作的範例, 請查看[RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)範例應用程式。

這個範例應用程式會傳送兩種類型的通知–紅色和綠色–並提供螢幕, 讓使用者加入宣告或退出任一種。

本指南中的程式碼片段來自此範例應用程式。

## <a name="notification-management-screen"></a>通知管理畫面

在範例應用程式中`ManageNotificationsViewController` , 會定義使用者介面, 讓使用者可以獨立啟用和停用紅色通知和綠色通知。 這是標準[`UIViewController`](xref:UIKit.UIViewController)
[`UISwitch`](xref:UIKit.UISwitch)包含每個通知類型的。 切換任何一種通知類型的參數, 在使用者預設的情況下, 使用者對於該類型通知的喜好設定:

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> [通知管理] 畫面也會檢查使用者是否已完全停用應用程式的通知。 若是如此, 它就會隱藏個別通知類型的切換。 若要這麼做, 請在 [通知管理] 畫面中:
>
> - 呼叫[`UNUserNotificationCenter.Current.GetNotificationSettingsAsync`](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) [並`AuthorizationStatus`](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus)檢查屬性。
> - 如果應用程式已完全停用通知, 則隱藏個別通知類型的切換。
> - 重新檢查每次應用程式移至前景時是否已停用通知, 因為使用者可以隨時在 iOS 設定中啟用/停用通知。

範例應用程式的`ViewController`類別 (會傳送通知) 會先檢查使用者的喜好設定, 然後再傳送本機通知, 以確保通知是使用者實際想要接收的類型:

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>深層連結

iOS 深層連結: 來自通知中心的代理程式更新管理畫面, 以及 [設定] 應用程式的 [通知] 設定。 為了加速此動作, 應用程式必須:

- 藉由傳遞`UNAuthorizationOptions.ProvidesAppNotificationSettings`至應用程式的通知授權要求, 表示有可用的通知管理畫面。
- `OpenSettings` [從`IUNUserNotificationCenterDelegate`](xref:UserNotifications.IUNUserNotificationCenterDelegate)執行方法。

### <a name="authorization-request"></a>授權要求

若要向作業系統指出有可用的通知管理畫面, 應用程式應該將`UNAuthorizationOptions.ProvidesAppNotificationSettings`選項 (以及所需的任何其他通知傳遞選項) 傳遞至上`UNUserNotificationCenter`的`RequestAuthorization`方法。

例如, 在範例應用程式`AppDelegate`中:

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

系統呼叫以深入連結代理程式更新管理畫面的方法,應直接將使用者流覽至該畫面。`OpenSettings`

在範例應用程式中, `ManageNotificationsViewController`如果需要, 這個方法會執行 segue 至。

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

- [範例應用程式-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
