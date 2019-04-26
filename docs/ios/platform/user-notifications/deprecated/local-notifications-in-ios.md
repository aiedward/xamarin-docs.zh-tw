---
title: 在 Xamarin.iOS 中的通知
description: 本節說明如何在 Xamarin.iOS 中實作本機通知。 它會說明 iOS 通知的各種 UI 項目，並討論 API 的涉及建立和顯示通知。
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: f31490a683adfb46c609f14adf08b68de0abc375
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61082970"
---
# <a name="notifications-in-xamarinios"></a>在 Xamarin.iOS 中的通知

> [!IMPORTANT]
> 在本節中的此資訊適用於 iOS 9 和之前。 適用於 iOS 10 及更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)。

iOS 有三種方式可向使用者指示已經收到通知：

- **音效或震動**-iOS 可以播放聲音來通知使用者。 如果已停用聲音，裝置可以設定成震動。
- **警示**-就能夠顯示螢幕上的通知資訊的對話方塊。
- **徽章**-發行通知時，可以 （具有徽章） 應用程式圖示上顯示數字。

也提供 iOS*通知中心*將顯示所有通知，本機和遠端使用者。 使用者可以將這從螢幕頂端向下撥動，來存取：

![通知中心](local-notifications-in-ios-images/image13.png "通知中心")

## <a name="creating-local-notifications-in-ios"></a>在 iOS 中建立本機通知

iOS 可讓您建立和處理本機通知相當簡單。
首先，iOS 8 需要向使用者顯示通知的權限的應用程式。 下列程式碼加入您的應用程式，然後再嘗試傳送本機通知-[附加範例](https://developer.xamarin.com/samples/monotouch/LocalNotifications/)將它置於**AppDelegate**的**FinishedLaunching**方法。

```csharp
var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes(
    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
);
application.RegisterUserNotificationSettings(notificationSettings);
```

[![確認傳送本機通知的能力](local-notifications-in-ios-images/image0-sml.png "確認傳送本機通知的能力")](local-notifications-in-ios-images/image0.png#lightbox)

若要排程本機通知，請建立`UILocalNotification`物件，設定`FireDate`，並將它透過排程`ScheduleLocalNotification`方法`UIApplication.SharedApplication`物件。 下列程式碼片段示範如何排程通知，將會引發一分鐘在未來，並顯示一則訊息的警示：

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

下列螢幕擷取畫面顯示此警示的樣子：

[![](local-notifications-in-ios-images/image2-sml.png "範例警示")](local-notifications-in-ios-images/image2.png#lightbox)

請注意，如果使用者選擇要*不允許*將顯示通知，然後執行任何動作。

如果您想要套用至應用程式圖示的徽章，以數字，您可以設定它的下一行程式碼所示：

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

在順序播放圖示、 音效設定 SoundName 屬性的通知，如下列程式碼片段所示：

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

如果超過 30 秒通知音效，iOS 會播放預設聲音改為。

> [!IMPORTANT]
> 有一個錯誤是在 iOS 模擬器會觸發事件的委派通知兩次。 在裝置上執行應用程式時，應該不會發生此問題。

## <a name="handling-notifications"></a>處理通知

iOS 應用程式處理的方式幾乎完全相同的遠端和本機通知。 當應用程式執行時，`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`方法`AppDelegate`會呼叫類別，並將做為參數傳遞的通知資訊。

應用程式可以處理通知，以不同的方式。 比方說，應用程式可能只會顯示警示來提醒使用者有關的某些事件。 或通知可能會用來顯示警示的程序已完成，例如將檔案伺服器的使用者。

下列程式碼顯示如何處理本機通知並顯示警示並徽章編號重設為零：

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

如果未執行應用程式，iOS 會播放聲音和/或更新適用的圖示徽章。 當使用者啟動應用程式與警示相關聯時，會啟動應用程式和`FinishedLaunching`會呼叫應用程式在委派上的方法，並通知資訊會透過傳入`launchOptions`參數。 如果選項字典包含索引鍵`UIApplication.LaunchOptionsLocalNotificationKey`，然後在`AppDelegate`知道應用程式已啟動本機通知。 下列程式碼片段示範此程序：

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

為遠端的通知，請`launchOptions`會有`LaunchOptionsRemoteNotificationKey`以及相關聯`NSDictionary`包含遠端通知裝載。 您可以擷取透過通知承載`alert`， `badge`，和`sound`索引鍵。 下列程式碼片段示範如何取得遠端通知：

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>總結

本節說明如何建立及發佈 Xamarin.iOS 中的通知。 它會顯示如何應用程式可能會通知來回應覆寫`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`方法中的`AppDelegate`。

## <a name="related-links"></a>相關連結

- [本機通知 （範例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本機和推播通知適用於開發人員](https://developer.apple.com/notifications/)
- [本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
