---
title: Xamarin.Forms StackLayout
description: StackLayout 會以水準或垂直方式組織一維堆疊中的子視圖。
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/11/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fcd5f3deb2c7645988cd70b3556b718151df99fd
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366655"
---
# <a name="no-locxamarinforms-stacklayout"></a>Xamarin.Forms StackLayout

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![：：：非 loc (Xamarin. Forms) ：：： StackLayout](stacklayout-images/layouts.png "：：：非 loc (Xamarin. Forms) ：：： StackLayout")](stacklayout-images/layouts-large.png#lightbox "：：：非 loc (Xamarin. Forms) ：：： StackLayout")

會以 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 水準或垂直方式組織一維堆疊中的子視圖。 依預設， `StackLayout` 會垂直導向。 此外， `StackLayout` 也可以當做包含其他子配置的父配置使用。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)型別為的 [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) ，代表子視圖的定位方向。 此屬性的預設值為 `Vertical`。
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)型別為的 `double` ，表示每個子視圖之間的空間量。 這個屬性的預設值為六個裝置獨立單位。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別衍生自 `Layout<T>` 類別，該類別會定義 `Children` 型別的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循將 [版面配置效能優化](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)的指導方針。

## <a name="vertical-orientation"></a>垂直方向

下列 XAML 顯示如何建立 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含不同子視圖的垂直方向：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.VerticalStackLayoutPage"
             Title="Vertical StackLayout demo">
    <StackLayout Margin="20">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

這個範例會建立一個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`Label`](xref:Xamarin.Forms.Label) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 物件的垂直。 根據預設，子視圖之間有六個與裝置無關的空間單位：

[![垂直方向 StackLayout 的螢幕擷取畫面](stacklayout-images/vertical.png "垂直方向 StackLayout")](stacklayout-images/vertical-large.png#lightbox "垂直方向 StackLayout")

對等的 C# 程式碼為：

```csharp
public class VerticalStackLayoutPageCS : ContentPage
{
    public VerticalStackLayoutPageCS()
    {
        Title = "Vertical StackLayout demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

> [!NOTE]
> 屬性的值 [`Margin`](xref:Xamarin.Forms.View.Margin) 代表元素與其相鄰元素之間的距離。 如需詳細資訊，請參閱[邊界和邊框距離](margin-and-padding.md)。

## <a name="horizontal-orientation"></a>水準方向

下列 XAML 顯示如何將 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 其屬性設定為，以建立水準方向 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) `Horizontal` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.HorizontalStackLayoutPage"
             Title="Horizontal StackLayout demo">
    <StackLayout Margin="20"
                 Orientation="Horizontal"
                 HorizontalOptions="Center">
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

這個範例會建立一個水準 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`BoxView`](xref:Xamarin.Forms.BoxView) 物件，而子視圖之間有六個與裝置無關的空間單位：

[![水準方向 StackLayout 的螢幕擷取畫面](stacklayout-images/horizontal.png "水準方向 StackLayout")](stacklayout-images/horizontal-large.png#lightbox "水準方向 StackLayout")

對等的 C# 程式碼為：

```csharp
public HorizontalStackLayoutPageCS()
{
    Title = "Horizontal StackLayout demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Orientation = StackOrientation.Horizontal,
        HorizontalOptions = LayoutOptions.Center,
        Children =
        {
            new BoxView { Color = Color.Red },
            new BoxView { Color = Color.Yellow },
            new BoxView { Color = Color.Blue },
            new BoxView { Color = Color.Green },
            new BoxView { Color = Color.Orange },
            new BoxView { Color = Color.Purple }
        }
    };
}
```

## <a name="space-between-child-views"></a>子視圖之間的空間

您 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 可以藉由將 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) 屬性設定為值，來變更中子視圖之間的間距 `double` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.StackLayoutSpacingPage"
             Title="StackLayout Spacing demo">
    <StackLayout Margin="20"
                 Spacing="0">
        <Label Text="Primary colors" />
        <BoxView Color="Red" />
        <BoxView Color="Yellow" />
        <BoxView Color="Blue" />
        <Label Text="Secondary colors" />
        <BoxView Color="Green" />
        <BoxView Color="Orange" />
        <BoxView Color="Purple" />
    </StackLayout>
</ContentPage>
```

這個範例會建立垂直 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`Label`](xref:Xamarin.Forms.Label) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 物件之間沒有間距的物件：

