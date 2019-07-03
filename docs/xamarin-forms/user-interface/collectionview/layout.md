---
title: Xamarin.Forms CollectionView 版面配置
description: 根據預設，CollectionView 會以垂直清單顯示其項目。 不過，可以指定垂直和水平清單與格線。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2019
ms.openlocfilehash: 786cea04718022847bba2ecffed8f377dd49bd8b
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512794"
---
# <a name="xamarinforms-collectionview-layout"></a>Xamarin.Forms CollectionView 版面配置

![](~/media/shared/preview.png "此 API 目前是發行前版本")

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 會定義下列屬性，控制配置：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)型別的[ `IItemsLayout` ](xref:Xamarin.Forms.IItemsLayout)，指定要使用的配置。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)型別的[ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy)，指定要使用的項目量值策略。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。

根據預設， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)會以垂直清單顯示其項目。 不過，為下列配置的任何可用：

- 垂直清單-以垂直方式成長，因為新的項目會加入單一資料行清單。
- 水平清單 – 水平成長加入新項目時的單一資料列清單。
- 垂直格線 – 垂直成長，因為新的項目會加入多重資料行方格。
- 水平格線 – 新的項目新增時，水平成長的多重資料列方格。

可以設定來指定這些版面配置[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)類別的屬性衍生自[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout)類別。 這個類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)型別的[ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation)，在其中指定的方向[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)擴充項目會加入。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)型別的[ `SnapPointsAlignment` ](xref:Xamarin.Forms.SnapPointsAlignment)，指定與項目貼齊點的對齊方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)型別的[ `SnapPointsType` ](xref:Xamarin.Forms.SnapPointsType)，指定捲動時貼齊點的行為。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。 如需貼齊點的詳細資訊，請參閱[貼齊點](scrolling.md#snap-points)中[Xamarin.Forms CollectionView 捲動](scrolling.md)指南。

[ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員：

- `Vertical` 指出[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)加入項目時將會以垂直方式展開。
- `Horizontal` 指出[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)加入項目時將會水平展開。

[ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout)類別繼承自[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout)類別，並定義`ItemSpacing`類型的屬性， `double`，表示每個項目周圍的空白空間。 這個屬性的預設值為 0，而且其值必須一律是大於或等於 0。 `ListItemsLayout`類別也會定義靜態`Vertical`和`Horizontal`成員。 可用來建立垂直或水平清單中，分別設定了這些成員。 或者，`ListItemsLayout`可以建立物件，指定[ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員，做為引數。

[ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout)類別繼承自[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsLayout)類別，並定義下列屬性：

- `VerticalItemSpacing`型別的`double`，表示每個項目周圍的垂直空白空間。 這個屬性的預設值為 0，而且其值必須一律是大於或等於 0。
- `HorizontalItemSpacing`型別的`double`，表示每個項目周圍的水平空白空間。 這個屬性的預設值為 0，而且其值必須一律是大於或等於 0。
- `Span`型別的`int`，表示資料行或在方格中顯示的資料列數目。 這個屬性的預設值為 1，而且其值必須一律是大於或等於 1。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) 您可以使用原生的版面配置引擎來執行版面配置。

## <a name="vertical-list"></a>垂直清單

根據預設， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)會垂直清單配置中顯示其項目。 因此，不需要設定[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性，以使用這種版面配置：

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

不過，基於完整性， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以設定以垂直清單顯示其項目，藉由設定其[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設`VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

或者，這也可透過設定[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)之物件的屬性[ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout)類別中，指定`Vertical` [ `ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員，做為引數：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
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

這會產生單一資料行清單中，以垂直方式成長，因為會加入新項目：

[![CollectionView 垂直清單版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-list.png "CollectionView 垂直清單配置")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直清單版面配置")

## <a name="horizontal-list"></a>水平清單

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 其項目水平清單顯示設定其[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設`HorizontalList`:

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

或者，這也可透過設定[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設[ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout)物件，指定`Horizontal` [ `ItemsLayoutOrientation` ](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員，做為引數：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout>
            <x:Arguments>
                <ItemsLayoutOrientation>Horizontal</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
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

這會產生單一資料列清單中，水平成長加入新項目時：

[![CollectionView 水平清單版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/horizontal-list.png "CollectionView 水平清單配置")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水平清單版面配置")

## <a name="vertical-grid"></a>垂直格線

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以顯示其項目中垂直格線設定其[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設[ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout)物件，其[ `Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation)屬性設定為`Vertical`:

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

根據預設，垂直[ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout)會顯示單一資料行中的項目。 不過，此範例會將`GridItemsLayout.Span`屬性設為 2。 這會導致兩欄方格中，以垂直方式成長，因為會加入新項目：

[![CollectionView 垂直格線版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-grid.png "CollectionView 垂直格線版面配置")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直格線版面配置")

## <a name="horizontal-grid"></a>水平格線

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以顯示水平格線中的其項目設定其[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設[ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout)物件，其[`Orientation` ](xref:Xamarin.Forms.ItemsLayout.Orientation)屬性設定為`Horizontal`:

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

根據預設，水平[ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout)會顯示單一資料列中的項目。 不過，此範例會將`GridItemsLayout.Span`屬性設為 4。 這會導致資料列的四個方格中，水平成長加入新項目時：

[![CollectionView 水平格線版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/horizontal-grid.png "CollectionView 水平格線版面配置")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水平格線版面配置")

## <a name="item-spacing"></a>項目間距

根據預設，每個項目[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)沒有其周圍的任何空白區域。 來變更此行為，請設定屬性上使用的項目配置`CollectionView`。

當[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)設定其[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設[ `ListItemsLayout` ](xref:Xamarin.Forms.ListItemsLayout)物件`ListItemsLayout.ItemSpacing`屬性可以設定為`double`值，表示每個項目周圍的空白空間：

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
> `ListItemsLayout.ItemSpacing`屬性設有驗證回呼，以確保屬性的值一律是大於或等於 0。

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

此程式碼會產生在垂直的單一資料行清單中，具有每個項目周圍的 20 個間距：

[![螢幕擷取畫面，以在 iOS 和 Android 上的項目間距 collectionview během](layout-images/vertical-list-spacing.png "CollectionView 項目間距")](layout-images/vertical-list-spacing-large.png#lightbox "CollectionView 項目間距")

當[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)設定其[ `ItemsLayout` ](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設[ `GridItemsLayout` ](xref:Xamarin.Forms.GridItemsLayout)物件`GridItemsLayout.VerticalItemSpacing`和`GridItemsLayout.HorizontalItemSpacing`屬性可以是若要設定`double`代表每個項目周圍的空白空間垂直和水平的值：

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
> `GridItemsLayout.VerticalItemSpacing`和`GridItemsLayout.HorizontalItemSpacing`屬性有設定，它們可確保屬性的值都大於或等於 0 的驗證回撥。

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

此程式碼會產生垂直的雙資料行格線，具有垂直間距設為每個項目，周圍的 20 和 30，每個項目周圍的水平間距：

[![螢幕擷取畫面，以在 iOS 和 Android 上的項目間距 collectionview během](layout-images/vertical-grid-spacing.png "CollectionView 項目間距")](layout-images/vertical-grid-spacing-large.png#lightbox "CollectionView 項目間距")

## <a name="item-sizing"></a>項目調整大小

根據預設，每個項目[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)個別測量和調整大小，前提是中的 UI 元素[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)未指定固定的大小。 此行為，可以變更，由[ `CollectionView.ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)屬性值。 這個屬性值可以設定為其中一個[ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemSizingStrategy)列舉成員：

- `MeasureAllItems` – 個別測量每個項目。 這是預設值。
- `MeasureFirstItem` – 只有第一個項目使用進行測量，所有後續的項目提供第一個項目相同的大小。

> [!IMPORTANT]
> `MeasureFirstItem`調整大小策略時，會導致更高的效能時，項目大小的目的是跨所有項目是統一的情況下使用。

下列程式碼範例顯示設定[ `ItemSizingStrategy` ](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)屬性：

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

## <a name="dynamic-resizing-of-items"></a>動態調整大小的項目

中的項目[ `CollectionView` ](xref:Xamarin.Forms.CollectionView)可以動態調整大小在執行階段變更版面配置相關的屬性內之項目的[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)。 例如，下列程式碼範例會變更[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)並[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)屬性[ `Image` ](xref:Xamarin.Forms.Image)物件：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

`OnImageTapped`事件處理常式會執行以回應[ `Image` ](xref:Xamarin.Forms.Image)物件被佔用，並變更影像的維度，以便更輕鬆地檢視：

[![螢幕擷取畫面與動態項目調整大小，在 iOS 和 Android 上 collectionview během](layout-images/runtime-resizing.png "CollectionView 動態項目調整大小")](layout-images/runtime-resizing-large.png#lightbox "CollectionView 動態項目調整大小")

## <a name="right-to-left-layout"></a>從右至左的配置

[`CollectionView`](xref:Xamarin.Forms.CollectionView) 可以藉由設定版面配置其內容由右至左的流向它[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性設[ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft)。 不過，`FlowDirection`應該在理想情況下上設定屬性頁面或根版面配置，這會導致頁面上或根版面配置中的所有項目，以回應流程方向：

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

預設值[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)元素與父代[ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此， [ `CollectionView` ](xref:Xamarin.Forms.CollectionView)繼承`FlowDirection`屬性值從[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，依序繼承`FlowDirection`屬性值從[`ContentPage`](xref:Xamarin.Forms.ContentPage). 這會導致從右至左配置中的下列螢幕擷取畫面所示：

[![CollectionView 垂直清單中由右至左配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-list-rtl.png "CollectionView 垂直清單中由右至左配置")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 由右至左垂直清單版面配置")

如需有關資料流程方向的詳細資訊，請參閱[由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [從右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.Forms CollectionView 捲動](scrolling.md)
