---
title: Xamarin.Android 意圖服務
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: topgenorth
ms.author: toopge
ms.date: 02/16/2018
ms.openlocfilehash: 80849213649707615f8bd8e941e1a51c6b54e76e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="intent-services-in-xamarinandroid"></a>Xamarin.Android 意圖服務

## <a name="intent-services-overview"></a>意圖服務概觀

同時啟動，且繫結的主執行緒，這表示，若要將效能維持平滑，服務必須以非同步方式執行的工作上執行的服務。 若要解決此問題最簡單的方式之一是與_背景工作佇列處理器模式_、 要進行的工作由單一執行緒服務的佇列中放置的位置。 

[ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/)是子類別的`Service`提供 Android 此模式的特定實作的類別。 它會管理佇列的工作，啟動工作者執行緒來服務佇列，並提取要求關閉背景工作執行緒上執行的佇列。 `IntentService`無訊息模式將會停止本身並沒有其他工作在佇列中的時，移除背景工作執行緒。
 
工作提交至佇列建立`Intent`然後再傳遞`Intent`至`StartService`方法。

不可能停止或中斷`OnHandleIntent`方法`IntentService`運作時。 這項設計，因為`IntentService`應該保持無狀態&ndash;它不應該仰賴依據作用中連線或從應用程式的其餘部分的通訊。 `IntentService`旨在 statelessly 處理工作要求。

有兩個需求的子類別化`IntentService`:

1. 新的類型 (由子類別化`IntentService`) 只會覆寫`OnHandleIntent`方法。
2. 新類型的建構函式需要用來命名背景工作執行緒將會處理要求的字串。 主要在偵錯應用程式時，會使用此背景工作執行緒的名稱。

下列程式碼會示範`IntentService`實作覆寫的`OnHandleIntent`方法：

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

工作會傳送至`IntentService`藉由執行個體化`Intent`，然後再呼叫[ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)與該意圖做為參數的方法。 意圖會做為參數傳遞至服務`OnHandleIntent`方法。 此程式碼片段是將工作要求傳送至用途的範例： 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService`可以從意圖，擷取值，這個程式碼片段所示：  

```csharp
protected override void OnHandleIntent (Android.Content.Intent intent)
{
    string fileToDownload = intent.GetStringExtra("file_to_download");
    
    Log.Debug("DemoIntentService", $"File to download: {fileToDownload}.");
}
```


## <a name="related-links"></a>相關連結

- [IntentService](https://developer.xamarin.com/api/type/Android.App.IntentService/)
- [StartService](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)
