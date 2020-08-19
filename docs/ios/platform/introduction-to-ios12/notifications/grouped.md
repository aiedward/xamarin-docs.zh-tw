---
title: 在 Xamarin 中分組的通知
description: 使用 iOS 12，可以在通知中心或依應用程式或執行緒的鎖定畫面中將通知分組。 本檔說明如何使用 Xamarin 傳送執行緒和 unthreaded 通知。
ms.prod: xamarin
ms.assetid: C6FA7C25-061B-4FD7-8E55-88597D512F3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/04/2018
ms.openlocfilehash: 8c4ad31d91e6f9e4415309eee6c3da8a095dc097
ms.sourcegitcommit: 80bd4d74812d2c086a164323b72a14662276d302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608607"
---
# <a name="grouped-notifications-in-xamarinios"></a>在 Xamarin 中分組的通知

根據預設，iOS 12 會將應用程式的所有通知放在群組中。 鎖定畫面和通知中心會將此群組顯示為最上方最新通知的堆疊。 使用者可以展開群組以查看其包含的所有通知，並將整個群組關閉。

應用程式也可以依執行緒將通知分組，讓使用者更容易尋找感興趣的特定資訊並與其互動。

## <a name="sample-app-groupednotifications"></a>範例應用程式： GroupedNotifications

若要瞭解如何搭配使用群組通知與 Xamarin，請參閱 [GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications) 範例應用程式。

這個範例應用程式會模擬與各種朋友的對話、傳送每則訊息的通知，並依執行緒將其分組。 它也會示範如何在應用層級群組中 unthreaded 通知。

本指南中的程式碼片段來自此範例應用程式。

## <a name="request-authorization-and-allow-foreground-notifications"></a>要求授權並允許前景通知

在應用程式可以傳送本機通知之前，它必須要求許可權。 在範例應用程式中 [`AppDelegate`](xref:UIKit.UIApplicationDelegate) ， [`FinishedLaunching`](xref:UIKit.UIApplicationDelegate.FinishedLaunching(UIKit.UIApplication,Foundation.NSDictionary)) 方法會要求此許可權：

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

[`Delegate`](xref:UserNotifications.UNUserNotificationCenter.Delegate)上述 (設定) ，以 [`UNUserNotificationCenter`](xref:UserNotifications.UNUserNotificationCenter) 決定前景應用程式是否應該藉由呼叫傳遞給的完成處理常式來顯示傳入通知 [`WillPresentNotification`](xref:UserNotifications.UNUserNotificationCenterDelegate_Extensions.WillPresentNotification(UserNotifications.IUNUserNotificationCenterDelegate,UserNotifications.UNUserNotificationCenter,UserNotifications.UNNotification,System.Action{UserNotifications.UNNotificationPresentationOptions})) ：

```csharp
[Export("userNotificationCenter:willPresentNotification:withCompletionHandler:")]
public void WillPresentNotification(UNUserNotificationCenter center, UNNotification notification, System.Action<UNNotificationPresentationOptions> completionHandler)
{
    completionHandler(UNNotificationPresentationOptions.Alert);
}
```

此 [`UNNotificationPresentationOptions.Alert`](xref:UserNotifications.UNNotificationPresentationOptions) 參數表示應用程式應該顯示警示，但不播放音效或更新徽章。

## <a name="threaded-notifications"></a>執行緒通知

重複 **使用 alice** 按鈕的範例應用程式訊息，讓它傳送與名為 alice 的 friend 交談的通知。
因為此對話的通知是相同執行緒的一部分，所以鎖定畫面和通知中心會將它們組合在一起。

若要開始與不同 friend 的交談，請按一下 [ **選擇新的 friend** ] 按鈕。 此交談的通知會顯示在不同的群組中。

### <a name="threadidentifier"></a>ThreadIdentifier

每當範例應用程式啟動新的執行緒時，就會建立唯一的執行緒識別碼：

```csharp
void StartNewThread()
{
    threadId = $"message-{friend}";
    // ...
}
```

若要傳送執行緒通知，範例應用程式：

- 檢查應用程式是否有傳送通知的授權。
- 建立 [`UNMutableNotificationContent`](xref:UserNotifications.UNMutableNotificationContent)
通知內容的物件，並將其 [`ThreadIdentifier`](xref:UserNotifications.UNMutableNotificationContent.ThreadIdentifier)
至上方建立的執行緒識別碼。
- 建立要求並排定通知：

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

相同應用程式中具有相同執行緒識別碼的所有通知都會出現在相同的通知群組中。

> [!NOTE]
> 若要在遠端通知上設定執行緒識別碼，請將 `thread-id` 金鑰新增至通知的 JSON 承載。 如需詳細資訊，請參閱 Apple 的 [產生遠端通知](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification) 檔。

### <a name="summaryargument"></a>SummaryArgument

`SummaryArgument` 指定通知將會如何影響出現在通知所屬通知群組左下角的摘要文字。 iOS 會匯總相同群組中通知的摘要文字，以建立整體摘要描述。

範例應用程式會使用訊息的作者做為摘要引數。 使用這種方法時，Alice 的六個通知群組的摘要文字可能會有 **5 個以上的通知**。

## <a name="unthreaded-notifications"></a>Unthreaded 通知

每個範例應用程式的 [ **約會提醒** ] 按鈕都會傳送各種約會提醒通知。 由於這些提醒並非執行緒，它們會出現在鎖定畫面和通知中心的應用層級通知群組中。

若要傳送 unthreaded 通知，範例應用程式的 `ScheduleUnthreadedNotification` 方法會使用與上述類似的程式碼。
但是，它不會 `ThreadIdentifier` 在物件上設定 `UNMutableNotificationContent` 。

## <a name="related-links"></a>相關連結

- [範例應用程式-GroupedNotifications](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-groupednotifications)
- [Xamarin 中的使用者通知架構](~/ios/platform/user-notifications/index.md)
- [使用者通知的新功能 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/710/)
- [使用群組通知 (WWDC 2018) ](https://developer.apple.com/videos/play/wwdc2018/711/)
- [最佳做法和使用者通知的新功能 (WWDC 2017) ](https://developer.apple.com/videos/play/wwdc2017/708/)
- [ (Apple) 產生遠端通知 ](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/generating_a_remote_notification)
