---
title： "第17章的摘要。 主控方格「描述：」建立 Mobile Apps：第 Xamarin.Forms 17 章的摘要。 主控方格 "ms. 生產： xamarin ms. 技術： xamarin-assetid： 71EDEF9C-4220-4D2E-A235-43F1EC8746C1 author： davidbritch ms-chap： dabritch ms. date： 11/07/2017 no-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-17-mastering-the-grid"></a>第17章的摘要。 掌控格線

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)

[`Grid`](xref:Xamarin.Forms.Grid)是功能強大的版面配置機制，它會將其子系排列到儲存格的資料列和資料行中。 不同于類似的 HTML 專案 `table` ， `Grid` 僅適用于版面配置，而不是簡報的用途。

## <a name="the-basic-grid"></a>基本方格

`Grid`衍生自 [`Layout<View>`](xref:Xamarin.Forms.Layout`1) ，其定義 [`Children`](xref:Xamarin.Forms.Layout`1.Children) 繼承的屬性 `Grid` 。 您可以在 XAML 或程式碼中填入此集合。

### <a name="the-grid-in-xaml"></a>XAML 中的方格

XAML 中的定義 `Grid` 通常一開始會使用 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和物件填入的和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 集合 `Grid` [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 。 這是您建立的資料列和資料行數目 `Grid` 及其屬性的方式。

`RowDefinition`具有 [`Height`](xref:Xamarin.Forms.RowDefinition.Height) 屬性，而且 `ColumnDefinition` 具有 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 類型的屬性，也就 [`GridLength`](xref:Xamarin.Forms.GridLength) 是結構。

在 XAML 中，會 [`GridLengthTypeConverter`](xref:Xamarin.Forms.GridLengthTypeConverter) 將簡單的文字字串轉換成 `GridLength` 值。 在幕後，[構造函式 `GridLength` ] （x： Xamarin.Forms 。GridLength .% 23ctor （Double， Xamarin.Forms 。Gridunittype.auto））會 `GridLength` 根據數位和類型的值 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) （具有三個成員的列舉）來建立值：

- [`Absolute`](xref:Xamarin.Forms.GridUnitType.Absolute)&mdash;寬度或高度是以與裝置無關的單位來指定（XAML 中的數位）
- [`Auto`](xref:Xamarin.Forms.GridUnitType.Auto)&mdash;高度或寬度是根據資料格內容（在 XAML 中為 "Auto"）大小的
- [`Star`](xref:Xamarin.Forms.GridUnitType.Star)&mdash;剩餘的高度或寬度會按比例分配（在 XAML 中為具有 "" 的數位 \* ，稱為*星號*）

的每個子系 `Grid` 也必須指派一個資料列和資料行（明確或隱含）。 資料列範圍和資料行範圍是選擇性的。 這些全都是使用附加的可系結屬性屬性所指定 &mdash; ， `Grid` 但設定于的子系 `Grid` 。 `Grid`定義四個靜態附加的可系結屬性：

- [`RowProperty`](xref:Xamarin.Forms.Grid.RowProperty)以 &mdash; 零為基底的資料列，預設值為0
- [`ColumnProperty`](xref:Xamarin.Forms.Grid.ColumnProperty)以 &mdash; 零為基底的資料行; 預設值為0
- [`RowSpanProperty`](xref:Xamarin.Forms.Grid.RowSpanProperty)&mdash;子系跨越的資料列數目，預設值為1
- [`ColumnSpanProperty`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)&mdash;子系跨越的資料行數目，預設值為1

在程式碼中，程式可以使用八個靜態方法來設定和取得這些值：

- [ `Grid.SetRow` ] （x： Xamarin.Forms 。Grid. SetRow （ Xamarin.Forms 。BindableObject、System.object）和 [ `Grid.GetRow` ] （x： Xamarin.Forms 。Grid. GetRow （ Xamarin.Forms 。BindableObject））
- [ `Grid.SetColumn` ] （x： Xamarin.Forms 。Grid. SetColumn （ Xamarin.Forms 。BindableObject、System.object）和 [ `Grid.GetColumn` ] （x： Xamarin.Forms 。Grid. GetColumn （ Xamarin.Forms 。BindableObject））
- [ `Grid.SetRowSpan` ] （x： Xamarin.Forms 。Grid. SetRowSpan （ Xamarin.Forms 。BindableObject、System.object）和 [ `Grid.GetRowSpan` ] （x： Xamarin.Forms 。Grid. GetRowSpan （ Xamarin.Forms 。BindableObject））
- [ `Grid.SetColumnSpan` ] （x： Xamarin.Forms 。Grid. SetColumnSpan （ Xamarin.Forms 。BindableObject、System.object）和 [ `Grid.GetColumnSpan` ] （x： Xamarin.Forms 。Grid. GetColumnSpan （ Xamarin.Forms 。BindableObject））

