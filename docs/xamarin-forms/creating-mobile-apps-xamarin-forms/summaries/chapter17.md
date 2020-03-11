---
title: 第17章的摘要。 主控方格
description: 使用 Xamarin 建立 Mobile Apps：第17章的摘要。 主控方格
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760633"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第17章的摘要。 主控方格

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

[`Grid`](xref:Xamarin.Forms.Grid)是一種功能強大的版面配置機制，它會將其子系排列到儲存格的資料列和資料行。 不同于類似的 HTML `table` 元素，`Grid` 僅適用于版面配置，而不是簡報的用途。

## <a name="the-basic-grid"></a>基本方格

`Grid` 衍生自[`Layout<View>`](xref:Xamarin.Forms.Layout`1)，這會定義 `Grid` 繼承的[`Children`](xref:Xamarin.Forms.Layout`1.Children)屬性。 您可以在 XAML 或程式碼中填入此集合。

### <a name="the-grid-in-xaml"></a>XAML 中的方格

XAML 中的 `Grid` 定義通常會以[`RowDefinition`](xref:Xamarin.Forms.RowDefinition)和[`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition)物件填入 `Grid` 的[`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions)和[`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions)集合。 這是您建立 `Grid`的資料列和資料行數目及其屬性的方式。

`RowDefinition` 具有[`Height`](xref:Xamarin.Forms.RowDefinition.Height)屬性，而且 `ColumnDefinition` 具有[`Width`](xref:Xamarin.Forms.ColumnDefinition.Width)屬性，而這兩種類型都[`GridLength`](xref:Xamarin.Forms.GridLength)結構。

在 XAML 中， [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter)會將簡單的文字字串轉換成 `GridLength` 值。 在幕後， [`GridLength`](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType))的函式會根據數位和類型[`GridUnitType`](xref:Xamarin.Forms.GridUnitType)的值（具有三個成員的列舉）來建立 `GridLength` 值：

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; 寬度或高度是在與裝置無關的單位（XAML 中的數位）中指定
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; 高度或寬度是根據資料格內容（在 XAML 中為 "Auto"）大小
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; 剩餘的高度或寬度會按比例分配（在 XAML 中為具有 "\*" 的數位，稱為*星號*）

`Grid` 的每個子系也必須指派一個資料列和資料行（明確或隱含）。 資料列範圍和資料行範圍是選擇性的。 這些全都是使用附加的可系結屬性所指定，&mdash; 屬性是由 `Grid` 定義，但設定在 `Grid`的子系上。 `Grid` 定義四個靜態附加的可系結屬性：

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty) &mdash; 以零為基底的資料列;預設值為0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty) &mdash; 以零為基底的資料行;預設值為0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty) &mdash; 子系跨越的資料列數目;預設值為1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) &mdash; 子系跨越的資料行數目;預設值為1

在程式碼中，程式可以使用八個靜態方法來設定和取得這些值：

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32)) 和 [`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

在 XAML 中，您可以使用下列屬性來設定這些值：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)範例示範如何在 XAML 中建立和初始化 `Grid`。

`Grid` 會從 `Layout` 繼承[`Padding`](xref:Xamarin.Forms.Layout.Padding)屬性，並定義兩個額外的屬性，以提供資料列和資料行之間的間距：

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)的預設值為6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)的預設值為6

`RowDefinitions` 和 `ColumnDefinitions` 集合並不是絕對必要的。 如果不存在，`Grid` 會建立 `Grid` 子系的資料列和資料行，並提供所有預設 `GridLength` "\*" （星號）。

### <a name="the-grid-in-code"></a>程式碼中的方格

[**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)範例會示範如何在程式碼中建立和填入 `Grid`。 您可以直接設定每個子系的附加屬性，或藉由呼叫其他 `Add` 方法（例如[IGridList<T>](xref:Xamarin.Forms.Grid.IGridList`1)介面所定義的[`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) ）間接設定。

### <a name="the-grid-bar-chart"></a>方格橫條圖

[**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)範例會示範如何使用 bulk [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*)方法，將多個 `BoxView` 專案新增至 `Grid`。 根據預設，這些 `BoxView` 元素的寬度相同。 然後，可以控制每個 `BoxView` 的高度，以類似橫條圖。

**GridBarChart**範例中的 `Grid` 會與一開始不可見的 `Frame`共用 `AbsoluteLayout` 父系。 此程式也會在每個 `BoxView` 上設定 `TapGestureRecognizer`，以使用 `Frame` 來顯示該攻絲列的相關資訊。

### <a name="alignment-in-the-grid"></a>貼齊格線

[**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)範例會示範如何使用 `VerticalOptions` 和 `HorizontalOptions` 屬性來對齊 `Grid` 資料格中的子系。

[**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)範例會 `Button` 以 `Grid` 儲存格為中心的元素，以空格分隔。

### <a name="cell-dividers-and-borders"></a>資料格分隔和框線

`Grid` 不包含繪製資料格分隔線或框線的功能。 不過，您可以自行建立。

此[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)示範如何針對精簡的 `BoxView` 專案，明確定義額外的資料列和資料行，以模仿分行。

[**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程式不會建立任何其他資料格，而是會對齊每個資料格中的 `BoxView` 元素，以模擬資料格框線。

## <a name="almost-real-life-grid-examples"></a>幾乎真實生活的方格範例

[**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)範例會使用 `Grid` 來顯示鍵盤：

[![鍵盤格線的三向螢幕擷取畫面](images/ch17fg12-small.png "鍵盤方格")](images/ch17fg12-large.png#lightbox "鍵盤方格")

### <a name="responding-to-orientation-changes"></a>回應方向變更

`Grid` 可以協助結構程式來回應方向變更。 [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)範例示範一種技術，可在直向電話的第二列與橫向行動電話的第二個數據行之間移動元素。

程式會將 `Slider` 元素初始化為0到255的範圍，並使用資料系結以十六進位顯示滑杆的值。 因為 `Slider` 值是浮點數，而且十六進位的 .NET 格式化字串僅適用于整數，所以[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs)類別會有説明。

## <a name="related-links"></a>相關連結

- [第17章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第17章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [格線](~/xamarin-forms/user-interface/layouts/grid.md)
