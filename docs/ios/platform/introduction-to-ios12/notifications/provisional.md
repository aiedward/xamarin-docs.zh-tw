---
title: 在 Xamarin 中的臨時通知
description: 本檔說明如何使用 Xamarin 來處理臨時通知。 在 iOS 12 中引進的臨時通知，可讓應用程式在不需要明確使用者權限的情況下傳送無訊息通知。
ms.prod: xamarin
ms.assetid: 5DCB36B9-2637-48AE-8FC0-F6124F08AC48
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 745bfbc56dec12b7d46003a1d488e5638dc6c110
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435172"
---
# <a name="provisional-notifications-in-xamarinios"></a>在 Xamarin 中的臨時通知

臨時通知可讓應用程式在不需要使用者明確的事先同意的情況下傳遞通知。 這些通知會以安靜的方式抵達，並只顯示在通知中心，讓使用者在選擇進入或離開繼續傳遞之前先預覽。

在通知中心中，使用者可以指定應用程式應該停止傳遞暫時性通知、繼續傳遞它們 provisionally，或開始更醒目地傳遞它們。

## <a name="sample-app-redgreennotifications"></a>範例應用程式： RedGreenNotifications

請看看 [RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications) 範例應用程式，它會傳送暫時性的通知。

## <a name="sending-provisional-notifications"></a>傳送臨時通知

若要傳送暫時性通知，請提供 `UNAuthorizationOptions.Provisional` 選項給 [`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)
的方法 `UNUserNotificationCenter` ：

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

如果使用者將暫時性通知升級為重要的傳遞，則 `UNAuthorizationOptions` 傳遞給的值 `RequestAuthorization` 會決定在上述程式碼中 (新的通知傳遞設定， `UNAuthorizationOptions.Alert` 並 `UNAuthorizationOptions.Sound`) 。

## <a name="related-links"></a>相關連結

- [範例應用程式-RedGreenNotifications](/samples/xamarin/ios-samples/ios12-redgreennotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple) ](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能 (WWDC 2017) ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 產生遠端通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)