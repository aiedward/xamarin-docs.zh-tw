---
title: "第 17 章的摘要。 主控方格"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 71EDEF9C-4220-4D2E-A235-43F1EC8746C1
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 09f63dd418ea1fb523c028edb02c28c22bfdccd1
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-17-mastering-the-grid"></a>第 17 章的摘要。 主控方格

[ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)是功能強大的配置排列成資料列和資料行的資料格的子系的機制。 不同於類似 HTML`table`項目，`Grid`主要僅供目的版面配置，而不是簡報。

## <a name="the-basic-grid"></a>基本的方格

`Grid` 衍生自[ `Layout<View>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/)，而後者可定義[ `Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout%3CT%3E.Children/)屬性，`Grid`繼承。 您可以填入這個集合中 XAML 或程式碼。

### <a name="the-grid-in-xaml"></a>XAML 中的方格

定義`Grid`在 XAML 中通常開頭填滿[ `RowDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowDefinitions/)和[ `ColumnDefinitions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnDefinitions/)集合`Grid`與[ `RowDefinition`](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/)和[ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/)物件。 這是您建立的資料列數目和資料行`Grid`，及其屬性。

`RowDefinition` 具有[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.RowDefinition.Height/)屬性和`ColumnDefinition`具有[ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ColumnDefinition.Width/)屬性，這兩個型別[ `GridLength` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLength/)，結構。

在 XAML 中、 [ `GridLengthTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridLengthTypeConverter/)轉換成的簡單的文字字串`GridLength`值。 在幕後[`GridLength`建構函式](https://developer.xamarin.com/api/constructor/Xamarin.Forms.GridLength.GridLength/p/System.Double/Xamarin.Forms.GridUnitType/)建立`GridLength`值根據數字和類型的值[ `GridUnitType` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/)，具有三個成員的列舉：

- [`Absolute`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Absolute/) （& c) 2014; #x裝置獨立單位 （必須是在 XAML 中的數字） 中指定寬度或高度
- [`Auto`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Auto/) （& c) 2014; #x高度或寬度是資料格內容 （在 XAML 中為 「 自動 」） 為基礎的自動調整大小
- [`Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/) （& c) 2014; #x剩餘的高度或寬度會按比例配置 (的數字 」\*"，稱為*星狀*，在 XAML 中)

每個子系的`Grid`也必須指派資料列和資料行 （明確或隱含）。 跨越資料列和資料行範圍是選擇性的。 這些被指定所有可繫結屬性 & #x 2014; 使用連接屬性所定義的`Grid`但設定的子系上`Grid`。 `Grid` 定義四個靜態連結可繫結屬性：

- [`RowProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowProperty/) （& c) 2014; #x以零為起始的資料列。預設值為 0
- [`ColumnProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnProperty/) （& c) 2014; #x以零為起始的資料行;預設值為 0
- [`RowSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.RowSpanProperty/) （& c) 2014; #x數字的資料列的子系跨越;預設值為 1
- [`ColumnSpanProperty`](https://developer.xamarin.com/api/field/Xamarin.Forms.Grid.ColumnSpanProperty/) （& c) 2014; #x數字資料行的子系跨越;預設值為 1

在程式碼中，程式可以使用八個靜態方法，以設定和取得這些值：

- [`Grid.SetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRow/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetRow`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRow/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumn/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetColumn`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumn/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetRowSpan/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetRowSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetRowSpan/p/Xamarin.Forms.BindableObject/)
- [`Grid.SetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.SetColumnSpan/p/Xamarin.Forms.BindableObject/System.Int32/) 和 [`Grid.GetColumnSpan`](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid.GetColumnSpan/p/Xamarin.Forms.BindableObject/)

在 XAML 中，您可以使用下列屬性來設定這些值：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[ **SimpleGridDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)範例將示範如何建立和初始化`Grid`在 XAML 中。

`Grid`繼承[ `Padding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Layout.Padding/)屬性從`Layout`和定義提供的資料列和資料行之間間距的兩個額外屬性：

- [`RowSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.RowSpacing/) 預設值為 6
- [`ColumnSpacing`](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.ColumnSpacing/) 預設值為 6

`RowDefinitions`和`ColumnDefinitions`集合不是絕對必要。 如果不存在`Grid`建立資料列和資料行`Grid`子系，並提供所有預設值`GridLength`的"\*"（星號）。

### <a name="the-grid-in-code"></a>程式碼中的方格

[ **GridCodeDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)範例示範如何建立和填入`Grid`程式碼中。 您可以設定附加的屬性，供每個子系直接或間接呼叫其他`Add`這類方法[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/)所定義[Grid.IGridList<T> ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid+IGridList%3CT%3E/)介面。

### <a name="the-grid-bar-chart"></a>[方格] 橫條圖

[ **GridBarChart** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)範例示範如何加入多個`BoxView`項目`Grid`使用大量[ `AddHorizontal` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.AddHorizontal/p/System.Collections.Generic.IEnumerable%7BXamarin.Forms.View%7D/)方法。 根據預設，這些`BoxView`元素具有相等的寬度。 每個高度`BoxView`然後類似橫條圖來控制。

`Grid`中**GridBarChart**範例共用`AbsoluteLayout`一開始隱藏父`Frame`。 程式也會設定為`TapGestureRecognizer`每`BoxView`使用`Frame`顯示點選軸的相關資訊。

### <a name="alignment-in-the-grid"></a>在方格中的對齊方式

[ **GridAlignment** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)範例示範如何使用`VerticalOptions`和`HorizontalOptions`屬性，以對齊中的子系`Grid`儲存格。

[ **SpacingButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)範例同樣空格`Button`項目置於`Grid`資料格。

### <a name="cell-dividers-and-borders"></a>儲存格分隔和框線

`Grid`不包括繪製儲存格分隔或框線的功能。 不過，您可以建立您自己。

[ **GridCellDividers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)示範如何定義額外的資料列和資料行，專為精簡`BoxView`模仿分隔線的項目。

[ **GridCellBorders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程式不會建立任何額外的資料格，但改為將對齊`BoxView`來模仿資料格框線每個資料格中的項目。

## <a name="almost-real-life-grid-examples"></a>幾乎真實生活格線範例

[ **KeypadGrid** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)範例會使用`Grid`顯示數字鍵台：

[![數字鍵台方格的三個螢幕擷取畫面](images/ch17fg12-small.png "字鍵台方格")](images/ch17fg12-large.png#lightbox "字鍵台方格")

### <a name="responding-to-orientation-changes"></a>回應方向變更

`Grid`有助於結構來回應方向變更程式。 [ **GridRgbSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)範例示範的技術是直式方向電話的第二個資料列和橫向的電話的第二個資料行之間移動項目。

在程式初始化`Slider`範圍 0 到 255，並顯示十六進位表示的滑桿的值會使用資料繫結項目。 因為`Slider`點，以及.NET 格式字串的十六進位只適用於具有整數，值則浮動[ `DoubleToIntConvert` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs)類別[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)的程式庫可幫助。



## <a name="related-links"></a>相關連結

- [第 17 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第 17 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [格線](~/xamarin-forms/user-interface/layouts/grid.md)
