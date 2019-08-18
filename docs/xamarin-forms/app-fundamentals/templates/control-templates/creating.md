---
title: 建立 ControlTemplate
description: 控制項範本可在應用程式層級或頁面層級定義。 本文示範如何建立和使用控制項範本。
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/14/2019
ms.openlocfilehash: 523113a7b54541733e14f947eefa247e4f774b99
ms.sourcegitcommit: 157da886e1f304c6b482aa3f265ef7d78b696ab7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69024507"
---
# <a name="create-a-controltemplate"></a>建立 ControlTemplate

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simpletheme)

_控制項範本可在應用程式層級或頁面層級定義。本文示範如何建立和使用控制項範本。_

## <a name="create-a-controltemplate-in-xaml"></a>在 XAML 中建立 ControlTemplate

若要在應用程式層級定義 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)，則 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 必須新增至 `App` 類別。 根據預設，所有從範本建立的 Xamarin.Forms 應用程式會使用**應用程式**類別來實作 [`Application`](xref:Xamarin.Forms.Application) 子類別。 若要在應用程式層級宣告 `ControlTemplate`，請在應用程式的 `ResourceDictionary` 中使用 XAML，預設的 **App** 類別必須取代為 XAML **App** 類別和相關聯的程式碼後置，如下列程式碼範例所示：

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

每個 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體會在 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中作為可重複使用的物件建立。  做法是為每個宣告提供唯一的 `x:Key` 屬性，這會在 `ResourceDictionary` 中為其提供描述性索引鍵。

下列程式碼範例顯示相關聯的 `App` 程式碼後置：

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

除了設定 [`MainPage`](xref:Xamarin.Forms.Application.MainPage) 屬性之外，程式碼後置還必須呼叫 `InitializeComponent` 方法來載入及剖析相關聯的 XAML。

下列程式碼範例顯示會將 `TealTemplate` 套用於 [`ContentView`](xref:Xamarin.Forms.ContentView) 的 [`ContentPage`](xref:Xamarin.Forms.ContentPage)：

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

