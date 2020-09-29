---
title: 'Xamarin 中的 [watchOS] 功能表控制項 (Force Touch) '
description: 本檔說明如何在 Xamarin 中使用 watchOS 強制觸控手勢。 它會討論如何回應強制觸控、如何新增功能表，以及變更功能表項目。
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 5ad45e1cc17a58875037f20996463f55f2b68685
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431554"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Xamarin 中的 [watchOS] 功能表控制項 (Force Touch) 

Watch 套件提供在監看式應用程式畫面上執行時，會觸發功能表的 Force Touch 手勢。

![顯示功能表的 Apple Watch](menu-images/menu.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>回應 Force Touch

如果已 `Menu` 針對介面控制器執行，則當使用者執行 Force Touch 將會顯示功能表。 如果未執行任何功能表，畫面會短暫地以動畫顯示，而不會執行其他動作。

強制觸控與螢幕上的任何特定元素沒有關聯;只有一個功能表可以附加至介面控制器，而且不論畫面上 Force Touch 按下的位置為何，它都會出現。

可以在其中顯示一到四個功能表選項。

## <a name="adding-a-menu"></a>新增功能表

在 `Menu` 設計階段，必須加入至腳本 `InterfaceController` 上的。 將功能表控制項拖曳至介面控制器上時，不會在分鏡腳本預覽上顯示視覺指示，但 **功能表** 會出現在 [ **檔大綱** ] 面板中：

![在設計階段編輯功能表](menu-images/menu-action.png)

最多可以將四個功能表項目加入至功能表控制項。 您可以在 Properties pad 中設定這些 **屬性** 。 您可以設定下列屬性：

- 標題和
- 自訂映射，或
- 系統映射：接受、新增、封鎖、拒絕、資訊、可能、更多、靜音、暫停、播放、重複、繼續、共用、隨機播放、喇叭、垃圾桶。

選取 [ `Action` **屬性**] 面板的 [**事件**] 區段，然後輸入動作方法的名稱，以建立。 部分方法會在程式碼中建立，可在介面控制器類別中執行，如下所示：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自訂映像

類似于 iOS 中的索引標籤影像，功能表項目影像需要具有 Alpha 色板的不透明模式，讓背景能夠顯示。

您應該將用於功能表的影像新增至 watch 應用程式專案， (不是 watch 應用程式擴充功能專案) ，以獲得最佳效能。

## <a name="changing-the-menu-items"></a>變更功能表項目

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在執行時間加入

`Menu`雖然可以用程式設計方式改變的集合，但您無法在執行時間將加入至介面控制器 `MenuItem` 。 *can*
使用如下 `AddMenuItem` 所示的方法：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

Xamarin Watch 套件 API 目前需要 `selector` `AdMenuItem` 方法的，其宣告方式應如下所示：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>在執行時間移除

您 `ClearAllMenuItems` 可以呼叫方法來移除所有以程式設計 *方式加入* 的功能表項目。

無法清除在腳本中設定的功能表項目。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的功能表檔](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)