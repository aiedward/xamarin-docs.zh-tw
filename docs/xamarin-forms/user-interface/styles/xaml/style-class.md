---
title: Xamarin.Forms 樣式類別
description: Xamarin.Forms 樣式類別可讓多個樣式套用至控制項，而不必樣式繼承。
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 2de21f19e2789c94c02b5d2e9c13fc06963e1a7f
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292798"
---
# <a name="xamarinforms-style-classes"></a>Xamarin.Forms 樣式類別

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_Xamarin.Forms 樣式類別可讓多個樣式套用至控制項，而不必樣式繼承。_

## <a name="create-style-classes"></a>建立樣式類別

樣式建立的類別可以藉由設定[ `Class` ](xref:Xamarin.Forms.Style.Class)上的屬性[ `Style` ](xref:Xamarin.Forms.Style)至`string`表示的類別名稱。 這提供比使用明確樣式定義的優點`x:Key`屬性中，為多個樣式類別，可以套用到[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)。

> [!IMPORTANT]
> 多個樣式可以共用相同的類別名稱，提供以不同的類型為目標。 這可讓多個樣式類別，名稱完全相同，不同類型的目標。

下列範例示範三個[ `BoxView` ](xref:Xamarin.Forms.BoxView)樣式類別，以及[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)樣式類別：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

`Separator`， `Rounded`，並`Circle`樣式的類別每一組[ `BoxView` ](xref:Xamarin.Forms.BoxView)設成特定值的屬性。

`Rotated`樣式類別具有[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)的[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)，這表示它只會套用至`VisualElement`執行個體。 不過，其[ `ApplyToDerivedTypes` ](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)屬性設定為`true`，以確保它可以套用至任何控制項衍生自`VisualElement`，例如[ `BoxView` ](xref:Xamarin.Forms.BoxView)。 如需有關如何將樣式套用至衍生類型的詳細資訊，請參閱[樣式套用至衍生型別](implicit.md#apply-a-style-to-derived-types)。

對等的 C# 程式碼是：

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>使用樣式類別

樣式類別，可供設定[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)控制項，也就是型別的屬性`IList<string>`，樣式類別名稱的清單。 也將會套用的樣式類別，前提是該控制項的型別符合[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)的樣式類別。

下列範例示範三個[ `BoxView` ](xref:Xamarin.Forms.BoxView)分別設為不同的樣式類別的執行個體：

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

在此範例中，第一個[ `BoxView` ](xref:Xamarin.Forms.BoxView)樣式是行分隔符號，而第三個`BoxView`是循環。 第二個`BoxView`有兩個樣式類別套用至其中，它讓 it 圓角邊角和旋轉 45 度：

![](style-class-images/boxviews.png "BoxViews 樣式的樣式類別")

> [!IMPORTANT]
> 多個樣式類別可以套用至控制項，因為[ `StyleClass` ](xref:Xamarin.Forms.VisualElement.StyleClass)屬性的類型是`IList<string>`。

對等的 C# 程式碼是：

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>相關連結

- [基本的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