在 XAML 中，您可以使用下列屬性來設定這些值：

- `Grid.Row`
- `Grid.Column`
- `Grid.RowSpan`
- `Grid.ColumnSpan`

[**SimpleGridDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SimpleGridDemo)範例會示範如何 `Grid` 在 XAML 中建立和初始化。

`Grid` [`Padding`](xref:Xamarin.Forms.Layout.Padding) 會從繼承屬性 `Layout` ，並定義兩個額外的屬性，以提供資料列和資料行之間的間距：

- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)的預設值為6
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)的預設值為6

`RowDefinitions`和 `ColumnDefinitions` 集合並不是絕對必要的。 如果不存在，則會建立子系的資料 `Grid` 列和資料行， `Grid` 並提供所有預設值 `GridLength` " \* " （星號）。

### <a name="the-grid-in-code"></a>程式碼中的方格

[**GridCodeDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCodeDemo)範例會示範如何在程式碼中建立和填入 `Grid` 。 您可以直接為每個子系設定附加屬性，或藉由呼叫 `Add` [`Add`](xref:Xamarin.Forms.Grid.IGridList`1.Add*) [IGridList <T> ](xref:Xamarin.Forms.Grid.IGridList`1)介面所定義的其他方法（例如）來間接設定。

### <a name="the-grid-bar-chart"></a>方格橫條圖

[**GridBarChart**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridBarChart)範例會示範如何 `BoxView` `Grid` 使用 bulk 方法，將多個專案加入 [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) 至。 根據預設，這些 `BoxView` 元素的寬度相同。 然後可以控制每個的高度 `BoxView` ，以類似橫條圖。

`Grid` **GridBarChart**範例中的會 `AbsoluteLayout` 與一開始不可見的父系共用 `Frame` 。 程式也會 `TapGestureRecognizer` 在每個上設定 `BoxView` ，以使用 `Frame` 來顯示有關攻絲列的資訊。

### <a name="alignment-in-the-grid"></a>貼齊格線

[**GridAlignment**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridAlignment)範例會示範如何使用 `VerticalOptions` 和 `HorizontalOptions` 屬性來對齊儲存格中的子系 `Grid` 。

[**SpacingButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/SpacingButtons)範例中的空格 `Button` 元素會以資料格為中心 `Grid` 。

### <a name="cell-dividers-and-borders"></a>資料格分隔和框線

不 `Grid` 包含繪製資料格分隔線或框線的功能。 不過，您可以自行建立。

此[**GridCellDividers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellDividers)示範如何定義更多的資料列和資料行，特別是精簡的 `BoxView` 元素，以模仿分行。

[**GridCellBorders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridCellBorders)程式不會建立任何其他資料格，而是會對齊 `BoxView` 每個資料格中的元素，以模擬資料格框線。

## <a name="almost-real-life-grid-examples"></a>幾乎真實生活的方格範例

[**KeypadGrid**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/KeypadGrid)範例會使用 `Grid` 來顯示鍵盤：

[![鍵盤格線的三向螢幕擷取畫面](images/ch17fg12-small.png "鍵盤方格")](images/ch17fg12-large.png#lightbox "鍵盤方格")

### <a name="responding-to-orientation-changes"></a>回應方向變更

`Grid`可以協助結構程式來回應方向變更。 [**GridRgbSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17/GridRgbSliders)範例示範一種技術，可在直向電話的第二列與橫向行動電話的第二個數據行之間移動元素。

程式會將 `Slider` 元素初始化為0到255的範圍，並使用資料系結以十六進位顯示滑杆的值。 因為 `Slider` 值是浮點數，而且十六進位的 .net 格式字串僅適用于整數，所以 [`DoubleToIntConvert`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DoubleToIntConverter.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別會有説明。

## <a name="related-links"></a>相關連結

- [第17章的全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch17-Apr2016.pdf)
- [第17章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter17)
- [Grid](~/xamarin-forms/user-interface/layouts/grid.md)
