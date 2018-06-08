---
title: 樣式繼承
description: 樣式可以繼承自其他樣式來減少重複，並允許重複使用。
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 7b489e90daec2659a6d11b2776731582bdf368ff
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848417"
---
# <a name="style-inheritance"></a>樣式繼承

_樣式可以繼承自其他樣式來減少重複，並允許重複使用。_

## <a name="style-inheritance-in-xaml"></a>在 XAML 中的樣式繼承

樣式繼承藉由設定[ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)屬性至現有[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)。 在 XAML 中，這藉由設定`BasedOn`屬性`StaticResource`參考先前建立的標記延伸`Style`。 在 C# 中，這藉由設定`BasedOn`屬性`Style`執行個體。

可以包含繼承自基底樣式的樣式[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)執行個體為新的屬性，或使用它們來覆寫基底樣式的樣式。 此外，繼承自基底樣式的樣式必須在相同的型別或衍生自的基底樣式的目標類型的型別目標。 例如，如果基底樣式的目標[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)情況下，目標可以為基礎的基底樣式的樣式`View`執行個體或衍生自類型`View`類別，例如[ `Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)和[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)執行個體。

下列程式碼示範*明確*XAML 頁面中設定樣式繼承：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
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

`baseStyle`目標[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)例項，並設定[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性。 `baseStyle`未設定任何控制項上直接。 相反地，`labelStyle`和`buttonStyle`從它繼承，設定其他可繫結的屬性值。 `labelStyle`和`buttonStyle`接著會套用到[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體和[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)執行個體，藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。 這會導致下列螢幕擷取畫面所示的外觀：

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> 隱含樣式可以衍生自明確樣式，但明確樣式不能衍生自隱含樣式。

### <a name="respecting-the-inheritance-chain"></a>尊重繼承鏈結

樣式只可以繼承自樣式相同層級，或以上版本，檢視階層中。 這表示：

- 應用程式層級資源只可以繼承自其他應用程式層級資源。
- 頁面層級的資源可以繼承自應用程式層級的資源，以及其他的頁面層級資源。
- 控制層級的資源可以繼承自應用程式層級的資源、 頁面層級資源和其他控制項的層級資源。

下列程式碼範例示範此繼承鏈結：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.StyleInheritancePage" Title="Inheritance" Icon="xaml.png">
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

在此範例中，`labelStyle`和`buttonStyle`會控制層級的資源，而`baseStyle`為頁面層級的資源。 但是，當`labelStyle`和`buttonStyle`繼承自`baseStyle`，不可能`baseStyle`繼承自`labelStyle`或`buttonStyle`，因為檢視階層中其各自的位置。

## <a name="style-inheritance-in-c35"></a>在 C 中的樣式繼承&#35;

對等的 C# 頁面上，其中[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)直接指派給執行個體[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性的所需的控制項，顯示在下列程式碼範例：

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

`baseStyle`目標[ `View` ](https://developer.xamarin.com/api/type/Xamarin.Forms.View/)例項，並設定[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性。 `baseStyle`未設定任何控制項上直接。 相反地，`labelStyle`和`buttonStyle`從它繼承，設定其他可繫結的屬性值。 `labelStyle`和`buttonStyle`接著會套用到[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體和[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)執行個體，藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。

## <a name="summary"></a>總結

樣式可以繼承自其他樣式來減少重複，並允許重複使用。 樣式繼承藉由設定[ `Style.BasedOn` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.BasedOn/)屬性至現有[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)。


## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
