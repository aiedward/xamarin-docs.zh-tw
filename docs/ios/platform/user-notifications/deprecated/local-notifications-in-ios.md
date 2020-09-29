---
title: Xamarin 中的通知
description: 本節說明如何在 Xamarin 中執行本機通知。 它將說明 iOS 通知的各種 UI 元素，並討論與建立和顯示通知相關的 API。
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: f99776a0e6a84c193362d0e5a4efb68e70717ad0
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436117"
---
# <a name="notifications-in-xamarinios"></a>Xamarin 中的通知

> [!IMPORTANT]
> 本節中的資訊適用于 iOS 9 和更早版本。 若為 iOS 10 和更新版本，請參閱 [使用者通知架構指南](~/ios/platform/user-notifications/index.md)。

iOS 有三種方式向使用者指出已收到通知：

- **音效或震動** -iOS 可以播放音效來通知使用者。 如果停用音效，則可以將裝置設定為震動。
- **警示** -您可以在畫面上顯示對話方塊，其中包含通知的相關資訊。
- **徽章** -發佈通知時， (徽章) 應用程式圖示上會顯示數位。

iOS 也提供 *通知中心* ，將所有通知（本機和遠端）顯示給使用者。 使用者可以從畫面頂端向下輕量來存取此內容：

![通知中心](local-notifications-in-ios-images/image13.png "通知中心")

## <a name="creating-local-notifications-in-ios"></a>在 iOS 中建立本機通知

iOS 可讓您建立及處理本機通知相當簡單。
首先，iOS 8 要求應用程式要求使用者顯示通知的許可權。 將下列程式碼新增至您的應用程式，然後再嘗試傳送本機通知- [附加的範例](/samples/xamarin/ios-samples/localnotifications) 會將它放在 **AppDelegate**的 **FinishedLaunching** 方法中。

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![確認傳送本機通知的能力](local-notifications-in-ios-images/image0-sml.png "確認傳送本機通知的能力")](local-notifications-in-ios-images/image0.png#lightbox)

若要排程本機通知，請建立 `UILocalNotification` 物件、設定 `FireDate` ，然後透過 `ScheduleLocalNotification` 物件上的方法來排程它 `UIApplication.SharedApplication` 。 下列程式碼片段示範如何排程未來將引發一分鐘的通知，並顯示包含下列訊息的警示：

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

下列螢幕擷取畫面顯示此警示看起來的樣子：

[![範例警示](local-notifications-in-ios-images/image2-sml.png)](local-notifications-in-ios-images/image2.png#lightbox)

請注意，如果使用者選擇 *不允許* 通知，就不會顯示任何內容。

如果您想要使用數位將徽章套用至應用程式圖示，您可以設定它，如下列程式碼所示：

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

若要以圖示播放音效，請在通知上設定 SoundName 屬性，如下列程式碼片段所示：

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

如果通知音效超過30秒，則 iOS 會改為播放預設音效。

> [!IMPORTANT]
> IOS 模擬器中有一個 bug 會引發委派通知兩次。 在裝置上執行應用程式時，應該不會發生此問題。

## <a name="handling-notifications"></a>處理通知

iOS 應用程式以幾乎完全相同的方式處理遠端和本機通知。 當應用程式正在執行時， `ReceivedLocalNotification` `ReceivedRemoteNotification` 將會呼叫類別上的方法或方法 `AppDelegate` ，並將通知資訊傳遞為參數。

應用程式可以使用不同的方式來處理通知。 例如，應用程式可能只會顯示警示來提醒使用者某個事件。 或者，您也可以使用通知，向使用者顯示一個進程已完成的警示，例如將檔案同步處理到伺服器。

下列程式碼顯示如何處理本機通知並顯示警示，並將徽章號碼重設為零：

```csharp
public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
{
    // show an alert
    UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
    okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

    Window.RootViewController.PresentViewController(okayAlertController, true, null);

    // reset our badge
    UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
}
```

如果應用程式不在執行中，iOS 會播放音效及/或更新圖示徽章（適用時）。 當使用者啟動與警示相關聯的應用程式時，應用程式就會啟動，而且會 `FinishedLaunching` 呼叫應用程式委派上的方法，並透過參數傳入通知資訊 `launchOptions` 。 如果選項字典包含金鑰，則 `UIApplication.LaunchOptionsLocalNotificationKey` `AppDelegate` 會知道應用程式是從本機通知啟動的。 下列程式碼片段示範此流程：

```csharp
// check for a local notification
if (launchOptions.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
{
    var localNotification = launchOptions[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
    if (localNotification != null)
    {
        UIAlertController okayAlertController = UIAlertController.Create(localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

        Window.RootViewController.PresentViewController(okayAlertController, true, null);

        // reset our badge
        UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
    }
}
```

若為遠端通知， `launchOptions` 將會有 `LaunchOptionsRemoteNotificationKey` 與相關聯的， `NSDictionary` 其中包含遠端通知承載。 您可以透過 `alert` 、和金鑰來解壓縮通知承載 `badge` `sound` 。 下列程式碼片段說明如何取得遠端通知：

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>摘要

本節說明如何在 Xamarin 中建立併發布通知。 它會顯示應用程式如何藉由覆寫中的 `ReceivedLocalNotification` 方法或方法來回應通知 `ReceivedRemoteNotification` `AppDelegate` 。

## <a name="related-links"></a>相關連結

- [本機通知 (範例) ](/samples/xamarin/ios-samples/localnotifications)
- [開發人員的本機和推播通知](https://developer.apple.com/notifications/)
- [本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](/dotnet/api/uikit.uiapplication)
- [UILocalNotification](/dotnet/api/uikit.UILocalNotification)