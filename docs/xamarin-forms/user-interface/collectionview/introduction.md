---
title: Xamarin. Forms CollectionView 簡介
description: CollectionView 是彈性且高效能的觀點, 可讓您使用不同的版面配置規格來呈現資料清單。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 5832cb28162602a41d31026bc4a0ed54ac6bfb34
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888603"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin. Forms CollectionView 簡介

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同版面配置規格呈現資料清單的檢視。 它的目的是要提供更具彈性且更高效[`ListView`](xref:Xamarin.Forms.ListView)能的替代方案。 例如, 下列螢幕擷取畫面顯示`CollectionView`使用兩個數據行垂直格線, 並允許多重選取的:

[![螢幕擷取畫面: IOS 和 Android 上的 CollectionView 垂直格線版面配置,](introduction-images/verticalgrid-multipleselection.png "具有多重選取專案的 CollectionView 垂直格線版面")]配置(introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多個選取範圍的 CollectionView 垂直格線版面")配置

[`CollectionView`](xref:Xamarin.Forms.CollectionView)適用于 Xamarin. 表單4.0。 不過, 它目前是實驗性的, 而且只能透過將下列程式程式碼新增至您`AppDelegate`的 iOS 或`MainActivity` Android 上的類別, 然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)適用于 iOS 和 Android, 但只在通用 Windows 平臺部分提供。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差異

[`CollectionView`](xref:Xamarin.Forms.CollectionView)雖然和[`ListView`](xref:Xamarin.Forms.ListView) api 很類似, 但有一些顯著的差異:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有彈性的版面配置模型, 可讓您以垂直或水準方式在清單或方格中呈現資料。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念。 相反地, 資料範本是用來定義清單中每個資料項目的外觀。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動利用基礎原生控制項所提供的虛擬化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)減少的 API 介面[`ListView`](xref:Xamarin.Forms.ListView)。 中的許多屬性和[`ListView`](xref:Xamarin.Forms.ListView)事件都不存在`CollectionView`于中。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建分隔符號。

## <a name="move-from-listview-to-collectionview"></a>從 ListView 移至 CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)現有 Xamarin 中的實作為。您可以透過下[`CollectionView`](xref:Xamarin.Forms.CollectionView)表的說明, 將表單實作為遷移到實現:

| 概念 | ListView API | CollectionView |
|---|---|---|
| 資料 | `ItemsSource` | 會藉由設定其`ItemsSource`屬性來填入資料。 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 如需詳細資訊, 請參閱[在 CollectionView 中填入資料](populate-data.md#populate-a-collectionview-with-data)。 |
| 專案外觀 | `ItemTemplate` | 將[`CollectionView`](xref:Xamarin.Forms.CollectionView) 屬性`ItemTemplate`設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), 即可定義中每個專案的外觀。 如需詳細資訊, 請參閱[定義專案外觀](populate-data.md#define-item-appearance)。 |
| 資料格 | `TextCell`、`ImageCell`、`ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念。 相反地, 資料範本是用來定義清單中每個資料項目的外觀。 |
| 資料列分隔符號 | `SeparatorColor`、 `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建分隔符號。 如有需要, 可以在專案範本中提供。 |
| 選取 | `SelectionMode`、 `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。 如需詳細資訊, 請參閱[CollectionView 選取專案](selection.md)。 |
| 資料列高度 | `HasUnevenRows`、 `RowHeight` | 在中`ItemSizingStrategy` , 每個專案的資料列高度取決於屬性。 `CollectionView` 如需詳細資訊, 請參閱[專案大小](layout.md#item-sizing)。|
| 快取 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動使用基礎原生控制項所提供的虛擬化。 |
| 頁首和頁尾 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以透過`Header`、 `Footer`、 `HeaderTemplate`和屬性,呈現與清單中的專案一起滾動的頁首和頁尾。`FooterTemplate` 如需詳細資訊, 請參閱頁首[和](layout.md#headers-and-footers)頁尾。 |
| 群組 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)將`IsGrouped`屬性設定為, 以`true`顯示正確群組的資料。 您可以將`GroupHeaderTemplate`和`GroupFooterTemplate`屬性設定為物件, 以[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)自訂群組標頭和群組尾。 如需詳細資訊, 請參閱 < [Xamarin CollectionView 群組](grouping.md)。 |
| 提取至重新整理 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 目前不支援`CollectionView`提取至重新整理, 但會在未來的版本中加入。 |
| 內容動作 | `ContextActions` | 目前不支援`CollectionView`內容動作, 但會在未來的版本中加入。 |
| 捲動 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)定義`ScrollTo`將專案滾動到視野的方法。 如需詳細資訊, 請參閱[滾動](scrolling.md)。 |

## <a name="related-links"></a>相關連結

- [CollectionView (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
