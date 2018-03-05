---
title: "建立 ControlTemplate"
description: "控制項範本可定義應用程式層級或頁面層級。 本文將示範如何建立和使用控制項範本。"
ms.topic: article
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e09ac502298296277d9264bcd18f1ce1cbbf0c55
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="creating-a-controltemplate"></a>建立 ControlTemplate

_控制項範本可定義應用程式層級或頁面層級。本文將示範如何建立和使用控制項範本。_

## <a name="creating-a-controltemplate-in-xaml"></a>在 XAML 中建立 ControlTemplate

若要定義[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)應用程式層級， [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)必須新增至`App`類別。 根據預設，從範本建立的所有 Xamarin.Forms 應用程式都使用**應用程式**類別來實作[ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)子類別。 若要宣告`ControlTemplate`應用程式層級，在應用程式的`ResourceDictionary`使用 XAML，預設值**應用程式**類別都必須取代 XAML**應用程式**類別和相關聯的程式碼後置，做為下列程式碼範例所示：

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

每個[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)當做可重複使用物件建立執行個體[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。  這藉由讓每個宣告的唯一`x:Key`屬性，提供描述性的索引鍵中`ResourceDictionary`。

下列程式碼範例會顯示相關聯`App`程式碼後置：

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

設定以及[ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/)屬性，程式碼後置必須也會呼叫`InitializeComponent`方法來載入及剖析相關聯的 XAML。

下列程式碼範例示範[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)套用`TealTemplate`至[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

`TealTemplate`指派給[ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/)屬性使用`StaticResource`標記延伸。 [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)屬性設定為[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) ，定義要顯示在內容[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。 此內容會由顯示[ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)中包含`TealTemplate`。 這會導致下列螢幕擷取畫面所示的外觀：

![](creating-images/teal-theme.png "藍綠色控制項範本")

### <a name="re-theming-an-application-at-runtime"></a>重新設定主題的應用程式在執行階段

按一下**變更主題**按鈕會執行`OnButtonClicked`方法，下列程式碼範例所示：

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

這個方法會取代現用[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)使用替代項目執行個體`ControlTemplate`執行個體，導致下列螢幕擷取畫面：

![](creating-images/aqua-theme.png "青色控制項範本")

> [!NOTE]
> **請注意**： 上`ContentPage`、`Content`屬性可以指派和`ControlTemplate`也可以設定屬性。 發生此狀況，如果`ControlTemplate`包含`ContentPresenter`執行個體指派給內容`Content`屬性會由`ContentPresenter`內`ControlTemplate`。

### <a name="setting-a-controltemplate-with-a-style"></a>設定 ControlTemplate 的樣式

A [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)也可以透過套用[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)進一步展開佈景主題的能力。 這可藉由建立*隱含*或*明確*樣式中的目標檢視[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)，以及設定`ControlTemplate`目標屬性在中檢視[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)執行個體。 下列程式碼範例示範*隱含*已加入到應用程式層級的樣式[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

因為[ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/)執行個體是*隱含*，將套用至所有`ContentView`應用程式中的執行個體。 因此，它不再需要設定[ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/)屬性，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

如需有關樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

### <a name="creating-a-controltemplate-at-page-level"></a>在頁面層級建立 ControlTemplate

除了建立[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)應用程式層級的執行個體，它們也可以建立在頁面層級，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

加入時[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)頁面層次[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)加入至[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，然後`ControlTemplate`隨附的執行個體在`ResourceDictionary`。

## <a name="creating-a-controltemplate-in-c35"></a>建立 ControlTemplate 中 & #35。

若要定義[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)應用程式層級，`class`必須建立代表`ControlTemplate`。 類別應衍生自[配置](~/xamarin-forms/user-interface/layouts/index.md)使用範本中，如下列程式碼範例所示：

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

`AquaTemplate`類別等同於`TealTemplate`類別，不同處在於會使用不同的色彩[ `BoxView.Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/)和[ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)屬性。

下列程式碼範例示範[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)套用`TealTemplate`至[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)會建立指定類型的定義中的控制項範本的類別執行個體`ControlTemplate`建構函式。

[ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/)屬性設定為[ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) ，定義要顯示在內容[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)。 此內容會由顯示[ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)中包含`TealTemplate`。 先前所述的相同機制使用將在執行階段主題變更`AquaTheme`。

## <a name="summary"></a>總結

本文示範如何建立和使用控制項範本。 控制項範本可定義應用程式層級或頁面層級。


## <a name="related-links"></a>相關連結

- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [簡單的佈景主題 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
