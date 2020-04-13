---
title: Xamarin.表單標籤
description: 本文介紹如何使用 Xamarin.Forms 標籤類在應用程式中顯示單行和多行文本。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: bed6b8a774ecb352427f16b78d10e50821f92701
ms.sourcegitcommit: 765b69ed451a0f48625ea597c3f39de95f3ae693
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80987591"
---
# <a name="xamarinforms-label"></a>Xamarin.表單標籤

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_在 Xamarin.表單顯示文字_

該[`Label`](xref:Xamarin.Forms.Label)檢視用於顯示單行和多行文本。 標籤可以具有文本裝飾、彩色文本和使用自定義字體(族、大小和選項)。

## <a name="text-decorations"></a>文字裝飾

通過將屬性設置為一個或多個[`Label`](xref:Xamarin.Forms.Label)`Label.TextDecorations``TextDecorations`枚舉成員,可以將下劃線和擊線文本修飾應用於實例:

- `None`
- `Underline`
- `Strikethrough`

以下 XAML 範例`Label.TextDecorations`展示設定屬性:

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

對等的 C# 程式碼為：

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

以下螢幕截圖顯示了應用於`TextDecorations`[`Label`](xref:Xamarin.Forms.Label)實例的枚舉成員:

![以文字裝飾的標籤](label-images/label-textdecorations.png)

