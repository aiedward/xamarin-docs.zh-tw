---
title: 第3章的摘要。 更深入的文字
description: 使用 Xamarin 建立 Mobile Apps：第3章的摘要。 更深入的文字
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "72980927"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第3章的摘要。 更深入的文字

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

本章將深入探討[`Label`](xref:Xamarin.Forms.Label)的觀點，包括色彩、字型和格式。

## <a name="wrapping-paragraphs"></a>包裝段落

當 `Label` 的[`Text`](xref:Xamarin.Forms.Label.Text)屬性包含長文字時，`Label` 會自動將它包裝到多行，如[**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)範例所示。 您可以內嵌 Unicode 程式碼（例如 ' \u2014 '）做為 em 虛線， C#或使用 ' \r ' 等字元來中斷換行。

當 `Label` 的 [ [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) ] 和 [ [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) ] 屬性設定為 [`LayoutOptions.Fill`] 時，`Label` 的整體大小會受到其容器所提供的空間所控制。 `Label` 被視為*受條件約束*。 `Label` 的大小是其容器的大小。

當 [`HorizontalOptions`] 和 [`VerticalOptions`] 屬性設定為 [`LayoutOptions.Fill`] 以外的值時，`Label` 的大小會受到轉譯文字所需的空間所約束，最大至其容器可供 `Label`使用的大小。 `Label` 稱為「不受*限制*」，而且它會決定其本身的大小。

（注意：受條件*約束*和無*限制*的詞彙可能會以直覺方式呈現，因為無限制的視圖通常小於限制的視圖。 此外，本書的早期章節也不會一致地使用這些詞彙）。

如 `Label` 的視圖可以在一個維度中受到限制，而不受條件約束。 `Label` 只會在水準限制時，將文字換成多行。

如果 `Label` 受到限制，可能會佔用比文字所需更多的空間。 文字可以放在 `Label`的整體區域中。 將 [ [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) ] 屬性設為[`TextAlignment`](xref:Xamarin.Forms.TextAlignment)列舉的成員（[`Start`](xref:Xamarin.Forms.TextAlignment.Start)、 [`Center`](xref:Xamarin.Forms.TextAlignment.Center)或[`End`](xref:Xamarin.Forms.TextAlignment.Center)），以控制段落所有線條的對齊方式。 預設值為 `Start`，並將文字靠左對齊。

將 [ [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) ] 屬性設定為 [`TextAlignment` 列舉] 的成員，將文字放在 `Label`所佔用區域的頂端、中央或下方。

