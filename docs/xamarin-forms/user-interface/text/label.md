---
title: Xamarin.Forms 標籤
description: 本文說明如何使用 Xamarin.Forms Label 類別，在應用程式中顯示單一和多行文字。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 045d2fa3575323a6a829921fb1b346c6d310cc6e
ms.sourcegitcommit: 10c7dd16fe78226053d1d036492b6c9102fc421b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "93374767"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 標籤

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-text)

_顯示文字 Xamarin.Forms_

此 [`Label`](xref:Xamarin.Forms.Label) 視圖會用來顯示文字，包括單行和多行。 標籤可以有文字裝飾、彩色文字，以及使用自訂字型 (系列、大小和選項) 。

## <a name="text-decorations"></a>文字裝飾

您可以將 [`Label`](xref:Xamarin.Forms.Label) `Label.TextDecorations` 屬性設定為一或多個列舉成員，以將底線和刪除線文字裝飾套用至實例 `TextDecorations` ：

- `None`
- `Underline`
- `Strikethrough`

下列 XAML 範例將示範如何設定 `Label.TextDecorations` 屬性：

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

下列螢幕擷取畫面顯示套用 `TextDecorations` 至實例的列舉成員 [`Label`](xref:Xamarin.Forms.Label) ：

![具有文字裝飾的標籤](label-images/label-textdecorations.png)

