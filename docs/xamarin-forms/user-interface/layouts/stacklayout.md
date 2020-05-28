---
title: Xamarin.FormsStackLayout
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
ms.openlocfilehash: f624674cc6d4ba1bdc34a42fb52fb63ff8a7135a
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137965"
---
# <a name="xamarinforms-stacklayout"></a>Xamarin.FormsStackLayout

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)

[![Xamarin.FormsStackLayout](stacklayout-images/layouts.png "[!OP.無-LOC （Xamarin）] StackLayout")](stacklayout-images/layouts-large.png#lightbox "[!OP.無-LOC （Xamarin）] StackLayout")

會 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 以水準或垂直方式組織一維堆疊中的子視圖。 根據預設， `StackLayout` 會垂直導向。 此外， `StackLayout` 也可以當做包含其他子版面配置的父配置使用。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別會定義下列屬性：

- [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation)，屬於類型 [`StackOrientation`](xref:Xamarin.Forms.StackOrientation) ，表示子視圖的定位方向。 此屬性的預設值為 `Vertical`。
- [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)，屬於類型 `double` ，表示每個子視圖之間的空間量。 這個屬性的預設值是六個與裝置無關的單位。

這些屬性是由物件所支援 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，這表示屬性可以是資料系結和樣式的目標。

[`StackLayout`](xref:Xamarin.Forms.StackLayout)類別衍生自 `Layout<T>` 類別，其定義 `Children` 類型的屬性 `IList<T>` 。 `Children`屬性是類別的 `ContentProperty` `Layout<T>` ，因此不需要從 XAML 明確設定。

> [!TIP]
> 若要取得最佳的版面配置效能，請遵循[優化版面配置效能](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)中的指導方針。

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
> 屬性的值 [`Margin`](xref:Xamarin.Forms.View.Margin) 代表元素與其相鄰專案之間的距離。 如需詳細資訊，請參閱[邊界和邊框距離](margin-and-padding.md)。

## <a name="horizontal-orientation"></a>水準方向

下列 XAML 顯示如何 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 將其屬性設為，以建立水準方向 [`Orientation`](xref:Xamarin.Forms.StackLayout.Orientation) `Horizontal` ：

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

這個範例會建立水準 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`BoxView`](xref:Xamarin.Forms.BoxView) 物件，而子視圖之間有六個與裝置無關的空間單位：

