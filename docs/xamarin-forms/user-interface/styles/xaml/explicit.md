---
title: 在 Xamarin.Forms 中的明確樣式
description: 明確樣式是藉由設定其樣式屬性選擇性地套用到控制項。 這篇文章說明如何使用 Xamarin.Forms 應用程式中的明確樣式。
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: b7888ea29dd02733059346bca21fec0a4c510c2e
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228005"
---
# <a name="explicit-styles-in-xamarinforms"></a>在 Xamarin.Forms 中的明確樣式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_明確樣式是藉由設定其樣式屬性選擇性地套用到控制項。_

## <a name="create-an-explicit-style-in-xaml"></a>在 XAML 中建立明確樣式

若要宣告[ `Style` ](xref:Xamarin.Forms.Style)在頁面層級[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)必須加入至頁面並再一或多個`Style`宣告可以包含在`ResourceDictionary`。 A`Style`更為*明確*藉由提供其宣告`x:Key`屬性，讓它的描述性的索引鍵中`ResourceDictionary`。 *明確*樣式必須再套用到特定的視覺項目設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性。

下列程式碼範例所示*明確*樣式在 XAML 中宣告中的頁面`ResourceDictionary`並套用至頁面的[ `Label` ](xref:Xamarin.Forms.Label)執行個體：

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

[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)定義三個*明確*套用至頁面的樣式[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 每個`Style`用來以不同的色彩顯示文字，也可以將字型的大小和水平及垂直版面配置選項。 每個`Style`套用至不同`Label`藉由設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性使用`StaticResource`標記延伸。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![明確樣式範例](explicit-images/explicit-styles.png)](explicit-images/explicit-styles-large.png#lightbox)

此外，最終[ `Label` ](xref:Xamarin.Forms.Label)具有[ `Style` ](xref:Xamarin.Forms.Style)套用至其中，但也會覆寫[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)屬性，以不同`Color`值。

### <a name="create-an-explicit-style-at-the-control-level"></a>在控制項層級建立明確樣式

除了建立*明確*頁面層級的樣式，它們也可以建立在控制層級，如下列程式碼範例所示：

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

在此範例中，*明確* [ `Style` ](xref:Xamarin.Forms.Style)執行個體指派給[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources)集合[ `StackLayout` ](xref:Xamarin.Forms.StackLayout)控制項。 控制項和其子系時，可以再套用樣式。

如需建立應用程式中的樣式[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，請參閱[全域樣式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-explicit-style-in-c35"></a>在 C 中建立明確樣式&#35;

[`Style`](xref:Xamarin.Forms.Style) 執行個體可以加入至網頁[ `Resources` ](xref:Xamarin.Forms.VisualElement.Resources) C# 中建立新的集合[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)，再以新增`Style`執行個體`ResourceDictionary`，如下所示下列程式碼範例：

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

建構函式會定義三個*明確*套用至頁面的樣式[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 每個*明確* [ `Style` ](xref:Xamarin.Forms.Style)新增至[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)使用[ `Add` ](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object))方法中，指定`key`字串以指向`Style`執行個體。 每個`Style`套用至不同`Label`藉由設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性。

不過，沒有使用任何優勢[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)這裡。 相反地， [ `Style` ](xref:Xamarin.Forms.Style)執行個體可以直接指派給[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)必要的視覺元素的屬性和`ResourceDictionary`可加以移除，如下列所示程式碼範例：

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

建構函式會定義三個*明確*套用至頁面的樣式[ `Label` ](xref:Xamarin.Forms.Label)執行個體。 每個`Style`用來以不同的色彩顯示文字，也可以將字型的大小和水平及垂直版面配置選項。 每個`Style`套用至不同`Label`藉由設定其[ `Style` ](xref:Xamarin.Forms.NavigableElement.Style)屬性。 此外，最終`Label`已經`Style`套用至其中，但也會覆寫`TextColor`到不同的屬性`Color`值。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
- [樣式](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