> [!NOTE]
> 文字裝飾也可以套用至 [`Span`](xref:Xamarin.Forms.Span) 實例。 如需類別的詳細資訊 `Span` ，請參閱 [格式化的文字](#formatted-text)。

## <a name="transform-text"></a>轉換文字

[`Label`](xref:Xamarin.Forms.Entry)可以藉 `Text` 由將 `TextTransform` 屬性設定為列舉值，來轉換其儲存在屬性中的文字大小寫 `TextTransform` 。 此列舉有四個值：

- `None` 表示不轉換文字。
- `Default` 指出將使用平臺的預設行為。 此為 `TextTransform` 屬性的預設值。
- `Lowercase` 指出文字將轉換成小寫。
- `Uppercase` 指出文字將轉換成大寫。

下列範例顯示如何將文字轉換成大寫：

```xaml
<Label Text="This text will be displayed in uppercase."
       TextTransform="Uppercase" />
```

對等的 C# 程式碼為：

```csharp
Label label = new Label
{
    Text = "This text will be displayed in uppercase.",
    TextTransform = TextTransform.Uppercase
};
```

## <a name="character-spacing"></a>字元間距

您可以藉 [`Label`](xref:Xamarin.Forms.Label) 由將 `Label.CharacterSpacing` 屬性設定為值，將字元間距套用至實例 `double` ：

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

結果是，所顯示文字中的字元會與 [`Label`](xref:Xamarin.Forms.Label) `CharacterSpacing` 裝置無關的單位分隔開來。

## <a name="new-lines"></a>新行

有兩個主要的技巧可將文字 [`Label`](xref:Xamarin.Forms.Label) 從 XAML 強制插入新行：

1. 使用 unicode 換行字元，也就是 " &amp; #10;"。
1. 使用 *屬性* 專案語法來指定文字。

下列程式碼顯示這兩種技術的範例：

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

在 c # 中，可以使用 "\n" 字元將文字強制放入新行：

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>色彩

您可以透過可系結屬性，將標籤設定為使用自訂文字色彩 [`TextColor`](xref:Xamarin.Forms.Label.TextColor) 。

請特別小心，以確保每個平臺都能使用這些色彩。 由於每個平臺的文字和背景色彩都有不同的預設值，因此您必須小心挑選每個平臺的預設值。

下列 XAML 範例會設定的文字色彩 `Label` ：

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

下列螢幕擷取畫面顯示設定屬性的結果 `TextColor` ：

![標籤 TextColor 範例](label-images/textcolor.png)

如需色彩的詳細資訊，請參閱 [色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字型

如需在上指定字型的詳細資訊 `Label` ， [](~/xamarin-forms/user-interface/text/fonts.md)請參閱字型。

## <a name="truncation-and-wrapping"></a>截斷和換行

您可以設定標籤，以透過數種方式來處理無法容納在同一行的文字（由 `LineBreakMode` 屬性公開）。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是具有下列值的列舉：

- **HeadTruncation** &ndash; 截斷文字的開頭，以顯示結尾。
- **CharacterWrap** &ndash; 將文字包裝在字元界限的新行上。
- **MiddleTruncation** &ndash; 顯示文字的開頭和結尾，中間以省略號取代。
- **NoWrap** &ndash; 不會將文字換行，只會在一行中顯示最多文字。
- **TailTruncation** &ndash; 顯示文字的開頭，截斷結尾。
- **WordWrap** &ndash; 在字邊界換行文字。

## <a name="display-a-specific-number-of-lines"></a>顯示特定的行數

藉 [`Label`](xref:Xamarin.Forms.Label) 由將屬性設定為值，可指定所顯示的行數 `Label.MaxLines` `int` ：

- 當 `MaxLines` 為-1 （其為預設值）時，會將 `Label` 屬性的值 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 設為只顯示一行、可能會被截斷，或全部包含所有文字的行。
- 當 `MaxLines` 是0時， `Label` 不會顯示。
- 當 `MaxLines` 是1時，結果會與將屬性設 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 為 [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) 、 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode) 、或相同 [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode) [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode) 。 但是， `Label` 將會採用屬性的值（ [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 如果適用的話），以放置省略號。
- 當 `MaxLines` 大於1時， `Label` 會顯示最多指定的行數，同時 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 遵守屬性的位置（如果有的話）的值。 但是， `MaxLines` 如果屬性設定為，則將屬性設為大於1的值將不會有任何作用 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) [`NoWrap`](xref:Xamarin.Forms.LineBreakMode) 。

下列 XAML 範例將示範如何 `MaxLines` 在上設定屬性 [`Label`](xref:Xamarin.Forms.Label) ：

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

下列螢幕擷取畫面顯示 `MaxLines` 當文字的長度足以容納2行以上時，將屬性設定為2的結果：

![標籤 MaxLines 範例](label-images/label-maxlines.png)

## <a name="display-html"></a>顯示 HTML

[`Label`](xref:Xamarin.Forms.Label)類別具有 `TextType` 屬性，可判斷 `Label` 實例是否應顯示純文字或 HTML 文字。 這個屬性應該設定為列舉的其中一個成員 `TextType` ：

- `Text` 指出 `Label` 將會顯示純文字，而是屬性的預設值 `Label.TextType` 。
- `Html` 指出 `Label` 將顯示 HTML 文字。

因此， [`Label`](xref:Xamarin.Forms.Label) 實例可以藉由將屬性設定 `Label.TextType` 為 `Html` ，並將 `Label.Text` 屬性設定為 HTML 字串來顯示 html：

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

在上述範例中，必須使用符號來將 HTML 中的雙引號字元加上引號 `\` 。

在 XAML 中，HTML 字串可能會因為額外的轉義 `<` 和符號而變成無法讀取 `>` ：

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

或者，為了更容易閱讀，您可以在區段中內嵌 HTML `CDATA` ：

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

在此範例中， `Label.Text` 屬性會設定為在區段中內嵌的 HTML 字串 `CDATA` 。 因為 `Text` 屬性是類別的，所以這是可行的 `ContentProperty` `Label` 。

下列螢幕擷取畫面顯示 [`Label`](xref:Xamarin.Forms.Label) 顯示 HTML：

![螢幕擷取畫面：在 iOS 和 Android 上顯示 HTML 的標籤](label-images/label-html.png)

> [!IMPORTANT]
> 顯示中的 HTML [`Label`](xref:Xamarin.Forms.Label) 僅限於基礎平臺所支援的 html 標記。

## <a name="formatted-text"></a>格式化文字

標籤 [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 會公開屬性，以允許在相同的視圖中使用多種字型和色彩來呈現文字。

`FormattedText`屬性的類型是 [`FormattedString`](xref:Xamarin.Forms.FormattedString) ，它包含一或多個透過 [`Span`](xref:Xamarin.Forms.Span) 屬性設定的實例 [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) 。 您 `Span` 可以使用下列屬性來設定視覺外觀：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) –範圍背景的色彩。
- `CharacterSpacing`的類型 `double` 是文字字元之間的間距 `Span` 。
- [`Font`](xref:Xamarin.Forms.Span.Font) –範圍中文字的字型。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) –範圍中文字的字型屬性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) –範圍中文字字型所屬的字型家族。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) –範圍中文字的字型大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) –範圍中的文字色彩。 這個屬性已過時，且已由屬性取代 `TextColor` 。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -要套用至範圍預設行高的乘數。 如需詳細資訊，請參閱 [線條高度](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style)  –要套用至範圍的樣式。
- [`Text`](xref:Xamarin.Forms.Span.Text) –範圍的文字。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) –範圍中的文字色彩。
- `TextDecorations` -要套用至範圍中文字的裝飾。 如需詳細資訊，請參閱 [文字裝飾](#text-decorations)。

、和可系結屬性的預設系結 [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text) 模式為 [`Text`](xref:Xamarin.Forms.Span.Text) [`OneWay`](xref:Xamarin.Forms.BindingMode) 。 如需有關此系結模式的詳細資訊，請參閱系結[模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中[的預設](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)系結模式。

此外， [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) 屬性也可以用來定義筆勢辨識器的集合，以回應上的手勢 [`Span`](xref:Xamarin.Forms.Span) 。

> [!NOTE]
> 不可能在中顯示 HTML [`Span`](xref:Xamarin.Forms.Span) 。

下列 XAML 範例示範 `FormattedText` 包含三個實例的屬性 [`Span`](xref:Xamarin.Forms.Span) ：

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
> 您 [`Text`](xref:Xamarin.Forms.Span.Text) `Span` 可以透過資料系結來設定的屬性。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

請注意， [`Span`](xref:Xamarin.Forms.Span) 也可以回應新增至範圍集合的任何手勢 [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) 。 例如，在 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) `Span` 上述程式碼範例中，已新增至第二個。 因此，當這種情況下， `Span` `TapGestureRecognizer` 會執行由屬性定義的來回應 `ICommand` [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) 。 如需手勢辨識器的詳細資訊，請參閱[ Xamarin.Forms 手勢](~/xamarin-forms/app-fundamentals/gestures/index.md)。

下列螢幕擷取畫面顯示將 `FormattedString` 屬性設為三個實例的結果 `Span` ：

![標籤 FormattedText 範例](label-images/formattedtext.png)

## <a name="line-height"></a>行高

您 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 可以藉由設定 [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 屬性或值，自訂和的垂直高度 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) `double` 。 在 iOS 和 Android 上，這些值是原始線條高度的乘數，而在通用 Windows 平臺 (UWP) `Label.LineHeight` 屬性值是標籤字型大小的乘數。

> [!NOTE]
>
> - 在 iOS 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 和 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 屬性會變更適合一行的文字行高，以及換行至多行的文字。
> - 在 Android 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 和 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 屬性只會變更換行至多行文字的線條高度。
> - 在 UWP 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 屬性會變更換行至多行文字的線條高度，而且 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 屬性不會有任何作用。

下列 XAML 範例將示範如何 [`LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 在上設定屬性 [`Label`](xref:Xamarin.Forms.Label) ：

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

下列螢幕擷取畫面顯示將 [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) 屬性設為1.8 的結果：

![標籤 LineHeight 範例](label-images/label-lineheight.png)

下列 XAML 範例將示範如何 [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 在上設定屬性 [`Span`](xref:Xamarin.Forms.Span) ：

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

下列螢幕擷取畫面顯示將 [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) 屬性設為1.8 的結果：

![Span LineHeight 範例](label-images/span-lineheight.png)

## <a name="padding"></a>填補

填補代表元素和其子項目之間的空間，用來將專案與本身的內容分開。 您可以藉 [`Label`](xref:Xamarin.Forms.Label) 由將 `Label.Padding` 屬性設定為值，將填補套用至實例 [`Thickness`](xref:Xamarin.Forms.Thickness) ：

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
> 在 iOS 上，當 [`Label`](xref:Xamarin.Forms.Label) 建立設定屬性的時 `Padding` ，將會套用填補，之後可以更新填補值。 但是，當 `Label` 建立未設定屬性的時 `Padding` ，稍後嘗試設定它將不會有任何作用。
>
> 在 Android 和通用 Windows 平臺上，您 `Padding` 可以在 `Label` 建立或更新版本時指定屬性值。

如需填補的詳細資訊，請參閱 [邊界和邊框距離](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="hyperlinks"></a>超連結

和實例所顯示的文字 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 可以使用下列方法轉換成超連結：

1. 設定 `TextColor` 或的和 `TextDecoration` 屬性 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 。
1. 將加入 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 至 [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) 或的集合 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) ，其屬性會系 [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) 結至 `ICommand` ，而且其 [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 屬性包含要開啟的 URL。
1. 定義 `ICommand` 將由執行的 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 。
1. 撰寫將由執行的程式碼 `ICommand` 。

下列程式碼範例（取自 [超連結示範](/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) 範例） [`Label`](xref:Xamarin.Forms.Label) 會顯示其內容設定自多個 [`Span`](xref:Xamarin.Forms.Span) 實例的。

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

在此範例中，第一個和第三個 [`Span`](xref:Xamarin.Forms.Span) 實例組成文字，而第二個則 `Span` 代表 tappable 超連結。 它的文字色彩設為藍色，並具有底線文字裝飾。 這會建立超連結的外觀，如下列螢幕擷取畫面所示：

[![超連結](label-images/hyperlinks-small.png "超連結")](label-images/hyperlinks-large.png#lightbox)

當您按下超連結時， [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 會執行由 `ICommand` 其屬性定義的來回應 [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command) 。 此外，屬性所指定的 URL [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 將會以參數的形式傳遞至 `ICommand` 。

XAML 頁面的程式碼後端包含實作為 `TapCommand` ：

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

會 `TapCommand` 執行 `Launcher.OpenAsync` 方法，並將 [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter) 屬性值傳遞為參數。 `Launcher.OpenAsync`方法是由提供 Xamarin.Essentials ，並在網頁瀏覽器中開啟 URL。 因此，整體效果是在頁面上按下超連結時，網頁瀏覽器隨即出現，並會流覽與超連結相關聯的 URL。

### <a name="creating-a-reusable-hyperlink-class"></a>建立可重複使用的超連結類別

先前建立超連結的方法需要在您的應用程式中每次需要超連結時撰寫重複的程式碼。 不過，和類別都可以子類別化 [`Label`](xref:Xamarin.Forms.Label) [`Span`](xref:Xamarin.Forms.Span) 來建立 `HyperlinkLabel` 和 `HyperlinkSpan` 類別，並在該處加入手勢辨識器和文字格式設定程式碼。

下列程式碼範例（取自 [超連結示範](/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/) 範例）會顯示 `HyperlinkSpan` 類別：

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

`HyperlinkSpan`類別 `Url` 會定義屬性並相關聯 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) ，而此函式會設定超連結的外觀，以及在按下 [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 超連結時回應的。 當 `HyperlinkSpan` 按下時， `TapGestureRecognizer` 會 `Launcher.OpenAsync` `Url` 在網頁瀏覽器中執行方法來開啟 URL （由屬性指定），以回應。

將 `HyperlinkSpan` 類別的實例加入至 XAML，即可取用類別：

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

## <a name="styling-labels"></a>設定標籤的樣式

上述各節涵蓋了針對個別實例的設定 [`Label`](xref:Xamarin.Forms.Label) 和 [`Span`](xref:Xamarin.Forms.Span) 屬性。 不過，屬性集可以分組為一種一致套用至一或多個視圖的樣式。 這可提高程式碼的可讀性，並讓設計變更更容易執行。 如需詳細資訊，請參閱 [樣式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相關連結

- [Text (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [ (範例) 的超連結 ](/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [使用 Xamarin.Forms 第3章建立 Mobile Apps](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Label API](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)