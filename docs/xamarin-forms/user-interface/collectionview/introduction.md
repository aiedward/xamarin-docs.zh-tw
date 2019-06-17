---
title: Xamarin.Forms CollectionView 簡介
description: CollectionView 是富彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 889c78ea6849cdd094d34ed0cf74ceebd33ce51d
ms.sourcegitcommit: 0596004d4a0e599c1da1ddd75a6ac928f21191c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66005146"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin.Forms CollectionView 簡介

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 呈現資料的清單檢視使用不同的版面配置規格。 它旨在提供更多的彈性，以及高效能替代方式，來[ `ListView` ](xref:Xamarin.Forms.ListView)。 例如，下列螢幕擷取畫面顯示`CollectionView`，使用兩個資料行的垂直格線，並可讓多個選取項目：

[![CollectionView 垂直格線版面配置，在 iOS 和 Android 上的螢幕擷取畫面](introduction-images/verticalgrid-multipleselection.png "CollectionView 垂直格線版面配置與多個選取項目")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "CollectionView 垂直格線版面配置與多個選取項目")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 使用 Xamarin.Forms 4.0。 不過，目前為實驗性，而且僅適用於藉由將下列這一行的程式碼，以您`AppDelegate`類別在 iOS 上，或您`MainActivity`類別在 Android 上，然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 只有在 iOS 和 Android 上使用。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 的差異

雖然[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)並[ `ListView` ](xref:Xamarin.Forms.ListView) Api 類似，有一些值得注意的差異：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 具有彈性的配置模型，可讓清單或方格中顯示的水平或垂直的資料。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 支援單一和多重選取。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 有沒有儲存格的概念。 相反地，資料範本用來在清單中定義的每個資料項目的外觀。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 自動會利用基礎原生控制項所提供的虛擬化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 減少的 API 介面[ `ListView` ](xref:Xamarin.Forms.ListView)。 許多屬性和事件[ `ListView` ](xref:Xamarin.Forms.ListView)不會出現在`CollectionView`。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) 不包含內建的分隔符號。

## <a name="move-from-listview-to-collectionview"></a>會將從 ListView CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) 在現有的 Xamarin.Forms 實作的實作可以移轉至[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)藉助於下表的實作：

| 概念 | ListView API | CollectionView |
|---|---|---|
| 資料 | `ItemsSource` | A [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)藉由設定資料填入其`ItemsSource`屬性。 如需詳細資訊，請參閱 <<c0> [ 填入資料 CollectionView](populate-data.md#populate-a-collectionview-with-data)。 |
| 項目外觀 | `ItemTemplate` | 在每個項目的外觀[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以藉由設定定義`ItemTemplate`屬性設[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 如需詳細資訊，請參閱 <<c0> [ 定義項目外觀](populate-data.md#define-item-appearance)。 |
| 資料格 | `TextCell`、`ImageCell`、`ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 有沒有儲存格的概念。 相反地，資料範本用來在清單中定義的每個資料項目的外觀。 |
| 資料列分隔符號 | `SeparatorColor`、 `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 不包含內建的分隔符號。 這些可以提供，如有需要，在項目範本。 |
| 選取 | `SelectionMode`、 `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 支援單一和多重選取。 如需詳細資訊，請參閱 < [Xamarin.Forms CollectionView 選取](selection.md)。 |
| 資料列高度 | `HasUnevenRows`、 `RowHeight` | 在  `CollectionView`，取決於每個項目的資料列高度`ItemSizingStrategy`屬性。 如需詳細資訊，請參閱 <<c0> [ 項目調整大小](layout.md#item-sizing)。|
| 快取 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會自動使用基礎原生控制項所提供的虛擬化。 |
| 頁首和頁尾 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | 頁首和頁尾目前不支援在`CollectionView`，但將在未來版本中加入。|
| 群組 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | 目前不支援群組`CollectionView`，但將在未來版本中加入。 |
| 拖動以重新整理 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | 目前不支援拖動以重新整理`CollectionView`，但將在未來版本中加入。 |
| 內容動作 | `ContextActions` | 內容動作目前不支援在`CollectionView`，但將在未來版本中加入。 |
| 捲動 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義`ScrollTo`方法，捲動至檢視的項目。 如需詳細資訊，請參閱 <<c0> [ 捲動](scrolling.md)。 |

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