[![水準方向 StackLayout 的螢幕擷取畫面](stacklayout-images/horizontal.png "水準導向 StackLayout")](stacklayout-images/horizontal-large.png#lightbox "水準導向 StackLayout")

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

將 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 屬性設定為值，即可變更中子視圖間的間距 [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing) `double` ：

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

這個範例會建立一個 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 [`Label`](xref:Xamarin.Forms.Label) 和 [`BoxView`](xref:Xamarin.Forms.BoxView) 物件的垂直，而且它們之間沒有間距：

[![沒有任何間距的 StackLayout 螢幕擷取畫面](stacklayout-images/spacing.png "不含任何間距的 StackLayout")](stacklayout-images/spacing-large.png#lightbox "不含任何間距的 StackLayout")

> [!TIP]
> [`Spacing`](xref:Xamarin.Forms.StackLayout.Spacing)屬性可以設定為負值，讓子視圖重迭。

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

中子視圖的大小和位置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 取決於子視圖的 [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) 和 [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) 屬性值，以及其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和屬性的值 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 。 在垂直的 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 子視圖中，當其大小未明確設定時，會展開以填滿可用的寬度。 同樣地，在水準的 `StackLayout` 子視圖中，當其大小未明確設定時，會展開以填滿可用的高度。

的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性（及其 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 子視圖）可以設定為結構中的欄位 [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) ，這會封裝兩個版面配置喜好設定：

- *對齊方式*會決定子視圖在其父系版面配置中的位置和大小。
- *展開*表示子視圖是否應該使用額外的空間（如果有的話）。

> [!TIP]
> [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) [`StackLayout`](xref:Xamarin.Forms.StackLayout) 除非您需要，否則請不要設定的和屬性。 `LayoutOptions.Fill` 和 `LayoutOptions.FillAndExpand` 的預設值可提供最優異的版面配置最佳化。 變更這些屬性會產生成本並耗用記憶體，即使將它們設定回預設值也一樣。

### <a name="alignment"></a>對齊

下列 XAML 範例會在中設定每個子視圖的對齊喜好設定 [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

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

在此範例中，會在物件上設定對齊喜好設定， [`Label`](xref:Xamarin.Forms.Label) 以控制其在中的位置 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)、 [`Center`](xref:Xamarin.Forms.LayoutOptions.Center) 、 [`End`](xref:Xamarin.Forms.LayoutOptions.End) 和 [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 欄位是用來定義 [`Label`](xref:Xamarin.Forms.Label) 父系內物件的對齊方式 `StackLayout` ：

[![已設定對齊選項之 StackLayout 的螢幕擷取畫面](stacklayout-images/alignment.png "具有對齊選項的 StackLayout")](stacklayout-images/alignment-large.png#lightbox "具有對齊選項的 StackLayout")

[`StackLayout`](xref:Xamarin.Forms.StackLayout) 只會遵循子檢視上的對齊喜好設定，而這與 `StackLayout` 方向相反。 因此，垂直方向 `StackLayout` 中的 [`Label`](xref:Xamarin.Forms.Label) 子檢視會將其 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 屬性設定為其中一個對齊欄位：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)，其會將置於的 [`Label`](xref:Xamarin.Forms.Label) 左邊 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)，這會將 [`Label`](xref:Xamarin.Forms.Label) 放在 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 的中心位置。
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)，其會將 [`Label`](xref:Xamarin.Forms.Label) 放在的右側 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。
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

下列 XAML 範例會針對中的每個設定擴充喜好設定 [`Label`](xref:Xamarin.Forms.Label) [`StackLayout`](xref:Xamarin.Forms.StackLayout) ：

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

在此範例中，會在物件上設定擴充喜好設定， [`Label`](xref:Xamarin.Forms.Label) 以控制其在中的大小 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)、 [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand) 、 [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand) 和 [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand) 欄位是用來定義對齊喜好設定，以及 `Label` 如果父系中有更多可用空間（如果有的話） `StackLayout` ：

[![已設定擴充選項之 StackLayout 的螢幕擷取畫面](stacklayout-images/expansion.png "具有展開選項的 StackLayout")](stacklayout-images/expansion-large.png#lightbox "具有展開選項的 StackLayout")

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

如需對齊和擴充的詳細資訊，請參閱[中 Xamarin.Forms 的版面配置選項](layout-options.md)。

## <a name="nested-stacklayout-objects"></a>Nested StackLayout 物件

[`StackLayout`](xref:Xamarin.Forms.StackLayout)可以當做包含嵌套子 `StackLayout` 物件或其他子版面配置的父配置使用。

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

在此範例中，父系 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 包含 `StackLayout` 物件內的嵌套物件 [`Frame`](xref:Xamarin.Forms.Frame) 。 父系 `StackLayout` 是垂直方向，而子物件則 `StackLayout` 是水準方向：

[![嵌套 StackLayout 物件的螢幕擷取畫面](stacklayout-images/combined.png "Nested StackLayouts")](stacklayout-images/combined-large.png#lightbox "Nested StackLayouts")

> [!IMPORTANT]
> 您的嵌套 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 物件和其他配置越深入，嵌套的配置就會影響效能。 如需詳細資訊，請參閱[選擇正確的版面](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout)配置。

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

- [StackLayout 示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-stacklayoutdemos)
- [中的版面配置選項Xamarin.Forms](layout-options.md)
- [選擇 Xamarin.Forms 版面配置](choose-layout.md)
- [改善 Xamarin.Forms 應用程式效能](~/xamarin-forms/deploy-test/performance.md)
