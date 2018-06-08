---
title: ThisAddIn
description: Xamarin.Forms 中的顯示文字
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: e0caa12136feb84d22ec4b90b84f3f92a601e0c0
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34847507"
---
# <a name="label"></a>ThisAddIn

_Xamarin.Forms 中的顯示文字_

`Label`檢視用於顯示文字，單一和多行。 標籤可以具有 （家族、 大小和選項） 的自訂字型和色彩的文字。 本文涵蓋下列主題：

- **[截斷和換行](#Truncation_and_Wrapping)** &ndash;截斷和換行選項來處理文字不能放在一行的情況。
- **[字型](#Font)** &ndash;的字型選項。
- **[色彩](#Color)** &ndash;標籤文字的色彩選項。
- **[格式化的文字](#Formatted_Text)** &ndash;顯示包含多個格式/樣式內嵌文字的選項。

## <a name="styling-label"></a>樣式標籤

下列各節涵蓋的設定屬性`Label`手動針對每個執行個體。 請注意，屬性的集合可以分組為一個會一致地套用到一個或多個檢視的樣式。 這樣可以提高程式碼的可讀性，而且讓您更輕鬆地實作設計變更。 請參閱[樣式](~/xamarin-forms/user-interface/text/styles.md)如需詳細資訊。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和換行

標籤可以設定來處理無法放在一行中的一種，所公開的文字`LineBreakMode`屬性。 [`LineBreakMode`](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/) 是一種列舉的下列選項：

- **HeadTruncation** &ndash;截斷的文字，並顯示結尾的開頭。
- **CharacterWrap** &ndash;字元界限處換行至下一行的文字。
- **MiddleTruncation** &ndash;顯示開頭和結尾的文字，以省略符號中間取代。
- **NoWrap** &ndash;不換行文字，只顯示最多可以為文字放在一行。
- **TailTruncation** &ndash;顯示文字，截斷結尾的開頭。
- **自動換行**&ndash;在字邊界換行文字。

## <a name="font"></a>字型

請參閱[使用字型](~/xamarin-forms/user-interface/text/fonts.md)如需詳細資訊。

## <a name="color"></a>色彩

`Label`s 可以設定為使用自訂文字色彩，透過可繫結`TextColor`屬性。

若要確保色彩將會使用每個平台上必須特別注意。 因為每個平台都有不同的預設值的文字和背景色彩，您將需要小心地挑選每個運作的預設值。

您可以使用下列程式碼來設定標籤的文字色彩：

程式碼：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/textcolor.png "標籤 TextColor 範例")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化的文字

標籤公開`FormattedText`屬性可讓您顯示文字的多個字型和色彩相同檢視中。

`FormattedText`屬性屬於型別[ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/)。 格式化的字串所組成一或多個`Span`s，每一個都具有下列屬性：

- **BackgroundColor** &ndash;可以用來設定背景色彩，例如若要達到螢光筆的效果。
- **FontAttributes** &ndash;可以設為粗體、 斜體或兩者皆非。
- **FontFamily** &ndash;設定要使用的字型。
- **FontSize** &ndash;設定文字大小。
- **ForegroundColor** &ndash;設定文字的色彩。
- **文字**&ndash;来呈現的文字。

下列 C# 程式碼會示範其中的第一個單字是粗體，而最後一個字是紅色的標籤：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
    var label = new Label { FontSize = 20 };
    var s = new FormattedString ();
    s.Spans.Add (new Span{ Text = "Red Bold", FontAttributes = FontAttributes.Bold });
    s.Spans.Add (new Span{ Text = "Default" });
    s.Spans.Add (new Span{ Text = "italic small", FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)), FontAttributes = FontAttributes.Italic});
    label.FormattedText = s;
        layout.Children.Add(label);
    }
}
```

在 XAML 中：

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.LabelPage"
Title="Label Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
      <Label FontSize=20>
        <Label.FormattedText>
          <FormattedString>
            <Span Text="Red Bold" ForegroundColor="Red" FontAttributes="Bold" />
            <Span Text="Default" />
            <Span Text="italic small" FontAttributes="Italic" FontSize="Small" />
          </FormattedString>
        </Label.FormattedText>
      </Label>
    </StackLayout>
  </ContentPage.Content>
</ContentPage>
```

![](label-images/formattedtext.png "標籤 FormattedText 範例")


## <a name="related-links"></a>相關連結

- [透過 Xamarin.Forms，第 3 章建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [標籤應用程式開發介面](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)
