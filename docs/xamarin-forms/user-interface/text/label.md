---
title: Xamarin.Forms 標籤
description: 這篇文章說明如何使用 Xamarin.Forms 標籤類別在應用程式中會顯示單一和多行文字。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/16/2018
ms.openlocfilehash: b5069381126db0859508480df5596ed5ec85686f
ms.sourcegitcommit: 4c0093ee5d4aeb16c0e6f0c740c4796736971651
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2018
ms.locfileid: "39203032"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 標籤

_在 Xamarin.Forms 中的顯示文字_

[ `Label` ](xref:Xamarin.Forms.Label)檢視用於顯示文字、 單一和多行。 標籤可以具有 （家族、 大小和選項） 的自訂字型和色彩的文字。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和換行

標籤可以設定來處理無法在同一行中所公開的數種方式的其中一個符合的文字`LineBreakMode`屬性。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是列舉型別使用下列值：

- **HeadTruncation** &ndash;截斷的文字，顯示結尾標頭。
- **CharacterWrap** &ndash;到新的一行上的文字換行字元界限處。
- **MiddleTruncation** &ndash;顯示的開頭和結尾的文字，以省略符號中間取代。
- **NoWrap** &ndash;不換行文字，只顯示可以盡可能文字放在一行。
- **TailTruncation** &ndash;顯示文字，截斷結尾的開頭。
- **WordWrap** &ndash;包裝在字邊界的文字。

## <a name="fonts"></a>字型

如需有關指定字型`Label`，請參閱 <<c2> [ 字型](~/xamarin-forms/user-interface/text/fonts.md)。

## <a name="colors"></a>色彩

`Label`s 可以設定為使用自訂的文字色彩，透過可繫結[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)屬性。

特別是為了確保會在每個平台上的可用色彩。 因為每個平台都有不同的預設值的文字和背景色彩，您必須謹慎挑選每個的運作方式的預設值。

您可以使用下列 XAML 來設定標籤的文字色彩：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a label." />
    </StackLayout>
</ContentPage>
```

對等的 C# 程式碼是：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

下列螢幕擷取畫面顯示的設定結果`TextColor`屬性：

![](label-images/textcolor.png "標籤 TextColor 範例")

如需有關色彩的詳細資訊，請參閱[色彩](~/xamarin-forms/user-interface/colors.md)。

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化的文字

標籤會公開[ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText)屬性可讓文字的呈現方式與多個字型和色彩在相同的檢視。

`FormattedText`屬性的類型是[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)，其中包括一或多個[ `Span` ](xref:Xamarin.Forms.Span)情況下，透過設定[ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans)屬性. 每個`Span`擁有下列屬性：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) -s p a n 背景的色彩。
- [`Font`](xref:Xamarin.Forms.Span.Font) – 範圍中文字的字型。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – 範圍中文字的字型屬性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – 所屬範圍中文字的字型的字型系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – 範圍中文字的字型的大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – 範圍中文字的色彩。 這個屬性已經過時，已被取代`TextColor`屬性。
- [`Style`](xref:Xamarin.Forms.Span.Style) – 要套用至範圍的樣式。
- [`Text`](xref:Xamarin.Forms.Span.Text) – 範圍的文字。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – 範圍中文字的色彩。

下列 XAML 範例將示範`FormattedText`屬性，其中包含三個`Span`執行個體：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}" />
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

對等的 C# 程式碼是：

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });
        formattedString.Spans.Add (new Span { Text = "default, ", Style = Device.Styles.BodyStyle });
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!NOTE]
> [ `Text` ](xref:Xamarin.Forms.Span.Text)屬性`Span`可以透過資料繫結設定。 如需詳細資訊，請參閱 <<c0> [ 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

下列螢幕擷取畫面顯示的設定結果`FormattedString`三個屬性`Span`執行個體：

![](label-images/formattedtext.png "標籤 FormattedText 範例")

## <a name="styling-a-label"></a>樣式標籤

前幾節涵蓋設定[ `Label` ](xref:Xamarin.Forms.Label)以每個執行個體為基礎的屬性。 不過，屬性的集合可以分組為會一致地套用到一或多個檢視的一種樣式。 這可以提高可讀性的程式碼，並讓您更輕鬆地實作設計變更。 如需詳細資訊，請參閱 <<c0> [ 樣式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相關連結

- [使用 Xamarin.Forms 時，第 3 章中建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [文字 （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [標籤 API](xref:Xamarin.Forms.Label)
