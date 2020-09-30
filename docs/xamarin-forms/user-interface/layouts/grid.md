---
title: Xamarin.Forms 網 格
description: 方格是將其子系 Xamarin.Forms 組織成資料列和資料行的版面配置。
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 59a4d5c0730719d6cc332728c6cab641d02239b4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563558"
---
# <a name="no-locxamarinforms-grid"></a>Xamarin.Forms 網 格

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![：：：非 loc (Xamarin. Forms) ：：： Grid](grid-images/layouts.png "：：：非 loc (Xamarin. Forms) ：：： Grid")](grid-images/layouts-large.png#lightbox "：：：非 loc (Xamarin. Forms) ：：： Grid")

是將其子系 [`Grid`](xref:Xamarin.Forms.Grid) 組織成資料列和資料行的版面配置，其可以有比例或絕對大小。 依預設，會 `Grid` 包含一個資料列和一個資料行。 此外， `Grid` 也可以當做包含其他子配置的父配置使用。

此配置 [`Grid`](xref:Xamarin.Forms.Grid) 不應與資料表混淆，也不能呈現表格式資料。 與 HTML 資料表不同的 `Grid` 是，是為了配置內容而設計。 若要顯示表格式資料，請考慮使用 [ListView](~/xamarin-forms/user-interface/listview/index.md)、 [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)或 [TableView](~/xamarin-forms/user-interface/tableview.md)。

[`Grid`](xref:Xamarin.Forms.Grid)類別會定義下列屬性：

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty)型別 `int` 為的，其為附加屬性，表示在父系內的視圖資料行對齊 `Grid` 。 這個屬性的預設值為 0。 驗證回呼可確保當設定屬性時，其值大於或等於0。
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions)型別為的， [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) 是 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 定義方格資料行寬度的物件清單。
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)型別為的 `double` ，表示方格資料行之間的距離。 這個屬性的預設值為6個裝置獨立單位。
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)型別 `int` 為的，其為附加屬性，指出視圖在父系中的總數據行數 `Grid` 。 這個屬性的預設值為 1。 驗證回呼可確保當設定屬性時，其值會大於或等於1。
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty)型別 `int` 為的，其為附加屬性，表示在父系內的視圖資料列對齊 `Grid` 。 這個屬性的預設值為 0。 驗證回呼可確保當設定屬性時，其值大於或等於0。
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions)型別為的， [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) 是 [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) 定義方格資料列高度的物件清單。
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)型別為的 `double` ，表示方格資料列之間的距離。 這個屬性的預設值為6個裝置獨立單位。
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)型別 `int` 為的，其為附加屬性，指出視圖在父系中的總數據列數 `Grid` 。 這個屬性的預設值為 1。 驗證回呼可確保當設定屬性時，其值會大於或等於1。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。

[`Grid`](xref:Xamarin.Forms.Grid)類別衍生自 `Layout<T>` 類別，該類別會定義 `Children` 型別的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循將 [版面配置效能優化](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)的指導方針。

## <a name="rows-and-columns"></a>資料列和資料行

依預設，會 [`Grid`](xref:Xamarin.Forms.Grid) 包含一個資料列和一個資料行：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

在此範例中， [`Grid`](xref:Xamarin.Forms.Grid) [`Label`](xref:Xamarin.Forms.Label) 會包含自動放置在單一位置的單一子系：

[![預設格線版面配置的螢幕擷取畫面](grid-images/default.png "預設格線版面配置")](grid-images/default-large.png#lightbox "預設格線版面配置")

的配置行為 [`Grid`](xref:Xamarin.Forms.Grid) 可以使用 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 屬性（分別是和物件的集合）來定義 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 。 這些集合會定義的資料列和資料行特性 `Grid` ，而且應該 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 針對中的每個資料列包含一個物件 `Grid` ，以及 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 中的每個資料行都包含一個物件 `Grid` 。

[`RowDefinition`](xref:Xamarin.Forms.RowDefinition)類別會定義 [`Height`](xref:Xamarin.Forms.RowDefinition.Height) 型別的屬性（property）， [`GridLength`](xref:Xamarin.Forms.GridLength) 而 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 類別會定義 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 型別的屬性（property） [`GridLength`](xref:Xamarin.Forms.GridLength) 。 [`GridLength`](xref:Xamarin.Forms.GridLength)結構會以列舉來指定資料列高度或資料行寬度 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) ，其中有三個成員：

