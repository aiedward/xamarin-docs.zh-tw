---
title: Introduction to Xamarin.Forms 樣式
description: 樣式可讓自訂的視覺元素的外觀。 樣式會定義特定型別，並且包含該類型上的可用屬性的值。
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 6f5af5fde46676cf669e6c02fb83f4aac5d31c46
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292086"
---
# <a name="introduction-to-xamarinforms-styles"></a>Introduction to Xamarin.Forms 樣式

_樣式可讓自訂的視覺元素的外觀。樣式會定義特定型別，並且包含該類型上的可用屬性的值。_

Xamarin.Forms 應用程式通常會包含多個具有相同的外觀的控制項。 例如，應用程式可能有多個[ `Label` ](xref:Xamarin.Forms.Label)執行個體具有相同的字型選項和版面配置選項，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="Styles.NoStylesPage"
    Title="No Styles"
    Icon="xaml.png">
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
        Icon = "csharp.png";
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

每個[ `Label` ](xref:Xamarin.Forms.Label)執行個體具有相同的屬性值，來控制所顯示之文字的外觀`Label`。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![](introduction-images/no-styles.png "標籤沒有樣式的外觀")](introduction-images/no-styles-large.png#lightbox "標籤沒有樣式的外觀")

設定每個個別控制項的外觀可能重複又容易出錯。 相反地，您可以建立可定義外觀，並套用至所需的控制項。

## <a name="create-a-style"></a>建立樣式

[ `Style` ](xref:Xamarin.Forms.Style)類別分組的屬性值集合成一個物件，然後可以套用至多個視覺項目執行個體。 這有助於減少重複的標記，並可讓應用程式外觀，以更輕鬆地變更。

雖然樣式設計主要是針對以 XAML 為基礎的應用程式時，他們也可以建立 C# 中：

- [`Style`](xref:Xamarin.Forms.Style) 在 XAML 中建立的執行個體通常定義於[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)指派給[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)控制項的集合頁面上，或[ `Resources` ](xref:Xamarin.Forms.Application.Resources)應用程式集合。
- [`Style`](xref:Xamarin.Forms.Style) 在頁面的類別中，或可全域存取的類別中，通常被定義在 C# 中建立的執行個體。

選擇要在何處定義 [`Style`](xref:Xamarin.Forms.Style) 會影響其可使用的位置：

- [`Style`](xref:Xamarin.Forms.Style) 在控制層級定義的執行個體只能套用至控制項和其子系。
- [`Style`](xref:Xamarin.Forms.Style) 在頁面層級定義的執行個體只能套用至頁面和其子系。
- [`Style`](xref:Xamarin.Forms.Style) 在應用程式層級定義的執行個體可以套用整個應用程式。

每個[ `Style` ](xref:Xamarin.Forms.Style)執行個體包含一或多個集合[ `Setter` ](xref:Xamarin.Forms.Setter)物件，與每個`Setter`具有[ `Property` ](xref:Xamarin.Forms.Setter.Property)和[`Value`](xref:Xamarin.Forms.Setter.Value). `Property`是可繫結項目屬性的樣式會套用到，名稱和`Value`是套用至屬性的值。

每個[ `Style` ](xref:Xamarin.Forms.Style)執行個體可以是*明確*，或*隱含*:

- *明確* [ `Style` ](xref:Xamarin.Forms.Style)藉由指定定義執行個體[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)和`x:Key`值，並設定目標項[`Style` ](xref:Xamarin.Forms.VisualElement.Style)屬性設`x:Key`參考。 如需詳細資訊*明確*樣式，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隱含* [ `Style` ](xref:Xamarin.Forms.Style)藉由只指定定義執行個體[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)。 `Style`執行個體將會再自動套用至該類型的所有項目。 附註的子類別化`TargetType`自動沒有`Style`套用。 如需詳細資訊*隱含*樣式，請參閱[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

建立時[ `Style` ](xref:Xamarin.Forms.Style)，則[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)屬性一律為必要。 下列程式碼範例所示*明確*樣式 (請注意`x:Key`) 在 XAML 中建立：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要套用`Style`，目標物件必須是[ `VisualElement` ](xref:Xamarin.Forms.VisualElement)符合[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)屬性值為`Style`，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

樣式檢視階層中較低的優先順序高於定義更高版本上。 例如，設定[ `Style` ](xref:Xamarin.Forms.Style) ，設定[ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor)至`Red`在應用程式層級將會覆寫設定的頁面層級樣式`Label.TextColor`到`Green`. 同樣地，將會覆寫控制項的層級樣式頁面層級的樣式。 此外，如果`Label.TextColor`設定直接控制屬性，此清單優先於任何樣式。

在本節中的文章示範，並說明如何建立和套用*明確*並*隱含*樣式、 如何建立全域的樣式、 樣式繼承，如何回應在執行階段的樣式變更以及如何使用 Xamarin.Forms 中包含的內建樣式。

> [!NOTE]
> **什麼是用於儲存 StyleId？**
>
> Xamarin.Forms 2.2 之前[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)屬性用來識別應用程式識別在 UI 測試，和例如 Pixate 佈景主題引擎中的個別項目。 不過，導入了 Xamarin.Forms 2.2 [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId)屬性，已取代[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)屬性。 如需詳細資訊，請參閱 <<c0> [ 自動化 Xamarin.Forms 測試使用 Xamarin.UITest 和測試雲端](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md)。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [樣式][](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
