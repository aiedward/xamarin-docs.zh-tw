---
title: Xamarin.Forms 方格
description: 這篇文章說明如何使用 Xamarin.Forms 方格類別來呈現檢視擁有的資料列和資料行的方格中。
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 0a1aa16aca1507153d691d5ec801a75e49e5d3ad
ms.sourcegitcommit: 06a52ac36031d0d303ac7fc8163a59c178799c80
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2018
ms.locfileid: "50911576"
---
# <a name="xamarinforms-grid"></a>Xamarin.Forms 方格

[`Grid`](xref:Xamarin.Forms.Grid) 排列成資料列和資料行的檢視表的支援。 資料列和資料行，都可以設定成比例的大小或絕對的大小。 `Grid`版面配置不應混淆與傳統的資料表，而不是呈現表格式資料。 `Grid` 沒有資料列、 資料行或資料格的格式化概念。 不同於 HTML 資料表，`Grid`純粹是用來配置內容。

[![](grid-images/layouts-sml.png "Xamarin.Forms 版面配置")](grid-images/layouts.png#lightbox "Xamarin.Forms 版面配置")

本文將介紹：

- **[用途](#purpose)** &ndash;的常見用法`Grid`。
- **[使用方式](#usage)** &ndash;如何使用`Grid`以達到您所需的設計。
  - **[資料列和資料行](#rows-and-columns)** &ndash;指定的資料列和資料行`Grid`。
  - **[將檢視放](#placing-views-in-a-grid)** &ndash;將檢視加入至方格的特定資料列和資料行。
  - **[間距](#spacing)** &ndash;設定資料列和資料行之間的空格。
  - **[Span](#spans)**  &ndash;設定跨越多個資料列或資料行的項目。

![](grid-images/grid.png "方格探勘")

## <a name="purpose"></a>用途

`Grid` 可用來排列方格的檢視。 這是在許多情況下很有用：

- 排列的計算機應用程式中的按鈕
- 排列按鈕/選擇在方格中，例如 iOS 或 Android 的主畫面
- 排列檢視，使其在一個維度 （如某些工具列） 中的相同大小的

## <a name="usage"></a>使用量

不同於傳統的資料表，`Grid`不會推斷的資料列和資料行內容的大小與數量。 相反地，`Grid`已經`RowDefinitions`和`ColumnDefinitions`集合。 這些保留資料列和資料行的數目會配置的定義。檢視會新增至`Grid`與指定的資料列和資料行索引，其識別哪些資料列和資料行應置於檢視。

### <a name="rows-and-columns"></a>資料列和資料行

資料列和資料行的資訊會儲存在`Grid`的`RowDefinitions`  &  `ColumnDefinitions`屬性，也就是每個集合的[ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition)並[ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)物件，分別。 `RowDefinition` 具有單一屬性， `Height`，並`ColumnDefinition`具有單一屬性， `Width`。 高度和寬度的選項如下所示：

- **自動**&ndash;自動以符合內容中的資料列或資料行的大小。 指定為[ `GridUnitType.Auto` ](xref:Xamarin.Forms.GridUnitType) C# 中，或為`Auto`在 XAML 中。
- **Proportional(*)** &ndash;大小為剩餘的空間所佔百分比的資料行和資料列。 指定為值和`GridUnitType.Star`C# 中，以及`#*`XAML，在使用`#`所需的值。 指定一個資料列/資料行具有`*`會讓它填滿可用空間。
- **絕對**&ndash;大小資料行和資料列以特定的固定高度和寬度的值。 指定為值和`GridUnitType.Absolute`C# 中，以及`#`XAML，在使用`#`所需的值。

> [!NOTE]
> 資料行的寬度值會設定為 '*' 藉由在 Xamarin.Forms 中的預設值，這可確保資料行，將會填滿可用空間。

請考慮需要三個資料列和兩個資料行的應用程式。 下方的資料列必須是完全 200px 高，第一列必須是兩次中間的資料列一樣高。 左邊的資料行必須是寬度不足以容納內容，並正確的資料行必須以填滿剩餘的空間。

在 XAML 中：

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

在 C# 中：

```csharp
var grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
```

### <a name="placing-views-in-a-grid"></a>將檢視放置在方格中

若要將檢視在`Grid`您必須將它們做為子系加入方格中，則指定哪些資料列和資料行所屬中。

在 XAML 中，使用`Grid.Row`和`Grid.Column`上每個個別的檢視，以指定位置。 請注意，`Grid.Row`和`Grid.Column`指定基礎資料列和資料行的清單中以零為起始的位置。 這表示在 4x4 方格中，左上角儲存格為 (0，0) 和右下方儲存格是 (3，3)。

`Grid`顯示下面包含四個資料格：

![](grid-images/label-grid.png "具有四個檢視方格")

在 XAML 中：

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

在 C# 中：

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);
```

上述程式碼建立四個標籤、 兩個資料行，與兩個資料列的格線。 請注意，每個標籤會有相同的大小和擴充資料列時，會以使用所有可用的空間。

在上述範例中，檢視會新增至[ `Grid.Children` ](xref:Xamarin.Forms.Grid.Children)集合[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/)指定左邊和頂端的引數的多載。 使用時[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/)多載，指定左、 右、 上框線和下引數，而左邊和頂端的引數一律會參考儲存格內[ `Grid` ](xref:Xamarin.Forms.Grid)，右邊和下方的引數可能會參考儲存格以外的`Grid`。 這是因為右引數一律必須晚於左邊的引數，以及底部引數一律必須晚於最上層的引數。 下列範例示範使用這兩個對等的程式碼`Add`多載：

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>間距

`Grid` 具有屬性來控制資料列和資料行之間的間距。 下列屬性可供自訂`Grid`:

- **ColumnSpacing** &ndash;的資料行之間的空間量。 這個屬性的預設值為 6。
- **RowSpacing** &ndash;的資料列之間的空間量。 這個屬性的預設值為 6。

下列 XAML 指定`Grid`使用兩個資料行、 一個資料列和 5 的資料行之間的間距像素：

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinitions Width="*" />
    <ColumnDefinitions Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

在 C# 中：

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>跨越

通常使用一個方格，時不應佔據多個資料列或資料行的項目。 請考慮一個簡單的計算機應用程式：

![](grid-images/calculator.png "Calulator 應用程式")

請注意，[0] 按鈕跨越兩個資料行，就像每個平台內建的計算機上。 這利用完成`ColumnSpan`屬性，指定資料行數目的項目應佔據。 這個按鈕 XAML:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

在 C#:

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

請注意，在程式碼中，靜態方法`Grid`類別用來執行定位的變更，包括變更`ColumnSpan`和`RowSpan`。 也請注意，不像可以隨時設定其他屬性，設定使用的靜態方法的屬性必須已先在方格中有變更。

上述的計算機應用程式的完整 XAML 如下所示：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

請注意，在方格頂端標籤和 [零] 按鈕都 occuping 多個資料行。 雖然可以使用巢狀的方格，來達成類似的版面配置`ColumnSpan`  &  `RowSpan`方法是更簡單。

C# 實作：

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```


## <a name="related-links"></a>相關連結

- [使用 Xamarin.Forms 時，第 17 章建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [格線](xref:Xamarin.Forms.Grid)
- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
