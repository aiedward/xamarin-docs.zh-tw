---
title: 使用 Xamarin.Android 啟動的服務
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 9e7dabf87314d87ab5ab335c220c0e292e56073b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110884"
---
# <a name="started-services-with-xamarinandroid"></a>使用 Xamarin.Android 啟動的服務

## <a name="started-services-overview"></a>啟動的服務概觀

啟動的服務通常執行工作的單位，而不需提供任何的直接意見反應或結果給用戶端。 工作單位的範例是一項服務，將檔案上傳至伺服器。 用戶端會從裝置至網站將檔案上傳服務提出要求。 服務會以無訊息模式將檔案上傳 （即使應用程式在前景中有任何活動），並上傳完成時自動終止。 請務必了解已啟動的服務將在應用程式的 UI 執行緒上執行。 這表示，如果服務是要執行將會封鎖 UI 執行緒的工作，它必須建立並視需要處置的執行緒。

不同於繫結的服務，沒有 「 純 」 已啟動的服務與其用戶端之間的通訊通道。 這表示已啟動的服務會實作一些不同的生命週期方法，比繫結的服務。 下列清單強調生命週期中常用的方法啟動服務：

* `OnCreate` &ndash; 呼叫第一次啟動服務時的一次。 這是實作初始化程式碼的位置。
* `OnBind` &ndash; 所有服務類別時，必須都實作這個方法，不過已啟動的服務通常沒有與它繫結的用戶端。 因為這個緣故，已啟動的服務只會傳回`null`。 混合式服務 （也就是繫結的服務和啟動的服務的組合） 相較之下，已實作，並傳回`Binder`用戶端。
* `OnStartCommand` &ndash; 針對每個要求來啟動服務，在呼叫回應呼叫`StartService`或重新啟動系統。 這是服務可由何處開始任何長時間執行的工作。 此方法會傳回`StartCommandResult`值，指出如何或者系統應該處理記憶體不足，造成在關機之後重新啟動服務。 這個呼叫會在主要執行緒。 下面詳細說明這個方法。
* `OnDestroy` &ndash; 服務正在被終結時，會呼叫這個方法。 它用來執行任何最終清除所需。

已啟動服務的重要方法是`OnStartCommand`方法。 它將會叫用，每次服務收到的要求執行一些工作。 下列程式碼是範例`OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一個參數是`Intent`物件，包含要執行之工作的相關中繼資料。 第二個參數會包含`StartCommandFlags`提供一些資訊，關於方法呼叫的值。 此參數會有兩個可能值的其中一個：

* `StartCommandFlag.Redelivery` &ndash; 這表示`Intent`是前一次重新傳遞`Intent`。 傳回服務時提供此值`StartCommandResult.RedeliverIntent`但它可以正常關閉前停止。
* `StartCommandFlag.Retry` &dash; 此值時先前收到`OnStartCommand`呼叫失敗，並且 Android 嘗試使用同一個意圖，表示為先前的失敗嘗試重新啟動該服務。
 
最後，第三個參數是整數值的唯一識別要求的應用程式。 可以多個呼叫端可能會叫用相同的服務物件。 這個值用來將停止使用指定的要求啟動的服務對服務的要求產生關聯。 它會一節中詳細討論[停止服務](#Stopping_the_Service)。 

值`StartCommandResult`服務所傳回建議的形式 android 上的服務因資源限制而終止時該怎麼辦。 有三個可能的值，如`StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash;這個值會告訴您不需要重新啟動服務，它已終止的 Android。 此範例，請考慮產生應用程式中的組件庫的縮圖的服務。 如果終止服務，它不一定要立即重新建立縮圖&ndash;縮圖可以重新建立該應用程式執行的下一次。
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash;這會告知 Android，重新啟動服務，但不是提供上次啟動服務的意圖。 如果不有任何擱置的意圖，以便處理，則會顯示`null`將意圖參數提供。 這個範例可能是音樂的播放程式應用程式;服務將會重新啟動已準備好播放音樂，但它會播放最後一首歌。 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash;這個值就是會告知 Android，以重新啟動服務，並重新傳送最後一個`Intent`。 其中一個範例就是下載資料檔案的應用程式的服務。 如果服務已終止，資料檔案仍然需要下載。 藉由傳回`StartCommandResult.RedeliverIntent`，當 Android 重新啟動服務的服務也會提供 （其中包含要下載之檔案的 URL） 的目的。 這可讓重新啟動或繼續 （取決於程式碼的實際實作） 下載。

還有第四個值`StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`。 這個值由`OnStartCommand`並說明如何 Android 將會繼續服務它已終止。 此值不是通常用來啟動服務。

已啟動服務的金鑰生命週期事件會由圖表所示： 

![圖表，顯示在其中的生命週期方法的呼叫的順序](started-services-images/started-service-01.png "圖表，顯示在其中的生命週期方法的呼叫的順序。")


<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>停止服務

已啟動的服務將繼續無限期; 執行Android 會保留服務執行，只要有足夠的系統資源。 用戶端必須先停止服務，或是完成其工作時，服務可能會停止本身。 有兩種方式可停止服務： 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash;用戶端 （例如活動） 可以要求服務停止藉由呼叫`StopService`方法： 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash;服務可能會自行關閉藉由叫用`StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>若要停止的服務使用 startId

多個呼叫端可以要求，啟動服務。 如果沒有未完成的啟動要求，服務可以使用`startId`，已傳遞到`OnStartCommand`以防止過早停止服務。 `startId`將會對應到最新呼叫`StartService`，而且將會遞增每次呼叫時。 因此，如果的後續要求`StartService`不尚未導致呼叫`OnStartCommand`，服務可以呼叫`StopSelfResult`，將它傳遞的最新的值`startId`收到 (而不是直接呼叫`StopSelf`)。 如果呼叫`StartService`尚不已經產生的對應呼叫`OnStartCommand`，系統不會停止服務，因為`startId`用於`StopSelf`呼叫不會對應至最新`StartService`呼叫。


## <a name="related-links"></a>相關連結

- [StartedServicesDemo （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [狀態列圖示](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
