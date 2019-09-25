---
title: Xamarin. 表單樣式簡介
description: 樣式允許自訂視覺元素的外觀。 樣式是針對特定類型而定義的，而且包含該類型上可用屬性的值。
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 35f8dad3590c07ceb3c93aa735b8c02d75098498
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "70228166"
---
# <a name="introduction-to-xamarinforms-styles"></a>Xamarin. 表單樣式簡介

_樣式允許自訂視覺元素的外觀。樣式是針對特定類型而定義的，而且包含該類型上可用屬性的值。_

Xamarin：表單應用程式通常包含多個具有相同外觀的控制項。 例如，應用程式可能會有多[`Label`](xref:Xamarin.Forms.Label)個具有相同字型選項和版面配置選項的實例，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="are not"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
            <Label Text="using styles"
                   HorizontalOptions="Center"
                   VerticalOptions="CenterAndExpand"
                   FontSize="Large" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

下列程式碼範例示範以 C# 建立的相等頁面：

```csharp
public class NoStylesPageCS : ContentPage
{
    public NoStylesPageCS ()
    {
        Title = "No Styles";
        IconImageSource = "csharp.png";
        Padding = new Thickness (0, 20, 0, 0);

        Content = new StackLayout {
            Children = {
                new Label {
                    Text = "These labels",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "are not",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                },
                new Label {
                    Text = "using styles",
                    HorizontalOptions = LayoutOptions.Center,
                    VerticalOptions = LayoutOptions.CenterAndExpand,
                    FontSize = Device.GetNamedSize (NamedSize.Large, typeof(Label))
                }
            }
        };
    }
}
```

每[`Label`](xref:Xamarin.Forms.Label)個實例都有相同的屬性值，以控制所顯示`Label`的文字外觀。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![不含樣式的標籤外觀](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

設定每個個別控制項的外觀可能是重複且容易出錯的工作。 相反地，您可以建立定義外觀的樣式，然後套用至必要的控制項。

## <a name="create-a-style"></a>建立樣式

[`Style`](xref:Xamarin.Forms.Style)類別會將屬性值的集合分組成一個物件，然後再套用到多個視覺元素實例。 這有助於減少重複的標記，並可讓應用程式的外觀更容易變更。

雖然樣式主要是針對以 XAML 為基礎的應用程式所設計，但也C#可以在中建立：

- [`Style`](xref:Xamarin.Forms.Style)在 XAML 中建立的實例通常是在[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)指派[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)給控制項、 [`Resources`](xref:Xamarin.Forms.Application.Resources)頁面或應用程式集合的中定義。
- [`Style`](xref:Xamarin.Forms.Style)在中C#建立的實例通常定義于頁面的類別中，或在可全域存取的類別中。

選擇要在何處定義 [`Style`](xref:Xamarin.Forms.Style) 會影響其可使用的位置：

- [`Style`](xref:Xamarin.Forms.Style)定義于控制項層級的實例只能套用至控制項和其子系。
- [`Style`](xref:Xamarin.Forms.Style)在頁面層級定義的實例只能套用至頁面和其子系。
- [`Style`](xref:Xamarin.Forms.Style)在應用層級定義的實例可以在整個應用程式中套用。

每[`Style`](xref:Xamarin.Forms.Style)個實例都包含一或多[`Setter`](xref:Xamarin.Forms.Setter)個物件的集合[`Property`](xref:Xamarin.Forms.Setter.Property) ， `Setter` [`Value`](xref:Xamarin.Forms.Setter.Value)每個都具有和。 是套用樣式之元素的可系結屬性名稱， `Value`而則是套用至屬性的值。 `Property`

每[`Style`](xref:Xamarin.Forms.Style)個實例都可以是*明確*或*隱含*的：

- `x:Key` [`Style`](xref:Xamarin.Forms.NavigableElement.Style) `x:Key` *明確* [`Style`](xref:Xamarin.Forms.Style)實例[的`TargetType`](xref:Xamarin.Forms.Style.TargetType)定義方式是指定和值，並將 target 專案的屬性設定為參考。 如需*明確*樣式的詳細資訊，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)。
- [`TargetType`](xref:Xamarin.Forms.Style.TargetType)*隱含* [`Style`](xref:Xamarin.Forms.Style)實例的定義方式是只指定。 然後`Style` ，實例會自動套用至該類型的所有元素。 請注意，的子`TargetType`類別不會自動`Style`套用。 如需*隱含*樣式的詳細資訊，請參閱[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

建立[`Style`](xref:Xamarin.Forms.Style)[時`TargetType`](xref:Xamarin.Forms.Style.TargetType) ，一律需要屬性。 下列程式碼範例顯示在 XAML 中建立的明確`x:Key`樣式（附注）：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要套用[`VisualElement`](xref:Xamarin.Forms.VisualElement) [`TargetType`](xref:Xamarin.Forms.Style.TargetType) `Style`，目標物件必須是，且符合的屬性值，如下列 XAML 程式碼範例所示： `Style`

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

樣式檢視階層中較低的優先順序高於定義更高版本上。 例如，設定[ `Style` ](xref:Xamarin.Forms.Style) ，設定[ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor)至`Red`在應用程式層級將會覆寫設定的頁面層級樣式`Label.TextColor`到`Green`. 同樣地，將會覆寫控制項的層級樣式頁面層級的樣式。 此外，如果`Label.TextColor`直接在控制項屬性上設定，這會優先于任何樣式。

本節中的文章示範並說明如何建立及套用*明確*和*隱含*樣式、如何建立全域樣式、樣式繼承、如何在執行時間回應樣式變更，以及如何使用包含在中的內建樣式Xamarin. 表單。

> [!NOTE]
> **什麼是 StyleId？**
>
> 在 Xamarin. 表單2.2 之前， [`StyleId`](xref:Xamarin.Forms.Element.StyleId)屬性是用來識別應用程式中的個別專案，以用於 UI 測試中的識別，以及主題引擎（例如 Pixate）。 不過，Xamarin. Forms 2.2 引進[`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)了已[`StyleId`](xref:Xamarin.Forms.Element.StyleId)取代屬性的屬性。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
