---
title: 逐步解說-在 Xamarin.iOS 中使用本機通知
description: 這一節我們將逐步解說如何在 Xamarin.iOS 應用程式中使用本機通知。 它將示範如何建立和發行通知，就會出現警示時收到由應用程式的基本概念。
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cf1e44ba4176922234fc1b6b9bfe5c463611cc7b
ms.sourcegitcommit: 081a2d094774c6f75437d28b71d22607e33aae71
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/03/2018
ms.locfileid: "37403425"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>逐步解說-在 Xamarin.iOS 中使用本機通知

_這一節我們將逐步解說如何在 Xamarin.iOS 應用程式中使用本機通知。它將示範如何建立和發行通知，就會出現警示時收到由應用程式的基本概念。_

> [!IMPORTANT]
> 在本節中的資訊適用於 iOS 9 和之前，它已保留這裡以支援較舊 iOS 版本。 適用於 iOS 10 及更新版本，請參閱[使用者通知架構指南](~/ios/platform/user-notifications/index.md)支援 iOS 裝置上的本機和遠端的通知。

## <a name="walkthrough"></a>逐步解說

可讓建立簡單的應用程式，將會顯示作用中的本機通知。 此應用程式上會有單一的按鈕。 當我們按一下按鈕時，它會建立本機的通知。 指定時間期限之後，我們會看到通知出現。


1. 在 Visual Studio for Mac，請在建立新的單一檢視 iOS 方案，並為它`Notifications`。
1. 開啟`Main.storyboard`檔案，並將按鈕拖曳至檢視。 將按鈕** 按鈕**，並為它提供標題**新增通知**。 您也可以設定一些[條件約束](~/ios/user-interface/designer/designer-auto-layout.md)此時的按鈕： 

    ![](local-notifications-in-ios-walkthrough-images/image3.png "設定在按鈕上的某些條件約束")
1. 編輯`ViewController`類別，並將下列事件處理常式新增至 ViewDidLoad 方法：

    ```csharp
    button.TouchUpInside += (sender, e) =>
    {
        // create the notification
        var notification = new UILocalNotification();

        // set the fire date (the date time in which it will fire)
        notification.FireDate = NSDate.FromTimeIntervalSinceNow(60);

        // configure the alert
        notification.AlertAction = "View Alert";
        notification.AlertBody = "Your one minute alert has fired!";

        // modify the badge
        notification.ApplicationIconBadgeNumber = 1;

        // set the sound to be the default sound
        notification.SoundName = UILocalNotification.DefaultSoundName;

        // schedule it
        UIApplication.SharedApplication.ScheduleLocalNotification(notification);
    };
    ```

    此程式碼會建立使用音效、 設定的圖示徽章值為 1，並向使用者顯示警示的通知。

1. 接下來編輯檔案`AppDelegate.cs`，先將下列程式碼`FinishedLaunching`方法。 我們已檢查，以查看裝置是否正在執行 iOS 8 中，如果我們**必要**尋求以接收通知的使用者的權限：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
            );

            application.RegisterUserNotificationSettings (notificationSettings);
        }
    ```

1. 仍在`AppDelegate.cs`，新增下列方法將會收到通知時所呼叫：

    ```csharp
    public override void ReceivedLocalNotification(UIApplication application, UILocalNotification notification)
            {
                // show an alert
                UIAlertController okayAlertController = UIAlertController.Create(notification.AlertAction, notification.AlertBody, UIAlertControllerStyle.Alert);
                okayAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));

                UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(okayAlertController, true, null);

                // reset our badge
                UIApplication.SharedApplication.ApplicationIconBadgeNumber = 0;
            }

    ```

1. 我們需要處理，因為本機通知啟動通知的情況。 編輯此方法`FinishedLaunching`在`AppDelegate`包含下列程式碼片段：


    ```csharp
    // check for a notification

    if (launchOptions != null)
    {
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
    }

    ```

1. 最後，執行應用程式。 在 iOS 8 您將會提示允許通知。 按一下 [ **[確定]** ，然後按一下**加入通知**] 按鈕。 在之後短暫的暫停中，您應該看到警示對話方塊中，如下列螢幕擷取畫面所示：

    ![](local-notifications-in-ios-walkthrough-images/image0.png "確認能夠傳送通知") ![](local-notifications-in-ios-walkthrough-images/image1.png "加入通知按鈕") ![](local-notifications-in-ios-walkthrough-images/image2.png "通知的警示對話方塊")

## <a name="summary"></a>總結

本逐步解說示範如何建立及發行通知在 iOS 中使用各種不同的 API。 它也會示範如何使用徽章，以提供給使用者的某些應用程式特定的意見反應更新應用程式圖示。


## <a name="related-links"></a>相關連結

- [本機通知 （範例）](https://developer.xamarin.com/samples/monotouch/LocalNotifications)
- [本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)
