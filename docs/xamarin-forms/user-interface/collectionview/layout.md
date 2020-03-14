---
title: Xamarin. Forms CollectionView 版面配置
description: 根據預設，CollectionView 會在垂直清單中顯示其專案。 不過，您可以指定垂直和水準清單和格線。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2019
ms.openlocfilehash: 6c2b3d8bad621db3110fe25041125c5694f21180
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304475"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin. Forms CollectionView 版面配置

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列控制配置的屬性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)，屬於[`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout)類型，可指定要使用的版面配置。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)，屬於[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)類型，會指定要使用的專案量值策略。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會在垂直清單中顯示其專案。 不過，您可以使用下列任何版面配置：

- 垂直清單-加入新專案時，垂直成長的單一資料行清單。
- 水準清單-加入新專案時水準成長的單一資料列清單。
- 垂直格線–在新增專案時，以垂直方式成長的多重資料行方格。
- 水準方格–在加入新專案時水準成長的多列方格。

將[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為衍生自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別的類別，即可指定這些配置。 這個類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)，屬於[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)類型，會指定[`CollectionView`](xref:Xamarin.Forms.CollectionView)在加入專案時展開的方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)，屬於[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)類型，可指定對齊點與專案對齊的方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)，屬於[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)類型，會在滾動時指定貼齊點的行為。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。 如需有關貼齊點的詳細資訊，請參閱[CollectionView 滾動](scrolling.md)指南中的[貼齊點](scrolling.md#snap-points)。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員：

- `Vertical` 表示在加入專案時， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會以垂直方式展開。
- `Horizontal` 表示[`CollectionView`](xref:Xamarin.Forms.CollectionView)會在加入專案時以水準方式展開。

`LinearItemsLayout` 類別會繼承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別，並定義類型 `double`的 `ItemSpacing` 屬性，代表每個專案周圍的空白空間。 這個屬性的預設值為0，且其值必須一律大於或等於0。 `LinearItemsLayout` 類別也會定義靜態 `Vertical` 和 `Horizontal` 成員。 這些成員可以分別用來建立垂直或水準清單。 或者，也可以建立 `LinearItemsLayout` 物件，指定[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員做為引數。

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)類別繼承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別，並定義下列屬性：

- `double`類型的 `VerticalItemSpacing`，代表每個專案周圍的垂直空白空間。 這個屬性的預設值為0，且其值必須一律大於或等於0。
- `double`類型的 `HorizontalItemSpacing`，代表每個專案周圍的水準空白空間。 這個屬性的預設值為0，且其值必須一律大於或等於0。
- `Span`，屬於 `int`類型，代表要在方格中顯示的資料行或資料列數目。 這個屬性的預設值為1，且其值必須一律大於或等於1。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)會使用原生版面配置引擎來執行版面配置。

## <a name="vertical-list"></a>垂直清單

根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會以垂直清單版面配置顯示其專案。 因此，不需要將[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為使用此配置：

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

不過，基於完整性， [`CollectionView`](xref:Xamarin.Forms.CollectionView)可以設定為在垂直清單中顯示其專案，方法是將其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為 `VerticalList`：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

或者，也可以藉由將[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為 `LinearItemsLayout` 物件，將 `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員指定為 `Orientation` 屬性值，來完成這項作業：

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

這會產生單一資料行清單，當加入新專案時，會以垂直方式成長：

[![在 iOS 和 Android 上 CollectionView 垂直清單版面配置的螢幕擷取畫面](layout-images/vertical-list.png "CollectionView 垂直清單版面配置")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直清單版面配置")

## <a name="horizontal-list"></a>水準清單

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以藉由將其 [ [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) ] 屬性設定為 [`HorizontalList`]，在水準清單中顯示其專案：

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

或者，也可以藉由將[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為 `LinearItemsLayout` 物件，將 `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員指定為 `Orientation` 屬性值，來完成此配置：

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

這會產生單一資料列清單，這會在新增新專案時水準成長：

[![在 iOS 和 Android 上 CollectionView 水準清單版面配置的螢幕擷取畫面](layout-images/horizontal-list.png "CollectionView 水準清單版面配置")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水準清單版面配置")

## <a name="vertical-grid"></a>垂直格線

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以藉由將其 [ [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) ] 屬性設定為[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)屬性設為 [`Vertical`] 的[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)物件，將其專案顯示在垂直方格中：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
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

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

根據預設，垂直[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)將會在單一資料行中顯示專案。 不過，此範例會將 `GridItemsLayout.Span` 屬性設定為2。 這會產生兩個數據行的方格，在加入新專案時，會以垂直方式成長：

[![CollectionView 垂直格線版面配置在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-grid.png "CollectionView 垂直格線版面配置")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直格線版面配置")

## <a name="horizontal-grid"></a>水準方格

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以在水準方格中顯示其專案，其方式是將其 [ [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) ] 屬性設定為[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)屬性設為 [`Horizontal`] 的[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)物件：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
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

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

根據預設，水準[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)會顯示單一資料列中的專案。 不過，此範例會將 `GridItemsLayout.Span` 屬性設定為4。 這會產生四個數據列的方格，這會在新增新專案時水準成長：

[![在 iOS 和 Android 上 CollectionView 水準方格版面配置的螢幕擷取畫面](layout-images/horizontal-grid.png "CollectionView 水準格線版面配置")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水準格線版面配置")

## <a name="headers-and-footers"></a>頁首和頁尾

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以呈現以清單中的專案來滾動的頁首和頁尾。 頁首和頁尾可以是字串、views 或[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列用來指定頁首和頁尾的屬性：

- `Header`，屬於 `object`類型，可指定要在清單開頭顯示的字串、系結或查看。
- `HeaderTemplate`，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型，會指定要用來格式化 `Header`的 `DataTemplate`。
- `Footer`，屬於 `object`類型的，會指定要顯示在清單結尾處的字串、系結或查看。
- `FooterTemplate`，屬於[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)類型，會指定要用來格式化 `Footer`的 `DataTemplate`。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

當標頭加入至水準成長的版面配置時（從左至右），標題會顯示在清單的左邊。 同樣地，當頁尾加入至水準成長的版面配置時（從左至右），頁尾會顯示在清單的右邊。

### <a name="display-strings-in-the-header-and-footer"></a>在頁首和頁尾中顯示字串

`Header` 和 `Footer` 屬性可以設定為 `string` 值，如下列範例所示：

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

此程式碼會產生下列螢幕擷取畫面，其中的標頭顯示在 iOS 螢幕擷取畫面中，而頁尾顯示于 Android 螢幕擷取畫面：

[![CollectionView 字串標頭和頁尾（在 iOS 和 Android 上）的螢幕擷取畫面](layout-images/header-footer-string.png "CollectionView 字串頁首和頁尾")](layout-images/header-footer-string-large.png#lightbox "CollectionView 字串頁首和頁尾")

### <a name="display-views-in-the-header-and-footer"></a>在頁首和頁尾中顯示 views

`Header` 和 `Footer` 屬性可以設定為一個視圖。 這可以是單一視圖，或包含多個子視圖的視圖。 下列範例顯示每個 `Header` 和 `Footer` 屬性，分別設定為包含[`Label`](xref:Xamarin.Forms.Label)物件的[`StackLayout`](xref:Xamarin.Forms.StackLayout)物件：

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

此程式碼會產生下列螢幕擷取畫面，其中的標頭顯示在 iOS 螢幕擷取畫面中，而頁尾顯示于 Android 螢幕擷取畫面：

[![在 iOS 和 Android 上使用 views CollectionView 頁首和頁尾的螢幕擷取畫面](layout-images/header-footer-view.png "CollectionView view 頁首和頁尾")](layout-images/header-footer-view-large.png#lightbox "CollectionView view 頁首和頁尾")

### <a name="display-a-templated-header-and-footer"></a>顯示樣板化頁首和頁尾

`HeaderTemplate` 和 `FooterTemplate` 屬性可以設定為用來格式化頁首和頁尾的[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。 在此案例中，`Header` 和 `Footer` 屬性必須系結至要套用之範本的目前來源，如下列範例所示：

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

此程式碼會產生下列螢幕擷取畫面，其中的標頭顯示在 iOS 螢幕擷取畫面中，而頁尾顯示于 Android 螢幕擷取畫面：

[![在 iOS 和 Android 上使用範本 CollectionView 頁首和頁尾的螢幕擷取畫面](layout-images/header-footer-template.png "CollectionView 範本頁首和頁尾")](layout-images/header-footer-template-large.png#lightbox "CollectionView 範本頁首和頁尾")

## <a name="item-spacing"></a>專案間距

根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView)中的每個專案在其周圍不會有任何空白空間。 藉由設定 `CollectionView`所使用之專案配置的屬性，即可變更此行為。

當[`CollectionView`](xref:Xamarin.Forms.CollectionView)將其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為 `LinearItemsLayout` 物件時，`LinearItemsLayout.ItemSpacing` 屬性可以設定為 `double` 值，表示每個專案周圍的空白空間：

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
> `LinearItemsLayout.ItemSpacing` 屬性具有驗證回呼集，可確保屬性的值一律大於或等於0。

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

此程式碼會產生垂直單一資料行清單，每個專案的間距為20：

[![IOS 和 Android 上專案間距的 CollectionView 螢幕擷取畫面](layout-images/vertical-list-spacing.png "CollectionView 專案間距")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 專案間距")

當[`CollectionView`](xref:Xamarin.Forms.CollectionView)將其[`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout)屬性設定為[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)物件時，可以將 [`GridItemsLayout.VerticalItemSpacing`] 和 [`GridItemsLayout.HorizontalItemSpacing`] 屬性設定為代表每個專案周圍垂直和水準之空白空間的 `double` 值：

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
> `GridItemsLayout.VerticalItemSpacing` 和 `GridItemsLayout.HorizontalItemSpacing` 屬性具有驗證回呼設定，可確保屬性的值一律大於或等於0。

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

這段程式碼會產生垂直的兩個數據行方格，其中每個專案的垂直間距為20，而每個專案的水準間距為30：

[![Android 上專案間距的 CollectionView 螢幕擷取畫面](layout-images/vertical-grid-spacing.png "CollectionView 專案間距")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 專案間距")

## <a name="item-sizing"></a>專案大小

根據預設， [`CollectionView`](xref:Xamarin.Forms.CollectionView)中的每個專案會個別測量並調整大小，但前提是[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的 UI 元素不會指定固定大小。 這個行為（可以變更）是由[`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)屬性值所指定。 這個屬性值可以設定為其中一個[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)列舉成員：

- `MeasureAllItems` –個別測量每個專案。 這是預設值。
- `MeasureFirstItem` –只測量第一個專案，並將所有後續專案的大小指定為與第一個專案相同。

> [!IMPORTANT]
> 當專案大小要在所有專案之間保持一致時，`MeasureFirstItem` 調整大小策略會導致效能增加。

下列程式碼範例會示範如何設定[`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy)屬性：

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

[`CollectionView`](xref:Xamarin.Forms.CollectionView)中的專案可以在執行時間以動態方式調整大小，方法是變更[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)內元素的版面配置相關屬性。 例如，下列程式碼範例會變更[`Image`](xref:Xamarin.Forms.Image)物件的[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)和[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)屬性：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

系統會執行 `OnImageTapped` 事件處理常式，以回應所按下的[`Image`](xref:Xamarin.Forms.Image)物件，並變更影像的維度，使其更容易被查看：

[![在 iOS 和 Android 上具有動態專案大小的 CollectionView 螢幕擷取畫面](layout-images/runtime-resizing.png "CollectionView 動態專案大小")](layout-images/runtime-resizing-large.png#lightbox "CollectionView 動態專案大小")

## <a name="right-to-left-layout"></a>由右至左的版面配置

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以藉由將其 [ [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) ] 屬性設定為 [ [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)]，以由右至左的流程方向來配置其內容。 不過，`FlowDirection` 屬性最好是在頁面或根配置上設定，使頁面或根配置中的所有專案都能回應流程方向：

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

具有父系之元素的預設[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)為[`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此， [`CollectionView`](xref:Xamarin.Forms.CollectionView)會從[`StackLayout`](xref:Xamarin.Forms.StackLayout)繼承 `FlowDirection` 屬性值，然後從[`ContentPage`](xref:Xamarin.Forms.ContentPage)繼承 `FlowDirection` 屬性值。 這會導致由右至左的版面配置，如下列螢幕擷取畫面所示：

[![在 iOS 和 Android 上 CollectionView 由右至左垂直清單版面配置的螢幕擷取畫面](layout-images/vertical-list-rtl.png "CollectionView 由右至左垂直清單版面配置")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 由右至左垂直清單版面配置")

如需流程方向的詳細資訊，請參閱由[右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin. 表單 CollectionView 滾動](scrolling.md)
