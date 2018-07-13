---
title: 從 Xamarin.Forms ControlTemplate 的繫結
description: 範本繫結可讓控制項的控制項範本，以資料繫結至公用屬性，啟用 [控制項] 範本，輕鬆地變更中的控制項上的屬性值。 這篇文章示範如何使用範本繫結控制項範本，從執行資料繫結。
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 86de2ad6009365b3debbe1a2310651002b023219
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995560"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>從 Xamarin.Forms ControlTemplate 的繫結

_範本繫結可讓控制項的控制項範本，以資料繫結至公用屬性，啟用 [控制項] 範本，輕鬆地變更中的控制項上的屬性值。這篇文章示範如何使用範本繫結控制項範本，從執行資料繫結。_

A [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)用來在控制項範本中控制項的屬性繫結至可繫結屬性的父代上*目標*擁有控制項範本的檢視。 比方說，而不是定義所顯示的文字[ `Label` ](xref:Xamarin.Forms.Label)內的執行個體[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)，您可以使用範本繫結來繫結[ `Label.Text`](xref:Xamarin.Forms.Label.Text)屬性可繫結的屬性，定義要顯示的文字。

A [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)類似於現有[ `Binding` ](xref:Xamarin.Forms.Binding)，不同之處在於*來源*的`TemplateBinding`一定會自動設定為父代*目標*擁有控制項範本的檢視。 不過，請注意，使用`TemplateBinding`之外[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)不支援。

## <a name="creating-a-templatebinding-in-xaml"></a>在 XAML 中建立 TemplateBinding

在 XAML 中， [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)會使用建立[ `TemplateBinding` ](xref:Xamarin.Forms.Xaml.TemplateBindingExtension)標記延伸，如下列程式碼範例所示：

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

而不是設定[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)靜態文字的屬性，屬性可以使用範本繫結來繫結至可繫結屬性的父代上*目標*擁有檢視[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). 不過，請注意，範本繫結繫結至`Parent.HeaderText`並`Parent.FooterText`，而非`HeaderText`和`FooterText`。 這是因為在此範例中，可繫結的屬性會定義上的父父代*目標*檢視，而不是父代，如下列程式碼範例所示：

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

*來源*的範本繫結一定會自動設定為父代*目標*擁有控制項範本，以下是檢視[ `ContentView` ](xref:Xamarin.Forms.ContentView)執行個體。 繫結所使用的範本[ `Parent` ](xref:Xamarin.Forms.Element.Parent)屬性傳回的父項目`ContentView`執行個體，也就是[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)執行個體。 因此，使用[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)中[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)繫結至`Parent.HeaderText`並`Parent.FooterText`找出可繫結的屬性上定義之`ContentPage`，做為下列程式碼範例所示：

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

這會導致下列的螢幕擷取畫面所示的外觀：

![](template-binding-images/teal-theme.png "使用範本繫結的藍綠色控制項範本")

## <a name="creating-a-templatebinding-in-c35"></a>在 C 中建立 TemplateBinding&#35;

在 C# 中， [ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)建立使用`TemplateBinding`建構函式，如下列程式碼範例所示：

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

而不是設定[ `Label.Text` ](xref:Xamarin.Forms.Label.Text)靜態文字的屬性，屬性可以使用範本繫結來繫結至可繫結屬性的父代上*目標*擁有檢視[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). 範本繫結由使用[ `SetBinding` ](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase))方法，指定[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)做為第二個參數的執行個體。 請注意，範本繫結的繫結至`Parent.HeaderText`並`Parent.FooterText`，因為可繫結屬性的定義的父父代*目標*檢視，而不是父代，如下列程式碼範例所示：

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

可繫結屬性的定義`ContentPage`、 稍早所述。

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>繫結 BindableProperty ViewModel 屬性

如先前所述[ `TemplateBinding` ](xref:Xamarin.Forms.TemplateBinding)控制項範本中的控制項屬性繫結至可繫結屬性的父代上*目標*擁有控制項範本的檢視。 接著，這些可繫結的屬性可以繫結 Viewmodel 的屬性。

下列程式碼範例會在 ViewModel 上定義兩個屬性：

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

`HeaderText`並`FooterText`可繫結屬性繫結至`HomePageViewModel.HeaderText`並`HomePageViewModel.FooterText`屬性，因為設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的執行個體`HomePageViewModel`類別。 整體來說，這會導致控制項中的屬性[ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate)繫結至[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)執行個體上[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)，其接著繫結至ViewModel 的屬性。

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

如需有關資料繫結至 Viewmodel 的詳細資訊，請參閱[從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>總結

示範使用範本繫結來執行資料繫結控制項範本，從這篇文章。 範本繫結可讓控制項的控制項範本，以資料繫結至公用屬性，啟用 [控制項] 範本，輕鬆地變更中的控制項上的屬性值。



## <a name="related-links"></a>相關連結

- [資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [簡單的佈景主題與範本繫結 （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)
- [簡單的佈景主題與範本繫結和 ViewModel （範例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
