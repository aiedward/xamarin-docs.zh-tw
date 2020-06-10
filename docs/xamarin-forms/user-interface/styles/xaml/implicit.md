---
title：在「描述：」中的隱含樣式 Xamarin.Forms 是由同一個 TargetType 的所有控制項所使用的樣式，而不需要每個控制項參考樣式。」
assetid： 02A75F3B-4389-49D4-A2F4-AFD473A4A161 ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：01/30/2019 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="implicit-styles-in-xamarinforms"></a>中的隱含樣式Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_隱含樣式是由相同 TargetType 的所有控制項所使用，而不需要每個控制項參考樣式。_

## <a name="create-an-implicit-style-in-xaml"></a>在 XAML 中建立隱含樣式

若要 [`Style`](xref:Xamarin.Forms.Style) 在頁面層級宣告， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 必須將加入至頁面，然後一或多個宣告 `Style` 可以包含在中 `ResourceDictionary` 。 `Style`不指定屬性，而成為*隱含*的 `x:Key` 。 然後，樣式會套用至完全符合的視覺效果元素 `TargetType` ，但不會套用至衍生自值的元素 `TargetType` 。

下列程式碼範例顯示在頁面的 XAML 中宣告的*隱含*樣式 `ResourceDictionary` ，並套用至頁面的 [`Entry`](xref:Xamarin.Forms.Entry) 實例：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
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

[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)會定義套用至頁面實例的單一*隱含*樣式 [`Entry`](xref:Xamarin.Forms.Entry) 。 `Style`是用來在黃色背景上顯示藍色文字，同時也設定其他外觀選項。 `Style`會新增至頁面的， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 但不會指定 `x:Key` 屬性。 因此， `Style` 會隱含地套用到所有 `Entry` 實例，因為它們完全符合的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 屬性 `Style` 。 不過， `Style` 不會套用至 `CustomEntry` 實例，也就是子類別化 `Entry` 。 這會導致下列螢幕擷取畫面中顯示的外觀：

[![隱含樣式範例](implicit-images/implicit-styles.png)](implicit-images/implicit-styles-large.png#lightbox)

此外，第四個會將 [`Entry`](xref:Xamarin.Forms.Entry) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) 隱含樣式的和屬性覆寫 [`TextColor`](xref:Xamarin.Forms.InputView.TextColor) 為不同的 `Color` 值。

### <a name="create-an-implicit-style-at-the-control-level"></a>在控制項層級建立隱含樣式

除了在頁面層級建立*隱含*樣式之外，也可以在控制項層級建立，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Styles;assembly=Styles" x:Class="Styles.ImplicitStylesPage" Title="Implicit" IconImageSource="xaml.png">
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

在此範例中，會將*隱含* [`Style`](xref:Xamarin.Forms.Style) 指派給 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 控制項的集合 [`StackLayout`](xref:Xamarin.Forms.StackLayout) 。 然後，可以將*隱含*樣式套用至控制項及其子系。

如需在應用程式中建立樣式的詳細資訊 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，請參閱[全域樣式](~/xamarin-forms/user-interface/styles/application.md)。

## <a name="create-an-implicit-style-in-c35"></a>在 C&#35; 中建立隱含樣式

[`Style`](xref:Xamarin.Forms.Style)藉由建立新的，然後將實例新增至，即可將實例加入至頁面的 [`Resources`](xref:Xamarin.Forms.VisualElement.Resources) 集合 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) `Style` `ResourceDictionary` ，如下列程式碼範例所示：

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

此函數會定義套用至頁面實例的單一*隱含*樣式 [`Entry`](xref:Xamarin.Forms.Entry) 。 `Style`是用來在黃色背景上顯示藍色文字，同時也設定其他外觀選項。 `Style`會新增至頁面的， [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 但不會指定 `key` 字串。 因此， `Style` 會隱含地套用到所有 `Entry` 實例，因為它們完全符合的 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 屬性 `Style` 。 不過， `Style` 不會套用至 `CustomEntry` 實例，也就是子類別化 `Entry` 。

## <a name="apply-a-style-to-derived-types"></a>將樣式套用至衍生類型

[`Style.ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes)屬性可讓樣式套用至衍生自屬性所參考之基底類型的控制項 [`TargetType`](xref:Xamarin.Forms.Style.TargetType) 。 因此，將此屬性設定為可 `true` 讓單一樣式以多個類型為目標，前提是該類型衍生自屬性中所指定的基底類型 `TargetType` 。

下列範例顯示將實例的背景色彩設定為紅色的隱含樣式 [`Button`](xref:Xamarin.Forms.Button) ：

```xaml
<Style TargetType="Button"
       ApplyToDerivedTypes="True">
    <Setter Property="BackgroundColor"
            Value="Red" />
</Style>
```

將此樣式放在頁面層級中 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) ，會導致它套用至 [`Button`](xref:Xamarin.Forms.Button) 頁面上的所有實例，也會套用至衍生自的任何控制項 `Button` 。 不過，如果 [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) 屬性保持未設定，則樣式只會套用至 `Button` 實例。

對等的 C# 程式碼為：

```csharp
var buttonStyle = new Style(typeof(Button))
{
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.Red
        }
    }
};

Resources = new ResourceDictionary { buttonStyle };
```

## <a name="related-links"></a>相關連結

- [XAML 標記延伸](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [基本樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
- [使用樣式（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary) \(英文\)
- [Style](xref:Xamarin.Forms.Style)
- [Setter](xref:Xamarin.Forms.Setter)
