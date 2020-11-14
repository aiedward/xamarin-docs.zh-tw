---
title: Xamarin.Forms CollectionView 簡介
description: CollectionView 是對 ListView 有彈性且高效能的替代方案。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ad9ef36471b14ad44b108189b94bb22d1b3db41
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590242"
---
# <a name="no-locxamarinforms-collectionview-introduction"></a>Xamarin.Forms CollectionView 簡介

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同的版面配置規格來呈現資料清單的觀點。 它的目標是要提供更具彈性且更具效能的替代方案 [`ListView`](xref:Xamarin.Forms.ListView) 。 例如，下列螢幕擷取畫面顯示 `CollectionView` 使用兩個數據行垂直格線，並允許多重選取的：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 垂直格線版面配置](introduction-images/verticalgrid-multipleselection.png "CollectionView 具有多個選取範圍的垂直格線版面配置")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "CollectionView 具有多個選取範圍的垂直格線版面配置")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 應該用來呈現需要滾動或選取的資料清單。 當要顯示的資料不需要滾動或選取時，可使用可系結的配置。 如需詳細資訊，請參閱[中 Xamarin.Forms ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)的可系結版面配置。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可從 Xamarin.Forms 4.3 取得。

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 適用于 iOS 和 Android，但只在通用 Windows 平臺 [部分可用](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) 。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差異

[`CollectionView`](xref:Xamarin.Forms.CollectionView)和 [`ListView`](xref:Xamarin.Forms.ListView) api 很類似，但有一些顯著的差異：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有彈性的版面配置模型，可讓您在清單或方格中以垂直或水準方式呈現資料。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 支援單一和多重選取。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 沒有資料格的概念。 相反地，資料範本是用來定義清單中每個資料項目目的外觀。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會自動利用基礎原生控制項所提供的虛擬化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 減少的 API 介面 [`ListView`](xref:Xamarin.Forms.ListView) 。 中的許多屬性和事件 [`ListView`](xref:Xamarin.Forms.ListView) 都不存在於中 `CollectionView` 。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 不包含內建分隔符號。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 如果 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) UI 執行緒已更新，將會擲回例外狀況。

## <a name="move-from-listview-to-collectionview"></a>從 ListView 移至 CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) 您 Xamarin.Forms 可以使用下表的說明，將現有應用程式中的實作為遷移至 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 執行：

| 概念 | ListView API | CollectionView |
|---|---|---|
| 資料 | `ItemsSource` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會藉由設定其屬性來填入資料 `ItemsSource` 。 如需詳細資訊，請參閱 [在 CollectionView 中填入資料](populate-data.md#populate-a-collectionview-with-data)。 |
| 專案外觀 | `ItemTemplate` | 您 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以藉由將屬性設定為，來定義中每個專案的外觀 `ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需詳細資訊，請參閱 [定義專案外觀](populate-data.md#define-item-appearance)。 |
| 資料格 | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 沒有資料格的概念，因此沒有洩漏指標的概念。 相反地，資料範本是用來定義清單中每個資料項目目的外觀。 |
| 資料列分隔符號 | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 不包含內建分隔符號。 您可以視需要在專案範本中提供這些專案。 |
| 選取項目 | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 支援單一和多重選取。 如需詳細資訊，請參閱[ Xamarin.Forms CollectionView 選取專案](selection.md)。 |
| 資料列高度 | `HasUnevenRows`, `RowHeight` | 在中 `CollectionView` ，每個專案的資料列高度是由屬性所決定 `ItemSizingStrategy` 。 如需詳細資訊，請參閱專案重設 [大小](layout.md#item-sizing)。|
| 快取 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會自動使用基礎原生控制項所提供的虛擬化。 |
| 頁首和頁尾 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以透過 `Header` 、 `Footer` 、 `HeaderTemplate` 和屬性，呈現以清單中的專案來滾動的頁首和頁尾 `FooterTemplate` 。 如需詳細資訊，請參閱頁首 [和](layout.md#headers-and-footers)頁尾。 |
| 群組 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 將其屬性設定為，以顯示正確分組的資料 `IsGrouped` `true` 。 您可以將 `GroupHeaderTemplate` 和屬性設定為物件，以自訂群組標頭和群組尾 `GroupFooterTemplate`  [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需詳細資訊，請參閱[ Xamarin.Forms CollectionView 群組](grouping.md)。 |
| 拖動以重新整理 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 將設定為的子系可支援「提取至重新整理」功能 [`CollectionView`](xref:Xamarin.Forms.CollectionView) `RefreshView` 。 如需詳細資訊，請參閱 [提取以](populate-data.md#pull-to-refresh)重新整理。 |
| 操作功能表項目 | `ContextActions` | 藉由將設定 `SwipeView` 為中的根視圖 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，來定義中每個資料項目目的外觀，以支援內容功能表項目 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 如需詳細資訊，請參閱 [快顯功能表](populate-data.md#context-menus)。 |
| 捲動 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義將 `ScrollTo` 專案滾動至視圖的方法。 如需詳細資訊，請參閱 [滾動](scrolling.md)。 |

## <a name="related-links"></a>相關連結

- [CollectionView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [中的可系結版面配置 Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
