---
title: 逐步解說-在 Xamarin 中使用本機通知
description: 在本節中，我們將逐步解說如何在 Xamarin iOS 應用程式中使用本機通知。 它會示範建立和發佈通知的基本概念，此通知會在應用程式收到警示時顯示警示。
ms.prod: xamarin
ms.assetid: 32B9C6F0-2BB3-4295-99CB-A75418969A62
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 915b5cb11aed96598e0460125734b15757f45466
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436140"
---
# <a name="walkthrough---using-local-notifications-in-xamarinios"></a>逐步解說-在 Xamarin 中使用本機通知

_在本節中，我們將逐步解說如何在 Xamarin iOS 應用程式中使用本機通知。它會示範建立和發佈通知的基本概念，此通知會在應用程式收到警示時顯示警示。_

> [!IMPORTANT]
> 本章節中的資訊適用于 iOS 9 和更早版本，其為支援較舊的 iOS 版本。 若為 iOS 10 和更新版本，請參閱 [使用者通知架構指南](~/ios/platform/user-notifications/index.md) ，以在 ios 裝置上支援本機和遠端通知。

## <a name="walkthrough"></a>逐步解說

讓我們建立一個簡單的應用程式，以顯示本機通知的實際運作方式。 此應用程式會有一個按鈕。 當我們按一下按鈕時，就會建立本機通知。 經過指定的時間週期之後，我們會看到通知出現。

1. 在 Visual Studio for Mac 中，建立新的單一視圖 iOS 解決方案並加以呼叫 `Notifications` 。
1. 開啟檔案 `Main.storyboard` ，並將按鈕拖曳到視圖上。 為按鈕 **按鈕**命名，並為其提供標題 **新增通知**。 您也可能想要在此時設定按鈕的一些 [條件約束](~/ios/user-interface/designer/designer-auto-layout.md) ： 

    ![設定按鈕上的一些條件約束](local-notifications-in-ios-walkthrough-images/image3.png)
1. 編輯 `ViewController` 類別，並將下列事件處理常式加入至 ViewDidLoad 方法：

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

    此程式碼會建立使用音效的通知，將圖示徽章的值設定為1，並向使用者顯示警示。

1. 接著 `AppDelegate.cs` ，請編輯檔案，然後將下列程式碼加入至 `FinishedLaunching` 方法。 我們已檢查過裝置是否正在執行 iOS 8，如果需要，我們 **必須** 要求使用者提供接收通知的許可權：

    ```csharp
    if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
        var notificationSettings = UIUserNotificationSettings.GetSettingsForTypes (
            UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound, null
        );

        application.RegisterUserNotificationSettings (notificationSettings);
    }
    ```

1. 同樣地 `AppDelegate.cs` ，新增下列方法，在收到通知時將會呼叫：

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

1. 我們需要處理因為本機通知而啟動通知的情況。 `FinishedLaunching`在中編輯方法 `AppDelegate` ，以包含下列程式碼片段：

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

1. 最後，執行應用程式。 在 iOS 8 上，系統會提示您允許通知。 按一下 **[確定]** ，然後按一下 [ **新增通知** ] 按鈕。 短暫暫停之後，您應該會看到 [警示] 對話方塊，如下列螢幕擷取畫面所示：

    ![確認傳送通知 ](local-notifications-in-ios-walkthrough-images/image0.png) ![ 的功能 [新增通知] 按鈕 ](local-notifications-in-ios-walkthrough-images/image1.png) ![ 通知警示對話方塊](local-notifications-in-ios-walkthrough-images/image2.png)

## <a name="summary"></a>摘要

本逐步解說示範如何使用各種 API 來建立和發佈 iOS 中的通知。 文中也示範了如何以徽章來更新應用程式圖示，以提供一些應用程式特定的意見反應給使用者。

## <a name="related-links"></a>相關連結

- [本機通知 (範例) ](/samples/xamarin/ios-samples/localnotifications)
- [本機和推播通知程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)