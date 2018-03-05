---
title: "Introduction to 樣式"
description: "樣式可讓自訂的視覺化元素的外觀。 樣式定義的特定型別，而且包含在該類型的屬性值。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 3FF899C0-6CFB-4C1D-837D-9E9E10181967
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/27/2016
ms.openlocfilehash: 6b4d63637eae7c1719f3f6e525327d416ddff59d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-styles"></a>Introduction to 樣式

_樣式可讓自訂的視覺化元素的外觀。樣式定義的特定型別，而且包含在該類型的屬性值。_

Xamarin.Forms 應用程式通常會包含多個具有相同的外觀的控制項。 例如，應用程式可能有多個[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體具有相同的字型選項和版面配置選項，如下列 XAML 程式碼範例所示：

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

下列程式碼範例顯示 C# 中建立的對等頁面：

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

每個[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體具有相同的屬性值，來控制顯示之文字的外觀`Label`。 這會導致下列螢幕擷取畫面所示的外觀：

[![](introduction-images/no-styles.png "標籤外觀沒有樣式")](introduction-images/no-styles-large.png "標籤外觀沒有樣式")

設定每個個別控制項的外觀可能重複而且容易產生錯誤。 相反地，樣式可以建立所定義的外觀，並套用至所需的控制項。

## <a name="creating-a-style"></a>建立樣式

[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)類別群組接著可以套用至多個視覺項目執行個體的一個物件的屬性值集合。 這有助於減少重複的標記，並可讓應用程式外觀更輕鬆地變更。

雖然樣式主要的設計以 XAML 為基礎的應用程式，也在 C# 中建立它們：

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在 XAML 中建立的執行個體通常定義在[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) ，指派給[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)控制項的集合頁面上，或[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Resources/)應用程式集合。
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在頁面的類別中，或可全域存取的類別中，通常被定義在 C# 中建立的執行個體。

選擇要定義在何處[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可以用它的影響：

- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在控制層級定義的執行個體只能套用至控制項和其子系。
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 在頁面層級定義的執行個體只能套用至頁面和其子系。
- [`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 定義應用程式層級的執行個體可以套用在整個應用程式。

每個[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)執行個體包含一或多個集合[ `Setter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)物件，與每個`Setter`具有[ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/)和[`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/). `Property` ，套用的樣式項目之可繫結屬性的名稱和`Value`是套用至屬性的值。

每個[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)執行個體可以是*明確*，或*隱含*:

- *明確* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)藉由指定定義執行個體[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)和`x:Key`值並設定目標元素[`Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性`x:Key`參考。 如需有關*明確*樣式，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)。
- *隱含* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)藉由只指定定義執行個體[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)。 `Style`執行個體將會再自動套用到該類型的所有項目。 請注意該類別的子`TargetType`自動具備`Style`套用。 如需有關*隱含*樣式，請參閱[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

建立時[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)、 [ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)屬性永遠是必要項。 下列程式碼範例示範*明確*樣式 (請注意`x:Key`) 在 XAML 中建立：

```xaml
<Style x:Key="labelStyle" TargetType="Label">
    <Setter Property="HorizontalOptions" Value="Center" />
    <Setter Property="VerticalOptions" Value="CenterAndExpand" />
    <Setter Property="FontSize" Value="Large" />
</Style>
```

若要套用`Style`，目標物件必須是[ `VisualElement` ](https://developer.xamarin.com/api/type/Xamarin.Forms.VisualElement/)符合[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)屬性值為`Style`，如下列 XAML 程式碼範例所示：

```xaml
<Label Text="Demonstrating an explicit style" Style="{StaticResource labelStyle}" />
```

樣式檢視階層中較低的優先順序高於定義更高版本上。 例如，設定[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)設定[ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)至`Red`在應用程式層級將會覆寫設定的頁面層級樣式`Label.TextColor`來`Green`. 同樣地，將會覆寫控制層級的樣式頁面層級的樣式。 此外，如果`Label.TextColor`設定直接上的控制項屬性，這會優先於任何樣式。

本節中的文章示範，並說明如何建立和套用*明確*和*隱含*樣式、 如何建立全域的樣式、 樣式繼承，如何回應樣式的變更，在執行階段，以及如何使用 Xamarin.Forms 中包含的內建樣式。

> [!NOTE]
> **什麼是用於儲存 StyleId？**
>
> Xamarin.Forms 2.2 之前[ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/)屬性用來識別在 UI 測試，和例如 Pixate 佈景主題引擎中識別應用程式中的個別項目。 不過，導入了 Xamarin.Forms 2.2 [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/)屬性，已取代[ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/)屬性。 如需詳細資訊，請參閱[使用 Xamarin.UITest 和 Test Cloud 進行測試自動化 Xamarin.Forms](~/xamarin-forms/deploy-test/uitest-and-test-cloud.md)。

## <a name="summary"></a>總結

Xamarin.Forms 應用程式通常會包含多個具有相同的外觀的控制項。 設定每個個別控制項的外觀可能重複而且容易產生錯誤。 相反地，樣式可以建立自訂控制項外觀的群組上的控制項類型所提供的設定屬性。


## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
