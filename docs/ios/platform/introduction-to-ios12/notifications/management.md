---
title: 在 Xamarin.iOS 中的通知管理
description: 本文件說明如何使用 Xamarin.iOS 來利用 iOS 12 中引進的新通知管理功能。
ms.prod: xamarin
ms.assetid: F1D90729-F85A-425B-B633-E2FA38FB4A0C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 96fce269784ad0ac41fd1685ac7ac6b957932bd8
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233168"
---
# <a name="notification-management-in-xamarinios"></a>在 Xamarin.iOS 中的通知管理

在 iOS 12 中，作業系統可以使用通知中心的深層連結和設定應用程式，應用程式的通知管理畫面。 此畫面可讓使用者選擇加入，並從各種類型的通知應用程式傳送。

## <a name="sample-app-redgreennotifications"></a>範例應用程式：RedGreenNotifications

若要查看通知管理的運作方式的範例，看看[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)範例應用程式。

此範例應用程式 – 紅色和綠色 – 傳送兩種類型的通知，並提供可讓使用者選擇參與或退出其中一種螢幕。

本指南中的程式碼片段來自此範例應用程式。

## <a name="notification-management-screen"></a>通知 [管理] 畫面

範例應用程式，`ManageNotificationsViewController`定義使用者介面，可讓使用者獨立啟用和停用紅色的通知和綠色的通知。 它是一種標準 [`UIViewController`](xref:UIKit.UIViewController)
其中包含[ `UISwitch` ](xref:UIKit.UISwitch)每種通知類型。 切換的任一種類型的通知儲存在使用者預設值，該類型的通知使用者的喜好設定：

```csharp
partial void HandleRedNotificationsSwitchValueChange(UISwitch sender)
{
    NSUserDefaults.StandardUserDefaults.SetBool(sender.On, RedNotificationsEnabledKey);
}
```

> [!NOTE]
> 通知的 [管理] 畫面也會檢查已完全停用該使用者的應用程式的通知。 如果是的話，它會隱藏個別的通知類型切換。 若要這樣做，通知的 [管理] 畫面：
>
> - 呼叫[ `UNUserNotificationCenter.Current.GetNotificationSettingsAsync` ](xref:UserNotifications.UNUserNotificationCenter.GetNotificationSettingsAsync) ，並檢查[ `AuthorizationStatus` ](xref:UserNotifications.UNNotificationSettings.AuthorizationStatus)屬性。
> - 如果通知已完全停用應用程式，請隱藏個別的通知類型切換。
> - 重新檢查是否通知已停用每個應用程式移到前景，因為使用者可以啟用/停用通知在 iOS 設定在任何時間的時間。

範例應用程式的`ViewController`傳送的通知，核取的使用者的喜好設定，才能傳送本機通知，以確保通知類型的使用者實際上想要接收的類別：

```csharp
partial void HandleTapRedNotificationButton(UIButton sender)
{
    bool redEnabled = NSUserDefaults.StandardUserDefaults.BoolForKey(ManageNotificationsViewController.RedNotificationsEnabledKey);
    if (redEnabled)
    {
        // ...
```

## <a name="deep-link"></a>深層連結

iOS 深層連結應用程式的通知管理畫面與通知中心設定應用程式中的應用程式的通知設定。 若要達成此目的，應用程式必須：

- 表示通知管理畫面可以藉由傳遞`UNAuthorizationOptions.ProvidesAppNotificationSettings`應用程式的通知授權要求。
- 實作`OpenSettings`方法，從[ `IUNUserNotificationCenterDelegate` ](xref:UserNotifications.IUNUserNotificationCenterDelegate)。

### <a name="authorization-request"></a>授權要求

若要表示作業系統通知管理畫面使用，應用程式應該傳遞`UNAuthorizationOptions.ProvidesAppNotificationSettings`（以及任何其他通知傳遞選項需要） 選項設定為`RequestAuthorization`方法`UNUserNotificationCenter`。

例如，在範例應用程式的`AppDelegate`:

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

`OpenSettings`深層連結應用程式的通知管理畫面中，系統所呼叫的方法應該瀏覽該畫面直接使用者。

範例應用程式，這個方法會執行到的 segue`ManageNotificationsViewController`視：

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

- [範例應用程式 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [在 使用者通知 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知 (WWDC 2017) 在最新消息](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
