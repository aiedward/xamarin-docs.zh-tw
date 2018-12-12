---
title: Xamarin.Forms CollectionView
description: CollectionView 是富彈性且高效能的檢視，來呈現資料使用不同的版面配置規格的清單。
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2018
ms.openlocfilehash: 46ab8169b31624e3862cf14f477bcd6cf8c8f3a3
ms.sourcegitcommit: 408b78dd6eded4696469e316af7922a5991f2211
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2018
ms.locfileid: "53246304"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.Forms CollectionView

![預覽](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)

`CollectionView` 呈現資料的清單檢視使用不同的版面配置規格。 它旨在提供更多的彈性，以及高效能替代方式`ListView`。 雖然`CollectionView`和`ListView`Api 類似，有一些值得注意的差異：

- `CollectionView` 有沒有儲存格的概念。 相反地，資料範本用來在清單中定義的每個資料項目的外觀。
- `CollectionView` 減少的 API 介面`ListView`。 許多屬性和事件`ListView`不會出現在`CollectionView`。
- `CollectionView` 具有彈性的配置模型，可讓清單或方格中顯示的水平或垂直的資料。

A`CollectionView`耗用藉由設定其`ItemsSource`屬性來實作任何集合`IEnumerable`，並將其`ItemTemplate`屬性設`DataTemplate`，定義每個清單項目外觀。 此外，其`ItemsLayout`屬性必須設為`ItemsLayout`類別，來定義版面配置規格的清單。

> [!IMPORTANT]
> `CollectionView`目前為早期預覽，且缺少的大部分其計劃的功能。 此外，當在實作完成，會變更 API。

`CollectionView` 使用 Xamarin.Forms 4.0 pre1。 不過，目前為實驗性，而且僅適用於藉由將下列這一行的程式碼，以您`AppDelegate`類別在 iOS 上，或您`MainActivity`類別在 Android 上，然後再呼叫`Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

## <a name="populating-a-collectionview-with-data"></a>填入資料 CollectionView

A`CollectionView`藉由設定資料填入其`ItemSource`屬性來實作任何集合`IEnumerable`。 可以在 XAML 中加入項目，初始化`ItemsSource`從項目的陣列的屬性：

```xaml
<CollectionView>
  <CollectionView.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </CollectionView.ItemsSource>
</CollectionView>
```

> [!NOTE]
> 請注意，`x:Array`元素需要`Type`屬性，指出陣列中項目的類型。

颾魤 ㄛ`CollectionView`可以使用資料來填入繫結中使用資料繫結其`ItemsSource`屬性設`IEnumerable`集合。 在 XAML 中，這達成與`Binding`標記延伸模組：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

在此範例中，`ItemsSource`屬性的資料繫結至`Monkeys`連接的檢視模型，它會傳回屬性`IList<Monkey>`集合。 下列程式碼範例示範`Monkey`類別，其中包含四個屬性：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

## <a name="providing-a-view-for-the-empty-state"></a>提供空白的狀態檢視

當`CollectionView`沒有任何資料，若要顯示，可以向使用者顯示適當的訊息。 這可以藉由設定`CollectionView.EmptyView`屬性，以`object`，將所指定集合顯示`ItemSource`屬性是空的：

```xaml
<CollectionView ItemsSource="{Binding EmptyMonkeys}">
    <CollectionView.EmptyView>
        <Label Text="No items to display" />
    </CollectionView.EmptyView>
</CollectionView>
```

`EmptyView`屬性可以設定為`string`、 繫結，或檢視中，因此如有需要，可以包含的互動式內容。

颾魤 ㄛ`EmptyViewTemplate`屬性可以設定為`DataTemplate`將用來格式化`EmptyView`。

## <a name="defining-list-item-appearance"></a>定義清單項目外觀

每個項目中資料的外觀`CollectionView`可以藉由設定定義`CollectionView.ItemTemplate`屬性設`DataTemplate`:

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
    ...
</CollectionView>
```

在此範例中，`DataTemplate`包含`Grid`具有`Image`，並將兩個`Label`執行個體，所有繫結的屬性至`Monkey`物件。

