---
title: 在 Xamarin.iOS 中執行緒
description: 本文件說明如何使用 System.Threading Api 的 Xamarin.iOS 應用程式。 它討論工作平行程式庫，建置回應迅速的應用程式，以及記憶體回收。
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/05/2017
ms.openlocfilehash: 8e4ee10fdabdcbb4c6cefe02b15dc93459708364
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350416"
---
# <a name="threading-in-xamarinios"></a>在 Xamarin.iOS 中執行緒

Xamarin.iOS 執行階段可讓開發人員存取.NET 執行緒同時使用執行緒時，明確的 Api (`System.Threading.Thread, System.Threading.ThreadPool`) 以及隱含地使用非同步委派模式或 BeginXXX 方法，以及完整的 Api 支援工作平行程式庫。



Xamarin 強呤魽您畷樾[工作平行程式庫](http://msdn.microsoft.com/library/dd460717.aspx)(TPL) 來建置應用程式有幾個原因：
-  預設 TPL 排程器會將委派工作執行的執行緒集區，接著會動態成長的程序進行，同時避免過多執行緒最後會競爭 CPU 時間的其中一個案例所需的執行緒數目。 
-  它是您更輕鬆地思考 TPL 工作方面的作業。 輕鬆地可以操作這些、 排程、 序列化其執行或啟動許多與一組豐富的 Api。 
-  它是使用的新 C# async 語言擴充功能進行程式設計基礎。 


執行緒集區會緩時變成長根據系統、 系統負載和應用程式要求可用的 CPU 核心數目，視需要的執行緒數目。 您可以藉由叫用方法中的使用此執行緒集區`System.Threading.ThreadPool`或使用預設值`System.Threading.Tasks.TaskScheduler`(屬於*平行處理架構*)。

通常開發人員使用的執行緒，他們必須建立應用程式的回應，並不想要封鎖執行迴圈的主要 UI 時。

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>開發應用程式的回應

UI 項目的存取權應該限制為相同的執行緒執行您的應用程式的主迴圈。 如果您想要變更的主要 UI 執行緒，您應該使用佇列的程式碼[NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/)，如下所示：

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

上述會叫用內的主執行緒中，內容中的委派的程式碼而不會造成您的應用程式可能就會當機的任何競爭狀況。

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>執行緒和記憶體回收

在過程中執行時，OBJECTIVE-C 執行階段會建立和釋放物件。 如果物件會標示為 「 自動-發行 」 OBJECTIVE-C 執行階段會釋放這些物件的目前執行緒`NSAutoReleasePool`。 Xamarin.iOS 會建立一個`NSAutoRelease`從每個執行緒集區`System.Threading.ThreadPool`和主執行緒。 此擴充功能所涵蓋使用預設 TaskScheduler System.Threading.Tasks 中建立任何執行緒。

如果您建立您自己使用的執行緒`System.Threading`您需要提供您自己`NSAutoRelease`集區，以防止資料洩漏。 若要這樣做，包裝您的執行緒，在下列程式碼片段：

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

注意： 自 Xamarin.iOS 5.2 您不必提供您自己`NSAutoReleasePool`因為其中一個為您將會自動提供。


## <a name="related-links"></a>相關連結

- [使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)
