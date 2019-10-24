---
title: Xamarin. Forms CarouselView 版面配置
description: 根據預設，CarouselView 會以水準方式顯示其專案。 不過，垂直方向也是可行的。
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: 051bbc1732dc1b074d27080f74621a57a80aaaa4
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749813"
---
# <a name="xamarinforms-carouselview-layout"></a>Xamarin. Forms CarouselView 版面配置

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)定義下列控制配置的屬性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)，屬於 `LinearItemsLayout` 類型，可指定要使用的版面配置。
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy)，屬於[`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy)類型，會指定要使用的專案量值策略。
- `NumberOfSideItems`，屬於 `int` 類型，這是與目前專案相鄰之可見專案的數目。 預設值為 0。
- `PeekAreaInsets`，屬於[`Thickness`](xref:Xamarin.Forms.Thickness)類型，可指定要讓相鄰專案部分可見的程度。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會以水準方向顯示其專案。 螢幕上會顯示單一專案，而滑動手勢會導致向前和向後流覽專案的集合。 不過，垂直方向也是可行的。 這是因為[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)屬性的類型是 `LinearItemsLayout`，它會繼承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別。 @No__t_0 類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)，屬於[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)類型，會指定[`CarouselView`](xref:Xamarin.Forms.CarouselView)在加入專案時展開的方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)，屬於[`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment)類型，可指定對齊點與專案對齊的方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)，屬於[`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType)類型，會在滾動時指定貼齊點的行為。

這些屬性是以[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件為後盾，也就是說，這些屬性可以是資料系結的目標。 如需有關貼齊點的詳細資訊，請參閱[CollectionView 滾動](scrolling.md)指南中的[貼齊點](scrolling.md#snap-points)。

[@No__t_1](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員：

- `Vertical` 表示在加入專案時， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會以垂直方式展開。
- `Horizontal` 表示[`CarouselView`](xref:Xamarin.Forms.CarouselView)會在加入專案時以水準方式展開。

@No__t_0 類別會繼承自[`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)類別，並定義類型 `double` 的 `ItemSpacing` 屬性，代表每個專案周圍的空白空間。 這個屬性的預設值為0，且其值必須一律大於或等於0。 @No__t_0 類別也會定義靜態 `Vertical` 和 `Horizontal` 成員。 這些成員可以分別用來建立垂直或水準清單。 或者，也可以建立 `LinearItemsLayout` 物件，指定[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員做為引數。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)會使用原生版面配置引擎來執行版面配置。

## <a name="horizontal-layout"></a>水準配置

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會水準顯示其專案。 因此，不需要將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為使用此配置：

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

或者，也可以藉由將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為 `LinearItemsLayout` 物件，將 `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員指定為 `Orientation` 屬性值，來完成此配置：

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

這會導致在新增新專案時水準成長的版面配置。

## <a name="vertical-layout"></a>垂直版面配置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以將[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為 `LinearItemsLayout` 物件，並將 `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉成員指定為 `Orientation` 屬性值，以垂直方式顯示其專案：

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

這會導致在加入新專案時，垂直成長的版面配置。

## <a name="partially-visible-adjacent-items"></a>部分可見的相鄰專案

[@No__t_1](xref:Xamarin.Forms.CarouselView)預設會一次顯示完整的專案。 不過，您可以將 `PeekAreaInsets` 屬性設定為 `Thickness` 值，指定要讓相鄰專案部分可見的程度，藉此變更此行為。 這對於向使用者指出有其他專案可供查看時非常有用。 下列 XAML 顯示設定此屬性的範例：

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

結果是連續的專案會在螢幕上部分公開。

## <a name="fully-visible-adjacent-items"></a>完全可見的相鄰專案

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會一次顯示一個專案。 不過，您可以將 `NumberOfSideItems` 屬性設定為整數，以表示要顯示在目前專案旁邊的專案數，藉此變更此行為。 下列 XAML 顯示設定此屬性的範例：

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
    ...
</CarouselView>
```

對等的 C# 程式碼為：

```csharp
CarouselView carouselView = new CarouselView
{
    ...
    NumberOfSideItems = 1
};
```

這個範例會導致在目前專案的每一端顯示一個連續的專案。

> [!NOTE]
> 設定 `NumberOfSideItems` 屬性時，仍然會套用任何 `PeekAreaInsets` 值。

## <a name="item-spacing"></a>專案間距

根據預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView)中的每個專案在其周圍不會有任何空白空間。 藉由設定 `CarouselView` 所使用之專案配置的屬性，即可變更此行為。

當[`CarouselView`](xref:Xamarin.Forms.CarouselView)將其[`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout)屬性設定為 `LinearItemsLayout` 物件時，`LinearItemsLayout.ItemSpacing` 屬性可以設定為 `double` 值，表示每個專案周圍的空白空間：

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
> @No__t_0 屬性具有驗證回呼集，可確保屬性的值一律大於或等於0。

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

這段程式碼會產生垂直版面配置，每個專案周圍的間距為20。

## <a name="dynamic-resizing-of-items"></a>動態調整專案大小

[@No__t_1](xref:Xamarin.Forms.CarouselView)中的專案可以在執行時間以動態方式調整大小，方法是變更[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)內元素的版面配置相關屬性。 例如，下列程式碼範例會變更[`Image`](xref:Xamarin.Forms.Image)物件的[`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest)和[`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest)屬性，以及其父系[`Frame`](xref:Xamarin.Forms.Frame)的 `HeightRequest` 屬性：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

系統會執行 `OnImageTapped` 事件處理常式，以回應所按下的[`Image`](xref:Xamarin.Forms.Image)物件，並變更影像的維度（和其父框架），使其更容易被查看。

## <a name="right-to-left-layout"></a>由右至左的版面配置

[`CarouselView`](xref:Xamarin.Forms.CarouselView)可以藉由將其 [ [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) ] 屬性設定為 [ [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)]，以由右至左的流程方向來配置其內容。 不過，`FlowDirection` 屬性最好是在頁面或根配置上設定，使頁面或根配置中的所有專案都能回應流程方向：

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

具有父系之元素的預設[`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection)為[`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)。 因此， [`CarouselView`](xref:Xamarin.Forms.CarouselView)會從[`ContentPage`](xref:Xamarin.Forms.ContentPage)繼承 `FlowDirection` 屬性值。

如需流程方向的詳細資訊，請參閱由[右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CarouselView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [由右至左當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin. 表單 CarouselView 滾動](scrolling.md)
