---
title: "Xamarin.Android 與啟動的服務"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: b6c0e67e3411aa5b7846a1b7bc0de2473a3546fd
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="started-services-with-xamarinandroid"></a>Xamarin.Android 與啟動的服務

## <a name="started-services-overview"></a>啟動的服務概觀

啟動的服務通常執行工作的單位，但未提供任何直接回饋或結果給用戶端。 舉例來說，工作是單位的一項服務，將檔案上傳至伺服器。 用戶端將上傳從裝置到網站的檔案服務提出要求。 服務將會無訊息模式將檔案上傳 （即使應用程式在前景中有任何活動），並上傳完成時自動終止。 請務必了解啟動的服務會在應用程式的 UI 執行緒上執行。 這表示，如果服務是要執行的工作，將會封鎖 UI 執行緒，它必須建立並視需要處置的執行緒。

不同於繫結的服務，沒有 「 單純的 」 啟動的服務及其用戶端之間的通訊通道。 這表示已啟動的服務會實作一些不同的生命週期之外方法繫結的服務。 下列清單將強調生命週期中常見的方法啟動服務：

* `OnCreate` &ndash; 呼叫第一次啟動服務時的一次。 這是應該實作初始化程式碼的位置。
* `OnBind` &ndash; 所有的服務類別必須實作這個方法，不過啟動的服務通常也不會有與它繫結的用戶端。 因為這個緣故，已啟動的服務只會傳回`null`。 混合式服務 （這是繫結的服務和啟動的服務的組合） 相較之下，已實作，並傳回`Binder`用戶端。
* `OnStartCommand` &ndash; 針對每個要求啟動的服務呼叫的回應中呼叫`StartService`或重新啟動系統。 這是服務可以開始任何長時間執行工作的位置。 方法會傳回`StartCommandResult`值，指出如何或如果系統應該處理記憶體不足造成關機之後重新啟動服務。 此呼叫會在主執行緒中的進行。 這個方法的以下更詳細說明。
* `OnDestroy` &ndash; 服務正在被終結時，會呼叫這個方法。 它用來執行任何最終清除功能所需。

啟動服務的重要方法是`OnStartCommand`方法。 它會叫用每次服務收到要求執行一些工作。 下列程式碼片段是範例`OnStartCommand`: 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一個參數是`Intent`物件，其中包含要執行之工作的相關中繼資料。 第二個參數會包含`StartCommandFlags`提供一些資訊的方法呼叫的值。 這個參數會有兩個可能值的其中一個：

* `StartCommandFlag.Redelivery` &ndash; 這表示`Intent`是前一次重新傳送`Intent`。 當服務已傳回時，系統會提供這個值`StartCommandResult.RedeliverIntent`但它無法正常關閉前停止。
* `StartCommandFlag.Retry` &dash; 當先前接收到此值`OnStartCommand`呼叫失敗，以及 Android 嘗試重新啟動該服務，與相同意圖，表示為先前的嘗試失敗。
 
最後，第三個參數都是整數值的唯一識別要求的應用程式。 很可能在多個呼叫端可能會叫用同一個服務物件。 這個值用來將停止與啟動服務的給定要求的服務的要求產生關聯。 將一節中詳細討論[停止服務](#Stopping_the_Service)。 

值`StartCommandResult`會傳回到服務為建議 Android 上該怎麼辦服務因為資源限制而終止。 有三個可能的值為`StartCommandResult`:

* **[StartCommandResult.NotSticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.NotSticky/)**  &ndash;這個值會告訴 Android，則不需要重新啟動服務，它已終止。 為此動作的範例，請考慮產生應用程式中的組件庫的縮圖的服務。 如果服務已刪除，不一定要立即重新建立縮圖&ndash;縮圖只能重建的下次執行應用程式。
* **[StartCommandResult.Sticky](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.Sticky/)**  &ndash;這樣做會告知 Android 重新啟動服務，但是不能傳遞已啟動的服務的最後一個意圖。 如果沒有任何擱置中的對應方式，來處理，則`null`將意圖參數提供。 可能的音樂播放機應用程式中; 這個範例。播放音樂，將會重新備妥可啟動服務，但是它會播放最後一首歌。 
* **[StartCommandResult.RedeliverIntent](https://developer.xamarin.com/api/field/Android.App.StartCommandResult.RedeliverIntent/)**  &ndash;這個值就是會告訴重新啟動服務，並重新傳送最後一個 Android `Intent`。 舉例來說，這是下載資料檔與應用程式的服務。 如果服務遭到刪除時，資料檔案仍然需要下載。 藉由傳回`StartCommandResult.RedeliverIntent`，當 Android 它也會提供 （其中包含要下載之檔案的 URL） 的意圖服務重新啟動服務。 這可讓重新啟動或繼續 （取決於實際實作程式碼） 下載。

沒有第四個值`StartCommandResult` &ndash; `StartCommandResult.ContinuationMask`。 這個值由`OnStartCommand`並說明如何 Android 將會繼續使用服務，它已終止。 此值不是通常用來啟動服務。

啟動服務的金鑰生命週期事件會顯示在此圖中： 

![以圖表顯示的順序存留週期方法稱為](started-services-images/started-service-01.png "圖，顯示在其中存留週期方法的呼叫的順序。")


## <a name="stopping-the-service"></a>停止服務

啟動的服務將繼續無限期; 執行Android 會保留服務正在執行，只要有足夠的系統資源。 請在用戶端必須先停止服務，或完成其工作時，服務可能會停止本身。 有兩種方式可停止服務： 
 
* **[Android.Content.Context.StopService()](https://developer.xamarin.com/api/member/Android.Content.Context.StopService/p/Android.Content.Intent/)**  &ndash; （例如活動） 的用戶端可以要求服務停止呼叫`StopService`方法： 

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

* **[Android.App.Service.StopSelf()](https://developer.xamarin.com/api/member/Android.App.Service.StopSelf()/)**  &ndash;服務可能關閉自己叫用`StopSelf`:

    ```csharp
    StopSelf();
    ```
    
### <a name="using-startid-to-stop-a-service"></a>使用 startId 停止服務

多個呼叫端可以要求啟動服務。 如果沒有未處理的啟動要求，服務可以使用`startId`，已傳遞到`OnStartCommand`避免服務過早停止。 `startId`會對應至最新呼叫`StartService`，而且將會遞增每次呼叫時。 因此，如果後續要求`StartService`不具有尚未產生呼叫`OnStartCommand`，服務可以呼叫`StopSelfResult`，將其傳遞的最新的值`startId`已收到 (而不是直接呼叫`StopSelf`)。 如果呼叫`StartService`尚未不已經產生的對應呼叫`OnStartCommand`，系統不會停止服務，因為`startId`用於`StopSelf`呼叫不會對應至最新`StartService`呼叫。


## <a name="related-links"></a>相關連結

- [StartedServicesDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/StartedServicesDemo/)
- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service)
- [Android.App.StartCommandFlags](https://developer.xamarin.com/api/type/Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](https://developer.xamarin.com/api/type/Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](https://developer.xamarin.com/api/type/Android.Content.BroadcastReceiver/)
- [Android.Content.Intent](https://developer.xamarin.com/api/type/Android.Content.Intent)
- [Android.OS.Handler](https://developer.xamarin.com/api/type/Android.OS.Handler/)
- [Android.Widget.Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)
- [狀態列圖示](http://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