- `Absolute` –資料列高度或資料行寬度是與裝置無關的單位值， (XAML) 中的數位。
- `Auto` –資料列高度或資料行寬度是根據 XAML) 中的資料格內容 (來 autosized `Auto` 。
- `Star` –剩餘的資料列高度或資料行寬度會依比例 (數位，然後 `*` 在 XAML) 中進行分配。

[`Grid`](xref:Xamarin.Forms.Grid)具有 `Height` 屬性的資料列 `Auto` 會以垂直的相同方式來限制該資料列中的視圖高度 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 同樣地，具有屬性的資料行也相當於 `Width` `Auto` 水準 `StackLayout` 。

> [!CAUTION]
> 請嘗試確定最少的資料列和資料行都設定為 [ [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小]。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 另外，也可以設定資料列和資料行，以佔用具有列舉值的比例空間量 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) 。

下列 XAML 顯示如何建立 [`Grid`](xref:Xamarin.Forms.Grid) 具有三個數據列和兩個數據行的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

在此範例中，的 [`Grid`](xref:Xamarin.Forms.Grid) 整體高度為頁面的高度。 `Grid`知道第三個數據列的高度是100與裝置無關的單位。 它會從本身的高度減去高度，並根據星號之前的數位，在第一個和第二個數據列之間按比例配置剩餘的高度。 在此範例中，第一個資料列的高度是第二個數據列的兩倍。

這兩個 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 物件都會將設定 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 為，也 `*` 就是相同的 `1*` ，這表示畫面的寬度會平均分散在兩個數據行下方。

> [!IMPORTANT]
> 屬性的預設值 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 為 `*` 。 同樣地，屬性的預設值 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 為 `*` 。 因此，在可以接受這些預設值的情況下，並不需要設定這些屬性。

子視圖可放置在 [`Grid`](xref:Xamarin.Forms.Grid) 具有 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和附加屬性的特定資料格中 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 。 此外，若要讓子視圖跨多個資料列和資料行，請使用 [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 和 [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加屬性。

下列 XAML 顯示相同的 [`Grid`](xref:Xamarin.Forms.Grid) 定義，也會在特定資料格中放置子視圖 `Grid` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <BoxView Color="Green" />
        <Label Text="Row 0, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Column="1"
                 Color="Blue" />
        <Label Grid.Column="1"
               Text="Row 0, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Color="Teal" />
        <Label Grid.Row="1"
               Text="Row 1, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="Purple" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Row1, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Grid.ColumnSpan="2"
                 Color="Red" />
        <Label Grid.Row="2"
               Grid.ColumnSpan="2"
               Text="Row 2, Columns 0 and 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

> [!NOTE]
> `Grid.Row`和 `Grid.Column` 屬性都是從0建立索引，因此參考第三個數據列， `Grid.Row="2"` 而 `Grid.Column="1"` 參考第二個數據行。 此外，這兩個屬性的預設值都是0，所以不需要在佔用第一個資料列或第一個資料行的子視圖上設定 [`Grid`](xref:Xamarin.Forms.Grid) 。

在此範例中，所有三個數據 [`Grid`](xref:Xamarin.Forms.Grid) 列都是由 [`BoxView`](xref:Xamarin.Forms.BoxView) 和 views 所佔用 [`Label`](xref:Xamarin.Forms.Label) 。 第三個數據列是100與裝置無關的單位，前兩個數據列佔用剩餘的空間 (第一個資料列的最大數目為第二個數據列) 。 這兩個數據行的寬度是相等的，並將 `Grid` 一半除以一半。 `BoxView`第三列的會跨越兩個數據行。

[![基本格線版面配置的螢幕擷取畫面](grid-images/basic.png "基本格線版面配置")](grid-images/basic-large.png#lightbox "基本格線版面配置")

此外，中的子視圖 [`Grid`](xref:Xamarin.Forms.Grid) 可以共用資料格。 子系出現在 XAML 中的順序，就是子系放在中的順序 `Grid` 。 在上述範例中， [`Label`](xref:Xamarin.Forms.Label) 只會顯示物件，因為它們是在物件的最上層呈現 [`BoxView`](xref:Xamarin.Forms.BoxView) 。 `Label`如果 `BoxView` 物件是在這些物件上轉譯，則這些物件將不會顯示。

對等的 C# 程式碼為：

```csharp
public class BasicGridPageCS : ContentPage
{
    public BasicGridPageCS()
    {
        Grid grid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition { Height = new GridLength(2, GridUnitType.Star) },
                new RowDefinition(),
                new RowDefinition { Height = new GridLength(100) }
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        // The BoxView and Label are in row 0 and column 0, and so only needs to be added to the
        // Grid.Children collection to get default row and column settings.
        grid.Children.Add(new BoxView
        {
            Color = Color.Green
        });
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        });

        // This BoxView and Label are in row 0 and column 1, which are specified as arguments
        // to the Add method.
        grid.Children.Add(new BoxView
        {
            Color = Color.Blue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 0);

        // Row 1
        // This BoxView and Label are in row 1 and column 0, which are specified as arguments
        // to the Add method overload.
        grid.Children.Add(new BoxView
        {
            Color = Color.Teal
        }, 0, 1, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row 1, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1, 1, 2); // These arguments indicate that that the child element goes in the column starting at 0 but ending before 1.
                        // They also indicate that the child element goes in the row starting at 1 but ending before 2.

        grid.Children.Add(new BoxView
        {
            Color = Color.Purple
        }, 1, 2, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row1, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 2, 1, 2);

        // Row 2
        // Alternatively, the BoxView and Label can be positioned in cells with the Grid.SetRow
        // and Grid.SetColumn methods.
        BoxView boxView = new BoxView { Color = Color.Red };
        Grid.SetRow(boxView, 2);
        Grid.SetColumnSpan(boxView, 2);
        Label label = new Label
        {
            Text = "Row 2, Column 0 and 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        };
        Grid.SetRow(label, 2);
        Grid.SetColumnSpan(label, 2);

        grid.Children.Add(boxView);
        grid.Children.Add(label);

        Title = "Basic Grid demo";
        Content = grid;
    }
}
```

在程式碼中，若要指定物件的高度 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 和物件的寬度， [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 您可以使用結構的值 [`GridLength`](xref:Xamarin.Forms.GridLength) ，通常會與列舉組合使用 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) 。

上述範例程式碼也會顯示幾種不同的方法，可將子系加入至 [`Grid`](xref:Xamarin.Forms.Grid) ，並指定其所在的資料格。 使用 `Add` 指定 *left*、 *right*、 *top*和 *底端* 引數的多載時， *左邊* 和 *top* 引數一律會參考中的儲存格，而 `Grid` *右邊* 和 *底部* 引數則會顯示為外部的資料格 `Grid` 。 這是因為 *右邊* 的引數必須一律大於 *左邊* 的引數，而 *底部* 的引數必須一律大於 *top* 引數。 下列範例假設 2x2 `Grid` ，會使用這兩個多載來顯示對等的程式碼 `Add` ：

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);           // first column, first row
grid.Children.Add(topRight, 1, 0);          // second column, first tow
grid.Children.Add(bottomLeft, 0, 1);        // first column, second row
grid.Children.Add(bottomRight, 1, 1);       // second column, second row

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);     // first column, first row
grid.Children.Add(topRight, 1, 2, 0, 1);    // second column, first tow
grid.Children.Add(bottomLeft, 0, 1, 1, 2);  // first column, second row
grid.Children.Add(bottomRight, 1, 2, 1, 2); // second column, second row
```

> [!NOTE]
> 此外，您可以 [`Grid`](xref:Xamarin.Forms.Grid) 使用和方法，將子視圖加入 [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) 至 [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) ，這會將子系加入至單一資料列或單一資料行 `Grid` 。 `Grid`接著會在進行這些呼叫時展開資料列或資料行，以及自動定位正確資料格中的子系。

### <a name="simplify-row-and-column-definitions"></a>簡化資料列和資料行定義

在 XAML 中，您可以使用簡化的語法來指定的資料列和資料行特性， [`Grid`](xref:Xamarin.Forms.Grid) 避免必須 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 針對每個資料列和資料行定義和物件。 相反地， [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 屬性可以設定為包含逗點分隔值的字串 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) ，而這些值是內建于 Xamarin.Forms create `RowDefinition` 和 objects 的型別轉換器 `ColumnDefinition` ：

```xaml
<Grid RowDefinitions="1*, Auto, 25, 14, 20"
      ColumnDefinitions="*, 2*, Auto, 300">
    ...
</Grid>
```

在此範例中， [`Grid`](xref:Xamarin.Forms.Grid) 有五個數據列和四個數據行。 第三個、第三個和第五個數據列設定為絕對高度，第二個數據列會自動調整大小為其內容。 其餘的高度接著會配置到第一個資料列。

第三個數據行設定為絕對寬度，第三個數據行則會自動調整大小為其內容。 其餘寬度會根據星號之前的數位，在第一個和第二個數據行之間按比例分配。 在此範例中，第二個數據行的寬度是第一個資料行 (的兩倍，因為等同 `*` 于 `1*`) 。

## <a name="space-between-rows-and-columns"></a>資料列和資料行之間的空間

依預設，資料 [`Grid`](xref:Xamarin.Forms.Grid) 列是以6個裝置獨立的空間單位分隔。 同樣地，資料 `Grid` 行是以6個裝置獨立的空間單位分隔。 您可以分別設定和屬性來變更這些預設值 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridSpacingPage"
             Title="Grid spacing demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        ..
    </Grid>
</ContentPage>
```

這個範例會建立一個 [`Grid`](xref:Xamarin.Forms.Grid) ，其資料列和資料行之間沒有間距：

[![資料格之間沒有間距之方格的螢幕擷取畫面](grid-images/spacing.png "資料格之間沒有間距的方格")](grid-images/spacing-large.png#lightbox "資料格之間沒有間距的方格")

> [!TIP]
> [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)和 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 屬性可以設定為負值，以使資料格的內容重迭。

對等的 C# 程式碼為：

```csharp
public GridSpacingPageCS()
{
    Grid grid = new Grid
    {
        RowSpacing = 0,
        ColumnSpacing = 0,
        // ...
    };
    // ...

    Content = grid;
}
```

## <a name="alignment"></a>對齊

[`Grid`](xref:Xamarin.Forms.Grid)和屬性可以將中的子視圖置於其資料格 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 內 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 您可以從結構將這些屬性設定為下欄欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> `AndExpands`結構中的欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 僅適用于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件。

下列 XAML 會建立 [`Grid`](xref:Xamarin.Forms.Grid) 具有九個相等大小儲存格的，並 [`Label`](xref:Xamarin.Forms.Label) 在每個資料格中放入不同的對齊方式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridAlignmentPage"
             Title="Grid alignment demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <BoxView Color="AliceBlue" />
        <Label Text="Upper left"
               HorizontalOptions="Start"
               VerticalOptions="Start" />
        <BoxView Grid.Column="1"
                 Color="LightSkyBlue" />
        <Label Grid.Column="1"
               Text="Upper center"
               HorizontalOptions="Center"
               VerticalOptions="Start"/>
        <BoxView Grid.Column="2"
                 Color="CadetBlue" />
        <Label Grid.Column="2"
               Text="Upper right"
               HorizontalOptions="End"
               VerticalOptions="Start" />
        <BoxView Grid.Row="1"
                 Color="CornflowerBlue" />
        <Label Grid.Row="1"
               Text="Center left"
               HorizontalOptions="Start"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="DodgerBlue" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Center center"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="2"
                 Color="DarkSlateBlue" />
        <Label Grid.Row="1"
               Grid.Column="2"
               Text="Center right"
               HorizontalOptions="End"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Color="SteelBlue" />
        <Label Grid.Row="2"
               Text="Lower left"
               HorizontalOptions="Start"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="1"
                 Color="LightBlue" />
        <Label Grid.Row="2"
               Grid.Column="1"
               Text="Lower center"
               HorizontalOptions="Center"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="2"
                 Color="BlueViolet" />
        <Label Grid.Row="2"
               Grid.Column="2"
               Text="Lower right"
               HorizontalOptions="End"
               VerticalOptions="End" />
    </Grid>
</ContentPage>
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label) 每個資料列中的物件都會垂直對齊，但使用不同的水準對齊方式。 或者，您也可以將這 `Label` 兩個數據行中的物件視為水準對齊，但使用不同的垂直對齊方式：

[![方格內對齊儲存格的螢幕擷取畫面](grid-images/alignment.png "方格中的儲存格對齊")](grid-images/alignment-large.png#lightbox "方格中的儲存格對齊")

對等的 C# 程式碼為：

```csharp
public class GridAlignmentPageCS : ContentPage
{
    public GridAlignmentPageCS()
    {
        Grid grid = new Grid
        {
            RowSpacing = 0,
            ColumnSpacing = 0,
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        grid.Children.Add(new BoxView
        {
            Color = Color.AliceBlue
        });
        grid.Children.Add(new Label
        {
            Text = "Upper left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        });

        grid.Children.Add(new BoxView
        {
            Color = Color.LightSkyBlue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Start
        }, 1, 0);

        grid.Children.Add(new BoxView
        {
            Color = Color.CadetBlue
        }, 2, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Start
        }, 2, 0);

        // Row 1
        grid.Children.Add(new BoxView
        {
            Color = Color.CornflowerBlue
        }, 0, 1);
        grid.Children.Add(new Label
        {
            Text = "Center left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DodgerBlue
        }, 1, 1);
        grid.Children.Add(new Label
        {
            Text = "Center center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DarkSlateBlue
        }, 2, 1);
        grid.Children.Add(new Label
        {
            Text = "Center right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Center
        }, 2, 1);

        // Row 2
        grid.Children.Add(new BoxView
        {
            Color = Color.SteelBlue
        }, 0, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.End
        }, 0, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.LightBlue
        }, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.End
        }, 1, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.BlueViolet
        }, 2, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.End
        }, 2, 2);

        Title = "Grid alignment demo";
        Content = grid;
    }
}
```

## <a name="nested-grid-objects"></a>嵌套格線物件

[`Grid`](xref:Xamarin.Forms.Grid)可用來做為父配置，其中包含嵌套的子 `Grid` 物件或其他子版面配置。 當嵌套 `Grid` 物件時， `Grid.Row` 、 `Grid.Column` 、 `Grid.RowSpan` 和 `Grid.ColumnSpan` 附加屬性一律會參考其父系內的視圖位置 `Grid` 。

下列 XAML 顯示的是嵌套物件的範例 [`Grid`](xref:Xamarin.Forms.Grid) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:converters="clr-namespace:GridDemos.Converters"
             x:Class="GridDemos.Views.ColorSlidersGridPage"
             Title="Nested Grids demo">

    <ContentPage.Resources>
        <converters:DoubleToIntConverter x:Key="doubleToInt" />

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment"
                    Value="Center" />
        </Style>
    </ContentPage.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <BoxView x:Name="boxView"
                 Color="Black" />
        <Grid Grid.Row="1"
              Margin="20">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Slider x:Name="redSlider"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="1"
                   Text="{Binding Source={x:Reference redSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0}'}" />
            <Slider x:Name="greenSlider"
                    Grid.Row="2"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="3"
                   Text="{Binding Source={x:Reference greenSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0}'}" />
            <Slider x:Name="blueSlider"
                    Grid.Row="4"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="5"
                   Text="{Binding Source={x:Reference blueSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Blue = {0}'}" />
        </Grid>
    </Grid>
</ContentPage>
```

在此範例中，根配置 [`Grid`](xref:Xamarin.Forms.Grid) 包含 [`BoxView`](xref:Xamarin.Forms.BoxView) 其第一個資料列中的，以及 `Grid` 第二個數據列中的子系。 子系 `Grid` 包含 [`Slider`](xref:Xamarin.Forms.Slider) 物件，這些物件會操作所顯示的色彩 `BoxView` ，以及 [`Label`](xref:Xamarin.Forms.Label) 顯示每個的值的物件 `Slider` ：

[![嵌套格線的螢幕擷取畫面](grid-images/nesting.png "嵌套格線物件")](grid-images/nesting-large.png#lightbox "嵌套格線物件")

> [!IMPORTANT]
> 您可以更深入 [`Grid`](xref:Xamarin.Forms.Grid) 地嵌套物件和其他配置，嵌套的版面配置會影響效能。 如需詳細資訊，請參閱 [選擇正確的版面](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout)配置。

對等的 C# 程式碼為：

```csharp
public class ColorSlidersGridPageCS : ContentPage
{
    BoxView boxView;
    Slider redSlider;
    Slider greenSlider;
    Slider blueSlider;

    public ColorSlidersGridPageCS()
    {
        // Create an implicit style for the Labels
        Style labelStyle = new Style(typeof(Label))
        {
            Setters =
            {
                new Setter { Property = Label.HorizontalTextAlignmentProperty, Value = TextAlignment.Center }
            }
        };
        Resources.Add(labelStyle);

        // Root page layout
        Grid rootGrid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition()
            }
        };

        boxView = new BoxView { Color = Color.Black };
        rootGrid.Children.Add(boxView);

        // Child page layout
        Grid childGrid = new Grid
        {
            Margin = new Thickness(20),
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            }
        };

        DoubleToIntConverter doubleToInt = new DoubleToIntConverter();

        redSlider = new Slider();
        redSlider.ValueChanged += OnSliderValueChanged;
        childGrid.Children.Add(redSlider);

        Label redLabel = new Label();
        redLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Red = {0}", source: redSlider));
        Grid.SetRow(redLabel, 1);
        childGrid.Children.Add(redLabel);

        greenSlider = new Slider();
        greenSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(greenSlider, 2);
        childGrid.Children.Add(greenSlider);

        Label greenLabel = new Label();
        greenLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Green = {0}", source: greenSlider));
        Grid.SetRow(greenLabel, 3);
        childGrid.Children.Add(greenLabel);

        blueSlider = new Slider();
        blueSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(blueSlider, 4);
        childGrid.Children.Add(blueSlider);

        Label blueLabel = new Label();
        blueLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Blue = {0}", source: blueSlider));
        Grid.SetRow(blueLabel, 5);
        childGrid.Children.Add(blueLabel);

        // Place the child Grid in the root Grid
        rootGrid.Children.Add(childGrid, 0, 1);

        Title = "Nested Grids demo";
        Content = rootGrid;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        boxView.Color = new Color(redSlider.Value, greenSlider.Value, blueSlider.Value);
    }
}
```

## <a name="related-links"></a>相關連結

- [方格示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [中的版面配置選項 Xamarin.Forms](layout-options.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)