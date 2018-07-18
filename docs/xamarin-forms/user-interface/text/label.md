---
title: Xamarin.Forms 標籤
description: 這篇文章說明如何使用 Xamarin.Forms 標籤類別在應用程式中會顯示單一和多行文字。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/22/2017
ms.openlocfilehash: ce602a84ea1024dc22298a3ec1567a9a34ad4a82
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995962"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 標籤

_在 Xamarin.Forms 中的顯示文字_

`Label`檢視用於顯示文字、 單一和多行。 標籤可以具有 （家族、 大小和選項） 的自訂字型和色彩的文字。 本文章涵蓋下列主題：

- **[截斷和包裝](#Truncation_and_Wrapping)** &ndash;截斷和換行選項來處理文字不能放在一行的情況。
- **[字型](#Font)** &ndash;的字型選項。
- **[色彩](#Color)** &ndash;標籤文字的色彩選項。
- **[格式化的文字](#Formatted_Text)** &ndash;選項，可顯示多個格式/樣式內嵌的文字。

## <a name="styling-label"></a>樣式標籤

下列各節涵蓋設定屬性`Label`以手動方式在每個執行個體為基礎。 請注意，屬性的集合可以分組為一個會一致地套用到一或多個檢視的樣式。 這可以提高可讀性的程式碼，並讓您更輕鬆地實作設計變更。 請參閱[樣式](~/xamarin-forms/user-interface/text/styles.md)如需詳細資訊。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和換行

標籤可以設定來處理無法在同一行中所公開的數種方式的其中一個符合的文字`LineBreakMode`屬性。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是一種列舉的下列選項：

- **HeadTruncation** &ndash;截斷的文字，顯示結尾標頭。
- **CharacterWrap** &ndash;到新的一行上的文字換行字元界限處。
- **MiddleTruncation** &ndash;顯示的開頭和結尾的文字，以省略符號中間取代。
- **NoWrap** &ndash;不換行文字，只顯示可以盡可能文字放在一行。
- **TailTruncation** &ndash;顯示文字，截斷結尾的開頭。
- **WordWrap** &ndash;包裝在字邊界的文字。

## <a name="font"></a>字型

請參閱[使用字型](~/xamarin-forms/user-interface/text/fonts.md)如需詳細資訊。

## <a name="color"></a>色彩

`Label`s 可以設定為使用自訂的文字色彩，透過可繫結`TextColor`屬性。

特別是為了確保會在每個平台上的可用色彩。 因為每個平台都有不同的預設值的文字和背景色彩，您必須謹慎挑選每個的運作方式的預設值。

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

標籤公開`FormattedText`屬性可讓您呈現文字，以多個字型和色彩在相同的檢視。

`FormattedText`屬性的類型是[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)。 格式化的字串所組成一或多個`Span`s，每一個都具有下列屬性：

- **BackgroundColor** &ndash;可用來設定背景色彩，例如若要達到螢光筆的效果。
- **FontAttributes** &ndash;可以設為粗體、 斜體、 或兩者皆非。
- **FontFamily** &ndash;設定要使用的字型。
- **FontSize** &ndash;設定文字的大小。
- **ForegroundColor** &ndash;設定文字的色彩。
- **文字**&ndash;来呈現的文字。

下列 C# 程式碼將示範其中的第一個文字是粗體，而最後一個字是 red 的標籤：

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

- [使用 Xamarin.Forms 時，第 3 章中建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [標籤 API](xref:Xamarin.Forms.Label)
