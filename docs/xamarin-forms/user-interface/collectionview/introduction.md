---
title: Xamarin. Forms CollectionView 簡介
description: CollectionView 是彈性且高效能的觀點，可讓您使用不同的版面配置規格來呈現資料清單。
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 89afb0f2bfe93a5f78b0cd162f2a65e585b54b4b
ms.sourcegitcommit: 43423d4018cc0d4b0b8c98a4b3da0704495eb0cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303230"
---
# <a name="xamarinforms-collectionview-introduction"></a>Xamarin. Forms CollectionView 簡介

![此 API 目前是發行前版本](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 是使用不同版面配置規格呈現資料清單的檢視。 它的目的是要提供更具彈性且高效能的替代方案，以[`ListView`](xref:Xamarin.Forms.ListView)。 例如，下列螢幕擷取畫面顯示使用兩個數據行垂直格線的 `CollectionView`，並允許多重選取：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 垂直格線版面配置，](introduction-images/verticalgrid-multipleselection.png "具有多重選取專案的 CollectionView 垂直格線版面")配置](introduction-images/verticalgrid-multipleselection-large.png#lightbox "具有多個選取範圍的 CollectionView 垂直格線版面配置")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)適用于 Xamarin. 表單4.0。 不過，它目前是實驗性，而且只能藉由將下列程式程式碼新增至 iOS 上的 @no__t 0 類別或 Android 上的 @no__t 1 類別，或呼叫 `Forms.Init` 之前，在 UWP 上使用您的 `App.xaml.cs`：

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)適用于 IOS 和 Android，但只在通用 Windows 平臺[部分提供](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14)。

## <a name="collectionview-and-listview-differences"></a>CollectionView 和 ListView 差異

雖然[@no__t 1](xref:Xamarin.Forms.CollectionView)和[@no__t 3](xref:Xamarin.Forms.ListView) api 很類似，但有一些顯著的差異：

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)具有彈性的版面配置模型，可讓資料以垂直或水準方式呈現在清單或方格中。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念。 相反地，資料範本是用來定義清單中每個資料項目的外觀。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動利用基礎原生控制項所提供的虛擬化。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)會減少[`ListView`](xref:Xamarin.Forms.ListView)的 API 介面。 @No__t-2 中不會有許多來自[`ListView`](xref:Xamarin.Forms.ListView)的屬性和事件。
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建分隔符號。

## <a name="move-from-listview-to-collectionview"></a>從 ListView 移至 CollectionView

現有 Xamarin 中的[@no__t 1](xref:Xamarin.Forms.ListView) 。表單執行可透過下表的協助，遷移至[@no__t 3 的](xref:Xamarin.Forms.CollectionView)實現：

| 概念 | ListView API | CollectionView |
|---|---|---|
| Data | `ItemsSource` | [@No__t-1](xref:Xamarin.Forms.CollectionView)會藉由設定其 `ItemsSource` 屬性來填入資料。 如需詳細資訊，請參閱[在 CollectionView 中填入資料](populate-data.md#populate-a-collectionview-with-data)。 |
| 專案外觀 | `ItemTemplate` | 將 `ItemTemplate` 屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，即可定義[`CollectionView`](xref:Xamarin.Forms.CollectionView)中每個專案的外觀。 如需詳細資訊，請參閱[定義專案外觀](populate-data.md#define-item-appearance)。 |
| 資料格 | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)沒有資料格的概念。 相反地，資料範本是用來定義清單中每個資料項目的外觀。 |
| 資料列分隔符號 | `SeparatorColor`、 `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)不包含內建分隔符號。 如有需要，可以在專案範本中提供。 |
| 選取 | `SelectionMode`、 `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)支援單一和多重選取。 如需詳細資訊，請參閱[CollectionView 選取專案](selection.md)。 |
| 資料列高度 | `HasUnevenRows`、 `RowHeight` | 在 `CollectionView` 中，每個專案的資料列高度是由 `ItemSizingStrategy` 屬性所決定。 如需詳細資訊，請參閱[專案大小](layout.md#item-sizing)。|
| 快取 | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會自動使用基礎原生控制項所提供的虛擬化。 |
| 頁首和頁尾 | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以呈現使用清單中的專案（透過 `Header`、`Footer`、`HeaderTemplate` 和 `FooterTemplate` 屬性）進行滾動的頁首和頁尾。 如需詳細資訊，請參閱頁首[和](layout.md#headers-and-footers)頁尾。 |
| 群組 | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會將其 `IsGrouped` 屬性設定為 `true`，以顯示正確群組的資料。 藉由將 `GroupHeaderTemplate` 和 @no__t 1 屬性設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件，即可自訂群組標頭和群組尾。 如需詳細資訊，請參閱 < [Xamarin CollectionView 群組](grouping.md)。 |
| 提取至重新整理 | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | @No__t-0 中目前不支援提取至重新整理，但會在未來的版本中加入。 |
| 內容動作 | `ContextActions` | @No__t-0 中目前不支援內容動作，但會在未來的版本中加入。 |
| 捲動 | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)會定義 `ScrollTo` 方法，將專案滾動到 view。 如需詳細資訊，請參閱[滾動](scrolling.md)。 |

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
