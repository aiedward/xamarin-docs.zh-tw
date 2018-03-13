---
title: "樣式"
description: "樣式 Xamarin.Forms 中的文字"
ms.topic: article
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: 6da9b4d36c8caca23328318b4f2bff784038bacd
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="styles"></a>樣式

_樣式 Xamarin.Forms 中的文字_


樣式可以用來調整標籤、 項目，以及編輯器的外觀。 樣式可以定義一次，並使用許多檢視，但樣式只用於具有一種類型的檢視。
可以指定樣式`Key`，並且選擇性地使用特定控制項`Style`屬性。

本文涵蓋下列主題：

- **[內建樣式](#Built-In_Styles)** &ndash;使用內建樣式整個應用程式以文字為基礎檢視的樣式。
- **[自訂樣式](#Custom_Styles)** &ndash;內建的選項還不夠時，定義自訂樣式。
- **[套用樣式](#Applying_Styles)** &ndash;將自訂和內建樣式套用至您的檢視。
- **[協助工具](#Accessibility)** &ndash;確保文字尊重協助工具設定。

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>內建樣式

Xamarin.Forms 可包含數個[內建](http://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)樣式的常見案例：

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

若要套用內建樣式的其中一個，請使用`DynamicResource`標記延伸到指定的樣式：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 C# 中，從選取內建樣式`Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

![](styles-images/builtinstyles.png "裝置樣式範例")

<a name="Custom_Styles" />

## <a name="custom-styles"></a>自訂樣式

樣式包含 setter 和 setter 組成屬性和值的屬性會設定為。

在 C# 中，大小 30 的紅色文字標籤的自訂樣式會定義如下：

```csharp
var LabelStyle = new Style (typeof(Label)) {
    Setters = {
        new Setter {Property = Label.TextColorProperty, Value = Color.Red},
        new Setter {Property = Label.FontSizeProperty, Value = 30}
    }
};

var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

在 XAML 中：

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <Style x:Key="LabelStyle" TargetType="Label">
            <Setter Property="TextColor" Value="Red"/>
            <Setter Property="FontSize" Value="30"/>
        </Style>
    </ResourceDictionary>
</ContentPage.Resources>

<ContentPage.Content>
    <StackLayout>
        <Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
    </StackLayout>
</ContentPage.Content>
```

請注意，資源 （包括所有樣式） 會定義內`ContentPage.Resources`，這是更熟悉的同層級`ContentPage.Content`項目。

![](styles-images/customstyle.png "自訂樣式範例")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>套用樣式

一旦建立樣式，它可以套用至任何檢視表比對其`TargetType`。

在 XAML 中，自訂樣式套用至檢視藉由提供其`Style`屬性`StaticResource`參考所需的樣式的標記延伸：

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

在 C# 樣式可以是直接套用到檢視或新增至並從頁面擷取`ResourceDictionary`。 若要直接新增：

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

加入和從分頁中擷取`ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

因為它們必須以回應協助工具設定，會以不同的方式，套用內建樣式。 若要套用在 XAML 中，內建樣式`DynamicResource`使用標記延伸模組：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 C# 中，從選取內建樣式`Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>協助工具選項

內建樣式存在是為了更輕鬆地採用協助工具喜好設定。 使用時的任何內建樣式，如果使用者據以設定協助工具喜好設定，將會自動會增加字型的大小。

請考慮下列檢視與內建樣式樣式與協助工具設定啟用和停用的相同頁面的範例：

已停用：

![](styles-images/pre-access.png "停用協助工具功能的裝置樣式")

啟用：

![](styles-images/post-access.png "裝置啟用的存取範圍的樣式")

若要確保協助工具，請確定內建樣式當做應用程式內的任何文字相關樣式為基礎，而且您會一致地使用樣式。 請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)如需有關擴充及一般情況下使用樣式。


## <a name="related-links"></a>相關連結

- [透過 Xamarin.Forms，12 章建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [樣式](http://developer.xamarin.comhttps://developer.xamarin.com/api/type/Xamarin.Forms.Style/)
