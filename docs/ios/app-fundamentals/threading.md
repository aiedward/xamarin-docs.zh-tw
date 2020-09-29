---
title: 在 Xamarin 中進行執行緒
description: 本檔說明如何在 Xamarin iOS 應用程式中使用系統執行緒 Api。 它會討論工作平行程式庫、建立回應式應用程式，以及垃圾收集。
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 30709b9b75c18f954135e950b95094f9ee2d71ac
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435268"
---
# <a name="threading-in-xamarinios"></a>在 Xamarin 中進行執行緒

當使用 `System.Threading.Thread, System.Threading.ThreadPool` 非同步委派模式或 BeginXXX 方法，以及支援工作平行程式庫的完整 api 範圍時，當您使用執行緒 () 和隱含地使用執行緒時，會明確地讓開發人員存取 .net 執行緒 api。

Xamarin 強烈建議您使用工作 [平行程式庫](/dotnet/standard/parallel-programming/task-parallel-library-tpl) (TPL) 來建立應用程式的幾個原因：

- 預設的 TPL 排程器會將工作執行委派給執行緒集區，而執行緒集區會根據進程的需要，以動態方式增加所需的執行緒數目，同時避免發生太多執行緒最後會爭用 CPU 時間的情況。 
- 以 TPL 工作來說，更容易考慮作業。 您可以輕鬆地操作、排程、將其執行序列化，或使用一組豐富的 Api 平行啟動許多。 
- 這是使用新的 c # async 語言延伸模組進行程式設計的基礎。 

執行緒集區會根據系統上的可用 CPU 核心數目、系統負載和您的應用程式需求，慢慢地增加執行緒數目。 您可以使用此執行緒集區，方法是在中叫用方法， `System.Threading.ThreadPool` 或使用 `System.Threading.Tasks.TaskScheduler` *平行* 架構) 的預設 (部分。

開發人員通常會在需要建立回應式應用程式時使用執行緒，而不想封鎖主要的 UI 執行迴圈。

 <a name="Developing_Responsive_Applications"></a>

## <a name="developing-responsive-applications"></a>開發回應式應用程式

UI 元素的存取權應該限制在執行應用程式之主要迴圈的相同執行緒上。 如果您想要從執行緒變更主要 UI，您應該使用 [NSObject](xref:Foundation.NSObject)將程式碼排入佇列，如下所示：

```csharp
MyThreadedRoutine ()  
{  
    var result = DoComputation ();  

    // we want to update an object that is managed by the main
    // thread; To do so, we need to ensure that we only access
    // this from the main thread:

    InvokeOnMainThread (delegate {  
        label.Text = "The result is: " + result;  
    });
}
```

上述程式碼會在主執行緒的內容中叫用委派內的程式碼，而不會造成可能會損毀應用程式的任何競爭情形。

 <a name="Threading_and_Garbage_Collection"></a>

## <a name="threading-and-garbage-collection"></a>執行緒和垃圾收集

在執行過程中，C 執行時間會建立和釋放物件。 如果物件標示為「自動發行」，則目標 C 執行時間會將這些物件釋放至執行緒的目前 `NSAutoReleasePool` 。 Xamarin 會 `NSAutoRelease` 為和主執行緒的每個執行緒建立一個集區 `System.Threading.ThreadPool` 。 此延伸模組涵蓋使用 TaskScheduler 中的預設建立的任何執行緒。

如果您使用建立自己的執行緒， `System.Threading` 則必須提供您自己的 `NSAutoRelease` 集區，以防止資料洩漏。 若要這樣做，只要將您的執行緒包裝在下列程式碼中即可：

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

注意：由於您不再需要自行提供，因此您不再需要提供自己 `NSAutoReleasePool` 的5.2。

## <a name="related-links"></a>相關連結

- [使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)