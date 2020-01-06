---
title: Xamarin. Forms CollectionView 簡介
description: CollectionView 是彈性且高效能的觀點，可讓您使用不同的版面配置規格來呈現資料清單。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: c04b5250bcdc575adc5aaff73901347e1e476b07
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489332"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin. Forms CollectionView 簡介

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同版面配置規格呈現資料清單的檢視。 它的目的是要提供更具彈性且高效能的[`ListView`](xref:Xamarin.Forms.ListView)替代方案。 例如，下列螢幕擷取畫面顯示使用兩個數據行垂直格線，並允許多重選取的 `CollectionView`：

[![CollectionView 垂直格線版面配置在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/verticalgrid-multipleselection.png "具有多個選取範圍的 CollectionView 垂直格線版面配置")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多個選取範圍的 CollectionView 垂直格線版面配置")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可從 Xamarin. 表單4.3 中取得。

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)適用于 IOS 和 Android，但只能在通用 Windows 平臺[部分使用](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14)。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差異

雖然[`CollectionView`](xref:Xamarin.Forms.CollectionView)和[`ListView`](xref:Xamarin.Forms.ListView) api 很類似，但有一些顯著的差異：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有彈性的版面配置模型，可讓您以垂直或水準方式在清單或方格中呈現資料。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念。 相反地，資料範本是用來定義清單中每個資料項目的外觀。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動利用基礎原生控制項所提供的虛擬化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)可減少[`ListView`](xref:Xamarin.Forms.ListView)的 API 介面。 `CollectionView`中不存在來自[`ListView`](xref:Xamarin.Forms.ListView)的許多屬性和事件。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建的分隔符號。
- 如果從 UI 執行緒更新其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) ， [`CollectionView`](xref:Xamarin.Forms.CollectionView)將會擲回例外狀況。

## <a name="move-from-listview-to-collectionview"></a>從 ListView 移至 CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)在現有 Xamarin 中的執行方式。您可以透過下表的說明，將表單部署遷移至[`CollectionView`](xref:Xamarin.Forms.CollectionView)的實現：

| 概念 | ListView API | CollectionView |
|---|---|---|
| Data | `ItemsSource` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會藉由設定其 `ItemsSource` 屬性來填入資料。 如需詳細資訊，請參閱[在 CollectionView 中填入資料](populate-data.md#populate-a-collectionview-with-data)。 |
| 專案外觀 | `ItemTemplate` | 藉由將 `ItemTemplate` 屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，即可定義[`CollectionView`](xref:Xamarin.Forms.CollectionView)中每個專案的外觀。 如需詳細資訊，請參閱[定義專案外觀](populate-data.md#define-item-appearance)。 |
| 儲存格 | `TextCell`中， `ImageCell`中， `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有儲存格的概念，因此沒有洩漏指標的概念。 相反地，資料範本是用來定義清單中每個資料項目的外觀。 |
| 資料列分隔符號 | `SeparatorColor`、 `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建的分隔符號。 如有需要，可以在專案範本中提供。 |
| 選取 | `SelectionMode`、 `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。 如需詳細資訊，請參閱[CollectionView 選取專案](selection.md)。 |
| 資料列高度 | `HasUnevenRows`、 `RowHeight` | 在 `CollectionView`中，每個專案的資料列高度取決於 `ItemSizingStrategy` 屬性。 如需詳細資訊，請參閱[專案大小](layout.md#item-sizing)。|
| 快取 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動使用基礎原生控制項所提供的虛擬化。 |
| 頁首和頁尾 | `Header`、 `HeaderElement`、 `HeaderTemplate`、 `Footer`、 `FooterElement`、 `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以透過 [`Header`]、[`Footer`]、[`HeaderTemplate`] 和 [`FooterTemplate`] 屬性，呈現與清單中的專案一起滾動的頁首和頁尾。 如需詳細資訊，請參閱頁首[和](layout.md#headers-and-footers)頁尾。 |
| 群組 | `GroupDisplayBinding`、 `GroupHeaderTemplate`、 `GroupShortNameBinding`、 `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會將其 `IsGrouped` 屬性設定為 `true`，以顯示正確群組的資料。 您可以將 `GroupHeaderTemplate` 和 `GroupFooterTemplate` 屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件，以自訂群組標頭和群組尾。 如需詳細資訊，請參閱 [Xamarin CollectionView 群組](grouping.md)。 |
| 拖動以重新整理 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 將[`CollectionView`](xref:Xamarin.Forms.CollectionView)設定為 `RefreshView`的子系，即可支援提取至重新整理功能。 如需詳細資訊，請參閱[Pull to refresh](populate-data.md#pull-to-refresh)。 |
| 操作功能表項目 | `ContextActions` | 藉由將 `SwipeView` 設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的根視圖，以定義[`CollectionView`](xref:Xamarin.Forms.CollectionView)中每個資料項目的外觀，即可支援內容功能表項目。 如需詳細資訊，請參閱[內容功能表](populate-data.md#context-menus)。 |
| 捲動 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)定義 `ScrollTo` 方法，將專案滾動到視野中。 如需詳細資訊，請參閱[滾動](scrolling.md)。 |

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
