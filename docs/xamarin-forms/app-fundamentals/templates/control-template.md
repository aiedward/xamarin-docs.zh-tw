---
title: Xamarin.Forms 控制項範本
description: Xamarin 控制項範本定義 ContentView 衍生自訂控制項的視覺化結構，以及 ContentPage 衍生的頁面。
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
ms.openlocfilehash: a73123b89cba932f2e2cb907645f6fe858cf6176
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305133"
---
# <a name="xamarinforms-control-templates"></a>Xamarin.Forms 控制項範本

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

Xamarin.Forms 控制項樣本使您能夠定義派生自定義控制項和[`ContentView`](xref:Xamarin.Forms.ContentView)[`ContentPage`](xref:Xamarin.Forms.ContentPage)派生頁面的可視結構。 控制項範本會將自訂控制項或頁面的使用者介面 (UI)，從實作控制項或頁面的邏輯分開。 其他內容也可以在預先定義的位置插入樣板化自訂控制項或樣板化頁面。

例如，您可以建立控制項範本來重新定義由自訂控制項提供的 UI。 然後必要的自訂控制項執行個體即可使用控制項範本。 或者，您也可以建立控制項範本來定義應用程式中多個頁面將會使用的任何通用 UI。 然後，控制項範本即可由多個頁面使用，而每個頁面仍會顯示其唯一的內容。

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

派生`CardView`自類[`ContentView`](xref:Xamarin.Forms.ContentView)的 類表示在類似卡片的布局中顯示數據的自定義控制項。 該類別包含其所顯示資料的屬性，這些屬性由可繫結的屬性支援。 不過，`CardView` 類別不會定義任何 UI。 UI 會藉由控制項範本來定義。 如需建立 `ContentView` 衍生自訂控制項的詳細資訊，請參閱 [Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md)。

使用[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)類型創建控制項樣本。 建立`ControlTemplate`時 ,將[`View`](xref:Xamarin.Forms.View)物件組合 起來為自訂控制件或頁面生成 UI。 `ControlTemplate` 必須只有一個 `View` 作為其根項目。 不過，根項目通常會包含其他 `View` 物件。 這些物件的組合會構成控制項的視覺結構。

雖然[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)可以內聯定義 ,[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)聲明 a 的典型方法是資源字典中的資源。 由於控制項範本是資源，因此會遵守適用於所有資源的範圍規則。 例如，如果您是在應用程式定義 XAML 檔案的根項目中宣告控制項範本，即可在應用程式中的任何地方使用該範本。 如果您在頁面中定義範本，則只有該頁面可以使用控制項範本。 如需資源的詳細資訊，請參閱 [Xamarin.Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)。

下面的 XAML 範例[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)顯示`CardView`了 物件的 a:

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

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)當 聲明為資源時,它必須具有`x:Key`使用 屬性指定的鍵,以便在資源字典中標識它。 在此範例中，`CardViewControlTemplate` 的根項目是 [`Frame`](xref:Xamarin.Forms.Frame) 物件。 `Frame` 物件使用 `RelativeSource` 標記延伸將其 `BindingContext` 設定為將套用範本的執行階段物件執行個體，稱為「樣板化父系」**。 物件`Frame`[`Grid`](xref:Xamarin.Forms.Grid)`Frame`[`Image`](xref:Xamarin.Forms.Image)[`Label`](xref:Xamarin.Forms.Label)使用、、[`BoxView`](xref:Xamarin.Forms.BoxView)和物件的組合`CardView`來定義 物件的可視結構。 由於從根 `Frame` 項目繼承 `BindingContext`，這些物件的繫結運算式會對 `CardView` 屬性進行解析。 如需 `RelativeSource` 標記延伸的詳細資訊，請參閱 [Xamarin.Forms 相對繫結](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)。

## <a name="consume-a-controltemplate"></a>使用 ControlTemplate

