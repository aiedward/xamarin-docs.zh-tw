---
title: 在 Xamarin 中使用 watchOS 導覽
description: 本檔說明如何在 watchOS 應用程式中使用導覽。 它討論了強制回應介面、階層式導覽和頁面型介面。
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 8b1ffe54ad199acbc7bf3918c187e198d96b51c8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939395"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>在 Xamarin 中使用 watchOS 導覽

監看式上提供的最簡單導覽選項是出現在目前場景上方的簡單強制回應[快顯視窗](#modal)。

針對多場景監看式應用程式，有兩個可用的導覽範例：

- [階層式導覽](#Hierarchical_Navigation)
- [以頁面為基礎的介面](#Page-Based_Interfaces)

<a name="modal"></a>

## <a name="modal-interfaces"></a>強制回應介面

使用 `PresentController` 方法來以模式開啟介面控制器。 介面控制器必須已在介面中定義 **。** 分鏡腳本。

```csharp
PresentController ("pageController","some context info");
```

以模式顯示的控制器會使用整個畫面（涵蓋前一個場景）。 標題預設會設定為 [**取消**]，並將其關閉。

若要以程式設計方式關閉已強制回應的控制器，請呼叫 `DismissController` 。

```csharp
DismissController();
```

模式畫面可以是單一場景，或使用以頁面為基礎的版面配置。

<a name="Hierarchical_Navigation"></a>

## <a name="hierarchical-navigation"></a>階層式導覽

呈現可以流覽回到堆疊的場景，類似于在 `UINavigationController` iOS 上運作的方式。 場景可以推送到導覽堆疊上，並將其關閉（以程式設計方式或由使用者選擇）。

![場景可以推送至導覽堆疊](navigation-images/hierarchy-1.png) ![可以從導覽堆疊中取出場景](navigation-images/hierarchy-2.png)

如同 iOS，在階層式導覽堆疊中，左邊緣滑動會流覽回父控制器。

[WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)和[WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)範例都包含階層式導覽。

### <a name="pushing-and-popping-in-code"></a>在程式碼中推送和彈出

監看套件不需要像 iOS 一樣建立 arching 的「流覽控制器」，只要使用方法推送控制器 `PushController` ，就會自動建立導覽堆疊。

```csharp
PushController("secondPageController","some context info");
```

監看式的畫面會在左上方包含 [**上一頁**] 按鈕，但您也可以使用，以程式設計方式從導覽堆疊中移除場景 `PopController` 。

```csharp
PopController();
```

如同 iOS，您也可以使用來返回流覽堆疊的根目錄 `PopToRootController` 。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segue

您可以在分鏡腳本中的場景之間建立 segue，以定義階層式導覽。 為了取得目標場景的內容，作業系統會呼叫 `GetContextForSegue` 來初始化新的介面控制器。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"></a>

## <a name="page-based-interfaces"></a>以頁面為基礎的介面

以頁面為基礎的介面從左至右滑動，類似于 `UIPageViewController` iOS 的運作方式。 指標點會沿著畫面底部顯示，以顯示目前顯示的頁面。

![範例第一頁](navigation-images/paged-1.png) ![範例第二頁](navigation-images/paged-2.png) ![第五頁範例](navigation-images/paged-5.png)

若要讓以頁面為基礎的介面成為監看式應用程式的主要 UI，請搭配 `ReloadRootControllers` 介面控制器和內容的陣列使用：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

您也可以 `PresentController` 從應用程式中的另一個場景，呈現不是根目錄的頁面型控制器。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables （範例）](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
