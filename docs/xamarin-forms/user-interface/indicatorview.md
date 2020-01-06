---
title: Xamarin. Forms IndicatorView
description: IndicatorView 是一個控制項，它會在 CarouselView 中顯示表示專案數目和目前位置的指標。
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/17/2019
ms.openlocfilehash: 6b7845011470d83d8f2187e0227950c23e46d52d
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490399"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin. Forms IndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

`IndicatorView` 是一種控制項，它會在 `CarouselView`中顯示表示專案數目和目前位置的指標：

[![CarouselView 和 IndicatorView （在 iOS 和 Android 上）的螢幕擷取畫面](indicatorview-images/circles.png "IndicatorView 圓形")](indicatorview-images/circles-large.png#lightbox "IndicatorView 圓形")

在 iOS 和 Android 平臺上，Xamarin. Forms 4.4 中有提供 `IndicatorView`。 不過，它目前是實驗性，而且只能透過將下列程式程式碼新增至 iOS 上的 `AppDelegate` 類別，或在 Android 上的 `MainActivity` 類別中使用，然後再呼叫 `Forms.Init`：

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` 會定義下列屬性：

- `Count`，屬於 `int`類型的指標數目。
- `HideSingle`，屬於 `bool`類型，指出指標是否應在只有一個存在時隱藏。 預設值為 `true`。
- `IndicatorColor`，屬於 `Color`的型別，也就是指標的色彩。
- `IndicatorSize`，屬於 `double`的類型，也就是指標的大小。 預設值為6.0。
- `Layout<View>`類型的 `IndicatorLayout`，會定義用來呈現 `IndicatorView`的版面配置類別。 這個屬性是由 Xamarin 所設定，而且通常不需要由開發人員設定。
- `IndicatorTemplate`，屬於 `DataTemplate`類型，這是定義每個指標外觀的範本。
- `IndicatorsShape`，屬於 `IndicatorShape`類型，每個指標的形狀。
- `ItemsSource`，屬於 `IEnumerable`類型，這是將顯示指標的集合。 設定 [`ItemsSourceBy`] 屬性時，將會自動設定此屬性。
- `ItemsSourceBy`，屬於 `VisualElement`類型的 `CarouselView` 物件，用來顯示的指標。
- `MaximumVisible`，屬於 `int`類型，這是可見指標的最大數目。 預設值為 `int.MaxValue`。
- `Position`，屬於 `int`的類型，這是目前選取的指標索引。 這個屬性會使用 `TwoWay` 系結。 設定 [`ItemsSourceBy`] 屬性時，將會自動設定此屬性。
- `SelectedIndicatorColor`，屬於 `Color`類型的指標色彩，表示 `CarouselView`中目前的專案。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示它們可以是資料系結的目標，以及樣式化的。

## <a name="create-an-indicatorview"></a>建立 IndicatorView

下列範例顯示如何在 XAML 中具現化 `IndicatorView`：

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

在此範例中，`IndicatorView` 會在 `CarouselView`之下轉譯，而 `CarouselView`中的每個專案都有一個指標。 `IndicatorView` 會藉由將 `ItemsSourceBy` 屬性設定為 `CarouselView` 物件來填入資料。 每個指標都是淺灰色圓圈，而表示 `CarouselView` 中目前專案的指標是暗灰色。

> [!IMPORTANT]
> 設定 `ItemsSourceBy` 屬性會導致 `Position` 屬性系結至 `CarouselView.Position` 屬性，而 `ItemsSource` 屬性系結至 `CarouselView.ItemsSource` 屬性。

## <a name="change-indicator-shape"></a>變更指標圖形

`IndicatorView` 類別具有 `IndicatorsShape` 屬性，表示指標的形狀。 這個屬性可以設定為其中一個 `IndicatorShape` 列舉成員：

- `Circle` 指定指標形狀將會迴圈。 此為 `IndicatorView.IndicatorsShape` 屬性的預設值。
- `Square` 表示指標形狀將會是正方形。

下列範例顯示設定為使用方形指標的 `IndicatorView`：

```xaml
<IndicatorView IndicatorsShape="Square"
               ItemsSourceBy="carouselView"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="define-indicator-appearance"></a>定義指標外觀

將 [`IndicatorView.IndicatorTemplate`] 屬性設定為 [ [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)]，即可定義每個指標的外觀：

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

在[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)中指定的元素會定義每個指標的外觀。 在此範例中，每個指標都是使用 `FontImage` 標記延伸顯示字型圖示的[`Image`](xref:Xamarin.Forms.Image) 。

下列螢幕擷取畫面顯示使用字型圖示呈現的指標：

[![IOS 和 Android 上樣板化 IndicatorView 的螢幕擷取畫面](indicatorview-images/templated.png "樣板化 IndicatorView")](indicatorview-images/templated-large.png#lightbox "樣板化 IndicatorView")

如需 `FontImage` 標記延伸的詳細資訊，請參閱[FontImage 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)。

## <a name="related-links"></a>相關連結

- [IndicatorView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [FontImage 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
