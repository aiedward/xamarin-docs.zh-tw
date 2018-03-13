---
title: "明確的樣式"
description: "明確樣式是藉由設定其樣式屬性選擇性地套用到控制項。"
ms.topic: article
ms.prod: xamarin
ms.assetid: C0DF9F8F-B431-4374-A574-325BC3C41A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 1fbc12288527c053a24041aa6c49cc1a4abdde55
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="explicit-styles"></a>明確的樣式

_明確樣式是藉由設定其樣式屬性選擇性地套用到控制項。_

## <a name="creating-an-explicit-style-in-xaml"></a>在 XAML 中建立明確的樣式

若要宣告[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)頁面層次[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)必須加入至頁面，然後一或多個`Style`宣告可以包含在`ResourceDictionary`。 A`Style`進行*明確*，提供其宣告`x:Key`屬性，提供描述性的索引鍵中`ResourceDictionary`。 *明確*樣式必須再套用到特定的視覺化項目藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。

下列程式碼範例示範*明確*樣式中的頁面在 XAML 中宣告`ResourceDictionary`並套用到該網頁[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
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

[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義三個*明確*樣式套用至網頁的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體。 每個`Style`用來在不同的色彩顯示文字，也可以將字型的大小和水平及垂直版面配置選項。 每個`Style`套用到不同`Label`藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性使用`StaticResource`標記延伸。 這會導致下列螢幕擷取畫面所示的外觀：

[![](explicit-images/explicit-styles.png "明確的樣式範例")](explicit-images/explicit-styles-large.png#lightbox "明確樣式範例")

此外，最終[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)具有[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)套用至其中，但也會覆寫[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)屬性，以不同的`Color`值。

### <a name="creating-an-explicit-style-at-the-control-level"></a>在控制層級建立明確的樣式

除了建立*明確*頁面層級的樣式，它們也可以建立層級控制，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ExplicitStylesPage" Title="Explicit" Icon="xaml.png">
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

在此範例中，*明確* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)執行個體指派給[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)集合[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)控制項。 然後可以套用樣式至控制項和其子系。

如需建立應用程式中的樣式資訊[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，請參閱[全域樣式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="creating-an-explicit-style-in-c35"></a>在 C 中建立明確的樣式&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 執行個體可以新增到網頁的[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) C# 中建立新的集合[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，再以加入`Style`執行個體來`ResourceDictionary`，如下所示下列程式碼範例：

```csharp
public class ExplicitStylesPageCS : ContentPage
{
    public ExplicitStylesPageCS ()
    {
        var labelRedStyle = new Style (typeof(Label)) {
            Setters = {
                ...
                new Setter { Property = Label.TextColorProperty, Value = Color.Red  }
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
                new Label { Text = "and an explicit style override",
                            Style = (Style)Resources ["labelBlueStyle"], TextColor = Color.Teal }
            }
        };
    }
}
```

建構函式定義三個*明確*樣式套用至網頁的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體。 每個*明確* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)加入至[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/)方法，指定`key`字串來參考`Style`執行個體。 每個`Style`套用到不同`Label`藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。

不過，沒有任何優勢使用[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)這裡。 相反地， [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)可以直接指派給執行個體[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)必要的視覺元素的屬性和`ResourceDictionary`可以移除，如下列所示程式碼範例：

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

建構函式定義三個*明確*樣式套用至網頁的[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)執行個體。 每個`Style`用來在不同的色彩顯示文字，也可以將字型的大小和水平及垂直版面配置選項。 每個`Style`套用到不同`Label`藉由設定其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。 此外，最終`Label`具有`Style`套用至其中，但也會覆寫`TextColor`到不同的屬性`Color`值。

## <a name="summary"></a>總結

A [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)進行*明確*，提供其宣告`x:Key`屬性，然後再選擇性地套用到控制項設定其[ `Style`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。



## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
