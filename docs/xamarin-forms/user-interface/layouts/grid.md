---
title: Xamarin.Forms格
description: Xamarin.Forms方格是將其子系組織成資料列和資料行的版面配置。
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/15/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9d2e697a07e033fd7c3c8d3efffa1d67f6c097c3
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84946334"
---
# <a name="xamarinforms-grid"></a>Xamarin.Forms格

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![Xamarin.Forms格](grid-images/layouts.png "[!OP.無-LOC （Xamarin）] 方格")](grid-images/layouts-large.png#lightbox "[!OP.無-LOC （Xamarin）] 方格")

[`Grid`](xref:Xamarin.Forms.Grid)是將其子系組織成資料列和資料行的版面配置，其可具有比例或絕對大小。 根據預設，會 `Grid` 包含一個資料列和一個資料行。 此外， `Grid` 也可以當做包含其他子版面配置的父配置使用。

配置 [`Grid`](xref:Xamarin.Forms.Grid) 不應該與資料表混淆，而且不是用來呈現表格式資料。 不同于 HTML 資料表， `Grid` 是用於配置內容。 若要顯示表格式資料，請考慮使用[ListView](~/xamarin-forms/user-interface/listview/index.md)、 [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)或[TableView](~/xamarin-forms/user-interface/tableview.md)。

[`Grid`](xref:Xamarin.Forms.Grid)類別會定義下列屬性：

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty)，屬於類型 `int` ，這是附加屬性，表示父項內的視圖的資料行對齊方式 `Grid` 。 這個屬性的預設值為 0。 驗證回呼可確保當屬性已設定時，其值會大於或等於0。
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions)，屬於類型 [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) ， [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 這是定義格線資料行寬度的物件清單。
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing)，屬於類型 `double` ，表示格線資料行之間的距離。 此屬性的預設值為6個與裝置無關的單位。
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty)，屬於類型 `int` ，這是附加屬性，表示視圖在父系內的總數據行數 `Grid` 。 這個屬性的預設值為 1。 驗證回呼可確保當屬性已設定時，其值會大於或等於1。
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty)，屬於類型 `int` ，這是附加屬性，表示父項內的視圖的資料列對齊方式 `Grid` 。 這個屬性的預設值為 0。 驗證回呼可確保當屬性已設定時，其值會大於或等於0。
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions)，屬於類型 [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) ， [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) 這是定義格線資料列高度的物件清單。
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)，屬於類型 `double` ，表示格線列之間的距離。 此屬性的預設值為6個與裝置無關的單位。
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty)，屬於類型 `int` ，這是附加屬性，表示視圖在父系內的總數據列數 `Grid` 。 這個屬性的預設值為 1。 驗證回呼可確保當屬性已設定時，其值會大於或等於1。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。

[`Grid`](xref:Xamarin.Forms.Grid)類別衍生自 `Layout<T>` 類別，其定義 `Children` 類型的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循[優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的指導方針。

## <a name="rows-and-columns"></a>資料列和資料行

根據預設，會 [`Grid`](xref:Xamarin.Forms.Grid) 包含一個資料列和一個資料行：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

在此範例中， [`Grid`](xref:Xamarin.Forms.Grid) 包含 [`Label`](xref:Xamarin.Forms.Label) 自動放在單一位置的單一子系：

[![預設格線版面配置的螢幕擷取畫面](grid-images/default.png "預設格線版面配置")](grid-images/default-large.png#lightbox "預設格線版面配置")

的配置行為 [`Grid`](xref:Xamarin.Forms.Grid) 可以使用 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和屬性來定義 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) ，它們分別是和物件的集合 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 。 這些集合會定義的資料列和資料行特性 `Grid` ，而且應該包含 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 中每個資料列的一個物件， `Grid` 以及中每個資料行的一個 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 物件 `Grid` 。

[`RowDefinition`](xref:Xamarin.Forms.RowDefinition)類別會定義 [`Height`](xref:Xamarin.Forms.RowDefinition.Height) 類型為的屬性， [`GridLength`](xref:Xamarin.Forms.GridLength) 而類別則會 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 定義 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 類型的屬性 [`GridLength`](xref:Xamarin.Forms.GridLength) 。 [`GridLength`](xref:Xamarin.Forms.GridLength)結構會以列舉的角度指定資料列高度或資料行寬度 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) ，其中有三個成員：

- `Absolute`–資料列高度或資料行寬度是裝置獨立單位（XAML 中的數位）的值。
- `Auto`–資料列高度或資料行寬度是根據資料格內容（ `Auto` XAML）來大小。
- `Star`-剩餘的資料列高度或資料行寬度是按比例分配（後面接著 XAML 的數位 `*` ）。

