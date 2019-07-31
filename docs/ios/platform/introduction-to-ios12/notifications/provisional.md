---
title: Xamarin 中的臨時通知
description: 本檔說明如何使用 Xamarin 來處理臨時通知。 在 iOS 12 中引進的暫時性通知, 可讓應用程式在沒有明確使用者權限的情況下傳送無訊息通知。
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 1b4cf7f2caee274f353afff89e30c2db96009c12
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652360"
---
# <a name="provisional-notifications-in-xamarinios"></a>Xamarin 中的臨時通知

暫時通知可讓應用程式在沒有使用者明確的事先同意的情況下傳遞通知。 這些通知會以無人安靜的方式抵達, 而且只會顯示在通知中心, 讓使用者在選擇進入或離開其持續傳遞之前先預覽。

在 [通知中心] 中, 使用者可以指定應用程式應該停止傳遞暫時性通知、繼續傳遞 provisionally, 或開始更醒目地傳遞它們。

## <a name="sample-app-redgreennotifications"></a>範例應用程式:RedGreenNotifications

請參閱[RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)範例應用程式, 它會傳送暫時性通知。

## <a name="sending-provisional-notifications"></a>傳送臨時通知

若要傳送暫時性通知, `UNAuthorizationOptions.Provisional`請提供作為選項[`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
的`UNUserNotificationCenter`方法:

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

如果使用者將暫時通知升級為顯著傳遞, 傳遞`UNAuthorizationOptions`至`RequestAuthorization`的值將會決定新的通知傳遞設定 (在上述程式碼`UNAuthorizationOptions.Alert`中`UNAuthorizationOptions.Sound`, 和)。

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017)](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
