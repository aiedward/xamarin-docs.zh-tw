---
title: Xamarin.Forms CollectionView 版面配置
description: 根據預設，CollectionView 會在垂直清單中顯示其專案。 不過，您可以指定垂直和水準清單以及格線。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dcff583f929e0ce8cda9fe81dc88452c6056ddf1
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371478"
---
# <a name="no-locxamarinforms-collectionview-layout"></a>Xamarin.Forms CollectionView 版面配置

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義下列控制項配置的屬性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)型別為的，可 [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout) 指定要使用的版面配置。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)型別為的 [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) ，可指定要使用的專案量值策略。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

依預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會在垂直清單中顯示其專案。 不過，您可以使用下列任何一種版面配置：

- 垂直清單–加入新的專案時，垂直成長的單一資料行清單。
- 水準清單–加入新專案時水準成長的單一資料列清單。
- 垂直方格–加入新的專案時，垂直成長的多欄格線。
- 水準方格–加入新專案時水準成長的多重資料列格線。

您可以藉由將 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 屬性設定為衍生自類別的類別來指定這些版面配置 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 。 此類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)，類型為 [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) ，指定要在其中 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 加入專案的展開方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)型別為的 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) ，可指定對齊點與專案的對齊方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)型別為的 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) ，指定滾動時對齊點的行為。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。 如需貼齊點的詳細資訊，請參閱[ Xamarin.Forms CollectionView 滾動](scrolling.md)指南中的[貼齊點](scrolling.md#snap-points)。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員：

- `Vertical` 指出 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 當加入專案時，將會垂直展開。
- `Horizontal` 指出 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 將會在新增專案時水準展開。

`LinearItemsLayout`類別繼承自 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 類別，並定義型別為 `ItemSpacing` 的屬性， `double` 代表每個專案周圍的空白空間。 這個屬性的預設值為0，而且其值必須一律大於或等於0。 `LinearItemsLayout`類別也會定義靜態 `Vertical` 和 `Horizontal` 成員。 這些成員可以分別用來建立垂直或水準清單。 或者，您也 `LinearItemsLayout` 可以建立物件，並將 [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為引數。

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)類別繼承自 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 類別，並定義下列屬性：

- `VerticalItemSpacing`型別為的， `double` 代表每個專案周圍的垂直空白空間。 這個屬性的預設值為0，而且其值必須一律大於或等於0。
- `HorizontalItemSpacing`型別為的， `double` 代表每個專案周圍的水準空白空間。 這個屬性的預設值為0，而且其值必須一律大於或等於0。
- `Span`型別為的， `int` 表示要在方格中顯示的資料行或資料列數目。 這個屬性的預設值為1，而且其值必須一律大於或等於1。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 使用原生版面配置引擎來執行版面配置。

## <a name="vertical-list"></a>垂直清單

依預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 會在垂直清單版面配置中顯示其專案。 因此，您不需要將 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 此屬性設定為使用此版面配置：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

不過，在 XAML 中，您可以設定 XAML a，將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 其屬性設定為，以將其專案顯示在垂直清單中 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `VerticalList` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

或者，您也可以將 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 屬性設定為物件，並將 `LinearItemsLayout` `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為 `Orientation` 屬性值，以達成此目的：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

這會產生單一資料行清單，其會在新增新專案時垂直成長：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 垂直清單版面配置](layout-images/vertical-list.png "CollectionView 垂直清單版面配置")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直清單版面配置")

## <a name="horizontal-list"></a>水準清單

在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以藉由將專案的屬性設定為，在水準清單中顯示其專案 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `HorizontalList` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

或者，您也可以將 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件，並將 `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為 `Orientation` 屬性值，藉此完成此配置：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

這會產生單一資料列清單，當加入新專案時，就會水準成長：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 水準清單版面配置](layout-images/horizontal-list.png "CollectionView 水準清單版面配置")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水準清單版面配置")

## <a name="vertical-grid"></a>垂直格線

在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以將專案的屬性設定為，以將其專案顯示在垂直方格中 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `VerticalGrid` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalGrid, 2">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

