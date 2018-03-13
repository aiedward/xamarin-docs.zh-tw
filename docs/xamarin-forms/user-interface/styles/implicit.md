---
title: "隱含的樣式"
description: "隱含樣式是一種由所有控制項都使用的相同的 TargetType，而不需要每個控制項，來參考樣式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 02A75F3B-4389-49D4-A2F4-AFD473A4A161
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: b96b306c882eb30aaf8c81604afb9b6a547d715b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="implicit-styles"></a>隱含的樣式

_隱含樣式是一種由所有控制項都使用的相同的 TargetType，而不需要每個控制項，來參考樣式。_

## <a name="creating-an-implicit-style-in-xaml"></a>在 XAML 中建立隱含的樣式

若要宣告[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)頁面層次[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)必須加入至頁面，然後一或多個`Style`宣告可以包含在`ResourceDictionary`。 A`Style`進行*隱含*未指定`x:Key`屬性。 樣式就會套用到符合的視覺項目`TargetType`牌，但不衍生自的項目`TargetType`值。

下列程式碼範例示範*隱含*樣式中的頁面在 XAML 中宣告`ResourceDictionary`，並套用到該網頁[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)執行個體：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style TargetType="Entry">
                <Setter Property="HorizontalOptions" Value="Fill" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BackgroundColor" Value="Yellow" />
                <Setter Property="FontAttributes" Value="Italic" />
                <Setter Property="TextColor" Value="Blue" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Entry Text="These entries" />
            <Entry Text="are demonstrating" />
            <Entry Text="implicit styles," />
            <Entry Text="and an implicit style override" BackgroundColor="Lime" TextColor="Red" />
            <local:CustomEntry Text="Subclassed Entry is not receiving the style" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義單一*隱含*樣式套用至網頁的[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)執行個體。 `Style`用來顯示背景為黃色，藍色文字，也可以將其他外觀選項。 `Style`加入至網頁的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)但未指定`x:Key`屬性。 因此，`Style`套用至所有`Entry`它們符合隱含執行個體[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)屬性`Style`完全。 不過，`Style`不會套用到`CustomEntry`執行個體，也就是子類別化`Entry`。 這會導致下列螢幕擷取畫面所示的外觀：

[![](implicit-images/implicit-styles.png "隱含樣式範例")](implicit-images/implicit-styles-large.png#lightbox "隱含樣式範例")

此外，第四個[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)會覆寫[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)和[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.TextColor/)不同隱含樣式屬性`Color`值。

### <a name="creating-an-implicit-style-at-the-control-level"></a>在控制層級建立隱含的樣式

除了建立*隱含*頁面層級的樣式，它們也可以建立層級控制，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style TargetType="Entry">
                        <Setter Property="HorizontalOptions" Value="Fill" />
                        ...
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Entry Text="These entries" />
            ...
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在此範例中，*隱含* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)指派給[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/)集合[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/)控制項。 *隱含*樣式接著可以套用至控制項和其子系。

如需建立應用程式中的樣式資訊[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，請參閱[全域樣式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="creating-an-implicit-style-in-c35"></a>在 C 中建立隱含的樣式&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 執行個體可以新增到網頁的[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) C# 中建立新的集合[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，再以加入`Style`執行個體來`ResourceDictionary`，如下所示下列程式碼範例：

```csharp
public class ImplicitStylesPageCS : ContentPage
{
    public ImplicitStylesPageCS ()
    {
        var entryStyle = new Style (typeof(Entry)) {
            Setters = {
                ...
                new Setter { Property = Entry.TextColorProperty, Value = Color.Blue }
            }
        };

        ...
        Resources = new ResourceDictionary ();
        Resources.Add (entryStyle);

        Content = new StackLayout {
            Children = {
                new Entry { Text = "These entries" },
                new Entry { Text = "are demonstrating" },
                new Entry { Text = "implicit styles," },
                new Entry { Text = "and an implicit style override", BackgroundColor = Color.Lime, TextColor = Color.Red },
                new CustomEntry  { Text = "Subclassed Entry is not receiving the style" }
            }
        };
    }
}
```

建構函式定義單一*隱含*樣式套用至網頁的[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)執行個體。 `Style`用來顯示背景為黃色，藍色文字，也可以將其他外觀選項。 `Style`加入至網頁的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)但未指定`key`字串。 因此，`Style`套用至所有`Entry`它們符合隱含執行個體[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)屬性`Style`完全。 不過，`Style`不會套用到`CustomEntry`執行個體，也就是子類別化`Entry`。

## <a name="summary"></a>總結

*隱含*樣式是使用相同的所有視覺化項目[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)，而不需要每個控制項，來參考樣式。 A`Style`進行*隱含*未指定`x:Key`屬性。 相反地，`x:Key`屬性就會自動變成值[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)屬性。



## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
