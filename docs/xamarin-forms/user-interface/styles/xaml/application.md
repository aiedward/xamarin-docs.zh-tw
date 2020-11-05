---
title: 中的全域樣式 Xamarin.Forms
description: 您可以藉由將樣式加入至應用程式的資源字典，使其成為全域可用。 這有助於避免跨頁面或控制項重複樣式。
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8d24300dcee76511466ec97f4944fe0be1278354
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371582"
---
# <a name="global-styles-in-no-locxamarinforms"></a>中的全域樣式 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_您可以藉由將樣式加入至應用程式的資源字典，使其成為全域可用。這有助於避免跨頁面或控制項重複樣式。_

## <a name="create-a-global-style-in-xaml"></a>在 XAML 中建立全域樣式

根據預設， Xamarin.Forms 從範本建立的所有應用程式會使用 **應用程式** 類別來執行子類別 [`Application`](xref:Xamarin.Forms.Application) 。 若要在 [`Style`](xref:Xamarin.Forms.Style) 應用層級宣告，在應用程式中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 使用 xaml 時，預設的 **應用** 程式類別必須取代為 XAML **應用** 程式類別和相關聯的程式碼後端。 如需詳細資訊，請參閱 [使用應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

下列程式碼範例顯示在 [`Style`](xref:Xamarin.Forms.Style) 應用層級宣告的：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.App">
    <Application.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                <Setter Property="BorderColor" Value="Lime" />
                <Setter Property="BorderRadius" Value="5" />
                <Setter Property="BorderWidth" Value="5" />
                <Setter Property="WidthRequest" Value="200" />
                <Setter Property="TextColor" Value="Teal" />
            </Style>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

這 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 定義了單一的 *明確* 樣式， `buttonStyle` 將用來設定實例的外觀 [`Button`](xref:Xamarin.Forms.Button) 。 不過，全域樣式可以是 *明確* 或 *隱含* 的。

下列程式碼範例顯示將套用 `buttonStyle` 至頁面實例的 XAML 頁面 [`Button`](xref:Xamarin.Forms.Button) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

這會導致下列螢幕擷取畫面中顯示的外觀：

[![全域樣式範例](application-images/application-styles-1.png)](application-images/application-styles-1-large.png#lightbox "全域樣式範例")

如需在頁面中建立樣式的詳細資訊 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，請參閱 [明確樣式](~/xamarin-forms/user-interface/styles/explicit.md) 和 [隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

### <a name="override-styles"></a>覆寫樣式

視圖階層中較低的樣式優先于定義較高的樣式。 例如， [`Style`](xref:Xamarin.Forms.Style) [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) `Red` 在應用層級設定為的，將會由設定為的頁面層級樣式來覆 `Button.TextColor` 寫 `Green` 。 同樣地，控制項層級樣式將會覆寫頁面層級樣式。 此外，如果 `Button.TextColor` 是在控制項屬性上直接設定，則會優先于任何樣式。 下列程式碼範例示範此優先順序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" IconImageSource="xaml.png">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="buttonStyle" TargetType="Button">
                ...
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <StackLayout.Resources>
                <ResourceDictionary>
                    <Style x:Key="buttonStyle" TargetType="Button">
                        ...
                        <Setter Property="TextColor" Value="Blue" />
                    </Style>
                </ResourceDictionary>
            </StackLayout.Resources>
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

在 `buttonStyle` 應用層級定義的原始，會由 `buttonStyle` 在頁面層級定義的實例覆寫。 此外，頁面層級樣式是由控制項層級覆寫 `buttonStyle` 。 因此，這些 [`Button`](xref:Xamarin.Forms.Button) 實例會以藍色文字顯示，如下列螢幕擷取畫面所示：

[![覆寫樣式範例](application-images/application-styles-2.png)](application-images/application-styles-2-large.png#lightbox "覆寫樣式範例")

## <a name="create-a-global-style-in-c35"></a>在 C&#35; 中建立全域樣式

[`Style`](xref:Xamarin.Forms.Style) 藉由建立新的，然後將實例加入至，即可將實例加入至應用程式的 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下列程式碼範例所示：

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,    Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

此 *函* 式會定義套用至 [`Button`](xref:Xamarin.Forms.Button) 整個應用程式實例的單一明確樣式。 *明確* [`Style`](xref:Xamarin.Forms.Style) 使用方法將實例新增至 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) ，並指定 `key` 要參考該實例的字串 `Style` 。 `Style`然後，可以將實例套用至應用程式中正確型別的任何控制項。 不過，全域樣式可以是 *明確* 或 *隱含* 的。

下列程式碼範例顯示將套用 `buttonStyle` 至頁面實例的 c # 頁面 [`Button`](xref:Xamarin.Forms.Button) ：

```csharp
public class ApplicationStylesPageCS : ContentPage
{
    public ApplicationStylesPageCS ()
    {
        ...
        Content = new StackLayout {
            Children = {
                new Button { Text = "These buttons", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "are demonstrating", Style = (Style)Application.Current.Resources ["buttonStyle"] },
                new Button { Text = "application styles", Style = (Style)Application.Current.Resources ["buttonStyle"]
                }
            }
        };
    }
}
```

`buttonStyle`會藉 [`Button`](xref:Xamarin.Forms.Button) 由設定其屬性來套用至實例 [`Style`](xref:Xamarin.Forms.NavigableElement.Style) ，並控制實例的外觀 `Button` 。

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [ (範例) 的基本樣式 ](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式 (範例) ](/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)