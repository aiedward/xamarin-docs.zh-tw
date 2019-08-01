---
title: Xamarin.Forms 標籤
description: 這篇文章說明如何使用 Xamarin.Forms 標籤類別在應用程式中會顯示單一和多行文字。
ms.prod: xamarin
ms.assetid: 02E6C553-5670-49A0-8EE9-5153ED21EA91
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 5c12b5a5c0e558403362bd12baddfb8201f40759
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642931"
---
# <a name="xamarinforms-label"></a>Xamarin.Forms 標籤

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_在 Xamarin.Forms 中的顯示文字_

[ `Label` ](xref:Xamarin.Forms.Label)檢視用於顯示文字、 單一和多行。 標籤可以文字裝飾，彩色文字，並使用自訂的字型 （家族、 大小和選項）。

## <a name="text-decorations"></a>文字裝飾

可套用至底線和刪除線文字裝飾[ `Label` ](xref:Xamarin.Forms.Label)藉由設定執行個體`Label.TextDecorations`一或多個屬性`TextDecorations`列舉成員：

- `None`
- `Underline`
- `Strikethrough`

下列 XAML 範例示範如何設定`Label.TextDecorations`屬性：

```xaml
<Label Text="This is underlined text." TextDecorations="Underline"  />
<Label Text="This is text with strikethrough." TextDecorations="Strikethrough" />
<Label Text="This is underlined text with strikethrough." TextDecorations="Underline, Strikethrough" />
```

對等的 C# 程式碼是：

```csharp
var underlineLabel = new Label { Text = "This is underlined text.", TextDecorations = TextDecorations.Underline };
var strikethroughLabel = new Label { Text = "This is text with strikethrough.", TextDecorations = TextDecorations.Strikethrough };
var bothLabel = new Label { Text = "This is underlined text with strikethrough.", TextDecorations = TextDecorations.Underline | TextDecorations.Strikethrough };
```

下列螢幕擷取畫面所示`TextDecorations`套用至列舉成員[ `Label` ](xref:Xamarin.Forms.Label)執行個體：

![](label-images/label-textdecorations.png "使用文字裝飾的標籤")

