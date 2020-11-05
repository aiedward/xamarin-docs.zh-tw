---
title: 第17章的摘要。 掌控格線
description: 建立 Mobile Apps 與 Xamarin.Forms ：第17章的摘要。 掌控格線
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d2904270b601b6602457873e8b0180c13a0d6d95
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373454"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第17章的摘要。 掌控格線

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

[`Grid`](xref:Xamarin.Forms.Grid)是一種功能強大的版面配置機制，可將其子系排列成資料列和資料行的資料格。 與類似的 HTML `table` 元素不同的 `Grid` 是，是專供版面配置而非展示之用。

## <a name="the-basic-grid"></a>基本格線

`Grid` 衍生自 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ，定義繼承的 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 屬性 `Grid` 。 您可以在 XAML 或程式碼中填入此集合。

### <a name="the-grid-in-xaml"></a>XAML 中的方格

XAML 中的定義 `Grid` 通常是從填入的 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和集合和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) `Grid` [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 和 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 物件開始。 這是您建立的資料列和資料行數目 `Grid` ，以及其屬性的方式。

`RowDefinition` 有一個 [`Height`](xref:Xamarin.Forms.RowDefinition.Height) 屬性，而且 `ColumnDefinition` 有一個 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 屬性，也就 [`GridLength`](xref:Xamarin.Forms.GridLength) 是結構的型別。

在 XAML 中，會 [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) 將簡單的文字字串轉換成 `GridLength` 值。 在幕後，[函式 `GridLength` ] (x： Xamarin.Forms 。GridLength .% 23ctor (system.string， Xamarin.Forms 。Gridunittype.auto) # A3 會 `GridLength` 根據 number 和 type 值 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) （具有三個成員的列舉）來建立值：

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash;寬度或高度是在與裝置無關的單位中指定， (XAML 中的數位) 
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash;高度或寬度會根據資料格內容來 autosized， ( 在 XAML 中為 "Auto") 
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash;在 XAML 中，會依比例將剩餘的高度或寬度配置 (具有 "" 的數位 \* ，稱為 *星號* ) 

您 `Grid` 也必須明確或隱含地) ，將資料列和資料行 (指派給的每個子系。 資料列範圍和資料行範圍是選擇性的。 這些都是使用所定義的附加可系結屬性 &mdash; 屬性來指定， `Grid` 但是會在的子系上設定 `Grid` 。 `Grid` 定義四個靜態附加的可系結屬性：

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)以零為起始 &mdash; 的資料列; 預設值為0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)以 &mdash; 零為基底的資料行; 預設值為0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash;子系跨越的資料列數目; 預設值為1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash;子系所跨越的資料行數目; 預設值為1

在程式碼中，程式可以使用八個靜態方法來設定和取得這些值：

- [ `Grid.SetRow` ] (x： Xamarin.Forms 。SetRow (Xamarin.Forms 。BindableObject、System.object) # A3 和 [ `Grid.GetRow` ] (x： Xamarin.Forms 。GetRow (Xamarin.Forms 。BindableObject) # A7
- [ `Grid.SetColumn` ] (x： Xamarin.Forms 。SetColumn (Xamarin.Forms 。BindableObject、System.object) # A3 和 [ `Grid.GetColumn` ] (x： Xamarin.Forms 。GetColumn (Xamarin.Forms 。BindableObject) # A7
- [ `Grid.SetRowSpan` ] (x： Xamarin.Forms 。SetRowSpan (Xamarin.Forms 。BindableObject、System.object) # A3 和 [ `Grid.GetRowSpan` ] (x： Xamarin.Forms 。GetRowSpan (Xamarin.Forms 。BindableObject) # A7
- [ `Grid.SetColumnSpan` ] (x： Xamarin.Forms 。SetColumnSpan (Xamarin.Forms 。BindableObject、System.object) # A3 和 [ `Grid.GetColumnSpan` ] (x： Xamarin.Forms 。GetColumnSpan (Xamarin.Forms 。BindableObject) # A7

在 XAML 中，您可以使用下列屬性來設定這些值：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)範例會示範如何 `Grid` 在 XAML 中建立和初始化。

會 `Grid` 繼承的 [`Padding`](xref:Xamarin.Forms.Layout.Padding) 屬性 `Layout` ，並定義兩個額外的屬性，以提供資料列和資料行之間的間距：

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) 預設值為6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 預設值為6

`RowDefinitions`和 `ColumnDefinitions` 集合不是絕對必要的。 如果不存在，會建立子系的資料 `Grid` 列和資料行， `Grid` 並為它們提供預設值 `GridLength` " \* " (星星) 。

### <a name="the-grid-in-code"></a>程式碼中的方格

[**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)範例會示範如何在程式碼中建立及填入 `Grid` 。 您可以直接設定每個子系的附加屬性，或呼叫其他 `Add` 方法（例如 [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) [IGridList <T> ](xref:Xamarin.Forms.Grid.IGridList`1)介面所定義）來間接設定附加屬性。

### <a name="the-grid-bar-chart"></a>方格橫條圖

[**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)範例顯示如何 `BoxView` `Grid` 使用 bulk 方法將多個專案加入至 [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) 。 根據預設，這些 `BoxView` 元素的寬度相同。 然後，您可以控制每個的高度， `BoxView` 以類似橫條圖。

`Grid` **GridBarChart** 範例中的會 `AbsoluteLayout` 與一開始不可見的父系共用父代 `Frame` 。 此程式也會 `TapGestureRecognizer` 在每個上設定， `BoxView` 以使用 `Frame` 顯示點擊列的相關資訊。

### <a name="alignment-in-the-grid"></a>格線中的對齊

[**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)範例示範如何使用 `VerticalOptions` 和 `HorizontalOptions` 屬性來對齊儲存格中的子系 `Grid` 。

[**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)範例會 `Button` 在儲存格中央放置相同的空間元素 `Grid` 。

### <a name="cell-dividers-and-borders"></a>儲存格分隔和框線

不 `Grid` 包含可繪製資料格分隔或框線的功能。 不過，您可以自行建立。

[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)會示範如何明確地定義更多的資料列和資料行，以用於 `BoxView` 模仿分隔線條的精簡專案。

[**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程式不會建立任何其他資料格，而是會對齊 `BoxView` 每個資料格中的元素，以模擬資料格框線。

## <a name="almost-real-life-grid-examples"></a>幾乎真實的方格範例

[**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)範例會使用 `Grid` 來顯示鍵盤：

[![鍵盤格線的三重螢幕擷取畫面](images/ch17fg12-small.png "鍵盤格線")](images/ch17fg12-large.png#lightbox "鍵盤格線")

### <a name="responding-to-orientation-changes"></a>回應方向變更

`Grid`有助於結構設計程式以回應方向變更。 [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)範例示範一種技術，可在直向手機的第二個數據列和橫向導向電話的第二個數據行之間移動專案。

程式會將 `Slider` 元素初始化為0到255的範圍，並使用資料系結以十六進位顯示滑杆的值。 因為 `Slider` 值是浮點數，而且十六進位的 .net 格式化字串只適用于整數，所以 [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的類別會有説明。

## <a name="related-links"></a>相關連結

- [第17章 (PDF 的完整文字) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第17章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [方格](~/xamarin-forms/user-interface/layouts/grid.md)
