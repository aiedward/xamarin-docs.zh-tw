---
title: Xamarin. 表單標籤
description: 本文說明如何使用 [Xamarin] 標籤類別，在應用程式中顯示單一和多行文字。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/09/2020
ms.openlocfilehash: 6ce4e39986afb7444e7d126e9789760d9311ca45
ms.sourcegitcommit: 737c7fbbe8aed1f33110a5217d7e6d6ef3e5b785
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793218"
---
# <a name="xamarinforms-label"></a>Xamarin. 表單標籤

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_在 [Xamarin] 表單中顯示文字_

此[`Label`](xref:Xamarin.Forms.Label)視圖會用於顯示文字，包括單行和多行。 標籤可以具有文字裝飾、彩色文字，並使用自訂字型（系列、大小和選項）。

## <a name="text-decorations"></a>文字裝飾

將`Label.TextDecorations`屬性設定為一個或多個`TextDecorations`列舉[`Label`](xref:Xamarin.Forms.Label)成員，即可將底線和刪除線的文字裝飾套用至實例：

- `None`
- `Underline`
- `Strikethrough`

下列 XAML 範例將示範如何設定`Label.TextDecorations`屬性：

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

下列螢幕擷取畫面顯示套用`TextDecorations`至[`Label`](xref:Xamarin.Forms.Label)實例的列舉成員：

![具有文字裝飾的標籤](label-images/label-textdecorations.png)

