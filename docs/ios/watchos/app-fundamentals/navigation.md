---
title: 使用 watchOS 在 Xamarin 中瀏覽
description: 本文件說明如何使用 watchOS 應用程式中的導覽。 它討論強制回應介面、 階層式導覽，以及頁面為基礎的介面。
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 0f087e4ce8fac2d86d45b6a27dc00c3fe4ad18db
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61412705"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>使用 watchOS 在 Xamarin 中瀏覽

最簡單瀏覽可用的選項上監看式是一項簡單[強制回應快顯](#modal)，會顯示在目前的場景。

有多場景 watch 應用程式的兩個可用的瀏覽範例：

- [階層式導覽](#Hierarchical_Navigation)
- [頁面為基礎的介面](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>強制回應介面

使用`PresentController`以強制回應方式開啟介面控制器的方法。 介面控制器必須已經定義於**Interface.storyboard**。

```csharp
PresentController ("pageController","some context info");
```

要強制顯示控制站會使用全螢幕 （涵蓋先前場景）。 根據預設，標題設定為**取消**和點選它將會關閉控制站。

若要以程式設計方式關閉要強制顯示控制站，請呼叫`DismissController`。

```csharp
DismissController();
```

強制回應的畫面可以是單一的場景或使用頁面為基礎的配置。

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>階層式導覽

顯示場景，例如可透過巡覽，其方式類似於堆疊`UINavigationController`在 iOS 上的運作方式。 場景可以推送至導覽堆疊，並拉 （以程式設計方式或依使用者選取範圍）。

![](navigation-images/hierarchy-1.png "場景可以放入巡覽堆疊") ![](navigation-images/hierarchy-2.png "運作原理可以從瀏覽堆疊取出")

如同 iOS 左邊緣撥動向後巡覽至的階層式導覽堆疊中的父控制站。

這兩個[WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog)並[WatchTables](https://developer.xamarin.com/samples/WatchTables)範例包括階層式導覽。

### <a name="pushing-and-popping-in-code"></a>推進和拉出程式碼中

觀看套件不需要過度 arching 的 「 瀏覽控制器 」 會建立類似 iOS-只要推送控制器使用`PushController`方法，並瀏覽堆疊會自動建立。

```csharp
PushController("secondPageController","some context info");
```

監看式的畫面會包含**回復**左上方，但是您的按鈕可以從導覽堆疊使用也以程式設計方式移除場景`PopController`。

```csharp
PopController();
```

即使用 iOS，也能以返回 瀏覽堆疊使用的根`PopToRootController`。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segue

Segue 可以在分鏡腳本來定義階層式導覽中的場景之間建立。 針對目標場景，作業系統呼叫來取得內容`GetContextForSegue`來初始化新的介面控制站。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```
<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>頁面為基礎的介面

頁面型介面撥動左到右，其方式類似於`UIPageViewController`在 iOS 上的運作方式。 指標點會顯示以顯示頁面目前顯示畫面的底部。

![](navigation-images/paged-1.png "範例的第一頁") ![](navigation-images/paged-2.png "範例的第二頁") ![](navigation-images/paged-5.png "範例的第五頁")


若要讓頁面為基礎的介面監看式應用程式的主要 UI，使用`ReloadRootControllers`介面控制器以及內容的陣列：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

您也會顯示一個頁面為基礎的控制站，不是根使用`PresentController`其中一個應用程式中其他的場景。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [WatchTables （範例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