使用 `StaticResource` 標記延伸，將 `TealTemplate` 指派給 [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) 屬性。 [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) 屬性會設定為 [`StackLayout`](xref:Xamarin.Forms.StackLayout)，此屬性會定義要在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上顯示的內容。 此內容將由包含在 `TealTemplate` 中的 [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 顯示。 這會導致下列螢幕擷取畫面中顯示的外觀：

![](creating-images/teal-theme.png "藍綠色控制項範本")

### <a name="re-theming-an-application-at-runtime"></a>在執行階段對應用程式進行重新佈景主題

按一下 [變更佈景主題]  按鈕會執行 `OnButtonClicked` 方法，如下列程式碼範例所示：

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

此方法會將使用中的 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體取代為 `ControlTemplate` 執行個體，如下列螢幕擷取畫面所示：

![](creating-images/aqua-theme.png "青色控制項範本")

> [!NOTE]
> 在 `ContentPage` 上可以指派 `Content` 屬性，且也可以設定 `ControlTemplate` 屬性。 發生此狀況時，如果 `ControlTemplate` 包含 `ContentPresenter` 執行個體，則指派給 `Content` 屬性的內容會由 `ControlTemplate` 內的 `ContentPresenter` 呈現。

### <a name="set-a-controltemplate-with-a-style"></a>使用樣式來設定 ControlTemplate

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 也可透過 [`Style`](xref:Xamarin.Forms.Style) 套用，以進一步擴展佈景主題能力。 這可藉由為 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 中的目標檢視建立「隱含」  或「明確」  樣式，以及設定 [`Style`](xref:Xamarin.Forms.Style) 執行個體中目標檢視的 `ControlTemplate` 屬性來達成。 下列程式碼範例示範已新增至應用程式層級 [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 的「隱含」  樣式：

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

因為 [`Style`](xref:Xamarin.Forms.Style) 執行個體為「隱含」  ，所以會套用至應用程式中的所有 `ContentView` 執行個體。 因此，該執行個體不再需要設定 [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) 屬性，如下列程式碼範例所示：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

如需樣式的詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)。

### <a name="create-a-controltemplate-at-page-level"></a>在頁面層級建立 ControlTemplate

除了將 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體建立於應用程式層級，也可以建立於頁面層級，如下列程式碼範例所示：

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

在頁面層級新增 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 時，[`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) 會新增至 [`ContentPage`](xref:Xamarin.Forms.ContentPage)，然後 `ControlTemplate` 執行個體會包含在 `ResourceDictionary` 中。

## <a name="create-a-controltemplate-in-c35"></a>在 C&#35; 中建立 ControlTemplate

若要在應用程式層級定義 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)，則必須建立表示 `ControlTemplate` 的 `class`。 類別應該衍生自用於範本的[配置](~/xamarin-forms/user-interface/layouts/index.yml)中，如下列程式碼範例所示：

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

`AquaTemplate` 類別與 `TealTemplate` 類別相同，但會將不同色彩用於 [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) 和 [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) 屬性。

下列程式碼範例顯示會將 `TealTemplate` 套用於 [`ContentView`](xref:Xamarin.Forms.ContentView) 的 [`ContentPage`](xref:Xamarin.Forms.ContentPage)：

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

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 執行個體會透過在 `ControlTemplate` 建構函式中指定會定義控制項範本的類別類型來建立。

[`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) 屬性會設定為 [`StackLayout`](xref:Xamarin.Forms.StackLayout)，此屬性會定義要在 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上顯示的內容。 此內容將由包含在 `TealTemplate` 中的 [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) 顯示。 前述的相同機制會用於在執行階段將佈景主題變更為 `AquaTheme`。

## <a name="get-a-named-element-from-a-template"></a>從範本取得具名元素

在範本具現化之後，即可擷取控制項範本內的具名元素。 這可透過 `GetTemplateChild` 方法來完成，此方法會傳回已具現化之 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 視覺化樹狀結構中的具名元素。

在控制項範本具現化之後，會呼叫範本的 `OnApplyTemplate` 方法。 因此應該會從 [`TemplatedPage`](xref:Xamarin.Forms.TemplatedPage) 衍生頁面 (例如 [`ContentPage`](xref:Xamarin.Forms.ContentPage)) 或 [`TemplatedView`](xref:Xamarin.Forms.TemplatedView) 衍生檢視 (例如 [`ContentView`](xref:Xamarin.Forms.ContentView)) 中的 `OnApplyTemplate` 覆寫呼叫 `GetTemplateChild` 方法。

> [!IMPORTANT]
> 只有在呼叫 `OnApplyTemplate` 方法之後，才應該呼叫 `GetTemplateChild` 方法。

下列範例顯示自訂控制項的控制項範本：

```xaml
<controls:MyCustomControl ...>
    <controls:MyCustomControl.ControlTemplate>
         <ControlTemplate>
              <Label x:Name="myLabel" />
         </ControlTemplate>
    <controls:MyCustomControl.ControlTemplate>
</controls:MyCustomControl>
```

[`Label`](xref:Xamarin.Forms.Label) 元素已具名，因此可以針對自訂控制項在程式碼後置中擷取。 這是透過從自訂控制項的 `OnApplyTemplate` 覆寫呼叫 `GetTemplateChild` 方法來達成的：

```csharp
class MyCustomControl : ContentView
{
    Label myLabel;

    protected override void OnApplyTemplate()
    {  
        myLabel = GetTemplateChild("myLabel");
    }
    //...
}
```

在此範例中，會擷取名為 `myLabel` 的 [`Label`](xref:Xamarin.Forms.Label) 物件。 `myLabel` 接著可由 `MyCustomControl` 類別存取並操作。

## <a name="related-links"></a>相關連結

- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [Simple Theme (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplates-simpletheme) (簡單的佈景主題 (範例))
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
