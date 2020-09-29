---
title: 前景服務
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 0736e427c5c75b0eebe9b9353322b146e6359603
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455296"
---
# <a name="foreground-services"></a>前景服務

前景服務是一種特殊類型的系結服務或已啟動的服務。 有時服務會執行使用者必須主動察覺的工作，這些服務稱為「 _前景服務_」。 前景服務的其中一個範例就是提供使用者在駕駛或進行過程中提供指示的應用程式。 即使應用程式在背景中，仍很重要的是，服務有足夠的資源可正常運作，而且使用者有快速且方便的方式來存取應用程式。 若為 Android 應用程式，這表示前景服務應該會獲得高於「一般」服務的優先順序，而前景服務必須提供 `Notification` ，只要服務正在執行，就會顯示 Android。

若要啟動前景服務，應用程式必須分派指示 Android 啟動服務的意圖。 然後，服務必須將本身註冊為 Android 的前景服務。 在 Android 8.0 (或更高版本上執行的應用程式) 應該使用 `Context.StartForegroundService` 方法來啟動服務，而在具有舊版 Android 的裝置上執行的應用程式應該使用 `Context.StartService`

這個 c # 擴充方法是如何啟動前景服務的範例。 在 Android 8.0 和更新版本上，它會使用 `StartForegroundService` 方法，否則 `StartService` 會使用較舊的方法。

```csharp
public static void StartForegroundServiceCompat<T>(this Context context, Bundle args = null) where T : Service
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

一旦啟動前景服務之後，就必須叫用來向 Android 註冊它自己 [`StartForeground`](xref:Android.App.Service.StartForeground*) 。 如果使用 `Service.StartForegroundService` 方法來啟動服務，但未註冊它本身，則 Android 會停止服務，並將應用程式標示為無回應。

`StartForeground` 採用兩個參數，這兩個都是必要參數：

- 在應用程式中唯一的整數值，用來識別服務。
- `Notification`只要服務正在執行，Android 將在狀態列中顯示的物件。

只要服務正在執行，Android 就會在狀態列中顯示通知。 通知至少會提供視覺提示給使用者，指出服務正在執行中。 在理想的情況下，通知應該為使用者提供應用程式的快捷方式，或可能有一些動作按鈕來控制應用程式。 其中一個範例是音樂播放機， &ndash; 顯示的通知可能會有按鈕可暫停/播放音樂、倒轉至上一首歌，或跳到下一首歌。 

此程式碼片段是將服務註冊為前景服務的範例：   

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

先前的通知會顯示如下所示的狀態列通知：

![顯示狀態列中通知的影像](foreground-services-images/foreground-services-01.png "顯示狀態列中通知的影像")

此螢幕擷取畫面顯示通知匣中的展開通知，其中包含可讓使用者控制服務的兩個動作：

![顯示展開通知的影像](foreground-services-images/foreground-services-02.png "顯示展開通知的影像。")

有關通知的詳細資訊可在[Android 通知](~/android/app-fundamentals/notifications/index.md)指南的[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)區段中取得。

## <a name="unregistering-as-a-foreground-service"></a>取消註冊為前景服務

服務可以藉由呼叫方法，將本身以前景服務的形式取消清單 `StopForeground` 。 `StopForeground` 將不會停止服務，但它會移除通知圖示，併發出 Android 通知，如有需要，可以關閉此服務。

傳遞至方法也可以移除顯示的狀態列通知 `true` ： 

```csharp
StopForeground(true);
```

如果服務在呼叫或時停止 `StopSelf` `StopService` ，則會移除狀態列通知。

## <a name="related-links"></a>相關連結

- [Android. App. 服務](xref:Android.App.Service)
- [StartForeground。](xref:Android.App.Service.StartForeground*)
- [本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-foregroundservicedemo)