如需有關資料範本的詳細資訊，請參閱[資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="specifying-a-layout"></a>指定版面配置

根據預設，`CollectionView`會以垂直清單顯示其項目。 不過，下列的版面配置規格可以使用任何：

- 垂直清單-以垂直方式成長，因為新的項目會加入單一資料行清單。
- 水平清單 – 水平成長加入新項目時的單一資料列清單。
- 垂直格線 – 垂直成長，因為新的項目會加入多重資料行方格。
- 水平格線 – 新的項目新增時，水平成長的多重資料列方格。

可以設定來指定這些版面配置`CollectionView.ItemsLayout`屬性，以`ItemsLayout`類別，與`ListItemsLayout`類別提供清單版面配置，而`GridItemsLayout`提供格線版面配置的類別。

`ItemsLayout`類別會定義`Orientation`型別的屬性`ItemsLayoutOrientation`。 `ItemsLayoutOrientation`列舉會定義`Vertical`和`Horizontal`成員值。

`ListItemsLayout`類別繼承自`ItemsLayout`類別，並定義靜態`VerticalList`和`HorizontalList`成員。 可用來建立垂直或水平清單中，分別設定了這些成員。 或者，`ListItemsLayout`執行個體可建立、 指定`ItemsLayoutOrientation`列舉成員，做為引數。

`GridItemsLayout`類別繼承自`ItemsLayout`類別，並定義`Span`型別的屬性`int`，表示資料行或在方格中顯示的資料列數目。 預設值`Span`屬性為 1，而且其值必須一律是大於或等於 1。

### <a name="vertical-list"></a>垂直清單

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

或者，這也可透過設定`ItemsLayout`的執行個體的內容`ListItemsLayout`類別，指定`Vertical``ItemsLayoutOrientation`列舉成員，做為引數：

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

這會產生單一資料行清單中，以垂直方式成長，因為會加入新項目：

[![CollectionView 垂直清單版面配置](collectionview-images/vertical-list.png "CollectionView 垂直清單版面配置")](collectionview-images/vertical-list-large.png#lightbox)

### <a name="horizontal-list"></a>水平清單

A`CollectionView`可以顯示水平 清單中的其項目藉由設定其`ItemsLayout`靜態屬性`ListItemsLayout.HorizontalList`成員：

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

或者，這也可透過設定`ItemsLayout`的執行個體的內容`ListItemsLayout`類別，指定`Horizontal``ItemsLayoutOrientation`列舉成員，做為引數：

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

這會產生單一資料列清單中，水平成長加入新項目時：

[![CollectionView 水平清單版面配置](collectionview-images/horizontal-list.png "CollectionView 水平清單版面配置")](collectionview-images/horizontal-list-large.png#lightbox)

### <a name="vertical-grid"></a>垂直格線

A`CollectionView`可以顯示其項目中垂直格線設定其`ItemsLayout`屬性設`GridItemsLayout`執行個體，其`Orientation`屬性設定為`Vertical`:

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

根據預設，垂直`GridItemsLayout`會顯示單一資料行中的項目。 不過，此範例會將`GridItemsLayout.Span`屬性設為 2。 這會導致兩欄方格中，以垂直方式成長，因為會加入新項目：

[![CollectionView 垂直格線版面配置](collectionview-images/vertical-grid.png "CollectionView 垂直格線版面配置")](collectionview-images/vertical-grid-large.png#lightbox)

### <a name="horizontal-grid"></a>水平格線

A`CollectionView`可以顯示水平格線中的其項目設定其`ItemsLayout`屬性設`GridItemsLayout`執行個體，其`Orientation`屬性設定為`Horizontal`:

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

根據預設，水平`GridItemsLayout`會顯示單一資料列中的項目。 不過，此範例會將`GridItemsLayout.Span`屬性設為 4。 這會導致資料列的四個方格中，水平成長加入新項目時：

[![CollectionView 水平格線版面配置](collectionview-images/horizontal-grid.png "CollectionView 水平格線版面配置")](collectionview-images/horizontal-grid-large.png#lightbox)

## <a name="scrolling"></a>捲動

`CollectionView` 可以捲動以檢視要求的項目`ScrollTo`方法。 這個方法會將指定的索引處的項目捲動至檢視：

```csharp
_collectionView.ScrollTo(12);
```

或者，此方法多載可用來指定的項目捲動至檢視：

```csharp
var viewModel = BindingContext as MonkeysViewModel;
var monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
_collectionView.ScrollTo(monkey);
```

這兩個多載可讓其他的引數指定，表示這個項目群組、 項目之後的捲軸已完成 (start、 置中，結尾)，以及是否要以動畫顯示捲軸的確切位置。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://github.com/xamarin/xamarin-forms-samples/tree/forms40/UserInterface/CollectionViewDemos/)