[![StackLayout 沒有任何間距的螢幕擷取畫面](stacklayout-images/spacing.png "不含任何間距的 StackLayout")](stacklayout-images/spacing-large.png#lightbox "不含任何間距的 StackLayout")

> [!TIP]
> [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)屬性可以設定為負數值，讓子視圖重迭。

對等的 C# 程式碼為：

```csharp
public class StackLayoutSpacingPageCS : ContentPage
{
    public StackLayoutSpacingPageCS()
    {
        Title = "StackLayout Spacing demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Spacing = 0,
            Children =
            {
                new Label { Text = "Primary colors" },
                new BoxView { Color = Color.Red },
                new BoxView { Color = Color.Yellow },
                new BoxView { Color = Color.Blue },
                new Label { Text = "Secondary colors" },
                new BoxView { Color = Color.Green },
                new BoxView { Color = Color.Orange },
                new BoxView { Color = Color.Purple }
            }
        };
    }
}
```

## <a name="position-and-size-of-child-views"></a>子視圖的位置和大小

中子視圖的大小和位置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，取決於子視圖的值和屬性的值 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) ，以及其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性的值 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 在垂直的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 情況下，當子視圖的大小未明確設定時，就會展開以填滿可用的寬度。 同樣地，在水準中 `StackLayout` ，子視圖會展開，以在其大小未明確設定時填滿可用的高度。

[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 其子視圖的和屬性 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 可以設定為結構中的欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ，此結構會封裝兩個版面配置喜好設定：

- *對齊* 會決定子視圖在其父配置內的位置和大小。
- *展開* 會指出子視圖是否應該使用額外的空間（如果有的話）。

> [!TIP]
> [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)除非您需要，否則請勿設定的和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 `LayoutOptions.Fill` 和 `LayoutOptions.FillAndExpand` 的預設值可提供最優異的版面配置最佳化。 變更這些屬性會產生成本並耗用記憶體，即使將它們設回預設值也一樣。

### <a name="alignment"></a>對應項目

下列 XAML 範例會在中的每個子視圖上設定對齊喜好設定 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.AlignmentPage"
             Title="Alignment demo">
    <StackLayout Margin="20">
        <Label Text="Start"
               BackgroundColor="Gray"
               HorizontalOptions="Start" />
        <Label Text="Center"
               BackgroundColor="Gray"
               HorizontalOptions="Center" />
        <Label Text="End"
               BackgroundColor="Gray"
               HorizontalOptions="End" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               HorizontalOptions="Fill" />
    </StackLayout>
</ContentPage>
```

在此範例中，會在物件上設定對齊喜好設定， [`Label`](xref:Xamarin.Forms.Label) 以控制其在中的位置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)、、 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) [`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 欄位是用來定義父系內物件的對齊方式 [`Label`](xref:Xamarin.Forms.Label) `StackLayout` ：

[![StackLayout 對齊選項設定的螢幕擷取畫面](stacklayout-images/alignment.png "具有對齊選項的 StackLayout")](stacklayout-images/alignment-large.png#lightbox "具有對齊選項的 StackLayout")

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會遵循子檢視上的對齊喜好設定，而這與 `StackLayout` 方向相反。 因此，垂直方向 `StackLayout` 中的 [`Label`](xref:Xamarin.Forms.Label) 子檢視會將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為其中一個對齊欄位：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，將放置在的 [`Label`](xref:Xamarin.Forms.Label) 左邊 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的中心位置。
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)，將放置於 [`Label`](xref:Xamarin.Forms.Label) 右邊的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)，這會確保 [`Label`](xref:Xamarin.Forms.Label) 填入 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的寬度。

對等的 C# 程式碼為：

```csharp
public class AlignmentPageCS : ContentPage
{
    public AlignmentPageCS()
    {
        Title = "Alignment demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
                new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
                new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
                new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
            }
        };
    }
}
```

### <a name="expansion"></a>擴充

