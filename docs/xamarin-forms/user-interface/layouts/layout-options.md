---
title: 在 Xamarin.Forms 中的版面配置選項
description: 每個 Xamarin.Forms 檢視具有 HorizontalOptions 和 VerticalOptions 屬性，類型 LayoutOptions。 這篇文章說明上對齊和展開的檢視具有 LayoutOptions 的每個值的效果。
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: 2e4fa5f1fb96077b0237dbeac9074006e761bc09
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655296"
---
# <a name="layout-options-in-xamarinforms"></a>在 Xamarin.Forms 中的版面配置選項

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)

_每個 Xamarin.Forms 檢視具有 HorizontalOptions 和 VerticalOptions 屬性，類型 LayoutOptions。這篇文章說明上對齊和展開的檢視具有 LayoutOptions 的每個值的效果。_

## <a name="overview"></a>總覽

[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)結構封裝兩個配置喜好設定：

- **對齊**– 檢視慣用的對齊方式，判斷其位置和其父系版面配置中的大小。
- **擴充**– 只要[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)，並指出是否檢視應該使用額外的空間，如果有的話。

這些配置喜好設定可套用至[ `View` ](xref:Xamarin.Forms.View)，相對於其父代，藉由設定[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)或是[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性`View`中的公用欄位的其中一個[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)結構。 公用的欄位如下所示：

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)
- [`StartAndExpand`](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)
- [`CenterAndExpand`](xref:Xamarin.Forms.LayoutOptions.CenterAndExpand)
- [`EndAndExpand`](xref:Xamarin.Forms.LayoutOptions.EndAndExpand)
- [`FillAndExpand`](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)

`Start`， `Center`， `End`，和`Fill`欄位用來定義父版面配置檢視的對齊方式：

- 水平對齊方式，如[ `Start` ](xref:Xamarin.Forms.LayoutOptions.Start)位置[ `View` ](xref:Xamarin.Forms.View)在左手邊的父系版面配置和垂直對齊方式，它會將`View`頂端父配置。
- 水平和垂直對齊[ `Center` ](xref:Xamarin.Forms.LayoutOptions.Center)水平或垂直中心[ `View` ](xref:Xamarin.Forms.View)。
- 水平對齊方式，如[ `End` ](xref:Xamarin.Forms.LayoutOptions.End)位置[ `View` ](xref:Xamarin.Forms.View)在右手邊的父系版面配置和垂直對齊方式，它會將`View`底部父代的版面配置。
- 水平對齊方式，如[ `Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill)確保[ `View` ](xref:Xamarin.Forms.View)填滿寬度的父系版面配置和垂直對齊方式，它可確保`View`填滿父配置的高度。

`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`值用來定義的對齊喜好設定，以及是否檢視會佔用更多空間如果有的話，父代內[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。

> [!NOTE]
> 檢視的預設值[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性是[ `LayoutOptions.Fill` ](xref:Xamarin.Forms.LayoutOptions.Fill)。

<a name="alignment" />

## <a name="alignment"></a>對齊

對齊方式可讓您控制如何檢視放置在其父代版面配置時父系版面配置包含未使用的空間 （也就是父配置會大於其所有子系的合併大小）。

A [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)只會遵守`Start`， `Center`， `End`，以及`Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)上會以相反方向的子檢視的欄位若要`StackLayout`方向。 因此，子檢視的垂直方向`StackLayout`可以設定其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)屬性的其中一個`Start`， `Center`， `End`，或`Fill`欄位。 同樣地，子檢視的水平方向`StackLayout`可以設定其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性的其中一個`Start`， `Center`， `End`，或`Fill`欄位。

A [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)不會遵守`Start`， `Center`， `End`，以及`Fill` [ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)上位於相同的方向，為的子檢視的欄位`StackLayout`方向。 因此，垂直方向`StackLayout`會忽略`Start`， `Center`， `End`，或`Fill`欄位上設定如果[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)子檢視的屬性。 同樣地，水平方向`StackLayout`會忽略`Start`， `Center`， `End`，或`Fill`欄位上設定如果[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)子檢視的屬性。

> [!NOTE]
> [`LayoutOptions.Fill`](xref:Xamarin.Forms.LayoutOptions.Fill) 覆寫調整要求使用指定的大小通常[ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)並[ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest)屬性。