> [!NOTE]
> 文字裝飾也套用至[ `Span` ](xref:Xamarin.Forms.Span)執行個體。 如需詳細資訊`Span`類別，請參閱[格式化的文字](#Formatted_Text)。

## <a name="colors"></a>色彩

標籤可以設定為使用自訂的文字色彩，透過可繫結[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)屬性。

特別是為了確保會在每個平台上的可用色彩。 因為每個平台都有不同的預設值的文字和背景色彩，您必須謹慎挑選每個的運作方式的預設值。

下列 XAML 範例設定的文字色彩`Label`:

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

對等的 C# 程式碼是：

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

下列螢幕擷取畫面顯示的設定結果`TextColor`屬性：

![](label-images/textcolor.png "標籤 TextColor 範例")

如需有關色彩的詳細資訊，請參閱[色彩](~/xamarin-forms/user-interface/colors.md)。

## <a name="fonts"></a>字型

如需有關指定字型`Label`，請參閱 <<c2> [ 字型](~/xamarin-forms/user-interface/text/fonts.md)。

<a name="Truncation_and_Wrapping" />

## <a name="truncation-and-wrapping"></a>截斷和換行

標籤可以設定來處理無法在同一行中所公開的數種方式的其中一個符合的文字`LineBreakMode`屬性。 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 是列舉型別使用下列值：

- **HeadTruncation** &ndash;截斷的文字，顯示結尾標頭。
- **CharacterWrap** &ndash;到新的一行上的文字換行字元界限處。
- **MiddleTruncation** &ndash;顯示的開頭和結尾的文字，以省略符號中間取代。
- **NoWrap** &ndash;不換行文字，只顯示可以盡可能文字放在一行。
- **TailTruncation** &ndash;顯示文字，截斷結尾的開頭。
- **WordWrap** &ndash;包裝在字邊界的文字。

## <a name="displaying-a-specific-number-of-lines"></a>顯示特定的行數

所顯示的行數[ `Label` ](xref:Xamarin.Forms.Label)可以設定來指定`Label.MaxLines`屬性設`int`值：

- 當`MaxLines`為 0，`Label`的值會遵守[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)屬性，以顯示一個列，可能被截斷或使用的所有文字的所有行。
- 當`MaxLines`為 1，結果等同於設定[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)屬性設[ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode)， [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode)， [`MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode)，或[ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode)。 不過，`Label`會採用的值[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)有關放置的省略符號，如果適用的屬性。
- 當`MaxLines`大於 1，`Label`會顯示最多指定數目的行數，同時又能採用的值[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)有關放置的省略符號，如果適用的屬性。 不過，設定`MaxLines`屬性設為大於 1 則沒有任何作用，如果值[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)屬性設定為[ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode)。

下列 XAML 範例示範如何設定`MaxLines`上的屬性[ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       MaxLines="2" />
```

對等的 C# 程式碼是：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  MaxLines = 2
};
```

下列螢幕擷取畫面顯示的設定結果`MaxLines`為 2，當文字夠長佔用超過 2 行的屬性：

![](label-images/label-maxlines.png "標籤 MaxLines 範例")

<a name="Formatted_Text" />

## <a name="formatted-text"></a>格式化文字

標籤會[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)公開屬性, 以允許在相同的視圖中呈現具有多個字型和色彩的文字。

`FormattedText`屬性的類型是[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)，其中包括一或多個[ `Span` ](xref:Xamarin.Forms.Span)情況下，透過設定[ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans)屬性. 下列`Span`屬性可以用來設定視覺外觀：

- [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) -s p a n 背景的色彩。
- [`Font`](xref:Xamarin.Forms.Span.Font) – 範圍中文字的字型。
- [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) – 範圍中文字的字型屬性。
- [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) – 所屬範圍中文字的字型的字型系列。
- [`FontSize`](xref:Xamarin.Forms.Span.FontSize) – 範圍中文字的字型的大小。
- [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) – 範圍中文字的色彩。 這個屬性已經過時，已被取代`TextColor`屬性。
- [`LineHeight`](xref:Xamarin.Forms.Span.LineHeight) -要套用至範圍的預設線條高度的乘數。 如需詳細資訊，請參閱 <<c0> [ 行高](#line-height)。
- [`Style`](xref:Xamarin.Forms.Span.Style)–要套用至範圍的樣式。
- [`Text`](xref:Xamarin.Forms.Span.Text) – 範圍的文字。
- [`TextColor`](xref:Xamarin.Forms.Span.TextColor) – 範圍中文字的色彩。
- `TextDecorations` -要套用至範圍中文字的裝飾。 如需詳細資訊，請參閱 <<c0> [ 文字裝飾](#text-decorations)。

> [!NOTE]
> [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) [`OneWay`](xref:Xamarin.Forms.BindingMode)、[和可系結屬性的預設系結模式為。`Text`](xref:Xamarin.Forms.Span.Text) [`Text`](xref:Xamarin.Forms.Span.Text) 如需此系結模式的詳細資訊, 請參閱系結[模式](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md)指南中[的預設](~/xamarin-forms/app-fundamentals/data-binding/binding-mode.md#the-default-binding-mode)系結模式。

颾魤 ㄛ [ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers)屬性可以用來定義的筆勢辨識器所將回應筆勢在集合[ `Span` ](xref:Xamarin.Forms.Span)。

下列 XAML 範例將示範`FormattedText`屬性，葀佹[ `Span` ](xref:Xamarin.Forms.Span)執行個體：

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
> [ `Text` ](xref:Xamarin.Forms.Span.Text)屬性`Span`可以透過資料繫結設定。 如需詳細資訊，請參閱 <<c0> [ 資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)。

請注意， [ `Span` ](xref:Xamarin.Forms.Span)也可以回應的範圍會加入任何筆勢[ `GestureRecognizers` ](xref:Xamarin.Forms.GestureElement.GestureRecognizers)集合。 例如， [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)已加入第二個`Span`在上述範例中，程式碼。 因此，當這`Span`點選`TapGestureRecognizer`會藉由執行回應`ICommand`所定義[ `Command` ](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性。 如需筆勢辨識器的詳細資訊，請參閱[Xamarin.Forms 筆勢](~/xamarin-forms/app-fundamentals/gestures/index.md)。

下列螢幕擷取畫面顯示的設定結果`FormattedString`三個屬性`Span`執行個體：

![](label-images/formattedtext.png "標籤 FormattedText 範例")

## <a name="line-height"></a>行高

垂直高度[ `Label` ](xref:Xamarin.Forms.Label)並[ `Span` ](xref:Xamarin.Forms.Span)可以藉由設定自訂[ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)屬性或[ `Span.LineHeight`](xref:Xamarin.Forms.Span.LineHeight)至`double`值。 IOS 和 Android 上這些值會是原始的列高度，以及在通用 Windows 平台 (UWP) 中的乘數`Label.LineHeight`屬性值是標籤字型大小的乘數。

> [!NOTE]
> - 在 iOS 上， [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)並[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)屬性變更線條的高度所容納的單一行中的文字和換行至多行的文字。
> - 在 Android 上， [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)並[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)屬性只變更線條的高度換行至多行的文字。
> - 在 UWP [ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)屬性變更一行的高度的文字換行至多行，而[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)屬性沒有任何作用。

下列 XAML 範例示範如何設定[ `LineHeight` ](xref:Xamarin.Forms.Label.LineHeight)上的屬性[ `Label` ](xref:Xamarin.Forms.Label):

```xaml
<Label Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus."
       LineBreakMode="WordWrap"
       LineHeight="1.8" />
```

對等的 C# 程式碼是：

```csharp
var label =
{
  Text = "Lorem ipsum dolor sit amet, consectetur adipiscing elit. In facilisis nulla eu felis fringilla vulputate. Nullam porta eleifend lacinia. Donec at iaculis tellus.", LineBreakMode = LineBreakMode.WordWrap,
  LineHeight = 1.8
};
```

下列螢幕擷取畫面顯示的設定結果[ `Label.LineHeight` ](xref:Xamarin.Forms.Label.LineHeight) 1.8 的屬性：

![](label-images/label-lineheight.png "標籤 LineHeight 範例")

下列 XAML 範例示範如何設定[ `LineHeight` ](xref:Xamarin.Forms.Span.LineHeight)上的屬性[ `Span` ](xref:Xamarin.Forms.Span):

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

對等的 C# 程式碼是：

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

下列螢幕擷取畫面顯示的設定結果[ `Span.LineHeight` ](xref:Xamarin.Forms.Span.LineHeight) 1.8 的屬性：

![](label-images/span-lineheight.png "Span LineHeight 範例")

## <a name="hyperlinks"></a>指向

[`Label`](xref:Xamarin.Forms.Label) [和`Span`](xref:Xamarin.Forms.Span)實例所顯示的文字, 可以使用下列方法轉換成超連結:

1. [`Label`](xref:Xamarin.Forms.Label)設定或`TextColor` `TextDecoration`的和屬性。 [`Span`](xref:Xamarin.Forms.Span)
1. [`Label`](xref:Xamarin.Forms.Label) [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)將加入[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) `ICommand`至或[`GestureRecognizers`](xref:Xamarin.Forms.GestureElement.GestureRecognizers) [`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)的集合, 其屬性系結至, 而其屬性包含要開啟的 URL。 [`Span`](xref:Xamarin.Forms.Span)
1. 定義將由執行的[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) `ICommand` 。
1. 撰寫將由`ICommand`執行的程式碼。

