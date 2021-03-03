---
title: Xamarin.Forms 即時視覺化樹狀結構
description: 在進行偵錯工具時，請參閱應用程式的執行時間 UI 階層。
ms.prod: xamarin
ms.assetid: 29c45b85-21b1-40ab-941f-4d9e57770c46
ms.technology: xamarin-forms
author: BretJohnson
ms.author: bretjohn
ms.date: 03/25/2021
no-loc:
- Xamarin.Forms
ms.openlocfilehash: 66ee5c28d3c6f35bec8e30e409a38430d41d8c68
ms.sourcegitcommit: 3aa9bdcaaedca74ab5175cb2338a1df122300243
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751610"
---
# <a name="xamarinforms-live-visual-tree"></a>Xamarin.Forms 即時視覺化樹狀結構

您可以使用 **即時視覺化樹狀結構** 來即時查看執行中的 XAML 程式碼。 它會顯示執行中應用程式 UI 元素的樹狀檢視 Xamarin.Forms 。

## <a name="requirements"></a>規格需求

* 使用 Xamarin.Forms 5.0 或更新版本。
* 只有已啟用熱重載的 *變更* (預設會啟用) 。

## <a name="usage"></a>使用方式

當符合需求時，[偵錯工具] 和 [即時視覺化樹狀結構] 視窗將會顯示應用程式的執行時間 UI 階層。

  * **Windows**：根據預設，它會顯示在 IDE 的左邊。 如果您沒有看到它，請使用 **Debug > Windows > 即時視覺化樹狀結構** 來顯示它。
  * **Mac**：根據預設，它會顯示在 IDE 的右側。 如果您沒有看到它，請使用 **View > Debug Windows > 即時視覺化樹狀結構** 來顯示它。

使用樹狀檢視來檢查應用程式的執行時間 UI 階層，並展開/折迭節點以專注于 UI 的特定部分。

## <a name="live-visual-tree-toolbar"></a>即時視覺化樹狀結構工具列 

XAML 專案的觀看預設會使用 **MY XAML** 功能來簡化。 切換至 [即時視覺化樹狀] 工具列上的 [ **僅顯示我的 XAML** ] 按鈕，以顯示所有 UI 元素。 如果您想要，您可以在 [選項] 中 [停用這項設定](/visualstudio/debugger/general-debugging-options-dialog-box.md) ，一律會顯示所有的 XAML 元素。

> [!NOTE]
> Visual Studio for Mac 目前不支援 **MY XAML** 功能。

XAML 的結構有許多您可能不會直接感興趣的專案，如果您不知道程式碼，則可能會有很長的時間流覽樹狀結構，以找出您要尋找的專案。 因此， **即時視覺化樹狀結構** 有多種方法可讓您使用應用程式的 UI，以協助您尋找想要檢查的元素。

目前只有 UWP 應用程式) 支援執行 **中應用程式的 Select 元素** (。 只要選取 [即時視覺化樹狀結構] 工具列最左邊的按鈕，即可啟用此模式。 在此模式中，您可以在應用程式中選取 UI 專案，而 [ **即時視覺化樹狀結構** ] 會自動更新，以顯示樹狀目錄中對應至該專案的節點和其屬性。 

在執行 **中的應用程式中顯示版面** 配置提示 (目前僅支援 UWP 應用程式) 。 只要選取緊鄰 [啟用選取範圍] 按鈕右邊的按鈕時，即可啟用此模式。 [顯示版面配置提示] 開啟時，會使此應用程式視窗沿著所選取物件的界限顯示水平及垂直線條，讓您能夠查看其向何處對齊，以及查看顯示此邊界的矩形。

**預覽選取範圍**。 只要選取 [即時視覺化樹狀] 工具列上從左邊數來的第三個按鈕，即可啟用這個模式。 如果您可存取該應用程式的原始程式碼，則此模式會顯示宣告此項目的 XAML。

## <a name="related-links"></a>相關連結

- [使用 XAML 熱重載撰寫和偵測執行中的 XAML 程式碼](hot-reload.md)
