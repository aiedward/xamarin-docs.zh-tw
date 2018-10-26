---
title: 前景服務
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: df917896f901060a5518076afa859d34a03f4d6d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108349"
---
# <a name="foreground-services"></a>前景服務

前景服務是一種特殊的繫結的服務或已啟動的服務。 有時候服務會執行使用者必須留意主動的工作，這些服務稱為_前景服務_。 前景服務的範例是提供使用者的指示進行時開車或步行的應用程式。 即使應用程式處於背景時，仍然十分重要，服務會有足夠的資源，才能正常運作，而且使用者擁有快速且方便的方式，來存取應用程式。 適用於 Android 的 app，這表示前景服務應該會收到較高的優先順序高於 「 一般 」 的服務，而且前景服務必須提供`Notification`Android 將會顯示，只要服務正在執行。
 
若要啟動的前景服務，應用程式就必須分派意圖，告知 Android，以啟動服務。 然後，服務必須註冊本身為前景服務與 Android。 Android 8.0 （或更高版本） 上執行的應用程式應該使用`Context.StartForegroundService`方法來啟動服務，而應該使用較舊版本的 Android 裝置執行的應用程式 `Context.StartService`

這個 C# 擴充方法是如何啟動前景服務的範例。 在 Android 8.0 和更新版本則會使用`StartForegroundService`方法，否則為較舊`StartService`將使用的方法。  

```csharp
public static void StartForegroundServiceComapt<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>註冊為前景服務

前景服務啟動之後，它必須將自己登錄與 Android 所叫用[ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)。 如果服務一開始`Service.StartForegroundService`方法但不會註冊本身，則 Android 會停止服務，並為沒有回應的應用程式的旗標。

`StartForeground` 採用兩個參數，這兩者都是必要項目：
 
* 整數值，來識別服務應用程式中是唯一的。
* A `Notification` Android 將會顯示的 [狀態] 列中，只要服務正在執行的物件。

Android 會將通知顯示在狀態列的只要服務正在執行。 該通知上方，至少會提供視覺提示給使用者的服務正在執行。 在理想情況下，應用程式或可能是某些動作的按鈕，來控制應用程式的捷徑使用者應該提供通知。 這個範例是音樂播放器&ndash;所顯示的通知可能會有按鈕來暫停/播放音樂，若要回到上一首歌曲，或跳到下一首歌曲。 

此程式碼片段是以前景服務中註冊服務的範例：   

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

先前的通知會顯示狀態列通知，如下所示：

![在狀態列中顯示通知的映像](foreground-services-images/foreground-services-01.png "狀態列中顯示通知的影像")

此螢幕擷取畫面會顯示已展開的通知的兩個動作可讓使用者控制的服務，通知系統匣中：

![顯示已展開的通知的映像](foreground-services-images/foreground-services-02.png "影像顯示展開的通知。")

通知的詳細資訊位於[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)一節[Android 通知](~/android/app-fundamentals/notifications/index.md)指南。

## <a name="unregistering-as-a-foreground-service"></a>正在移除註冊做為前景服務

服務可以取消列出本身為前景服務呼叫的方法來`StopForeground`。 `StopForeground` 將不會停止服務，但它會移除訊號可以向下關閉這項服務，如有必要的 Android 與通知圖示。

狀態列通知隨即出現，也可以藉由傳遞移除`true`方法： 

```csharp
StopForeground(true);
```

如果服務藉由呼叫暫止`StopSelf`或`StopService`，狀態列通知將會移除。

## <a name="related-links"></a>相關連結

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
