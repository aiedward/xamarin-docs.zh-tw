---
title: Xamarin 中的執行緒
description: 本檔說明如何在 Xamarin iOS 應用程式中使用系統執行緒 Api。 它討論工作平行程式庫、建立回應式應用程式和垃圾收集。
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 0de7fcd5af9e0338679893b3d7fde073c5274365
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567710"
---
# <a name="threading-in-xamarinios"></a>Xamarin 中的執行緒

當使用執行緒（ `System.Threading.Thread, System.Threading.ThreadPool` ）並在使用非同步委派模式或 BeginXXX 方法，以及支援工作平行程式庫的完整 api 範圍時，此 Xamarin 執行時間可讓開發人員直接存取 .net 執行緒 api。

Xamarin 強烈建議您使用工作[平行程式庫](https://msdn.microsoft.com/library/dd460717.aspx)（TPL）來建立應用程式，原因如下：

- 預設的 TPL 排程器會將工作執行委派給執行緒集區，接著會動態地增加處理常式所需的執行緒數目，同時避免太多執行緒最後會爭用 CPU 時間的情況。 
- 以 TPL 工作的角度來思考作業比較容易。 您可以輕鬆地進行操作、排程、序列化其執行，或以一組豐富的 Api 平行啟動許多。 
- 這是使用新的 c # 非同步語言擴充功能進行程式設計的基礎。 

執行緒集區會根據系統上可用的 CPU 核心數目、系統負載和您的應用程式需求，慢慢地增加所需的執行緒數目。 您可以使用這個執行緒集區，方法是在中叫用方法， `System.Threading.ThreadPool` 或使用預設值 `System.Threading.Tasks.TaskScheduler` （*平行*架構的一部分）。

開發人員通常會在需要建立回應式應用程式時使用執行緒，而不想封鎖主要的 UI 執行迴圈。

 <a name="Developing_Responsive_Applications"></a>

## <a name="developing-responsive-applications"></a>開發回應式應用程式

UI 元素的存取權應該限制為對您的應用程式執行主要迴圈的相同執行緒。 如果您想要從執行緒對主要 UI 進行變更，您應該使用[NSObject](xref:Foundation.NSObject)將程式碼排入佇列，如下所示：

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

上面的會在主執行緒的內容中叫用委派內的程式碼，而不會造成可能會損毀您的應用程式的任何競爭情形。

 <a name="Threading_and_Garbage_Collection"></a>

## <a name="threading-and-garbage-collection"></a>執行緒和垃圾收集

在執行過程中，目標-C 執行時間會建立和發行物件。 如果物件已標示為「自動發行」，則目標-C 執行時間會將這些物件釋放給執行緒的目前 `NSAutoReleasePool` 。 `NSAutoRelease` `System.Threading.ThreadPool` 針對從和主執行緒的每個執行緒，iOS 建立一個集區。 這項擴充功能涵蓋了使用 TaskScheduler 中的預設值建立的任何執行緒。

如果您使用建立自己的執行緒， `System.Threading` 就必須提供自己的 `NSAutoRelease` 集區，以防止資料洩漏。 若要這樣做，只要將您的執行緒包裝在下面這段程式碼中即可：

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

注意：因為 Xamarin. iOS 5.2，所以您不需要再提供您自己的帳戶，因為系統會 `NSAutoReleasePool` 自動為您提供一個。

## <a name="related-links"></a>相關連結

- [使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)
