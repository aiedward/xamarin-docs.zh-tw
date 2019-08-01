---
title: 在 Xamarin 中的通知
description: 本節說明如何在 Xamarin 中執行本機通知。 其中將說明 iOS 通知的各種 UI 元素, 並討論與建立和顯示通知相關的 API。
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 7f2619010a410cabc54074e669ff4f1ea24bd0fa
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655497"
---
# <a name="notifications-in-xamarinios"></a>在 Xamarin 中的通知

> [!IMPORTANT]
> 本節中的資訊適用于 iOS 9 和之前的版本。 若為 iOS 10 及更新版本, 請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)。

iOS 有三種方式可向使用者指出已收到通知:

- **音效或震動**-iOS 可以播放音效來通知使用者。 如果音效已停用, 則可以將裝置設定為震動。
- **警示**-可以在畫面上顯示對話方塊, 其中包含通知的相關資訊。
- **徽章**-發佈通知時, 可以在應用程式圖示上顯示數位 (徽章)。

iOS 也會提供*通知中心*, 以顯示使用者的所有通知 (本機和遠端)。 使用者可以從畫面頂端向下輕量來存取此項:

![通知中心](local-notifications-in-ios-images/image13.png "通知中心")

## <a name="creating-local-notifications-in-ios"></a>在 iOS 中建立本機通知

iOS 讓建立和處理本機通知變得相當簡單。
首先, iOS 8 需要應用程式要求使用者顯示通知的許可權。 在嘗試傳送本機通知之前, 將下列程式碼新增至您的應用程式-[附加的範例](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)會將它放在**AppDelegate**的**FinishedLaunching**方法中。

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![確認傳送本機通知的能力](local-notifications-in-ios-images/image0-sml.png "確認傳送本機通知的能力")](local-notifications-in-ios-images/image0.png#lightbox)

若要排程本機通知, 請建立`UILocalNotification`物件、 `FireDate`設定, 然後透過`UIApplication.SharedApplication`物件上的`ScheduleLocalNotification`方法進行排程。 下列程式碼片段示範如何排程在未來將會引發一分鐘的通知, 並顯示含有訊息的警示:

```csharp
UILocalNotification notification = new UILocalNotification();
notification.FireDate = NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

下列螢幕擷取畫面顯示此警示的樣子:

[![](local-notifications-in-ios-images/image2-sml.png "範例警示")](local-notifications-in-ios-images/image2.png#lightbox)

請注意, 如果使用者選擇*不允許*通知, 則不會顯示任何內容。

如果您想要將徽章套用至具有數位的應用程式圖示, 您可以設定它, 如下列程式碼所示:

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

若要以圖示播放音效, 請在通知上設定 SoundName 屬性, 如下列程式碼片段所示:

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

如果通知音效超過30秒, iOS 將會改為播放預設音效。

> [!IMPORTANT]
> IOS 模擬器中有一個 bug, 會引發委派通知兩次。 在裝置上執行應用程式時, 應該不會發生此問題。

## <a name="handling-notifications"></a>處理通知

iOS 應用程式以幾乎完全相同的方式處理遠端和本機通知。 當應用程式正在執行時, `ReceivedLocalNotification`會呼叫`AppDelegate`類別`ReceivedRemoteNotification`上的方法或方法, 並將通知資訊當做參數傳遞。

應用程式可以用不同的方式處理通知。 例如, 應用程式可能只會顯示警示, 以提醒使用者某個事件的相關資訊。 或者, 可能會使用通知向使用者顯示處理常式已完成的警示, 例如將檔案同步至伺服器。

下列程式碼示範如何處理本機通知和顯示警示, 並將徽章編號重設為零:

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

如果應用程式未執行, iOS 將會播放音效, 並 (或) 將圖示徽章更新為適用的狀態。 當使用者啟動與警示相關聯的應用程式時, 應用程式將會啟動, `FinishedLaunching`並會呼叫 app 委派上的方法, 並透過`launchOptions`參數傳入通知資訊。 如果選項字典包含索引鍵`UIApplication.LaunchOptionsLocalNotificationKey`, `AppDelegate`則會知道應用程式是從本機通知啟動。 下列程式碼片段示範此流程:

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

若為遠端通知, `launchOptions`將會`LaunchOptionsRemoteNotificationKey`具有具有相關聯`NSDictionary`的, 其中包含遠端通知裝載的。 您可以透過`alert`、 `badge`和`sound`金鑰來解壓縮通知承載。 下列程式碼片段顯示如何取得遠端通知:

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>總結

本節說明如何在 Xamarin 中建立和發佈通知。 它會顯示應用程式如何藉由覆寫`ReceivedLocalNotification`中`AppDelegate`的方法`ReceivedRemoteNotification`或方法, 來回應通知。

## <a name="related-links"></a>相關連結

- [本機通知 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/localnotifications)
- [開發人員的本機和推播通知](https://developer.apple.com/notifications/)
- [本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