[`Grid`](xref:Xamarin.Forms.Grid)具有 `Height` 的屬性的資料列 `Auto` 會以與垂直相同的方式，限制該資料列中的視圖高度 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 同樣地，具有屬性的資料行，其 `Width` `Auto` 運作方式非常類似水準 `StackLayout` 。

> [!CAUTION]
> 請嘗試確保最少的資料列和資料行設定為 [ [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小]。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 或者，使用列舉值來設定資料列和資料行，以佔用比例的空間量 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) 。

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

在此範例中， [`Grid`](xref:Xamarin.Forms.Grid) 具有整體高度，也就是頁面的高度。 `Grid`知道第三個數據列的高度是100裝置獨立單位。 它會從其本身的高度減去該高度，並根據星號前面的數位，在第一個和第二個數據列之間按比例分配剩餘的高度。 在此範例中，第一個資料列的高度是第二個數據列的兩倍。

這兩個 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 物件都將設定 [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 為 `*` ，這與相同 `1*` ，這表示畫面的寬度在兩個數據行底下平均地分割。

> [!IMPORTANT]
> 屬性的預設值 [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) 為 `*` 。 同樣地，屬性的預設值 [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) 為 `*` 。 因此，在可接受這些預設值的情況下，並不需要設定這些屬性。

子視圖可以放在 [`Grid`](xref:Xamarin.Forms.Grid) 具有 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和附加屬性的特定資料格中 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 。 此外，若要讓子視圖跨越多個資料列和資料行，請使用 [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 和 [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加屬性。

下列 XAML 會顯示相同的 [`Grid`](xref:Xamarin.Forms.Grid) 定義，也會將子視圖放在特定的資料 `Grid` 格內：

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
> `Grid.Row`和 `Grid.Column` 屬性都是從0編制索引，而是 `Grid.Row="2"` 指第三個數據列，而則 `Grid.Column="1"` 是指第二個數據行。 此外，這兩個屬性的預設值都是0，因此不需要在佔用第一個資料列或第一個資料行的子視圖上設定 [`Grid`](xref:Xamarin.Forms.Grid) 。

在此範例中，所有三個數據 [`Grid`](xref:Xamarin.Forms.Grid) 列都是由 [`BoxView`](xref:Xamarin.Forms.BoxView) 和 views 所佔用 [`Label`](xref:Xamarin.Forms.Label) 。 第三個數據列是100裝置獨立單位高，其中前兩個數據列佔用剩餘的空間（第一個資料列的數目與第二個數據列的高兩倍）。 這兩個數據行的寬度相等，並 `Grid` 以半形分割。 `BoxView`第三個數據列中的會跨越這兩個數據行。

[![基本方格版面配置的螢幕擷取畫面](grid-images/basic.png "基本方格版面配置")](grid-images/basic-large.png#lightbox "基本方格版面配置")

此外，中的子視圖 [`Grid`](xref:Xamarin.Forms.Grid) 可以共用資料格。 子系出現在 XAML 中的順序，就是子系放在中的順序 `Grid` 。 在上述範例中， [`Label`](xref:Xamarin.Forms.Label) 只會顯示物件，因為它們會呈現在 [`BoxView`](xref:Xamarin.Forms.BoxView) 物件上。 `Label`如果 `BoxView` 物件呈現在其上方，則不會顯示這些物件。

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

在程式碼中，若要指定物件的高度 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 和物件的寬度， [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 您可以使用結構的值 [`GridLength`](xref:Xamarin.Forms.GridLength) （通常與列舉結合） [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) 。

上述範例程式碼也會示範數種不同的方法，可將子系加入至 [`Grid`](xref:Xamarin.Forms.Grid) ，並指定其所在的資料格。 使用 `Add` 指定*left*、 *right*、 *top*和*底端*引數的多載時，*左側*和*頂端*引數一律會參考內的資料格 `Grid` ，而*右邊*和*底端*引數則是指參考位於以外的資料格 `Grid` 。 這是因為*右*引數必須一律大於*左邊*的引數，而*下*一個引數必須一律大於*top*引數。 下列範例假設是一個2x2，會 `Grid` 使用這兩個多載來顯示對等的程式碼 `Add` ：

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
> 此外，您可以使用和方法將子視圖加入至 [`Grid`](xref:Xamarin.Forms.Grid) [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) ，這會將子系加入至單一資料列或單一資料行 `Grid` 。 `Grid`接著會在進行這些呼叫時展開資料列或資料行，並自動將子系定位在正確的資料格中。

### <a name="simplify-row-and-column-definitions"></a>簡化資料列和資料行定義

在 XAML 中，您可以使用簡化的語法來指定的資料列和資料行特性， [`Grid`](xref:Xamarin.Forms.Grid) 避免必須 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 為每個資料列和資料行定義和物件。 相反地， [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 屬性可以設定為包含逗號分隔值的字串 [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) ，其中內建在 Xamarin.Forms create `RowDefinition` 和 objects 中的類型轉換器 `ColumnDefinition` ：

```xaml
<Grid RowDefinitions="1*, Auto, 25, 14, 20"
      ColumnDefinitions="*, 2*, Auto, 300">
    ...
</Grid>
```

在此範例中， [`Grid`](xref:Xamarin.Forms.Grid) 有五個數據列和四個數據行。 第三個、向和第五個數據列設定為絕對高度，第二個數據列會自動調整大小為其內容。 其餘的高度則會配置到第一個資料列。

[向下] 資料行會設定為 [絕對寬度]，而第三個數據行則會自動調整大小為其內容。 在第一個和第二個數據行之間，會根據星號前面的數位來按比例分配其餘寬度。 在此範例中，第二個數據行的寬度是第一個資料行的兩倍（因為與 `*` 相同 `1*` ）。

## <a name="space-between-rows-and-columns"></a>資料列與資料行之間的間距

根據預設，資料 [`Grid`](xref:Xamarin.Forms.Grid) 列會以6個與裝置無關的空間單位來分隔。 同樣地，資料 `Grid` 行是以6個與裝置無關的空間單位分隔。 您可以分別設定和屬性來變更這些預設值 [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) ：

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

這個範例會建立在 [`Grid`](xref:Xamarin.Forms.Grid) 其資料列和資料行之間沒有間距的。

[![資料格之間沒有間距的方格螢幕擷取畫面](grid-images/spacing.png "資料格之間沒有間距的方格")](grid-images/spacing-large.png#lightbox "資料格之間沒有間距的方格")

> [!TIP]
> [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing)和 [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) 屬性可以設定為負值，讓資料格的內容重迭。

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

中的子視圖 [`Grid`](xref:Xamarin.Forms.Grid) 可以由和屬性放在其資料格內 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 這些屬性可以設定為結構中的下欄欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> `AndExpands`結構中的欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) 僅適用于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件。

下列 XAML 會建立 [`Grid`](xref:Xamarin.Forms.Grid) 具有九個相等大小儲存格的，並 [`Label`](xref:Xamarin.Forms.Label) 在每個資料格中放置一個不同的對齊方式：

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

在此範例中， [`Label`](xref:Xamarin.Forms.Label) 每個資料列中的物件都是垂直對齊，但使用不同的水準對齊方式。 或者，您也可以將這視為 `Label` 每個資料行中的物件水準對齊，但使用不同的垂直對齊方式：

[![方格中的儲存格對齊螢幕擷取畫面](grid-images/alignment.png "方格中的資料格對齊")](grid-images/alignment-large.png#lightbox "方格中的資料格對齊")

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

[`Grid`](xref:Xamarin.Forms.Grid)可以當做包含嵌套子 `Grid` 物件或其他子版面配置的父配置使用。 當嵌套 `Grid` 物件時， `Grid.Row` 、 `Grid.Column` 、 `Grid.RowSpan` 和 `Grid.ColumnSpan` 附加屬性一律會參考其父系內的視圖位置 `Grid` 。

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

在此範例中，根配置會 [`Grid`](xref:Xamarin.Forms.Grid) [`BoxView`](xref:Xamarin.Forms.BoxView) 在其第一個資料列中包含，並在第二個數據列中包含子系 `Grid` 。 子系 `Grid` 包含 [`Slider`](xref:Xamarin.Forms.Slider) 操作所顯示色彩的物件 `BoxView` ，以及 [`Label`](xref:Xamarin.Forms.Label) 顯示每個值的物件 `Slider` ：

[![嵌套格線的螢幕擷取畫面](grid-images/nesting.png "嵌套格線物件")](grid-images/nesting-large.png#lightbox "嵌套格線物件")

> [!IMPORTANT]
> 您的嵌套 [`Grid`](xref:Xamarin.Forms.Grid) 物件和其他配置越深入，嵌套的配置就會影響效能。 如需詳細資訊，請參閱[選擇正確的版面](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout)配置。

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

- [方格示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [中的版面配置選項Xamarin.Forms](layout-options.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)
