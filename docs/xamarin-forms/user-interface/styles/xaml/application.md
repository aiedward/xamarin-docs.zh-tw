---
title: 全域樣式
description: 樣式可供全域將它們新增至應用程式的資源字典。 這有助於避免跨頁或控制項的出現重複的樣式。
ms.prod: xamarin
ms.assetid: BDC65F82-65E0-4C8E-BB91-8E340EB2D15A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: a505720e5fef8fe9e9ef82d03e53370210772f45
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="global-styles"></a>全域樣式

_樣式可供全域將它們新增至應用程式的資源字典。這有助於避免跨頁或控制項的出現重複的樣式。_

## <a name="creating-a-global-style-in-xaml"></a>在 XAML 中建立全域的樣式

根據預設，從範本建立的所有 Xamarin.Forms 應用程式都使用**應用程式**類別來實作[ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)子類別。 若要宣告[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)應用程式層級，在應用程式的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用 XAML，預設值**應用程式**類別都必須取代 XAML**應用程式**類別和相關聯的程式碼後置。 如需詳細資訊，請參閱[使用應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

下列程式碼範例示範[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)應用程式層級宣告：

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

這[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)定義單一*明確*樣式， `buttonStyle`，這會用來設定的外觀[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)執行個體。 不過，全域樣式可能*明確*或*隱含*。

下列程式碼範例會顯示 XAML 頁面套用`buttonStyle`頁面[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)執行個體：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
    <ContentPage.Content>
        <StackLayout Padding="0,20,0,0">
            <Button Text="These buttons" Style="{StaticResource buttonStyle}" />
            <Button Text="are demonstrating" Style="{StaticResource buttonStyle}" />
            <Button Text="application style overrides" Style="{StaticResource buttonStyle}" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

這會導致下列螢幕擷取畫面所示的外觀：

[![](application-images/application-styles-1.png "全域樣式範例")](application-images/application-styles-1-large.png#lightbox "全域樣式範例")

如需有關建立頁面的樣式資訊[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)和[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

### <a name="overriding-styles"></a>覆寫樣式

樣式檢視階層中較低的優先順序高於定義更高版本上。 例如，設定[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)設定[ `Button.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)至`Red`在應用程式層級將會覆寫設定的頁面層級樣式`Button.TextColor`來`Green`. 同樣地，將會覆寫控制層級的樣式頁面層級的樣式。 此外，如果`Button.TextColor`設定直接上的控制項屬性，這將會優先於任何樣式。 下列程式碼範例示範此優先順序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Styles.ApplicationStylesPage" Title="Application" Icon="xaml.png">
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

原始`buttonStyle`、 定義應用程式層級時，會覆寫`buttonStyle`在頁面層級定義的執行個體。 此外，網頁層級的樣式會覆寫控制層級`buttonStyle`。 因此， [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)藍色文字，會顯示執行個體，如下列螢幕擷取畫面所示：

[![](application-images/application-styles-2.png "覆寫樣式範例")](application-images/application-styles-2-large.png#lightbox "覆寫樣式範例")

## <a name="creating-a-global-style-in-c35"></a>在 C 中建立全域的樣式&#35;

[`Style`](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) 執行個體可以新增到應用程式的[ `Resources` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Resources/) C# 中建立新的集合[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，再以加入`Style`執行個體來`ResourceDictionary`，做為下列程式碼範例所示：

```csharp
public class App : Application
{
    public App ()
    {
        var buttonStyle = new Style (typeof(Button)) {
            Setters = {
                ...
                new Setter { Property = Button.TextColorProperty,   Value = Color.Teal }
            }
        };

        Resources = new ResourceDictionary ();
        Resources.Add ("buttonStyle", buttonStyle);
        ...
    }
    ...
}
```

建構函式定義單一*明確*樣式套用至[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)整個應用程式的執行個體。 *明確* [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)執行個體加入至[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)使用[ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.ResourceDictionary.Add/p/System.String/System.Object/)方法，並指定`key`字串來參考`Style`執行個體。 `Style`執行個體接著可以套用至應用程式中正確類型的任何控制項。 不過，全域樣式可能*明確*或*隱含*。

下列程式碼範例將示範 C# 頁面套用`buttonStyle`頁面[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)執行個體：

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

`buttonStyle`套用至[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)藉由設定執行個體及其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性和控制項的外觀`Button`執行個體。

## <a name="summary"></a>總結

樣式可供全域將它們新增至應用程式的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 這有助於避免跨頁或控制項的出現重複的樣式。



## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本的樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Styles/BasicStyles/)
- [使用樣式 （範例）](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
- [樣式](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
- [Setter](https://developer.xamarin.com/api/type/Xamarin.Forms.Setter/)
