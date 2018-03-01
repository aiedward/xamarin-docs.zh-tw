---
title: "使用 瀏覽"
ms.topic: article
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 79277d412b87e6ac44557122fa06d4d5d873fd38
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-navigation"></a>使用 瀏覽

最簡單瀏覽可用的選項上監看式是簡單[強制回應的快顯](#modal)出現在目前的場景之上。

如有多個場景監看式應用程式的兩個可用的瀏覽開發架構：

- [階層式導覽](#Hierarchical_Navigation)
- [頁面為基礎的介面](#Page-Based_Interfaces)

## <a name="modal-interfaces"></a>強制回應的介面

使用`PresentController`方法來開啟介面控制站，以強制回應方式。 介面控制站必須已定義在**Interface.storyboard**。

```csharp
PresentController ("pageController","some context info");
```

要強制顯示控制站使用全螢幕 （涵蓋先前場景）。 根據預設，標題會設定為**取消**點選它將會關閉控制站。

若要以程式設計方式關閉以強制回應方式顯示控制站，請呼叫`DismissController`。

```csharp
DismissController();
```

強制回應螢幕可以是單一窗定位在場景或使用頁面為基礎的配置。


## <a name="hierarchical-navigation"></a>階層式導覽

呈現的場景，像堆疊可巡覽回透過、 類似的方式一樣`UINavigationController`可在 iOS 上運作。 場景可以推入至巡覽堆疊及推出 （以程式設計方式或依使用者選取範圍）。

![](navigation-images/hierarchy-1.png "場景可以放入巡覽堆疊") ![ ](navigation-images/hierarchy-2.png "運作原理可以從瀏覽堆疊取出")

如同 iOS 左邊緣撥動巡覽回父控制器階層式巡覽堆疊中。

這兩個[WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog)和[WatchTables](https://developer.xamarin.com/samples/WatchTables)範例包含階層式導覽。

### <a name="pushing-and-popping-in-code"></a>發送，以及在程式碼中移除

監看套件不需要過度 arching 的 「 瀏覽控制器 」 類似 iOS-建立直接推送控制器使用`PushController`方法和巡覽堆疊將會自動建立。

```csharp
PushController("secondPageController","some context info");
```

監看的螢幕會包含**回**按鈕在左上方，但是您可以從瀏覽堆疊使用也以程式設計方式移除場景`PopController`。

```csharp
PopController();
```

做為使用 iOS，它也可能返回瀏覽堆疊使用的根`PopToRootController`。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segues

Segues 可由場景中定義的階層式導覽分鏡腳本之間。 若要取得目標場景，作業系統呼叫內容`GetContextForSegue`來初始化新的介面控制站。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

## <a name="page-based-interfaces"></a>頁面為基礎的介面

頁面為基礎的介面撥動左到右，其方式類似於`UIPageViewController`可在 iOS 上運作。 點指示器會顯示螢幕才能顯示目前顯示哪一頁的底部。

![](navigation-images/paged-1.png "範例的第一頁") ![ ](navigation-images/paged-2.png "範例的第二頁") ![ ](navigation-images/paged-5.png "範例的第五頁")


若要讓頁面為基礎的介面監看式應用程式的主要 UI，使用`ReloadRootControllers`介面控制站及內容的陣列：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

您也可以呈現的頁面為基礎的控制站，不是根目錄使用`PresentController`從其他應用程式中的場景的其中一個。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [WatchTables （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchTables/)
