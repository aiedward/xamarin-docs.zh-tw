---
title: 第17章摘要。 掌控格線
description: 使用 Xamarin.表單創建行動應用程式:第 17 章摘要。 掌控格線
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 37b5e2bbafa816de27390771ae6daa33c74f7651
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760633"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第17章摘要。 掌控格線

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

[`Grid`](xref:Xamarin.Forms.Grid)是一種強大的布局機制,可將其子級排列到儲存格的行和列中。 與類似的`table`HTML`Grid`元素不同 ,僅用於佈局目的,而不是表示目的。

## <a name="the-basic-grid"></a>基本格線

`Grid`派生自[`Layout<View>`](xref:Xamarin.Forms.Layout`1),它[`Children`](xref:Xamarin.Forms.Layout`1.Children)`Grid`定義 繼承的屬性。 您可以在 XAML 或代碼中填充此集合。

### <a name="the-grid-in-xaml"></a>XAML 中的網格

XAML`Grid`[`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions)中 的定義通常從[`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions)`Grid`[`RowDefinition`](xref:Xamarin.Forms.RowDefinition)填[`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition)充 和和 物件的 和集合開始。 這是如何建立`Grid`的行數和列及其屬性。

`RowDefinition`具有屬性[`Height`](xref:Xamarin.Forms.RowDefinition.Height),`ColumnDefinition`並且具有[`Width`](xref:Xamarin.Forms.ColumnDefinition.Width)一 個屬性,[`GridLength`](xref:Xamarin.Forms.GridLength)兩者 都是結構。

在 XAML[`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter)中,將簡單的文字字`GridLength`串 轉換為值。 在背景,`GridLength`[`GridUnitType`](xref:Xamarin.Forms.GridUnitType)[`GridLength`建構函數](xref:Xamarin.Forms.GridLength.%23ctor(System.Double,Xamarin.Forms.GridUnitType))基於數字和類型 值建立值,即包含三個成員的枚舉:

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash;寬度或高度以與裝置無關的單位指定(XAML 中的數位)
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash;高度或寬度根據儲存格內容自動調整大小(XAML 中的"自動')
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash;剩餘高度或寬度按比例分配(在 XAML\*中 ,帶有""稱為*星形*的數位)

還必須為 中的`Grid`每個子級分配一行和列(顯式或隱式)。 行範圍和列範圍是可選的。 所有這些都使用附加的可綁定屬性指定,&mdash;這些屬性由`Grid`定義的 屬性,`Grid`但設置在的子級。 `Grid`定義四個靜態附加綁定屬性:

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)&mdash;零行;預設值為 0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)&mdash;零基列;預設值為 0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash;子範圍的行數;預設值為 1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash;子範圍的列數;預設值為 1

在代碼中,程式可以使用八種靜態方法來設置和獲取這些值:

- [`Grid.SetRow`](xref:Xamarin.Forms.Grid.SetRow(Xamarin.Forms.BindableObject,System.Int32))和[`Grid.GetRow`](xref:Xamarin.Forms.Grid.GetRow(Xamarin.Forms.BindableObject))
- [`Grid.SetColumn`](xref:Xamarin.Forms.Grid.SetColumn(Xamarin.Forms.BindableObject,System.Int32))和[`Grid.GetColumn`](xref:Xamarin.Forms.Grid.GetColumn(Xamarin.Forms.BindableObject))
- [`Grid.SetRowSpan`](xref:Xamarin.Forms.Grid.SetRowSpan(Xamarin.Forms.BindableObject,System.Int32))和[`Grid.GetRowSpan`](xref:Xamarin.Forms.Grid.GetRowSpan(Xamarin.Forms.BindableObject))
- [`Grid.SetColumnSpan`](xref:Xamarin.Forms.Grid.SetColumnSpan(Xamarin.Forms.BindableObject,System.Int32))和[`Grid.GetColumnSpan`](xref:Xamarin.Forms.Grid.GetColumnSpan(Xamarin.Forms.BindableObject))

在 XAML 中,您可以使用以下屬性來設定這些值:

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)範例展示在 XAML 建立與初始化`Grid`。

[`Padding`](xref:Xamarin.Forms.Layout.Padding)繼承`Grid`屬性 並定義兩個在行與欄之間提供間距`Layout`的其他屬性:

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)預設值為 6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)預設值為 6

和`RowDefinitions``ColumnDefinitions`集合不是嚴格要求的。 如果不存在,則`Grid`為`Grid`子級創建行和列,併為他們提供預設值`GridLength`"(\*星形)。

### <a name="the-grid-in-code"></a>代碼中的格線

[**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)範例展示如何在代碼建立與填充`Grid`。 您可以直接或通過調用`Add`[Grid.IGridList<T>](xref:Xamarin.Forms.Grid.IGridList`1)介面[`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*)定義的其他方法來直接或間接地設置每個子級的附加屬性。

### <a name="the-grid-bar-chart"></a>格線條圖

[**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)範例展示如何使用大量`BoxView``Grid`[`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*)方法向新增多個元素。 默認情況下,這些`BoxView`元素具有相等的寬度。 然後,可以控制`BoxView`每個高度,以類似於條形圖。

**GridBarChart**範例中的`AbsoluteLayout``Frame``Grid`共用父項,該父項最初不可見。 程式還在每個`TapGestureRecognizer``BoxView`上設置一個,以`Frame`使用 來顯示有關已點擊柱的資訊。

### <a name="alignment-in-the-grid"></a>格線中對齊

[**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)範例展示如何`VerticalOptions`使用`HorizontalOptions`和`Grid`屬性對齊 單元格中的子級。

[**間距按鈕**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)對以單元格為中心`Button``Grid`的 等間距元素進行採樣。

### <a name="cell-dividers-and-borders"></a>儲存格分隔符與邊框

`Grid`不包括繪製單元格分隔符或邊框的功能。 但是,您可以自己製作。

[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)演示如何定義其他行和列,專門為精`BoxView`簡 元素類比分隔線。

[**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程式不會創建任何其他單元格,而是對齊每個單元格`BoxView`中的元素以類比單元格邊框。

## <a name="almost-real-life-grid-examples"></a>幾乎真實網格範例

[**鍵盤格線**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)範例`Grid`使用 顯示鍵盤:

[![鍵盤格線的三重螢幕截圖](images/ch17fg12-small.png "鍵盤格線")](images/ch17fg12-large.png#lightbox "鍵盤格線")

### <a name="responding-to-orientation-changes"></a>回應方向變化

可幫助`Grid`構建程式以回應方向更改。 [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)示例演示了一種在面向縱向的手機的第二行和面向景觀的手機的第二列之間移動元素的技術。

程式將`Slider`元素初始化到 0 到 255 的範圍,並使用數據綁定在十六進位中顯示滑塊的值。 由於`Slider`值是浮點,而十六進位的 .NET 格式字串僅適用於整數,[**因此 Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs)類有助於。

## <a name="related-links"></a>相關連結

- [第17章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第17章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [格線](~/xamarin-forms/user-interface/layouts/grid.md)
