---
title: 在 Xamarin.iOS 中的重大警示
description: 本文件說明如何使用 Xamarin.iOS 中的重大警示。 使用 iOS 12，導入的重大警示播放聲音不論 「 請勿打擾 」 是否在干擾性通知或響鈴開關已關閉。
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 9/4/2018
ms.openlocfilehash: 699d19228d2dee92f7a730bba4186a3aa5f21b04
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233233"
---
# <a name="critical-alerts-in-xamarinios"></a>在 Xamarin.iOS 中的重大警示

Ios 12、 應用程式可以傳送重大警示。 重大警示播放的音效，不論啟用 「 請勿打擾 」 或響鈴開關已關閉。 這些通知是干擾性，且應該只在使用者必須立即採取行動時才使用。

## <a name="custom-critical-alert-entitlement"></a>自訂的重大警示權限

在您的應用程式，先顯示重大警示[要求自訂重要的警示通知的權利](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/)從 Apple。

從 Apple 接收此權利及如何設定您的應用程式來使用它時，任何相關聯指示新增自訂後[權利](~/ios/deploy-test/provisioning/entitlements.md)至您的應用程式**Entitlements.plist**檔案。 然後，設定您**iOS 套件組合簽署**選項，以使用**Entitlements.plist**簽署模擬器和裝置上的應用程式時。

## <a name="request-authorization"></a>要求授權

應用程式的通知授權要求會提示使用者允許或禁止應用程式的通知。 如果通知授權要求會傳送重大警示的權限要求，應用程式也給予使用者機會參加重大警示。

下列程式碼要求傳送重大警示和標準通知及聲音，藉由傳遞適當的權限 [`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions)
到數值[ `RequestAuthorization` ](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*):

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    var options = UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.CriticalAlert;
    center.RequestAuthorization(options, (bool success, NSError error) => {
        // ...
    );
    return true;
}
```

## <a name="local-critical-alerts"></a>本機的重大警示

若要傳送本機的重大警示，建立 [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
並設定其`Sound`屬性設為：

- `UNNotificationSound.DefaultCriticalSound`它會使用預設重大通知音效。
- `UNNotificationSound.GetCriticalSound`可讓您指定自訂聽起來，以搭配您的應用程式和磁碟區。

接著，建立`UNNotificationRequest`從通知內容，並將它新增至通知中心：

```csharp
var content = new UNMutableNotificationContent()
{
    Title = "Critical alert title",
    Body = "Text of the critical alert",
    CategoryIdentifier = "my-critical-alert-category",
    // Sound = UNNotificationSound.DefaultCriticalSound
    Sound = UNNotificationSound.GetCriticalSound("my_critical_sound.m4a", 1.0f)
};

var request = UNNotificationRequest.FromIdentifier(
    Guid.NewGuid().ToString(),
    content,
    UNTimeIntervalNotificationTrigger.CreateTrigger(3, false)
);

var center = UNUserNotificationCenter.Current;
center.AddNotificationRequest(request, null);
```

> [!IMPORTANT]
> 如果無法進行您的應用程式，將不會傳遞重大警示。 以及出現提示時第一次傳送重大警示的應用程式要求權限，因此使用者也可以啟用或停用您的應用程式中的重大警示**通知**一節的 ios**設定**應用程式。

## <a name="remote-critical-alerts"></a>遠端的重大警示

如需遠端的重大警示的資訊，請參閱[什麼是新的使用者通知](https://developer.apple.com/videos/play/wwdc2018/710/)WWDC 2018，從工作階段並[產生遠端通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)文件。

## <a name="related-links"></a>相關連結

- [在 Xamarin.iOS 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications (Apple)](https://developer.apple.com/documentation/usernotifications?language=objc)
- [在 使用者通知 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知 (WWDC 2017) 在最新消息](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