下列 XAML 範例會針對中的每一個設定擴充喜好設定 [`Label`](xref:Xamarin.Forms.Label) [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.ExpansionPage"
             Title="Expansion demo">
    <StackLayout Margin="20">
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Start"
               BackgroundColor="Gray"
               VerticalOptions="StartAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Center"
               BackgroundColor="Gray"
               VerticalOptions="CenterAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="End"
               BackgroundColor="Gray"
               VerticalOptions="EndAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
        <Label Text="Fill"
               BackgroundColor="Gray"
               VerticalOptions="FillAndExpand" />
        <BoxView BackgroundColor="Red"
                 HeightRequest="1" />
    </StackLayout>
</ContentPage>
```

在此範例中，會在物件上設定展開喜好設定， [`Label`](xref:Xamarin.Forms.Label) 以控制其在中的大小 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、、 [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 欄位是用來定義對齊喜好設定，以及是否 `Label` 會在父系中有更多空間時佔用空間 `StackLayout` ：

[![具有展開選項組之 StackLayout 的螢幕擷取畫面](stacklayout-images/expansion.png "具有展開選項的 StackLayout")](stacklayout-images/expansion-large.png#lightbox "具有展開選項的 StackLayout")

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會讓子檢視往其方向延展。 因此，垂直方向的 `StackLayout` 若要延展 [`Label`](xref:Xamarin.Forms.Label) 子檢視，其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性須設定為其中一個延展欄位。 這表示，在垂直對齊的情況下，每個 `Label` 會佔用 `StackLayout` 內相同數量的空間。 不過，最後的 `Label` 會將其 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性設為 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)，因此只有其大小不同。

> [!TIP]
> 使用時 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ，請確定只有一個子視圖設定為 [`LayoutOptions.Expands`](xref:Xamarin.Forms.LayoutOptions.Expands) 。 此屬性可確保指定的子系會佔用 `StackLayout` 所能提供的最大空間，重複執行這些計算將會浪費資源。

對等的 C# 程式碼為：

```csharp
public ExpansionPageCS()
{
    Title = "Expansion demo";
    Content = new StackLayout
    {
        Margin = new Thickness(20),
        Children =
        {
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "StartAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.StartAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "CenterAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.CenterAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "EndAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.EndAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 },
            new Label { Text = "FillAndExpand", BackgroundColor = Color.Gray, VerticalOptions = LayoutOptions.FillAndExpand },
            new BoxView { BackgroundColor = Color.Red, HeightRequest = 1 }
        }
    };
}
```

> [!IMPORTANT]
> 如果 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 中的所有空間都已使用，則延展喜好設定不會有作用。

如需對齊和展開的詳細資訊，請參閱[中 Xamarin.Forms 的版面配置選項](layout-options.md)。

## <a name="nested-stacklayout-objects"></a>嵌套 StackLayout 物件

[`StackLayout`](xref:Xamarin.Forms.StackLayout)可用來做為父配置，其中包含嵌套的子 `StackLayout` 物件或其他子版面配置。

下列 XAML 顯示的是嵌套物件的範例 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="StackLayoutDemos.Views.CombinedStackLayoutPage"
             Title="Combined StackLayouts demo">
    <StackLayout Margin="20">
        ...
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Red" />
                <Label Text="Red"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Yellow" />
                <Label Text="Yellow"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        <Frame BorderColor="Black"
               Padding="5">
            <StackLayout Orientation="Horizontal"
                         Spacing="15">
                <BoxView Color="Blue" />
                <Label Text="Blue"
                       FontSize="Large"
                       VerticalOptions="Center" />
            </StackLayout>
        </Frame>
        ...
    </StackLayout>
</ContentPage>
```

在此範例中，父系 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 `StackLayout` 物件內的嵌套物件 [`Frame`](xref:Xamarin.Forms.Frame) 。 父代 `StackLayout` 是垂直方向，而子 `StackLayout` 物件是水準方向：

[![嵌套 StackLayout 物件的螢幕擷取畫面](stacklayout-images/combined.png "嵌套 StackLayouts")](stacklayout-images/combined-large.png#lightbox "嵌套 StackLayouts")

> [!IMPORTANT]
> 您可以更深入 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 地嵌套物件和其他配置，嵌套的版面配置會影響效能。 如需詳細資訊，請參閱 [選擇正確的版面](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout)配置。

對等的 C# 程式碼為：

```csharp
public class CombinedStackLayoutPageCS : ContentPage
{
    public CombinedStackLayoutPageCS()
    {
        Title = "Combined StackLayouts demo";
        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children =
            {
                new Label { Text = "Primary colors" },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Red },
                            new Label { Text = "Red", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Yellow },
                            new Label { Text = "Yellow", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                new Frame
                {
                    BorderColor = Color.Black,
                    Padding = new Thickness(5),
                    Content = new StackLayout
                    {
                        Orientation = StackOrientation.Horizontal,
                        Spacing = 15,
                        Children =
                        {
                            new BoxView { Color = Color.Blue },
                            new Label { Text = "Blue", FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)), VerticalOptions = LayoutOptions.Center }
                        }
                    }
                },
                // ...
            }
        };
    }
}
```

## <a name="related-links"></a>相關連結

- [StackLayout 示範 (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [中的版面配置選項 Xamarin.Forms](layout-options.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)