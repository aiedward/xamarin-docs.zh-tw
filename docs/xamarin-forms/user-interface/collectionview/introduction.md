---
title: Xamarin.FormsCollectionView 簡介
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d6a09ead9c3def2f58ad2755de4574f6d6e331e8
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136431"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin.FormsCollectionView 簡介

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)這是使用不同的版面配置規格來呈現資料清單的視圖。 它的目的是要提供更具彈性且更高效能的替代方案 [`ListView`](xref:Xamarin.Forms.ListView) 。 例如，下列螢幕擷取畫面顯示 `CollectionView` 使用兩個數據行垂直格線，並允許多重選取的：

[![CollectionView 垂直格線版面配置在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/verticalgrid-multipleselection.png "具有多個選取範圍的 CollectionView 垂直格線版面配置")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多個選取範圍的 CollectionView 垂直格線版面配置")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可從 Xamarin.Forms 4.3 取得。

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)適用于 iOS 和 Android，但只在通用 Windows 平臺[部分提供](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14)。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差異

雖然 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 和 [`ListView`](xref:Xamarin.Forms.ListView) api 很類似，但有一些顯著的差異：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有彈性的版面配置模型，可讓您以垂直或水準方式在清單或方格中呈現資料。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念。 相反地，資料範本是用來定義清單中每個資料項目的外觀。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動利用基礎原生控制項所提供的虛擬化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)減少的 API 介面 [`ListView`](xref:Xamarin.Forms.ListView) 。 中的許多屬性和事件 [`ListView`](xref:Xamarin.Forms.ListView) 都不存在於中 `CollectionView` 。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建分隔符號。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)如果 UI 執行緒已更新，將會擲回例外狀況 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 。

## <a name="move-from-listview-to-collectionview"></a>從 ListView 移至 CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)您可以使用下表的說明，將現有的執行中的實 Xamarin.Forms 作為遷移到 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 實現：

| 概念 | ListView API | CollectionView |
|---|---|---|
| 資料 | `ItemsSource` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會藉由設定其屬性來填入資料 `ItemsSource` 。 如需詳細資訊，請參閱[在 CollectionView 中填入資料](populate-data.md#populate-a-collectionview-with-data)。 |
| 專案外觀 | `ItemTemplate` | 將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 屬性設定為，即可定義中每個專案的外觀 `ItemTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需詳細資訊，請參閱[定義專案外觀](populate-data.md#define-item-appearance)。 |
| 資料格 | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念，因此沒有洩漏指標的概念。 相反地，資料範本是用來定義清單中每個資料項目的外觀。 |
| 資料列分隔符號 | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建分隔符號。 如有需要，可以在專案範本中提供。 |
| 選取項目 | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。 如需詳細資訊，請參閱[ Xamarin.Forms CollectionView Selection](selection.md)。 |
| 資料列高度 | `HasUnevenRows`, `RowHeight` | 在中 `CollectionView` ，每個專案的資料列高度取決於 `ItemSizingStrategy` 屬性。 如需詳細資訊，請參閱[專案大小](layout.md#item-sizing)。|
| Caching | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動使用基礎原生控制項所提供的虛擬化。 |
| 頁首和頁尾 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以透過 `Header` 、 `Footer` 、 `HeaderTemplate` 和屬性，呈現與清單中的專案一起滾動的頁首和頁尾 `FooterTemplate` 。 如需詳細資訊，請參閱頁首[和](layout.md#headers-and-footers)頁尾。 |
| 群組 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)將 `IsGrouped` 屬性設定為，以顯示正確群組的資料 `true` 。 您可以將 `GroupHeaderTemplate` 和屬性設定為物件，以自訂群組標頭和群組尾 `GroupFooterTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 如需詳細資訊，請參閱[ Xamarin.Forms CollectionView 群組](grouping.md)。 |
| 拖動以重新整理 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 將設定為的子系，即可支援提取至重新整理功能 [`CollectionView`](xref:Xamarin.Forms.CollectionView) `RefreshView` 。 如需詳細資訊，請參閱[Pull to refresh](populate-data.md#pull-to-refresh)。 |
| 操作功能表項目 | `ContextActions` | `SwipeView`在中，將設定為的根視圖 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，以定義中每個資料項目目的外觀，即可支援內容功能表項目 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 如需詳細資訊，請參閱[內容功能表](populate-data.md#context-menus)。 |
| 捲動 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)定義將 `ScrollTo` 專案滾動到視野的方法。 如需詳細資訊，請參閱[滾動](scrolling.md)。 |

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
