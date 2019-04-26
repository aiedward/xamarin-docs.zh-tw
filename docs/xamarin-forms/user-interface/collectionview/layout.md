---
title: 指定 Xamarin.Forms CollectionView 版面配置
description: 根據預設，CollectionView 會以垂直清單顯示其項目。 不過，可以指定垂直和水平清單與格線。
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2019
ms.openlocfilehash: 8ed365ed41ac31c66d41f1a32a7a16929cdc6770
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61367535"
---
# <a name="specify-xamarinforms-collectionview-layout"></a>指定 Xamarin.Forms CollectionView 版面配置

![預覽](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

> [!IMPORTANT]
> `CollectionView`目前為預覽狀態，且缺少其中一些規劃功能。 此外，實作完成時，可能會變更的 API。

`CollectionView` 會定義下列屬性，控制配置：

- `ItemsLayout`型別的`IItemsLayout`，指定要使用的配置。
- `ItemSizingStrategy`型別的`ItemSizingStrategy`，指定要使用的項目量值策略。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。

根據預設，`CollectionView`會以垂直清單顯示其項目。 不過，為下列配置的任何可用：

- 垂直清單-以垂直方式成長，因為新的項目會加入單一資料行清單。
- 水平清單 – 水平成長加入新項目時的單一資料列清單。
- 垂直格線 – 垂直成長，因為新的項目會加入多重資料行方格。
- 水平格線 – 新的項目新增時，水平成長的多重資料列方格。

可以設定來指定這些版面配置`ItemsLayout`類別的屬性衍生自`ItemsLayout`類別。 這個類別會定義下列屬性：

- `Orientation`型別的`ItemsLayoutOrientation`，在其中指定的方向`CollectionView`擴充項目會加入。
- `SnapPointsAlignment`型別的`SnapPointsAlignment`，指定與項目貼齊點的對齊方式。
- `SnapPointsType`型別的`SnapPointsType`，指定捲動時貼齊點的行為。

這些屬性都會受到[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)物件，這表示，屬性可以是資料繫結的目標。 如需貼齊點的詳細資訊，請參閱[貼齊點](scrolling.md#snap-points)中[捲動至檢視的項目](scrolling.md)指南。

`ItemsLayoutOrientation`列舉會定義下列成員：

- `Vertical` 表示`CollectionView`加入項目時將會以垂直方式展開。
- `Horizontal` 表示`CollectionView`加入項目時將會水平展開。

`ListItemsLayout`類別繼承自`ItemsLayout`類別，並定義靜態`VerticalList`和`HorizontalList`成員。 可用來建立垂直或水平清單中，分別設定了這些成員。 或者，`ListItemsLayout`可以建立物件，指定`ItemsLayoutOrientation`列舉成員，做為引數。

`GridItemsLayout`類別繼承自`ItemsLayout`類別，並定義`Span`型別的屬性`int`，表示資料行或在方格中顯示的資料列數目。 預設值`Span`屬性為 1，而且其值必須一律是大於或等於 1。

> [!NOTE]
> `CollectionView` 您可以使用原生的版面配置引擎來執行版面配置。

## <a name="vertical-list"></a>垂直清單

根據預設，`CollectionView`會垂直清單配置中顯示其項目。 因此，不需要設定`ItemsLayout`屬性，以使用這種版面配置：

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

不過，基於完整性，`CollectionView`可以設定以垂直清單顯示其項目，藉由設定其`ItemsLayout`靜態`ListItemsLayout.VerticalList`成員：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.VerticalList}">
    ...
</CollectionView>
```

或者，這也可透過設定`ItemsLayout`之物件的屬性`ListItemsLayout`類別，指定`Vertical``ItemsLayoutOrientation`列舉成員，做為引數：

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
    ItemsLayout = ListItemsLayout.VerticalList
};
```

這會產生單一資料行清單中，以垂直方式成長，因為會加入新項目：

[![CollectionView 垂直清單版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-list.png "CollectionView 垂直清單配置")](layout-images/vertical-list-large.png#lightbox "CollectionView 垂直清單版面配置")

## <a name="horizontal-list"></a>水平清單

`CollectionView` 其項目水平清單顯示設定其`ItemsLayout`靜態屬性`ListItemsLayout.HorizontalList`成員：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="{x:Static ListItemsLayout.HorizontalList}">
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

或者，這也可透過設定`ItemsLayout`屬性，以`ListItemsLayout`物件，指定`Horizontal``ItemsLayoutOrientation`列舉成員，做為引數：

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
    ItemsLayout = ListItemsLayout.HorizontalList
};
```

這會產生單一資料列清單中，水平成長加入新項目時：

[![CollectionView 水平清單版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/horizontal-list.png "CollectionView 水平清單配置")](layout-images/horizontal-list-large.png#lightbox "CollectionView 水平清單版面配置")

## <a name="vertical-grid"></a>垂直格線

`CollectionView` 可以顯示其項目中垂直格線設定其`ItemsLayout`屬性，以`GridItemsLayout`物件，其`Orientation`屬性設定為`Vertical`:

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

根據預設，垂直`GridItemsLayout`會顯示單一資料行中的項目。 不過，此範例會將`GridItemsLayout.Span`屬性設為 2。 這會導致兩欄方格中，以垂直方式成長，因為會加入新項目：

[![CollectionView 垂直格線版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-grid.png "CollectionView 垂直格線版面配置")](layout-images/vertical-grid-large.png#lightbox "CollectionView 垂直格線版面配置")

## <a name="horizontal-grid"></a>水平格線

`CollectionView` 可以顯示水平格線中的其項目設定其`ItemsLayout`屬性，以`GridItemsLayout`物件，其`Orientation`屬性設定為`Horizontal`:

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

根據預設，水平`GridItemsLayout`會顯示單一資料列中的項目。 不過，此範例會將`GridItemsLayout.Span`屬性設為 4。 這會導致資料列的四個方格中，水平成長加入新項目時：

[![CollectionView 水平格線版面配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/horizontal-grid.png "CollectionView 水平格線版面配置")](layout-images/horizontal-grid-large.png#lightbox "CollectionView 水平格線版面配置")

## <a name="right-to-left-layout"></a>從右至左的配置

`CollectionView` 可以藉由設定版面配置其內容由右至左的流向它[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)屬性設[ `RightToLeft` ](xref:Xamarin.Forms.FlowDirection.RightToLeft)。 不過，`FlowDirection`應該在理想情況下上設定屬性頁面或根版面配置，這會導致頁面上或根版面配置中的所有項目，以回應流程方向：

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

預設值[ `FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection)元素與父代[ `MatchParent` ](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此，`CollectionView`繼承`FlowDirection`屬性值從[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，依序繼承`FlowDirection`屬性值從[ `ContentPage` ](xref:Xamarin.Forms.ContentPage). 這會導致從右至左配置中的下列螢幕擷取畫面所示：

[![CollectionView 垂直清單中由右至左配置，在 iOS 和 Android 上的螢幕擷取畫面](layout-images/vertical-list-rtl.png "CollectionView 垂直清單中由右至左配置")](layout-images/vertical-list-rtl-large.png#lightbox "CollectionView 由右至左垂直清單版面配置")

如需有關資料流程方向的詳細資訊，請參閱[由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="item-sizing"></a>項目調整大小

根據預設，每個項目`CollectionView`個別測量和調整大小，前提是中的 UI 元素[ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate)未指定固定的大小。 此行為，可以變更，由`CollectionView.ItemSizingStrategy`屬性值。 這個屬性值可以設定為其中一個`ItemSizingStrategy`列舉成員：

- `MeasureAllItems` – 個別測量每個項目。 這是預設值。
- `MeasureFirstItem` – 只有第一個項目使用進行測量，所有後續的項目提供第一個項目相同的大小。

> [!IMPORTANT]
> `MeasureFirstItem`調整大小策略應在其中的項目大小是跨所有的項目，是統一，因此會導致增加效能的情況下。

下列程式碼範例顯示設定`ItemSizingStrategy`屬性：

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

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
- [從右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [捲動至檢視的項目](scrolling.md)