通過將[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)屬性設置為控件範本[`ContentView`](xref:Xamarin.Forms.ContentView)物件 ,可以將 應用於派生的自定義控制項。 同樣,通過將[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)派生[`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate)頁的屬性設置[`ContentPage`](xref:Xamarin.Forms.ContentPage)為 控件範本物件,可以應用於派生頁面。 在運行時,應用[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)時 ,在中定義的所有`ControlTemplate`控制項將添加到範本化自定義控制項或範本化頁的可視化樹中。

下面的範例顯示`CardViewControlTemplate`要分配給[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)`CardView`每個 物件的屬性:

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

在此範例中，`CardViewControlTemplate` 中控制項會成為每個 `CardView` 物件視覺化樹狀結構的一部分。 由於控件範本[`Frame`](xref:Xamarin.Forms.Frame)的根物件將其`BindingContext`設置到範本化父物件,因此及其`Frame`子物件針對每個`CardView`物件的屬性解析其綁定表達式。

下列螢幕擷取畫面顯示套用於三個 `CardView` 物件的 `CardViewControlTemplate`：

[![iOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/relativesource-controltemplate.png "樣板化 CardView 物件")](control-template-images/relativesource-controltemplate-large.png#lightbox "樣板化 CardView 物件")

> [!IMPORTANT]
> 通過在範本化自定義控制項或[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)樣本化頁中重寫`OnApplyTemplate`方法來檢測應用於控制項實例的時間點。 如需詳細資訊，請參閱[從範本取得具名項目](#get-a-named-element-from-a-template)。

## <a name="pass-parameters-with-templatebinding"></a>使用 TemplateBinding 傳遞參數

標記`TemplateBinding`延伸將 元素的屬性[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)綁定 到由範本化自定義控制項或範本化頁定義的公共屬性。 當您使用 `TemplateBinding` 時，可讓控制項上的屬性能夠作為範本參數。 因此，在設定樣板化自訂控制項或樣板化頁面上的屬性時，該值會傳遞至具有 `TemplateBinding` 的項目。

> [!IMPORTANT]
> 標記`TemplateBinding`延伸是[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)創建`RelativeSource`使用 標記擴展`BindingContext`將範本 中的根元素設置為其範本化父元素的替代方法。 `TemplateBinding` 標記延伸會消除 `RelativeSource` 繫結，並將 `Binding` 運算式取代為 `TemplateBinding` 運算式。

`TemplateBinding` 標記延伸會定義下列屬性：

- `Path`，屬於 `string` 類型，屬性的路徑。
- `Mode`，屬於 `BindingMode` 類型，在「來源」** 與「目標」** 之間變更散佈的方向。
- `Converter`，屬於 `IValueConverter` 類型，繫結值轉換器。
- `ConverterParameter`，屬於 `object` 類型，繫結值轉換器的參數。
- `StringFormat`，屬於 `string` 類型，繫結的字串格式。

`TemplateBinding` 標記延伸的 `ContentProperty` 是 `Path`。 因此，如果路徑是 `TemplateBinding` 運算式中的第一個項目，則標記延伸的 "Path=" 部分會省略。 如需在繫結運算式中使用這些屬性的詳細資訊，請參閱 [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

> [!WARNING]
> 標記`TemplateBinding`延伸檔僅在中[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)使用 。 不過，當嘗試在 `ControlTemplate` 外使用 `TemplateBinding` 運算式時，不會導致建置錯誤或擲回例外狀況。

下面的 XAML 範例[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)顯示`CardView`了 用於`TemplateBinding`物件,它使用 標記延伸:

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

在此範例中，`TemplateBinding` 標記延伸會對每個 `CardView` 物件的屬性解析繫結運算式。 下列螢幕擷取畫面顯示套用於三個 `CardView` 物件的 `CardViewControlTemplate`：

[![iOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/templatebinding-controltemplate.png "樣板化 CardView 物件")](control-template-images/templatebinding-controltemplate-large.png#lightbox "樣板化 CardView 物件")

> [!IMPORTANT]
> 使用 `TemplateBinding` 標記延伸，這相當於將範本中根項目的 `BindingContext` 設定為其具有 `RelativeSource` 標記延伸的樣板化父系，然後使用 `Binding` 標記延伸模組來解析子物件的繫結。 實際上，`TemplateBinding` 標記延伸會建立其 `Source` 為 `RelativeBindingSource.TemplatedParent` 的 `Binding`。

## <a name="apply-a-controltemplate-with-a-style"></a>使用樣式來套用 ControlTemplate

控制項範本也可以使用樣式來套用。 您可以建立「隱含」** 或「明確」** 樣式 (此樣式使用 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)) 來完成此操作。

下列 XAML 範例顯示使用 `CardViewControlTemplate` 的「隱含」** 樣式：

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

這個選項,*隱含*[`Style`](xref:Xamarin.Forms.Style)將自動`CardView`套用每個物件,[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)並將每個`CardView`物件的屬性設定到`CardViewControlTemplate`。

如需樣式的詳細資訊，請參閱 [Xamarin.Forms 樣式](~/xamarin-forms/user-interface/styles/index.md)。

## <a name="redefine-a-controls-ui"></a>重新定義控制項的 UI

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)[`ContentView`](xref:Xamarin.Forms.ContentView)[`ContentPage`](xref:Xamarin.Forms.ContentPage)`ControlTemplate`當實例化並分配給 派生自定義控制項或 派生頁的屬性時,為自訂控制件或頁面定義的可視結構將替換為 中定義的可視結構。 `ControlTemplate`

例如，`CardViewUI` 自訂控制項會使用下列 XAML 來定義其使用者介面：

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

但是,通過在中[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)定義新的可視結構並將其分配給[`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)`CardViewUI`物件的屬性來替換構成此 UI 的控制項:

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

