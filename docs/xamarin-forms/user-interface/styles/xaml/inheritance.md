---
title: 在 Xamarin.Forms 中的樣式繼承
description: 若要減少重複，並啟用重複使用其他樣式可以繼承樣式。 這篇文章說明如何在 Xamarin.Forms 應用程式執行樣式繼承。
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: c5fa46a67496123eb105ae12404d5607032f5ac1
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971154"
---
# <a name="style-inheritance-in-xamarinforms"></a>在 Xamarin.Forms 中的樣式繼承

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)

_若要減少重複，並啟用重複使用其他樣式可以繼承樣式。_

## <a name="style-inheritance-in-xaml"></a>在 XAML 中的樣式繼承

樣式繼承藉由設定[ `Style.BasedOn` ](xref:Xamarin.Forms.Style.BasedOn)屬性的現有[ `Style` ](xref:Xamarin.Forms.Style)。 在 XAML，做法是藉由設定`BasedOn`屬性，以`StaticResource`參考先前建立的標記延伸`Style`。 在 C# 中，做法是藉由設定`BasedOn`屬性設`Style`執行個體。

可以包含繼承自基底樣式的樣式[ `Setter` ](xref:Xamarin.Forms.Setter)執行個體，為新的屬性，或使用它們來覆寫的基底樣式的樣式。 此外，繼承自基底樣式的樣式都必須指向相同的型別或衍生自的基底樣式的目標類型的類型。 比方說，如果基底樣式目標[ `View` ](xref:Xamarin.Forms.View)執行個體，以基礎基底樣式的樣式可以將目標`View`執行個體或衍生自類型`View`類別，例如[ `Label`](xref:Xamarin.Forms.Label)並[ `Button` ](xref:Xamarin.Forms.Button)執行個體。

下列程式碼示範*明確*XAML 頁面中的樣式繼承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
            </Style>
            <Style x:Key="labelStyle" TargetType="Label"
                   BasedOn="{StaticResource baseStyle}">
                ...
                <Setter Property="TextColor" Value="Teal" />
            </Style>
            <Style x:Key="buttonStyle" TargetType="Button"
                   BasedOn="{StaticResource baseStyle}">
                <Setter Property="BorderColor" Value="Lime" />
                ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelStyle}" />
            ...
            <Button Text="So is the button"
                    Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

`baseStyle`目標[ `View` ](xref:Xamarin.Forms.View)執行個體，並設定[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性。 `baseStyle`未直接在任何控制項上設定。 相反地，`labelStyle`和`buttonStyle`繼承自它，設定額外的可繫結屬性值。 `labelStyle`並`buttonStyle`會接著套用至[ `Label` ](xref:Xamarin.Forms.Label)執行個體並[ `Button` ](xref:Xamarin.Forms.Button)執行個體，藉由設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性。 這會導致下列的螢幕擷取畫面所示的外觀：

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> 隱含樣式可以衍生自明確的樣式，但明確樣式無法衍生自隱含樣式。

### <a name="respecting-the-inheritance-chain"></a>尊重繼承鏈結

樣式只可以繼承樣式，在相同的層級，或以上版本，檢視階層中。 這表示：

- 應用程式層級資源只可以繼承自其他應用程式層級的資源。
- 頁面層級的資源可以繼承自應用程式層級的資源和其他頁面層級的資源。
- 控制層級的資源可以繼承自應用程式層級的資源、 頁面層級的資源和其他控制項的層級資源。

此繼承鏈結以下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="baseStyle" TargetType="View">
              ...
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelStyle" TargetType="Label" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                    <Style x:Key="buttonStyle" TargetType="Button" BasedOn="{StaticResource baseStyle}">
                      ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此範例中，`labelStyle`並`buttonStyle`是控制層級的資源，而`baseStyle`是頁面層級的資源。 不過，雖然`labelStyle`並`buttonStyle`繼承自`baseStyle`，不可能`baseStyle`繼承`labelStyle`或`buttonStyle`，因為它們各自的位置，檢視階層中。

## <a name="style-inheritance-in-c35"></a>在 C 中的樣式繼承&#35;

對等的 C# 頁面，其中[ `Style` ](xref:Xamarin.Forms.Style)執行個體直接指派給[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)所需的控制項的屬性會顯示在下列程式碼範例：

```csharp
public class StyleInheritancePageCS : ContentPage
{
    public StyleInheritancePageCS ()
    {
        var baseStyle = new Style (typeof(View)) {
            Setters = {
                new Setter {
                    Property = View.HorizontalOptionsProperty, Value = LayoutOptions.Center    },
                ...
            }
        };

        var labelStyle = new Style (typeof(Label)) {
            BasedOn = baseStyle,
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Teal    }
            }
        };

        var buttonStyle = new Style (typeof(Button)) {
            BasedOn = baseStyle,
            Setters = {
                new Setter { Property = Button.BorderColorProperty, Value =    Color.Lime },
                ...
            }
        };
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelStyle },
                ...
                new Button { Text = "So is the button", Style = buttonStyle }
            }
        };
    }
}
```

`baseStyle`目標[ `View` ](xref:Xamarin.Forms.View)執行個體，並設定[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性。 `baseStyle`未直接在任何控制項上設定。 相反地，`labelStyle`和`buttonStyle`繼承自它，設定額外的可繫結屬性值。 `labelStyle`並`buttonStyle`會接著套用至[ `Label` ](xref:Xamarin.Forms.Label)執行個體並[ `Button` ](xref:Xamarin.Forms.Button)執行個體，藉由設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
