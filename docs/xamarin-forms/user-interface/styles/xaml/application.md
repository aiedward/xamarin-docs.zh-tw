---
title：「全域樣式在「描述：」中， Xamarin.Forms 可以藉由將樣式新增至應用程式的資源字典來全域提供。 這有助於避免在頁面或控制項之間重複樣式。」
assetid： BDC65F82-65E0-4C8E-BB91-8E340EB2D15A ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：02/17/2016 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="global-styles-in-xamarinforms"></a>中的全域樣式Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_您可以藉由將樣式新增至應用程式的資源字典，將其設為全域可供使用。這有助於避免跨頁面或控制項的樣式重複。_

## <a name="create-a-global-style-in-xaml"></a>在 XAML 中建立全域樣式

根據預設， Xamarin.Forms 從範本建立的所有應用程式都會使用**應用程式**類別來執行子 [`Application`](xref:Xamarin.Forms.Application) 類別。 若要在 [`Style`](xref:Xamarin.Forms.Style) 應用層級宣告，請在應用程式的 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) using xaml 中，使用 xaml**應用**程式類別和相關聯的程式碼後置來取代預設的**app**類別。 如需詳細資訊，請參閱[使用應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)。

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

這 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 會定義一個*明確*的樣式， `buttonStyle` 這將用來設定實例的外觀 [`Button`](xref:Xamarin.Forms.Button) 。 不過，全域樣式可以是*明確*或*隱含*的。

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

[![](application-images/application-styles-1.png "Global Styles Example")](application-images/application-styles-1-large.png#lightbox "Global Styles Example")

如需在頁面中建立樣式的詳細資訊 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，請參閱[明確樣式](~/xamarin-forms/user-interface/styles/explicit.md)和[隱含樣式](~/xamarin-forms/user-interface/styles/implicit.md)。

### <a name="override-styles"></a>覆寫樣式

在視圖階層架構中較低的樣式，其優先順序會高於定義的較高層級。 例如，將設定 [`Style`](xref:Xamarin.Forms.Style) [`Button.TextColor`](xref:Xamarin.Forms.Button.TextColor) 為 `Red` 在應用層級的設定，將會被設定為的頁面層級樣式所覆寫 `Button.TextColor` `Green` 。 同樣地，控制項層級樣式也會覆寫頁面層級樣式。 此外，如果 `Button.TextColor` 直接在控制項屬性上設定，這會優先于任何樣式。 下列程式碼範例示範此優先順序：

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

在 `buttonStyle` 應用層級定義的原始來源，會由 `buttonStyle` 在頁面層級定義的實例覆寫。 此外，頁面層級樣式會由控制項層級覆寫 `buttonStyle` 。 因此， [`Button`](xref:Xamarin.Forms.Button) 實例會以藍色文字顯示，如下列螢幕擷取畫面所示：

[![](application-images/application-styles-2.png "Overriding Styles Example")](application-images/application-styles-2-large.png#lightbox "Overriding Styles Example")

## <a name="create-a-global-style-in-c35"></a>在 C&#35; 中建立全域樣式

[`Style`](xref:Xamarin.Forms.Style)藉 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 由建立新的，然後將實例新增至，即可在 c # 中將實例加入至應用程式的集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下列程式碼範例所示：

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

此函式會定義在整個應用程式中套用至實例的單一*明確*樣式 [`Button`](xref:Xamarin.Forms.Button) 。 *明確* [`Style`](xref:Xamarin.Forms.Style)實例會 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 使用方法加入至 [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)) ，並指定 `key` 要參考實例的字串 `Style` 。 `Style`然後，可以將實例套用至應用程式中正確類型的任何控制項。 不過，全域樣式可以是*明確*或*隱含*的。

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
- [基本樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
