---
title: Xamarin. Forms CollectionView 版面配置
description: 根據預設, CollectionView 會在垂直清單中顯示其專案。 不過, 您可以指定垂直和水準清單和格線。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2019
ms.openlocfilehash: ac32e340212dd42c373a39df138436e7ee313958
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976577"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin. Forms CollectionView 版面配置

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列控制配置的屬性:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), 屬於類型[`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout), 可指定要使用的版面配置。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), 屬於類型[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), 可指定要使用的專案量值策略。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。

根據預設, [`CollectionView`](xref:Xamarin.Forms.CollectionView)會在垂直清單中顯示其專案。 不過, 您可以使用下列任何版面配置:

- 垂直清單-加入新專案時, 垂直成長的單一資料行清單。
- 水準清單-加入新專案時水準成長的單一資料列清單。
- 垂直格線–在新增專案時, 以垂直方式成長的多重資料行方格。
- 水準方格–在加入新專案時水準成長的多列方格。

將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為衍生[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)自類別的類別, 即可指定這些配置。 這個類別會定義下列屬性:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), 屬於類型[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), [`CollectionView`](xref:Xamarin.Forms.CollectionView)可指定要在其中加入做為專案展開的方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), 屬於類型[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), 指定對齊點與專案對齊的方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), 屬於類型[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), 會在滾動時指定貼齊點的行為。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。 如需有關貼齊點的詳細資訊, 請參閱[CollectionView 滾動](scrolling.md)指南中的[貼齊點](scrolling.md#snap-points)。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員:

- `Vertical`表示將會[`CollectionView`](xref:Xamarin.Forms.CollectionView)在加入專案時垂直展開。
- `Horizontal`表示將會[`CollectionView`](xref:Xamarin.Forms.CollectionView)在加入專案時, 以水準方式展開。

類別繼承[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)自類別,並定義`double`類型為的屬性,代表每個專案周圍的空白空間。`ItemSpacing` [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) 這個屬性的預設值為 0, 且其值必須一律大於或等於0。 類別也會定義靜態`Vertical`和`Horizontal`成員。 `ListItemsLayout` 這些成員可以分別用來建立垂直或水準清單。 或者, `ListItemsLayout`也可以建立物件, 並[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)指定列舉成員做為引數。

[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)類別[繼承`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)自類別, 並定義下列屬性:

- `VerticalItemSpacing`, 屬於類型`double`, 表示每個專案周圍的垂直空白空間。 這個屬性的預設值為 0, 且其值必須一律大於或等於0。
- `HorizontalItemSpacing`, 屬於類型`double`, 表示每個專案周圍的水準空白空間。 這個屬性的預設值為 0, 且其值必須一律大於或等於0。
- `Span`, 屬於類型`int`, 表示要在方格中顯示的資料行或資料列數目。 這個屬性的預設值為 1, 且其值必須一律大於或等於1。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)使用原生版面配置引擎來執行版面配置。

## <a name="vertical-list"></a>垂直清單

根據預設, [`CollectionView`](xref:Xamarin.Forms.CollectionView)會以垂直清單版面配置顯示其專案。 因此, 不需要將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為使用此配置:

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

