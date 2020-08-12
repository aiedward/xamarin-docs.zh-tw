---
title: 選擇 Xamarin.Forms 版面配置
description: Xamarin.Forms版面配置類別可讓您排列和分組應用程式中的 UI 控制項。
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a097d6dad067f209d3aad27bde382f28910324a
ms.sourcegitcommit: c3329ab25d377907d8804cdd5e26dc84a274f39c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130925"
---
# <a name="choose-a-no-locxamarinforms-layout"></a>選擇 Xamarin.Forms 版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms版面配置類別可讓您排列和分組應用程式中的 UI 控制項。 選擇版面配置類別需要知道版面配置如何放置其子專案，以及版面配置如何調整其子項目的大小。 此外，您可能需要嵌套配置，以建立您所需的版面配置。

下圖顯示可使用主要版面配置類別來達成的一般配置 Xamarin.Forms ：

[![：：： No-loc 中的主要版面配置類別 (Xamarin. Forms) ：：：](images/layouts.png "：：：無-loc (Xamarin. Forms) ：：： layout 類別")](images/layouts-large.png#lightbox "：：：無-loc (Xamarin. Forms) ：：： layout 類別")

## <a name="stacklayout"></a>StackLayout

會 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 以水準或垂直方式組織一維堆疊中的元素。 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)屬性會指定元素的方向，而預設的方向為 [`Vertical`](xref:Xamarin.Forms.StackOrientation) 。 `StackLayout`通常用來排列頁面上 UI 的子區段。

下列 XAML 顯示如何建立 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含三個物件的垂直 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

在中 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如果未明確設定專案的大小，它會展開以填滿可用的寬度，如果 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) 屬性設定為，則為 height [`Horizontal`](xref:Xamarin.Forms.StackOrientation) 。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)通常用來做為父配置，其中包含其他子版面配置。 不過， `StackLayout` 不應使用物件的組合來重現 [`Grid`](xref:Xamarin.Forms.Grid) 版面配置 `StackLayout` 。 下列程式碼顯示此錯誤做法的範例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Name:" />
            <Entry Placeholder="Enter your name" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Age:" />
            <Entry Placeholder="Enter your age" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Occupation:" />
            <Entry Placeholder="Enter your occupation" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
            <Label Text="Address:" />
            <Entry Placeholder="Enter your address" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

這很浪費資源，因為會執行不必要的版面配置計算。 相反地，所需的版面配置可以使用來達成更好的 [`Grid`](xref:Xamarin.Forms.Grid) 。

