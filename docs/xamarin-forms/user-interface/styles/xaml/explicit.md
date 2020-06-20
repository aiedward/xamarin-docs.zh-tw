---
title: 中的明確樣式Xamarin.Forms
description: 明確樣式是藉由設定樣式屬性，選擇性地套用至控制項。 本文說明如何在應用程式中使用明確樣式 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 62b84a5028c17c28a69a887a832028c2064fa78d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136262"
---
# <a name="explicit-styles-in-xamarinforms"></a>中的明確樣式Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_明確樣式是藉由設定樣式屬性，選擇性地套用至控制項。_

## <a name="create-an-explicit-style-in-xaml"></a>在 XAML 中建立明確樣式

若要 [`Style`](xref:Xamarin.Forms.Style) 在頁面層級宣告， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 必須將加入至頁面，然後一或多個宣告 `Style` 可以包含在中 `ResourceDictionary` 。 `Style`會藉由*explicit*將 `x:Key` 屬性（attribute）提供給它，讓它成為明確的，藉此將它設為明確的索引鍵 `ResourceDictionary` 。 然後，*明確*樣式必須藉由設定其屬性，套用至特定的視覺效果元素 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

下列程式碼範例顯示在頁面的 XAML 中宣告的*明確*樣式 `ResourceDictionary` ，並套用至頁面的 [`Label`](xref:Xamarin.Forms.Label) 實例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="labelRedStyle" TargetType="Label">
                <Setter Property="HorizontalOptions"
                        Value="Center" />
                <Setter Property="VerticalOptions"
                        Value="CenterAndExpand" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
            <Style x:Key="labelGreenStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Green" />
            </Style>
            <Style x:Key="labelBlueStyle" TargetType="Label">
                ...
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Label Text="These labels"
                   Style="{StaticResource labelRedStyle}" />
            <Label Text="are demonstrating"
                   Style="{StaticResource labelGreenStyle}" />
            <Label Text="explicit styles,"
                   Style="{StaticResource labelBlueStyle}" />
            <Label Text="and an explicit style override"
                   Style="{StaticResource labelBlueStyle}"
                   TextColor="Teal" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)會定義套用至頁面實例的三個*明確*樣式 [`Label`](xref:Xamarin.Forms.Label) 。 每個 `Style` 都會用來以不同的色彩顯示文字，同時也會設定字型大小和水準和垂直版面配置選項。 每個 `Style` 都會套用至不同的， `Label` 方法是 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 使用標記延伸來設定其屬性 `StaticResource` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![明確樣式範例](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

此外，最後會套用 [`Label`](xref:Xamarin.Forms.Label) [`Style`](xref:Xamarin.Forms.Style) 至它，但也會將屬性覆寫 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 為不同的 `Color` 值。

### <a name="create-an-explicit-style-at-the-control-level"></a>在控制項層級建立明確樣式

除了在頁面層級建立*明確*樣式之外，也可以在控制項層級建立，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="labelRedStyle" TargetType="Label">
                      ...
                    </Style>
                    ...
                </ResourceDictionary>
            </StackLayout.Resources>
            <Label Text="These labels" Style="{StaticResource labelRedStyle}" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此範例中，會將*明確*的 [`Style`](xref:Xamarin.Forms.Style) 實例指派給控制項的 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 集合 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 然後，樣式可以套用至控制項及其子系。

如需在應用程式中建立樣式的詳細資訊 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，請參閱[全域樣式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-explicit-style-in-c35"></a>在 C&#35; 中建立明確樣式

[`Style`](xref:Xamarin.Forms.Style)藉由建立新的，然後將實例新增至，即可將實例加入至頁面的 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下列程式碼範例所示：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red    }
            }
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Green }
            }
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Blue }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("labelRedStyle", labelRedStyle);
        Resources.Add ("labelGreenStyle", labelGreenStyle);
        Resources.Add ("labelBlueStyle", labelBlueStyle);
        ...

        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels",
                            Style = (Style)Resources ["labelRedStyle"] },
                new Label { Text = "are demonstrating",
                            Style = (Style)Resources ["labelGreenStyle"] },
                new Label { Text = "explicit styles,",
                            Style = (Style)Resources ["labelBlueStyle"] },
                new Label {    Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

此函式會定義套用至頁面實例的三個*明確*樣式 [`Label`](xref:Xamarin.Forms.Label) 。 每個*明確* [`Style`](xref:Xamarin.Forms.Style) 都會 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 使用方法加入至 [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) ，並指定 `key` 要參考實例的字串 `Style` 。 藉 `Style` `Label` 由設定其屬性，每個都會套用至不同的 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。

不過，在這裡使用沒有任何好處 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 。 相反地，您 [`Style`](xref:Xamarin.Forms.Style) 可以將實例直接指派給 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 所需視覺效果元素的屬性，而且 `ResourceDictionary` 可以移除，如下列程式碼範例所示：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            ...
        };
        var labelGreenStyle = new Style (typeof(Label)) {
            ...
        };
        var labelBlueStyle = new Style (typeof(Label)) {
            ...
        };
        ...
        Content = new StackLayout {
            Children = {
                new Label { Text = "These labels", Style = labelRedStyle },
                new Label { Text = "are demonstrating", Style = labelGreenStyle },
                new Label { Text = "explicit styles,", Style = labelBlueStyle },
                new Label { Text = "and an explicit style override", Style = labelBlueStyle,
                            TextColor = Color.Teal }
            }
        };
    }
}
```

此函式會定義套用至頁面實例的三個*明確*樣式 [`Label`](xref:Xamarin.Forms.Label) 。 每個 `Style` 都會用來以不同的色彩顯示文字，同時也會設定字型大小和水準和垂直版面配置選項。 每個 `Style` 都會透過 `Label` 設定其屬性來套用至不同的 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) 。 此外，最後會套用 `Label` `Style` 至它，但也會將屬性覆寫 `TextColor` 為不同的 `Color` 值。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
