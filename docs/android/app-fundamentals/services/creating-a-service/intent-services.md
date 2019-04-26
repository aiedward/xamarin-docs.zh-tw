---
title: 在 Xamarin.Android 中 intent 服務
ms.prod: xamarin
ms.assetid: A5B86FE4-C8E2-4B0A-84CA-EF8F5119E31B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1301f34ad1f7a0069c542ba81bf237a673fd239d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61013112"
---
# <a name="intent-services-in-xamarinandroid"></a>在 Xamarin.Android 中 intent 服務

## <a name="intent-services-overview"></a>Intent 服務概觀

同時啟動，而且繫結的主執行緒，這表示，若要將效能維持平滑，服務必須以非同步方式執行的工作上執行的服務。 若要解決這個問題最簡單的方法之一是使用_背景工作佇列處理器模式_、 完成的工作由單一執行緒服務的佇列中放置的位置。 

[ `IntentService` ](https://developer.xamarin.com/api/type/Android.App.IntentService/)是子類別的`Service`提供 Android 特定實作此模式的類別。 它會管理佇列工作，啟動背景工作執行緒來服務佇列，並提取要求關閉佇列背景工作執行緒上執行。 `IntentService`以無訊息模式將會停止本身並沒有其他工作佇列中的時，移除背景工作執行緒。
 
藉由建立工作提交至佇列`Intent`然後再傳遞`Intent`到`StartService`方法。

不可能停止或中斷`OnHandleIntent`方法`IntentService`時它是否能運作。 這項設計，因為`IntentService`應該保持無狀態&ndash;它不應該仰賴依據作用中連線或通訊的應用程式的其餘部分。 `IntentService`旨在 statelessly 處理工作要求。

有兩個需求的子類別化`IntentService`:

1. 新的型別 (由子類別化`IntentService`) 只會覆寫`OnHandleIntent`方法。
2. 新類型的建構函式需要用來命名背景工作執行緒將會處理要求的字串。 偵錯應用程式時，主要用於此背景工作執行緒的名稱。

下列程式碼示範`IntentService`實作覆寫的`OnHandleIntent`方法：

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

工作會傳送至`IntentService`藉由執行個體化`Intent`，然後再呼叫[ `StartService` ](https://developer.xamarin.com/api/member/Android.Content.Context.StartService/p/Android.Content.Intent/)與，意圖做為參數的方法。 意圖會以參數傳遞至服務`OnHandleIntent`方法。 此程式碼片段是傳送到意圖的工作要求的範例： 

```csharp
// This code might be called from within an Activity, for example in an event
// handler for a button click.
Intent downloadIntent = new Intent(this, typeof(DemoIntentService));

// This is just one example of passing some values to an IntentService via the Intent:
downloadIntent.Put
("file_to_download", "http://www.somewhere.com/file/to/download.zip");

StartService(downloadIntent);
```

`IntentService`可以意圖，從擷取的值，此程式碼片段所示：  

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