不過, 基於完整性, [`CollectionView`](xref:Xamarin.Forms.CollectionView)您可以將其[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為`VerticalList`, 將設定為以垂直清單顯示其專案:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

或者, 也可以藉[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)由將屬性設定為[`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout)類別的物件, 並將[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員指定`Vertical`為`Orientation`屬性值, 來完成這項作業:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Vertical
};
```

這會產生單一資料行清單, 當加入新專案時, 會以垂直方式成長:

[ ![CollectionView 垂直清單版面配置在 IOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-list.png "CollectionView 垂直清單版面")]配置(layout-images/vertical-list-large.png#lightbox "CollectionView 垂直清單版面")配置

## <a name="horizontal-list"></a>水準清單

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將專案的[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為, `HorizontalList`以將其專案顯示在水準清單中:

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

或者, 也可以藉[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)由將屬性設定為[`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout)類別的物件, 並將[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員指定`Horizontal`為`Orientation`屬性值, 來完成這項作業:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Horizontal
};
```

這會產生單一資料列清單, 這會在新增新專案時水準成長:

[ ![CollectionView 水準清單配置在 IOS 和 Android 上的螢幕擷取畫面](layout-images/horizontal-list.png "CollectionView 水準清單版面")]配置(layout-images/horizontal-list-large.png#lightbox "CollectionView 水準清單版面")配置

## <a name="vertical-grid"></a>垂直格線

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)專案的屬性設定為[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)屬性設為`Vertical`的[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)物件, 以在垂直方格中顯示其專案:

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

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

根據預設, 垂直[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)會在單一資料行中顯示專案。 不過, 此範例會將`GridItemsLayout.Span`屬性設定為2。 這會產生兩個數據行的方格, 在加入新專案時, 會以垂直方式成長:

[ ![CollectionView 垂直格線版面配置的螢幕擷取畫面, 在 IOS 和 Android 上](layout-images/vertical-grid.png "CollectionView 垂直格線版面")]配置(layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直格線版面")配置

## <a name="horizontal-grid"></a>水準方格

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)專案的屬性設定為[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)屬性設為`Horizontal`的[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)物件, 以水準方格顯示其專案:

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

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

根據預設, 水準[`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout)會顯示單一資料列中的專案。 不過, 此範例會將`GridItemsLayout.Span`屬性設定為4。 這會產生四個數據列的方格, 這會在新增新專案時水準成長:

[ ![CollectionView 水準格線版面配置的螢幕擷取畫面, 在 IOS 和 Android 上](layout-images/horizontal-grid.png "CollectionView 水準方格版面")]配置(layout-images/horizontal-grid-large.png#lightbox "CollectionView 水準格線版面")配置

## <a name="headers-and-footers"></a>頁首和頁尾

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以呈現使用清單中的專案來滾動的頁首和頁尾。 頁首和頁尾可以是字串、views 或[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義用來指定頁首和頁尾的下列屬性:

- `Header`, 屬於類型`object`, 可指定要在清單開頭顯示的字串、系結或查看。
- `HeaderTemplate`, 屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), `DataTemplate`可指定要用來格式化的`Header`。
- `Footer`, 屬於類型`object`, 可指定要顯示在清單結尾的字串、系結或查看。
- `FooterTemplate`, 屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate), `DataTemplate`可指定要用來格式化的`Footer`。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援, 這表示屬性可以是資料系結的目標。

當標頭加入至水準成長的版面配置時 (從左至右), 標題會顯示在清單的左邊。 同樣地, 當頁尾加入至水準成長的版面配置時 (從左至右), 頁尾會顯示在清單的右邊。

### <a name="display-strings-in-the-header-and-footer"></a>在頁首和頁尾中顯示字串

和屬性可以設定為`string`值, 如下列範例所示: `Footer` `Header`

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

此程式碼會產生下列螢幕擷取畫面, 其中的標頭顯示在 iOS 螢幕擷取畫面中, 而頁尾顯示于 Android 螢幕擷取畫面:

[ ![CollectionView 字串頁首和頁尾 (在 IOS 和 Android 上) 的螢幕擷取畫面](layout-images/header-footer-string.png "CollectionView 字串標頭和")]頁尾(layout-images/header-footer-string-large.png#lightbox "CollectionView 字串頁首和")頁尾

### <a name="display-views-in-the-header-and-footer"></a>在頁首和頁尾中顯示 views

`Header` 和`Footer`屬性可以設定為一個視圖。 這可以是單一視圖, 或包含多個子視圖的視圖。 `Header`下列範例顯示每個設定`Footer`為包含[`Label`](xref:Xamarin.Forms.Label)物件之[`StackLayout`](xref:Xamarin.Forms.StackLayout)物件的和屬性:

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

對等的 C# 程式碼是：

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

此程式碼會產生下列螢幕擷取畫面, 其中的標頭顯示在 iOS 螢幕擷取畫面中, 而頁尾顯示于 Android 螢幕擷取畫面:

[在 iOS 和 Android(layout-images/header-footer-view.png "CollectionView view 頁首和")頁尾![上使用 views CollectionView 頁首和頁尾的螢幕擷取畫面]](layout-images/header-footer-view-large.png#lightbox "CollectionView view 頁首和")頁尾

### <a name="display-a-templated-header-and-footer"></a>顯示樣板化頁首和頁尾

和屬性可以設定為[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)用來格式化頁首和頁尾的物件。 `FooterTemplate` `HeaderTemplate` 在此案例中, `Header`和`Footer`屬性必須系結至要套用之範本的目前來源, 如下列範例所示:

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

對等的 C# 程式碼是：

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

此程式碼會產生下列螢幕擷取畫面, 其中的標頭顯示在 iOS 螢幕擷取畫面中, 而頁尾顯示于 Android 螢幕擷取畫面:

[![在 IOS 和 Android 上使用範本的 CollectionView 頁首和頁尾螢幕擷取畫面](layout-images/header-footer-template.png "CollectionView 範本頁首和")]頁尾(layout-images/header-footer-template-large.png#lightbox "CollectionView 範本頁首和")頁尾

## <a name="item-spacing"></a>專案間距

根據預設, 中的[`CollectionView`](xref:Xamarin.Forms.CollectionView)每個專案都不會有任何空白空間。 您可以藉由在所使用`CollectionView`的專案配置上設定屬性來變更這個行為。

[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) [當將`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout)其屬性[`CollectionView`](xref:Xamarin.Forms.CollectionView)設定為物件`double`時, 可以將屬性設定為代表每個專案周圍空白空間的值:`ListItemsLayout.ItemSpacing`

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout ItemSpacing="20">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> `ListItemsLayout.ItemSpacing`屬性具有驗證回呼集, 可確保屬性的值一律大於或等於0。

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

此程式碼會產生垂直單一資料行清單, 每個專案的間距為 20:

[![在 IOS 和 Android CollectionView 專案間距上具有專案間距的 CollectionView 螢幕擷取畫面](layout-images/vertical-list-spacing.png " ") ](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 專案間距")

[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) `GridItemsLayout.HorizontalItemSpacing` 當將`GridItemsLayout.VerticalItemSpacing`其屬性`double`設定為物件時, 可以將和屬性設定為代表每個專案之垂直和水準的空白空間值: [`CollectionView`](xref:Xamarin.Forms.CollectionView)

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
> `GridItemsLayout.VerticalItemSpacing` 和`GridItemsLayout.HorizontalItemSpacing`屬性已設定驗證回呼, 這可確保屬性的值一律大於或等於0。

對等的 C# 程式碼是：

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

這段程式碼會產生垂直的兩個數據行方格, 其中每個專案的垂直間距為 20, 而每個專案的水準間距為 30:

[![在 IOS 和 Android CollectionView 專案間距上具有專案間距的 CollectionView 螢幕擷取畫面](layout-images/vertical-grid-spacing.png " ") ](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 專案間距")

## <a name="item-sizing"></a>專案大小

根據預設, 中[`CollectionView`](xref:Xamarin.Forms.CollectionView)的每個專案會個別測量並調整大小, 但前提是[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中的 UI 元素不會指定固定大小。 這個行為 (可以變更) 是由[`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)屬性值指定。 這個屬性值可以設定為其中一個[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)列舉成員:

- `MeasureAllItems`–會個別測量每個專案。 這是預設值。
- `MeasureFirstItem`–只測量第一個專案, 並將所有後續專案的大小指定為與第一個專案相同。

> [!IMPORTANT]
> 當`MeasureFirstItem`專案大小要在所有專案之間保持一致時, 調整大小策略會導致效能增加。

下列程式碼範例示範如何設定[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)屬性:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

對等的 C# 程式碼是：

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>動態調整專案大小

中[`CollectionView`](xref:Xamarin.Forms.CollectionView)的專案可以在執行時間以動態方式調整大小, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)方法是在中變更元素的版面配置相關屬性。 例如, 下列程式[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`Image`](xref:Xamarin.Forms.Image)代碼範例會變更物件的[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)和屬性:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

系統會執行[`Image`](xref:Xamarin.Forms.Image) 事件處理常式,以回應所按下的物件,並變更影像的維度,以便更`OnImageTapped`輕鬆地查看:

[在 iOS 和 Android(layout-images/runtime-resizing.png "CollectionView 動態專案大小")![上動態專案調整大小的 CollectionView 螢幕擷取畫面]](layout-images/runtime-resizing-large.png#lightbox "CollectionView 動態專案大小")

## <a name="right-to-left-layout"></a>由右至左的版面配置

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以將其內容設定為[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft), 以由右至左的流程方向來配置其內容。 不過, 在`FlowDirection`理想的情況下, 屬性應該設定在頁面或根配置上, 這會導致頁面或根配置中的所有元素回應流程方向:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
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

具有父系[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)之元素的預設值為。 [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) 因此, [`CollectionView`](xref:Xamarin.Forms.CollectionView)會`FlowDirection`從`FlowDirection` [`ContentPage`](xref:Xamarin.Forms.ContentPage)繼承屬性值[,而後者又會繼承的屬性值。`StackLayout`](xref:Xamarin.Forms.StackLayout) 這會導致由右至左的版面配置, 如下列螢幕擷取畫面所示:

[ ![CollectionView 由右至左垂直清單版面配置的螢幕擷取畫面, 在 IOS 和 Android 上 CollectionView 由](layout-images/vertical-list-rtl.png "右至左的垂直清單版面")]配置(layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 由右至左垂直清單版面")配置

如需流程方向的詳細資訊, 請參閱由[右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CollectionView (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin. 表單 CollectionView 滾動](scrolling.md)
