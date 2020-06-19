---
title: Xamarin.FormsIndicatorView
description: IndicatorView 是一個控制項，它會在 CarouselView 中顯示表示專案數目和目前位置的指標。
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0e6d223fd10e7b792f2d145a7cdd417865a095bb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101440"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.FormsIndicatorView

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

`IndicatorView`是一個控制項，它會在中顯示表示專案數和目前位置的指標 `CarouselView` ：

[![CarouselView 和 IndicatorView （在 iOS 和 Android 上）的螢幕擷取畫面](indicatorview-images/circles.png "IndicatorView 圓形")](indicatorview-images/circles-large.png#lightbox "IndicatorView 圓形")

`IndicatorView` 會定義下列屬性：

- `Count`，屬於類型 `int` ，指標數目。
- `HideSingle`，屬於類型 `bool` ，表示當指標只有一個存在時是否應該隱藏。 預設值是 `true`。
- `IndicatorColor`，屬於類型 `Color` ，指標的色彩。
- `IndicatorSize`，屬於類型 `double` ，指標的大小。 預設值為6.0。
- `IndicatorLayout`，屬於類型 `Layout<View>` ，可定義用來呈現的版面配置類別 `IndicatorView` 。 這個屬性是由所設定 Xamarin.Forms ，而且通常不需要由開發人員設定。
- `IndicatorTemplate`，屬於類型 `DataTemplate` ，這是定義每個指標外觀的範本。
- `IndicatorsShape`，屬於類型 `IndicatorShape` ，每個指標的形狀。
- `ItemsSource`，屬於類型 `IEnumerable` ，將顯示指標的集合。 當設定屬性時，將會自動設定此屬性 `CarouselView.IndicatorView` 。
- `MaximumVisible`，屬於類型 `int` ，這是可見指標的最大數目。 預設值是 `int.MaxValue`。
- `Position`，屬於類型 `int` ，這是目前選取的指標索引。 這個屬性會使用系結 `TwoWay` 。 當設定屬性時，將會自動設定此屬性 `CarouselView.IndicatorView` 。
- `SelectedIndicatorColor`，屬於類型 `Color` ，表示中目前專案的指標色彩 `CarouselView` 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標，以及樣式化的。

## <a name="create-an-indicatorview"></a>建立 IndicatorView

下列範例顯示如何 `IndicatorView` 在 XAML 中具現化：

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

在此範例中， `IndicatorView` 會在底下呈現 `CarouselView` ，其中每個專案都有一個指標 `CarouselView` 。 `IndicatorView`會藉由將屬性設定為物件，來填入資料 `CarouselView.IndicatorView` `IndicatorView` 。 每個指標都是淺灰色圓形，而表示中目前專案的指標 `CarouselView` 是暗灰色。

> [!IMPORTANT]
> 設定 `CarouselView.IndicatorView` 屬性會導致屬性系結 `IndicatorView.Position` 至 `CarouselView.Position` 屬性，並將 `IndicatorView.ItemsSource` 屬性系結至屬性 `CarouselView.ItemsSource` 。

## <a name="change-indicator-shape"></a>變更指標圖形

`IndicatorView`類別具有 `IndicatorsShape` 屬性，可決定指標的形狀。 這個屬性可以設定為其中一個 `IndicatorShape` 列舉成員：

- `Circle`指定指標形狀將會迴圈。 此為 `IndicatorView.IndicatorsShape` 屬性的預設值。
- `Square`表示指標圖形將為正方形。

下列範例顯示 `IndicatorView` 設定為使用方形指標的：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>變更指標大小

`IndicatorView`類別具有 `IndicatorSize` 類型的屬性 `double` ，可判斷指標的大小（以與裝置無關的單位表示）。 這個屬性的預設值為6.0。

下列範例顯示 `IndicatorView` 設定為顯示較大指標的：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>限制顯示的指示器數目

`IndicatorView`類別具有 `MaximumVisible` 類型的屬性 `int` ，可決定可見指標的最大數目。

下列範例顯示 `IndicatorView` 設定為顯示最多六個指標的：

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>定義指標外觀

將屬性設定為，即可定義每個指標的外觀 `IndicatorView.IndicatorTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

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

在中指定的元素會 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義每個指標的外觀。 在此範例中，每個指標都是 [`Image`](xref:Xamarin.Forms.Image) ，它會使用標記延伸顯示字型圖示 `FontImage` 。

下列螢幕擷取畫面顯示使用字型圖示呈現的指標：

[![IOS 和 Android 上樣板化 IndicatorView 的螢幕擷取畫面](indicatorview-images/templated.png "樣板化 IndicatorView")](indicatorview-images/templated-large.png#lightbox "樣板化 IndicatorView")

如需標記延伸的詳細資訊 `FontImage` ，請參閱[FontImage 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)。

## <a name="related-links"></a>相關連結

- [IndicatorView （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [FontImage 標記延伸](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
