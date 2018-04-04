---
title: Xamarin.iOS 中的通知
description: 本節說明如何實作 Xamarin.iOS 在本機的通知。 它會說明 iOS 通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。
ms.prod: xamarin
ms.assetid: 5BB76915-5DB0-48C7-A267-FA9F7C50793E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: d4dd759f52e52f417441f69e6417fab536daf6d3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="notifications-in-xamarinios"></a>Xamarin.iOS 中的通知

_本節說明如何實作 Xamarin.iOS 在本機的通知。它會說明 iOS 通知的各種 UI 項目，並討論應用程式開發介面的涉及建立和顯示通知。_

> [!IMPORTANT]
> 本節中的資訊屬於 iOS 9 及之前，它已保留這裡以支援較舊 iOS 版本。 適用於 iOS 10 和更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)支援 iOS 裝置上的本機和遠端的通知。

iOS 有三種方式可向使用者指示已經收到通知：

-  **音效或震動**-iOS 可以播放的音效來通知使用者。 如果已停用聲音，可以設定裝置震動。
-  **警示**-可以與通知有關的資訊在螢幕上顯示的對話方塊。
-  **徽章**-發行通知時，可以 （具有徽章） 應用程式圖示上顯示數字。


也提供 iOS*通知中心*將顯示所有通知，本機和遠端使用者。 使用者可以存取這撥動往下從畫面頂端：

 ![](local-notifications-in-ios-images/image13.png "通知中心")

## <a name="creating-local-notifications-in-ios"></a>在 iOS 中建立本機的通知

iOS 可讓您建立和處理本機通知相當簡單。
首先，iOS 8 需要要求的使用者的權限顯示通知的應用程式。 加入下列程式碼嘗試將通知傳送到本機應用程式-附加的範例將它放入**AppDelegate**的**FinishedLaunching**方法。

```csharp
var settings = UIUserNotificationSettings.GetSettingsForTypes(
  UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound
  , null);
UIApplication.SharedApplication.RegisterUserNotificationSettings (settings);
```

  [![](local-notifications-in-ios-images/image0-sml.png "確認傳送本機通知的能力")](local-notifications-in-ios-images/image0.png#lightbox)

若要排定您建立本機通知`UILocalNotification`物件、 設定`FireDate`，並排定它透過`ScheduleLocalNotification`方法`UIApplication.SharedApplication`物件。 下列程式碼片段將示範如何排程將會引發一分鐘以後，並顯示警示訊息的通知：

```csharp
UILocalNotification notification = new UILocalNotification();
NSDate.FromTimeIntervalSinceNow(15);
//notification.AlertTitle = "Alert Title"; // required for Apple Watch notifications
notification.AlertAction = "View Alert";
notification.AlertBody = "Your 15 second alert has fired!";
UIApplication.SharedApplication.ScheduleLocalNotification(notification);
```

下列螢幕擷取畫面顯示此警示的外觀：

  [![](local-notifications-in-ios-images/image2-sml.png "範例警示")](local-notifications-in-ios-images/image2.png#lightbox)

請注意，如果使用者選擇要*不允許*將顯示通知，然後執行任何動作。

如果您想要套用至應用程式圖示的徽章以數字，您可以設定下列一行程式碼所示：

```csharp
notification.ApplicationIconBadgeNumber = 1;
```

在順序播放聲音圖示，以設定 SoundName 屬性的通知，如下列程式碼片段所示：

```csharp
notification.SoundName = UILocalNotification.DefaultSoundName;
```

每個 Apple 人性化介面指導方針，通知會播放音效，如果它應該也伴隨著徽章或警示可協助使用者識別產生警示的應用程式。 此外，如果超過 30 秒鐘聲音，iOS 會播放預設聲音改為。

> [!IMPORTANT]
> 沒有會引發兩次的委派通知的 iOS 模擬器中的 bug。 在裝置上執行應用程式時，應該不會發生此問題。

## <a name="handling-notifications"></a>處理告知

iOS 應用程式會處理遠端和本機通知幾乎完全相同的方式。 當執行應用程式時，`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`AppDelegate 類別上的方法會呼叫，並且會做為參數傳遞的通知資訊。

應用程式可以處理通知以不同的方式。 比方說，應用程式可能只會顯示警示提醒使用者有關某些事件。 或通知可能會用來顯示警示的處理序已完成，例如正在同步處理的檔案伺服器的使用者。

下列程式碼會示範如何處理本機通知以及顯示警示和重設為零的徽章數字：

```csharp
 public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
        {
            // show an alert
            UIAlertController okayAlertController = UIAlertController.Create (notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
            okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
            viewController.PresentViewController (okayAlertController, true, null);

            // reset our badge
            UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
        }
```

如果未執行應用程式，iOS 會播放聲音和/或圖示徽章適用的更新。 當使用者開始與警示相關聯的應用程式時，應用程式會啟動將呼叫應用程式在委派上的 FinishedLaunching 方法和中透過 options 參數傳遞的通知資訊。 如果選項字典中包含索引鍵`UIApplication.LaunchOptionsLocalNotificationKey`，就會 AppDelegate 知道應用程式已啟動本機的通知。 下列程式碼片段會示範此程序：

```csharp
// check for a local notification
if (options.ContainsKey(UIApplication.LaunchOptionsLocalNotificationKey))
 {
     var localNotification = options[UIApplication.LaunchOptionsLocalNotificationKey] as UILocalNotification;
     if (localNotification != null)
     {
        UIAlertController okayAlertController = UIAlertController.Create (localNotification.AlertAction, localNotification.AlertBody, UIAlertControllerStyle.Alert);
        okayAlertController.AddAction (UIAlertAction.Create ("OK", UIAlertActionStyle.Default, null));
        viewController.PresentViewController (okayAlertController, true, null);

         // reset our badge
         UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
     }
 }
```

如果它是遠端通知選項字典會改為包含`LaunchOptionsRemoteNotificationKey`，而且該金鑰的產生的值會`NSDictionary`遠端通知裝載的物件。 您可以擷取通知裝載透過警示、 徽章和聲音的索引鍵。 下列程式碼片段示範如何取得遠端通知：

```csharp
NSDictionary remoteNotification = options[UIApplication.LaunchOptionsRemoteNotificationKey];
if(remoteNotification != null)
{
    string alert = remoteNotification["alert"];
}
```

## <a name="summary"></a>總結

本節說明如何建立及發行通知 Xamarin.iOS 中。 它顯示如何應用程式可能會回應通知藉由覆寫`ReceivedLocalNotification`方法或`ReceivedRemoteNotification`方法中的`AppDelegate`。


## <a name="related-links"></a>相關連結

- [本機通知 （範例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本機和開發人員的推播通知](https://developer.apple.com/notifications/)
- [本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UILocalNotification](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UILocalNotification)
