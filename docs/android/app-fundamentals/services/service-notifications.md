---
title: 服務通知
description: 本指南討論 Android 的服務可能會如何使用本機通知分派給使用者的資訊。
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 65ccb94bd4fac1f3818b4f08eb34ecf73d6c52e4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30762629"
---
# <a name="service-notifications"></a>服務通知

_本指南討論 Android 的服務可能會如何使用本機通知分派給使用者的資訊。_


## <a name="service-notifications-overview"></a>服務通知概觀

服務通知可讓應用程式顯示資訊給使用者，即使 Android 應用程式不在前景。 它有可能提供的使用者，例如顯示從應用程式的活動動作的通知。 下列程式碼範例將示範如何服務可能會分派給使用者的通知：

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

這個螢幕擷取畫面會顯示通知的範例：

[![在狀態列中顯示通知圖示](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

當使用者投影片向下從頂端的 [通知] 畫面時，會顯示完整的通知：

![顯示在通知匣 notication](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>更新通知

若要更新的通知，服務會重新發佈通知使用相同通知識別碼。 顯示或更新 [狀態] 列中的通知，視 android。

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

通知的詳細資訊可用於[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)區段[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。


## <a name="related-links"></a>相關連結

- [在 Android 中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
