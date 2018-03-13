---
title: LayoutOptions
description: "Xamarin.Forms 中的每個檢視有 HorizontalOptions 並 VerticalOptions 屬性的型別 LayoutOptions。 這篇文章會說明每個 LayoutOptions 值對於對齊和展開的檢視效果。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7CAB5631-5153-4DEF-8AD7-C6011CE44307
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/10/2017
ms.openlocfilehash: a2aa143d5aeb801cd753dd99718ca9cf6dd72353
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="layoutoptions"></a>LayoutOptions

_Xamarin.Forms 中的每個檢視有 HorizontalOptions 並 VerticalOptions 屬性的型別 LayoutOptions。這篇文章會說明每個 LayoutOptions 值對於對齊和展開的檢視效果。_

## <a name="overview"></a>總覽

[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)結構封裝兩個配置喜好設定：

- **對齊**– 檢視慣用的對齊方式，判斷其位置和大小，其父代配置中的。
- **擴充**– 只要[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)，並指出是否檢視應該使用多餘的空間，如果有的話。

這些配置喜好設定可以套用至[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)、 相對於其父代，藉由設定[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)或[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性`View`至其中的公用欄位[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)結構。 公用的欄位如下：

- [`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)
- [`Center`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)
- [`End`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)
- [`Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)
- [`StartAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)
- [`CenterAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.CenterAndExpand/)
- [`EndAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.EndAndExpand/)
- [`FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)

`Start`， `Center`， `End`，和`Fill`欄位可用來定義父配置中的檢視表的對齊方式：

- 水平對齊方式， [ `Start` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/)位置[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)左側父版面配置，並對垂直對齊方式，它會將放`View`頂端父配置。
- 水平和垂直對齊方式， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Center/)水平或垂直置[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)。
- 水平對齊方式， [ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.End/)位置[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)在右手邊的父版面配置和垂直對齊方式，它會將放`View`底部父版面配置。
- 水平對齊方式， [ `Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)可確保[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)填滿寬度的父代的配置，以及垂直對齊方式，它可以確保`View`填滿父配置的高度。

`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`值可用來定義對齊喜好設定和是否檢視會佔用較多的空間如果有的話，在父系[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。

> [!NOTE]
> 檢視的預設值[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性是[ `LayoutOptions.Fill` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/)。

<a name="alignment" />

## <a name="alignment"></a>對齊

對齊控制項如何檢視放置在其父代配置父版面配置包含未使用的空間時 （也就是父配置大於所有子系的合併大小）。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)只尊重`Start`， `Center`， `End`，和`Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)在相反方向中的子檢視上的欄位若要`StackLayout`方向。 因此，子檢視的垂直方向`StackLayout`可以設定其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)屬性的其中一個`Start`， `Center`， `End`，或`Fill`欄位。 同樣地，子檢視的水平方向`StackLayout`可以設定其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性的其中一個`Start`， `Center`， `End`，或`Fill`欄位。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)不會遵守`Start`， `Center`， `End`，和`Fill` [ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)位於方向會與相同的子檢視上的欄位`StackLayout`方向。 垂直方向因此`StackLayout`忽略`Start`， `Center`， `End`，或`Fill`欄位上設定[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)子檢視的屬性。 水平方向同樣地，`StackLayout`忽略`Start`， `Center`， `End`，或`Fill`欄位上設定[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)子檢視的屬性。

> [!NOTE]
> [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) 通常覆寫大小使用指定的要求[ `HeightRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.HeightRequest/)和[ `WidthRequest` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.WidthRequest/)屬性。

下列 XAML 程式碼範例示範垂直方向[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)其中每個子系[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)設定其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)屬性其中一個的四個對齊欄位[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)結構：

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

程式碼會產生下列螢幕擷取畫面所示的版面配置：

[![](layout-options-images/alignment.png "對齊版面配置選項")](layout-options-images/alignment-large.png#lightbox "對齊版面配置選項")

<a name="expansion" />

## <a name="expansion"></a>擴充

擴充控制項的檢視是否會佔用較多空間，是否有的話，內[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。 如果`StackLayout`包含未使用的空間 (亦即`StackLayout`大於所有子系的合併大小)，未使用的空間為平均共用所有的子檢視，藉由設定要求擴充其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)或[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)使用欄位`AndExpand`後置詞。 請注意，當中的所有空間`StackLayout`是使用中，展開選項沒有任何作用。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)才可以展開其方向的方向子檢視。 垂直方向因此`StackLayout`可以擴充設定的子檢視其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性的其中一個`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`欄位，如果`StackLayout`包含未使用的空間。 水平方向同樣地，`StackLayout`可以擴充設定的子檢視其[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)屬性的其中一個`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，或`FillAndExpand`欄位，如果`StackLayout`包含未使用的空間。

A [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)無法展開子檢視，以其方向的方向。 因此，在垂直方向`StackLayout`，設定[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)子檢視的屬性[ `StartAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.StartAndExpand/)具有相同的效果與將屬性設定為[`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Start/).

> [!NOTE]
> 請注意，啟用擴充不會變更檢視的大小除非它使用[ `LayoutOptions.FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)。

下列 XAML 程式碼範例示範垂直方向[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)其中每個子系[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)設定其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性四個的擴充欄位的其中一個[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)結構：

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

程式碼會產生下列螢幕擷取畫面所示的版面配置：

[![](layout-options-images/expansion.png "展開版面配置選項")](layout-options-images/expansion-large.png#lightbox "展開版面配置選項")

每個[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)所佔的空間內相同的量[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。 不過，只有最終`Label`，將其[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性[ `FillAndExpand` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/)有不同的大小。 此外，每個`Label`分隔的一個小型的紅色[ `BoxView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BoxView/)，可讓空間`Label`佔用輕鬆地檢視。

## <a name="summary"></a>總結

這篇文章解釋影響每個[ `LayoutOptions` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)結構值的對齊方式和擴充的檢視，相對於其父代。 `Start`， `Center`， `End`，和`Fill`欄位可用來定義父版面配置中的檢視表的對齊方式和`StartAndExpand`， `CenterAndExpand`， `EndAndExpand`，和`FillAndExpand`欄位可用來定義對齊喜好設定，並判斷檢視是否會佔用較多空間，是否有的話，內[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)。



## <a name="related-links"></a>相關連結

- [LayoutOptions （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/layoutoptions/)
- [LayoutOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.LayoutOptions/)
