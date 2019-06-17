---
title: 在 Xamarin.iOS 中群組的通知
description: 使用 iOS 12，就可以在通知中心或由應用程式或執行緒的鎖定畫面中的群組通知。 本文件說明如何傳送執行緒，以及利用 Xamarin.iOS 的 unthreaded 的通知。
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/04/2018
ms.openlocfilehash: 6798c4c5fa7502ba5e99cb8bc209468acaa4a9ec
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402417"
---
# <a name="grouped-notifications-in-xamarinios"></a>在 Xamarin.iOS 中群組的通知

根據預設，iOS 12 置於所有應用程式的通知群組。 鎖定畫面及通知中心顯示此群組為最新通知，在最上層堆疊。 使用者可以展開以查看它包含並關閉整個群組的所有通知群組。

應用程式也可以讓使用者更輕鬆地尋找並與他們感興趣的特定資訊進行互動的執行緒群組的通知。

## <a name="sample-app-groupednotifications"></a>範例應用程式：GroupedNotifications

若要了解如何使用 Xamarin.iOS 中使用群組的通知，看看[GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)範例應用程式。

此範例應用程式會模擬各種傳送每則訊息的通知，並將它們群組執行緒的朋友交談。 它也會示範如何 unthreaded 的通知進入應用程式層級的群組。

本指南中的程式碼片段來自此範例應用程式。

## <a name="request-authorization-and-allow-foreground-notifications"></a>要求授權，並允許前景通知

應用程式可以傳送本機通知之前，它必須要求權限，若要這樣做。 在範例應用程式之[ `AppDelegate` ](xref:UIKit.UIApplicationDelegate)，則[ `FinishedLaunching` ](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary))方法會要求此權限：

```csharp
public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
{
    UNUserNotificationCenter center = UNUserNotificationCenter.Current;
    center.RequestAuthorization(UNAuthorizationOptions.Alert, (bool success, NSError error) =>
    {
        // Set the Delegate regardless of success; users can modify their notification
        // preferences at any time in the Settings app.
        center.Delegate = this;
    });
    return true;
}
```

[ `Delegate` ](xref:UserNotifications.UNUserNotificationCenter.Delegate) （設定上述） [ `UNUserNotificationCenter` ](xref:UserNotifications.UNUserNotificationCenter)決定前景應用程式是否應該藉由呼叫傳遞給完成處理常式會顯示內送通知[`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})):

```csharp
[Export("userNotificationCenter:willPresentotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

[ `UNNotificationPresentationOptions.Alert` ](xref:UserNotifications.UNNotificationPresentationOptions)參數指示應用程式應該顯示該警示，但不是播放的音效或更新徽章。

## <a name="threaded-notifications"></a>執行緒的通知

點選的範例應用程式**訊息與 Alice**按鈕重複，讓它與名為 Alice 朋友傳送對話的通知。
由於這項交談的通知屬於同一個執行緒，鎖定畫面及通知中心它們組成群組。

若要開始交談以不同的 friend，點選**選擇新朋友** 按鈕。 此交談的通知會出現在不同的群組。

### <a name="threadidentifier"></a>ThreadIdentifier

每當範例應用程式會啟動新的執行緒，它會建立唯一的執行緒識別碼：

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

若要傳送執行緒的通知，範例應用程式：

- 檢查應用程式是否具有授權傳送通知。
- 建立 [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
物件通知的內容，並設定其 [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
以上面建立的執行緒識別項。
- 建立要求，並排程通知：

```csharp
async partial void ScheduleThreadedNotification(UIButton sender)
{
    var center = UNUserNotificationCenter.Current;

    UNNotificationSettings settings = await center.GetNotificationSettingsAsync();
    if (settings.AuthorizationStatus != UNAuthorizationStatus.Authorized)
    {
        return;
    }

    string author =  // ...
    string message = // ...

    var content = new UNMutableNotificationContent()
    {
        ThreadIdentifier = threadId,
        Title = author,
        Body = message,
        SummaryArgument = author
    };

    var request = UNNotificationRequest.FromIdentifier(
        Guid.NewGuid().ToString(),
        content,
        UNTimeIntervalNotificationTrigger.CreateTrigger(1, false)
    );

    center.AddNotificationRequest(request, null);

    // ...
}
```

具有相同的執行緒識別項相同的應用程式的所有通知會都出現在相同的通知群組。

> [!NOTE]
> 若要設定遠端通知執行緒識別項，新增`thread-id`金鑰來通知的 JSON 承載。 請參閱 Apple[產生遠端通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)文件，如需詳細資訊。

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` 指定通知將會如何影響左下角的通知所屬的通知群組顯示的摘要文字。 從相同群組中建立的整體摘要描述的通知，iOS 會彙總摘要文字。

範例應用程式會使用訊息的作者，做為摘要的引數。 使用這個方法，可能是一群與 Alice 的六個通知的摘要文字**5 更多的通知，Alice 和我從**。

## <a name="unthreaded-notifications"></a>Unthreaded 的通知

範例應用程式的每次點選**約會提醒**按鈕會傳送一份的各種約會提醒通知。 這些提醒不會限制執行緒，因為它們會出現在應用程式層級的通知群組中，在鎖定畫面上，在通知中心。

若要傳送 unthreaded 的通知，範例應用程式的`ScheduleUnthreadedNotification`方法會使用與上述類似的程式碼。
不過，它不會設定`ThreadIdentifier`上`UNMutableNotificationContent`物件。

## <a name="related-links"></a>相關連結

- [範例應用程式 – GroupedNotifications](https://developer.xamarin.com/samples/monotouch/iOS12/GroupedNotifications)
- [在 Xamarin.iOS 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [在 使用者通知 (WWDC 2018) 最新消息](https://developer.apple.com/videos/play/wwdc2018/710/)
- [使用群組的通知 (WWDC 2018)](https://developer.apple.com/videos/play/wwdc2018/711/)
- [最佳做法和使用者通知 (WWDC 2017) 在最新消息](https://developer.apple.com/videos/play/wwdc2017/708/)
- [產生遠端通知 (Apple)](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
