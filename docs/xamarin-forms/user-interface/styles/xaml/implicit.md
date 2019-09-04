---
title: 在 Xamarin.Forms 中的隱含樣式
description: 隱含樣式是由所有控制項都使用的相同 TargetType，而不需要每個控制項，以參考樣式。
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: cdbfaafdac8f965adaf4b840b568154e40ef7e10
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228179"
---
# <a name="implicit-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的隱含樣式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_隱含樣式是由所有控制項都使用的相同 TargetType，而不需要每個控制項，以參考樣式。_

## <a name="create-an-implicit-style-in-xaml"></a>在 XAML 中建立隱含樣式

若要宣告[ `Style` ](xref:Xamarin.Forms.Style)在頁面層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)必須加入至頁面並再一或多個`Style`宣告可以包含在`ResourceDictionary`。 A`Style`更為*隱含*不指定`x:Key`屬性。 要比對的視覺項目，然後套用樣式`TargetType`剛好，但不衍生自的項目`TargetType`值。

下列程式碼範例所示*隱含*樣式在 XAML 中宣告中的頁面`ResourceDictionary`，並套用至頁面的[ `Entry` ](xref:Xamarin.Forms.Entry)執行個體：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定義單一*隱含*套用至網頁的樣式[ `Entry` ](xref:Xamarin.Forms.Entry)執行個體。 `Style`用來在背景為黃色，顯示的藍色文字，同時也設定其他選項的外觀。 `Style`新增至頁面的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)而不指定`x:Key`屬性。 因此，`Style`套用至所有`Entry`因為它們符合隱含執行個體[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)屬性`Style`完全。 不過，`Style`不會套用至`CustomEntry`執行個體，也就是子類別化之`Entry`。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![隱含樣式範例](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

此外，第四個[ `Entry` ](xref:Xamarin.Forms.Entry)覆寫[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)並[ `TextColor` ](xref:Xamarin.Forms.Entry.TextColor)屬性的不同的隱含樣式`Color`值。

### <a name="create-an-implicit-style-at-the-control-level"></a>在控制項層級建立隱含樣式

除了建立*隱含*頁面層級的樣式，它們也可以建立在控制層級，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此範例中，*隱含* [ `Style` ](xref:Xamarin.Forms.Style)指派給[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)集合[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)控制項。 *隱含*樣式可以再套用到控制項和其子系。

如需建立應用程式中的樣式[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，請參閱[全域樣式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-implicit-style-in-c35"></a>在 C 中建立隱含樣式&#35;

[`Style`](xref:Xamarin.Forms.Style) 執行個體可以加入至網頁[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) C# 中建立新的集合[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，再以新增`Style`執行個體`ResourceDictionary`，如下所示下列程式碼範例：

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

建構函式定義單一*隱含*套用至網頁的樣式[ `Entry` ](xref:Xamarin.Forms.Entry)執行個體。 `Style`用來在背景為黃色，顯示的藍色文字，同時也設定其他選項的外觀。 `Style`新增至頁面的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)而不指定`key`字串。 因此，`Style`套用至所有`Entry`因為它們符合隱含執行個體[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)屬性`Style`完全。 不過，`Style`不會套用至`CustomEntry`執行個體，也就是子類別化之`Entry`。

## <a name="apply-a-style-to-derived-types"></a>將樣式套用至衍生類型

屬性可讓樣式套用至衍生自[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性所參考之基底類型的控制項。 [`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) 因此, 將此屬性設定`true`為可讓單一樣式以多個類型為目標, 前提是該類型衍生自`TargetType`屬性中所指定的基底類型。

下列範例顯示將[`Button`](xref:Xamarin.Forms.Button)實例的背景色彩設定為紅色的隱含樣式:

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

將此樣式放在頁面層級[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中, 會導致它套用至頁面[`Button`](xref:Xamarin.Forms.Button)上的所有實例, 也會套用至衍生自`Button`的任何控制項。 不過, 如果[`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)屬性保持未設定, 則樣式只會套用至`Button`實例。

對等的 C# 程式碼是：

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
