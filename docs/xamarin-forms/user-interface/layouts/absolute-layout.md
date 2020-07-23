---
title: Xamarin.FormsAbsoluteLayout
description: 本文說明如何使用 Xamarin.Forms AbsoluteLayout 類別來建立最適合圖元的 ui。 這個類別會以其本身的大小和位置或絕對值為子項目的位置和大小。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 923f7643bd1e137192bfb80dbbc7c5d2c25b5471
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938420"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.FormsAbsoluteLayout

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)將子專案的位置和大小與其本身的大小和位置或絕對值成正比。 子視圖可能會使用比例值或靜態值來定位和調整大小，而且可以混用比例和靜態值。

[![Xamarin.Forms大綱](absolute-layout-images/layouts-sml.png)](absolute-layout-images/layouts.png#lightbox "[!OP.無-LOC （Xamarin）] 版面配置")

此文章將涵蓋︰

- **[用途](#purpose)** &ndash;的常見用法 `AbsoluteLayout` 。
- **[使用](#usage)** &ndash; 方式如何使用 `AbsoluteLayout` 來達到您想要的設計。
  - **[比例配置](#proportional-layouts)** &ndash;瞭解比例值在中的使用方式 `AbsoluteLayout` 。
  - **[指定值](#specifying-values)** &ndash;瞭解如何指定比例和絕對值。
  - **[比例值](#proportional-values)** &ndash;瞭解比例值的工作方式。
    - **[絕對值](#absolute-values)** &ndash;瞭解絕對值的工作方式。

## <a name="purpose"></a>目的

因為的定位模型，配置 `AbsoluteLayout` 會使元素的位置相對直接，使其與版面配置的任一側或置中對齊。 有比例的大小和位置，中的元素 `AbsoluteLayout` 可以自動調整為任何視圖大小。 對於僅限位置但大小不應調整的專案，可以混合具有絕對和比例的值。

`AbsoluteLayout`可以在任何地方使用，而元素必須位於視圖內，而且在對齊元素與邊緣時特別有用。

## <a name="usage"></a>使用量

### <a name="proportional-layouts"></a>比例配置

`AbsoluteLayout`具有唯一錨定模型，其中專案的錨點會相對於其專案定位，因為使用比例位置時，專案是相對於版面配置的位置。 使用絕對位置時，錨點會在此視圖內的（0，0）。 這有兩個重要的結果：

- 無法使用比例值將元素置於關閉畫面。
- 無論配置或裝置的大小為何，專案都可以可靠地放置在版面配置或中央的任何一邊。

`AbsoluteLayout`（例如 `RelativeLayout` ）能夠放置專案，使其重迭。

請注意，在下列螢幕擷取畫面中，方塊的錨點是一個空白字元。 請注意，錨點和方塊在版面配置上移動時的關聯性：

![位於 ](absolute-layout-images/anchor-start.png)
 ![ 中央 ](absolute-layout-images/anchor-center.png)
 ![ 錨點結尾處的錨點](absolute-layout-images/anchor-end.png)

### <a name="specifying-values"></a>指定值

中的 Views `AbsoluteLayout` 會使用四個值來定位：

- **X** &ndash; 視圖錨點的 x （水準）位置
- **Y** &ndash; 視圖錨點的 y （垂直）位置
- **寬度** &ndash;視圖的寬度
- **高度** &ndash;視圖的高度

每個值都可以設定為[比例](#proportional-values)值或[絕對值](#absolute-values)。

值會指定為界限和旗標的組合。 `LayoutBounds`是由 [`Rectangle`](xref:Xamarin.Forms.Rectangle) 四個值所組成： `x` 、 `y` 、 `width` 、 `height` 。

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags)指定值的解讀方式，並具有下列預先定義的選項：

- **無** &ndash;將所有值解讀為絕對。 如果未指定任何版面配置旗標，這就是預設值。
- **全部** &ndash;將所有值以比例方式轉譯。
- **WidthProportional** &ndash;將 `Width` 值解讀為比例，並將所有其他值視為絕對值。
- **HeightProportional** &ndash;僅將高度值轉譯為與所有其他值為絕對值的正比。
- **XProportional** &ndash;將 `X` 值以比例解讀，同時將所有其他值視為絕對。
- **YProportional** &ndash;將 `Y` 值以比例解讀，同時將所有其他值視為絕對。
- **PositionProportional** &ndash;將 `X` 和 `Y` 值解讀為比例，而大小值會解讀為絕對。
- **SizeProportional** &ndash;將 `Width` 和 `Height` 值解讀為比例，而位置值是絕對的。

在 XAML 中，會使用屬性，將界限和旗標設定為配置內的視圖定義的一部分 `AbsoluteLayout.LayoutBounds` 。 界限會設定為以逗號分隔的值清單，分別為、、 `X` `Y` `Width` 和 `Height` （依此順序）。 您也可以使用屬性，在版面配置中的觀點宣告中指定旗標 `AbsoluteLayout.LayoutFlags` 。 請注意，您可以使用逗號分隔的清單，在 XAML 中結合旗標。 請考慮下列範例：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.AbsoluteLayoutExploration"
Title="Absolute Layout Exploration">
    <ContentPage.Content>
        <AbsoluteLayout>
            <Label Text="I'm centered on iPhone 4 but no other device"
                AbsoluteLayout.LayoutBounds="115,150,100,100" LineBreakMode="WordWrap"  />
            <Label Text="I'm bottom center on every device."
                AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"
                LineBreakMode="WordWrap"  />
            <BoxView Color="Olive"  AbsoluteLayout.LayoutBounds="1,.5, 25, 100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Red" AbsoluteLayout.LayoutBounds="0,.5,25,100"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
            <BoxView Color="Blue" AbsoluteLayout.LayoutBounds=".5,0,100,25"
                AbsoluteLayout.LayoutFlags="PositionProportional" />
        </AbsoluteLayout>
    </ContentPage.Content>
</ContentPage>
```

![AbsoluteLayout 範例](absolute-layout-images/exploration.png)

請注意：

- 中心的標籤是使用絕對大小和位置值來定位。 因此，它會以 iPhone 4S 和較低的方式呈現，但不會在較大的裝置上置中。
- 版面配置底部的文字會使用比例大小和位置值來定位。 它一律會出現在版面配置的底部，但其大小會隨著較大的版面配置大小而增加。
- 三個彩色的 `BoxView` 會使用比例位置和絕對大小，放在畫面的頂端、左邊和右邊緣。

下列步驟會在 c # 中達到相同的版面配置：

```csharp
public class AbsoluteLayoutExplorationCode : ContentPage
{
    public AbsoluteLayoutExplorationCode ()
    {
        Title = "Absolute Layout Exploration - Code";
        var layout = new AbsoluteLayout();

        var centerLabel = new Label {
        Text = "I'm centered on iPhone 4 but no other device.",
        LineBreakMode = LineBreakMode.WordWrap};

        AbsoluteLayout.SetLayoutBounds (centerLabel, new Rectangle (115, 159, 100, 100));
        // No need to set layout flags, absolute positioning is the default

        var bottomLabel = new Label { Text = "I'm bottom center on every device.", LineBreakMode = LineBreakMode.WordWrap };
        AbsoluteLayout.SetLayoutBounds (bottomLabel, new Rectangle (.5, 1, .5, .1));
        AbsoluteLayout.SetLayoutFlags (bottomLabel, AbsoluteLayoutFlags.All);

        var rightBox = new BoxView{ Color = Color.Olive };
        AbsoluteLayout.SetLayoutBounds (rightBox, new Rectangle (1, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (rightBox, AbsoluteLayoutFlags.PositionProportional);

        var leftBox = new BoxView{ Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds (leftBox, new Rectangle (0, .5, 25, 100));
        AbsoluteLayout.SetLayoutFlags (leftBox, AbsoluteLayoutFlags.PositionProportional);

        var topBox = new BoxView{ Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds (topBox, new Rectangle (.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags (topBox, AbsoluteLayoutFlags.PositionProportional);

        layout.Children.Add (bottomLabel);
        layout.Children.Add (centerLabel);
        layout.Children.Add (rightBox);
        layout.Children.Add (leftBox);
        layout.Children.Add (topBox);

        Content = layout;
    }
}
```

### <a name="proportional-values"></a>比例值

比例值會定義版面配置和視圖之間的關聯性。 此關聯性會將子視圖的位置或小數位數值定義為父配置對應值的比例。 這些值會以 `double` 介於0和1之間的值表示。

比例值是用來定位和調整版面配置內的視圖。 因此，當視圖的寬度設定為比例時，結果的寬度值會乘以的 `AbsoluteLayout` 寬度。 例如，如果 `AbsoluteLayout` width 的 `500` 和 view 設定為的比例寬度為 .5，則此視圖的呈現寬度會是250（500 x. 5）。

若要使用比例值，請 `LayoutBounds` 使用（x，y）比例和比例大小來設定，然後將設定 `LayoutFlags` 為 `All` 。

在 XAML 中：

```xaml
<Label Text="I'm bottom center on every device."
  AbsoluteLayout.LayoutBounds=".5,1,.5,.1" AbsoluteLayout.LayoutFlags="All"  />
```

在 C# 中：

```csharp
var label = new Label {Text = "I'm bottom center on every device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(.5,1,.5,.1));
AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.All);
```

### <a name="absolute-values"></a>絕對值

絕對值明確定義在版面配置內應放置視圖的位置。 與比例值不同的是，絕對值可以定位並調整不符合版面配置範圍的視圖大小。

當配置的大小未知時，使用絕對值進行定位可能會有危險。 當使用絕對位置時，畫面中央的某個元素可能會以任何其他大小位移。 請務必在支援裝置的各種螢幕大小中測試您的應用程式。

若要使用絕對版面配置值，請 `LayoutBounds` 使用（x，y）座標和明確大小來設定，然後將設定 `LayoutFlags` 為 `None` 。

在 XAML 中：

```xaml
<Label Text="I'm centered on iPhone 4 but no other device."
  AbsoluteLayout.LayoutBounds="115,150,100,100"  />
```

在 C# 中：

```csharp
var label = new Label {Text = "I'm centered on iPhone 4 but no other device."};
AbsoluteLayout.SetLayoutBounds(label, new Rectangle(115,150,100,100));
```

## <a name="exploring-a-complex-layout"></a>探索複雜的版面配置

每個版面配置都有建立特定版面配置的優點和缺點。 在這一系列的版面配置文章中，已建立範例應用程式，並使用三種不同的版面配置來執行相同的頁面配置。

請考慮下列 XAML：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.AbsoluteLayoutPage"
Title="AbsoluteLayout">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Save" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <ScrollView>
            <AbsoluteLayout BackgroundColor="Maroon">
                <BoxView BackgroundColor="Gray" AbsoluteLayout.LayoutBounds="0
                    0,1,100" AbsoluteLayout.LayoutFlags="XProportional,YProportional,WidthProportional" />
                <Button BackgroundColor="Maroon"
                    AbsoluteLayout.LayoutBounds=".5,55,70,70" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="35" />
                <Button BackgroundColor="Red" AbsoluteLayout.LayoutBounds=".5
                    60,60,60" AbsoluteLayout.LayoutFlags="XProportional" BorderRadius="30" />
                <Label Text="User Name" FontAttributes="Bold" FontSize="26"
                    TextColor="Black" HorizontalTextAlignment="Center" AbsoluteLayout.LayoutBounds=".5,140,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <Entry Text="Bio + Hashtags" TextColor="White"
                    BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds=".5,180,1,40" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                <AbsoluteLayout BackgroundColor="White"
                        AbsoluteLayout.LayoutBounds="0, 220, 1, 50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional">
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional">
                        <Button BackgroundColor="Black" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Accent Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                    <AbsoluteLayout AbsoluteLayout.LayoutBounds="1,0,.5,1" AbsoluteLayout.LayoutFlags="WidthProportional,HeightProportional,XProportional">
                        <Button BackgroundColor="Maroon" BorderRadius="20"
                            AbsoluteLayout.LayoutBounds="5,.5,40,40" AbsoluteLayout.LayoutFlags="YProportional" />
                        <Label Text="Primary Color" TextColor="Black"
                            AbsoluteLayout.LayoutBounds="50,.55,1,25" AbsoluteLayout.LayoutFlags="YProportional,WidthProportional" />
                    </AbsoluteLayout>
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,270,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Age:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
                        AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,320,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Interests:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin.Forms" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
                <AbsoluteLayout AbsoluteLayout.LayoutBounds="0,370,1,50" AbsoluteLayout.LayoutFlags="WidthProportional" Padding="5,0,0,0">
                    <Label Text="Ask me about:" TextColor="White"
                        AbsoluteLayout.LayoutBounds="0,25,.25,50" AbsoluteLayout.LayoutFlags="WidthProportional" />
                    <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
                        BackgroundColor="Maroon" AbsoluteLayout.LayoutBounds="1,10,.75,50" AbsoluteLayout.LayoutFlags="XProportional,WidthProportional" />
                </AbsoluteLayout>
            </AbsoluteLayout>
        </ScrollView>
    </ContentPage.Content>
</ContentPage>
```

上述程式碼會產生下列版面配置：

![複雜的 AbsoluteLayout](absolute-layout-images/abs.png)

請注意， `AbsoluteLayout` 會進行嵌套，因為在某些情況下，相較于在相同的版面配置中呈現所有元素，嵌套配置可能會比較容易。

## <a name="related-links"></a>相關連結

- [建立 Mobile Apps Xamarin.Forms ，第14章](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [版面配置（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [BusinessTumble 範例（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
