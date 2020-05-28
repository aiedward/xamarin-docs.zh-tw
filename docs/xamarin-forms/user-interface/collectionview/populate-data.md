---
title: Xamarin.FormsCollectionView 資料
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e381184271d4a7bfa9872d2502d2281b1f3864bf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134559"
---
# <a name="xamarinforms-collectionview-data"></a>Xamarin.FormsCollectionView 資料

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)包含下列屬性，可定義要顯示的資料及其外觀：

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)，屬於類型 `IEnumerable` ，可指定要顯示的專案集合，並具有的預設值 `null` 。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)，屬於類型 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，可指定要套用至要顯示的專案集合中每個專案的範本。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)定義 `ItemsUpdatingScrollMode` 屬性，代表將 `CollectionView` 新專案加入其中時的滾動行為。 如需這個屬性的詳細資訊，請參閱[在新增新專案時控制捲軸位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)在使用者滾動時支援累加式資料虛擬化。 如需詳細資訊，請參閱[以累加方式載入資料](#load-data-incrementally)。

## <a name="populate-a-collectionview-with-data"></a>在 CollectionView 中填入資料

[`CollectionView`](xref:Xamarin.Forms.CollectionView)會藉由將資料的屬性設定 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 為任何可執行檔集合，來填入資料 `IEnumerable` 。 藉由從字串陣列初始化屬性，可以在 XAML 中加入專案 `ItemsSource` ：

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
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.ItemsSource = new string[]
{
    "Baboon",
    "Capuchin Monkey",
    "Blue Monkey",
    "Squirrel Monkey",
    "Golden Lion Tamarin",
    "Howler Monkey",
    "Japanese Macaque"
};
```

> [!WARNING]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)如果 UI 執行緒已更新，將會擲回例外狀況 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 。

根據預設，會 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 在垂直清單中顯示專案，如下列螢幕擷取畫面所示：

[![IOS 和 Android 上包含文字專案之 CollectionView 的螢幕擷取畫面](populate-data-images/text.png "CollectionView 中的文字專案")](populate-data-images/text-large.png#lightbox "CollectionView 中的文字專案")

> [!IMPORTANT]
> 如果在 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 基礎集合中加入、移除或變更專案時需要重新整理，基礎集合應該是傳送 `IEnumerable` 屬性變更通知的集合，例如 `ObservableCollection` 。

如需如何變更版面配置的詳細資訊 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，請參閱[ Xamarin.Forms CollectionView layout](layout.md)。 如需如何在中定義每個專案外觀的詳細資訊 `CollectionView` ，請參閱[定義專案外觀](#define-item-appearance)。

### <a name="data-binding"></a>資料繫結

[`CollectionView`](xref:Xamarin.Forms.CollectionView)可以使用資料系結將其屬性系結至集合，以填入資料 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable` 。 在 XAML 中，這是透過 `Binding` 標記延伸來達成：

```xaml
<CollectionView ItemsSource="{Binding Monkeys}" />
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此範例中， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性資料會系結至 `Monkeys` 已連接之 viewmodel 的屬性。

> [!NOTE]
> 可以啟用編譯的系結，以改善應用程式中的資料系結效能 Xamarin.Forms 。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結。

## <a name="define-item-appearance"></a>定義專案外觀

將 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 屬性設定為，即可定義中每個專案的外觀 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

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

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    Grid grid = new Grid { Padding = 10 };
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });
    grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Auto });

    Image image = new Image { Aspect = Aspect.AspectFill, HeightRequest = 60, WidthRequest = 60 };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label nameLabel = new Label { FontAttributes = FontAttributes.Bold };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Label locationLabel = new Label { FontAttributes = FontAttributes.Italic, VerticalOptions = LayoutOptions.End };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Grid.SetRowSpan(image, 2);

    grid.Children.Add(image);
    grid.Children.Add(nameLabel, 1, 0);
    grid.Children.Add(locationLabel, 1, 1);

    return grid;
});
```

在中指定的元素會 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義清單中每個專案的外觀。 在此範例中，中的配置 `DataTemplate` 是由所管理 [`Grid`](xref:Xamarin.Forms.Grid) 。 `Grid`包含 [`Image`](xref:Xamarin.Forms.Image) 物件和兩個 [`Label`](xref:Xamarin.Forms.Label) 物件，全都系結至類別的屬性 `Monkey` ：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

下列螢幕擷取畫面顯示清單中每個專案的範本化結果：

[![CollectionView 的螢幕擷取畫面，其中每個專案都是樣板化，在 iOS 和 Android 上](populate-data-images/datatemplate.png "CollectionView 中的樣板化專案")](populate-data-images/datatemplate-large.png#lightbox "CollectionView 中的樣板化專案")

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 由將屬性設定為物件，可在執行時間根據專案值選擇中每個專案的外觀 [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CollectionViewDemos.Controls">
    <ContentPage.Resources>
        <DataTemplate x:Key="AmericanMonkeyTemplate">
            ...
        </DataTemplate>

        <DataTemplate x:Key="OtherMonkeyTemplate">
            ...
        </DataTemplate>

        <controls:MonkeyDataTemplateSelector x:Key="MonkeySelector"
                                             AmericanMonkey="{StaticResource AmericanMonkeyTemplate}"
                                             OtherMonkey="{StaticResource OtherMonkeyTemplate}" />
    </ContentPage.Resources>

    <CollectionView ItemsSource="{Binding Monkeys}"
                    ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性會設定為 `MonkeyDataTemplateSelector` 物件。 下列範例顯示 `MonkeyDataTemplateSelector` 類別：

```csharp
public class MonkeyDataTemplateSelector : DataTemplateSelector
{
    public DataTemplate AmericanMonkey { get; set; }
    public DataTemplate OtherMonkey { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return ((Monkey)item).Location.Contains("America") ? AmericanMonkey : OtherMonkey;
    }
}
```

`MonkeyDataTemplateSelector`類別會定義 `AmericanMonkey` `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 此覆寫會傳回 `OnSelectTemplate` `AmericanMonkey` 範本，當猴子名稱包含 "北美洲" 時，會以青色顯示猴子名稱和位置。 當猴子名稱不包含 "北美洲" 時，覆 `OnSelectTemplate` 寫會傳回 `OtherMonkey` 範本，以顯示銀級中的猴子名稱和位置：

[![在 iOS 和 Android 上 CollectionView 執行時間專案範本選擇的螢幕擷取畫面](populate-data-images/datatemplateselector.png "CollectionView 中的執行時間專案範本選擇")](populate-data-images/datatemplateselector-large.png#lightbox "CollectionView 中的執行時間專案範本選擇")

如需資料範本選取器的詳細資訊，請參閱[Create a Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用時 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，絕對不會將物件的根項目設定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 為 `ViewCell` 。 這會導致擲回例外狀況，因為沒有資料 `CollectionView` 格的概念。

## <a name="context-menus"></a>操作功能表

[`CollectionView`](xref:Xamarin.Forms.CollectionView)透過來支援資料項目目的內容功能表 `SwipeView` ，這會以滑動手勢顯示內容功能表。 `SwipeView`是一個容器控制項，它會包裝內容專案，並提供該內容專案的快顯功能表專案。 因此，會藉 `CollectionView` 由建立 `SwipeView` 定義要包裝之內容的， `SwipeView` 以及由滑動手勢所顯示的操作功能表項目，來為執行的操作功能表。 將設定 `SwipeView` 為中的根視圖 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，以定義中每個資料項目目的外觀，即可達成此目的 `CollectionView` ：

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <SwipeView>
                <SwipeView.LeftItems>
                    <SwipeItems>
                        <SwipeItem Text="Favorite"
                                   IconImageSource="favorite.png"
                                   BackgroundColor="LightGreen"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.FavoriteCommand}"
                                   CommandParameter="{Binding}" />
                        <SwipeItem Text="Delete"
                                   IconImageSource="delete.png"
                                   BackgroundColor="LightPink"
                                   Command="{Binding Source={x:Reference collectionView}, Path=BindingContext.DeleteCommand}"
                                   CommandParameter="{Binding}" />
                    </SwipeItems>
                </SwipeView.LeftItems>
                <Grid BackgroundColor="White"
                      Padding="10">
                    <!-- Define item appearance -->
                </Grid>
            </SwipeView>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

collectionView.ItemTemplate = new DataTemplate(() =>
{
    // Define item appearance
    Grid grid = new Grid { Padding = 10, BackgroundColor = Color.White };
    // ...

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: collectionView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: collectionView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.LeftItems = new SwipeItems { favoriteSwipeItem, deleteSwipeItem };
    swipeView.Content = grid;    
    return swipeView;
});
```

在此範例中， `SwipeView` 內容是 [`Grid`](xref:Xamarin.Forms.Grid) ，它會定義中每個專案的外觀 [`CollectionView`](xref:Xamarin.Forms.CollectionView) 。 [滑動] 專案是用來對內容執行動作 `SwipeView` ，並在從左側撥動控制項時顯示：

[![CollectionView 內容功能表項目在 iOS 和 Android 上的螢幕擷取畫面](populate-data-images/swipeview.png "具有 SwipeView 內容功能表項目的 CollectionView")](populate-data-images/swipeview-large.png#lightbox "具有 SwipeView 內容功能表項目的 CollectionView")

`SwipeView`支援四種不同的輕量方向，其中的滑動方向是由 `SwipeItems` 物件新增至的方向集合所定義 `SwipeItems` 。 根據預設，當使用者按下滑動專案時，就會執行它。 此外，一旦執行了滑動專案，就會隱藏滑動專案並 `SwipeView` 重新顯示內容。 不過，您可以變更這些行為。

如需控制項的詳細資訊 `SwipeView` ，請參閱[ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)。

## <a name="pull-to-refresh"></a>拖動以重新整理

[`CollectionView`](xref:Xamarin.Forms.CollectionView)支援透過提取至重新整理功能 `RefreshView` ，可讓您藉由在專案清單上向下拉出來重新整理顯示的資料。 是容器控制項，可提供將功能的提取重新整理到其子系 `RefreshView` ，前提是子系支援可滾動的內容。 因此，藉由將提取重新整理設定為的子系，來為重新整理執行 `CollectionView` `RefreshView` ：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CollectionView ItemsSource="{Binding Animals}">
        ...
    </CollectionView>
</RefreshView>
```

對等的 C# 程式碼為：

```csharp
RefreshView refreshView = new RefreshView();
ICommand refreshCommand = new Command(() =>
{
    // IsRefreshing is true
    // Refresh data here
    refreshView.IsRefreshing = false;
});
refreshView.Command = refreshCommand;

CollectionView collectionView = new CollectionView();
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = collectionView;
// ...
```

當使用者起始重新整理時， `ICommand` `Command` 會執行由屬性定義的，這應該會重新整理所顯示的專案。 重新整理視覺效果會在進行重新整理時顯示，這是由動畫的進度圓形所組成：

[![IOS 和 Android 上的 CollectionView 提取重新整理的螢幕擷取畫面](populate-data-images/pull-to-refresh.png "CollectionView 的提取更新")](populate-data-images/pull-to-refresh-large.png#lightbox "CollectionView 的提取更新")

屬性的值 `RefreshView.IsRefreshing` 表示的目前狀態 `RefreshView` 。 當使用者觸發重新整理時，這個屬性會自動轉換為 `true` 。 重新整理完成後，您應該將屬性重設為 `false` 。

如需的詳細資訊 `RefreshView` ，請參閱[ Xamarin.Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)。

## <a name="load-data-incrementally"></a>以累加方式載入資料

[`CollectionView`](xref:Xamarin.Forms.CollectionView)在使用者滾動時支援累加式資料虛擬化。 這可讓您在使用者滾動時，從 web 服務以非同步方式載入資料頁等案例。 此外，您可以設定載入更多資料的時間點，讓使用者看不到空白空間，或停止滾動。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)定義下列屬性來控制資料的累加式載入：

- `RemainingItemsThreshold`，屬於類型 `int` ，這是在引發事件的清單中尚未顯示的專案閾值 `RemainingItemsThresholdReached` 。
- `RemainingItemsThresholdReachedCommand`，屬於類型 `ICommand` ，這是在到達時執行的 `RemainingItemsThreshold` 。
- `RemainingItemsThresholdReachedCommandParameter`，屬於 `object` 類型，這是傳遞至 `RemainingItemsThresholdReachedCommand` 的參數。

[`CollectionView`](xref:Xamarin.Forms.CollectionView)也會定義 `RemainingItemsThresholdReached` 當向上 `CollectionView` 滾動到尚未顯示專案的程度時，所引發的事件 `RemainingItemsThreshold` 。 您可以處理這個事件，以載入更多專案。 此外，當事件引發時， `RemainingItemsThresholdReached` `RemainingItemsThresholdReachedCommand` 會執行，以便在 viewmodel 中進行累加式資料載入。

屬性的預設值 `RemainingItemsThreshold` 為-1，表示 `RemainingItemsThresholdReached` 永遠不會引發事件。 當屬性值為0時， `RemainingItemsThresholdReached` 當中的最後一個專案顯示時，就會引發事件 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 。 對於大於0的值， `RemainingItemsThresholdReached` 當 `ItemsSource` 包含尚未滾動到的專案數目時，就會引發事件。

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)驗證 `RemainingItemsThreshold` 屬性，使其值一律大於或等於-1。

下列 XAML 範例顯示 [`CollectionView`](xref:Xamarin.Forms.CollectionView) ，它會以累加方式載入資料：

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                RemainingItemsThreshold="5"
                RemainingItemsThresholdReached="OnCollectionViewRemainingItemsThresholdReached">
    ...
</CollectionView>
```

對等的 C# 程式碼為：

```csharp
CollectionView collectionView = new CollectionView
{
    RemainingItemsThreshold = 5
};
collectionView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

在此程式碼範例中， `RemainingItemsThresholdReached` 當有5個專案尚未滾動到時，就會引發事件，而在回應中會執行 `OnCollectionViewRemainingItemsThresholdReached` 事件處理常式：

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 您也可以藉由將系結 `RemainingItemsThresholdReachedCommand` 至 viewmodel 中的實作為增量方式載入資料 `ICommand` 。

## <a name="related-links"></a>相關連結

- [CollectionView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Xamarin.FormsRefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.FormsSwipeView](~/xamarin-forms/user-interface/swipeview.md)
- [Xamarin.Forms資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
