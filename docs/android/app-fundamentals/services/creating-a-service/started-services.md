---
title: 已啟動 Xamarin. Android 的服務
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1b7bed0fc6dba1d9f80524ac3429b7fdcb751ab9
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70755063"
---
# <a name="started-services-with-xamarinandroid"></a>已啟動 Xamarin. Android 的服務

## <a name="started-services-overview"></a>已啟動服務總覽

已啟動的服務通常會執行工作單位，而不會向用戶端提供任何直接的意見反應或結果。 工作單位的範例是將檔案上傳到伺服器的服務。 用戶端會向服務提出要求，將檔案從裝置上傳至網站。 服務會安靜地上傳檔案（即使應用程式在前景沒有活動），並在上傳完成時自行終止。 請務必瞭解，已啟動的服務將會在應用程式的 UI 執行緒上執行。 這表示如果服務要執行將封鎖 UI 執行緒的工作，它必須視需要建立和處置執行緒。

不同于系結服務，「純粹」啟動的服務與其用戶端之間沒有通道。 這表示已啟動的服務將會執行與系結服務不同的生命週期方法。 下列清單強調已啟動服務中的常見生命週期方法：

- 第一次啟動服務時，`OnCreate` &ndash; 呼叫一次。 這是應該在其中執行初始化程式碼的位置。
- `OnBind` &ndash; 這個方法必須由所有服務類別來執行，不過，已啟動的服務通常不會有系結至它的用戶端。 因此，已啟動的服務只會傳回 `null`。 相反地，混合式服務（系結服務和已啟動服務的組合）必須執行並傳回用戶端的 `Binder`。
- `OnStartCommand` &ndash; 為每個啟動服務的要求呼叫，以回應系統的 `StartService` 或重新開機。 這是服務可以開始任何長時間執行之工作的位置。 方法會傳回 `StartCommandResult` 值，指出系統在關閉後，如何或是否應該處理因記憶體不足而重新開機服務。 這個呼叫會在主執行緒上進行。 下面將更詳細地說明此方法。
- `OnDestroy` &ndash; 在終結服務時，會呼叫這個方法。 它是用來執行任何必要的最後清除。

已啟動之服務的重要方法是 `OnStartCommand` 方法。 每次服務收到要求來執行一些工作時，就會叫用它。 下列程式碼片段是 `OnStartCommand` 的範例： 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

第一個參數是 `Intent` 物件，其中包含要執行之工作的中繼資料。 第二個參數包含 `StartCommandFlags` 值，可提供方法呼叫的一些相關資訊。 此參數有兩個可能值的其中一個：

- `StartCommandFlag.Redelivery` &ndash; 這表示 `Intent` 是先前 `Intent` 的重新傳遞。 此值是在服務傳回 `StartCommandResult.RedeliverIntent`，但已停止，才能正確地關閉。
- `StartCommandFlag.Retry` &dash; 當先前的 `OnStartCommand` 呼叫失敗，而 Android 嘗試使用與先前失敗的嘗試相同的意圖來啟動服務時，就會收到此值。

最後，第三個參數是一個整數值，它對識別要求的應用程式而言是唯一的。 多個呼叫端可能會叫用相同的服務物件。 這個值是用來建立要求的關聯，以停止具有給定要求的服務，以啟動服務。 這會在[停止服務](#Stopping_the_Service)一節中更詳細地加以討論。 

服務會傳回值 `StartCommandResult`，做為 Android 的建議，以便在服務因資源條件約束而終止時該怎麼做。 @No__t_0 有三個可能的值：

- **[StartCommandResult. NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash; 此值會告訴 Android，不需要重新開機已終止的服務。 例如，假設有一項服務會在應用程式中產生圖庫的縮圖。 如果服務已終止，則不一定要立即重新建立縮圖 &ndash; 縮圖可以在下一次執行應用程式時重新建立。
- **[StartCommandResult](xref:Android.App.StartCommandResult.Sticky)** &ndash; 這會告訴 Android 重新開機服務，但不會傳遞啟動服務的最後一個意圖。 如果沒有要處理的暫止意圖，則會針對意圖參數提供 `null`。 其中一個範例可能是音樂播放機應用程式;服務將會重新開機以播放音樂，但它會播放最後一首歌曲。
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; 這個值會指示 Android 重新開機服務，然後重新傳遞最後的 `Intent`。 其中一個範例是下載應用程式資料檔案的服務。 如果服務已終止，仍然需要下載資料檔案。 藉由傳回 `StartCommandResult.RedeliverIntent`，當 Android 重新開機服務時，它也會提供意圖（保存要下載之檔案的 URL）給服務。 這可讓下載重新開機或繼續（視程式碼的確切執行而定）。

@No__t_0 &ndash; `StartCommandResult.ContinuationMask` 有第四個值。 此值會由 `OnStartCommand` 傳回，並描述 Android 如何繼續其已終止的服務。 這個值通常不會用來啟動服務。

此圖顯示已啟動服務的主要生命週期事件： 

![顯示生命週期方法呼叫順序的圖表](started-services-images/started-service-01.png "圖表，顯示生命週期方法的呼叫順序。")

<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>停止服務

已啟動的服務會無限期地繼續執行;只要有足夠的系統資源，Android 就會讓服務保持執行狀態。 用戶端必須停止服務，或服務在完成其工作時，可能會自行停止。 有兩種方式可停止服務： 

- **[StopService （）](xref:Android.Content.Context.StopService*)** &ndash; 用戶端（例如活動）可以藉由呼叫 `StopService` 方法來要求服務停止：

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[StopSelf （）](xref:Android.App.Service.StopSelf*)** &ndash; 服務會藉由叫用 `StopSelf` 來自行關閉：

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>使用 startId 停止服務

多個呼叫端可以要求服務啟動。 如果有未處理的啟動要求，服務可以使用傳入 `OnStartCommand` 的 `startId`，以防止服務過早停止。 @No__t_0 會對應至 `StartService` 的最新呼叫，而且每次被呼叫時都會遞增。 因此，如果 `StartService` 的後續要求尚未造成呼叫 `OnStartCommand`，服務就可以呼叫 `StopSelfResult`，並將收到的最新 `startId` 值（而不只是呼叫 `StopSelf`）傳遞給它。 如果 `StartService` 的呼叫尚未導致對應的 `OnStartCommand` 呼叫，系統將不會停止服務，因為 `StopSelf` 呼叫中使用的 `startId` 不會對應到最新的 `StartService` 呼叫。

## <a name="related-links"></a>相關連結

- [StartedServicesDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android 應用程式服務](xref:Android.App.Service)
- [StartCommandFlags](xref:Android.App.StartCommandFlags)
- [StartCommandResult](xref:Android.App.StartCommandResult)
- [BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android. Content. 意圖](xref:Android.Content.Intent)
- [Android. OS. 處理常式](xref:Android.OS.Handler)
- [Android. Widget](xref:Android.Widget.Toast)
- [狀態列圖示](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