> [!TIP]
> 使用時 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，請確定只有一個子專案設定為 [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 。 此屬性可確保指定的子系會佔用 `StackLayout` 所能提供的最大空間，重複執行這些計算將會浪費資源。

如需詳細資訊，請參閱[ Xamarin.Forms StackLayout](stacklayout.md)。

## <a name="grid"></a>方格

[`Grid`](xref:Xamarin.Forms.Grid)是用來在資料列和資料行中顯示專案，這些專案可以有比例或絕對大小。 方格的資料列和資料行會使用 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和屬性來指定 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 。

若要將專案放在特定的資料 [`Grid`](xref:Xamarin.Forms.Grid) 格中，請使用 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 附加屬性。 若要讓元素橫跨多個資料列和資料行，請使用 [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 和 [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加屬性。

> [!NOTE]
> [`Grid`](xref:Xamarin.Forms.Grid)版面配置不應該與資料表混淆，而且不能呈現表格式資料。 不同于 HTML 資料表， `Grid` 是用於配置內容。 若要顯示表格式資料，請考慮使用[ListView](~/xamarin-forms/user-interface/listview/index.md)、 [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)或[TableView](~/xamarin-forms/user-interface/tableview.md)。

下列 XAML 顯示如何建立 [`Grid`](xref:Xamarin.Forms.Grid) 具有兩個數據列和兩個數據行的：

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="50" />
        <RowDefinition Height="50" />
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto" />
        <ColumnDefinition />
    </Grid.ColumnDefinitions>    
    <Label Text="Column 0, Row 0"
           WidthRequest="200" />
    <Label Grid.Column="1"
           Text="Column 1, Row 0" />
    <Label Grid.Row="1"
           Text="Column 0, Row 1" />
    <Label Grid.Column="1"
           Grid.Row="1"
           Text="Column 1, Row 1" />
</Grid>
```

在此範例中，大小調整的運作方式如下：

- 每個資料列都有一個明確的高度，分別為50裝置獨立單位。
- 第一個資料行的寬度設定為 [`Auto`](xref:Xamarin.Forms.GridLength.Auto) ，因此，它的子系必須是寬。 在此情況下，它是200裝置獨立單位，以容納第一個的寬度 [`Label`](xref:Xamarin.Forms.Label) 。

您可以使用自動調整大小來散發資料行或資料列中的空間，讓資料行和資料列大小符合其內容。 將的高度或的寬度設定為，即可達成此目的 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 。 按比例調整大小也可以用來在方格的資料列和資料行之間以加權比例散發可用空間。 將的高度 `RowDefinition` （或的寬度 `ColumnDefinition` ）設定為使用運算子的值，即可達成此目的 `*` 。

> [!CAUTION]
> 請嘗試確保最少的資料列和資料行設定為 [ [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小]。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 或者，使用列舉值來設定資料列和資料行，以佔用比例的空間量 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) 。

如需詳細資訊，請參閱[ Xamarin.Forms Grid](grid.md)。

## <a name="flexlayout"></a>FlexLayout

與 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) 相似之處在于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，它會在堆疊中以水準或垂直方式顯示子項目。 不過， `FlexLayout` 如果有太多要放入單一資料列或資料行中的專案，也可以包裝其子系，同時也能夠更精確地控制其子專案的大小、方向和對齊方式。

下列 XAML 示範如何建立 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) ，以在單一資料行中顯示其 views：

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">
    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

在此範例中，版面配置的運作方式如下：

- [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction)屬性會設定為 `Column` ，這會使的子系 `FlexLayout` 在專案的單一資料行中排列。
- [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性會設定為 `Center` ，這會使每個專案水準置中。
- [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性會設定為 `SpaceEvenly` ，這會在所有專案之間，以及在第一個專案和最後一個專案的上方，平均配置所有剩餘的垂直空間。

如需詳細資訊，請參閱[ Xamarin.Forms FlexLayout](flex-layout.md)。

## <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)是用來定位和調整專案相對於版面配置或同級元素的屬性。 根據預設，專案位於版面配置的左上角。 `RelativeLayout`可以用來建立跨裝置大小按比例調整的 ui。

在中 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ，位置和大小會指定為條件約束。 條件約束具有 [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) 和 [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) 屬性，可用來將位置和大小定義為倍數 (或其他物件屬性的分數) ，再加上常數。 此外，常數可以是負數。

> [!NOTE]
> 支援將專案 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) 置於其本身的界限之外。

下列 XAML 顯示如何排列中的專案 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ：

```xaml
<RelativeLayout>
    <BoxView Color="Blue"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView Color="Red"
             HeightRequest="50"
             WidthRequest="50"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.85}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0}" />
    <BoxView x:Name="pole"
             Color="Gray"
             WidthRequest="15"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.45}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.25}" />
    <BoxView Color="Green"
             RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
             RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=.2, Constant=20}"
             RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
             RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

在此範例中，版面配置的運作方式如下：

