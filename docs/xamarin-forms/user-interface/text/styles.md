---
title: Xamarin.Forms 文字樣式
description: 本文說明如何在應用程式中設定文字的樣式 Xamarin.Forms 。 樣式可以定義為一次，並由許多視圖使用，但是樣式只能與一種類型的視圖一起使用。
ms.prod: xamarin
ms.assetid: 57C0CFD6-A568-46B8-ADA1-BF25681893CF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2c6898439da5869d79bc500643ca3cfc52c2e3f
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557552"
---
# <a name="no-locxamarinforms-text-styles"></a>Xamarin.Forms 文字樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_設定文字的樣式 Xamarin.Forms_

樣式可以用來調整標籤、專案和編輯器的外觀。 樣式可以定義為一次，並由許多視圖使用，但是樣式只能與一種類型的視圖一起使用。
您可以指定樣式 `Key` ，並選擇性地使用特定控制項的屬性來套用樣式 `Style` 。

## <a name="built-in-styles"></a>內建樣式

Xamarin.Forms 針對常見案例包含數個 [內建](xref:Xamarin.Forms.Device.Styles) 的樣式：

- `BodyStyle`
- `CaptionStyle`
- `ListItemDetailTextStyle`
- `ListItemTextStyle`
- `SubtitleStyle`
- `TitleStyle`

若要套用其中一個內建樣式，請使用 `DynamicResource` 標記延伸來指定樣式：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 c # 中，您可以從下列選項選取內建樣式 `Device.Styles` ：

```csharp
label.Style = Device.Styles.TitleStyle;
```

![裝置樣式範例](styles-images/builtinstyles.png)

## <a name="custom-styles"></a>自訂樣式

樣式包含 setter 和 setter 包含屬性，以及屬性將設定為的值。

在 c # 中，大小為30的紅色文字之標籤的自訂樣式定義如下：

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

請注意，資源 (包括定義) 的所有樣式 `ContentPage.Resources` ，也就是比較熟悉的元素的同級 `ContentPage.Content` 。

![自訂樣式範例](styles-images/customstyle.png)

## <a name="applying-styles"></a>套用樣式

一旦建立樣式之後，就可以將它套用至任何符合其的視圖 `TargetType` 。

在 XAML 中，自訂樣式會藉由提供 `Style` 具有 `StaticResource` 參考所需樣式之標記延伸的屬性來套用至 views：

```xaml
<Label Text="Check out my style." Style="{StaticResource LabelStyle}" />
```

在 c # 中，您可以直接將樣式套用至視圖，也可以在頁面上加入和取出樣式 `ResourceDictionary` 。 若要直接新增：

```csharp
var label = new Label { Text = "Check out my style.", Style = LabelStyle };
```

若要從頁面加入和取出 `ResourceDictionary` ：

```csharp
this.Resources.Add ("LabelStyle", LabelStyle);
label.Style = (Style)Resources["LabelStyle"];
```

內建樣式的套用方式不同，因為它們必須回應協助工具設定。 若要在 XAML 中套用內建樣式，請 `DynamicResource` 使用標記延伸：

```xaml
<Label Text="I'm a Title" Style="{DynamicResource TitleStyle}"/>
```

在 c # 中，您可以從下列選項選取內建樣式 `Device.Styles` ：

```csharp
label.Style = Device.Styles.TitleStyle;
```

## <a name="accessibility"></a>協助工具選項

內建的樣式可讓您更輕鬆地遵守協助工具喜好設定。 使用任何內建的樣式時，如果使用者據以設定其協助工具喜好設定，則會自動增加字型大小。

請考慮下列頁面範例，其中包含已啟用和停用協助工具設定之內建樣式的相同網頁檢視：

已停用：

![已停用協助工具的裝置樣式](styles-images/pre-access.png)

已啟用：

![啟用協助工具的裝置樣式](styles-images/post-access.png)

若要確保存取範圍，請確定內建樣式會用來作為應用程式內任何文字相關樣式的基礎，而且您會以一致的方式使用樣式。 如需擴充和使用一般樣式的詳細資訊，請參閱 [樣式](~/xamarin-forms/user-interface/styles/index.md) 。

## <a name="related-links"></a>相關連結

- [使用第 Xamarin.Forms 12 章建立 Mobile Apps](https://developer.xamarin.com/r/xamarin-forms/book/chapter12.pdf)
- [樣式](~/xamarin-forms/user-interface/styles/index.md)
- [Text (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [Style](xref:Xamarin.Forms.Style)