---
title: 在 Xamarin 中的重大警示
description: 本檔說明如何搭配使用重大警示與 Xamarin。 IOS 12 引進的重大警示是干擾性通知，不論是否開啟或響鈴開關皆會播放音效。
ms.prod: xamarin
ms.assetid: 75742257-081D-44F4-B49E-FB807DF85262
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 43b810b95e4da2927030617e68c0ade824a0beaa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031983"
---
# <a name="critical-alerts-in-xamarinios"></a>在 Xamarin 中的重大警示

使用 iOS 12，應用程式可以傳送重大警示。 重要警示會播放音效，而不論是否已啟用 [不要打擾] 或響鈴開關是否已關閉。 這些通知是干擾性的，只有在使用者必須採取立即動作時才會使用。

## <a name="custom-critical-alert-entitlement"></a>自訂重大警示權利

若要在您的應用程式中顯示重大警示，請先向 Apple[要求自訂的重大警示通知權利](https://developer.apple.com/contact/request/notifications-critical-alerts-entitlement/)。

從 Apple 收到此權利，並遵循有關如何設定應用程式來使用它的任何相關指示之後，請將自訂權利新增至您應用程式的**plist** [檔案](~/ios/deploy-test/provisioning/entitlements.md)。 然後，將您的**iOS 配套簽署**選項設定為在模擬器和裝置上簽署應用程式時使用**plist** 。

## <a name="request-authorization"></a>要求授權

應用程式的通知授權要求會提示使用者允許或禁止應用程式的通知。 如果通知授權要求要求傳送重大警示的許可權，應用程式也會讓使用者有機會加入重大警示。

下列程式碼會透過傳遞適當的[`UNAuthorizationOptions`](xref:UserNotifications.UNAuthorizationOptions) ，要求傳送重要警示和標準通知和音效的許可權
要[`RequestAuthorization`](xref:UserNotifications.UNUserNotificationCenter.RequestAuthorization*)的值：

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

## <a name="local-critical-alerts"></a>本機重大警示

若要傳送本機重大警示，請建立[`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
並將其 [`Sound`] 屬性設定為下列其中一項：

- `UNNotificationSound.DefaultCriticalSound`，它會使用預設的重要通知音效。
- `UNNotificationSound.GetCriticalSound`，可讓您指定與您的應用程式和磁片區配套的自訂音效。

然後，從通知內容建立 `UNNotificationRequest`，並將它新增至通知中心：

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
> 如果未針對您的應用程式啟用重要警示，將不會傳遞它們。 除了應用程式第一次要求傳送重大警示的許可權之外，使用者也可以在 iOS [**設定**] 應用程式的 [**通知**] 區段中啟用或停用重大警示。

## <a name="remote-critical-alerts"></a>遠端重大警示

如需有關遠端重大警示的詳細資訊，請參閱 WWDC 2018 的[使用者通知會話的新功能](https://developer.apple.com/videos/play/wwdc2018/710/)和[產生遠端通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)檔。

## <a name="related-links"></a>相關連結

- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [UserNotifications （Apple）](https://developer.apple.com/documentation/usernotifications?language=objc)
- [使用者通知的新功能（WWDC 2018）](https://developer.apple.com/videos/play/wwdc2018/710/)
- [最佳做法和使用者通知的新功能（WWDC 2017）](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知（Apple）](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
