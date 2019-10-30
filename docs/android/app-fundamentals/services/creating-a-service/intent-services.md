---
title: Xamarin 中的意圖服務
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: c58787a051bfc965cb7493138ed6114ac23ed04d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024851"
---
# <a name="intent-services-in-xamarinandroid"></a>Xamarin 中的意圖服務

## <a name="intent-services-overview"></a>意圖服務總覽

啟動和系結的服務都是在主執行緒上執行，這表示為了讓效能保持順暢，服務必須以非同步方式執行工作。 解決此問題最簡單的方法之一，就是使用背景_工作佇列處理器模式_，其中要完成的工作會放在由單一線程服務的佇列中。

[`IntentService`](xref:Android.App.IntentService)是 `Service` 類別的子類別，可提供此模式的 Android 特定執行。 它會管理佇列工作、啟動工作者執行緒來服務佇列，以及從要在背景工作執行緒上執行的佇列提取要求。 `IntentService` 會在佇列中沒有其他工作時，以安靜的方式停止自己，並移除背景工作執行緒。

工作會藉由建立 `Intent`，然後將該 `Intent` 傳遞至 `StartService` 方法，來提交給佇列。

當 `OnHandleIntent` 方法正在運作時，無法停止或中斷其 `IntentService`。 基於此設計，`IntentService` 應該保持無狀態 &ndash; 它不應該依賴作用中的連接或來自應用程式其餘部分的通訊。 `IntentService` 的目的是要 statelessly 處理工作要求。

子類別化 `IntentService`有兩個需求：

1. 新的類型（由子類別 `IntentService`所建立）只會覆寫 `OnHandleIntent` 方法。
2. 新類型的函式需要一個字串，用來命名將處理要求的背景工作執行緒。 此背景工作執行緒的名稱主要是在偵錯工具時使用。

下列程式碼顯示具有覆寫之 `OnHandleIntent` 方法的 `IntentService` 執行：

```csharp
[Service]
public class DemoIntentService: IntentService
{
    public DemoIntentService () : base("DemoIntentService")
    {
    }

    protected override void OnHandleIntent (Android.Content.Intent intent)
    {
        Console.WriteLine ("perform some long running work");
        ...
        Console.WriteLine ("work complete");
    }
}
```

工作會藉由具現化 `Intent`，然後以該意圖呼叫[`StartService`](xref:Android.Content.Context.StartService*)方法做為參數，來傳送至 `IntentService`。 意圖會當做 `OnHandleIntent` 方法中的參數傳遞至服務。 此程式碼片段是將工作要求傳送至意圖的範例： 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService` 可以從意圖中解壓縮值，如下列程式碼片段所示：  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");

    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```

## <a name="related-links"></a>相關連結

- [IntentService](xref:Android.App.IntentService)
- [StartService](xref:Android.Content.Context.StartService*)
