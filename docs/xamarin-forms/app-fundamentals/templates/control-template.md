---
title: Xamarin. Forms 控制項範本
description: '[Xamarin] 控制項範本會定義 ContentView 衍生自訂控制項的視覺化結構，以及 ContentPage 衍生的頁面。'
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
ms.openlocfilehash: 707e105b8535cbbb2819c5b8daeaa32bf6c5da37
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2020
ms.locfileid: "75956592"
---
# <a name="xamarinforms-control-templates"></a>Xamarin. Forms 控制項範本

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

[Xamarin] 控制項範本可讓您定義[`ContentView`](xref:Xamarin.Forms.ContentView)衍生自訂控制項的視覺化結構，並[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面。 控制項範本會將自訂控制項或頁面的使用者介面（UI）與執行控制項或頁面的邏輯分開。 您也可以在預先定義的位置，將其他內容插入樣板化自訂控制項或樣板化頁面中。

例如，您可以建立控制項範本來重新定義自訂控制項所提供的 UI。 然後，必要的自訂控制項實例可以使用控制項範本。 或者，也可以建立控制項範本，以定義應用程式中多個頁面將使用的任何通用 UI。 然後，可以使用多個頁面的控制項範本，而每個頁面仍會顯示其唯一的內容。

## <a name="create-a-controltemplate"></a>建立 ControlTemplate

下列範例顯示 `CardView` 自訂控制項的程式碼：

```csharp
public class CardView : ContentView
{
    public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(nameof(CardTitle), typeof(string), typeof(CardView), string.Empty);
    public static readonly BindableProperty CardDescriptionProperty = BindableProperty.Create(nameof(CardDescription), typeof(string), typeof(CardView), string.Empty);
    // ...

    public string CardTitle
    {
        get => (string)GetValue(CardTitleProperty);
        set => SetValue(CardTitleProperty, value);
    }

    public string CardDescription
    {
        get => (string)GetValue(CardDescriptionProperty);
        set => SetValue(CardDescriptionProperty, value);
    }
    // ...
}
```

衍生自[`ContentView`](xref:Xamarin.Forms.ContentView)類別的 `CardView` 類別，代表以卡片類似配置顯示資料的自訂控制項。 類別包含屬性，其會針對所顯示的資料，以可系結屬性為後盾。 不過，`CardView` 類別不會定義任何 UI。 相反地，UI 會以控制項範本定義。 如需建立 `ContentView` 衍生自訂控制項的詳細資訊，請參閱[ContentView](~/xamarin-forms/user-interface/layouts/contentview.md)。

建立具有[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)類型的控制項範本。 當您建立 `ControlTemplate`時，您會結合[`View`](xref:Xamarin.Forms.View)物件來建立自訂控制項或頁面的 UI。 `ControlTemplate` 只能有一個 `View` 做為其根項目。 不過，根項目通常會包含其他 `View` 物件。 這些物件的組合會構成控制項的視覺結構。

雖然可以用內嵌方式定義[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ，但宣告[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)的一般方法是做為資源字典中的資源。 由於控制項範本是資源，因此它們會遵守適用于所有資源的相同範圍規則。 例如，如果您在應用程式定義 XAML 檔案的根項目中宣告控制項範本，則可以在應用程式的任何位置使用此範本。 如果您在頁面中定義範本，只有該頁面可以使用控制項範本。 如需資源的詳細資訊，請參閱[Xamarin. Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

下列 XAML 範例顯示 `CardView` 物件的[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
      <ControlTemplate x:Key="CardViewControlTemplate">
          <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                 BackgroundColor="{Binding CardColor}"
                 BorderColor="{Binding BorderColor}"
                 CornerRadius="5"
                 HasShadow="True"
                 Padding="8"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">
              <Grid>
                  <Grid.RowDefinitions>
                      <RowDefinition Height="75" />
                      <RowDefinition Height="4" />
                      <RowDefinition Height="Auto" />
                  </Grid.RowDefinitions>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="75" />
                      <ColumnDefinition Width="200" />
                  </Grid.ColumnDefinitions>
                  <Frame IsClippedToBounds="True"
                         BorderColor="{Binding BorderColor}"
                         BackgroundColor="{Binding IconBackgroundColor}"
                         CornerRadius="38"
                         HeightRequest="60"
                         WidthRequest="60"
                         HorizontalOptions="Center"
                         VerticalOptions="Center">
                      <Image Source="{Binding IconImageSource}"
                             Margin="-20"
                             WidthRequest="100"
                             HeightRequest="100"
                             Aspect="AspectFill" />
                  </Frame>
                  <Label Grid.Column="1"
                         Text="{Binding CardTitle}"
                         FontAttributes="Bold"
                         FontSize="Large"
                         VerticalTextAlignment="Center"
                         HorizontalTextAlignment="Start" />
                  <BoxView Grid.Row="1"
                           Grid.ColumnSpan="2"
                           BackgroundColor="{Binding BorderColor}"
                           HeightRequest="2"
                           HorizontalOptions="Fill" />
                  <Label Grid.Row="2"
                         Grid.ColumnSpan="2"
                         Text="{Binding CardDescription}"
                         VerticalTextAlignment="Start"
                         VerticalOptions="Fill"
                         HorizontalOptions="Fill" />
              </Grid>
          </Frame>
      </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

當[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)宣告為資源時，它必須具有以 `x:Key` 屬性指定的索引鍵，才能在資源字典中識別。 在此範例中，`CardViewControlTemplate` 的根項目是[`Frame`](xref:Xamarin.Forms.Frame)物件。 `Frame` 物件使用 `RelativeSource` 標記延伸，將其 `BindingContext` 設定為將套用範本的執行時間物件實例，也就是所謂的樣板*化父系*。 `Frame` 物件使用[`Grid`](xref:Xamarin.Forms.Grid)、`Frame`、 [`Image`](xref:Xamarin.Forms.Image)、 [`Label`](xref:Xamarin.Forms.Label)和[`BoxView`](xref:Xamarin.Forms.BoxView)物件的組合，以定義 `CardView` 物件的視覺化結構。 這些物件的系結運算式會針對 `CardView` 屬性進行解析，因為繼承根 `Frame` 元素的 `BindingContext`。 如需 `RelativeSource` 標記延伸的詳細資訊，請參閱[Xamarin. 表單相對](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)系結。

## <a name="consume-a-controltemplate"></a>使用 ControlTemplate

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)可以套用至[`ContentView`](xref:Xamarin.Forms.ContentView)衍生的自訂控制項，方法是將其[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)屬性設定為控制項範本物件。 同樣地， [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)可以藉由將其[`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate)屬性設定為控制項範本物件，來套用至[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面。 在執行時間，套用[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)時，在 `ControlTemplate` 中定義的所有控制項都會加入至樣板化自訂控制項或樣板化頁面的視覺化樹狀結構中。

下列範例顯示指派給每個 `CardView` 物件之[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)屬性的 `CardViewControlTemplate`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

在此範例中，`CardViewControlTemplate` 中的控制項會成為每個 `CardView` 物件之視覺化樹狀結構的一部分。 由於控制項範本的根[`Frame`](xref:Xamarin.Forms.Frame)物件會將其 `BindingContext` 設定為樣板化父系，因此 `Frame` 及其子系會針對每個 `CardView` 物件的屬性，解析其系結運算式。

下列螢幕擷取畫面顯示適用于三個 `CardView` 物件的 `CardViewControlTemplate`：

[![IOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/relativesource-controltemplate.png "樣板化的 CardView 物件")](control-template-images/relativesource-controltemplate-large.png#lightbox "樣板化的 CardView 物件")

> [!IMPORTANT]
> 藉由覆寫樣板化自訂控制項或樣板化頁面中的 `OnApplyTemplate` 方法，可以偵測到[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)套用至控制項實例的時間點。 如需詳細資訊，請參閱[從範本取得已命名的元素](#get-a-named-element-from-a-template)。

## <a name="pass-parameters-with-templatebinding"></a>使用 TemplateBinding 傳遞參數

`TemplateBinding` 標記延伸會將[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中專案的屬性系結至樣板化自訂控制項或樣板化頁面所定義的公用屬性。 當您使用 `TemplateBinding`時，您會啟用控制項的屬性，作為範本的參數。 因此，當設定樣板化自訂控制項或樣板化頁面上的屬性時，該值會傳遞至其上有 `TemplateBinding` 的元素。

> [!IMPORTANT]
> `TemplateBinding` 標記延伸是建立[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)的替代方法，其使用 `RelativeSource` 標記延伸，將範本中根項目的 `BindingContext` 設定為其樣板化父系。 `TemplateBinding` 標記延伸會排除 `RelativeSource` 系結，並使用 `TemplateBinding` 運算式來取代 `Binding` 運算式。

`TemplateBinding` 標記延伸會定義下列屬性：

- `Path`，屬於 `string`類型的，這是屬性的路徑。
- `Mode`，屬於 `BindingMode`類型，這是變更在*來源*和*目標*之間傳播的方向。
- `Converter`，屬於 `IValueConverter`類型的系結值轉換器。
- `ConverterParameter`，屬於 `object`類型，系結值轉換器的參數。
- `StringFormat`，屬於 `string`類型，這是系結的字串格式。

`TemplateBinding` 標記延伸的 `ContentProperty` 會 `Path`。 因此，如果路徑是 `TemplateBinding` 運算式中的第一個專案，則會省略標記延伸的 "Path =" 部分。 如需在系結運算式中使用這些屬性的詳細資訊，請參閱[Xamarin. Forms 資料](~/xamarin-forms/app-fundamentals/data-binding/index.md)系結。

> [!WARNING]
> `TemplateBinding` 標記延伸模組只能在[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)內使用。 不過，嘗試在 `ControlTemplate` 外使用 `TemplateBinding` 運算式不會導致組建錯誤或擲回例外狀況。

下列 XAML 範例顯示使用 `TemplateBinding` 標記延伸之 `CardView` 物件的[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BackgroundColor="{TemplateBinding CardColor}"
                   BorderColor="{TemplateBinding BorderColor}"
                   CornerRadius="5"
                   HasShadow="True"
                   Padding="8"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="75" />
                        <RowDefinition Height="4" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="75" />
                        <ColumnDefinition Width="200" />
                    </Grid.ColumnDefinitions>
                    <Frame IsClippedToBounds="True"
                           BorderColor="{TemplateBinding BorderColor}"
                           BackgroundColor="{TemplateBinding IconBackgroundColor}"
                           CornerRadius="38"
                           HeightRequest="60"
                           WidthRequest="60"
                           HorizontalOptions="Center"
                           VerticalOptions="Center">
                        <Image Source="{TemplateBinding IconImageSource}"
                               Margin="-20"
                               WidthRequest="100"
                               HeightRequest="100"
                               Aspect="AspectFill" />
                    </Frame>
                    <Label Grid.Column="1"
                           Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold"
                           FontSize="Large"
                           VerticalTextAlignment="Center"
                           HorizontalTextAlignment="Start" />
                    <BoxView Grid.Row="1"
                             Grid.ColumnSpan="2"
                             BackgroundColor="{TemplateBinding BorderColor}"
                             HeightRequest="2"
                             HorizontalOptions="Fill" />
                    <Label Grid.Row="2"
                           Grid.ColumnSpan="2"
                           Text="{TemplateBinding CardDescription}"
                           VerticalTextAlignment="Start"
                           VerticalOptions="Fill"
                           HorizontalOptions="Fill" />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

在此範例中，`TemplateBinding` 標記延伸會針對每個 `CardView` 物件的屬性，解析系結運算式。 下列螢幕擷取畫面顯示適用于三個 `CardView` 物件的 `CardViewControlTemplate`：

[![IOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/templatebinding-controltemplate.png "樣板化的 CardView 物件")](control-template-images/templatebinding-controltemplate-large.png#lightbox "樣板化的 CardView 物件")

> [!IMPORTANT]
> 使用 `TemplateBinding` 標記延伸相當於將範本中根項目的 `BindingContext` 設定為具有 `RelativeSource` 標記延伸的樣板化父系，然後使用 `Binding` 標記延伸模組來解析子物件的系結。 事實上，`TemplateBinding` 標記延伸會建立 `RelativeBindingSource.TemplatedParent``Source` 的 `Binding`。

## <a name="apply-a-controltemplate-with-a-style"></a>套用樣式的 ControlTemplate

控制項範本也可以與樣式一起套用。 建立使用[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)的*隱含*或*明確*樣式，即可達成此目的。

下列 XAML 範例顯示使用 `CardViewControlTemplate`的*隱含*樣式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            ...
        </ControlTemplate>

        <Style TargetType="controls:CardView">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource CardViewControlTemplate}" />
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"/>
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
    </StackLayout>
</ContentPage>
```

在此範例中，*隱含* [`Style`](xref:Xamarin.Forms.Style)會自動套用至每個 `CardView` 物件，並將每個 `CardView` 的[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)屬性設定為 `CardViewControlTemplate`。

如需樣式的詳細資訊，請參閱[Xamarin. 表單樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="redefine-a-controls-ui"></a>重新定義控制項的 UI

當[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)具現化並指派給[`ContentView`](xref:Xamarin.Forms.ContentView)衍生自訂控制項的 `ControlTemplate` 屬性或[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面時，為自訂控制項或頁面定義的視覺化結構會取代為 `ControlTemplate`中定義的視覺化結構。

例如，`CardViewUI` 的自訂控制項會使用下列 XAML 定義其使用者介面：

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ControlTemplateDemos.Controls.CardViewUI"
             x:Name="this">
    <Frame BindingContext="{x:Reference this}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="75" />
                <ColumnDefinition Width="200" />
            </Grid.ColumnDefinitions>
            <Frame IsClippedToBounds="True"
                   BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Gray'}"
                   CornerRadius="38"
                   HeightRequest="60"
                   WidthRequest="60"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Image Source="{Binding IconImageSource}"
                       Margin="-20"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill" />
            </Frame>
            <Label Grid.Column="1"
                   Text="{Binding CardTitle, FallbackValue='Card title'}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     Grid.ColumnSpan="2"
                     BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Grid.ColumnSpan="2"
                   Text="{Binding CardDescription, FallbackValue='Card description'}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
        </Grid>
    </Frame>
</ContentView>
```

不過，組成此 UI 的控制項可以藉由在[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中定義新的視覺結構來取代，並將其指派給 `CardViewUI` 物件的[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)屬性：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="{TemplateBinding IconImageSource}"
                        BackgroundColor="{TemplateBinding IconBackgroundColor}"
                        WidthRequest="100"
                        HeightRequest="100"
                        Aspect="AspectFill"
                        HorizontalOptions="Center"
                        VerticalOptions="Center" />
                <StackLayout Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="John Doe"
                             CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Jane Doe"
                             CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Xamarin Monkey"
                             CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
    </StackLayout>
</ContentPage>
```

在此範例中，`CardViewUI` 物件的視覺化結構會在[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中重新定義，以提供更精簡的視覺效果結構，適用于精簡的清單：

[![IOS 和 Android 上樣板化 CardViewUI 物件的螢幕擷取畫面](control-template-images/redefine-controltemplate.png "樣板化的 CardViewUI 物件")](control-template-images/redefine-controltemplate-large.png#lightbox "樣板化的 CardViewUI 物件")

## <a name="substitute-content-into-a-contentpresenter"></a>將內容替換成 ContentPresenter

[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)可以放在控制項範本中，以標示樣板化自訂控制項或樣板化頁面將顯示內容的位置。 接著，使用控制項範本的自訂控制項或頁面將會定義 `ContentPresenter`要顯示的內容。 下圖說明內含一些控制項的頁面 `ControlTemplate`，包括以藍色方框標示的 `ContentPresenter`：

![ContentPage 的控制項範本](control-template-images/control-template.png "ContentPage 的控制項範本")

下列 XAML 會顯示名為 `TealTemplate` 的控制項範本，其中包含其視覺化結構中的[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) ：

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="0.1*" />
            <RowDefinition Height="0.8*" />
            <RowDefinition Height="0.1*" />
        </Grid.RowDefinitions>
        <BoxView Color="Teal" />
        <Label Margin="20,0,0,0"
               Text="{TemplateBinding HeaderText}"
               TextColor="White"
               FontSize="Title"
               VerticalOptions="Center" />
        <ContentPresenter Grid.Row="1" />
        <BoxView Grid.Row="2"
                 Color="Teal" />
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        <controls:HyperlinkLabel Grid.Row="2"
                                 Margin="0,0,20,0"
                                 Text="Help"
                                 TextColor="White"
                                 Url="https://docs.microsoft.com/xamarin/xamarin-forms/"
                                 HorizontalOptions="End"
                                 VerticalOptions="Center" />
    </Grid>
</ControlTemplate>
```

下列範例會顯示指派給[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生頁面之[`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate)屬性的 `TealTemplate`：

```xaml
<controls:HeaderFooterPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"                           
                           ControlTemplate="{StaticResource TealTemplate}"
                           HeaderText="MyApp"
                           ...>
    <StackLayout Margin="10">
        <Entry Placeholder="Enter username" />
        <Entry Placeholder="Enter password"
               IsPassword="True" />
        <Button Text="Login" />
    </StackLayout>
</controls:HeaderFooterPage>
```

在執行時間，將 `TealTemplate` 套用至頁面時，會將頁面內容取代為控制項範本中定義的[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) ：

[![IOS 和 Android 上樣板化頁面物件的螢幕擷取畫面](control-template-images/tealtemplate-contentpage.png "樣板化 ContentPage")](control-template-images/tealtemplate-contentpage-large.png#lightbox "樣板化 ContentPage")

## <a name="get-a-named-element-from-a-template"></a>從範本取得具名元素

您可以從樣板化自訂控制項或樣板化頁面，抓取控制項範本中的命名元素。 這可以透過 `GetTemplateChild` 方法來達成，這會在具現化的[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)視覺化樹狀結構中傳回已命名的元素（如果找到的話）。 否則，它會傳回 `null`。

在控制項範本具現化之後，會呼叫範本的 `OnApplyTemplate` 方法。 因此，應該從樣板化控制項或樣板化頁面中的 `OnApplyTemplate` 覆寫呼叫 `GetTemplateChild` 方法。

> [!IMPORTANT]
> 只有在呼叫 `OnApplyTemplate` 方法之後，才應該呼叫 `GetTemplateChild` 方法。

下列 XAML 會顯示名為 `TealTemplate` 的控制項範本，可套用至[`ContentPage`](xref:Xamarin.Forms.ContentPage)衍生的頁面：

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        ...
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        ...
    </Grid>
</ControlTemplate>
```

在此範例中， [`Label`](xref:Xamarin.Forms.Label)元素的名稱為，而且可以在樣板化頁面的程式碼中抓取。 從樣板化頁面的 `OnApplyTemplate` 覆寫呼叫 `GetTemplateChild` 方法，即可達成此目的：

```csharp
public partial class AccessTemplateElementPage : HeaderFooterPage
{
    Label themeLabel;

    public AccessTemplateElementPage()
    {
        InitializeComponent();
    }

    protected override void OnApplyTemplate()
    {
        base.OnApplyTemplate();
        themeLabel = (Label)GetTemplateChild("changeThemeLabel");
        themeLabel.Text = OriginalTemplate ? "Aqua Theme" : "Teal Theme";
    }
}
```

在此範例中，已具現化 `ControlTemplate` 時，會抓取名為 `changeThemeLabel` 的[`Label`](xref:Xamarin.Forms.Label)物件。 `changeThemeLabel` 接著可由 `AccessTemplateElementPage` 類別存取並操作。 下列螢幕擷取畫面顯示 `Label` 所顯示的文字已變更：

[![IOS 和 Android 上樣板化頁面物件的螢幕擷取畫面](control-template-images/get-named-element.png "樣板化 ContentPage")](control-template-images/get-named-element-large.png#lightbox "樣板化 ContentPage")

## <a name="bind-to-a-viewmodel"></a>系結至 viewmodel

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)可以將資料系結至 viewmodel，即使 `ControlTemplate` 系結至樣板化父系（套用範本的執行時間物件實例）。

下列 XAML 範例顯示使用名為 `PeopleViewModel`之 viewmodel 的頁面：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ControlTemplateDemos"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.BindingContext>
        <local:PeopleViewModel />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <DataTemplate x:Key="PersonTemplate">
            <controls:CardView BorderColor="DarkGray"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Description}"
                               ControlTemplate="{StaticResource CardViewControlTemplate}" />
        </DataTemplate>
    </ContentPage.Resources>

    <StackLayout Margin="10"
                 BindableLayout.ItemsSource="{Binding People}"
                 BindableLayout.ItemTemplate="{StaticResource PersonTemplate}" />
</ContentPage>
```

在此範例中，頁面的 `BindingContext` 設定為 `PeopleViewModel` 實例。 此 viewmodel 會公開 `People` 集合和名為 `DeletePersonCommand`的 `ICommand`。 頁面上的[`StackLayout`](xref:Xamarin.Forms.StackLayout)會使用可系結的配置，將資料系結至 `People` 集合，而可系結配置的 `ItemTemplate` 則會設定為 `PersonTemplate` 資源。 這個[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)指定 `People` 集合中的每個專案都將使用 `CardView` 物件來顯示。 `CardView` 物件的視覺化結構是使用名為 `CardViewControlTemplate`的[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)來定義：

```xaml
<ControlTemplate x:Key="CardViewControlTemplate">
    <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Label Text="{Binding CardTitle}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     BackgroundColor="{Binding BorderColor}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Text="{Binding CardDescription}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
            <Button Text="Delete"
                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeletePersonCommand}"
                    CommandParameter="{Binding CardTitle}"
                    HorizontalOptions="End" />
        </Grid>
    </Frame>
</ControlTemplate>
```

在此範例中， [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)的根項目是[`Frame`](xref:Xamarin.Forms.Frame)物件。 `Frame` 物件使用 `RelativeSource` 標記延伸，將其 `BindingContext` 設定為樣板化父系。 由於繼承根 `Frame` 專案的 `BindingContext`，`Frame` 物件及其子系的系結運算式會針對 `CardView` 屬性進行解析。 下列螢幕擷取畫面顯示顯示 `People` 集合的頁面，其中包含三個專案：

[![IOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/viewmodel-controltemplate.png "樣板化的 CardView 物件")](control-template-images/viewmodel-controltemplate-large.png#lightbox "樣板化的 CardView 物件")

當[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中的物件系結至其樣板化父系上的屬性時，控制項範本內的[`Button`](xref:Xamarin.Forms.Button)會系結至其樣板化父系和 viewmodel 中的 `DeletePersonCommand`。 這是因為 `Button.Command` 屬性會將其系結來源重新定義為上階的系結內容，而其系結內容類型為 `PeopleViewModel`，也就是[`StackLayout`](xref:Xamarin.Forms.StackLayout)。 然後，系結運算式的 `Path` 部分可以解析 `DeletePersonCommand` 屬性。 不過，`Button.CommandParameter` 屬性不會改變其系結來源，而是從[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中的父系繼承它。 因此，`CommandParameter` 屬性會系結至 `CardView`的 `CardTitle` 屬性。

[`Button`](xref:Xamarin.Forms.Button)系結的整體作用是在 `Button` 被按下時，會執行 `PeopleViewModel` 類別中的 `DeletePersonCommand`，並將 `CardName` 屬性的值傳遞至 `DeletePersonCommand`。 這會導致從可系結的配置中移除指定的 `CardView`：

[![IOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/viewmodel-itemdeleted.png "樣板化的 CardView 物件")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "樣板化的 CardView 物件")

如需相對系結的詳細資訊，請參閱[Xamarin. 表單相對](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)系結。

## <a name="related-links"></a>相關連結

- [ControlTemplateDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemo)
- [Xamarin. Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Xamarin. 表單相對系結](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Xamarin. 表單資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin. 表單資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin. 表單樣式](~/xamarin-forms/user-interface/styles/index.md)
