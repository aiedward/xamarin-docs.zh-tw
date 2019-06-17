---
title: Xamarin.Forms 文字樣式
description: 這篇文章說明如何在 Xamarin.Forms 應用程式中的樣式文字。 樣式可以定義一次，並使用許多檢視，但樣式僅適用於具有一種類型的檢視。
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: f70ab3faf6984720e395f5a41b4cabb63fe03053
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61188747"
---
# <a name="xamarinforms-text-styles"></a>Xamarin.Forms 文字樣式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_在 Xamarin.Forms 中的樣式文字_

樣式可用來調整標籤、 項目，以及編輯器的外觀。 樣式可以定義一次，並使用許多檢視，但樣式僅適用於具有一種類型的檢視。
您可以指定樣式`Key`選擇性地使用特定的控制項與套用`Style`屬性。

<a name="Built-In_Styles" />

## <a name="built-in-styles"></a>內建樣式

Xamarin.Forms 包含數個[內建](xref:Xamarin.Forms.Device.Styles)樣式的常見案例：

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

若要套用其中一個內建樣式，請使用`DynamicResource`指定樣式的標記延伸模組：

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

樣式組成 setter 屬性組成的 setter 和屬性的值會設定為。

在 C# 中，有大小 30 的紅色文字的標籤的自訂樣式就，如下所示定義：

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

請注意，資源 （包括所有樣式） 會定義內`ContentPage.Resources`，這是較熟悉的同層級`ContentPage.Content`項目。

![](styles-images/customstyle.png "自訂樣式範例")

<a name="Applying_Styles" />

## <a name="applying-styles"></a>套用樣式

一旦建立樣式，它可以套用至任何檢視表比對其`TargetType`。

在 XAML 中，自訂樣式套用至檢視藉由提供他們`Style`屬性與`StaticResource`標記延伸參考所要的樣式：

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

在 C# 中，樣式可以是直接套用到檢視或新增至並從頁面擷取`ResourceDictionary`。 若要直接新增：

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

若要從網頁的新增和擷取`ResourceDictionary`:

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

內建樣式是以不同的方式套用，因為它們需要回應的協助工具設定。 若要將 XAML 中的內建樣式套用`DynamicResource`使用標記延伸模組：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 C# 中，從選取內建樣式`Device.Styles`:

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>協助工具選項

內建樣式可用來輕鬆地採用協助工具偏好設定。 使用時的任何內建樣式，如果使用者據以設定他們的協助工具喜好設定，就會自動會增加字型大小。

請考慮下列檢視樣式的內建樣式與協助工具設定啟用和停用的相同頁面的範例：

已停用：

![](styles-images/pre-access.png "使用協助工具已停用裝置樣式")

已啟用：

![](styles-images/post-access.png "裝置啟用的存取範圍的樣式")

若要確保協助工具，請確定內建樣式做為應用程式內的任何文字相關樣式為基礎，而且您正在使用的樣式以一致的方式。 請參閱[樣式](~/xamarin-forms/user-interface/styles/index.md)如需詳細資訊，擴充和一般使用的樣式。


## <a name="related-links"></a>相關連結

- [使用 Xamarin.Forms 時，第 12 章建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [樣式](xref:Xamarin.Forms.Style)
