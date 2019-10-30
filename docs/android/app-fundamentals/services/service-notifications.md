---
title: 服務通知
description: 本指南討論 Android 服務如何使用本機通知來將資訊分派給使用者。
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: b02785863f89ef6a273c52c09f45a99c17cb6242
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024525"
---
# <a name="service-notifications"></a>服務通知

_本指南討論 Android 服務如何使用本機通知來將資訊分派給使用者。_

## <a name="service-notifications-overview"></a>服務通知總覽

服務通知可讓應用程式向使用者顯示資訊，即使 Android 應用程式不在前景中也一樣。 通知可能會提供使用者的動作，例如顯示應用程式中的活動。 下列程式碼範例會示範服務如何將通知分派給使用者：

```csharp
[Service]
public class MyService: Service 
{
    // A notification requires an id that is unique to the application.
    const int NOTIFICATION_ID = 9000;
    
    public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
    {
        // Code omitted for clarity - here is where the service would do something.
    
        // Work has finished, now dispatch anotification to let the user know.
        Notification.Builder notificationBuilder = new Notification.Builder(this)
            .SetSmallIcon(Resource.Drawable.ic_notification_small_icon)
            .SetContentTitle(Resources.GetString(Resource.String.notification_content_title))
            .SetContentText(Resources.GetString(Resource.String.notification_content_text));
        
        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.Notify(NOTIFICATION_ID, notificationBuilder.Build());
    }
}
```

這個螢幕擷取畫面是顯示通知的範例：

[狀態列中顯示![通知圖示](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

當使用者從頂端滑下通知畫面時，會顯示完整的通知：

![Notication 顯示在通知紙匣中](service-notifications-images/02-fullnotification.png)

## <a name="updating-a-notification"></a>更新通知

若要更新通知，服務會使用相同的通知識別碼來重新發佈通知。 Android 會視需要在狀態列中顯示或更新通知。

```csharp 
void UpdateNotification(string content)
{
   var notification = GetNotification(content, pendingIntent);

   NotificationManager notificationManager = (NotificationManager)GetSystemService(Context.NotificationService);
   notificationManager.Notify(NOTIFICATION_ID, notification);
}

Notification GetNotification(string content, PendingIntent intent)
{
   return new Notification.Builder(this)
           .SetContentTitle(tag)
           .SetContentText(content)
           .SetSmallIcon(Resource.Drawable.NotifyLg)
           .SetLargeIcon(BitmapFactory.DecodeResource(Resources, Resource.Drawable.Icon))
           .SetContentIntent(intent).Build();
}
```

關於通知的詳細資訊可在[Android 通知](~/android/app-fundamentals/notifications/index.md)指南的[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)一節中取得。

## <a name="related-links"></a>相關連結

- [Android 中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
