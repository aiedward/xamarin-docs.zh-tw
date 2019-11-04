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
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "70228166"
---
# <a name="introduction-to-xamarinforms-styles"></a>Xamarin. 表單樣式簡介

_樣式允許自訂視覺元素的外觀。樣式是針對特定類型而定義的，而且包含該類型上可用屬性的值。_

Xamarin：表單應用程式通常包含多個具有相同外觀的控制項。 例如，應用程式可能會有多個具有相同字型選項和版面配置選項的[`Label`](xref:Xamarin.Forms.Label)實例，如下列 XAML 程式碼範例所示：

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

每個[`Label`](xref:Xamarin.Forms.Label)實例都具有完全相同的屬性值，以控制 `Label`顯示之文字的外觀。 這會導致下列螢幕擷取畫面中顯示的外觀：

[不含樣式的![標籤外觀](introduction-images/no-styles.png)](introduction-images/no-styles-large.png#lightbox)

設定每個個別控制項的外觀可能是重複且容易出錯的工作。 相反地，您可以建立定義外觀的樣式，然後套用至必要的控制項。

## <a name="create-a-style"></a>建立樣式

[`Style`](xref:Xamarin.Forms.Style)類別會將屬性值的集合分組到一個物件中，然後再套用到多個視覺元素實例。 這有助於減少重複的標記，並可讓應用程式的外觀更容易變更。

雖然樣式主要是針對以 XAML 為基礎的應用程式所設計，但也C#可以在中建立：

- 在 XAML 中建立的[`Style`](xref:Xamarin.Forms.Style)實例通常是在指派給控制項的[`Resources`](xref:Xamarin.Forms.VisualElement.Resources)集合、頁面或應用程式[`Resources`](xref:Xamarin.Forms.Application.Resources)集合的[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)中定義。
- [`Style`](xref:Xamarin.Forms.Style)在中建立的C#實例通常會定義于頁面的類別中，或在可全域存取的類別中。

選擇要在何處定義 [`Style`](xref:Xamarin.Forms.Style) 會影響其可使用的位置：

- 在控制項層級定義的[`Style`](xref:Xamarin.Forms.Style)實例只能套用至控制項和其子系。
- 在頁面層級定義的[`Style`](xref:Xamarin.Forms.Style)實例只能套用至頁面和其子系。
- 在應用層級定義的[`Style`](xref:Xamarin.Forms.Style)實例，可以在整個應用程式中套用。

每個[`Style`](xref:Xamarin.Forms.Style)實例都包含一或多個[`Setter`](xref:Xamarin.Forms.Setter)物件的集合，每個 `Setter` 都有一個[`Property`](xref:Xamarin.Forms.Setter.Property)和一個[`Value`](xref:Xamarin.Forms.Setter.Value)。 `Property` 是套用樣式之元素的可系結屬性名稱，而 `Value` 則是套用至屬性的值。

每個[`Style`](xref:Xamarin.Forms.Style)實例都可以是*明確*或*隱含*的：

- *明確*的[`Style`](xref:Xamarin.Forms.Style)實例是藉由指定[`TargetType`](xref:Xamarin.Forms.Style.TargetType)和 `x:Key` 值，以及將 target 元素的[`Style`](xref:Xamarin.Forms.NavigableElement.Style)屬性設定為 `x:Key` 參考來定義。 如需*明確*樣式的詳細資訊，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隱含*的[`Style`](xref:Xamarin.Forms.Style)實例是藉由僅指定[`TargetType`](xref:Xamarin.Forms.Style.TargetType)來定義。 然後，`Style` 實例會自動套用至該類型的所有元素。 請注意，`TargetType` 的子類別不會自動套用 `Style`。 如需*隱含*樣式的詳細資訊，請參閱[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

建立[`Style`](xref:Xamarin.Forms.Style)時，一律需要[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性。 下列程式碼範例顯示在 XAML 中建立的*明確*樣式（請注意 `x:Key`）：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要套用 `Style`，目標物件必須是符合 `Style`之[`TargetType`](xref:Xamarin.Forms.Style.TargetType)屬性值的[`VisualElement`](xref:Xamarin.Forms.VisualElement) ，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

在視圖階層架構中較低的樣式，其優先順序會高於定義的較高層級。 例如，設定在應用層級設定[`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor)為 `Red` 的[`Style`](xref:Xamarin.Forms.Style) ，將會被設定 `Label.TextColor` 為 `Green`的頁面層級樣式所覆寫。 同樣地，控制項層級樣式也會覆寫頁面層級樣式。 此外，如果在控制項屬性上直接設定 `Label.TextColor`，這會優先于任何樣式。

本節中的文章示範並說明如何建立及套用*明確*和*隱含*樣式、如何建立全域樣式、樣式繼承、如何在執行時間回應樣式變更，以及如何使用包含在中的內建樣式Xamarin. 表單。

> [!NOTE]
> **什麼是 StyleId？**
>
> 在 Xamarin. 表單2.2 之前， [`StyleId`](xref:Xamarin.Forms.Element.StyleId)屬性是用來識別應用程式中的個別專案，以用於 UI 測試中的識別，以及主題引擎（例如 Pixate）。 不過，Xamarin. Forms 2.2 引進了[`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)屬性，已取代[`StyleId`](xref:Xamarin.Forms.Element.StyleId)屬性。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [樣式](xref:Xamarin.Forms.Style)
- [庫](xref:Xamarin.Forms.Setter)
