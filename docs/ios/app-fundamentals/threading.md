---
title: 執行緒
ms.prod: xamarin
ms.assetid: 50BCAF3B-1020-DDC1-0339-7028985AAC72
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 63a213a62021923ac6dae8b080f3f8931621251d
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="threading"></a>執行緒

Xamarin.iOS 執行階段可讓開發人員存取.NET 執行緒應用程式開發介面，同時使用執行緒時，明確 (`System.Threading.Thread, System.Threading.ThreadPool`) 以及隱含地使用非同步委派模式或 BeginXXX 方法，以及完整範圍的應用程式開發介面支援工作平行程式庫。



Xamarin 強烈建議您改用[工作平行程式庫](http://msdn.microsoft.com/library/dd460717.aspx)(TPL) 建置應用程式的幾個原因：
-  預設 TPL 排程器會將委派工作執行的執行緒集區，接著會動態地增加處理程序發生時，同時避免太多執行緒最後會競爭 CPU 時間的其中一個案例所需的執行緒數目。 
-  它很容易就能考慮依據 TPL 工作作業。 您可以輕鬆地操作這些、 排程這些、 序列化其執行或啟動許多使用一組豐富的應用程式開發介面的平行。 
-  它是新 C# 非同步語言擴充功能的程式設計基礎。 


執行緒集區緩時變會成長為所需的執行緒數目依據系統、 在系統負載和應用程式要求可用的 CPU 核心數目。 您可以使用此執行緒集區中的方法叫用`System.Threading.ThreadPool`或使用預設`System.Threading.Tasks.TaskScheduler`(屬於*平行架構*)。

通常開發人員使用執行緒，則使用者必須建立應用程式的回應和不想要封鎖執行迴圈的主要 UI 時。

 <a name="Developing_Responsive_Applications" />


## <a name="developing-responsive-applications"></a>開發應用程式的回應

UI 項目存取權應該限制為相同的執行緒正在執行您的應用程式主迴圈。 如果您想要變更的主要 UI 執行緒，您應該排入佇列的程式碼使用[NSObject.InvokeOnMainThread](https://developer.xamarin.com/api/type/Foundation.NSObject/)，如下所示：

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

上述會叫用內的委派，在主執行緒的內容中的程式碼而不會造成任何可能有可能會損毀您的應用程式的競爭情形。

 <a name="Threading_and_Garbage_Collection" />


## <a name="threading-and-garbage-collection"></a>執行緒和記憶體回收

在過程中執行，Objective C 執行階段會建立和釋放物件。 如果物件會標示為 「 自動釋放"Objective C 執行階段就會釋放這些物件之執行緒的目前`NSAutoReleasePool`。 Xamarin.iOS 會建立一個`NSAutoRelease`從每個執行緒集區`System.Threading.ThreadPool`和主執行緒。 此延伸模組所涵蓋使用預設 TaskScheduler System.Threading.Tasks 中建立任何執行緒。

如果您建立您自己使用的執行緒`System.Threading`您需要提供您自己`NSAutoRelease`集區，以防止資料流失。 若要這樣做，包裝您的執行緒，在下列程式碼片段：

```csharp
void MyThreadStart (object arg)
{
   using (var ns = new NSAutoReleasePool ()){
      // Your code goes here.
   }
}
```

附註： 自 Xamarin.iOS 5.2 您沒有提供您自己`NSAutoReleasePool`因為其中一個為您將會自動提供。


## <a name="related-links"></a>相關連結

- [使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)
