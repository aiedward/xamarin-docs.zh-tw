---
title: 選擇 Xamarin.Forms 版面配置
description: Xamarin.Forms 版面配置類別可讓您在應用程式中排列和分組 UI 控制項。
ms.prod: xamarin
ms.assetid: 05A39752-A174-447E-A30D-3CC9EF98CB96
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/21/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6f5702695698881a30cfbf3e63110856a97a2c24
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555343"
---
# <a name="choose-a-no-locxamarinforms-layout"></a>選擇 Xamarin.Forms 版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

Xamarin.Forms 版面配置類別可讓您在應用程式中排列和分組 UI 控制項。 選擇版面配置類別需要知道配置如何定位其子項目，以及版面配置如何調整其子項目的大小。 此外，您可能需要將配置嵌入以建立您想要的版面配置。

下圖顯示可透過主要版面配置類別達成的一般版面配置 Xamarin.Forms ：

[![：：： No loc (Xamarin 中的主要版面配置類別) ：：：](images/layouts.png "：：：非 loc (Xamarin. Forms) ：：： layout 類別")](images/layouts-large.png#lightbox "：：：非 loc (Xamarin. Forms) ：：： layout 類別")

## <a name="stacklayout"></a>StackLayout

會以 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 水準或垂直方式組織一維堆疊中的元素。 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)屬性會指定元素的方向，而預設方向為 [`Vertical`](xref:Xamarin.Forms.StackOrientation) 。 `StackLayout` 通常用來排列頁面上 UI 的子區段。

下列 XAML 顯示如何建立 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含三個物件的垂直 [`Label`](xref:Xamarin.Forms.Label) ：

```xaml
<StackLayout Margin="20,35,20,25">
    <Label Text="The StackLayout has its Margin property set, to control the rendering position of the StackLayout." />
    <Label Text="The Padding property can be set to specify the distance between the StackLayout and its children." />
    <Label Text="The Spacing property can be set to specify the distance between views in the StackLayout." />
</StackLayout>
```

在中 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，如果未明確設定專案的大小，它會展開以填滿可用的寬度，或在屬性設為時填滿高度 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) [`Horizontal`](xref:Xamarin.Forms.StackOrientation) 。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)通常用來做為父配置，其中包含其他子版面配置。 但是， `StackLayout` 不應該使用物件的組合來重現 [`Grid`](xref:Xamarin.Forms.Grid) 版面配置 `StackLayout` 。 下列程式碼顯示此不正確做法的範例：

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

這很浪費資源，因為會執行不必要的版面配置計算。 相反地，您可以使用來更妥善地達成所需的版面配置 [`Grid`](xref:Xamarin.Forms.Grid) 。

