---
title: AbsoluteLayout
description: 若要建立像素完美的 Ui 使用 AbsoluteLayout。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: d07026fbcc43a43a9f26d85ad15d5a4e3165e2ef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="absolutelayout"></a>AbsoluteLayout

[`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) 放置並調整大小成正比到其本身的大小和位置，或由絕對值的子項目。 子檢視可能定位和調整大小成比例的值或靜態值，使用比例，並可以混合靜態值。

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms 配置")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms 版面配置")

本文將說明如何：

- **[目的](#Purpose)** &ndash;的常見用法`AbsoluteLayout`。
- **[使用量](#Usage)** &ndash;如何使用`AbsoluteLayout`達到您所需的設計。
  - **[比例配置](#Proportional_Layouts)** &ndash;了解如何成比例的值中運作`AbsoluteLayout`。
  - **[指定值](#Specifying_Values)** &ndash;了解如何指定比例和絕對值。
  - **[調和間距值](#Proportional_Values)** &ndash;了解如何成比例的值運作。
    - **[絕對值](#Absolute_Values)** &ndash;了絕對值的運作方式。

<a name="Purpose" />

## <a name="purpose"></a>用途

因為定位模型`AbsoluteLayout`，版面配置可讓相當直接的方法，使其與任一邊的版面配置時，對齊或置中定位項目。 有成比例的大小和位置中的項目`AbsoluteLayout`可自動調整成任何檢視大小。 針對其中的位置，但不是大小應該會調整項目，可以混合絕對及比例的值。

`AbsoluteLayout` 您可以使用任何項目放置在檢視中需要和對齊邊緣的項目時就特別有用。

<a name="Usage" />

## <a name="usage"></a>使用量

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>調和間距的版面配置

`AbsoluteLayout` 具有唯一的錨點模型讓錨點的項目相對於其項目為比例位置使用時與元素位置相對於版面配置。 使用絕對位置時，錨點會在 (0，0) 位於檢視。 這有兩個重要的結果：

- 項目不能定位螢幕使用成比例的值。
- 項目可以可靠地放置一起配置的或在中心，而不管版面配置或裝置的大小。

`AbsoluteLayout`例如`RelativeLayout`，可以將項目，因此發生重疊。

請注意，在下列螢幕擷取畫面， 方塊中的錨點是白色的點。 在移動配置，請注意錨點和方塊之間的關聯性：

![](absolute-layout-images/anchor-start.png "錨點開頭")
![](absolute-layout-images/anchor-center.png "錨定在中心")
![](absolute-layout-images/anchor-end.png "結尾的錨點")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>指定的值

檢視的`AbsoluteLayout`位於使用四個值：

- **X** &ndash;檢視的錨點的 x （水平） 位置
- **Y** &ndash; y （垂直） 的檢視表的錨點位置
- **寬度**&ndash;寬度為檢視
- **高度**&ndash;檢視的高度

每個這些值可以設定為[比例](#Proportional_Values)值或[絕對](#Absolute_Values)值。

值會指定為界限和旗標的組合。 `LayoutBounds` 是[ `Rectangle` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Rectangle/)四個值所組成： `x`， `y`， `width`， `height`。

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayoutFlags/) 指定值解譯的方式，並且具有下列預先定義的選項：

- **無**&ndash;將所有的值解譯為 absolute。 如果沒有配置旗標會指定，這是預設值。
- **所有**&ndash;會解譯成比例的所有值。
- **WidthProportional** &ndash;解譯`Width`絕對比例和所有其他值的值。
- **HeightProportional** &ndash;只高度會將該值解譯成比例的所有其他絕對的值。
- **XProportional** &ndash;解譯`X`值成比例，同時將所有其他值視為絕對。
- **YProportional** &ndash;解譯`Y`值成比例，同時將所有其他值視為絕對。
- **PositionProportional** &ndash;解譯`X`和`Y`值做為成正比，而大小值會解譯為 absolute。
- **SizeProportional** &ndash;解譯`Width`和`Height`絕對位置值時，值成比例。

在 XAML 中，繫結和旗標已設定為檢視配置內定義的一部分使用`AbsoluteLayout.LayoutBounds`屬性。 範圍會設定為以逗號分隔清單的值， `X`， `Y`， `Width`，和`Height`，依此順序。 旗標所指定的版面配置使用中的檢視宣告`AbsoluteLayout.LayoutFlags`屬性。 請注意，可以在 XAML 中使用逗號分隔的清單結合旗標。 參考下列範例：

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

![](absolute-layout-images/exploration.png "AbsoluteLayout 範例")

請注意下列事項：

- 使用絕對大小和位置的值位在中心中的標籤。 因此，它會出現在 iPhone 4S 上置中和低，但不是置中較大的裝置上。
- 使用比例大小和位置的值位於底部的 版面配置的文字。 它一律會出現在底端中央的版面配置，但其大小會隨較大的配置大小。
- 三個彩色`BoxView`s 位於使用成比例的位置和絕對大小螢幕上、 左和右邊緣。

下列項目達到相同的配置，在 C# 中：

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
<a name="Proportional_Values" />

### <a name="proportional-values"></a>調和間距的值

調和間距的值會定義版面配置和檢視之間的關聯性。 此關聯性定義對應的父配置值比例子檢視的位置或小數位數值。 這些值會表示為`double`s 和介於 0 和 1 之間的值。

位置和配置內的大小檢視會使用成比例的值。 因此，當檢視表的寬度設定為所佔百分比，結果寬度值會乘以比例`AbsoluteLayout`的寬度。 例如，使用`AbsoluteLayout`寬度的`500`會 250 (500 x.5 上設定成有調和間距寬度為.5，呈現寬度為檢視的檢視。

若要使用成比例的值，請設定`LayoutBounds`使用 （x，y） 比例和調和間距大小，然後設定`LayoutFlags`至`All`。

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

<a name="Absolute_Values" />

### <a name="absolute-values"></a>絕對值

絕對值明確地定義檢視配置中的位置。 不同於成比例的值，絕對值可的定位和調整大小無法容納版面配置的範圍內的檢視。

使用絕對值來定位可能十分危險，當版面配置的大小已知的。 使用絕對位置時, 的一種大小螢幕中心中的項目無法位移在任何其他大小。 請務必測試您的應用程式跨各種螢幕大小的支援的裝置。

若要使用絕對配置值，設定`LayoutBounds`使用 （x，y） 座標和明確的大小，然後設定`LayoutFlags`至`None`。

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

## <a name="exploring-a-complex-layout"></a>瀏覽複雜的配置

每個配置有優點和缺點，用於建立特定配置。 在這一系列的版面配置文件中，整個範例應用程式已建立具有相同的頁面配置實作使用三個不同的版面配置。

請考慮下列 XAML:

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

上述程式碼會產生下列配置：

![](absolute-layout-images/abs.png "複雜 AbsoluteLayout")

請注意，由於在 Windows Phone 呈現按鈕的方式的差異，部分的圓形呈現已取代 boxviews 在 Windows Phone 的螢幕擷取畫面。

請注意， `AbsoluteLayout`s 巢狀的因為在某些情況下建立巢狀配置來得容易呈現相同的配置中的所有項目。



## <a name="related-links"></a>相關連結

- [透過 Xamarin.Forms，章 14 建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/)
- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
