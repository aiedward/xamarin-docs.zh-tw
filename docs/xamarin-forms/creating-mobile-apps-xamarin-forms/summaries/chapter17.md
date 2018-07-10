---
title: 第 17 章的摘要。 掌控格線
description: 使用 Xamarin.Forms 建立行動應用程式： 第 17 章摘要。 掌控格線
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: b71859d0848d7bf790b3cc4beddc67a5ea86d340
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935474"
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第 17 章的摘要。 掌控格線

[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)會排列成資料列和資料行的資料格的子系的功能強大的版面配置機制。 不同於類似的 HTML`table`項目，`Grid`僅適用於版面配置，而不是簡報的目的。

## <a name="the-basic-grid"></a>基本的方格

`Grid` 衍生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)，其定義[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)屬性，`Grid`繼承。 您可以填寫此 XAML 或程式碼中的集合。

### <a name="the-grid-in-xaml"></a>XAML 中的方格

定義`Grid`XAML 中通常開始填滿[ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/)並[ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/)集合`Grid`使用[ `RowDefinition`](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/)並[ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/)物件。 這是您建立的資料列數目和資料行`Grid`，及其屬性。

`RowDefinition` 已[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/)屬性並`ColumnDefinition`具有[ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/)屬性，這兩個類型[ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/)，結構。

在 XAML 中， [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/)轉換成的簡單的文字字串`GridLength`值。 在幕後[`GridLength`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/)建立`GridLength`值會根據數字和類型的值[ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/)，含有三個成員的列舉類型：

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute) &mdash; 裝置獨立單位 （必須在 XAML 中的數字） 中指定的寬度或高度
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto) &mdash; 高度或寬度是根據儲存格內容 (在 XAML 中為"Auto") 的自動調整大小
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star) &mdash; 剩餘的高度或寬度會按比例配置 (數字 」\*"，稱為*星狀*，在 XAML 中)

每一個子系`Grid`也必須要有的資料列和資料行 （明確或隱含）。 跨越資料列和資料行範圍是選擇性的。 這些所有使用指定的可繫結的附加的屬性&mdash;所定義的屬性`Grid`但設定上的子系`Grid`。 `Grid` 會定義四個靜態連結可繫結屬性：

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) &mdash; 以零為起始的資料列，預設值為 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) &mdash; 以零為起始的資料行;預設值為 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) &mdash; 數字的資料列的子系跨越;預設值為 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) &mdash; 數字資料行的子系跨越;預設值為 1

在程式碼中，程式可以使用八個靜態方法，來設定和取得這些值：

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

在 XAML 中，您可以使用下列屬性來設定這些值：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)範例示範如何建立和初始化`Grid`在 XAML 中。

`Grid`繼承[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/) 屬性從`Layout`並定義兩個提供的資料列和資料行之間間距的額外屬性：

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) 預設值為 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) 預設值為 6

`RowDefinitions`和`ColumnDefinitions`集合不是絕對必要。 如果不存在`Grid`建立資料列和資料行`Grid`子系，並提供所有預設值`GridLength`的 「\*"（星號）。

### <a name="the-grid-in-code"></a>程式碼中的方格

[ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)範例示範如何建立和填入`Grid`在程式碼中。 您可以設定附加的屬性，針對每個子系，直接或間接呼叫其他`Add`這類方法[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/)所定義[Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/)介面。

### <a name="the-grid-bar-chart"></a>[方格] 橫條圖

[ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)範例示範如何新增多個`BoxView`項目`Grid`使用大量[ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/)方法。 根據預設，這些`BoxView`元素具有相等的寬度。 每個高度`BoxView`然後您可以控制類似於橫條圖。

`Grid`中**GridBarChart**範例共用`AbsoluteLayout`父一開始隱形`Frame`。 程式也會設定`TapGestureRecognizer`在每個`BoxView`使用`Frame`顯示點選軸的相關資訊。

### <a name="alignment-in-the-grid"></a>在方格中的對齊方式

[ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)範例示範如何使用`VerticalOptions`並`HorizontalOptions`屬性，以對齊中的子系`Grid`資料格。

[ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)範例的平均空間`Button`項目置於`Grid`資料格。

### <a name="cell-dividers-and-borders"></a>儲存格分隔和框線

`Grid`不包含前一頁或檔案櫃儲存格框線的繪製功能。 不過，您可以讓您自己。

[ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)示範如何定義額外的資料列和資料行，專為精簡`BoxView`模仿分隔線的項目。

[ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程式不會建立任何額外的資料格，但改為將對齊`BoxView`來模擬的儲存格框線的每個儲存格中的項目。

## <a name="almost-real-life-grid-examples"></a>幾乎真實格線範例

[ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)範例會使用`Grid`顯示數字鍵台：

[![三重的螢幕擷取畫面的數字鍵台方格](images/ch17fg12-small.png "數字鍵台方格")](images/ch17fg12-large.png#lightbox "字鍵台方格")

### <a name="responding-to-orientation-changes"></a>回應方向變更

`Grid`有助於建構回應方向變更程式。 [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)範例會示範一種技術，第二個資料列是直向導向電話與橫向手機的第二個資料行之間移動項目。

程式初始化`Slider`一系列的 0 到 255 及十六進位顯示滑桿的值會使用資料繫結項目。 因為`Slider`點，以及.NET 格式字串的十六進位僅適用於具有整數，值則浮動[ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs)類別[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫可協助。



## <a name="related-links"></a>相關連結

- [第 17 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第 17 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [格線](~/xamarin-forms/user-interface/layouts/grid.md)
