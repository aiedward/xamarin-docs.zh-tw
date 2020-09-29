---
title: 在 Xamarin 中使用 watchOS 導覽
description: 本檔說明如何在 watchOS 應用程式中使用導覽。 它會討論模式介面、階層式導覽和頁面型介面。
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a44d68426ff03ba0b6ab41f57e339caebce62c39
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436715"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>在 Xamarin 中使用 watchOS 導覽

Watch 上最簡單的流覽選項是顯示在目前場景頂端的簡單 [模式快顯視窗](#modal) 。

若為多場景 watch 應用程式，有兩個可用的導覽範例：

- [階層式導覽](#Hierarchical_Navigation)
- [以頁面為基礎的介面](#Page-Based_Interfaces)

<a name="modal"></a>

## <a name="modal-interfaces"></a>強制回應介面

您 `PresentController` 可以使用方法，以強制回應方式開啟介面控制器。 介面控制器必須已在介面中定義 **。** 分鏡腳本。

```csharp
PresentController ("pageController","some context info");
```

以強制回應方式呈現的控制器會使用整個畫面 (涵蓋先前的場景) 。 依預設，[標題] 設定為 [ **取消** ]，並將它關閉控制器。

若要以程式設計方式關閉以強制回應方式顯示的控制器，請呼叫 `DismissController` 。

```csharp
DismissController();
```

模式畫面可以是單一場景，也可以使用以頁面為基礎的版面配置。

<a name="Hierarchical_Navigation"></a>

## <a name="hierarchical-navigation"></a>階層式導覽

提供像是可流覽的堆疊的場景，類似于在 `UINavigationController` iOS 上運作的方式。 場景可以推送至導覽堆疊上，並以程式設計方式或使用者選取的) 來關閉 (。

![場景可以推送至導覽堆疊](navigation-images/hierarchy-1.png) ![場景可以從導覽堆疊中取出](navigation-images/hierarchy-2.png)

就像 iOS 一樣，在階層式導覽堆疊中，左邊緣滑回父控制器。

[WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog)和[WatchTables](/samples/xamarin/ios-samples/watchos-watchtables)範例都包含階層式導覽。

### <a name="pushing-and-popping-in-code"></a>在程式碼中推送和彈出

Watch 套件不需要像 iOS 一樣建立 arching 的「流覽控制器」，只要使用方法推送控制器 `PushController` ，就會自動建立導覽堆疊。

```csharp
PushController("secondPageController","some context info");
```

Watch 的畫面會在左上角包含 **上一頁** 按鈕，但是您也可以使用程式設計方式，從導覽堆疊中移除場景 `PopController` 。

```csharp
PopController();
```

如同 iOS，也可以使用來返回導覽堆疊的根目錄 `PopToRootController` 。

```csharp
PopToRootController();
```

### <a name="using-segues"></a>使用 Segue

您可以在腳本的場景之間建立 segue，以定義階層式導覽。 為了取得目標場景的內容，作業系統會呼叫 `GetContextForSegue` 來初始化新的介面控制器。

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

以頁面為基礎的介面從左至右滑動，類似于 `UIPageViewController` iOS 上的運作方式。 指標點會沿著畫面底部顯示，以顯示目前顯示的頁面。

![範例第一頁](navigation-images/paged-1.png) ![範例第二頁](navigation-images/paged-2.png) ![第五頁範例](navigation-images/paged-5.png)

若要讓以頁面為基礎的介面成為 watch 應用程式的主要 UI，請使用 `ReloadRootControllers` 與介面控制器和內容的陣列：

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

您也可以使用 `PresentController` 應用程式中的另一個場景來呈現非根目錄的頁面型控制器。

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (範例) ](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)