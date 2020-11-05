---
title: 中的樣式繼承 Xamarin.Forms
description: 樣式可以繼承自其他樣式，以減少重複並讓重複使用。 本文說明如何在應用程式中執行樣式繼承 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 67A3A39C-8CC0-446D-8162-FFA73582D3B8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46e4147b61d4d78ec30e047452720c422e7b1dc9
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371855"
---
# <a name="style-inheritance-in-no-locxamarinforms"></a>中的樣式繼承 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_樣式可以繼承自其他樣式，以減少重複並讓重複使用。_

## <a name="style-inheritance-in-xaml"></a>XAML 中的樣式繼承

樣式繼承是藉由將 [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) 屬性設定為現有的來執行 [`Style`](xref:Xamarin.Forms.Style) 。 在 XAML 中，這是藉由將 `BasedOn` 屬性設定為 `StaticResource` 參考先前所建立之標記延伸的方式來達成 `Style` 。 在 c # 中，這是藉由將 `BasedOn` 屬性設定為實例來達成 `Style` 。

繼承自基底樣式的樣式可以包含 [`Setter`](xref:Xamarin.Forms.Setter) 新屬性的實例，或使用它們來覆寫基底樣式的樣式。 此外，繼承自基底樣式的樣式必須以相同類型為目標，或是衍生自基底樣式之目標型別的型別。 例如，如果基底樣式以實例為目標 [`View`](xref:Xamarin.Forms.View) ，則根據基底樣式的樣式可以針對 `View` 衍生自類別的實例或類型 `View` ，例如 [`Label`](xref:Xamarin.Forms.Label) 和 [`Button`](xref:Xamarin.Forms.Button) 實例。

下列程式碼示範 XAML 頁面中的 *明確* 樣式繼承：

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

`baseStyle`目標 [`View`](xref:Xamarin.Forms.View) 實例，並設定 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性。 `baseStyle`不會直接在任何控制項上設定。 相反地 `labelStyle` ， `buttonStyle` 它會從它繼承，設定其他可系結的屬性值。 然後， `labelStyle` 會將和套用 `buttonStyle` 至 [`Label`](xref:Xamarin.Forms.Label) 實例和 [`Button`](xref:Xamarin.Forms.Button) 實例，方法是設定其 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 屬性。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![樣式繼承螢幕擷取畫面](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> 隱含樣式可以衍生自明確的樣式，但無法從隱含樣式衍生明確的樣式。

### <a name="respecting-the-inheritance-chain"></a>尊重繼承鏈

樣式只能繼承自相同層級（或以上）在 view 階層中的樣式。 這表示：

- 應用層級資源只能繼承自其他應用層級資源。
- 頁面層級資源可以繼承自應用層級資源和其他頁面層級資源。
- 控制項層級資源可以繼承自應用層級資源、頁面層級資源和其他控制項層級資源。

下列程式碼範例會示範這個繼承鏈：

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

在此範例中， `labelStyle` 和是 `buttonStyle` 控制層級的資源，而 `baseStyle` 是頁面層級資源。 但是，雖然 `labelStyle` 和 `buttonStyle` 繼承自 `baseStyle` ，但無法 `baseStyle` 繼承自 `labelStyle` 或 `buttonStyle` ，因為它們在 view 階層中各自的位置。

## <a name="style-inheritance-in-c35"></a>C&#35; 中的樣式繼承

下列程式碼範例會顯示對等的 c # 頁面，其中 [`Style`](xref:Xamarin.Forms.Style) 實例會直接指派給 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 必要控制項的屬性：

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

`baseStyle`目標 [`View`](xref:Xamarin.Forms.View) 實例，並設定 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性。 `baseStyle`不會直接在任何控制項上設定。 相反地 `labelStyle` ， `buttonStyle` 它會從它繼承，設定其他可系結的屬性值。 然後， `labelStyle` 會將和套用 `buttonStyle` 至 [`Label`](xref:Xamarin.Forms.Label) 實例和 [`Button`](xref:Xamarin.Forms.Button) 實例，方法是設定其 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 屬性。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [ (範例) 的基本樣式 ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式 (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)