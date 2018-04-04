---
title: ControlTemplate 從繫結
description: 範本繫結允許資料控制項樣板中的控制項繫結至公用屬性，啟用 [控制項] 範本，輕鬆地變更中的控制項上的屬性值。 本文示範如何使用執行資料繫結的控制項範本的範本繫結。
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 3b306c79aea9bd2192aa73eddcf95790a9b24353
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="binding-from-a-controltemplate"></a>ControlTemplate 從繫結

_範本繫結允許資料控制項樣板中的控制項繫結至公用屬性，啟用 [控制項] 範本，輕鬆地變更中的控制項上的屬性值。本文示範如何使用執行資料繫結的控制項範本的範本繫結。_

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)使用控制項範本中的控制項屬性繫結至可繫結上的父屬性*目標*擁有控制項樣板檢視。 例如，而不是定義所顯示的文字[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)內執行個體[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)，您可以使用範本繫結至繫結[ `Label.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性可繫結的屬性，定義要顯示的文字。

A [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)類似現有[ `Binding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Binding/)，不同之處在於*來源*的`TemplateBinding`一律會自動設定為父代*目標*擁有控制項樣板檢視。 不過，請注意，使用`TemplateBinding`之外[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)不支援。

## <a name="creating-a-templatebinding-in-xaml"></a>在 XAML 中建立為 TemplateBinding

在 XAML 中、 [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)使用建立[ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.TemplateBindingExtension/)標記延伸，如下列程式碼範例所示：

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

而非設定[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)靜態文字的屬性，屬性可以使用範本繫結繫結至可繫結內容的父代*目標*擁有檢視[`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). 不過，請注意，範本繫結繫結至`Parent.HeaderText`和`Parent.FooterText`，而非`HeaderText`和`FooterText`。 這是因為在此範例中，可繫結的屬性會定義在祖系*目標*檢視，而不是父代，如下列程式碼範例所示：

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

*來源*範本的繫結會一律自動設定為父代*目標*擁有 [控制項] 範本，以下是檢視[ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)執行個體。 繫結所使用的範本[ `Parent` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.Parent/)屬性傳回的父項目`ContentView`執行個體，也就是[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)執行個體。 因此，使用[ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)中[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)繫結至`Parent.HeaderText`和`Parent.FooterText`找出可繫結內容上定義之`ContentPage`，做為下列程式碼範例所示：

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

這會導致下列螢幕擷取畫面所示的外觀：

![](template-binding-images/teal-theme.png "使用範本繫結的控制項範本藍綠色")

## <a name="creating-a-templatebinding-in-c35"></a>在 C 中建立為 TemplateBinding&#35;

在 C# 中， [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)建立使用`TemplateBinding`建構函式，如下列程式碼範例所示：

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

而非設定[ `Label.Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)靜態文字的屬性，屬性可以使用範本繫結繫結至可繫結內容的父代*目標*擁有檢視[`ControlTemplate`](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/). 範本繫結由使用[ `SetBinding` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetBinding/p/Xamarin.Forms.BindableProperty/Xamarin.Forms.BindingBase/)方法，指定[ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)做為第二個參數的執行個體。 請注意，範本繫結的繫結至`Parent.HeaderText`和`Parent.FooterText`，因為的父父代上定義的繫結屬性*目標*檢視，而不是父代，如下列程式碼範例所示：

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

可繫結屬性的定義`ContentPage`，如之前所述。

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>繫結 BindableProperty ViewModel 屬性

如先前所述， [ `TemplateBinding` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)控制項樣板中的控制項屬性繫結至可繫結屬性的父代上*目標*擁有控制項樣板檢視。 接著，可以繫結中 ViewModels 屬性這些可繫結的屬性。

下列程式碼範例會定義兩個屬性上 ViewModel:

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

`HeaderText`和`FooterText`ViewModel 屬性可繫結到，如下列 XAML 程式碼範例所示：

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

`HeaderText`和`FooterText`可繫結屬性繫結至`HomePageViewModel.HeaderText`和`HomePageViewModel.FooterText`屬性，因為設定[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的執行個體`HomePageViewModel`類別。 整體來說，這會導致中的控制項屬性[ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)繫結至[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)執行個體[ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)，這又繫結至ViewModel 屬性。

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

如需資料繫結至 ViewModels 的詳細資訊，請參閱[從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>總結

本文示範使用範本繫結來執行資料繫結的控制項範本。 範本繫結允許資料控制項樣板中的控制項繫結至公用屬性，啟用 [控制項] 範本，輕鬆地變更中的控制項上的屬性值。



## <a name="related-links"></a>相關連結

- [資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [簡單的佈景主題的範本繫結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [簡單的佈景主題與範本繫結和 ViewModel （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](https://developer.xamarin.com/api/type/Xamarin.Forms.TemplateBinding/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