此範例中,`CardViewUI`物件的視覺結構在提供更緊湊的可視結構(適合壓縮[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)清單)中重新定義:

[![iOS 和 Android 上樣板化 CardViewUI 物件的螢幕擷取畫面](control-template-images/redefine-controltemplate.png "樣板化 CardViewUI 物件")](control-template-images/redefine-controltemplate-large.png#lightbox "樣板化 CardViewUI 物件")

## <a name="substitute-content-into-a-contentpresenter"></a>以 ContentPresenter 來替代內容

[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)可以放置在控制項樣本中,以標記範本化自定義控制項或範本化頁面顯示的內容的位置。 然後，使用控制項範本的自訂控制項或頁面將定義 `ContentPresenter` 要顯示的內容。 下圖說明內含一些控制項的頁面 `ControlTemplate`，包括以藍色方框標示的 `ContentPresenter`：

![ContentPage 的控制項範本](control-template-images/control-template.png "ContentPage 的控制項範本")

以下 XAML 顯示了一`TealTemplate`個名為 在其[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)可視結構 中包含 的控制項樣本:

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

以下範例顯示`TealTemplate`分配[`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate)[`ContentPage`](xref:Xamarin.Forms.ContentPage)給 派生頁面的屬性:

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

在執行時,當應用於`TealTemplate`頁面時,頁面內容將替換為控制項樣本中[`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter)定義的頁面內容:

[![iOS 和 Android 上樣板化頁面物件的螢幕擷取畫面](control-template-images/tealtemplate-contentpage.png "樣板化 ContentPage")](control-template-images/tealtemplate-contentpage-large.png#lightbox "樣板化 ContentPage")

## <a name="get-a-named-element-from-a-template"></a>從範本取得具名元素

控制項範本內的具名項目，可以從樣板化自訂控制項或樣板化頁面擷取。 這可以`GetTemplateChild`通過 方法來實現,該方法返回實[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)例化 可視化樹中的命名元素(如果找到)。 否則會傳回 `null`。

在控制項範本具現化之後，會呼叫範本的 `OnApplyTemplate` 方法。 因此，`GetTemplateChild` 方法應從樣板化控制項或樣板化頁面中的 `OnApplyTemplate` 覆寫呼叫。

> [!IMPORTANT]
> 只有在呼叫 `OnApplyTemplate` 方法之後，才應該呼叫 `GetTemplateChild` 方法。

以下 XAML 顯示了一`TealTemplate`個可[`ContentPage`](xref:Xamarin.Forms.ContentPage)應用於 派生頁面的名為控制項樣本:

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

在此範例中,[`Label`](xref:Xamarin.Forms.Label)元素被命名,可以在範本化頁的代碼中檢索。 此操作可透過從樣板化頁面的 `OnApplyTemplate` 覆寫呼叫 `GetTemplateChild` 方法來完成：

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

在此示例中,在實例化[`Label`](xref:Xamarin.Forms.Label)`changeThemeLabel``ControlTemplate`后將檢索命名的物件。 `changeThemeLabel` 接著可由 `AccessTemplateElementPage` 類別存取並操作。 下列螢幕擷取畫面顯示 `Label` 所顯示的文字已變更：

[![iOS 和 Android 上樣板化頁面物件的螢幕擷取畫面](control-template-images/get-named-element.png "樣板化 ContentPage")](control-template-images/get-named-element-large.png#lightbox "樣板化 ContentPage")

## <a name="bind-to-a-viewmodel"></a>繫結至 viewmodel

可以[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)將數據綁定到視圖模型,即使`ControlTemplate`綁定到範本化父級(應用範本的運行時物件實例)。

下列 XAML 範例顯示頁面，該頁面使用名為 `PeopleViewModel` 的 viewmodel：

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

在此範例中，頁面的 `BindingContext` 設定為 `PeopleViewModel` 執行個體。 此 viewmodel 會公開 `People` 集合與名為 `DeletePersonCommand` 的 `ICommand`。 頁上[`StackLayout`](xref:Xamarin.Forms.StackLayout)使用可綁定的佈局來綁定`People`到集合的數據,`ItemTemplate`並且可綁定佈局的設置`PersonTemplate`為 資源。 這[`DataTemplate`](xref:Xamarin.Forms.DataTemplate)`People`指定集合中的每個項都將使用`CardView`對象顯示。 `CardView`物件的視覺結構使用[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)命名`CardViewControlTemplate`:

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

這個元素是物件[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)[`Frame`](xref:Xamarin.Forms.Frame)。 `Frame` 物件使用 `RelativeSource` 標記延伸來將其 `BindingContext` 設定為樣板化父系。 由於從根 `Frame` 項目繼承 `BindingContext`，`Frame` 物件和其子系的繫結運算式會對 `CardView` 屬性進行解析。 下列螢幕擷取畫面顯示會顯示 `People` 集合的頁面，其中包含三個項目：

[![iOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/viewmodel-controltemplate.png "樣板化 CardView 物件")](control-template-images/viewmodel-controltemplate-large.png#lightbox "樣板化 CardView 物件")

當[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)中的物件綁定到其範本化父級的屬性時[`Button`](xref:Xamarin.Forms.Button), 控件範本中的物件將綁定到其範本化父`DeletePersonCommand`級和 視圖模型中的物件。 這是因為屬性`Button.Command`重新定義其綁定源,將其繫結上下文型`PeopleViewModel`態為的祖先的綁定上下文,[`StackLayout`](xref:Xamarin.Forms.StackLayout)是 。 接著，繫結運算式的 `Path` 部分即可解析 `DeletePersonCommand` 屬性。 但是,該`Button.CommandParameter`屬性不會更改其綁定源,而是從中的父屬性繼承[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)它 。 因此，`CommandParameter` 屬性會繫結至 `CardView` 的 `CardTitle` 屬性。

[`Button`](xref:Xamarin.Forms.Button)繫結總體效果`Button`是,當被挖掘時,將執行`DeletePersonCommand`類別`PeopleViewModel`中的,將`CardName`屬性的值傳遞給`DeletePersonCommand`。 這會導致從可繫結配置移除指定的 `CardView`：

[![iOS 和 Android 上樣板化 CardView 物件的螢幕擷取畫面](control-template-images/viewmodel-itemdeleted.png "樣板化 CardView 物件")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "樣板化 CardView 物件")

如需相對繫結的詳細資訊，請參閱 [Xamarin.Forms 相對繫結](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)。

## <a name="related-links"></a>相關連結

- [ControlTemplateDemos (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Xamarin.Forms 相對繫結](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Xamarin.Forms 資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.Forms 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.Forms 樣式](~/xamarin-forms/user-interface/styles/index.md)
