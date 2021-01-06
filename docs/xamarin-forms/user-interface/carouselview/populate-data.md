---
title: Xamarin.Forms CarouselView 資料
description: CarouselView 會將其 ItemsSource 屬性設定為任何可執行 IEnumerable 的集合，以填入資料。
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b47c810f26dad6c1f6b58eb82bdd234d5cd3f6aa
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940430"
---
# <a name="no-locxamarinforms-carouselview-data"></a>Xamarin.Forms CarouselView 資料

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 包含下列定義要顯示之資料的屬性，以及其外觀：

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)，類型為 `IEnumerable` ，指定要顯示的專案集合，而且具有的預設值 `null` 。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)，類型為 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，指定要套用至要顯示之專案集合中每個專案的範本。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義 `ItemsUpdatingScrollMode` 屬性，這個屬性工作表示 `CarouselView` 新增專案時的滾動行為。 如需此屬性的詳細資訊，請參閱 [在新增專案時控制捲軸位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 在使用者滾動時支援累加式資料虛擬化。 如需詳細資訊，請參閱 [以累加方式載入資料](#load-data-incrementally)。

## <a name="populate-a-carouselview-with-data"></a>使用資料填入 CarouselView

藉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 由將資料的屬性設定為任何執行的集合，來填入資料 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable` 。 依預設，會以 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 水準方式顯示專案。

> [!IMPORTANT]
> 如果在 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 基礎集合中加入、移除或變更專案時需要重新整理，則基礎集合應該是傳送 `IEnumerable` 屬性變更通知的集合，例如 `ObservableCollection` 。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 您可以使用資料系結，將其屬性系結至集合，以填入資料 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) `IEnumerable` 。 在 XAML 中，這是利用 `Binding` 標記延伸來達成：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此範例中， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 屬性資料系結至 `Monkeys` 連接 viewmodel 的屬性。

> [!NOTE]
> 您可以啟用編譯的系結，以改善應用程式中的資料系結效能 Xamarin.Forms 。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

如需如何變更方向的詳細資訊 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，請參閱[ Xamarin.Forms CarouselView 版面](layout.md)配置。 如需如何在中定義每個專案之外觀的詳細資訊 `CarouselView` ，請參閱 [定義專案外觀](#define-item-appearance)。 如需資料系結的詳細資訊，請參閱[ Xamarin.Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結。

## <a name="define-item-appearance"></a>定義專案外觀

您 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以藉由將屬性設定為，來定義中每個專案的外觀 [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                <Frame HasShadow="True"
                       BorderColor="DarkGray"
                       CornerRadius="5"
                       Margin="20"
                       HeightRequest="300"
                       HorizontalOptions="Center"
                       VerticalOptions="CenterAndExpand">
                    <StackLayout>
                        <Label Text="{Binding Name}"
                               FontAttributes="Bold"
                               FontSize="Large"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                        <Image Source="{Binding ImageUrl}"
                               Aspect="AspectFill"
                               HeightRequest="150"
                               WidthRequest="150"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Location}"
                               HorizontalOptions="Center" />
                        <Label Text="{Binding Details}"
                               FontAttributes="Italic"
                               HorizontalOptions="Center"
                               MaxLines="5"
                               LineBreakMode="TailTruncation" />
                    </StackLayout>
                </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    Label nameLabel = new Label { ... };
    nameLabel.SetBinding(Label.TextProperty, "Name");

    Image image = new Image { ... };
    image.SetBinding(Image.SourceProperty, "ImageUrl");

    Label locationLabel = new Label { ... };
    locationLabel.SetBinding(Label.TextProperty, "Location");

    Label detailsLabel = new Label { ... };
    detailsLabel.SetBinding(Label.TextProperty, "Details");

    StackLayout stackLayout = new StackLayout
    {
        Children = { nameLabel, image, locationLabel, detailsLabel }
    };

    Frame frame = new Frame { ... };
    StackLayout rootStackLayout = new StackLayout
    {
        Children = { frame }
    };

    return rootStackLayout;
});
```

在中指定的 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 專案會在中定義每個專案的外觀 `CarouselView` 。 在此範例中，中的配置 `DataTemplate` 是由管理 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，而且資料會以 [`Image`](xref:Xamarin.Forms.Image) 物件和三個物件顯示，這些都系結 [`Label`](xref:Xamarin.Forms.Label) 至類別的屬性 `Monkey` ：

```csharp
public class Monkey
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

下列螢幕擷取畫面顯示範本化每個專案的結果：

[![CarouselView 的螢幕擷取畫面，其中每個專案都在 iOS 和 Android 上樣板化](populate-data-images/datatemplate.png "CarouselView 中的樣板化專案")](populate-data-images/datatemplate-large.png#lightbox "CarouselView 中的樣板化專案")

如需資料範本的詳細資訊，請參閱[ Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 由將屬性設定為物件，可在執行時間根據專案值選擇中的每個專案的外觀 [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) ：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CarouselViewDemos.Controls"
             x:Class="CarouselViewDemos.Views.HorizontalLayoutDataTemplateSelectorPage">
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

    <CarouselView ItemsSource="{Binding Monkeys}"
                  ItemTemplate="{StaticResource MonkeySelector}" />
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ItemTemplate = new MonkeyDataTemplateSelector { ... }
};
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定為 `MonkeyDataTemplateSelector` 物件。 下列範例顯示 `MonkeyDataTemplateSelector` 類別：

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

`MonkeyDataTemplateSelector`類別會定義 `AmericanMonkey` `OtherMonkey` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 設定為不同資料範本的和屬性。 `OnSelectTemplate` `AmericanMonkey` 當猴子名稱包含 "北美洲" 時，覆寫會傳回範本。 當猴子名稱不包含 "北美洲" 時，覆 `OnSelectTemplate` 寫會傳回 `OtherMonkey` 範本，顯示其資料呈現灰色：

[![IOS 和 Android 上 CarouselView 執行時間專案範本選取專案的螢幕擷取畫面](populate-data-images/datatemplateselector.png "CarouselView 中的執行時間專案範本選取專案")](populate-data-images/datatemplateselector-large.png#lightbox "CarouselView 中的執行時間專案範本選取專案")

如需資料範本選取器的詳細資訊，請參閱 [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用時 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，絕對不要將物件的根項目設定 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 為 `ViewCell` 。 這會導致擲回例外狀況，因為 `CarouselView` 沒有資料格的概念。

## <a name="display-indicators"></a>顯示指示器

指標，代表中的專案數和目前的位置 `CarouselView` ，可顯示在旁邊 `CarouselView` 。 您可以使用控制項來完成這項作業 `IndicatorView` ：

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

在此範例中， `IndicatorView` 會在下轉譯 `CarouselView` ，並在中顯示每個專案的指標 `CarouselView` 。 `IndicatorView`會將 `CarouselView.IndicatorView` 屬性設定為物件，以填入資料 `IndicatorView` 。 每個指標都是淺灰色圓圈，而表示中目前專案的指標 `CarouselView` 為暗灰色：

[![IOS 和 Android 上 CarouselView 和 IndicatorView 的螢幕擷取畫面](populate-data-images/indicators.png "IndicatorView 圓形")](populate-data-images/indicators-large.png#lightbox "IndicatorView 圓形")

> [!IMPORTANT]
> 設定屬性會導致屬性系結 `CarouselView.IndicatorView` `IndicatorView.Position` 至屬性 `CarouselView.Position` ，並將屬性系結 `IndicatorView.ItemsSource` 至屬性 `CarouselView.ItemsSource` 。

如需指標的詳細資訊，請參閱[ Xamarin.Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)。

## <a name="context-menus"></a>操作功能表

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 支援透過進行資料項目目的內容功能表 `SwipeView` ，這會顯示具有滑動手勢的內容功能表。 `SwipeView`是包裝內容專案的容器控制項，並且提供該內容專案的內容功能表項目。 因此，會藉 `CarouselView` 由建立 `SwipeView` 定義換行內容的，以及滑動手勢所顯示的操作功能表項目，來為建立快顯功能表 `SwipeView` 。 這是藉由將加入 `SwipeView` 至 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，定義中每個資料項目目的外觀來達成 `CarouselView` ：

```xaml
<CarouselView x:Name="carouselView"
              ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemTemplate>
        <DataTemplate>
            <StackLayout>
                    <Frame HasShadow="True"
                           BorderColor="DarkGray"
                           CornerRadius="5"
                           Margin="20"
                           HeightRequest="300"
                           HorizontalOptions="Center"
                           VerticalOptions="CenterAndExpand">
                        <SwipeView>
                            <SwipeView.TopItems>
                                <SwipeItems>
                                    <SwipeItem Text="Favorite"
                                               IconImageSource="favorite.png"
                                               BackgroundColor="LightGreen"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.FavoriteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.TopItems>
                            <SwipeView.BottomItems>
                                <SwipeItems>
                                    <SwipeItem Text="Delete"
                                               IconImageSource="delete.png"
                                               BackgroundColor="LightPink"
                                               Command="{Binding Source={x:Reference carouselView}, Path=BindingContext.DeleteCommand}"
                                               CommandParameter="{Binding}" />
                                </SwipeItems>
                            </SwipeView.BottomItems>
                            <StackLayout>
                                <!-- Define item appearance -->
                            </StackLayout>
                        </SwipeView>
                    </Frame>
            </StackLayout>
        </DataTemplate>
    </CarouselView.ItemTemplate>
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");

carouselView.ItemTemplate = new DataTemplate(() =>
{
    StackLayout stackLayout = new StackLayout();
    Frame frame = new Frame { ... };

    SwipeView swipeView = new SwipeView();
    SwipeItem favoriteSwipeItem = new SwipeItem
    {
        Text = "Favorite",
        IconImageSource = "favorite.png",
        BackgroundColor = Color.LightGreen
    };
    favoriteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.FavoriteCommand", source: carouselView));
    favoriteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    SwipeItem deleteSwipeItem = new SwipeItem
    {
        Text = "Delete",
        IconImageSource = "delete.png",
        BackgroundColor = Color.LightPink
    };
    deleteSwipeItem.SetBinding(MenuItem.CommandProperty, new Binding("BindingContext.DeleteCommand", source: carouselView));
    deleteSwipeItem.SetBinding(MenuItem.CommandParameterProperty, ".");

    swipeView.TopItems = new SwipeItems { favoriteSwipeItem };
    swipeView.BottomItems = new SwipeItems { deleteSwipeItem };

    StackLayout swipeViewStackLayout = new StackLayout { ... };
    swipeView.Content = swipeViewStackLayout;
    frame.Content = swipeView;
    stackLayout.Children.Add(frame);

    return stackLayout;
});
```

在此範例中， `SwipeView` 內容是 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，定義中的每個專案的外觀 [`Frame`](xref:Xamarin.Forms.Frame) [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 滑動專案是用來在內容上執行動作 `SwipeView` ，並且會在從頂端和底部撥動控制項時顯示：

[![螢幕擷取畫面： iOS 和 Android 上的 CarouselView 下方內容功能表項目](populate-data-images/swipeview-bottom.png "具有底部 SwipeView 內容功能表項目的 CarouselView")](populate-data-images/swipeview-bottom-large.png#lightbox "具有底部 SwipeView 內容功能表項目的 CarouselView") 
[ ![IOS 和 Android 上 CarouselView 頂端功能表項目的螢幕擷取畫面](populate-data-images/swipeview-top.png "具有 top SwipeView 內容功能表項目的 CarouselView")](populate-data-images/swipeview-top-large.png#lightbox "具有 top SwipeView 內容功能表項目的 CarouselView")

`SwipeView` 支援四種不同的滑動方向，並以物件加入的方向集合來定義滑動方向 `SwipeItems` `SwipeItems` 。 根據預設，當使用者按下該專案時，會執行滑動專案。 此外，一旦執行滑動專案，就會隱藏滑動專案並 `SwipeView` 重新顯示內容。 不過，這些行為可以變更。

如需控制項的詳細資訊 `SwipeView` ，請參閱[ Xamarin.Forms SwipeView](~/xamarin-forms/user-interface/swipeview.md)。

## <a name="pull-to-refresh"></a>拖動以重新整理

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 支援透過進行提取以重新整理功能 `RefreshView` ，藉由在專案上拉下，讓顯示的資料重新整理。 是容器控制項，可提供將重新整理 `RefreshView` 功能提供給其子系的功能，前提是子系支援可滾動的內容。 因此，藉 `CarouselView` 由將其設定為的子系，以針對來執行的提取重新整理 `RefreshView` ：

```xaml
<RefreshView IsRefreshing="{Binding IsRefreshing}"
             Command="{Binding RefreshCommand}">
    <CarouselView ItemsSource="{Binding Animals}">
        ...
    </CarouselView>
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

CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
refreshView.Content = carouselView;
// ...
```

當使用者起始重新整理時， `ICommand` `Command` 會執行由屬性定義的，這應該會重新整理所顯示的專案。 重新整理時，會顯示重新整理視覺效果，這是由動畫的進度圓形所組成：

[![在 iOS 和 Android 上 CarouselView 拉重新整理的螢幕擷取畫面](populate-data-images/pull-to-refresh.png "CarouselView 提取以重新整理")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView 提取以重新整理")

屬性的值 `RefreshView.IsRefreshing` 表示的目前狀態 `RefreshView` 。 當使用者觸發重新整理時，這個屬性會自動轉換為 `true` 。 重新整理完成之後，您應該將屬性重設為 `false` 。

如需的詳細資訊 `RefreshView` ，請參閱[ Xamarin.Forms refreshview 拖動](~/xamarin-forms/user-interface/refreshview.md)。

## <a name="load-data-incrementally"></a>以累加方式載入資料

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 在使用者滾動時支援累加式資料虛擬化。 這可讓您在使用者滾動時，以非同步方式從 web 服務載入資料頁面。 此外，載入更多資料的點是可設定的，讓使用者看不到空格，或停止滾動。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義下列屬性來控制資料的累加式載入：

- `RemainingItemsThreshold`，類型為 `int` ，在事件將引發的清單中尚未顯示的專案閾值 `RemainingItemsThresholdReached` 。
- `RemainingItemsThresholdReachedCommand`，類型為 `ICommand` ，當到達時，即會執行 `RemainingItemsThreshold` 。
- `RemainingItemsThresholdReachedCommandParameter`，屬於 `object` 類型，這是傳遞至 `RemainingItemsThresholdReachedCommand` 的參數。

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 此外，也 `RemainingItemsThresholdReached` 會定義當的滾動時間太 `CarouselView` 遠而 `RemainingItemsThreshold` 無法顯示專案時所引發的事件。 您可以處理這個事件來載入更多專案。 此外，當 `RemainingItemsThresholdReached` 引發事件時， `RemainingItemsThresholdReachedCommand` 會執行，以便在 viewmodel 中進行增量資料載入。

屬性的預設值 `RemainingItemsThreshold` 為-1，表示 `RemainingItemsThresholdReached` 永遠不會引發事件。 當屬性值為0時， `RemainingItemsThresholdReached` 當中的最後一個專案顯示時，就會引發此事件 [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) 。 針對大於0的值， `RemainingItemsThresholdReached` 當 `ItemsSource` 包含尚未滾動至的專案數目時，就會引發此事件。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) 驗證 `RemainingItemsThreshold` 屬性，使其值一律大於或等於-1。

下列 XAML 範例顯示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會以累加方式載入資料的：

```xaml
<CarouselView ItemsSource="{Binding Animals}"
              RemainingItemsThreshold="2"
              RemainingItemsThresholdReached="OnCarouselViewRemainingItemsThresholdReached"
              RemainingItemsThresholdReachedCommand="{Binding LoadMoreDataCommand}">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    RemainingItemsThreshold = 2
};
carouselView.RemainingItemsThresholdReached += OnCollectionViewRemainingItemsThresholdReached;
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
```

在此程式碼範例中， `RemainingItemsThresholdReached` 事件會在有2個專案尚未滾動時引發，而回應會執行 `OnCollectionViewRemainingItemsThresholdReached` 事件處理常式：

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 您也可以藉由將系結 `RemainingItemsThresholdReachedCommand` 至 viewmodel 中的實，以累加方式載入資料 `ICommand` 。

## <a name="related-links"></a>相關連結

- [CarouselView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin.Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Xamarin.Forms Refreshview 拖動](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.Forms 資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin.Forms DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
