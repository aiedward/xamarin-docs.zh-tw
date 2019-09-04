---
title: Xamarin. 表單樣式類別
description: '[Xamarin] 樣式類別可以讓多個樣式套用至控制項, 而不需要採用樣式繼承。'
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 4a353d64f0e7e29da6c64f93b8554c3661f4d389
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228135"
---
# <a name="xamarinforms-style-classes"></a>Xamarin. 表單樣式類別

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_[Xamarin] 樣式類別可以讓多個樣式套用至控制項, 而不需要採用樣式繼承。_

## <a name="create-style-classes"></a>建立樣式類別

藉由將[`Class`](xref:Xamarin.Forms.Style.Class) [`Style`](xref:Xamarin.Forms.Style)上的屬性設定為`string`表示類別名稱的, 即可建立樣式類別。 這項供應專案的優點是使用`x:Key`屬性[`VisualElement`](xref:Xamarin.Forms.VisualElement)來定義明確的樣式, 因此可以將多個樣式類別套用至。

> [!IMPORTANT]
> 多個樣式可以共用相同的類別名稱, 但前提是它們是以不同的類型為目標。 這可讓多個以相同方式命名的樣式類別, 以不同的類型為目標。

下列範例顯示三個[`BoxView`](xref:Xamarin.Forms.BoxView)樣式類別, 以及一個[`VisualElement`](xref:Xamarin.Forms.VisualElement)樣式類別:

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

、和樣式類別分別會將屬性[`BoxView`](xref:Xamarin.Forms.BoxView)設定為特定值。 `Circle` `Rounded` `Separator`

樣式類別`VisualElement`具有的[,`VisualElement`](xref:Xamarin.Forms.VisualElement)這表示它只能套用至實例。 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) `Rotated` 不過, 其[`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)屬性會設定為`true`, 以確保它可以套用至任何[`BoxView`](xref:Xamarin.Forms.BoxView)衍生自`VisualElement`的控制項, 例如。 如需將樣式套用至衍生類型的詳細資訊, 請參閱[將樣式套用至衍生類型](implicit.md#apply-a-style-to-derived-types)。

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

將控制項的[`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)屬性 (屬於類型`IList<string>`) 設定為樣式類別名稱的清單, 即可使用樣式類別。 將套用樣式類別, 前提是控制項的類型符合[`TargetType`](xref:Xamarin.Forms.Style.TargetType)樣式類別的。

下列範例顯示三[`BoxView`](xref:Xamarin.Forms.BoxView)個實例, 每個都設定為不同的樣式類別:

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

在此範例中, 第[`BoxView`](xref:Xamarin.Forms.BoxView)一個會將樣式設為行分隔符號, 而第`BoxView`三個則是迴圈。 第二`BoxView`個會套用兩個樣式類別, 讓它的圓角和旋轉45度:

![使用樣式類別樣式的 BoxViews](style-class-images/boxviews.png)

> [!IMPORTANT]
> 因為[`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass)屬性的類型`IList<string>`為, 所以可以將多個樣式類別套用至控制項。 發生這種情況時, 樣式類別會以遞增的清單順序套用。 因此, 當多個樣式類別設定相同的屬性時, 位於最高清單位置的樣式類別中的屬性將會優先。

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

- [基本的樣式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
