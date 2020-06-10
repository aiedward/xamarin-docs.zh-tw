---
title： " Xamarin.Forms CarouselView Layout" 描述： "根據預設，CarouselView 會以水準方式顯示其專案。 不過，也可以使用垂直方向。」
assetid： fede0382-c972-4023-a4ea-fe5cadec91a6 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：01/28/2020 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview-layout"></a>Xamarin.FormsCarouselView 版面配置

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義下列控制配置的屬性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)，屬於類型 `LinearItemsLayout` ，可指定要使用的版面配置。
- `PeekAreaInsets`，屬於類型 [`Thickness`](xref:Xamarin.Forms.Thickness) ，指定要讓相鄰專案部分可見的程度。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會以水準方向顯示其專案。 螢幕上會顯示單一專案，而滑動手勢會導致向前和向後流覽專案的集合。 不過，垂直方向也是可行的。 這是因為 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 屬性的類型是 `LinearItemsLayout` ，它繼承自 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 類別。 `ItemsLayout`類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)，屬於類型 [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) ，可指定要在其中 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 加入做為專案展開的方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)，屬於類型 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) ，指定對齊點與專案對齊的方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)，屬於類型 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) ，會在滾動時指定貼齊點的行為。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。 如需有關貼齊點的詳細資訊，請參閱[ Xamarin.Forms CollectionView 滾動](scrolling.md)指南中的[貼齊點](scrolling.md#snap-points)。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員：

- `Vertical`表示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 將會在加入專案時垂直展開。
- `Horizontal`表示 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 將會在加入專案時，以水準方式展開。

`LinearItemsLayout`類別繼承自 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 類別，並定義 `ItemSpacing` 類型為的屬性 `double` ，代表每個專案周圍的空白空間。 這個屬性的預設值為0，且其值必須一律大於或等於0。 `LinearItemsLayout`類別也會定義靜態 `Vertical` 和 `Horizontal` 成員。 這些成員可以分別用來建立垂直或水準清單。 或者，也 `LinearItemsLayout` 可以建立物件， [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 並指定列舉成員做為引數。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)使用原生版面配置引擎來執行版面配置。

## <a name="horizontal-layout"></a>水平的版面配置

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會以水準方式顯示其專案。 因此，不需要將 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為使用此配置：

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

或者，也可以藉由將 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件，將 `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為 `Orientation` 屬性值，來完成此配置：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

這會導致在新增新專案時水準成長的版面配置：

[![IOS 和 Android 上的 CarouselView 水準版面配置螢幕擷取畫面](layout-images/horizontal.png "CarouselView 水準版面配置")](layout-images/horizontal-large.png#lightbox "CarouselView 水準版面配置")

## <a name="vertical-layout"></a>垂直版面配置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以將 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件，並將 `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為 `Orientation` 屬性值，以垂直方式顯示其專案：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CarouselView.ItemsLayout>
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
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

這會導致在新增新專案時，垂直成長的版面配置：

[![在 iOS 和 Android 上 CarouselView 垂直版面配置的螢幕擷取畫面](layout-images/vertical.png "CarouselView 垂直版面配置")](layout-images/vertical-large.png#lightbox "CarouselView 垂直版面配置")

## <a name="partially-visible-adjacent-items"></a>部分可見的相鄰專案

根據預設，會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 一次顯示完整的專案。 不過，您可以將 `PeekAreaInsets` 屬性設定為 `Thickness` 值，指定要讓相鄰專案部分可見的程度，藉此變更此行為。 這對於向使用者指出有其他專案可供查看時非常有用。 下列 XAML 顯示設定此屬性的範例：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    PeekAreaInsets = new Thickness(100)
};
```

結果是連續的專案會在螢幕上部分公開：

[![IOS 和 Android 上部分可見的相鄰專案之 CollectionView 的螢幕擷取畫面](layout-images/peek-items.png "CarouselView 查看區域內凹")](layout-images/peek-items-large.png#lightbox "CarouselView 尖峰區域內凹")

## <a name="item-spacing"></a>專案間距

根據預設，中的每個專案之間沒有空格 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 藉由在所使用的專案配置上設定屬性，即可變更此行為 `ItemSpacing` `CarouselView` 。

當將 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 其 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件時，可以將 `LinearItemsLayout.ItemSpacing` 屬性設定為 `double` 代表專案之間間距的值：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

> [!NOTE]
> `LinearItemsLayout.ItemSpacing`屬性具有驗證回呼集，可確保屬性的值一律大於或等於0。

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

這段程式碼會產生垂直版面配置，專案之間的間距為20。

## <a name="dynamic-resizing-of-items"></a>動態調整專案大小

中的專案 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以在執行時間以動態方式調整大小，方法是在中變更元素的版面配置相關屬性 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 例如，下列程式碼範例會變更 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 物件的和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性 [`Image`](xref:Xamarin.Forms.Image) ，以及 `HeightRequest` 其父系的屬性 [`Frame`](xref:Xamarin.Forms.Frame) ：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

`OnImageTapped`系統會執行事件處理常式，以回應 [`Image`](xref:Xamarin.Forms.Image) 所按下的物件，並變更影像的維度（及其父系 `Frame` ），以便更輕鬆地查看：

[![在 iOS 和 Android 上具有動態專案大小的 CarouselView 螢幕擷取畫面](layout-images/runtime-resizing.png "CarouselView 動態專案大小")](layout-images/runtime-resizing-large.png#lightbox "CarouselView 動態專案大小")

## <a name="right-to-left-layout"></a>由右至左的版面配置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以將其內容設定為，以由右至左的流程方向來配置其內容 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 。 不過，在 `FlowDirection` 理想的情況下，屬性應該設定在頁面或根配置上，這會導致頁面或根配置中的所有元素回應流程方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CarouselViewDemos.Views.HorizontalTemplateLayoutRTLPage"
             Title="Horizontal layout (RTL FlowDirection)"
             FlowDirection="RightToLeft">    
    <CarouselView ItemsSource="{Binding Monkeys}">
        ...
    </CarouselView>
</ContentPage>
```

具有父系之元素的預設值 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) 為 [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) 。 因此，會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 從繼承 `FlowDirection` 屬性值 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

如需流程方向的詳細資訊，請參閱由[右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.FormsCarouselView 滾動](scrolling.md)
