---
title: Xamarin.Forms IndicatorView
description: IndicatorView 是一個控制項，會顯示代表 CarouselView 中專案數目和目前位置的指標。
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/24/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 27ee2892a25a86210bc86ad4d329fd1f1ca2c788
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751384"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin.Forms IndicatorView

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)是一個控制項，可顯示表示專案數目的指標，以及中的目前位置 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。

[![IOS 和 Android 上 CarouselView 和 IndicatorView 的螢幕擷取畫面](indicatorview-images/circles.png "IndicatorView 圓形")](indicatorview-images/circles-large.png#lightbox "IndicatorView 圓形")

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 定義下列屬性：

- `Count`，類型為指標 `int` 數目。
- `HideSingle`型別為的 `bool` ，指出當只有一個指標存在時，是否應該隱藏指標。 預設值是 `true`。
- `IndicatorColor`，類型為指標的 `Color` 色彩。
- `IndicatorSize`，類型為指標的 `double` 大小。 預設值為6.0。
- `IndicatorLayout`型別為的，會 `Layout<View>` 定義用來呈現的版面配置類別 [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 。 這個屬性是由設定的 Xamarin.Forms ，而且通常不需要由開發人員設定。
- `IndicatorTemplate`型別為的 `DataTemplate` 範本，定義每個指標的外觀。
- `IndicatorsShape`，屬於類型 `IndicatorShape` ，每個指標的形狀。
- `ItemsSource`，類型為 `IEnumerable` ，將顯示指標的集合。 當設定屬性時，會自動設定這個屬性 `CarouselView.IndicatorView` 。
- `MaximumVisible`，類型為 `int` 可見指標的最大數目。 預設值是 `int.MaxValue`。
- `Position`，類型為 `int` 目前選取的指標索引。 這個屬性會使用系結 `TwoWay` 。 當設定屬性時，會自動設定這個屬性 `CarouselView.IndicatorView` 。
- `SelectedIndicatorColor`，屬於類型 `Color` ，表示中目前專案之指標的色彩 [`CarouselView`](xref:Xamarin.Forms.CarouselView) 。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示它們可以是資料系結的目標和樣式。

## <a name="create-an-indicatorview"></a>建立 IndicatorView

下列範例顯示如何 [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 在 XAML 中具現化：

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

在此範例中， [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 會在下轉譯 [`CarouselView`](xref:Xamarin.Forms.CarouselView) ，並在中顯示每個專案的指標 `CarouselView` 。 `IndicatorView`會將 `CarouselView.IndicatorView` 屬性設定為物件，以填入資料 `IndicatorView` 。 每個指標都是淺灰色圓圈，而表示中目前專案的指標 `CarouselView` 為暗灰色。

> [!IMPORTANT]
> 設定屬性會導致屬性系結 `CarouselView.IndicatorView` `IndicatorView.Position` 至屬性 `CarouselView.Position` ，並將屬性系結 `IndicatorView.ItemsSource` 至屬性 `CarouselView.ItemsSource` 。

## <a name="change-indicator-shape"></a>變更指標圖形

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)類別具有 `IndicatorsShape` 屬性，可決定指標的形狀。 這個屬性可以設定為其中一個 `IndicatorShape` 列舉成員：

- `Circle` 指定指標圖形將為圓形。 此為 `IndicatorView.IndicatorsShape` 屬性的預設值。
- `Square` 指出指標圖形將會是正方形。

下列範例顯示 [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 已設定為使用正方形指標的：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>變更指標大小

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)類別具有 `IndicatorSize` 類型的屬性， `double` 可決定與裝置無關的單位中指標的大小。 這個屬性的預設值是6.0。

下列範例顯示 [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 已設定為顯示較大指標的：

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>限制顯示的指標數目

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView)類別具有 `MaximumVisible` 類型的屬性， `int` 可決定可見指標的最大數目。

下列範例顯示 [`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 已設定為最多顯示六個指標的：

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>定義指標外觀

您可以藉由將屬性設定為，來定義每個指標的外觀 `IndicatorView.IndicatorTemplate` [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) ：

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   Margin="0,0,0,40"
                   IndicatorColor="Transparent"
                   SelectedIndicatorColor="Transparent"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Label Text="&#xf30c;"
                       FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

中指定的元素會 [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) 定義每個指標的外觀。 在此範例中，每個指標都會 [`Label`](xref:Xamarin.Forms.Label) 顯示字型圖示。

下列螢幕擷取畫面顯示使用字型圖示呈現的指標：

[![IOS 和 Android 上樣板化 IndicatorView 的螢幕擷取畫面](indicatorview-images/templated.png "樣板化 IndicatorView")](indicatorview-images/templated-large.png#lightbox "樣板化 IndicatorView")

## <a name="set-visual-states"></a>設定視覺狀態

[`IndicatorView`](xref:Xamarin.Forms.IndicatorView) 具有 `Selected` 視覺狀態，可用來針對中的目前位置，起始對指標的視覺效果變更 `IndicatorView` 。 常見的使用案例 [`VisualState`](xref:Xamarin.Forms.VisualState) 是變更表示目前位置之指標的色彩：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style x:Key="IndicatorLabelStyle"
               TargetType="Label">
            <Setter Property="VisualStateManager.VisualStateGroups">
                <VisualStateGroupList>
                    <VisualStateGroup x:Name="CommonStates">
                        <VisualState x:Name="Normal">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="LightGray" />
                            </VisualState.Setters>
                        </VisualState>
                        <VisualState x:Name="Selected">
                            <VisualState.Setters>
                                <Setter Property="TextColor"
                                        Value="Black" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateGroupList>
            </Setter>
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        ...
        <IndicatorView x:Name="indicatorView"
                       Margin="0,0,0,40"
                       IndicatorColor="Transparent"
                       SelectedIndicatorColor="Transparent"
                       HorizontalOptions="Center">
            <IndicatorView.IndicatorTemplate>
                <DataTemplate>
                    <Label Text="&#xf30c;"
                           FontFamily="{OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}"
                           Style="{StaticResource IndicatorLabelStyle}" />
                </DataTemplate>
            </IndicatorView.IndicatorTemplate>
        </IndicatorView>
    </StackLayout>
</ContentPage>
```

在此範例中， `Selected` 視覺狀態指定代表目前位置的指標會將其 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 設定為黑色。 否則 `TextColor` 指標的會是淺灰色：

![IndicatorView 選取的視覺狀態的螢幕擷取畫面](indicatorview-images/visual-state.png)

如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [IndicatorView (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Xamarin.Forms CarouselView](~/xamarin-forms/user-interface/carouselview/index.md)
- [Xamarin.Forms 視覺狀態管理員](visual-state-manager.md)
