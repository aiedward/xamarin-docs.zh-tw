---
title: StackLayout
description: "您可以使用 StackLayout 呈現的檢視集合在一個維度。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A91EA70-268C-462C-AAAF-F8DA011403F8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: b2d89fd6f9030864931395db00bd6f6321b7fbf9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="stacklayout"></a>StackLayout

`StackLayout` 水平或垂直組織中的一維的行 （「 堆疊 」） 的檢視。 在中檢視`StackLayout`可以使用版面配置中的空間大小。 位置是由檢視加入至版面配置和檢視的配置選項順序來決定。

[ ![](stack-layout-images/layouts-sml.png "Xamarin.Forms 配置")](stack-layout-images/layouts.png "Xamarin.Forms 版面配置")

## <a name="purpose"></a>用途

`StackLayout` 是較不複雜，比其他檢視。 只要加入檢視，可以建立簡單的線性介面`StackLayout`，及建立巢狀成更複雜的介面。

## <a name="usage--behavior"></a>使用狀況 & 行為

### <a name="spacing"></a>間距

根據預設，`StackLayout`會加入檢視之間 6px 邊界。 這可以控制或設定成有沒有邊界設定`Spacing`StackLayout 上的屬性。 下列示範如何設定間距和其他間距選項的效果：

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout Spacing="10" x:Name="layout">
      <Button Text="StackLayout" VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView Color="Yellow" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView Color="Green" VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" Color="Blue" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在 C# 中：

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { Text = "StackLayout", VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var yellowBox = new BoxView { Color = Color.Yellow, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var greenBox = new BoxView { Color = Color.Green, VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var blueBox = new BoxView { Color = Color.Blue, VerticalOptions = LayoutOptions.FillAndExpand,
      HorizontalOptions = LayoutOptions.FillAndExpand, HeightRequest = 75 };

    layout.Children.Add(button);
    layout.Children.Add(yellowBox);
    layout.Children.Add(greenBox);
    layout.Children.Add(blueBox);
    layout.Spacing = 10;
    Content = layout;
  }
}
```

間距 = 0:

![](stack-layout-images/spacing-zero.png "以間距 StackLayout = 0")

10 的間距：

![](stack-layout-images/spacing-ten.png "以間距 StackLayout = 10")

### <a name="sizing"></a>調整大小

StackLayout 中之檢視的大小取決於高度和寬度的要求和版面配置選項。 `StackLayout` 將會強制執行填補。 下列`LayoutOption`s 會佔用一樣多的空間，因為從配置可用的檢視：

- **CenterAndExpand** &ndash;配置中將檢視，並展開佔用的空間配置會提供它。
- **EndAndExpand** &ndash;會將檢視的配置 （下方或最右邊的界限） 結尾，並會展開，以佔用的空間配置會提供如。
- **FillAndExpand** &ndash;不具有任何填補，使其佔用的空間配置會提供它會將檢視。
- **StartAndExpand** &ndash;會將檢視在版面配置的開頭，並佔用的空間會提供父。

如需詳細資訊，請參閱[擴充](~/xamarin-forms/user-interface/layouts/layout-options.md#expansion)。

### <a name="positioning"></a>定位

StackLayout 中的檢視可以放置並調整大小使用`LayoutOptions`。 每個檢視可以有`VerticalOptions`和`HorizontalOptions`，定義如何檢視將定位本身相對於版面配置。 下列預先定義`LayoutOptions`可用：

- **Center** &ndash;配置中的將檢視置。
- **結束**&ndash;放置檢視的配置 （下方或最右邊的界限） 結尾。
- **填滿**&ndash;會放在檢視，使其具有任何填補。
- **啟動**&ndash;會檢視放在配置的開頭。

下列程式碼示範如何設定版面配置選項：

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.StackLayoutDemo"
Title="StackLayout Demo">
  <ContentPage.Content>
    <StackLayout x:Name="layout">
      <Button VerticalOptions="Start"
        HorizontalOptions="FillAndExpand" />
      <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView VerticalOptions="FillAndExpand"
        HorizontalOptions="FillAndExpand" />
            <BoxView HeightRequest="75" VerticalOptions="End"
        HorizontalOptions="FillAndExpand" />
    </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在 C# 中：

```csharp
public class StackLayoutCode : ContentPage
{
  public StackLayoutCode ()
  {
    var layout = new StackLayout ();
    var button = new Button { VerticalOptions = LayoutOptions.Start,
     HorizontalOptions = LayoutOptions.FillAndExpand };
    var oneBox = new BoxView { VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var twoBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };
    var threeBox = new BoxView {  VerticalOptions = LayoutOptions.FillAndExpand, HorizontalOptions = LayoutOptions.FillAndExpand };

    layout.Children.Add(button);
    layout.Children.Add(oneBox);
    layout.Children.Add(twoBox);
    layout.Children.Add(threeBox);
    Content = layout;
  }
}
```

如需詳細資訊，請參閱[對齊](~/xamarin-forms/user-interface/layouts/layout-options.md#alignment)。

## <a name="exploring-a-complex-layout"></a>瀏覽複雜的配置

每個配置有優點和缺點，用於建立特定配置。 在這一系列的版面配置文件中，整個範例應用程式已建立具有相同的頁面配置實作使用三個不同的版面配置。

請考慮下列 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.StackLayoutPage"
BackgroundColor="Maroon"
Title="StackLayouts">
    <ContentPage.Content>
    <ScrollView>
      <StackLayout Spacing="0" Padding="0" BackgroundColor="Maroon">
        <BoxView HorizontalOptions="FillAndExpand" HeightRequest="100"
          VerticalOptions="Start" Color="Gray" />
        <Button BorderRadius="30" HeightRequest="60" WidthRequest="60"
          BackgroundColor="Red" HorizontalOptions="Center" VerticalOptions="Start" />
        <StackLayout HeightRequest="100" VerticalOptions="Start" HorizontalOptions="FillAndExpand" Spacing="20" BackgroundColor="Maroon">
          <Label Text="User Name" FontSize="28" HorizontalOptions="Center"
            VerticalOptions="Center" FontAttributes="Bold" />
          <Entry Text="Bio + Hashtags" TextColor="White"
            BackgroundColor="Maroon" HorizontalOptions="FillAndExpand" VerticalOptions="CenterAndExpand" />
        </StackLayout>
        <StackLayout Orientation="Horizontal" HeightRequest="50" BackgroundColor="White" Padding="5">
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="Start">
            <BoxView BackgroundColor="Black" WidthRequest="40" HeightRequest="40"  HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
          <StackLayout Spacing="0" BackgroundColor="White" Orientation="Horizontal" HorizontalOptions="EndAndExpand">
            <BoxView BackgroundColor="Maroon" WidthRequest="40" HeightRequest="40" HorizontalOptions="Start" VerticalOptions="Center" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color" HorizontalOptions="StartAndExpand" VerticalOptions="Center" />
          </StackLayout>
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Age:" TextColor="White" HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="35" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Interests:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100" />
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
        <StackLayout Orientation="Horizontal">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
          HorizontalOptions="Start" VerticalOptions="Center" WidthRequest="100"/>
          <Entry HorizontalOptions="FillAndExpand" Text="Xamarin, C#, .NET, Mono..." TextColor="White" BackgroundColor="Maroon" />
        </StackLayout>
      </StackLayout>
    </ScrollView>
    </ContentPage.Content>
</ContentPage>

```

上述程式碼會產生下列配置：

![](stack-layout-images/stack.png "複雜 StackLayout")

請注意，由於在 Windows Phone 呈現按鈕的方式的差異，部分的圓形呈現已取代 boxviews 在 Windows Phone 的螢幕擷取畫面。

請注意， `StackLayouts`s 巢狀的因為在某些情況下建立巢狀配置來得容易呈現相同的配置中的所有項目。 也請注意，因為`StackLayout`不支援重疊的項目頁面不具有某些配置細中找到其他配置頁面。



## <a name="related-links"></a>相關連結

- [LayoutOptions](~/xamarin-forms/user-interface/layouts/layout-options.md)
- [版面配置 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [BusinessTumble 範例 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
