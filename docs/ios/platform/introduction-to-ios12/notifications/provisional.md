---
title: 在 Xamarin.iOS 中佈建通知
description: 本文件說明如何使用 Xamarin.iOS 使用暫時的通知。 佈建通知，iOS 第 12 中導入可讓傳送無訊息的通知，而不需要明確的使用者權限的應用程式。
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 434dd2e2ee7a0064b706872a228070c5114078c4
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667018"
---
# <a name="provisional-notifications-in-xamarinios"></a>在 Xamarin.iOS 中佈建通知

佈建通知可讓應用程式來提供不需要使用者的明確事先同意的通知。 這些通知到達時以無訊息模式，並只在通知中心，讓使用者選擇參與或退出其持續傳遞之前預覽中顯示。

在通知中心，使用者可以指定應用程式應該停止傳遞佈建通知，繼續將其傳遞到部份，或開始更顯著地傳遞。

## <a name="sample-app-redgreennotifications"></a>範例應用程式：RedGreenNotifications

看看[RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)範例應用程式，這會傳送暫時的通知。

## <a name="sending-provisional-notifications"></a>傳送的佈建通知

若要傳送暫時的通知，提供`UNAuthorizationOptions.Provisional`當做選項 [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
方法`UNUserNotificationCenter`:

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Provisional;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

如果在使用者升級到顯著的傳遞，暫時通知`UNAuthorizationOptions`值傳遞給`RequestAuthorization`會決定新的通知傳遞設定 (在上述的程式碼`UNAuthorizationOptions.Alert`和`UNAuthorizationOptions.Sound`)。

## <a name="related-links"></a>相關連結

- [範例應用程式 – RedGreenNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/RedGreenNotifications)
- [在 Xamarin.iOS 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [在 使用者通知 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知 (WWDC 2017) 在最新消息](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
