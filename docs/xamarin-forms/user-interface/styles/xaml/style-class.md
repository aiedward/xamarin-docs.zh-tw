---
title: Xamarin.Forms 樣式類別
description: Xamarin.Forms 樣式類別可將多個樣式套用至控制項，而不需要使用樣式繼承。
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: abe0b4804cdcca7fa5864dbadbfa2254b6c8c469
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366473"
---
# <a name="no-locxamarinforms-style-classes"></a>Xamarin.Forms 樣式類別

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Xamarin.Forms 樣式類別可將多個樣式套用至控制項，而不需要使用樣式繼承。_

## <a name="create-style-classes"></a>建立樣式類別

您可以藉由將中的屬性設定 [`Class`](xref:Xamarin.Forms.Style.Class) [`Style`](xref:Xamarin.Forms.Style) 為代表類別名稱的來建立樣式類別 `string` 。 這項功能的優點，就是使用屬性定義明確的樣式 `x:Key` ，因此可以將多個樣式類別套用至 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 。

> [!IMPORTANT]
> 多個樣式可以共用相同的類別名稱，前提是它們的目標不同類型。 這可讓多個名稱相同的樣式類別，以不同的類型為目標。

下列範例顯示三個 [`BoxView`](xref:Xamarin.Forms.BoxView) 樣式類別和一個 [`VisualElement`](xref:Xamarin.Forms.VisualElement) 樣式類別：

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

`Separator`、 `Rounded` 和 `Circle` 樣式類別會將屬性設定 [`BoxView`](xref:Xamarin.Forms.BoxView) 為特定值。

`Rotated`樣式類別具有 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 的 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，這表示它只能套用至 `VisualElement` 實例。 不過，其 [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) 屬性會設定為 `true` ，以確保它可以套用至衍生自的任何控制項 `VisualElement` ，例如 [`BoxView`](xref:Xamarin.Forms.BoxView) 。 如需將樣式套用至衍生類型的詳細資訊，請參閱 [將樣式套用至衍生類型](implicit.md#apply-a-style-to-derived-types)。

對等的 C# 程式碼為：

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

您可以將 [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) 控制項的屬性（類型 `IList<string>` ）設定為樣式類別名稱的清單，藉以使用樣式類別。 如果控制項的類型符合樣式類別的，則會套用樣式類別 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 。

下列範例顯示三個 [`BoxView`](xref:Xamarin.Forms.BoxView) 實例，每個實例都設定為不同的樣式類別：

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

在此範例中，第一個 [`BoxView`](xref:Xamarin.Forms.BoxView) 會將樣式設為行分隔符號，而第三個 `BoxView` 是圓形。 第二個 `BoxView` 樣式類別會套用至它，它會提供圓角並旋轉45度：

![BoxViews 樣式類別的樣式](style-class-images/boxviews.png)

> [!IMPORTANT]
> 您可以將多個樣式類別套用至控制項，因為 [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) 屬性的型別為 `IList<string>` 。 發生這種情況時，會以遞增的清單順序套用樣式類別。 因此，當多個樣式類別設定相同的屬性時，位於最高清單位置之 style 類別中的屬性將會優先。

對等的 C# 程式碼為：

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

- [ (範例) 的基本樣式 ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)