下列取自[超連結](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)示範範例的程式碼範例會顯示[`Label`](xref:Xamarin.Forms.Label) , 其內容是從多個[`Span`](xref:Xamarin.Forms.Span)實例所設定:

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

在此範例中, 第一個和[`Span`](xref:Xamarin.Forms.Span)第三個實例會包含文字`Span` , 第二個則代表 tappable 超連結。 其文字色彩設為藍色, 且具有底線文字裝飾。 這會建立超連結的外觀, 如下列螢幕擷取畫面所示:

[![超連結](label-images/hyperlinks-small.png "超連結")](label-images/hyperlinks-large.png#lightbox)

當您按下超連結時[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) , 會執行其[`Command`](xref:Xamarin.Forms.TapGestureRecognizer.Command)屬性`ICommand`所定義的來回應。 此外, [`CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)屬性所指定的 URL 將會傳遞`ICommand`至做為參數。

XAML 頁面的程式碼後置包含`TapCommand`執行:

```csharp
public partial class MainPage : ContentPage
{
    public ICommand TapCommand => new Command<string>(OpenBrowser);

    public MainPage()
    {
        InitializeComponent();
        BindingContext = this;
    }

    void OpenBrowser(string url)
    {
        Device.OpenUri(new Uri(url));
    }
}
```

會`TapCommand` [`TapGestureRecognizer.CommandParameter`](xref:Xamarin.Forms.TapGestureRecognizer.CommandParameter)執行方法, 並傳遞屬性值做為參數。 `OpenBrowser` 接著, 這個方法會呼叫[`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*)方法, 以在網頁瀏覽器中開啟 URL。 因此, 整體效果是在頁面上按下超連結時, 網頁瀏覽器會出現, 且會導覽與超連結相關聯的 URL。

### <a name="creating-a-reusable-hyperlink-class"></a>建立可重複使用的超連結類別

先前建立超連結的方法需要在您的應用程式中要求超連結時, 撰寫重複的程式碼。 不過, [`Label`](xref:Xamarin.Forms.Label)和[`Span`](xref:Xamarin.Forms.Span)類別都可以子類別化, 以`HyperlinkLabel`建立`HyperlinkSpan`和類別, 並在該處加入手勢辨識器和文字格式設定程式碼。

下列程式碼範例取自「[超連結示範](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks/)」範例, 其中顯示`HyperlinkSpan`一個類別:

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
            Command = new Command(() => Device.OpenUri(new Uri(Url)))
        });
    }
}
```

類別會[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)定義屬性和相關聯的, 而[`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)此函式會設定超連結的外觀, 以及在按下超連結時回應的。 `Url` `HyperlinkSpan` 當按[`Device.OpenUri`](xref:Xamarin.Forms.Device.OpenUri*) `Url`下時, 會執行方法以在網頁瀏覽器中開啟由屬性所指定的 URL 來回應。 `TapGestureRecognizer` `HyperlinkSpan`

藉由將類別的實例加入至 XAML, 即可取用類別:`HyperlinkSpan`

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

前幾節涵蓋設定[ `Label` ](xref:Xamarin.Forms.Label)並[ `Span` ](xref:Xamarin.Forms.Span)以每個執行個體為基礎的屬性。 不過，屬性的集合可以分組為會一致地套用到一或多個檢視的一種樣式。 這可以提高可讀性的程式碼，並讓您更輕鬆地實作設計變更。 如需詳細資訊，請參閱 <<c0> [ 樣式](~/xamarin-forms/user-interface/text/styles.md)。

## <a name="related-links"></a>相關連結

- [文字 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [超連結 (範例)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-hyperlinks)
- [使用 Xamarin.Forms 時，第 3 章中建立行動應用程式](https://developer.xamarin.com/r/xamarin-forms/book/chapter03.pdf)
- [標籤 API](xref:Xamarin.Forms.Label)
- [API 範圍](xref:Xamarin.Forms.Span)