> [!TIP]
> 使用時 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，請確定只有一個子項目設定為 [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 。 此屬性可確保指定的子系會佔用 `StackLayout` 所能提供的最大空間，重複執行這些計算將會浪費資源。

如需詳細資訊，請參閱[ Xamarin.Forms StackLayout](stacklayout.md)。

## <a name="grid"></a>Grid

[`Grid`](xref:Xamarin.Forms.Grid)用來顯示資料列和資料行中的元素，這些專案可以有比例或絕對大小。 方格的資料列和資料行是以 [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) 和 [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) 屬性指定。

若要定位特定資料 [`Grid`](xref:Xamarin.Forms.Grid) 格中的元素，請使用 [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) 和 [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) 附加屬性。 若要讓元素跨越多個資料列和資料行，請使用 [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) 和 [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) 附加屬性。

> [!NOTE]
> [`Grid`](xref:Xamarin.Forms.Grid)版面配置不應與資料表混淆，也不能呈現表格式資料。 與 HTML 資料表不同的 `Grid` 是，是為了配置內容而設計。 若要顯示表格式資料，請考慮使用 [ListView](~/xamarin-forms/user-interface/listview/index.md)、 [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)或 [TableView](~/xamarin-forms/user-interface/tableview.md)。

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

在此範例中，調整大小的運作方式如下：

- 每個資料列都有50裝置獨立單位的明確高度。
- 第一個資料行的寬度設定為，因此為其子系 [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 所需的寬度。 在此情況下，它是200與裝置無關的單位寬度，以容納第一個的寬度 [`Label`](xref:Xamarin.Forms.Label) 。

您可以使用自動調整大小，在資料行或資料列內散佈空間，讓資料行和資料列大小符合其內容。 這是藉由將的高度或的 [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) 寬度設定 [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) 為來達成 [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 。 比例調整大小也可以用來根據加權比例，在方格的資料列和資料行之間分散可用空間。 將的高度 `RowDefinition` （或的寬度 `ColumnDefinition` ）設定為使用運算子的值，即可達到這個目的 `*` 。

> [!CAUTION]
> 請嘗試確定最少的資料列和資料行都設定為 [ [`Auto`](xref:Xamarin.Forms.GridLength.Auto) 大小]。 每個自動調整大小的資料列或資料行都會導致版面配置引擎執行額外的版面配置計算。 可能的話，請改用固定大小的資料列和資料行。 另外，也可以設定資料列和資料行，以佔用具有列舉值的比例空間量 [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) 。

如需詳細資訊，請參閱[ Xamarin.Forms Grid](grid.md)。

## <a name="flexlayout"></a>FlexLayout

[`FlexLayout`](xref:Xamarin.Forms.FlexLayout)類似于 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，它會在堆疊中以水準或垂直方式顯示子項目。 但是， `FlexLayout` 如果在單一資料列或資料行中無法容納太多專案，也可以包裝其子系，也可以更細微地控制其子項目的大小、方向和對齊方式。

下列 XAML 顯示如何建立 [`FlexLayout`](xref:Xamarin.Forms.FlexLayout) ，以在單一資料行中顯示其觀點：

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

- [`Direction`](xref:Xamarin.Forms.FlexLayout.Direction)屬性設定為 `Column` ，會將的子系 `FlexLayout` 排列在專案的單一資料行中。
- [`AlignItems`](xref:Xamarin.Forms.FlexLayout.AlignItems)屬性設定為 `Center` ，會使每個專案以水準方式置中。
- [`JustifyContent`](xref:Xamarin.Forms.FlexLayout.JustifyContent)屬性設定為 `SpaceEvenly` ，它會配置所有專案之間的所有剩餘垂直空間，以及在第一個專案和最後一個專案的正上方。

如需詳細資訊，請參閱[ Xamarin.Forms FlexLayout](flex-layout.md)。

## <a name="relativelayout"></a>RelativeLayout

[`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)是用來定位和調整相對於版面配置或同級元素屬性的專案。 依預設，專案位於版面配置的左上角。 `RelativeLayout`可以用來建立可依比例調整跨裝置大小的 ui。

在中 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ，位置和大小會指定為條件約束。 條件約束具有 [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor) 和 [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant) 屬性，可以用來將位置和大小定義為倍數 (或分數) 其他物件的屬性，加上常數。 此外，常數可以是負數。

> [!NOTE]
> [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)支援將元素放置在它自己的界限之外。

下列 XAML 顯示如何在中排列元素 [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ：

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

- 藍色 [`BoxView`](xref:Xamarin.Forms.BoxView) 會獲得50x50 裝置獨立單位的明確大小。 它會放在版面配置的左上角，也就是預設位置。
- 紅色 [`BoxView`](xref:Xamarin.Forms.BoxView) 會獲得50x50 裝置獨立單位的明確大小。 它會放在版面配置的右上角。
- 灰色的 [`BoxView`](xref:Xamarin.Forms.BoxView) 寬度為15個裝置獨立單位的明確寬度，而其高度設定為其父系高度的75%。
- 綠色 [`BoxView`](xref:Xamarin.Forms.BoxView) 未提供明確的大小。 其位置會設定為相對於 `BoxView` 指定的 `pole` 。

> [!WARNING]
> 盡量避免使用 `RelativeLayout`。 它會導致 CPU 必須執行更多工作。

如需詳細資訊，請參閱[ Xamarin.Forms RelativeLayout](relativelayout.md)。

## <a name="absolutelayout"></a>AbsoluteLayout

使用 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 明確的值，或相對於配置大小的值，使用來定位和調整元素的大小。 位置是由相對於左上角之子系的左上角所指定 `AbsoluteLayout` 。

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)應該視為特殊用途的版面配置，只有當您可以在子系上強加大小，或當元素的大小不會影響其他子系的位置時才使用。 此配置的標準用途是建立重迭，其中涵蓋具有其他控制項的頁面，或許是為了保護使用者與頁面上的一般控制項互動。

> [!IMPORTANT]
> `HorizontalOptions`和 `VerticalOptions` 屬性不會影響的子系 `AbsoluteLayout` 。

在中 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) ， [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) 附加屬性是用來指定元素的水準位置、垂直位置、寬度和高度。 此外，附加的 [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 屬性會指定如何轉譯版面配置界限。

下列 XAML 顯示如何排列中的元素 [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) ：

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

- 每個 [`BoxView`](xref:Xamarin.Forms.BoxView) 都有明確的100x100 大小，並且顯示在水準置中的相同位置。
- 紅色 [`BoxView`](xref:Xamarin.Forms.BoxView) 會旋轉30度，而綠色 `BoxView` 會旋轉60度。
- 在每個上 [`BoxView`](xref:Xamarin.Forms.BoxView) ， [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) 附加屬性都設為 `PositionProportional` ，表示在計算寬度和高度之後，位置會與剩餘的空間成正比。

> [!CAUTION]
> 請盡可能避免使用 [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) 屬性，因為這會導致版面配置引擎執行其他的版面配置計算。

如需詳細資訊，請參閱[ Xamarin.Forms AbsoluteLayout](absolutelayout.md)。

## <a name="input-transparency"></a>輸入透明度

每個視覺元素都有一個 [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) 屬性，可用來定義專案是否接收輸入。 其預設值為 `false` ，確保元素會收到輸入。

在版面配置類別上設定此屬性時，其值會傳送至子項目。 因此，在 [`InputTransparent`](xref:Xamarin.Forms.VisualElement.InputTransparent) 版面配置類別上將屬性設為， `true` 會導致版面配置內的所有專案都不會收到輸入。

## <a name="layout-performance"></a>版面配置效能

若要取得最佳的版面配置效能，請遵循將 [版面配置效能優化](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)的指導方針。

此外，也可以使用版面配置壓縮來改善頁面轉譯效能，這會從視覺化樹狀結構中移除指定的版面配置。 如需詳細資訊，請參閱 [版面配置壓縮](layout-compression.md)。

## <a name="related-links"></a>相關連結

- [版面配置 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Xamarin.Forms (影片) 的版面配置 ](https://youtu.be/4HlLjTZQzjM)
- [Xamarin.Forms StackLayout](stacklayout.md)
- [Xamarin.Forms 網 格](grid.md)
- [Xamarin.Forms FlexLayout](flex-layout.md)
- [Xamarin.Forms AbsoluteLayout](absolutelayout.md)
- [Xamarin.Forms RelativeLayout](relativelayout.md)
- [優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)
- [版面配置壓縮](layout-compression.md)