> [!NOTE]
> 文本修飾也可以應用於[`Span`](xref:Xamarin.Forms.Span)實例。 關於此類別的詳細資訊,`Span`請參考[格式化文字](#Formatted_Text)。

## <a name="character-spacing"></a>字元間距

字元間距可以通過[`Label`](xref:Xamarin.Forms.Label)`Label.CharacterSpacing`將 屬性`double`設定為 值應用於實體:

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

結果是,文本[`Label`](xref:Xamarin.Forms.Label)中顯示的字元與間隔`CharacterSpacing`設備無關的單位。

## <a name="new-lines"></a>新行

從 XAML[`Label`](xref:Xamarin.Forms.Label)將文字 強制到新行有兩種主要技術:

1. 使用單碼換行符,即"&#10;"。
1. 使用*屬性元素*語法指定文本。

以下代碼顯示了這兩種技術的範例:

```xaml
<!-- Unicode line feed character -->
<Label Text="First line &#10; Second line" />

<!-- Property element syntax -->
<Label>
    <Label.Text>
        First line
        Second line
    </Label.Text>
</Label>
```

在 C# 中,文字可以強制到具有「\n」字元的新行上:

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>色彩

標籤可以通過[`TextColor`](xref:Xamarin.Forms.Label.TextColor)可綁定屬性設置為使用自定義文本顏色。

為確保每個平臺上的顏色可用,需要特別注意。 由於每個平台的文本和背景顏色都有不同的預設值,因此您需要小心選擇適用於每個平台的預設值。

以下 XAML 範例設定`Label`的文字 顏色:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo">
    <StackLayout Padding="5,10">
      <Label TextColor="#77d065" FontSize = "20" Text="This is a green label." />
    </StackLayout>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
public partial class LabelPage : ContentPage
{
    public LabelPage ()
    {
        InitializeComponent ();

        var layout = new StackLayout { Padding = new Thickness(5,10) };
        var label = new Label { Text="This is a green label.", TextColor = Color.FromHex("#77d065"), FontSize = 20 };
        layout.Children.Add(label);
        this.Content = layout;
    }
}
```

以下螢幕截圖顯示了設定`TextColor`屬性的結果:

![標籤文字顏色範例](label-images/textcolor.png)

有關顏色的詳細資訊,請參閱[顏色](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字型

有關在 上指定字型的詳細`Label`資訊 ,請參閱[字型](~/xamarin-forms/user-interface/text/fonts.md)。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和包裝

可以設置標籤以處理無法以多種方式之一(由`LineBreakMode`屬性公開)在一行上擬合的文本。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)是具有以下值的枚舉:

- **頭截**&ndash;斷截斷文本的頭部,顯示結尾。
- **字元包裝**&ndash;在字元邊界處將文本包裝到新行上。
- **中間截圖**&ndash;顯示文本的開頭和結尾,中間以省略號替換。
- **NoWrap**&ndash;不會換行文本,只顯示一行可以容納的文本。
- **尾截條**&ndash;顯示文本的開頭,截斷末尾。
- **WordWrap**&ndash;在單詞邊界處包裝文本。

## <a name="display-a-specific-number-of-lines"></a>顯示特定數量的列

使用屬性`Label.MaxLines`設定為`int`值,可以指定[`Label`](xref:Xamarin.Forms.Label)由顯示的行數:

- 當`MaxLines`為 -1(其預設值)時`Label`, 屬性的[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)值僅 顯示一行(可能截斷)或包含所有文本的所有行。
- 當`MaxLines`為`Label`0 時,不顯示。
- 當`MaxLines`為 1 時,結果[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)[`NoWrap`](xref:Xamarin.Forms.LineBreakMode)與設定為[`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode)時[`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)相同[`TailTruncation`](xref:Xamarin.Forms.LineBreakMode), 或 。 但是,如果`Label`適用,將尊重[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)財產在放置橢圓時的價值。
- 當`MaxLines`大於 1`Label`時, 將顯示最多為指定數量的行[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)數,同時尊重 屬性相對於橢圓線放置的值(如果適用)。 但是,`MaxLines`[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)如果 屬性[`NoWrap`](xref:Xamarin.Forms.LineBreakMode)設置為 ,則將屬性設置為 大於 1 的值不起作用。

下面的 XAML 範例`MaxLines`展示在[`Label`](xref:Xamarin.Forms.Label)上設定 屬性:

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

對等的 C# 程式碼為：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

以下螢幕截圖顯示將`MaxLines`屬性設定為 2 的結果,當文字足夠長到超過 2 行時:

![標籤最大值線範例](label-images/label-maxlines.png)

## <a name="display-html"></a>顯示 HTML

類別[`Label`](xref:Xamarin.Forms.Label)具有屬性`TextType`,該屬性確定`Label`實體應顯示純文字還是 HTML 文本。 此屬性應設定為`TextType`枚舉的成員之一:

- `Text`表示 將`Label`顯示 純文本,並且是`Label.TextType`屬性的 預設值。
- `Html`表示將顯示`Label`HTML 文本。

因此,[`Label`](xref:Xamarin.Forms.Label)實體可以透過`Label.TextType`屬性設定為與`Html``Label.Text`屬性設定為 HTML 字串來顯示 HTML:

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

在上面的範例中,必須使用`\`符號轉義 HTML 中的雙引號字元。

在 XAML 中,由於額外轉`<``>`義 與符號,HTML 字串可能會變得不可讀:

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

或者,為了獲得更高的可讀性,可以在`CDATA`一節中內聯 HTML:

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

這個屬性`Label.Text`設定為`CDATA`節中內聯的 HTML 字串。 這之所以有效,`Text`因為屬性`ContentProperty`是`Label`類別的 。

以下螢幕截圖顯示[`Label`](xref:Xamarin.Forms.Label)HTML:

![在 iOS 與 Android 上顯示 HTML 的分頁螢幕擷取](label-images/label-html.png)

> [!IMPORTANT]
> 在[`Label`](xref:Xamarin.Forms.Label)中 顯示 HTML 僅限於基礎平台支援的 HTML 標記。

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化文字

標籤公開允許[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)在同一視圖中顯示具有多種字體和顏色的文本的屬性。

屬性`FormattedText`的類型為[`FormattedString`](xref:Xamarin.Forms.FormattedString),它包括一個或[`Span`](xref:Xamarin.Forms.Span)多個 實例,[`Spans`](xref:Xamarin.Forms.FormattedString.Spans)通過 屬性設置。 以下`Span`屬性可用於設定可視外觀:

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)• 跨距背景的顏色。
- `CharacterSpacing`類型`double`,`Span`是文字字元之間的間距。
- [`Font`](xref:Xamarin.Forms.Span.Font)• 範圍中文字的字體。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)• 範圍中文字的字體屬性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)• 範圍中文字的字體所屬的字體系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)• 範圍中文字的字體大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)• 範圍中文字的顏色。 此屬性已過時,已替換為該`TextColor`屬性。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)- 應用於跨度的預設線高度的乘數。 有關詳細資訊,請參閱[線高度](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style)• 要應用於範圍的樣式。
- [`Text`](xref:Xamarin.Forms.Span.Text)• 範圍的文本。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)• 範圍中文字的顏色。
- `TextDecorations`- 要應用於範圍中文字的裝飾。 有關詳細資訊,請參考[文字裝飾](#text-decorations)。

和[`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)[`Text`](xref:Xamarin.Forms.Span.Text)可[`Text`](xref:Xamarin.Forms.Span.Text)綁定屬性具有[`OneWay`](xref:Xamarin.Forms.BindingMode)預設綁定模式。 有關此繫結模式的詳細資訊,請參閱[連結模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中的[預設結合模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)。

此外,該[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)屬性可用於定義將回應上手勢的手勢辨識器的[`Span`](xref:Xamarin.Forms.Span)集合 。

> [!NOTE]
> 無法在中[`Span`](xref:Xamarin.Forms.Span)顯示 HTML。

下面的 XAML 範`FormattedText`例展示由[`Span`](xref:Xamarin.Forms.Span)三個實體組成的屬性:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="TextSample.LabelPage"
             Title="Label Demo - XAML">
    <StackLayout Padding="5,10">
        ...
        <Label LineBreakMode="WordWrap">
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Red Bold, " TextColor="Red" FontAttributes="Bold" />
                    <Span Text="default, " Style="{DynamicResource BodyStyle}">
                        <Span.GestureRecognizers>
                            <TapGestureRecognizer Command="{Binding TapCommand}" />
                        </Span.GestureRecognizers>
                    </Span>
                    <Span Text="italic small." FontAttributes="Italic" FontSize="Small" />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

對等的 C# 程式碼為：

```csharp
public class LabelPageCode : ContentPage
{
    public LabelPageCode ()
    {
        var layout = new StackLayout{ Padding = new Thickness (5, 10) };
        ...
        var formattedString = new FormattedString ();
        formattedString.Spans.Add (new Span{ Text = "Red bold, ", ForegroundColor = Color.Red, FontAttributes = FontAttributes.Bold });

        var span = new Span { Text = "default, " };
        span.GestureRecognizers.Add(new TapGestureRecognizer { Command = new Command(async () => await DisplayAlert("Tapped", "This is a tapped Span.", "OK")) });
        formattedString.Spans.Add(span);
        formattedString.Spans.Add (new Span { Text = "italic small.", FontAttributes = FontAttributes.Italic, FontSize =  Device.GetNamedSize(NamedSize.Small, typeof(Label)) });

        layout.Children.Add (new Label { FormattedText = formattedString });
        this.Content = layout;
    }
}
```

> [!IMPORTANT]
> 可透過[`Text`](xref:Xamarin.Forms.Span.Text)數據綁定設置`Span`屬性。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

請注意,還可以[`Span`](xref:Xamarin.Forms.Span)回應添加到 span[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)集合的任何手勢。 例如,在上述[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)代碼示例中,已添加到第`Span`二個代碼示例中。 因此,當點擊`Span`時`TapGestureRecognizer`, 將`ICommand`[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)通過執行 屬性定義的將回應。 有關手勢辨識器的詳細資訊,請參閱[Xamarin.窗體手勢](~/xamarin-forms/app-fundamentals/gestures/index.md)。

以下螢幕截圖顯示了將`FormattedString`屬性設定為三`Span`個 實體的結果:

![標籤格式化文字範例](label-images/formattedtext.png)

## <a name="line-height"></a>行高

可以[`Label`](xref:Xamarin.Forms.Label)通過[`Span`](xref:Xamarin.Forms.Span)[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)設定[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)`double`屬性或 值來自定義和的垂直高度。 在 iOS 和 Android 上,這些值是原始行高度的乘數,在`Label.LineHeight`通用 Windows 平臺 (UWP) 上,屬性值是標籤字體大小的乘數。

> [!NOTE]
>
> - 在[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)iOS[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)上 ,和 屬性更改適合單行的文本的行高度,以及環繞到多行的文本。
> - 在[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)Android[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)上 , 和 屬性僅更改環繞到多行的文本的行高。
> - 在 UWP[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)上,該屬性更改環繞到多行的文本的行[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)高度, 並且該屬性不起作用。

下面的 XAML 範例[`LineHeight`](xref:Xamarin.Forms.Label.LineHeight)展示在[`Label`](xref:Xamarin.Forms.Label)上設定 屬性:

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

對等的 C# 程式碼為：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

以下螢幕截圖顯示了將[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性設定為 1.8 的結果:

![標籤線高度範例](label-images/label-lineheight.png)

下面的 XAML 範例[`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)展示在[`Span`](xref:Xamarin.Forms.Span)上設定 屬性:

```xaml
<Label LineBreakMode="WordWrap">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. "
                  LineHeight="1.8"/>
            <Span Text="Nullam feugiat sodales elit, et maximus nibh vulputate id."
                  LineHeight="1.8" />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

對等的 C# 程式碼為：

```csharp
var formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In a tincidunt sem. Phasellus mollis sit amet turpis in rutrum. Sed aliquam ac urna id scelerisque. ",
  LineHeight = 1.8
});
formattedString.Spans.Add(new Span
{
  Text = "Nullam feugiat sodales elit, et maximus nibh vulputate id.",
  LineHeight = 1.8
});
var label = new Label
{
  FormattedText = formattedString,
  LineBreakMode = LineBreakMode.WordWrap
};
```

以下螢幕截圖顯示了將[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)屬性設定為 1.8 的結果:

![跨線高度範例](label-images/span-lineheight.png)

## <a name="padding"></a>填補

填充表示元素與其子元素之間的空間,用於將元素與其自己的內容分開。 可以通過`Label.Padding`屬性設定為[`Thickness`](xref:Xamarin.Forms.Thickness)值應用[`Label`](xref:Xamarin.Forms.Label)於 實體:

```xaml
<Label Padding="10">
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Lorem ipsum" />
            <Span Text="dolor sit amet." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

對等的 C# 程式碼為：

```csharp
FormattedString formattedString = new FormattedString();
formattedString.Spans.Add(new Span
{
  Text = "Lorem ipsum"
});
formattedString.Spans.Add(new Span
{
  Text = "dolor sit amet."
});
Label label = new Label
{
    FormattedText = formattedString,
    Padding = new Thickness(20)
};
```

> [!IMPORTANT]
> 在 iOS[`Label`](xref:Xamarin.Forms.Label)上 ,當`Padding`創建設置屬性 的 時,將應用填充,以後可以更新填充值。 但是,當創建`Label`未`Padding`設置 該屬性的 時,嘗試以後設置該屬性將不起作用。
>
> 在 Android 和通用`Padding`Windows 平臺上,`Label`可以在創建或更高 版本時指定屬性值。

有關填充的詳細資訊,請參閱[邊距和填充](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="hyperlinks"></a>超連結

使用以下方法,[`Label`](xref:Xamarin.Forms.Label)[`Span`](xref:Xamarin.Forms.Span)顯示與實體的文字可以轉換為超連結:

1. 設置`TextColor``TextDecoration`[`Label`](xref:Xamarin.Forms.Label)[`Span`](xref:Xamarin.Forms.Span)或的和 屬性。
1. 新增[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)[`Label`](xref:Xamarin.Forms.Label)到的[`Span`](xref:Xamarin.Forms.Span)集合中,[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)的屬性繫結到`ICommand`,[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)其屬性包含要開啟的網址。
1. `ICommand`定義將由執行的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)。
1. 編寫將由 執行的`ICommand`代碼 。

以下代碼範例取自[超連結示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)範例,顯示了其內容從[`Label`](xref:Xamarin.Forms.Label)[`Span`](xref:Xamarin.Forms.Span)多個 實體設定的 。

```xaml
<Label>
    <Label.FormattedText>
        <FormattedString>
            <Span Text="Alternatively, click " />
            <Span Text="here"
                  TextColor="Blue"
                  TextDecorations="Underline">
                <Span.GestureRecognizers>
                    <TapGestureRecognizer Command="{Binding TapCommand}"
                                          CommandParameter="https://docs.microsoft.com/xamarin/" />
                </Span.GestureRecognizers>
            </Span>
            <Span Text=" to view Xamarin documentation." />
        </FormattedString>
    </Label.FormattedText>
</Label>
```

在此示例中,第一個和第三[`Span`](xref:Xamarin.Forms.Span)個實例包括文本,而第`Span`二 個實例表示可評估的超連結。 它的文本顏色設置為藍色,並且具有下劃線文本修飾。 這將建立超連結的外觀,如以下螢幕截圖所示:

[![超連結](label-images/hyperlinks-small.png "超連結")](label-images/hyperlinks-large.png#lightbox)

當點擊超連結時,[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)將透過執行`ICommand`[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)其 屬性定義的來回應。 此外,[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性指定的網址會作為參數傳遞`ICommand`給 。

XAML 頁面的代碼後面包含`TapCommand`:

```csharp
public partial class MainPage : ContentPage
{
    // Launcher.OpenAsync is provided by Xamarin.Essentials.
    public ICommand TapCommand => new Command<string>(async (url) => await Launcher.OpenAsync(url));

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }
}
```

`TapCommand`執行`Launcher.OpenAsync`方法,將[`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性值作為參數傳遞。 該方法`Launcher.OpenAsync`由 Xamarin.Essentials 提供,並在 Web 瀏覽器中打開 URL。 因此,總體效果是,當在頁面上點擊超連結時,將顯示一個 Web 瀏覽器,並將與超連結關聯的 URL 導航到。

### <a name="creating-a-reusable-hyperlink-class"></a>建立可重用的超連結類別

創建超連結的上一種方法要求每次應用程式中需要超連結時編寫重複代碼。 但是,可以對[`Label`](xref:Xamarin.Forms.Label)[`Span`](xref:Xamarin.Forms.Span)和類進行子類化以創建`HyperlinkLabel`和`HyperlinkSpan`類,並添加手勢識別器和文本格式代碼。

以下代碼範例取自[超連結示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)範例,顯示了`HyperlinkSpan`一個 類:

```csharp
public class HyperlinkSpan : Span
{
    public static readonly BindableProperty UrlProperty =
        BindableProperty.Create(nameof(Url), typeof(string), typeof(HyperlinkSpan), null);

    public string Url
    {
        get { return (string)GetValue(UrlProperty); }
        set { SetValue(UrlProperty, value); }
    }

    public HyperlinkSpan()
    {
        TextDecorations = TextDecorations.Underline;
        TextColor = Color.Blue;
        GestureRecognizers.Add(new TapGestureRecognizer
        {
            // Launcher.OpenAsync is provided by Xamarin.Essentials.
            Command = new Command(async () => await Launcher.OpenAsync(Url))
        });
    }
}
```

類別`HyperlinkSpan`定義屬性`Url`與關聯的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty),建構函數設定超連結外觀,並在點擊超連結時將回應的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)。 `HyperlinkSpan`當被點擊時,`TapGestureRecognizer`將透過`Launcher.OpenAsync`執行方法來回應在 Web`Url`瀏覽器中打開 由屬性指定的網址。

可以`HyperlinkSpan`通過 將類的實體加入 XAML 來使用該類:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:HyperlinkDemo"
             x:Class="HyperlinkDemo.MainPage">
    <StackLayout>
        ...
        <Label>
            <Label.FormattedText>
                <FormattedString>
                    <Span Text="Alternatively, click " />
                    <local:HyperlinkSpan Text="here"
                                         Url="https://docs.microsoft.com/appcenter/" />
                    <Span Text=" to view AppCenter documentation." />
                </FormattedString>
            </Label.FormattedText>
        </Label>
    </StackLayout>
</ContentPage>
```

## <a name="styling-labels"></a>樣式標籤

前面的各節按實例[`Label`](xref:Xamarin.Forms.Label)[`Span`](xref:Xamarin.Forms.Span)介紹 設置和屬性。 但是,屬性集可以分組到一個樣式中,該樣式始終應用於一個或多個視圖。 這可以提高代碼的可讀性,並使設計更改更易於實現。 有關詳細資訊,請參閱[樣式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相關連結

- [文字(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [超連結(範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [使用 Xamarin.表單建立移動應用程式,第 3 章](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Label API](xref:Xamarin.Forms.Label)
- [跨 API](xref:Xamarin.Forms.Span)
