---
title: Xamarin 中的 watchOS 功能表控制項（Force Touch）
description: 本檔說明如何在 Xamarin 中使用 watchOS 強制觸控手勢。 它討論如何回應強制觸控、如何新增功能表，以及變更功能表項目。
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1a9013c8f3adad0f52c264ea1216cab784d6b58f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939655"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Xamarin 中的 watchOS 功能表控制項（Force Touch）

Watch 套件提供在監看式應用程式畫面上執行時，觸發功能表的 Force Touch 手勢。

![顯示功能表的 Apple Watch](menu-images/menu.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>回應 Force Touch

如果已 `Menu` 針對介面控制器執行，則當使用者執行時，將會顯示功能表 Force Touch。 如果未執行任何功能表，畫面會短暫地產生動畫，而不會發生其他動作。

強制觸控不會與螢幕上的任何特定元素相關聯;只有一個功能表可以連接到介面控制器，而不論畫面上 Force Touch 按的位置為何，都會出現。

可以在其中顯示一個和四個功能表選項。

## <a name="adding-a-menu"></a>新增功能表

在 `Menu` 設計階段，必須將加入至分鏡腳本 `InterfaceController` 。 當 menu 控制項拖曳至介面控制器上時，在分鏡腳本預覽中並沒有視覺指示，但**功能表**會出現在 [**檔大綱**] 面板中：

![在設計階段編輯功能表](menu-images/menu-action.png)

最多可以將四個功能表項目加入至功能表控制項。 可以在**Properties** pad 中設定。 可以設定下列屬性：

- 標題，以及
- 自訂映射，或
- 系統映射： [接受]、[新增]、[封鎖]、[資訊]、[可能]、[更多]、[暫停]、[播放]、[重複]、[繼續]、[共用]、[

`Action`選取**Properties** Pad 的 [**事件**] 區段，然後輸入動作方法的名稱，以建立。 系統會在程式碼中建立部分方法，這可以在介面控制器類別中執行，如下所示：

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>自訂映像

類似于 iOS 中的索引標籤影像，功能表項目影像需要具有 Alpha 色板的不透明模式，以允許背景顯示。

您應該將用於功能表的影像新增至 watch 應用程式專案（而非 watch 應用程式擴充功能專案），以獲得最佳效能。

## <a name="changing-the-menu-items"></a>變更功能表項目

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>在執行時間加入

您無法 `Menu` 在執行時間將加入至介面控制器，但可透過程式設計 `MenuItem` 方式變更的*can*集合。
使用 `AddMenuItem` 方法，如下所示：

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

在方法中，目前的 Xamarin Watch 套件 API 需要 `selector` `AdMenuItem` ，其應宣告如下：

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>在執行時間移除

您 `ClearAllMenuItems` 可以呼叫方法，以移除所有*以程式設計方式加入*的功能表項目。

無法清除在分鏡腳本中設定的功能表項目。

## <a name="related-links"></a>相關連結

- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的功能表檔](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
