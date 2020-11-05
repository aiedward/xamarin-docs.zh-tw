---
title: Xamarin.Forms CarouselView 版面配置
description: 根據預設，CarouselView 會以水準方式顯示其專案。 不過，您也可以使用垂直方向。
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1c9a3144c3b1458592269fb2814360c8dcab6d9e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373376"
---
# <a name="no-locxamarinforms-carouselview-layout"></a>Xamarin.Forms CarouselView 版面配置

![發行前 API](~/media/shared/preview.png)

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 定義下列控制項配置的屬性：

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout)型別為的，可 `LinearItemsLayout` 指定要使用的版面配置。
- `PeekAreaInsets`型別為的 [`Thickness`](xref:Xamarin.Forms.Thickness) ，可指定要將相鄰專案部分可見的程度。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。

依預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會以水準方向顯示其專案。 畫面上將會顯示單一專案，其中的滑動手勢會導致向前和向後流覽專案集合。 不過，您也可以使用垂直方向。 這是因為此 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 屬性是 `LinearItemsLayout` 繼承自類別的型別 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 。 `ItemsLayout`類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation)，類型為 [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) ，指定要在其中 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 加入專案的展開方向。
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment)型別為的 [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) ，可指定對齊點與專案的對齊方式。
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType)型別為的 [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) ，指定滾動時對齊點的行為。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結的目標。 如需貼齊點的詳細資訊，請參閱[ Xamarin.Forms CollectionView 滾動](scrolling.md)指南中的[貼齊點](scrolling.md#snap-points)。

[`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation)列舉會定義下列成員：

- `Vertical` 指出 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 當加入專案時，將會垂直展開。
- `Horizontal` 指出 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 將會在新增專案時水準展開。

`LinearItemsLayout`類別繼承自 [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) 類別，並定義型別為 `ItemSpacing` 的屬性， `double` 代表每個專案周圍的空白空間。 這個屬性的預設值為0，而且其值必須一律大於或等於0。 `LinearItemsLayout`類別也會定義靜態 `Vertical` 和 `Horizontal` 成員。 這些成員可以分別用來建立垂直或水準清單。 或者，您也 `LinearItemsLayout` 可以建立物件，並將 [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為引數。

> [!NOTE]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView) 使用原生版面配置引擎來執行版面配置。

## <a name="horizontal-layout"></a>水平的版面配置

依預設， [`CarouselView`](xref:Xamarin.Forms.CarouselView) 會以水準方式顯示其專案。 因此，您不需要將 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 此屬性設定為使用此版面配置：

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

或者，您也可以將 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件，並將 `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為 `Orientation` 屬性值，藉此完成此配置：

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

這會導致在新增專案時水準成長的版面配置：

[![螢幕擷取畫面： iOS 和 Android 上的 CarouselView 水準版面配置](layout-images/horizontal.png "CarouselView 水準版面配置")](layout-images/horizontal-large.png#lightbox "CarouselView 水準版面配置")

## <a name="vertical-layout"></a>垂直版面配置

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以將 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件，並將 `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) 列舉成員指定為 `Orientation` 屬性值，以垂直方式顯示其專案：

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

這會導致在新增專案時，垂直成長的版面配置：

[![螢幕擷取畫面： iOS 和 Android 上的 CarouselView 垂直版面配置](layout-images/vertical.png "CarouselView 垂直版面配置")](layout-images/vertical-large.png#lightbox "CarouselView 垂直版面配置")

## <a name="partially-visible-adjacent-items"></a>部分可見的相鄰專案

預設會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 一次顯示完整專案。 不過，您可以藉由將屬性設定為值來變更此行為，以 `PeekAreaInsets` `Thickness` 指定讓相鄰專案的部分可見的程度。 這有助於向使用者指出有其他專案要查看。 下列 XAML 顯示設定這個屬性的範例：

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

[![螢幕擷取畫面：在 iOS 和 Android 上，有部分可見的相鄰專案的 CollectionView](layout-images/peek-items.png "CarouselView 查看區域內凹")](layout-images/peek-items-large.png#lightbox "CarouselView 尖峰區域內凹")

## <a name="item-spacing"></a>專案間距

依預設，中的每個專案之間都沒有空格 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。 您可以藉由在使用的專案配置上設定屬性，來變更這個行為 `ItemSpacing` `CarouselView` 。

當將 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 其 [`ItemsLayout`](xref:Xamarin.Forms.CarouselView.ItemsLayout) 屬性設定為 `LinearItemsLayout` 物件時，可以將 `LinearItemsLayout.ItemSpacing` 屬性設定為 `double` 代表專案之間空間的值：

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
> `LinearItemsLayout.ItemSpacing`屬性具有驗證回撥設定，可確保屬性的值一律大於或等於0。

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

這段程式碼會產生垂直配置，在專案之間具有20個間距。

## <a name="dynamic-resizing-of-items"></a>動態調整專案大小

中的專案 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以在執行時間動態調整大小，方法是變更中專案的版面配置相關屬性 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 。 例如，下列程式碼範例會變更 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 物件的和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性 [`Image`](xref:Xamarin.Forms.Image) ，以及 `HeightRequest` 其父系的屬性 [`Frame`](xref:Xamarin.Forms.Frame) ：

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(150) ? 200 : 150;
    Frame frame = ((Frame)image.Parent.Parent);
    frame.HeightRequest = frame.HeightRequest.Equals(300) ? 350 : 300;
}
```

`OnImageTapped`系統會執行事件處理常式來回應 [`Image`](xref:Xamarin.Forms.Image) 正在進行點擊的物件，並變更影像 (和其父) 的維度 `Frame` ，以便更輕鬆地查看：

[![螢幕擷取畫面： iOS 和 Android 上的動態專案大小調整 CarouselView](layout-images/runtime-resizing.png "CarouselView 動態專案大小調整")](layout-images/runtime-resizing-large.png#lightbox "CarouselView 動態專案大小調整")

## <a name="right-to-left-layout"></a>由右至左的版面配置

[`CarouselView`](xref:Xamarin.Forms.CarouselView) 可以藉由將其屬性設定為，以由右至左的流程方向，將其內容版面配置 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) 。 不過，在理想的情況下，您 `FlowDirection` 應該在頁面或根配置上設定屬性，使頁面中的所有專案或根配置都能回應流程方向：

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

父元素的預設值為 [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent) 。 因此，會 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 繼承的 `FlowDirection` 屬性值 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 。

如需流程方向的詳細資訊，請參閱由 [右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="related-links"></a>相關連結

- [CarouselView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
- [從右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Xamarin.Forms CarouselView 滾動](scrolling.md)