- 藍色 [`BoxView`](xref:Xamarin.Forms.BoxView) 會提供明確的50x50 裝置獨立單位大小。 它會放在版面配置的左上角，也就是預設位置。
- 紅色 [`BoxView`](xref:Xamarin.Forms.BoxView) 會提供明確的50x50 裝置獨立單位大小。 它會放在版面配置的右上角。
- 灰色的 [`BoxView`](xref:Xamarin.Forms.BoxView) 寬度為15個與裝置無關的單位，而其高度設為其父系高度的75%。
- 綠色 [`BoxView`](xref:Xamarin.Forms.BoxView) 不會提供明確大小。 其位置會設定為相對於 `BoxView` 名為的 `pole` 。

> [!WARNING]
> 盡量避免使用 `RelativeLayout`。 它會導致 CPU 必須執行更多工作。

如需詳細資訊，請參閱[ Xamarin.Forms RelativeLayout](relative-layout.md)。

## <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)是用來使用明確值來定位和調整專案的位置，或相對於版面配置大小的值。 位置是由相對於左上角之子系的左上角所指定 `AbsoluteLayout` 。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)只有當您可以在子系上施加大小，或當元素的大小不會影響其他子系的位置時，才應該將視為特殊用途的版面配置。 此配置的標準用途是建立重迭，其中涵蓋含有其他控制項的頁面，可能會保護使用者與頁面上的一般控制項的互動。

> [!IMPORTANT]
> `HorizontalOptions`和 `VerticalOptions` 屬性不會影響的子系 `AbsoluteLayout` 。

在中 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) ， [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 附加屬性會用來指定元素的水準位置、垂直位置、寬度和高度。 此外， [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 附加屬性會指定如何解讀版面配置界限。

下列 XAML 顯示如何排列中的專案 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) ：

```xaml
<AbsoluteLayout Margin="40">
    <BoxView Color="Red"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="30" />
    <BoxView Color="Green"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
             Rotation="60" />
    <BoxView Color="Blue"
             AbsoluteLayout.LayoutFlags="PositionProportional"
             AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" />
</AbsoluteLayout>
```

在此範例中，版面配置的運作方式如下：

- 每個 [`BoxView`](xref:Xamarin.Forms.BoxView) 都會提供明確的100x100 大小，並以水準方式顯示在相同的位置。
- 紅色 [`BoxView`](xref:Xamarin.Forms.BoxView) 會旋轉30度，綠色 `BoxView` 會旋轉60度。
- 在每個上 [`BoxView`](xref:Xamarin.Forms.BoxView) ， [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 附加屬性會設定為 `PositionProportional` ，表示位置會與寬度和高度所占的剩餘空間成正比。

> [!CAUTION]
> 盡可能避免使用 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 屬性，因為這會導致版面配置引擎執行其他版面配置計算。

如需詳細資訊，請參閱[ Xamarin.Forms AbsoluteLayout](absolutelayout.md)。

## <a name="input-transparency"></a>輸入透明度

每個視覺元素都有一個 [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) 屬性，可用來定義專案是否接收輸入。 其預設值為 `false` ，確保元素會收到輸入。

在版面配置類別上設定這個屬性時，其值會傳送至子項目。 因此，在 [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) 版面配置類別上，將屬性設定為， `true` 會導致配置中的所有元素都不會收到輸入。

## <a name="layout-performance"></a>版面配置效能

若要取得最佳的版面配置效能，請遵循[優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的指導方針。

此外，您也可以使用版面配置壓縮來改善頁面轉譯效能，這會從視覺化樹狀結構中移除指定的版面配置。 如需詳細資訊，請參閱[版面配置壓縮](layout-compression.md)。

## <a name="related-links"></a>相關連結

- [版面配置 (範例) ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Xamarin.Forms (影片) 的版面配置](https://youtu.be/4HlLjTZQzjM)
- [Xamarin.FormsStackLayout](stacklayout.md)
- [Xamarin.Forms格](grid.md)
- [Xamarin.FormsFlexLayout](flex-layout.md)
- [Xamarin.FormsAbsoluteLayout](absolutelayout.md)
- [Xamarin.FormsRelativeLayout](relative-layout.md)
- [優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [版面配置壓縮](layout-compression.md)
