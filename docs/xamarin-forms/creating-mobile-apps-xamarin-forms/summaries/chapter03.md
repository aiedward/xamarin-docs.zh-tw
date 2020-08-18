---
title: 第3章的摘要。 Deeper into text
description: 建立 Mobile Apps Xamarin.Forms ：第3章的摘要。 Deeper into text
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5423a9f716f384eca107003bdeca69615f8b459f
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136899"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第3章的摘要。 Deeper into text

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

這一章會 [`Label`](xref:Xamarin.Forms.Label) 深入探索視野，包括色彩、字型和格式。

## <a name="wrapping-paragraphs"></a>換行段落

當的 [`Text`](xref:Xamarin.Forms.Label.Text) 屬性 `Label` 包含長文字時， `Label` 會自動將其包裝成多行，如 [**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles) 範例所示。 您可以將 Unicode 代碼（例如 ' \u2014 '）內嵌為 em 虛線，或是將 c # 字元（例如 ' \r '）換成新行。

當的 [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) 和 [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) 屬性 `Label` 設定為時，的 `LayoutOptions.Fill` 整體大小 `Label` 會受到其容器可供使用的空間所控管。 `Label`被視為*受限制*。 的大小 `Label` 是其容器的大小。

當 `HorizontalOptions` 和 `VerticalOptions` 屬性設定為以外的值時 `LayoutOptions.Fill` ，的大小 `Label` 會受到轉譯文字所需的空間所控管，最大到其容器可供使用的大小 `Label` 。 `Label`說是不受*限制*的，它會決定自己的大小。

 (附注：*受限制**和不受限制的*詞彙可能會以直覺方式顯示，因為未受限制的視圖通常比受限的視圖小。 此外，本書籍的早期章節不會一致地使用這些詞彙。 ) 

例如的視圖 `Label` 可以在一個維度中受到限制，而另一個維度則不受限制。 `Label`只有在水準限制時，才會將文字包裝在多行上。

如果有 `Label` 限制，則可能會佔用比文字所需更多的空間。 文字可以放在的整體區域內 `Label` 。 將 [`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment) 屬性設為 [`TextAlignment`](xref:Xamarin.Forms.TextAlignment) 列舉 ([`Start`](xref:Xamarin.Forms.TextAlignment.Start) 、或) 的成員， [`Center`](xref:Xamarin.Forms.TextAlignment.Center) [`End`](xref:Xamarin.Forms.TextAlignment.Center) 以控制段落所有行的對齊方式。 預設值為 `Start` ，並將文字靠左對齊。

將 [`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment) 屬性設為列舉的成員， `TextAlignment` 以將文字放置於所佔用之區域的頂端、中央或底部 `Label` 。

將 [`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode) 屬性（property）設定為 [`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode) 列舉 ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap) 、、 [`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap) [`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap) 、 [`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation) 、或) 的成員， [`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation) [`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation) 以控制如何在段落分隔中或截斷多行。

## <a name="text-and-background-colors"></a>文字和背景色彩

將的和屬性設定為 [值]， [`TextColor`](xref:Xamarin.Forms.Label.TextColor) [`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor) `Label` [`Color`](xref:Xamarin.Forms.Color) 以控制文字和背景的色彩。

會 `BackgroundColor` 套用至所佔用之整個區域的背景 `Label` 。 根據 `HorizontalOptions` 和 `VerticalOptions` 屬性，該大小可能會比顯示文字所需的區域大得多。 您可以使用色彩來試驗、、和的各種值， `HorizontalOptions` `VerticalOptions` `HorizontalExeAlignment` `VerticalTextAlignment` 以瞭解它們如何影響的大小和位置 `Label` ，以及中文字的大小和位置 `Label` 。

## <a name="the-color-structure"></a>色彩結構

此 [`Color`](xref:Xamarin.Forms.Color) 結構可讓您以紅-綠-藍 (RGB) 值來指定色彩，或將色調/飽和度-亮度 (HSL) 值或色彩名稱。 也可以使用 Alpha 色板指出透明度。

使用函式 `Color` 來指定：

