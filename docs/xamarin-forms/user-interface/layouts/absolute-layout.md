---
title: Xamarin.Forms AbsoluteLayout
description: 這篇文章說明如何使用 Xamarin.Forms AbsoluteLayout 類別來建立像素完美的 Ui。 此類別會定位和調整大小成正比，其本身的大小和位置，或由絕對值的子項目。
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: 0d49b8c50db08ad07952425492591ee246de4f8b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998344"
---
# <a name="xamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) 位置，和大小成正比，其本身的大小和位置，或由絕對值的子項目。 子檢視可能會定位和調整大小按比例的值或靜態值，使用依比例，並可以混合使用靜態值。

[![](absolute-layout-images/layouts-sml.png "Xamarin.Forms 版面配置")](absolute-layout-images/layouts.png#lightbox "Xamarin.Forms 版面配置")

本文將介紹：

- **[用途](#Purpose)** &ndash;的常見用法`AbsoluteLayout`。
- **[使用方式](#Usage)** &ndash;如何使用`AbsoluteLayout`以達到您所需的設計。
  - **[按比例配置](#Proportional_Layouts)** &ndash;了解如何按比例的值用於`AbsoluteLayout`。
  - **[指定值](#Specifying_Values)** &ndash;了解如何指定比例和絕對值。
  - **[調和間距值](#Proportional_Values)** &ndash;了解如何按比例的值運作。
    - **[絕對值](#Absolute_Values)** &ndash;了解絕對值的運作方式。

<a name="Purpose" />

## <a name="purpose"></a>用途

因為定位模型`AbsoluteLayout`，版面配置會變得相對簡單，讓它們使用的版面配置時，任何一側排清或置中定位項目。 調和間距大小和位置中的項目`AbsoluteLayout`可自動調整為任何檢視大小。 只有位置，但不是大小應該調整其中的項目，可以混合使用絕對和比例的值。

`AbsoluteLayout` 您可以使用任何位置項目必須放置在檢視和對齊邊緣的項目時就特別有用。

<a name="Usage" />

## <a name="usage"></a>使用量

<a name="Proportional_Layouts" />

### <a name="proportional-layouts"></a>按比例配置

`AbsoluteLayout` 具有唯一的錨點模型，讓錨點的項目相對於其項目，該項目位置相對於配置所使用的比例定位時。 使用絕對位置時，錨點會在 (0，0) 位於檢視。 這有兩個重要的結果：

- 項目無法放置螢幕使用依比例的值。
- 可以可靠地放置項目，一起配置的或在管理中心中，無論規模大小的版面配置或裝置。

`AbsoluteLayout`例如`RelativeLayout`，能夠放置項目，使它們的重疊。

請注意，在下列螢幕擷取畫面，方塊的錨點是白色的點。 在配置的移動，請注意，錨點與方塊之間的關聯性：

![](absolute-layout-images/anchor-start.png "在開頭的錨點")
![](absolute-layout-images/anchor-center.png "錨點中心")
![](absolute-layout-images/anchor-end.png "結尾的錨點")

<a name="Specifying_Values" />

### <a name="specifying-values"></a>指定的值

檢視內`AbsoluteLayout`位於使用四個值：

- **X** &ndash;檢視的錨定的 x （水平） 位置
- **Y** &ndash;檢視的錨定的 y （垂直） 位置
- **寬度**&ndash;檢視的寬度
- **高度**&ndash;檢視的高度

每個這些值可以設定為[調和間距](#Proportional_Values)值，或有[絕對](#Absolute_Values)值。

值會指定為繫結和旗標的組合。 `LayoutBounds` 已[ `Rectangle` ](xref:Xamarin.Forms.Rectangle)四個值所組成： `x`， `y`， `width`， `height`。

### <a name="absolutelayoutflags"></a>AbsoluteLayoutFlags

[`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) 指定值解譯的方式，並具有下列預先定義的選項：

- **無**&ndash;解譯為絕對值的所有值。 如果不指定了任何版面配置的旗標，這是預設值。
- **所有**&ndash;會解譯成比例的所有值。
- **WidthProportional** &ndash;解譯`Width`依比例和所有其他值的值為絕對值。
- **HeightProportional** &ndash;只高度會將該值解譯為調和間距與所有其他絕對的值。
- **XProportional** &ndash;解譯`X`值成比例，同時將所有其他值視為絕對。
- **YProportional** &ndash;解譯`Y`值成比例，同時將所有其他值視為絕對。
- **PositionProportional** &ndash;解譯`X`和`Y`值做為成正比，而大小值會解譯為絕對值。
- **SizeProportional** &ndash;解譯`Width`和`Height`絕對位置的值時，為調和間距值。

在 XAML 中，繫結和旗標設定為版面配置中的檢視定義的一部分使用`AbsoluteLayout.LayoutBounds`屬性。 範圍設定為值的逗號分隔的清單`X`， `Y`， `Width`，和`Height`，依此順序。 旗標也會在版面配置中使用檢視的宣告中指定`AbsoluteLayout.LayoutFlags`屬性。 請注意，可以在 XAML 使用逗號分隔的清單中合併旗標。 參考下列範例：

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

- 在管理中心的標籤會位於使用絕對大小和位置的值。 因此，它會顯示在 iPhone 4 秒置中和低，但不是置中較大的裝置上。
- 底部的版面配置的文字會位於使用調和間距大小和位置的值。 它一定會出現在底端中央的版面配置，但其大小會隨著較大的配置大小。
- 三個彩色`BoxView`s 置放於使用依比例的位置和絕對大小在畫面上、 左和右邊緣。

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

調和間距的值會定義版面配置和檢視之間的關聯性。 此關聯性定義的子檢視的位置或小數位數值為父系版面配置的對應值的比例。 這些值會表示為`double`秒，介於 0 和 1 之間的值。

位置和配置內的大小檢視會使用依比例的值。 因此，當檢視表的寬度設定為所佔百分比時，產生的寬度值會是乘以比例`AbsoluteLayout`的寬度。 例如，使用`AbsoluteLayout`寬度的`500`會 250 (500 x.5 上檢視，設為 有.5，呈現檢視的寬度比例寬度。

若要使用依比例的值，請設定`LayoutBounds`使用 （x，y） 比例和調和間距大小，然後設定`LayoutFlags`至`All`。

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

絕對值明確定義檢視的配置中的所在。 不同於成比例的值，絕對值則是能夠定位和調整大小不符的版面配置的範圍內的檢視項目。

使用絕對值來定位可能會造成危險，當配置的大小未知。 當使用絕對位置，在一個大小螢幕的中央中的項目可以位移任何其他大小。 請務必測試您的應用程式跨各種螢幕大小的支援的裝置。

若要使用絕對版面配置的值，請設定`LayoutBounds`使用 （x，y） 座標和明確的大小，然後設定`LayoutFlags`至`None`。

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

每個配置有優點和缺點，用於建立特定的配置。 在此系列的版面配置的文章中，已建立範例應用程式以使用三個不同的版面配置實作相同的頁面配置。

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

請注意， `AbsoluteLayout`s 為巢狀，因為在某些情況下建立巢狀版面配置可能很容易呈現在相同的版面配置內的所有項目。

## <a name="related-links"></a>相關連結

- [使用 Xamarin.Forms 時，第 14 章建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter14.pdf)
- [AbsoluteLayout](xref:Xamarin.Forms.AbsoluteLayout)
- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
