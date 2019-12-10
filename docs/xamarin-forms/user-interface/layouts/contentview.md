---
title: Xamarin. Forms ContentView
description: 本文說明如何使用 ContentView 類別來建立自訂控制項，例如 CardView 範例。
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: 69f3311834fd438af97b3d2fa527572f02d2b0cb
ms.sourcegitcommit: fa2898d95b35fcee05503f3829351ba5a7d4a44d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74955075"
---
# <a name="xamarinforms-contentview"></a>Xamarin. Forms ContentView

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

[`ContentView`](xref:Xamarin.Forms.ContentView)類別是包含單一子專案的 `Layout` 類型，通常用來建立自訂、可重複使用的控制項。 `ContentView` 類別繼承自[`TemplatedView`](xref:Xamarin.Forms.TemplatedView)。 本文和相關的範例，說明如何根據 `ContentView` 類別建立自訂的 `CardView` 控制項。

下列螢幕擷取畫面顯示衍生自 `ContentView` 類別的 `CardView` 控制項：

[![CardView 範例應用程式螢幕擷取畫面](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

`ContentView` 類別會定義單一屬性：

* [`Content`](xref:Xamarin.Forms.ContentView.Content)是 `View` 物件。 這個屬性是由[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)物件所支援，因此它可以是資料系結的目標。

`ContentView` 也會繼承 `TemplatedView` 類別中的屬性：

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)是可以定義或覆寫控制面板的 `ControlTemplate`。

如需 `ControlTemplate` 屬性的詳細資訊，請參閱[使用 ControlTemplate 自訂外觀](#customize-appearance-with-a-controltemplate)。

## <a name="create-a-custom-control"></a>建立自訂控制項

`ContentView` 類別本身提供一些功能，但可用於建立自訂控制項。 範例專案會定義一個 `CardView` 控制項-一個 UI 專案，它會在類似卡片的版面配置中顯示影像、標題和描述。

建立自訂控制項的程式是：

1. 使用 Visual Studio 2019 中的 `ContentView` 範本來建立新的類別。
1. 在新自訂控制項的程式碼後置檔案中，定義任何唯一的屬性或事件。
1. 建立自訂控制項的 UI。

> [!NOTE]
> 您可以建立自訂控制項，其配置定義于程式碼中，而不是 XAML。 為了簡單起見，範例應用程式只會定義具有 XAML 版面配置的單一 `CardView` 類別。 不過，範例應用程式包含一個**CardViewCodePage**類別，它會顯示在程式碼中使用自訂控制項的程式。

### <a name="create-code-behind-properties"></a>建立程式碼後置屬性

`CardView` 自訂控制項會定義下列屬性：

* `CardTitle`：代表卡片上所顯示之標題的 `string` 物件。
* `CardDescription`：代表卡片上顯示之描述的 `string` 物件。
* `IconImageSource`：代表卡片上顯示之影像的 `ImageSource` 物件。
* `IconBackgroundColor`：代表卡片上顯示之影像背景色彩的 `Color` 物件。
* `BorderColor`：代表卡片框線、影像框線和分隔線色彩的 `Color` 物件。
* `CardColor`：代表卡片背景色彩的 `Color` 物件。

> [!NOTE]
> 基於示範目的，`BorderColor` 屬性會影響多個專案。 如有需要，此屬性可分為三個屬性。

每個屬性都受到 `BindableProperty` 實例的支援。 支援的 `BindableProperty` 可讓每個屬性使用 MVVM 模式來進行樣式化和系結。

下列範例顯示如何建立支援 `BindableProperty`：

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

自訂屬性會使用 `GetValue` 和 `SetValue` 方法來取得和設定 `BindableProperty` 物件值：

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

如需 `BindableProperty` 物件的詳細資訊，請參閱可系結[屬性](~/xamarin-forms/xaml/bindable-properties.md)。

### <a name="define-ui"></a>定義 UI

自訂控制項 UI 會使用 `ContentView` 做為 `CardView` 控制項的根項目。 下列範例顯示 `CardView` XAML：

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Grey'}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle, FallbackValue='Card Title'}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     ... />
            <Label Text="{Binding CardDescription, FallbackValue='Card description text.'}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

`ContentView` 元素會將 `x:Name` 屬性設定為**這個**，這可以用來存取系結至 `CardView` 實例的物件。 版面配置中的元素會將其屬性的系結設定為系結物件上定義的值。

如需資料繫結的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

> [!NOTE]
> 如果系結 `null`，`FallbackValue` 屬性會提供預設值。 這也可讓 Visual Studio 中的[XAML 預覽](~/xamarin-forms/xaml/xaml-previewer/index.md)器呈現 `CardView` 控制項。

## <a name="instantiate-a-custom-control"></a>具現化自訂控制項

自訂控制項命名空間的參考必須加入至具現化自訂控制項的頁面。 下列範例顯示的命名空間參考，稱為在 XAML 中新增至 `ContentPage` 實例的**控制項**：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

加入參考之後，就可以在 XAML 中具現化 `CardView`，並定義其屬性：

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

`CardView` 也可以在程式碼中具現化：

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

## <a name="customize-appearance-with-a-controltemplate"></a>使用 ControlTemplate 自訂外觀

從 `ContentView` 類別衍生的自訂控制項可以使用 XAML、程式碼來定義外觀，或完全不會定義外觀。 不論外觀的定義方式為何，`ControlTemplate` 物件都可以使用自訂版面配置來覆寫外觀。

在某些使用案例中，`CardView` 版面配置可能會佔用太多空間。 `ControlTemplate` 可以覆寫 `CardView` 配置，以提供更精簡的觀點，適用于壓縮的清單：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

`ControlTemplate` 中的資料系結會使用 `TemplateBinding` 標記延伸來指定系結。 然後可以使用其 `x:Key` 值，將 `ControlTemplate` 屬性設定為已定義的 ControlTemplate 物件。 下列範例顯示 `CardView` 實例上設定的 `ControlTemplate` 屬性：

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

下列螢幕擷取畫面顯示標準 `CardView` 實例，以及已覆寫 `ControlTemplate` 的 `CardView`：

[![CardView ControlTemplate 螢幕擷取畫面](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

如需控制項範本的詳細資訊，請參閱[Xamarin 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)。

## <a name="related-links"></a>相關連結

* [ContentView 範例應用程式](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Xamarin. 表單資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* 可系結[屬性](~/xamarin-forms/xaml/bindable-properties.md)。
* [Xamarin. Forms 控制項範本](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
