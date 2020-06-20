---
title: 樣式簡介 Xamarin.Forms
description: 樣式允許自訂視覺元素的外觀。 樣式是針對特定類型而定義的，而且包含該類型上可用屬性的值。
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5766af7da3a0cf550a2ccb3a926dad25fd7962eb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138813"
---
# <a name="introduction-to-xamarinforms-styles"></a>樣式簡介 Xamarin.Forms

_樣式允許自訂視覺元素的外觀。樣式是針對特定類型而定義的，而且包含該類型上可用屬性的值。_

Xamarin.Forms應用程式通常會包含多個具有相同外觀的控制項。 例如，應用程式可能會有多個 [`Label`](xref:Xamarin.Forms.Label) 具有相同字型選項和版面配置選項的實例，如下列 XAML 程式碼範例所示：

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

每個 [`Label`](xref:Xamarin.Forms.Label) 實例都有相同的屬性值，以控制所顯示的文字外觀 `Label` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![不含樣式的標籤外觀](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

設定每個個別控制項的外觀可能是重複且容易出錯的工作。 相反地，您可以建立定義外觀的樣式，然後套用至必要的控制項。

## <a name="create-a-style"></a>建立樣式

[`Style`](xref:Xamarin.Forms.Style)類別會將屬性值的集合分組成一個物件，然後再套用到多個視覺元素實例。 這有助於減少重複的標記，並可讓應用程式的外觀更容易變更。

雖然樣式主要是針對以 XAML 為基礎的應用程式所設計，但也可以用 c # 建立：

- [`Style`](xref:Xamarin.Forms.Style)在 XAML 中建立的實例通常是在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 指派給 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 控制項、頁面或 [`Resources`](xref:Xamarin.Forms.Application.Resources) 應用程式集合的中定義。
- [`Style`](xref:Xamarin.Forms.Style)在 c # 中建立的實例通常是在頁面的類別中定義，或是在可全域存取的類別中定義。

選擇要在何處定義 [`Style`](xref:Xamarin.Forms.Style) 可使用的影響：

- [`Style`](xref:Xamarin.Forms.Style)定義于控制項層級的實例只能套用至控制項和其子系。
- [`Style`](xref:Xamarin.Forms.Style)在頁面層級定義的實例只能套用至頁面和其子系。
- [`Style`](xref:Xamarin.Forms.Style)在應用層級定義的實例可以在整個應用程式中套用。

每個 [`Style`](xref:Xamarin.Forms.Style) 實例都包含一或多個 [`Setter`](xref:Xamarin.Forms.Setter) 物件的集合，每個都具有 `Setter` [`Property`](xref:Xamarin.Forms.Setter.Property) 和 [`Value`](xref:Xamarin.Forms.Setter.Value) 。 `Property` 為樣式所套用元素的可繫結屬性名稱，且 `Value` 為套用至屬性的值。

每個 [`Style`](xref:Xamarin.Forms.Style) 實例都可以是*明確*或*隱含*的：

- *明確* [`Style`](xref:Xamarin.Forms.Style) 實例的定義方式是指定 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 和 `x:Key` 值，並將 target 專案的 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 屬性設定為 `x:Key` 參考。 如需*明確*樣式的詳細資訊，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隱含* [`Style`](xref:Xamarin.Forms.Style) 實例的定義方式是只指定 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 。 `Style`然後，實例會自動套用至該類型的所有元素。 請注意，的子類別 `TargetType` 不會自動套用 `Style` 。 如需*隱含*樣式的詳細資訊，請參閱[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

建立時 [`Style`](xref:Xamarin.Forms.Style) ， [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 一律需要屬性。 下列程式碼範例顯示在 XAML 中建立的*明確*樣式（附注 `x:Key` ）：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要套用 `Style` ，目標物件必須是， [`VisualElement`](xref:Xamarin.Forms.VisualElement) 且符合的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 屬性值 `Style` ，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

在視圖階層架構中較低的樣式，其優先順序會高於定義的較高層級。 例如，將設定 [`Style`](xref:Xamarin.Forms.Style) [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) 為 `Red` 在應用層級的設定，將會被設定為的頁面層級樣式所覆寫 `Label.TextColor` `Green` 。 同樣地，控制項層級樣式也會覆寫頁面層級樣式。 此外，如果 `Label.TextColor` 直接在控制項屬性上設定，這會優先于任何樣式。

本節中的文章將示範並說明如何建立和套用*明確*和*隱含*樣式、如何建立全域樣式、樣式繼承、如何在執行時間回應樣式變更，以及如何使用中包含的內建樣式 Xamarin.Forms 。

> [!NOTE]
> **什麼是 StyleId？**
>
> 在 Xamarin.Forms 2.2 之前， [`StyleId`](xref:Xamarin.Forms.Element.StyleId) 屬性是用來識別應用程式中的個別元素，以便在 UI 測試和主題引擎（例如 Pixate）中識別。 不過， Xamarin.Forms 2.2 引進了 [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) 已取代屬性的屬性 [`StyleId`](xref:Xamarin.Forms.Element.StyleId) 。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
