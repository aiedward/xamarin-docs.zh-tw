---
title: 從 Xamarin.Forms ControlTemplate 繫結
description: 範本繫結可讓控制項範本中的控制項將資料繫結至公用屬性，輕鬆變更控制項範本中的控制項屬性值。 本文示範如何使用範本繫結，從控制項範本執行資料繫結。
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 49f66164c707f91f298b2e5cb09b35f1767186cf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051577"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>從 Xamarin.Forms ControlTemplate 繫結

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/)

_範本繫結可讓控制項範本中的控制項將資料繫結至公用屬性，輕鬆變更控制項範本中的控制項屬性值。本文示範如何使用範本繫結，從控制項範本執行資料繫結。_

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 用於將控制項範本中的控制項屬性繫結至擁有控制項範本之「目標」檢視父代上的可繫結屬性。 例如，您可以使用範本繫結將 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 屬性繫結至可繫結屬性，定義要顯示的文字，而不要定義 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 內 [`Label`](xref:Xamarin.Forms.Label) 執行個體所顯示的文字。

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 類似於現有的 [`Binding`](xref:Xamarin.Forms.Binding)，不同之處在於 `TemplateBinding` 的「來源」一律自動設為擁有控制項範本之「目標」檢視的父代。 但請注意，不支援在 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 外使用 `TemplateBinding`。

## <a name="creating-a-templatebinding-in-xaml"></a>在 XAML 中建立 TemplateBinding

在 XAML 中，[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 是使用 [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) 標記延伸所建立，如下列程式碼範例所示：

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

屬性可以使用範本繫結來繫結擁有 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 之「目標」檢視父代上的可繫結屬性，而不是將 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 屬性設成靜態文字。 但請注意，範本繫結會繫結至 `Parent.HeaderText` 和 `Parent.FooterText`，而非 `HeaderText` 和 `FooterText`。 這是因為，在本範例中，可繫結屬性是在「目標」檢視的父父代中定義，而不是在父代中定義，如下列程式碼範例所示：

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

範本繫結的「來源」一律自動設為擁有控制項範本之「目標」檢視的父代，在此為 [`ContentView`](xref:Xamarin.Forms.ContentView) 執行個體。 範本繫結使用 [`Parent`](xref:Xamarin.Forms.Element.Parent) 屬性傳回 `ContentView` 執行個體的父項目，即 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 執行個體。 因此，在 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 中使用 [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 繫結 `Parent.HeaderText` 和 `Parent.FooterText`，可找出在 `ContentPage` 上定義的可繫結屬性，如下列程式碼範例所示：

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

這會導致下列螢幕擷取畫面中顯示的外觀：

![](template-binding-images/teal-theme.png "使用範本繫結的藍綠色控制項範本")

## <a name="creating-a-templatebinding-in-c35"></a>在 C&#35; 中建立 TemplateBinding

在 C# 中，[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 是使用 `TemplateBinding` 建構函式所建立，如下列程式碼範例所示：

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

屬性可以使用範本繫結來繫結擁有 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 之「目標」檢視父代上的可繫結屬性，而不是將 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 屬性設成靜態文字。 範本繫結是使用 [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) 方法所建立，指定 [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 執行個體為第二個參數。 請注意，範本繫結是繫結至 `Parent.HeaderText` 和 `Parent.FooterText`，因為可繫結屬性是在「目標」檢視的父父代中定義，而不是在父代中定義，如下列程式碼範例所示：

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

如前文所述，可繫結屬性是在 `ContentPage` 中定義。

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>將 BindableProperty 繫結至 ViewModel 屬性

如前所述，[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 將控制項範本中的控制項屬性繫結至擁有控制項範本之「目標」檢視父代上的可繫結屬性。 接著，這些可繫結屬性就可以繫結到 ViewModel 中的屬性。

下列程式碼範例會在 ViewModel 上定義兩個屬性：

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

`HeaderText` 和 `FooterText` ViewModel 屬性可以是繫結對象，如下列 XAML 程式碼範例所示：

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

`HeaderText` 和 `FooterText` 可繫結屬性會繫結至 `HomePageViewModel.HeaderText` 和 `HomePageViewModel.FooterText` 屬性，因為將 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設定為 `HomePageViewModel` 類別的執行個體。 整體而言，這會導致 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 中的控制項屬性繫結至 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 執行個體，接著繫結至 ViewModel 屬性。

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

您也可以將控制項範本繫結至 Parent.BindingContext.<屬性名稱>，直接繫結檢視模型屬性，因此不用再針對 `ContentPage` 上的 `HeaderText` 和 `FooterText` 宣告 `BindableProperty`，例如：

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

如需將資料繫結至 ViewModel 的詳細資訊，請參閱[從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>總結

本文已示範如何使用範本繫結，從控制項範本執行資料繫結。 範本繫結可讓控制項範本中的控制項將資料繫結至公用屬性，輕鬆變更控制項範本中的控制項屬性值。

## <a name="related-links"></a>相關連結

- [資料繫結的基本概念](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [從資料繫結至 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [Simple Theme with Template Binding (Samples)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebinding/) (範本繫結的簡易佈景主題 (範例))
- [Simple Theme with Template Binding (Samples)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/) (範本繫結的簡易佈景主題 (範例))
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)