將 [ [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) ] 屬性設為[`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)列舉的成員（[`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap)、 [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)、 [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap)、 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation)、 [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)或[`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)），以控制段落中的多行如何中斷或被截斷。

## <a name="text-and-background-colors"></a>文字和背景色彩

將 `Label` 的 [ [`TextColor`](xref:Xamarin.Forms.Label.TextColor) ] 和 [ [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性] 設定為 [ [`Color`](xref:Xamarin.Forms.Color)值]，以控制文字和背景的色彩。

`BackgroundColor` 適用于 `Label`所佔用之整個區域的背景。 視 `HorizontalOptions` 和 `VerticalOptions` 屬性而定，該大小可能會大幅大於顯示文字所需的區域。 您可以使用色彩來試驗 `HorizontalOptions`、`VerticalOptions`、`HorizontalExeAlignment`和 `VerticalTextAlignment` 的各種值，以查看它們如何影響 `Label`的大小和位置，以及 `Label`內文字的大小和位置。

## <a name="the-color-structure"></a>色彩結構

[`Color`](xref:Xamarin.Forms.Color)結構可讓您將色彩指定為紅色-綠-藍（RGB）值，或色調-飽和度-亮度（HSL）值或色彩名稱。 Alpha 色板也可以用來表示透明度。

使用 `Color` 的函式來指定：

- [灰色網底](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [具有透明度的 RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

引數是介於0到1的 `double` 值。

您也可以使用數個靜態方法來建立 `Color` 值：

- 從0到 1 `double` RGB 值的[`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))
- 從0到255的整數 RGB 值[`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))
- 具有透明度 `double` RGB 值的[`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))
- 具有透明度的整數 RGB 值[`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))
- 使用透明度 `double` HSL 值的[`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))
- `uint` 值的[`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) ，計算方式為（B + 256 \* （G + 256 \* （R + 256 \* a）））
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) ，格式為 "#AARRGGBB" 或 "#RRGGBB" 或 "#ARGB" 或 "#RGB" 的十六進位數位 `string` 格式，其中每個字母都對應到 Alpha、紅色、綠色和藍色聲道的十六進位數位。 這個方法主要用於 XAML 色彩轉換，如 < [xaml 與程式碼第7章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)所述。

一旦建立之後，`Color` 值就不變。 您可以從下列屬性取得色彩的特性：

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

這些全都是介於0到1之間的 `double` 值。

`Color` 也會定義適用于通用色彩的240公用靜態唯讀欄位。 撰寫本書時，只有17個常見的色彩可用。

另一個公用靜態唯讀欄位定義了所有顏色通道設為零的色彩：

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

有數個實例方法允許修改現有的色彩，以建立新的色彩：

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

最後，兩個靜態唯讀屬性會定義特殊的色彩值：

- [`Color.Default`](xref:Xamarin.Forms.Color.Default)，所有通道都會設定為 &ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` 的目的是要強制執行平臺的色彩配置，因此在不同平臺的不同內容中會有不同的意義。 根據預設，平臺色彩配置為：

- iOS：淺色背景的深色文字
- Android：深色背景上的淺文字（書籍中）或淺背景的深色文字（在範例程式碼存放庫的**主要**分支中透過 AppCompat 的材質設計）
- UWP：淺背景的深色文字

`Color.Accent` 值會產生可在深色或淺色背景看到的平臺特定（且有時是使用者可選取）色彩。

## <a name="changing-the-application-color-scheme"></a>變更應用程式色彩配置

各種平臺都有預設的色彩配置，如上表所示。

以 Android 為目標時，可以藉由在 Android. Manifest .xml 檔案中指定淺色主題，或藉由[新增 AppCompat 和材質設計](~/xamarin-forms/platform/android/appcompat-material-design.md)，切換為深色亮度配置。

對於 Windows 平臺，使用者通常會選取色彩主題，但是您可以在平臺的 app.xaml 檔案中，將 `RequestedTheme` 屬性設定為 `Light` 或 `Dark`。 根據預設，UWP 專案中的 app.xaml 檔案包含設定為 `Light`的 `RequestedTheme` 屬性。

## <a name="font-sizes-and-attributes"></a>字型大小和屬性

將 `Label` 的[`FontFamily`](xref:Xamarin.Forms.Label.FontFamily)屬性設定為字串（例如「Times Roman」）以選取字型系列。 不過，您必須指定特定平臺上支援的字型系列，而平臺在這方面並不一致。

將 `Label` 的[`FontSize`](xref:Xamarin.Forms.Label.FontSize)屬性設定為 `double`，以指定字型的近似高度。 如需以智慧方式選擇字型大小的詳細資訊，請參閱[第5章，處理大小](chapter05.md)。

或者，您也可以取得數個預設平臺相關字型大小的其中一種。 靜態[`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type)) [方法和多](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element))載都會根據[`NamedSize`](xref:Xamarin.Forms.NamedSize)列舉的成員（[`Default`](xref:Xamarin.Forms.NamedSize.Default)、 [`Micro`](xref:Xamarin.Forms.NamedSize.Micro)、 [`Small`](xref:Xamarin.Forms.NamedSize.Small)、 [`Medium`](xref:Xamarin.Forms.NamedSize.Medium)和[`Large`](xref:Xamarin.Forms.NamedSize.Large)）傳回適用于平臺的 `double` 字型大小值。 從 `Medium` 成員傳回的值，與 `Default`不一定相同。 [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)範例會顯示具有這些已命名大小的文字。

將 `Label` 的[`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes)屬性設定為這些[`FontAttributes`](xref:Xamarin.Forms.FontAttributes)列舉、 [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)、 [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)或[`None`](xref:Xamarin.Forms.FontAttributes.None)的成員。 您可以使用C#位 or 運算子來結合 `Bold` 和 `Italic` 成員。

## <a name="formatted-text"></a>格式化文字

在目前為止的所有範例中，`Label` 所顯示的整個文字都已進行一致的格式化。 若要改變文字字串內的格式，請勿設定 `Label`的 `Text` 屬性。 相反地，請將[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)屬性設定為[`FormattedString`](xref:Xamarin.Forms.FormattedString)類型的物件。

`FormattedString` 具有[`Spans`](xref:Xamarin.Forms.FormattedString.Spans)屬性，這是[`Span`](xref:Xamarin.Forms.Span)物件的集合。 每個 `Span` 物件都有自己的[`Text`](xref:Xamarin.Forms.Span.Text)、 [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)、 [`FontSize`](xref:Xamarin.Forms.Span.FontSize)、 [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)、 [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)和[`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)屬性。

[**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)範例會示範如何使用單行文字的 `FormattedText` 屬性，而[**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)會示範整個段落的技術，如下所示：

[![可變格式段落的三向螢幕擷取畫面](images/ch03fg06-small.png "可變格式的標籤文字")](images/ch03fg06-large.png#lightbox "可變格式的標籤文字")

[**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程式會使用單一 `Label`，而 `FormattedString` 物件則會顯示每個平臺的所有命名字型大小。

## <a name="related-links"></a>相關連結

- [第3章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第3章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 3 F#章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [使用色彩](~/xamarin-forms/user-interface/colors.md)