- [灰色陰影](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [具有透明度的 RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

引數是 `double` 介於0到1之間的值。

您也可以使用數個靜態方法來建立 `Color` 值：

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))`double`從0到1的 RGB 值
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) 從0到255的整數 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) 適用于 `double` 具有透明度的 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) 具有透明度的整數 RGB 值
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))`double`具有透明度的 HSL 值
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) 針對 `uint` 計算為 (B + 256 \* (G + 256 \* (R + 256 \* a) # A4 # A5 的值
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) 針對 `string` 格式為 "#AARRGGBB" 或 "#RRGGBB" 或 "#ARGB" 或 "#RGB" 的十六進位數位，其中每個字母都對應至 Alpha、紅色、綠色和藍色通道的十六進位數位。 這個方法是 XAML 色彩轉換的主要用途，如 XAML 和程式 [代碼第7章](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)所述。

一旦建立之後， `Color` 值就是不可變的。 您可以從下列屬性取得色彩的特性：

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

這些都是 `double` 介於0到1之間的值。

`Color` 也會定義通用色彩的240公用靜態唯讀欄位。 撰寫書籍時，只有17種常見的色彩可用。

另一個公用靜態唯讀欄位定義了所有色彩通道都設為零的色彩：

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

有幾個實例方法允許修改現有的色彩以建立新的色彩：

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

最後，兩個靜態唯讀屬性會定義特殊的色彩值：

- [`Color.Default`](xref:Xamarin.Forms.Color.Default)，所有通道都設為 &ndash; 1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` 的目的是要強制執行平臺的色彩配置，因此在不同平臺上的不同內容中會有不同的意義。 平臺色彩配置預設為：

- iOS：淺色背景的深色文字
- Android：深色背景上的淺文字 (于書籍) 或淺色背景中的深色文字，以透過範例程式碼存放庫的 **主要** 分支中的 AppCompat 來設計的材質 () 
- UWP：淺色背景的深色文字

此 `Color.Accent` 值會產生平臺特定的 (，有時也會顯示在深色或淺色背景上可見的使用者可選取) 色彩。

## <a name="changing-the-application-color-scheme"></a>變更應用程式色彩配置

各種平臺都有預設的色彩配置，如上表所示。

以 Android 為目標時，您可以藉由在 Android.Manifest.xml 檔案中指定淺色主題，或 [加入 AppCompat 和材質設計](~/xamarin-forms/platform/android/appcompat-material-design.md)，切換至輕量配置。

針對 Windows 平臺，色彩主題通常會由使用者選取，但您可以將 `RequestedTheme` 屬性集新增至 `Light` `Dark` 平臺的 app.config 檔案中的或。 根據預設，UWP 專案中的 app.xaml 檔案包含 `RequestedTheme` 設定為的屬性 `Light` 。

## <a name="font-sizes-and-attributes"></a>字型大小和屬性

將的 [`FontFamily`](xref:Xamarin.Forms.Label.FontFamily) 屬性設定 `Label` 為字串，例如 "Times Roman" 來選取字型系列。 不過，您必須指定特定平臺所支援的字型系列，而平臺在這方面不一致。

將的屬性設定為，以 [`FontSize`](xref:Xamarin.Forms.Label.FontSize) `Label` `double` 指定字型的近似高度。 如需智慧選擇字型大小的詳細資訊，請參閱 [第5章，處理大小](chapter05.md)。

您也可以取得數個預設的平臺相依字型大小的其中一種。 靜態 [ `Device.GetNamedSize` ] (x： Xamarin.Forms 。GetNamedSize (Xamarin.Forms 。NamedSize，System.object) # A3 方法和 [多載] (x： Xamarin.Forms 。GetNamedSize (Xamarin.Forms 。NamedSize， Xamarin.Forms 。元素) # A7 都會 `double` 根據列舉的成員，傳回適合平臺的字型大小 (、、、 [`NamedSize`](xref:Xamarin.Forms.NamedSize) [`Default`](xref:Xamarin.Forms.NamedSize.Default) [`Micro`](xref:Xamarin.Forms.NamedSize.Micro) [`Small`](xref:Xamarin.Forms.NamedSize.Small) [`Medium`](xref:Xamarin.Forms.NamedSize.Medium) 和 [`Large`](xref:Xamarin.Forms.NamedSize.Large)) 。 從成員傳回的值 `Medium` 不一定與相同 `Default` 。 [**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)範例會顯示具有這些命名大小的文字。

將的 [`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes) 屬性設定 `Label` 為這些 [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) 列舉、、或的 [`Bold`](xref:Xamarin.Forms.FontAttributes.Bold) 成員  [`Italic`](xref:Xamarin.Forms.FontAttributes.Italic) [`None`](xref:Xamarin.Forms.FontAttributes.None) 。 您可以結合 `Bold` 和 `Italic` 成員與 c # 位 or 運算子。

## <a name="formatted-text"></a>格式化文字

在截至目前為止的所有範例中，所顯示的整個文字 `Label` 都已一致地格式化。 若要變更文字字串內的格式，請不要設定的 `Text` 屬性 `Label` 。 相反地，請將 [`FormattedText`](xref:Xamarin.Forms.Label.FormattedText) 屬性設定為類型的物件 [`FormattedString`](xref:Xamarin.Forms.FormattedString) 。

`FormattedString` 具有 [`Spans`](xref:Xamarin.Forms.FormattedString.Spans) 是物件集合的屬性 [`Span`](xref:Xamarin.Forms.Span) 。 每個 `Span` 物件都有它自己的、、、、 [`Text`](xref:Xamarin.Forms.Span.Text) [`FontFamily`](xref:Xamarin.Forms.Span.FontFamily) [`FontSize`](xref:Xamarin.Forms.Span.FontSize) [`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes) [`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor) 和 [`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor) 屬性。

[**VariableFormattedText**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)範例會示範如何使用 `FormattedText` 單一文字行的屬性，而[**VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)會示範整個段落的技巧，如下所示：

[![可變格式化段落的三個螢幕擷取畫面](images/ch03fg06-small.png "可變格式的標籤文字")](images/ch03fg06-large.png#lightbox "可變格式的標籤文字")

[**NamedFontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程式會使用單一 `Label` 和 `FormattedString` 物件來顯示每個平臺的所有命名字型大小。

## <a name="related-links"></a>相關連結

- [第3章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第3章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第3章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [標籤](~/xamarin-forms/user-interface/text/label.md)
- [使用色彩](~/xamarin-forms/user-interface/colors.md)
