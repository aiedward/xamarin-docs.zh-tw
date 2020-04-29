---
title: Xamarin. 表單 CarouselView 資料
description: CarouselView 會藉由將其 ItemsSource 屬性設定為任何可執行 IEnumerable 的集合來填入資料。
ms.prod: xamarin
ms.assetid: 20DB2C57-CE3A-4D91-80DC-73AE361A3CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/29/2020
ms.openlocfilehash: cdd77d333ead9b4ff4d2cf29b1e36ee2f287dd22
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82517359"
---
# <a name="xamarinforms-carouselview-data"></a>Xamarin. 表單 CarouselView 資料

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)包含下列屬性，可定義要顯示的資料及其外觀：

- [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)，屬於類型`IEnumerable`，可指定要顯示的專案集合，並具有的預設值`null`。
- [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)，屬於類型[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)，可指定要套用至要顯示的專案集合中每個專案的範本。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示屬性可以是資料系結的目標。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)定義`ItemsUpdatingScrollMode`屬性，代表將`CarouselView`新專案加入其中時的滾動行為。 如需這個屬性的詳細資訊，請參閱[在新增新專案時控制捲軸位置](scrolling.md#control-scroll-position-when-new-items-are-added)。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)在使用者滾動時支援累加式資料虛擬化。 如需詳細資訊，請參閱[以累加方式載入資料](#load-data-incrementally)。

## <a name="populate-a-carouselview-with-data"></a>在 CarouselView 中填入資料

會[`CarouselView`](xref:Xamarin.Forms.CarouselView)藉由將資料的[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性設定為任何可執行檔集合`IEnumerable`，來填入資料。 藉由從字串陣列初始化`ItemsSource`屬性，可以在 XAML 中加入專案：

```xaml
<CarouselView>
    <CarouselView.ItemsSource>
        <x:Array Type="{x:Type x:String}">
            <x:String>Baboon</x:String>
            <x:String>Capuchin Monkey</x:String>
            <x:String>Blue Monkey</x:String>
            <x:String>Squirrel Monkey</x:String>
            <x:String>Golden Lion Tamarin</x:String>
            <x:String>Howler Monkey</x:String>
            <x:String>Japanese Macaque</x:String>
        </x:Array>
    </CarouselView.ItemsSource>
</CarouselView>
```

> [!NOTE]
> 請注意，`x:Array` 項目需要 `Type` 屬性，以指出陣列中的項目類型。

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.ItemsSource = new string[]
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

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)如果在基礎集合中加入、移除或變更專案時需要重新整理，基礎集合應該是傳送屬性變更通知的`IEnumerable`集合，例如`ObservableCollection`。

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會水準顯示專案。 下列螢幕擷取畫面顯示在`CarouselView` IOS 和 Android 上顯示不同的字串專案：

[![IOS 和 Android 上包含文字專案之 CarouselView 的螢幕擷取畫面](populate-data-images/text.png "CarouselView 中的文字專案")](populate-data-images/text-large.png#lightbox "CarouselView 中的文字專案")

如需如何變更方向的[`CarouselView`](xref:Xamarin.Forms.CarouselView)詳細資訊，請參閱[CarouselView 版面](layout.md)配置。 如需如何在中定義每個專案外觀的詳細資訊`CarouselView`，請參閱[定義專案外觀](#define-item-appearance)。

### <a name="data-binding"></a>資料繫結

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以使用資料系結將其[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性系結至`IEnumerable`集合，以填入資料。 在 XAML 中，這是透過`Binding`標記延伸來達成：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}" />
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView();
carouselView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

在此範例中， [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)屬性資料會系結`Monkeys`至已連接之 viewmodel 的屬性。

> [!NOTE]
> 可以啟用編譯的系結，以改善 Xamarin 中的資料系結效能。 如需詳細資訊，請參閱[編譯繫結](~/xamarin-forms/app-fundamentals/data-binding/compiled-bindings.md)。

如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

## <a name="define-item-appearance"></a>定義專案外觀

將[`CarouselView`](xref:Xamarin.Forms.CarouselView) [`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定為，即可定義中每個專案的外觀[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)：

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

在中指定的元素[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)會定義中每個專案的外觀`CarouselView`。 在範例中，中`DataTemplate`的配置是由所管理[`StackLayout`](xref:Xamarin.Forms.StackLayout)，而且資料會以[`Image`](xref:Xamarin.Forms.Image)物件和三個[`Label`](xref:Xamarin.Forms.Label)物件顯示，全都系結至`Monkey`類別的屬性：

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

[![CarouselView 的螢幕擷取畫面，其中每個專案都是樣板化，在 iOS 和 Android 上](populate-data-images/datatemplate.png "CarouselView 中的樣板化專案")](populate-data-images/datatemplate-large.png#lightbox "CarouselView 中的樣板化專案")

如需資料範本的詳細資訊，請參閱 [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)。

## <a name="choose-item-appearance-at-runtime"></a>在執行時間選擇專案外觀

藉由將[`CarouselView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性設定為[`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector)物件[`CarouselView`](xref:Xamarin.Forms.CarouselView) ，可在執行時間根據專案值選擇中每個專案的外觀：

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

[`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate)屬性會設定為`MonkeyDataTemplateSelector`物件。 下列範例顯示`MonkeyDataTemplateSelector`類別：

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

`MonkeyDataTemplateSelector`類別會定義`AmericanMonkey`設定`OtherMonkey`為不同資料範本的和[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)屬性。 當`OnSelectTemplate`猴子名稱包含`AmericanMonkey` "北美洲" 時，覆寫會傳回範本。 當猴子名稱不包含 "北美洲" 時，覆`OnSelectTemplate`寫會傳回`OtherMonkey`範本，其會將其資料顯示為灰色：

[![在 iOS 和 Android 上 CarouselView 執行時間專案範本選擇的螢幕擷取畫面](populate-data-images/datatemplateselector.png "CarouselView 中的執行時間專案範本選擇")](populate-data-images/datatemplateselector-large.png#lightbox "CarouselView 中的執行時間專案範本選擇")

如需資料範本選取器的詳細資訊，請參閱[建立 Xamarin DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)。

> [!IMPORTANT]
> 使用[`CarouselView`](xref:Xamarin.Forms.CarouselView)時，絕對不會將[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)物件的根項目設定為`ViewCell`。 這會導致擲回例外狀況，因為`CarouselView`沒有資料格的概念。

## <a name="display-indicators"></a>顯示指示器

表示中`CarouselView`的專案數和目前位置的指標，可以顯示在旁邊`CarouselView`。 這可以透過`IndicatorView`控制項來完成：

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

在此範例中， `IndicatorView`會在底下呈現`CarouselView`，其中每個專案都有一個指標`CarouselView`。 `IndicatorView`會藉由將`CarouselView.IndicatorView`屬性設定為`IndicatorView`物件，來填入資料。 每個指標都是淺灰色圓形，而表示中目前專案的指標`CarouselView`是暗灰色：

[![CarouselView 和 IndicatorView （在 iOS 和 Android 上）的螢幕擷取畫面](populate-data-images/indicators.png "IndicatorView 圓形")](populate-data-images/indicators-large.png#lightbox "IndicatorView 圓形")

> [!IMPORTANT]
> `CarouselView.IndicatorView`設定屬性會導致`IndicatorView.Position`屬性系結至`CarouselView.Position`屬性，並將`IndicatorView.ItemsSource`屬性系結至`CarouselView.ItemsSource`屬性。

如需指標的詳細資訊，請參閱[IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)。

## <a name="context-menus"></a>操作功能表

[`CarouselView`](xref:Xamarin.Forms.CarouselView)透過來支援資料項目目的內容功能表`SwipeView`，這會以滑動手勢顯示內容功能表。 `SwipeView`是一個容器控制項，它會包裝內容專案，並提供該內容專案的快顯功能表專案。 因此，會藉`CarouselView`由建立`SwipeView`定義要`SwipeView`包裝之內容的，以及由滑動手勢所顯示的操作功能表項目，來為執行的操作功能表。 將新增`SwipeView`至[`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ，以定義中每個資料項目目的外觀，即可達成此目的： `CarouselView`

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

在此範例中， `SwipeView`內容是[`StackLayout`](xref:Xamarin.Forms.StackLayout) ，它會定義[`Frame`](xref:Xamarin.Forms.Frame)中所環繞之每個專案的外觀。 [`CarouselView`](xref:Xamarin.Forms.CarouselView) [滑動] 專案是用來對`SwipeView`內容執行動作，並在控制項從頂端和底部撥動時顯示：

[![CarouselView 底端操作功能表項目的螢幕擷取畫面，位於](populate-data-images/swipeview-bottom.png "具有下方 SwipeView 內容功能表項目的 CarouselView")](populate-data-images/swipeview-bottom-large.png#lightbox "具有下方 SwipeView 內容功能表項目的 CarouselView")
ios 和 android 上[![CarouselView 頂端功能表項目的螢幕擷取畫面](populate-data-images/swipeview-top.png "具有 top SwipeView 內容功能表項目的 CarouselView")](populate-data-images/swipeview-top-large.png#lightbox "具有 top SwipeView 內容功能表項目的 CarouselView")

`SwipeView`支援四種不同的輕量方向，其中的滑動方向是由`SwipeItems` `SwipeItems`物件新增至的方向集合所定義。 根據預設，當使用者按下滑動專案時，就會執行它。 此外，一旦執行了滑動專案，就會隱藏滑動專案並重新顯示`SwipeView`內容。 不過，您可以變更這些行為。

如需控制項的`SwipeView`詳細資訊，請參閱[SwipeView](~/xamarin-forms/user-interface/swipeview.md)。

## <a name="pull-to-refresh"></a>拖動以重新整理

[`CarouselView`](xref:Xamarin.Forms.CarouselView)支援透過提取至重新整理功能`RefreshView`，可讓您藉由在專案上拉出來重新整理顯示的資料。 `RefreshView`是容器控制項，可提供將功能的提取重新整理到其子系，前提是子系支援可滾動的內容。 因此，藉`CarouselView`由將提取重新整理設定為的子系，來為重新`RefreshView`整理執行：

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

當使用者起始重新整理時，會`ICommand`執行由`Command`屬性定義的，這應該會重新整理所顯示的專案。 重新整理視覺效果會在進行重新整理時顯示，這是由動畫的進度圓形所組成：

[![IOS 和 Android 上的 CarouselView 提取重新整理的螢幕擷取畫面](populate-data-images/pull-to-refresh.png "CarouselView 的提取更新")](populate-data-images/pull-to-refresh-large.png#lightbox "CarouselView 的提取更新")

`RefreshView.IsRefreshing`屬性的值表示的目前狀態`RefreshView`。 當使用者觸發重新整理時，這個屬性會自動轉換為`true`。 重新整理完成後，您應該將屬性重設`false`為。

如需的詳細`RefreshView`資訊，請參閱[RefreshView](~/xamarin-forms/user-interface/refreshview.md)。

## <a name="load-data-incrementally"></a>以累加方式載入資料

[`CarouselView`](xref:Xamarin.Forms.CarouselView)在使用者滾動時支援累加式資料虛擬化。 這可讓您在使用者滾動時，從 web 服務以非同步方式載入資料頁等案例。 此外，您可以設定載入更多資料的時間點，讓使用者看不到空白空間，或停止滾動。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義下列屬性來控制資料的累加式載入：

- `RemainingItemsThreshold`，屬於類型`int`，這是在引發`RemainingItemsThresholdReached`事件的清單中尚未顯示的專案閾值。
- `RemainingItemsThresholdReachedCommand`，屬於類型`ICommand`，這是在到達時`RemainingItemsThreshold`執行的。
- `RemainingItemsThresholdReachedCommandParameter`，屬於 `object` 類型，這是傳遞至 `RemainingItemsThresholdReachedCommand` 的參數。

[`CarouselView`](xref:Xamarin.Forms.CarouselView)也會定義`RemainingItemsThresholdReached`當向上滾動到`CarouselView`尚未顯示專案的`RemainingItemsThreshold`程度時，所引發的事件。 您可以處理這個事件，以載入更多專案。 此外，當`RemainingItemsThresholdReached`事件引發時， `RemainingItemsThresholdReachedCommand`會執行，以便在 viewmodel 中進行累加式資料載入。

`RemainingItemsThreshold`屬性的預設值為-1，表示永遠不會引發`RemainingItemsThresholdReached`事件。 當屬性值為0時，當`RemainingItemsThresholdReached`中[`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource)的最後一個專案顯示時，就會引發事件。 對於大於0的值，當`RemainingItemsThresholdReached` `ItemsSource`包含尚未滾動到的專案數目時，就會引發事件。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)驗證`RemainingItemsThreshold`屬性，使其值一律大於或等於-1。

下列 XAML 範例顯示[`CarouselView`](xref:Xamarin.Forms.CarouselView) ，它會以累加方式載入資料：

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

在此程式碼範例中`RemainingItemsThresholdReached` ，當有2個專案尚未滾動到時，就會引發事件，而`OnCollectionViewRemainingItemsThresholdReached`在回應中會執行事件處理常式：

```csharp
void OnCollectionViewRemainingItemsThresholdReached(object sender, EventArgs e)
{
    // Retrieve more data here and add it to the CollectionView's ItemsSource collection.
}
```

> [!NOTE]
> 您也可以藉由將系結`RemainingItemsThresholdReachedCommand`至 viewmodel 中`ICommand`的實作為增量方式載入資料。

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [Xamarin. Forms IndicatorView](~/xamarin-forms/user-interface/indicatorview.md)
- [Xamarin. Forms RefreshView](~/xamarin-forms/user-interface/refreshview.md)
- [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms 資料範本](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [建立 Xamarin 表單 DataTemplateSelector](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
