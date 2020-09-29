---
title: 使用 Xamarin 啟動服務
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 6075b125f36625a8dec12c041631e3794a71cc6a
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455362"
---
# <a name="started-services-with-xamarinandroid"></a>使用 Xamarin 啟動服務

## <a name="started-services-overview"></a>已啟動服務總覽

已啟動的服務通常會執行工作單位，而不會對用戶端提供任何直接的意見反應或結果。 工作單位的範例是將檔案上傳至伺服器的服務。 用戶端會向服務提出要求，以將檔案從裝置上傳至網站。 即使應用程式在前景) 中沒有任何活動，服務也會將檔案安靜上傳 (，並在上傳完成時終止本身。 請務必瞭解，啟動的服務將會在應用程式的 UI 執行緒上執行。 這表示如果服務要執行將封鎖 UI 執行緒的工作，則必須視需要建立和處置執行緒。

與系結服務不同的是，「單純」啟動的服務及其用戶端之間沒有任何通道。 這表示已啟動的服務將會執行一些與系結服務不同的生命週期方法。 下列清單強調已啟動服務中的一般生命週期方法：

- `OnCreate`在 &ndash; 第一次啟動服務時呼叫一次。 這是應該實作為初始化程式碼的地方。
- `OnBind`&ndash;此方法必須由所有服務類別來執行，但已啟動的服務通常不會有與其系結的用戶端。 因此，啟動的服務只會傳回 `null` 。 相反地，混合式服務 (是系結服務和已啟動服務的組合) 必須 `Binder` 針對用戶端執行和傳回。
- `OnStartCommand`&ndash;針對每個啟動服務的要求呼叫，以回應系統的呼叫 `StartService` 或重新開機。 這是服務可以開始任何長時間執行之工作的地方。 方法會傳回一個  `StartCommandResult` 值，這個值會指出系統是否應該在關機後（因為記憶體不足）來重新開機服務。 此呼叫會在主執行緒上進行。 以下將更詳細說明這個方法。
- `OnDestroy`&ndash;當服務被終結時，會呼叫這個方法。 它是用來執行任何必要的最後清除。

已啟動服務的重要方法是 `OnStartCommand` 方法。 每次服務收到執行某些工作的要求時，就會叫用它。 下列程式碼片段為範例 `OnStartCommand` ： 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一個參數是一個 `Intent` 物件，其中包含要執行之工作的相關中繼資料。 第二個參數包含的 `StartCommandFlags` 值提供方法呼叫的一些相關資訊。 此參數具有兩個可能值的其中一個：

- `StartCommandFlag.Redelivery`&ndash;這表示 `Intent` 是先前的重新傳遞 `Intent` 。 此值是在服務已傳回但在 `StartCommandResult.RedeliverIntent` 可以正常關閉之前停止的情況下提供。
-`StartCommandFlag.Retry`&dash;當先前的 `OnStartCommand` 呼叫失敗，而且 Android 嘗試以與先前嘗試失敗的相同意圖再次啟動服務時，就會收到此值。

最後，第三個參數是一個整數值，這個值對識別要求的應用程式而言是唯一的。 多個呼叫端可能會叫用相同的服務物件。 這個值是用來建立要求的關聯，以使用指定的要求啟動服務來停止服務。 在 [停止服務](#Stopping_the_Service)的區段中，將會更詳細地討論。 

`StartCommandResult`服務會傳回值做為 Android 的建議，在服務因資源限制而終止時該怎麼辦。 有三個可能的值 `StartCommandResult` ：

- **[StartCommandResult。 NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash; 此值會告知 Android 不需要重新開機已終止的服務。 舉例來說，請考慮在應用程式中產生資源庫縮圖的服務。 如果服務已終止，則在 &ndash; 下次執行應用程式時，可以重新建立縮圖，並不重要。
- **[StartCommandResult](xref:Android.App.StartCommandResult.Sticky)** &ndash; ，這會告知 Android 重新開機服務，但不提供啟動服務的最後一個意圖。 如果沒有待處理的意圖，則 `null` 會為意圖參數提供。 其中一個範例可能是音樂播放機應用程式;服務將會重新開機，以播放音樂，但它將會播放上一首歌。
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; 此值會告知 Android 重新開機服務並重新傳遞最後一個 `Intent` 。 其中一個範例就是下載應用程式資料檔案的服務。 如果終止服務，仍需要下載資料檔。 藉由傳回 `StartCommandResult.RedeliverIntent` ，當 Android 重新開機服務時，它也會提供意圖 (，其中包含要下載) 至服務之檔案的 URL。 這可讓下載重新開機或繼續 (，視程式碼) 的確切執行而定。

有四個值 `StartCommandResult` &ndash; `StartCommandResult.ContinuationMask` 。 這個值是由所傳回 `OnStartCommand` ，它會說明 Android 如何繼續它所終止的服務。 此值通常不會用來啟動服務。

下圖顯示已啟動服務的主要生命週期事件： 

![顯示生命週期方法呼叫順序的圖表](started-services-images/started-service-01.png "圖表，顯示呼叫生命週期方法的順序。")

<a name="Stopping_the_Service"></a>

## <a name="stopping-the-service"></a>停止服務

已啟動的服務會無限期地持續執行;只要有足夠的系統資源，Android 就會讓服務保持執行狀態。 用戶端必須停止服務，否則服務可能會在工作完成時自行停止。 有兩種方式可停止服務： 

- **[StopService ( # B1 ](xref:Android.Content.Context.StopService*)** &ndash; 用戶端 (例如活動) 可以藉由呼叫方法來要求服務停止 `StopService` ：

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[StopSelf ( # B1 ](xref:Android.App.Service.StopSelf*)** &ndash; 服務可能會藉由叫用下列方法來自行關閉 `StopSelf` ：

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>使用 startId 停止服務

多個呼叫端可以要求服務啟動。 如果有未處理的啟動要求，服務可以使用傳入的， `startId` `OnStartCommand` 以防止服務被提前停止。 `startId`會對應至的最新呼叫 `StartService` ，並且會在每次呼叫時遞增。 因此，如果的後續要求尚未 `StartService` 產生的呼叫 `OnStartCommand` ，服務可以呼叫 `StopSelfResult` ，將 (它的最新值傳遞給它， `startId` 而不只是呼叫 `StopSelf`) 。 如果的呼叫尚未 `StartService` 產生對應的呼叫 `OnStartCommand` ，系統將不會停止服務，因為 `startId` 呼叫中使用的 `StopSelf` 不會對應至最新的 `StartService` 呼叫。

## <a name="related-links"></a>相關連結

- [StartedServicesDemo (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android. App. 服務](xref:Android.App.Service)
- [StartCommandFlags](xref:Android.App.StartCommandFlags)
- [StartCommandResult](xref:Android.App.StartCommandResult)
- [>broadcastreceiver](xref:Android.Content.BroadcastReceiver)
- [Android. Content-type](xref:Android.Content.Intent)
- [Android. 處理常式](xref:Android.OS.Handler)
- [Android Widget](xref:Android.Widget.Toast)
- [狀態列圖示](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)