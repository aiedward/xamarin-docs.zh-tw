---
title: 服務通知
description: 本指南會討論如何 Android 服務可能會使用本機通知分派給使用者的資訊。
ms.prod: xamarin
ms.assetid: 6C06FDE7-6385-40EF-AC7C-8EFB54E29F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: d56f67254a9eae334fa8ac3f08d3ef270800c309
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103242"
---
# <a name="service-notifications"></a>服務通知

_本指南會討論如何 Android 服務可能會使用本機通知分派給使用者的資訊。_


## <a name="service-notifications-overview"></a>服務通知概觀

服務通知，向使用者顯示資訊的應用程式，即使 Android 應用程式不在前景中。 您可提供針對使用者，例如顯示從應用程式的活動動作的通知。 下列程式碼範例示範如何服務可能會分派給使用者的通知：

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

此螢幕擷取畫面會顯示通知的範例：

[![在狀態列中顯示的通知圖示](service-notifications-images/01-notification-sml.png)](service-notifications-images/01-notification.png#lightbox)

當使用者滑向下從頂端的 [通知] 畫面時，會顯示完整的通知：

![通知系統匣中顯示的 notication](service-notifications-images/02-fullnotification.png)


## <a name="updating-a-notification"></a>正在更新通知

若要更新的通知，服務將會重新發佈通知使用相同通知識別碼。 Android 將會顯示，或更新 [狀態] 列中的通知，視。

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

通知的詳細資訊位於[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)一節[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。


## <a name="related-links"></a>相關連結

- [在 Android 中的本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
