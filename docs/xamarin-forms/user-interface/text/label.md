---
title: Xamarin. 表單標籤
description: 本文說明如何使用 [Xamarin] 標籤類別，在應用程式中顯示單一和多行文字。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/28/2019
ms.openlocfilehash: 64fa15a15468a84ada3a377a9ac85bbf6310099c
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490034"
---
# <a name="xamarinforms-label"></a>Xamarin. 表單標籤

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_在 [Xamarin] 表單中顯示文字_

[ [`Label`](xref:Xamarin.Forms.Label) ] 視圖用來顯示文字，包括單一和多行。 標籤可以具有文字裝飾、彩色文字，並使用自訂字型（系列、大小和選項）。

## <a name="text-decorations"></a>文字裝飾

將 `Label.TextDecorations` 屬性設定為一個或多個 `TextDecorations` 列舉成員，即可將底線和刪除線文字裝飾套用至[`Label`](xref:Xamarin.Forms.Label)實例：

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

下列螢幕擷取畫面顯示套用至[`Label`](xref:Xamarin.Forms.Label)實例的 `TextDecorations` 列舉成員：

![具有文字裝飾的標籤](label-images/label-textdecorations.png)

> [!NOTE]
> 文字裝飾也可以套用至[`Span`](xref:Xamarin.Forms.Span)實例。 如需 `Span` 類別的詳細資訊，請參閱[格式化文字](#Formatted_Text)。

## <a name="character-spacing"></a>字元間距

將 [`Label.CharacterSpacing`] 屬性設定為 `double` 值，即可將字元間距套用至[`Label`](xref:Xamarin.Forms.Label)實例：

```xaml
<Label Text="Character spaced text"
       CharacterSpacing="10" />
```

對等的 C# 程式碼為：

```csharp
Label label = new Label { Text = "Character spaced text", CharacterSpacing = 10 };
```

結果是[`Label`](xref:Xamarin.Forms.Label)所顯示文字中的字元間距，`CharacterSpacing` 與裝置無關的單位分開。

## <a name="colors"></a>色彩

標籤可以透過可系結的[`TextColor`](xref:Xamarin.Forms.Label.TextColor)屬性設定為使用自訂文字色彩。

必須特別小心，以確保每個平臺都能使用色彩。 由於每個平臺的文字和背景色彩都有不同的預設值，因此您必須小心挑選預設值，以在每個平臺上運作。

下列 XAML 範例會設定 `Label`的文字色彩：

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

下列螢幕擷取畫面顯示設定 `TextColor` 屬性的結果：

![標籤 TextColor 範例](label-images/textcolor.png)

如需色彩的詳細資訊，請參閱[色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字型

如需在 `Label`上指定字型的詳細資訊，請參閱[字型。](~/xamarin-forms/user-interface/text/fonts.md)

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和換行

標籤可以設定為以數種方式的其中一行來處理無法容納的文字，由 `LineBreakMode` 屬性公開。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)是具有下列值的列舉：

- **HeadTruncation** &ndash; 會截斷文字的標頭，並顯示結尾。
- **CharacterWrap** &ndash; 將文字換行到字元界限的新一行。
- **MiddleTruncation** &ndash; 顯示文字的開頭和結尾，中間以省略號取代。
- **NoWrap** &ndash; 不會將文字換行，只顯示一行中的最多文字。
- **TailTruncation** &ndash; 會顯示文字的開頭，並截斷結尾。
- **自動換行**&ndash; 將文字包裝在文字界限。

## <a name="display-a-specific-number-of-lines"></a>顯示特定數目的行

藉由將 `Label.MaxLines` 屬性設定為 `int` 值，即可指定[`Label`](xref:Xamarin.Forms.Label)所顯示的行數：

- 當 `MaxLines` 為-1 （其為預設值）時，`Label` 會遵循[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性的值，只顯示一行、可能被截斷，或包含所有文字的所有行。
- 當 `MaxLines` 為0時，不會顯示 `Label`。
- 當 `MaxLines` 為1時，其結果等同于將[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性設定為[`NoWrap`](xref:Xamarin.Forms.LineBreakMode)、 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode)、 [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode)或[`TailTruncation`](xref:Xamarin.Forms.LineBreakMode)。 不過，`Label` 將會遵守[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性的值（如果適用的話）。
- 當 `MaxLines` 大於1時，`Label` 會顯示到指定的行數，同時採用與省略號位置相關的[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性值（如果適用的話）。 不過，如果[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)屬性設定為[`NoWrap`](xref:Xamarin.Forms.LineBreakMode)，將 `MaxLines` 屬性設定為大於1的值就不會有任何作用。

下列 XAML 範例將示範如何在[`Label`](xref:Xamarin.Forms.Label)上設定 `MaxLines` 屬性：

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

下列螢幕擷取畫面顯示將 [`MaxLines`] 屬性設定為 [2] 的結果（當文字長度足以佔用2行以上）時：

![標籤 MaxLines 範例](label-images/label-maxlines.png)

## <a name="display-html"></a>顯示 HTML

[`Label`](xref:Xamarin.Forms.Label)類別具有 `TextType` 屬性，可決定 `Label` 實例是否應顯示純文字或 HTML 文字。 這個屬性應該設定為 `TextType` 列舉的其中一個成員：

- `Text` 表示 `Label` 會顯示純文字，而則是 `Label.TextType` 屬性的預設值。
- `Html` 表示 `Label` 會顯示 HTML 文字。

因此， [`Label`](xref:Xamarin.Forms.Label)實例可以將 `Label.TextType` 屬性設為 `Html`，並將 `Label.Text` 屬性設定為 html 字串，藉以顯示 html：

```csharp
Label label = new Label
{
    Text = "This is <strong style=\"color:red\">HTML</strong> text.",
    TextType = TextType.Html
};
```

在上述範例中，HTML 中的雙引號字元必須使用 `\` 符號進行轉義。

在 XAML 中，HTML 字串可能會因為額外的 `<` 和 `>` 符號的轉義而變成無法讀取：

```xaml
<Label Text="This is &lt;strong style=&quot;color:red&quot;&gt;HTML&lt;/strong&gt; text."
       TextType="Html"  />
```

或者，若要提高可讀性，可以在 `CDATA` 區段中內嵌 HTML：

```xaml
<Label TextType="Html">
    <![CDATA[
    This is <strong style="color:red">HTML</strong> text.
    ]]>
</Label>
```

在此範例中，`Label.Text` 屬性會設定為在 `CDATA` 區段中內嵌的 HTML 字串。 這是可行的，因為 `Text` 屬性是 `Label` 類別的 `ContentProperty`。

下列螢幕擷取畫面顯示顯示 HTML 的[`Label`](xref:Xamarin.Forms.Label) ：

![在 iOS 和 Android 上顯示 HTML 的標籤螢幕擷取畫面](label-images/label-html.png)

> [!IMPORTANT]
> 在[`Label`](xref:Xamarin.Forms.Label)中顯示 html 僅限於基礎平臺支援的 html 標記。

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化文字

標籤會公開[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)屬性，允許在相同的視圖中呈現具有多個字型和色彩的文字。

`FormattedText` 屬性是[`FormattedString`](xref:Xamarin.Forms.FormattedString)類型，其中包含一個或多個[`Span`](xref:Xamarin.Forms.Span)實例，透過[`Spans`](xref:Xamarin.Forms.FormattedString.Spans)屬性來設定。 下列 `Span` 屬性可以用來設定視覺外觀：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) –範圍背景的色彩。
- `CharacterSpacing`，屬於 `double`類型，這是 `Span` 文字字元之間的間距。
- [`Font`](xref:Xamarin.Forms.Span.Font) –範圍中文字的字型。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) –範圍中文字的字型屬性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) –範圍中文字的字型所屬的字型系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) –範圍中文字的字型大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) –範圍中文字的色彩。 這個屬性已經過時，並已由 `TextColor` 屬性取代。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -要套用至範圍的預設行高的乘數。 如需詳細資訊，請參閱[行高](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style) –要套用至範圍的樣式。
- [`Text`](xref:Xamarin.Forms.Span.Text) –範圍的文字。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) –範圍中文字的色彩。
- `TextDecorations`-要套用至範圍中文字的裝飾。 如需詳細資訊，請參閱[文字裝飾](#text-decorations)。

[ [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)]、[ [`Text`](xref:Xamarin.Forms.Span.Text)] 和 [ [`Text`](xref:Xamarin.Forms.Span.Text)可系結] 屬性的預設系結模式為 [ [`OneWay`](xref:Xamarin.Forms.BindingMode)]。 如需此系結模式的詳細資訊，請參閱系結[模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中[的預設](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)系結模式。

此外， [`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)屬性可以用來定義筆勢辨識器的集合，這些辨識器會回應[`Span`](xref:Xamarin.Forms.Span)上的手勢。

> [!NOTE]
> 不可能在[`Span`](xref:Xamarin.Forms.Span)中顯示 HTML。

下列 XAML 範例示範由三個[`Span`](xref:Xamarin.Forms.Span)實例所組成的 `FormattedText` 屬性：

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
> 您可以透過資料系結來設定 `Span` 的[`Text`](xref:Xamarin.Forms.Span.Text)屬性。 如需詳細資訊，請參閱[資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

請注意， [`Span`](xref:Xamarin.Forms.Span)也可以回應加入至範圍[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)集合中的任何手勢。 例如， [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)已加入至上述程式碼範例中的第二個 `Span`。 因此，當此 `Span` 被按下時，`TapGestureRecognizer` 會藉由執行[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性所定義的 `ICommand` 來回應。 如需筆勢辨識器的詳細資訊，請參閱「 [Xamarin」手勢](~/xamarin-forms/app-fundamentals/gestures/index.md)。

下列螢幕擷取畫面顯示將 [`FormattedString`] 屬性設定為三個 `Span` 實例的結果：

![標籤 FormattedText 範例](label-images/formattedtext.png)

## <a name="line-height"></a>行高

您可以將[`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性或[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)設定為 `double` 值，以自訂[`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)的垂直高度。 在 iOS 和 Android 上，這些值是原始行高的乘數，而在通用 Windows 平臺（UWP）上，`Label.LineHeight` 屬性值是標籤字型大小的乘數。

> [!NOTE]
>
> - 在 iOS 上，[ [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) ] 和 [ [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) ] 屬性會變更符合單一線條的文字行高，以及換行到多行的文字。
> - 在 Android 上，[ [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight) ] 和 [ [`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight) ] 屬性只會變更包裝在多行上之文字的線條高度。
> - 在 UWP 上， [`Label.LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性會變更包裝到多行的文字行高，而[`Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)屬性則沒有任何作用。

下列 XAML 範例將示範如何在[`Label`](xref:Xamarin.Forms.Label)上設定[`LineHeight`](xref:Xamarin.Forms.Label.LineHeight)屬性：

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

下列 XAML 範例將示範如何在[`Span`](xref:Xamarin.Forms.Span)上設定[`LineHeight`](xref:Xamarin.Forms.Span.LineHeight)屬性：

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

## <a name="padding"></a>與邊框距離

填補代表專案和其子專案之間的間距，並用來分隔專案與本身的內容。 將 `Label.Padding` 屬性設定為[`Thickness`](xref:Xamarin.Forms.Thickness)值，即可將填補套用至[`Label`](xref:Xamarin.Forms.Label)實例：

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
> 在 iOS 上，當建立設定 `Padding` 屬性的[`Label`](xref:Xamarin.Forms.Label)時，將會套用填補，並于稍後更新填補值。 不過，建立未設定 `Padding` 屬性的 `Label` 時，稍後嘗試設定它將不會有任何作用。
>
> 在 Android 和通用 Windows 平臺上，您可以在建立 `Label` 時或稍後指定 `Padding` 屬性值。

如需填補的詳細資訊，請參閱[邊界和填補](~/xamarin-forms/user-interface/layouts/margin-and-padding.md)。

## <a name="hyperlinks"></a>超連結

[`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)實例所顯示的文字，可以使用下列方法轉換成超連結：

1. 設定[`Label`](xref:Xamarin.Forms.Label)或[`Span`](xref:Xamarin.Forms.Span)的 `TextColor` 和 `TextDecoration` 屬性。
1. 將[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)新增至[`Label`](xref:Xamarin.Forms.Label)或[`Span`](xref:Xamarin.Forms.Span)的[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers)集合，其[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性會系結至 `ICommand`，而且其[`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性包含要開啟的 URL。
1. 定義[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)將執行的 `ICommand`。
1. 撰寫 `ICommand`將執行的程式碼。

下列取自[超連結](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)示範範例的程式碼範例，會顯示從多個[`Span`](xref:Xamarin.Forms.Span)實例設定內容的[`Label`](xref:Xamarin.Forms.Label) ：

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

在此範例中，第一個和第三個[`Span`](xref:Xamarin.Forms.Span)實例會包含文字，而第二個 `Span` 則代表 tappable 超連結。 其文字色彩設為藍色，且具有底線文字裝飾。 這會建立超連結的外觀，如下列螢幕擷取畫面所示：

[![指向](label-images/hyperlinks-small.png "超連結")](label-images/hyperlinks-large.png#lightbox)

當按下超連結時， [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)會藉由執行其[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性所定義的 `ICommand` 來回應。 此外， [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性所指定的 URL 將會以參數的形式傳遞至 `ICommand`。

XAML 頁面的程式碼後置包含 `TapCommand` 的執行：

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

`TapCommand` 會執行 `Launcher.OpenAsync` 方法，並傳遞[`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性值做為參數。 `Launcher.OpenAsync` 方法是由 Xamarin 提供，並會在網頁瀏覽器中開啟 URL。 因此，整體效果是在頁面上按下超連結時，網頁瀏覽器會出現，且會導覽與超連結相關聯的 URL。

### <a name="creating-a-reusable-hyperlink-class"></a>建立可重複使用的超連結類別

先前建立超連結的方法需要在您的應用程式中要求超連結時，撰寫重複的程式碼。 不過， [`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)類別都可以子類別化，以建立 `HyperlinkLabel` 和 `HyperlinkSpan` 類別，並在該處加入手勢辨識器和文字格式設定程式碼。

下列取自[超連結](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)示範範例的程式碼範例會顯示 `HyperlinkSpan` 類別：

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

`HyperlinkSpan` 類別會定義 `Url` 屬性和相關聯的[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)，而此函式會設定超連結的外觀，以及在按下超連結時回應的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 。 當您按下 `HyperlinkSpan` 時，`TapGestureRecognizer` 會在網頁瀏覽器中執行 `Launcher.OpenAsync` 方法來開啟 `Url` 屬性所指定的 URL，藉以回應。

藉由將類別的實例加入至 XAML，可以取用 `HyperlinkSpan` 類別：

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

先前章節會針對每個實例來設定[`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)屬性。 不過，您可以將屬性集分成一種樣式，以一致的方式套用至一或多個視圖。 這會增加程式碼的可讀性，並讓設計變得更容易執行。 如需詳細資訊，請參閱[樣式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相關連結

- [文字 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [超連結（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [使用 Xamarin 建立 Mobile Apps，第3章](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [Label API](xref:Xamarin.Forms.Label)
- [Span API](xref:Xamarin.Forms.Span)