或者，您也可以將屬性設定為 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 屬性設定為的物件，藉以完成這個配置 [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) `Vertical` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

依預設，垂直 [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 會在單一資料行中顯示專案。 不過，此範例會將 `GridItemsLayout.Span` 屬性設定為2。 這會產生兩個數據行的方格，在新增專案時，會垂直增加：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 垂直格線版面配置](layout-images/vertical-grid.png "CollectionView 垂直格線版面配置")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直格線版面配置")

## <a name="horizontal-grid"></a>水平格線

在 XAML 中， [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以將專案的屬性設定為，以水準方格顯示其專案 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) `HorizontalGrid` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalGrid, 4">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

或者，您也可以將屬性設定為 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 屬性設定為的物件，藉以完成這個配置 [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) `Horizontal` ：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

依預設，水準 [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 會在單一資料列中顯示專案。 不過，此範例會將 `GridItemsLayout.Span` 屬性設定為4。 這會產生四個數據列的格線，這會在新增新專案時水準成長：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 水準格線版面配置](layout-images/horizontal-grid.png "CollectionView 水準格線版面配置")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水準格線版面配置")

## <a name="headers-and-footers"></a>頁首和頁尾

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以呈現以清單中的專案來滾動的頁首和頁尾。 頁首和頁尾可以是字串、views 或 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 物件。

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 定義下列屬性來指定頁首和頁尾：

- `Header`，類型為 `object` ，指定將在清單開頭顯示的字串、系結或視圖。
- `HeaderTemplate`型別為的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ， `DataTemplate` 可指定要用來格式化的 `Header` 。
- `Footer`，類型為 `object` ，指定將顯示在清單結尾的字串、系結或視圖。
- `FooterTemplate`型別為的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ， `DataTemplate` 可指定要用來格式化的 `Footer` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

將標頭新增至水準成長的版面配置時（從左至右），標頭會顯示在清單左邊。 同樣地，當頁尾加入至水準成長的版面配置時（從左至右），頁尾會顯示在清單右邊。

### <a name="display-strings-in-the-header-and-footer"></a>在頁首和頁尾顯示字串

`Header`和 `Footer` 屬性可以設定為 `string` 值，如下列範例所示：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

這段程式碼會導致下列螢幕擷取畫面，其中的標頭顯示于 iOS 螢幕擷取畫面中，而頁尾顯示于 Android 螢幕擷取畫面中：

[![螢幕擷取畫面： iOS 和 Android 上的 CollectionView 字串頁首和頁尾](layout-images/header-footer-string.png "CollectionView 字串頁首和頁尾")](layout-images/header-footer-string-large.png#lightbox "CollectionView 字串頁首和頁尾")

### <a name="display-views-in-the-header-and-footer"></a>在頁首和頁尾顯示視圖

`Header`和 `Footer` 屬性都可以設定為 view。 這可以是單一視圖，也可以是包含多個子視圖的視圖。 下列範例會顯示 `Header` `Footer` 每個和屬性，並將其設定為 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`Label`](xref:Xamarin.Forms.Label) 物件的物件：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

這段程式碼會導致下列螢幕擷取畫面，其中的標頭顯示于 iOS 螢幕擷取畫面中，而頁尾顯示于 Android 螢幕擷取畫面中：

