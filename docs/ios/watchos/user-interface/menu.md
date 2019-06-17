---
title: watchOS 功能表控制項 （強制碰觸） 在 Xamarin 中
description: 本文件說明如何在 Xamarin 中使用 watchOS 強制觸控手勢。 它討論如何回應用力長按如何加入功能表，並變更功能表項目。
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 7696c820ab6fdf19bdef46db31061fb5914e6cf4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880673"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>watchOS 功能表控制項 （強制碰觸） 在 Xamarin 中

監看式套件提供強制觸控筆勢，以觸發監看式應用程式畫面上實作時的功能表。

![](menu-images/menu.png "在顯示功能表的 Apple Watch")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>回應用力長按

如果`Menu`已實作介面控制器，當使用者執行將會顯示功能表強制觸控。 如果已實作沒有功能表，畫面簡短地以動畫顯示的任何其他動作，就會發生。

強制修飾未與任何畫面; 上的特定項目相關聯只將一個菜可以附加至介面控制器，它會顯示不論強制觸控按下螢幕上發生的位置。

介於一到四個功能表可以呈現選項。


## <a name="adding-a-menu"></a>新增功能表

A`Menu`必須新增至`InterfaceController`在設計階段將分鏡腳本。 功能表控制項拖曳至介面控制器時不在分鏡腳本預覽視覺指示，但 **功能表** 會出現在 **文件大綱** 板：

![](menu-images/menu-action.png "在設計階段編輯功能表")

最多四個功能表項目可以加入功能表控制項。 在設定這些**屬性**板。 您可以設定下列屬性：

- 標題和
- 自訂映像，或
- 系統映像：接受、 新增區塊、 拒絕、 資訊，也許，靜音、 暫停、 播放、 重複、 繼續、 共用、 隨機播放、 喇叭、 資源回收筒。

建立`Action`藉由選取**事件**一節**屬性**板，然後輸入動作方法的名稱。 部分方法將會建立在程式碼，可以實作在介面控制器類別中，像這樣：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自訂映像

類似於在 iOS 中的映像 索引標籤，功能表項目影像需要使用不透明的模式，使用 alpha 色頻，可讓透過顯示背景。

您應該加入監看式應用程式專案 （不監看式應用程式擴充功能專案） 為了達到最佳效能的功能表所用的影像。


## <a name="changing-the-menu-items"></a>變更功能表項目

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在執行階段加入

您不會導致`Menu`若要新增至介面控制器在執行階段，不過的集合`MenuItem`s*可以*利用程式設計的方式。
使用`AddMenuItem`方法所示：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Xamarin.iOS 監看式套件 API 目前需要`selector`針對`AdMenuItem`方法，應該宣告如下：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>移除在執行階段

`ClearAllMenuItems`方法可以呼叫以移除所有*以程式設計方式加入*功能表項目。

無法清除設定分鏡腳本中的功能表項目。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 功能表文件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
