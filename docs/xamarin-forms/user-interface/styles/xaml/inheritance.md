---
title: 中的樣式繼承Xamarin.Forms
description: 樣式可以繼承自其他樣式，以減少重複程度並讓重複使用。 本文說明如何在應用程式中執行樣式繼承 Xamarin.Forms 。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80cc419ae098f4a0cbbd782785c0ec5ba03fa703
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138953"
---
# <a name="style-inheritance-in-xamarinforms"></a>中的樣式繼承Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_樣式可以繼承自其他樣式，以減少重複程度並讓重複使用。_

## <a name="style-inheritance-in-xaml"></a>XAML 中的樣式繼承

將屬性設定為現有的，即可執行樣式繼承 [`Style.BasedOn`](xref:Xamarin.Forms.Style.BasedOn) [`Style`](xref:Xamarin.Forms.Style) 。 在 XAML 中，將 `BasedOn` 屬性設定為 `StaticResource` 參考先前所建立之的標記延伸，即可達成此目的 `Style` 。 在 c # 中，將屬性設定為實例即可達成此目的 `BasedOn` `Style` 。

繼承自基底樣式的樣式可以包含 [`Setter`](xref:Xamarin.Forms.Setter) 新屬性的實例，或使用它們來覆寫基底樣式的樣式。 此外，繼承自基底樣式的樣式必須以相同的類型為目標，或是衍生自基底樣式之目標型別的類型。 例如，如果基底樣式以實例為目標 [`View`](xref:Xamarin.Forms.View) ，根據基底樣式的樣式可以針對 `View` 衍生自類別的實例或類型（ `View` 例如 [`Label`](xref:Xamarin.Forms.Label) 和實例）進行目標 [`Button`](xref:Xamarin.Forms.Button) 。

下列程式碼示範 XAML 頁面中的*明確*樣式繼承：

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

`baseStyle`目標 [`View`](xref:Xamarin.Forms.View) 實例，並設定 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性。 `baseStyle`不會直接在任何控制項上設定。 相反 `labelStyle` 地，和 `buttonStyle` 會從它繼承，設定其他可系結的屬性值。 然後 `labelStyle` `buttonStyle` 會藉 [`Label`](xref:Xamarin.Forms.Label) [`Button`](xref:Xamarin.Forms.Button) 由設定其屬性，將和套用至實例和實例 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![](inheritance-images/style-inheritance.png)](inheritance-images/style-inheritance-large.png#lightbox)

> [!NOTE]
> 隱含樣式可以衍生自明確樣式，但無法從隱含樣式衍生明確樣式。

### <a name="respecting-the-inheritance-chain"></a>尊重繼承鏈

樣式只能繼承自視圖階層中相同層級（或以上）的樣式。 這表示：

- 應用層級資源只能繼承自其他應用層級資源。
- 頁面層級資源可以繼承自應用層級資源和其他頁面層級資源。
- 控制項層級資源可以繼承自應用層級資源、頁面層級資源和其他控制項層級資源。

下列程式碼範例示範此繼承鏈：

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

在此範例中， `labelStyle` 和是 `buttonStyle` 控制項層級資源，而 `baseStyle` 是頁面層級資源。 不過，從 `labelStyle` 和 `buttonStyle` 繼承時 `baseStyle` ， `baseStyle` `labelStyle` `buttonStyle` 由於其在 view 階層中各自的位置，因此不可能繼承自或。

## <a name="style-inheritance-in-c35"></a>C&#35; 中的樣式繼承

對等的 c # 頁面，其中 [`Style`](xref:Xamarin.Forms.Style) 實例會直接指派給 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 必要控制項的屬性，如下列程式碼範例所示：

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

`baseStyle`目標 [`View`](xref:Xamarin.Forms.View) 實例，並設定 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性。 `baseStyle`不會直接在任何控制項上設定。 相反 `labelStyle` 地，和 `buttonStyle` 會從它繼承，設定其他可系結的屬性值。 然後 `labelStyle` `buttonStyle` 會藉 [`Label`](xref:Xamarin.Forms.Label) [`Button`](xref:Xamarin.Forms.Button) 由設定其屬性，將和套用至實例和實例 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