> [!NOTE]
> 文字裝飾也可以套用至[`Span`](xref:Xamarin.Forms.Span)實例。 如需類別的`Span`詳細資訊，請參閱[格式化文字](#Formatted_Text)。

## <a name="character-spacing"></a>字元間距

將`Label.CharacterSpacing`屬性設定為`double`值， [`Label`](xref:Xamarin.Forms.Label)即可將字元間距套用至實例：

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

結果是所顯示文字中的字元[`Label`](xref:Xamarin.Forms.Label)會與`CharacterSpacing`裝置獨立單位分開。

## <a name="new-lines"></a>新行

有兩個主要的技術可將中的[`Label`](xref:Xamarin.Forms.Label)文字強制執行至新行，從 XAML：

1. 使用 unicode 換行字元，也就是 "&amp;#10;"。
1. 使用*屬性元素*語法指定文字。

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

在 c # 中，文字可以強制放在具有 "\n" 字元的新行上：

```csharp
Label label = new Label { Text = "First line\nSecond line" };
```

## <a name="colors"></a>色彩

標籤可以透過可系結[`TextColor`](xref:Xamarin.Forms.Label.TextColor)屬性設定為使用自訂文字色彩。

必須特別小心，以確保每個平臺都能使用色彩。 由於每個平臺的文字和背景色彩都有不同的預設值，因此您必須小心挑選預設值，以在每個平臺上運作。

下列 XAML 範例會設定的文字色彩`Label`：

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

下列螢幕擷取畫面顯示設定`TextColor`屬性的結果：

![標籤 TextColor 範例](label-images/textcolor.png)

如需色彩的詳細資訊，請參閱[色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字型

如需在上指定字型的詳細`Label`資訊，[請參閱字型](~/xamarin-forms/user-interface/text/fonts.md)。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和換行

標籤可以設定為以數種方式的其中一行來處理無法容納的文字（由屬性`LineBreakMode`所公開）。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)是具有下列值的列舉：

- **HeadTruncation** &ndash;會截斷文字的標頭，並顯示結尾。
- **CharacterWrap** &ndash;會將文字包裝在字元界限的新行上。
- **MiddleTruncation** &ndash;會顯示文字的開頭和結尾，並以省略號取代中間的。
- **NoWrap** &ndash;不會將文字換行，只會顯示一行中的最多文字。
- **TailTruncation** &ndash;會顯示文字的開頭，並截斷結尾。
- **自動換行** &ndash;會將文字包裝在字邊界上。

## <a name="display-a-specific-number-of-lines"></a>顯示特定數目的行

藉由將`Label.MaxLines`屬性設定為`int`值[`Label`](xref:Xamarin.Forms.Label) ，即可指定所顯示的行數：

- 當`MaxLines`是-1 （這是其預設值）時`Label` ，會將[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性的值接受為只顯示一行、可能被截斷，或包含所有文字的所有行。
- 當`MaxLines`為0時， `Label`不會顯示。
- 當`MaxLines`是1時，結果等同于將[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性設定為[`NoWrap`](xref:Xamarin.Forms.LineBreakMode)、 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode)、 [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)或。 [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode) 不過， `Label`將會遵循[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性的值（如果適用的話）。
- 當`MaxLines`大於1時， `Label`將會顯示到指定的行數，同時採用[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性的值（如果適用的話）。 不過，如果`MaxLines` [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性設定為[`NoWrap`](xref:Xamarin.Forms.LineBreakMode)，則將屬性設定為大於1的值將不會有任何作用。

下列 XAML 範例示範如何在上`MaxLines`設定屬性[`Label`](xref:Xamarin.Forms.Label)：

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

下列螢幕擷取畫面顯示將`MaxLines`屬性設定為2的結果（當文字長度足以佔用2行以上）時：

![標籤 MaxLines 範例](label-images/label-maxlines.png)

## <a name="display-html"></a>顯示 HTML

[`Label`](xref:Xamarin.Forms.Label)類別具有`TextType`屬性，可決定`Label`實例是否應顯示純文字或 HTML 文字。 這個屬性應該設定為`TextType`列舉的其中一個成員：

- `Text`表示`Label`將會顯示純文字，而則是`Label.TextType`屬性的預設值。
- `Html`表示`Label`將會顯示 HTML 文字。

因此， [`Label`](xref:Xamarin.Forms.Label)實例可以將`Label.TextType`屬性設為`Html`，並將`Label.Text`屬性設定為 html 字串，藉以顯示 html：

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

在上述範例中，必須使用`\`符號來轉義 HTML 中的雙引號字元。

在 XAML 中，HTML 字串可能會因為額外的`<`轉義和符號`>`而變成無法讀取：

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

或者，為了提高可讀性，HTML 可以內嵌在`CDATA`區段中：

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

在此範例中， `Label.Text`屬性會設定為在`CDATA`區段中內嵌的 HTML 字串。 這是可行的`Text` ，因為屬性`ContentProperty`是`Label`類別的。

下列螢幕擷取畫面顯示 HTML [`Label`](xref:Xamarin.Forms.Label)的顯示：

![在 iOS 和 Android 上顯示 HTML 的標籤螢幕擷取畫面](label-images/label-html.png)

> [!IMPORTANT]
> 在中[`Label`](xref:Xamarin.Forms.Label)顯示 html 僅限於基礎平臺支援的 html 標記。

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化文字

標籤會[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)公開屬性，以允許在相同的視圖中呈現具有多個字型和色彩的文字。

`FormattedText`屬性的類型[`FormattedString`](xref:Xamarin.Forms.FormattedString)是，其中包含一或多個[`Span`](xref:Xamarin.Forms.Span)透過[`Spans`](xref:Xamarin.Forms.FormattedString.Spans)屬性設定的實例。 下列`Span`屬性可用於設定視覺外觀：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)–範圍背景的色彩。
- `CharacterSpacing`，屬於類型`double`，這是`Span`文字字元之間的間距。
- [`Font`](xref:Xamarin.Forms.Span.Font)–範圍中文字的字型。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)–範圍中文字的字型屬性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)–範圍中文字的字型所屬的字型系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize)–範圍中文字的字型大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)–範圍中文字的色彩。 這個屬性已經過時，並已由`TextColor`屬性取代。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)-要套用至範圍的預設行高的乘數。 如需詳細資訊，請參閱[行高](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style)–要套用至範圍的樣式。
- [`Text`](xref:Xamarin.Forms.Span.Text)–範圍的文字。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor)–範圍中文字的色彩。
- `TextDecorations`-要套用至範圍中文字的裝飾。 如需詳細資訊，請參閱[文字裝飾](#text-decorations)。

[`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`Text`](xref:Xamarin.Forms.Span.Text)、 [`Text`](xref:Xamarin.Forms.Span.Text)和可系結屬性的預設系結模式為[`OneWay`](xref:Xamarin.Forms.BindingMode)。 如需此系結模式的詳細資訊，請參閱系結[模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中[的預設](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)系結模式。

此外， [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)屬性也可以用來定義筆勢辨識器的集合，這些辨識器會回應中的[`Span`](xref:Xamarin.Forms.Span)手勢。

> [!NOTE]
> 不可能在中顯示 HTML [`Span`](xref:Xamarin.Forms.Span)。

下列 XAML 範例示範包含三`FormattedText`個[`Span`](xref:Xamarin.Forms.Span)實例的屬性：

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
> 的[`Text`](xref:Xamarin.Forms.Span.Text)屬性`Span`可以透過資料系結來設定。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

請注意， [`Span`](xref:Xamarin.Forms.Span)也可以回應任何加入至範圍[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)集合的手勢。 例如，在上述[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)程式碼範例中，已`Span`新增至第二個。 因此，在此`Span`情況下， `TapGestureRecognizer`會藉由執行`ICommand` [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性所定義的來回應。 如需筆勢辨識器的詳細資訊，請參閱「 [Xamarin」手勢](~/xamarin-forms/app-fundamentals/gestures/index.md)。

下列螢幕擷取畫面顯示將`FormattedString`屬性設定為三個`Span`實例的結果：

![標籤 FormattedText 範例](label-images/formattedtext.png)

## <a name="line-height"></a>行高

[`Label`](xref:Xamarin.Forms.Label)您[`Span`](xref:Xamarin.Forms.Span)可以藉由設定[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性或[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) `double`值來自訂和的垂直高度。 在 iOS 和 Android 上，這些值是原始行高的乘數，而在通用 Windows 平臺（UWP）上， `Label.LineHeight`屬性值是標籤字型大小的乘數。

> [!NOTE]
>
> - 在 iOS 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)和[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)屬性會變更符合單一線條的文字行高，以及換行至多行的文字。
> - 在 Android 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)和[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)屬性只會變更包裝到多行文字的線條高度。
> - 在 UWP 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性會變更包裝到多行的文字行高，而屬性則[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)不會有任何作用。

下列 XAML 範例示範如何在上[`LineHeight`](xref:Xamarin.Forms.Label.LineHeight)設定屬性[`Label`](xref:Xamarin.Forms.Label)：

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

下列螢幕擷取畫面顯示將[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性設定為1.8 的結果：

![標籤 LineHeight 範例](label-images/label-lineheight.png)

下列 XAML 範例示範如何在上[`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)設定屬性[`Span`](xref:Xamarin.Forms.Span)：

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

下列螢幕擷取畫面顯示將[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)屬性設定為1.8 的結果：

![Span LineHeight 範例](label-images/span-lineheight.png)

## <a name="padding"></a>填補

填補代表專案和其子專案之間的間距，並用來分隔專案與本身的內容。 將`Label.Padding`屬性設定為[`Thickness`](xref:Xamarin.Forms.Thickness)值[`Label`](xref:Xamarin.Forms.Label) ，即可將填補套用至實例：

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
> 在 iOS 上，當[`Label`](xref:Xamarin.Forms.Label)建立設定`Padding`屬性的時，將會套用填補，並于稍後更新填補值。 不過，建立未`Label`設定`Padding`屬性的時，稍後嘗試設定它將不會有任何作用。
>
> 在 Android 和通用 Windows 平臺上，當`Padding`建立或更新版本時`Label` ，可以指定屬性值。

如需填補的詳細資訊，請參閱[邊界和填補](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="hyperlinks"></a>超連結

[`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)實例所顯示的文字，可以使用下列方法轉換成超連結：

1. 設定[`Label`](xref:Xamarin.Forms.Label)或`TextColor` [`Span`](xref:Xamarin.Forms.Span)的`TextDecoration`和屬性。
1. 將加入[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)至[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) [`Label`](xref:Xamarin.Forms.Label)或[`Span`](xref:Xamarin.Forms.Span)的集合，其[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性系結至`ICommand`，而其[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性包含要開啟的 URL。
1. `ICommand`定義將由執行的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)。
1. 撰寫將由執行的程式碼`ICommand`。

下列取自[超連結](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)示範範例的程式碼範例會顯示， [`Label`](xref:Xamarin.Forms.Label)其內容是從多個[`Span`](xref:Xamarin.Forms.Span)實例所設定：

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

在此範例中，第一個和[`Span`](xref:Xamarin.Forms.Span)第三個實例會包含文字`Span` ，第二個則代表 tappable 超連結。 其文字色彩設為藍色，且具有底線文字裝飾。 這會建立超連結的外觀，如下列螢幕擷取畫面所示：

[![超連結](label-images/hyperlinks-small.png "超連結")](label-images/hyperlinks-large.png#lightbox)

當您按下超連結時[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) ，會執行其`ICommand` [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性所定義的來回應。 此外， [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性所指定的 URL 將會傳遞至`ICommand`做為參數。

XAML 頁面的程式碼後置包含`TapCommand`執行：

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

會`TapCommand`執行`Launcher.OpenAsync`方法，並傳遞[`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性值做為參數。 `Launcher.OpenAsync`方法是由 Xamarin 提供，並在網頁瀏覽器中開啟 URL。 因此，整體效果是在頁面上按下超連結時，網頁瀏覽器會出現，且會導覽與超連結相關聯的 URL。

### <a name="creating-a-reusable-hyperlink-class"></a>建立可重複使用的超連結類別

先前建立超連結的方法需要在您的應用程式中要求超連結時，撰寫重複的程式碼。 不過， [`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)類別都可以子類別化，以`HyperlinkLabel`建立`HyperlinkSpan`和類別，並在該處加入手勢辨識器和文字格式設定程式碼。

下列程式碼範例取自「[超連結示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)」範例，其中顯示`HyperlinkSpan`一個類別：

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

類別會定義`Url`屬性和相關聯[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)的，而此函式會設定超連結的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)外觀，以及在按下超連結時回應的。 `HyperlinkSpan` `HyperlinkSpan`當按下時， `TapGestureRecognizer`會執行`Launcher.OpenAsync`方法以在網頁瀏覽器中開啟由`Url`屬性所指定的 URL 來回應。

藉`HyperlinkSpan`由將類別的實例加入至 XAML，即可取用類別：

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

前面幾節涵蓋了[`Label`](xref:Xamarin.Forms.Label)每[`Span`](xref:Xamarin.Forms.Span)個實例的設定和屬性。 不過，您可以將屬性集分成一種樣式，以一致的方式套用至一或多個視圖。 這會增加程式碼的可讀性，並讓設計變得更容易執行。 如需詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相關連結

- [文字（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [超連結（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [使用 Xamarin 建立 Mobile Apps，第3章](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Label API](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)
