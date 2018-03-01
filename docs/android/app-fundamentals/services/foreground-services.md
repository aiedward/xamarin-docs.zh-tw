---
title: "前景服務"
ms.topic: article
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 857c7fe47ad5fa0f50fce95672bc8ffbf6771dc9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="foreground-services"></a>前景服務

有些服務正在執行的使用者會主動留意某些工作，這些服務也稱為_前景服務_。 前景服務的範例是已為使用者提供指示開車或步行時的應用程式。 即使應用程式是在背景中，仍然十分重要的服務具有足夠的資源，才能正常運作，而且使用者擁有可快速又方便地存取應用程式。 Android 應用程式中，這表示前景服務應該會收到優先順序高於 「 一般 」 服務，而且前景服務必須提供`Notification`Android 將會顯示，只要服務正在執行。
 
前景是建立和啟動服務就像任何其他服務。 當服務啟動時，它會註冊本身與 Android 為前景服務。
 
本指南會討論必須採取註冊前景服務，以及完成時停止服務的額外步驟。

## <a name="registering-as-a-foreground-service"></a>註冊為前景服務

前景服務是一種特殊類型的繫結的服務或啟動的服務。 服務，一旦啟動後，就會呼叫[ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/) android 為前景服務登錄它自己的方法。   

`StartForeground` 接受兩個參數，兩者都是強制性：
 
* 整數值，來識別服務應用程式中是唯一的。
* A `Notification` Android 將會顯示在狀態列的只要服務正在執行的物件。

Android 將狀態列中顯示通知，只要服務正在執行。 該通知上方，最小值，將服務正在執行使用者提供視覺提示。 在理想情況下，通知應該提供的使用者的應用程式或可能是有些動作的按鈕，來控制應用程式的捷徑。 舉例來說，這是音樂播放器&ndash;會顯示通知可能會暫停播放音樂，倒回上一首歌曲，或跳至下一首歌曲的按鈕。 

此程式碼片段是以前景服務註冊服務的範例：   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.
    
    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

先前的通知會顯示狀態列通知，會如下所示：

![在狀態列中顯示通知的影像](foreground-services-images/foreground-services-01.png "狀態列中顯示通知的影像")

這個螢幕擷取畫面會顯示展開的通知中通知紙匣，以允許使用者控制服務的兩個動作：

![顯示展開的通知的影像](foreground-services-images/foreground-services-02.png "顯示展開的通知的影像。")

通知的詳細資訊可用於[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)區段[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。

## <a name="unregistering-as-a-foreground-service"></a>取消註冊為前景服務

服務可以取消其本身前景服務呼叫的方法來`StopForeground`。 `StopForeground` 不會停止服務，但它也會移除通知圖示和信號可以向下關閉這項服務，如有必要的 Android。

會顯示狀態 列通知也可以藉由傳遞移除`true`方法： 

```csharp
StopForeground(true);
```

如果服務暫止呼叫`StopSelf`或`StopService`，則同樣會移除狀態 列通知。


## <a name="related-links"></a>相關連結

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForegrond](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [本機的通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