[![螢幕擷取畫面：在 iOS 和 Android 上使用 views 的 CollectionView 頁首和頁尾](layout-images/header-footer-view.png "CollectionView view 頁首和頁尾")](layout-images/header-footer-view-large.png#lightbox "CollectionView view 頁首和頁尾")

### <a name="display-a-templated-header-and-footer"></a>顯示樣板化頁首和頁尾

`HeaderTemplate`和 `FooterTemplate` 屬性可以設定為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 用來格式化頁首和頁尾的物件。 在此案例中， `Header` 和 `Footer` 屬性必須系結至要套用之範本的目前來源，如下列範例所示：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

這段程式碼會導致下列螢幕擷取畫面，其中的標頭顯示于 iOS 螢幕擷取畫面中，而頁尾顯示于 Android 螢幕擷取畫面中：

[![螢幕擷取畫面：在 iOS 和 Android 上使用範本的 CollectionView 頁首和頁尾](layout-images/header-footer-template.png "CollectionView 範本頁首和頁尾")](layout-images/header-footer-template-large.png#lightbox "CollectionView 範本頁首和頁尾")

## <a name="item-spacing"></a>專案間距

依預設，中的每個專案之間都沒有空格 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 您可以藉由設定所使用之專案配置的屬性來變更這個行為 `CollectionView` 。

當將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 其 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件時，可以將 `LinearItemsLayout.ItemSpacing` 屬性設定為 `double` 代表專案之間空間的值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `LinearItemsLayout.ItemSpacing`屬性具有驗證回撥設定，可確保屬性的值一律大於或等於0。

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

這段程式碼會產生垂直單一資料行清單，專案之間的間距為20：

[![螢幕擷取畫面： iOS 和 Android 上具有專案間距的 CollectionView](layout-images/vertical-list-spacing.png "CollectionView 專案間距")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 專案間距")

當將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 其 [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) 屬性設定為 [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) 物件時， `GridItemsLayout.VerticalItemSpacing` 和 `GridItemsLayout.HorizontalItemSpacing` 屬性可以設定為 `double` 代表專案之間垂直和水準空白空間的值：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `GridItemsLayout.VerticalItemSpacing`和 `GridItemsLayout.HorizontalItemSpacing` 屬性會設定驗證回呼，以確保屬性的值一律大於或等於0。

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

這段程式碼會產生垂直兩個數據行的方格，其中專案之間的垂直間距為20，而專案之間的水準間距為30：

[![Android 上專案間距 CollectionView 的螢幕擷取畫面](layout-images/vertical-grid-spacing.png "CollectionView 專案間距")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 專案間距")

## <a name="item-sizing"></a>專案大小調整

依預設，中的每個專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 都會個別測量和調整大小，前提是中的 UI 元素 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 不會指定固定的大小。 此行為可以變更，由 [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) 屬性值指定。 這個屬性值可以設定為其中一個 [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) 列舉成員：

- `MeasureAllItems` –個別測量每個專案。 這是預設值。
- `MeasureFirstItem` –只會測量第一個專案，而所有後續專案的大小會與第一個專案相同。

> [!IMPORTANT]
> `MeasureFirstItem`在專案大小要在所有專案之間保持一致的情況下，調整大小策略會導致效能提高。

下列程式碼範例會示範如何設定 [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) 屬性：

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>動態調整專案大小

中的專案 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以在執行時間動態調整大小，方法是變更中專案的版面配置相關屬性 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 例如，下列程式碼範例會變更 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 物件的和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性 [`Image`](xref:Xamarin.Forms.Image) ：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

`OnImageTapped`系統會執行事件處理常式來回應 [`Image`](xref:Xamarin.Forms.Image) 正在進行點擊的物件，並變更影像的維度，以便更輕鬆地加以查看：

[![螢幕擷取畫面： iOS 和 Android 上的動態專案大小調整 CollectionView](layout-images/runtime-resizing.png "CollectionView 動態專案大小調整")](layout-images/runtime-resizing-large.png#lightbox "CollectionView 動態專案大小調整")

## <a name="right-to-left-layout"></a>由右至左的版面配置

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以藉由將其屬性設定為，以由右至左的流程方向，將其內容版面配置 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 。 不過，在理想的情況下，您 `FlowDirection` 應該在頁面或根配置上設定屬性，使頁面中的所有專案或根配置都能回應流程方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

父元素的預設值為 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) 。 因此，會 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 繼承的 `FlowDirection` 屬性值 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，而後者又會繼承的 `FlowDirection` 屬性值 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。 這會導致下列螢幕擷取畫面中顯示由右至左的版面配置：

[![螢幕擷取畫面：在 iOS 和 Android 上，CollectionView 的由右至左垂直清單版面配置](layout-images/vertical-list-rtl.png "CollectionView 由右至左的垂直清單版面配置")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 由右至左的垂直清單版面配置")

如需流程方向的詳細資訊，請參閱由 [右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CollectionView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [從右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.Forms CollectionView 滾動](scrolling.md)