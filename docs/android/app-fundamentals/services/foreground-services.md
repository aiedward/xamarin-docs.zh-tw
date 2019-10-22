---
title: 前景服務
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: 6f3427641ba4ace3b640fcc970fd33f55087a9c8
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68644104"
---
# <a name="foreground-services"></a>前景服務

前景服務是一種特殊類型的系結服務或已啟動的服務。 服務偶爾會執行使用者必須主動留意的工作，這些服務就是所謂的「_前景服務_」。 前景服務的一個範例是一個應用程式，可在駕駛或流覽時提供指示給使用者。 即使應用程式在背景中，服務必須有足夠的資源才能正常運作，而且使用者有快速且方便的存取應用程式的方式。 對於 Android 應用程式，這表示前景服務應會收到高於「一般」服務的優先順序，而前景服務必須提供 Android 會在服務執行時顯示的 `Notification`。

若要啟動前景服務，應用程式必須分派指示 Android 啟動服務的意圖。 然後，服務必須將其本身註冊為 Android 的前景服務。 在 Android 8.0 （或更新版本）上執行的應用程式應該使用 `Context.StartForegroundService` 方法來啟動服務，而在具有舊版 Android 的裝置上執行的應用程式應該使用 `Context.StartService`

這個C#擴充方法是如何啟動前景服務的範例。 在 Android 8.0 和更新版本上，它會使用 `StartForegroundService` 方法，否則將會使用較舊的 `StartService` 方法。

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

一旦啟動前景服務，就必須叫用[`StartForeground`](xref:Android.App.Service.StartForeground*)，才能向 Android 註冊其本身。 如果服務是以 `Service.StartForegroundService` 方法啟動，但未自行註冊，則 Android 會停止服務，並將應用程式標示為無回應。

`StartForeground` 接受兩個參數，兩者都是強制的：

- 在應用程式中用來識別服務的唯一整數值。
- 只要服務正在執行，Android 就會顯示在狀態列中的 `Notification` 物件。

只要服務正在執行，Android 就會在狀態列中顯示通知。 通知至少會提供視覺提示給執行服務的使用者。 在理想的情況下，通知應為使用者提供應用程式的快捷方式，或可能有一些動作按鈕可控制應用程式。 其中一個範例是音樂播放機 &ndash; 顯示的通知可能會有按鈕可以暫停/播放音樂、倒轉至上一個歌曲，或跳到下一個歌曲。 

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

先前的通知會顯示狀態列通知，如下所示：

![在狀態列中顯示通知的影像](foreground-services-images/foreground-services-01.png "在狀態列中顯示通知的影像")

這個螢幕擷取畫面顯示通知紙匣中展開的通知，其中包含可讓使用者控制服務的兩個動作：

![顯示展開通知的影像](foreground-services-images/foreground-services-02.png "顯示展開通知的影像。")

關於通知的詳細資訊可在[Android 通知](~/android/app-fundamentals/notifications/index.md)指南的[本機通知](~/android/app-fundamentals/notifications/local-notifications.md)一節中取得。

## <a name="unregistering-as-a-foreground-service"></a>取消註冊為前景服務

服務可以藉由呼叫方法 `StopForeground`，將其本身解除清單為前景服務。 `StopForeground` 將不會停止服務，但會移除通知圖示並向 Android 發出信號，以在必要時關閉此服務。

藉由將 `true` 傳遞至方法，也可以移除所顯示的狀態列通知： 

```csharp
StopForeground(true);
```

如果服務因呼叫 `StopSelf` 或 `StopService` 而停止，則會移除狀態列通知。

## <a name="related-links"></a>相關連結

- [Android 應用程式服務](xref:Android.App.Service)
- [StartForeground。](xref:Android.App.Service.StartForeground*)
- [本機通知](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-foregroundservicedemo)
