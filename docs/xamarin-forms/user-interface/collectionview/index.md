---
title： " Xamarin.Forms CollectionView" 描述： "CollectionView 是彈性且高效能的觀點，可讓您使用不同的版面配置規格來呈現資料清單。"
assetid： 2BC9B223-2D5C-4B09-849C-B9D578954557 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：07/24/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-collectionview"></a>Xamarin.FormsCollectionView

## <a name="introduction"></a>[簡介](introduction.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)是彈性且高效能的觀點，可讓您使用不同的版面配置規格來呈現資料清單。

## <a name="data"></a>[資料](populate-data.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)會藉由將資料的屬性設定 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 為任何可執行檔集合，來填入資料 `IEnumerable` 。 您可以藉由將屬性設定為，來定義清單中每個專案的外觀 [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。

## <a name="layout"></a>[配置](layout.md)

根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會在垂直清單中顯示其專案。 不過，您可以指定垂直和水準清單和格線。

## <a name="selection"></a>[選項](selection.md)

預設 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會停用選取範圍。 不過，可以啟用單一和多重選取。

## <a name="empty-views"></a>[空白的視圖](emptyview.md)

在中 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，您可以指定空的視圖，以在沒有資料可供顯示時提供意見反應給使用者。 空白的視圖可以是字串、視圖或多個視圖。

## <a name="scrolling"></a>[捲動](scrolling.md)

當使用者撥動起始捲軸時，可以控制捲軸的結束位置，以便完全顯示專案。 此外，也 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會定義兩個 [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) 方法，以程式設計方式將專案滾動到 view。 其中一個多載會將指定索引處的專案滾動到 view，而另一個則會將指定的專案滾動到 view。

## <a name="grouping"></a>[群組](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將 `IsGrouped` 屬性設定為，以顯示正確群組的資料 `true` 。