下列 XAML 程式碼範例示範垂直方向[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)其中每個子系[ `Label` ](xref:Xamarin.Forms.Label)設定其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)屬性其中的四個對齊欄位[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)結構：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <Label Text="Start" BackgroundColor="Gray" HorizontalOptions="Start" />
  <Label Text="Center" BackgroundColor="Gray" HorizontalOptions="Center" />
  <Label Text="End" BackgroundColor="Gray" HorizontalOptions="End" />
  <Label Text="Fill" BackgroundColor="Gray" HorizontalOptions="Fill" />
</StackLayout>
```

對等的 C# 程式碼如下所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
    new Label { Text = "Start", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Start },
    new Label { Text = "Center", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Center },
    new Label { Text = "End", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.End },
    new Label { Text = "Fill", BackgroundColor = Color.Gray, HorizontalOptions = LayoutOptions.Fill }
  }
};
```

程式碼會產生下列的螢幕擷取畫面所示的版面配置：

[![](layout-options-images/alignment.png "對齊配置選項")](layout-options-images/alignment-large.png#lightbox "對齊配置選項")

<a name="expansion" />

## <a name="expansion"></a>擴充

擴充控制項的檢視是否會佔用更多空間，是否有的話，內[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 如果`StackLayout`包含未使用的空間 (也就是`StackLayout`大於所有子系的合併大小)，未使用的空間會藉由設定要求的擴充的所有子檢視的平均共用其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)或是[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性，以[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)使用欄位`AndExpand`後置詞。 請注意，當中的所有空間`StackLayout`是展開選項使用，會有任何作用。

A [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)只能同時展開子檢視其方向的方向。 因此，垂直方向`StackLayout`可以展開子檢視設定其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性的其中一個`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`欄位，如果`StackLayout`包含未使用的空間。 同樣地，水平方向`StackLayout`可以展開子檢視設定其[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)屬性的其中一個`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`欄位，如果`StackLayout`包含未使用的空間。

A [ `StackLayout` ](xref:Xamarin.Forms.StackLayout)無法展開的方向相對於容量及其方向中的子檢視。 因此，在垂直方向`StackLayout`，將[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)子檢視的屬性[ `StartAndExpand` ](xref:Xamarin.Forms.LayoutOptions.StartAndExpand)具有相同的效果與將屬性設定為[`Start`](xref:Xamarin.Forms.LayoutOptions.Start).

> [!NOTE]
> 請注意，啟用擴充不會變更檢視的大小除非它使用[ `LayoutOptions.FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)。

下列 XAML 程式碼範例示範垂直方向[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)其中每個子系[ `Label` ](xref:Xamarin.Forms.Label)設定其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性中的四種的擴充欄位的其中一個[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)結構：

```xaml
<StackLayout Margin="0,20,0,0">
  ...
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Start" BackgroundColor="Gray" VerticalOptions="StartAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Center" BackgroundColor="Gray" VerticalOptions="CenterAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="End" BackgroundColor="Gray" VerticalOptions="EndAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
  <Label Text="Fill" BackgroundColor="Gray" VerticalOptions="FillAndExpand" />
  <BoxView BackgroundColor="Red" HeightRequest="1" />
</StackLayout>
```

對等的 C# 程式碼如下所示：

```csharp
Content = new StackLayout
{
  Margin = new Thickness(0, 20, 0, 0),
  Children = {
    ...
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
```

程式碼會產生下列的螢幕擷取畫面所示的版面配置：

[![](layout-options-images/expansion.png "擴充的版面配置選項")](layout-options-images/expansion-large.png#lightbox "擴充版面配置選項")

每個[ `Label` ](xref:Xamarin.Forms.Label)佔用相同數量的空間內[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。 不過，只有最終`Label`，集合及其[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性設[ `FillAndExpand` ](xref:Xamarin.Forms.LayoutOptions.FillAndExpand)有不同的大小。 此外，每個`Label`分隔的一個小型的紅色[ `BoxView` ](xref:Xamarin.Forms.BoxView)，可使用的空間`Label`佔用輕鬆檢視。

## <a name="summary"></a>總結

這篇文章所述的效果，每個[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions)結構值的對齊方式和展開的檢視，相對於其父代。 `Start`， `Center`， `End`，和`Fill`欄位用來定義檢視的對齊方式為父系版面配置，而`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`欄位用來定義對齊喜好設定，並判斷檢視是否會佔用更多空間，是否有的話，內[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)。



## <a name="related-links"></a>相關連結

- [LayoutOptions （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layoutoptions)
- [LayoutOptions](xref:Xamarin.Forms.LayoutOptions)
