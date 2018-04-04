---
title: 功能表控制項 （強制觸控）
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 4895f140acbc8a622cfb91c2fe9db6c5e9d9d1d7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="menu-control-force-touch"></a>功能表控制項 （強制觸控）

監看式套件會提供觸發程序時實作監看式應用程式畫面上的功能表強制觸控筆勢。

![](menu-images/menu.png "Apple Watch 顯示功能表")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>回應強制觸控

如果`Menu`使用者執行 Force，觸控功能表會顯示已實作介面控制站。 如果已實作沒有功能表，螢幕會簡短動畫會執行任何其他動作。

強制修飾不會與在畫面上的任何特定項目產生關聯只有一個功能表可以連結到介面控制器，因此不會出現不論強制觸控按下螢幕上發生的位置。

一個和第四個功能表之間可以呈現選項。


## <a name="adding-a-menu"></a>新增功能表

A`Menu`必須新增至`InterfaceController`在設計階段分鏡腳本。 當功能表控制項拖曳到介面控制站上沒有分鏡腳本 preview 上的沒有視覺指示但**功能表**會出現在**文件大綱**板：

![](menu-images/menu-action.png "在設計階段編輯功能表")

最多四個功能表項目可以加入功能表控制項。 可在設定**屬性**填補。 您可以設定下列屬性：

- 標題，並
- 自訂映像，或
- 系統映像： 接受，請新增、 區塊、 拒絕、 資訊，有可能，詳細資訊，將設為靜音、 暫停、 播放、 重複、 繼續、 共用、 隨機播放、 喇叭、 資源回收筒。

建立`Action`選取**事件**區段**屬性**填補，並輸入動作方法的名稱。 部分方法將會建立在程式碼中，可以實作在介面控制器類別中，像這樣：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自訂映像

類似於索引標籤映像，在 iOS 中，功能表項目影像需要具有 alpha 色板，讓背景透過顯示的不透明模式。

您應該加入監看式應用程式專案 （而非監看式應用程式擴充功能專案） 為了達到最佳效能的功能表所用的影像。


## <a name="changing-the-menu-items"></a>變更功能表項目

<!--
### Design Time Items

Menu items added the the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在執行階段加入

不會導致`Menu`雖然介面控制站，在執行階段，加入的集合`MenuItem`s*可以*以程式設計的方式改變。
使用`AddMenuItem`方法所示：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Xamarin.iOS 監看式套件 API 目前需要`selector`如`AdMenuItem`方法，應該宣告如下：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>移除在執行階段

`ClearAllMenuItems`方法可以呼叫以移除所有*以程式設計方式加入*功能表項目。

無法清除腳本中設定的功能表項目。



## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的功能表文件](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
