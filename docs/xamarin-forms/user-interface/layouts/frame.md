---
title: Xamarin. 表單框架
description: '[Xamarin] 框架類別是用來包裝視圖或版面配置的版面配置，其框線可使用色彩、陰影和其他選項進行設定。'
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
ms.openlocfilehash: 619b29a9d65594b1badd805c3361fe1a174d7174
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2019
ms.locfileid: "69976492"
---
# <a name="xamarinforms-frame"></a>Xamarin. 表單框架

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

[Xamarin] [`Frame`](xref:Xamarin.Forms.Frame)類別是用來包裝視圖的版面配置，其框線可使用色彩、陰影和其他選項進行設定。 框架通常用來在控制項周圍建立框線，但可用來建立更複雜的 UI。 如需詳細資訊，請參閱[先進的框架使用](#advanced-frame-usage)方式。

下列螢幕擷取畫面顯示 iOS 和 Android 上的 `Frame` 控制項：

[![iOS 和 Android 上的框架範例](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "IOS 和 Android 上的框架範例")

@No__t_0 類別會定義下列屬性：

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor)是 `Color` 值，可決定 `Frame` 框線的色彩。
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)是決定角落圓角半徑的 `float` 值。
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)是用來判斷框架是否有陰影的 `bool` 值。

這些屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，這表示 `Frame` 可以是資料系結的目標。

> [!NOTE]
> @No__t_0 屬性行為與平臺相依。 預設值為所有平臺上的 `true`。 不過，在 UWP 的投影上不會呈現。 投影會同時在 Android 和 iOS 上轉譯，但 iOS 上的投影會變暗，而且佔用更多空間。

## <a name="create-a-frame"></a>建立框架

@No__t_0 可以在 XAML 中具現化。 預設 `Frame` 物件有白色背景、陰影和沒有框線。 @No__t_0 物件通常會包裝另一個控制項。 下列範例顯示包裝 `Label` 物件的預設 `Frame`：

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

您也可以在程式碼中建立 `Frame`：

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

您可以在 XAML 中設定屬性，以自訂 `Frame` 物件的圓角、以色彩標示框線和陰影。 下列範例顯示自訂的 `Frame` 物件：

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

這些實例屬性也可以在程式碼中設定：

```csharp
Frame frame = new Frame
{
    BorderColor = Color.Orange,
    CornerRadius = 10,
    HasShadow = true,
    Content = new Label { Text = "Example" }
};
```

## <a name="advanced-frame-usage"></a>高級框架使用方式

@No__t_0 類別繼承自 `ContentView`，這表示它可以包含任何類型的 `View` 物件，包括 `Layout` 物件。 這種功能可讓 `Frame` 用來建立複雜的 UI 物件，例如卡片。

### <a name="create-a-card-with-a-frame"></a>建立包含框架的卡片

將 `Frame` 物件與 `Layout` 物件（例如 `StackLayout` 物件）結合，可讓您建立更複雜的 UI。 下列螢幕擷取畫面顯示使用 `Frame` 物件建立的範例卡片：

[![使用框架建立的卡片螢幕擷取畫面](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "使用框架建立的卡片螢幕擷取畫面")

下列 XAML 示範如何使用 `Frame` 類別來建立卡片：

```xaml
<Frame BorderColor="Gray"
       CornerRadius="5"
       Padding="8">
  <StackLayout>
    <Label Text="Card Example"
           FontSize="Medium"
           FontAttributes="Bold" />
    <BoxView Color="Gray"
             HeightRequest="2"
             HorizontalOptions="Fill" />
    <Label Text="Frames can wrap more complex layouts to create more complex UI components, such as this card!"/>
  </StackLayout>
</Frame>
```

您也可以在程式碼中建立卡片：

```csharp
Frame cardFrame = new Frame
{
    BorderColor = Color.Gray,
    CornerRadius = 5,
    Padding = 8,
    Content = new StackLayout
    {
        Children =
        {
            new Label
            {
                Text = "Card Example",
                FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label)),
                FontAttributes = FontAttributes.Bold
            },
            new BoxView
            {
                Color = Color.Gray,
                HeightRequest = 2,
                HorizontalOptions = LayoutOptions.Fill
            },
            new Label
            {
                Text = "Frames can wrap more complex layouts to create more complex UI components, such as this card!"
            }
        }
    }
};
```

### <a name="round-elements"></a>Round 元素

@No__t_1 控制項的 `CornerRadius` 屬性可以用來建立圓形影像。 下列螢幕擷取畫面顯示使用 `Frame` 物件建立的圓形影像範例：

[![ 「使用框架建立的圓形影像螢幕擷取畫面」](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "以框架建立之圓形影像的螢幕擷取畫面")

下列 XAML 顯示如何在 XAML 中建立圓形影像：

```xaml
<Frame Margin="10"
       BorderColor="Black"
       CornerRadius="50"
       HeightRequest="60"
       WidthRequest="60"
       IsClippedToBounds="True"
       HorizontalOptions="Center"
       VerticalOptions="Center">
  <Image Source="outdoors.jpg"
         Aspect="AspectFill"
         Margin="-20"
         HeightRequest="100"
         WidthRequest="100" />
</Frame>
```

您也可以在程式碼中建立圓形影像：

```csharp
Frame circleImageFrame = new Frame
{
    Margin = 10,
    BorderColor = Color.Black,
    CornerRadius = 50,
    HeightRequest = 60,
    WidthRequest = 60,
    IsClippedToBounds = true,
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center,
    Content = new Image
    {
        Source = ImageSource.FromFile("outdoors.jpg"),
        Aspect = Aspect.AspectFill,
        Margin = -20,
        HeightRequest = 100,
        WidthRequest = 100
    }
};
```

您必須將**室外**影像新增至每個平臺專案，而如何達成此目的會因平臺而異。 如需詳細資訊，請參閱[Xamarin 中的影像](~/xamarin-forms/user-interface/images.md)。

> [!NOTE]
> 圓角的行為在不同的平臺上有些許差異。 @No__t_0 物件的 `Margin` 應該是影像寬度和父框架寬度之間的一半差異，而且應該是負數，以便在 `Frame` 物件內平均置中影像。 不過，不保證要求的寬度和高度，因此 `Margin`、`HeightRequest` 和 `WidthRequest` 屬性可能需要根據您的影像大小和其他版面配置選項來變更。

## <a name="related-links"></a>相關連結

* [畫面格示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Xamarin 中的影像](~/xamarin-forms/user-interface/images.md)
