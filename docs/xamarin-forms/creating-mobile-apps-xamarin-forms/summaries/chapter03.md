---
title: 第 3 章的摘要。 更深入成文字
description: 使用 Xamarin.Forms 建立行動應用程式：第 3 章的摘要。 更深入成文字
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 11b0a9c5b165df2b0d02bb09fb282cc18290e6ca
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207841"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第 3 章的摘要。 更深入成文字

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

這一章探討[ `Label` ](xref:Xamarin.Forms.Label)更為深入探討，包括色彩、 字型和格式化的檢視。

## <a name="wrapping-paragraphs"></a>文繞圖段落

當[ `Text` ](xref:Xamarin.Forms.Label.Text)屬性`Label`包含長的文字`Label`會自動將它包裝多行所示[ **Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)範例。 您可以內嵌 Unicode 程式碼，例如 '\u2014' em dash，或C#字元，例如 '\r' 中斷到新的一行。

時[ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions)並[ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)屬性`Label`設為`LayoutOptions.Fill`的整體大小`Label`受到空間，其容器會提供。 `Label`要*限制*。 大小`Label`是其容器的大小。

當`HorizontalOptions`和`VerticalOptions`以外的其他屬性設定為值`LayoutOptions.Fill`，則大小`Label`受到所呈現的文字，但不超過其容器提供的大小所需的空間`Label`。 `Label`要*無限制*，它會決定其本身的大小。

(注意：條款*受限*並*無限制*可能違反直覺，因為未受限制的檢視是通常小於條件約束的檢視。 此外，這些條款不會使用一致的方式 》 一書中。）

這類檢視`Label`可以限制在一個維度中，並在其他未受限制。 A`Label`會僅將文字換行多行如果水平的條件約束。

如果`Label`是限制，它可能會佔用相當大的空間超出所需的文字。 文字可以放置在整體處理區內`Label`。 設定[ `HorizontalTextAlignment` ](xref:Xamarin.Forms.Label.HorizontalTextAlignment)屬性的成員來[ `TextAlignment` ](xref:Xamarin.Forms.TextAlignment)列舉型別 ([`Start`](xref:Xamarin.Forms.TextAlignment.Start)， [ `Center` ](xref:Xamarin.Forms.TextAlignment.Center)，或[ `End` ](xref:Xamarin.Forms.TextAlignment.Center)) 來控制的段落的所有行的對齊方式。 預設值是`Start`和文字靠左對齊。

設定[ `VerticalTextAlignment` ](xref:Xamarin.Forms.Label.VerticalTextAlignment)屬性的成員來`TextAlignment`列舉型別，將文字上方、 中心或佔用的區域底部放置`Label`。

設定[ `LineBreakMode` ](xref:Xamarin.Forms.Label.LineBreakMode)屬性的成員來[ `LineBreakMode` ](xref:Xamarin.Forms.LineBreakMode)列舉型別 ([`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap)， [ `CharacterWrap` ](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)， [ `NoWrap` ](xref:Xamarin.Forms.LineBreakMode.NoWrap)， [ `HeadTruncation` ](xref:Xamarin.Forms.LineBreakMode.HeadTruncation)， [ `MiddleTruncation` ](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)，或[ `TailTruncation` ](xref:Xamarin.Forms.LineBreakMode.TailTruncation)) 至控制項的多個中分段符號或截斷的方式。

## <a name="text-and-background-colors"></a>文字和背景色彩

設定[ `TextColor` ](xref:Xamarin.Forms.Label.TextColor)並[ `BackgroundColor` ](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性`Label`至[ `Color` ](xref:Xamarin.Forms.Color)值，以控制文字和背景的色彩。

`BackgroundColor`套用至所佔用的整個區域的背景`Label`。 取決於`HorizontalOptions`和`VerticalOptions`屬性、 大小可能會大幅大於： 將文字顯示所需的區域。 您可以使用色彩來試驗不同的值`HorizontalOptions`， `VerticalOptions`， `HorizontalExeAlignment`，和`VerticalTextAlignment`若要查看它們如何影響的大小和位置`Label`，和的大小和位置中的文字`Label`。

## <a name="the-color-structure"></a>色彩結構

[ `Color` ](xref:Xamarin.Forms.Color)結構可讓您指定的色彩，以紅-綠-藍 (RGB) 值或色相-彩度-亮度 (HSL) 的值，或使用的色彩名稱。 也可用來表示透明度的 Alpha 色板。

使用`Color`建構函式來指定：

- [灰色陰影](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [透明度的 RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

引數是`double`範圍從 0 到 1 的值。

您也可以使用數個靜態方法來建立`Color`值：

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double)) 針對`double`介於 0 到 1 的 RGB 值
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32)) 針對從 0 到 255 之間的整數 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double)) 針對`double`透明度的 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32)) 對於整數與透明度的 RGB 值
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double)) 針對`double`透明度 HSL 值
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32)) 針對`uint`值計算方式為 (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String)) 針對`string`表單中的十六進位數字格式"#AARRGGBB 」 或 「 #RRGGBB"或"#ARGB 」 或 「 #RGB"，其中每個字母對應至十六進位數字的 alpha、 紅色、 綠色和藍色色頻。 這個方法是主要用於 XAML 色彩轉換中所述[第 7 章、 XAML 與程式碼](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)。

建立後，`Color`值是不變。 可以從下列屬性中取得的色彩特性：

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

這些全部都`double`範圍從 0 到 1 的值。

`Color` 也會定義 240 公用靜態唯讀欄位的常見的色彩。 在撰寫活頁簿時，只有 17 常見的色彩推出。

另一個公用靜態唯讀欄位會定義的色彩設定為零的所有色彩通道：

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

數個執行個體方法允許修改現有的色彩，來建立新的色彩：

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

最後，兩個靜態的唯讀屬性會定義特殊的色彩值：

- [`Color.Default`](xref:Xamarin.Forms.Color.Default)所有的通道設定為&ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default` 為了強制執行平台的色彩配置，並因此在不同平台上的不同內容中有不同的意義。 根據預設平台色彩配置如下：

- iOS:深色的文字，在淺色背景
- Android:亮色調 （以書籍） 的深色背景上的文字或深色的文字，在淺色背景 (材料設計，透過在 AppCompat**主要**的範例程式碼存放庫的分支)
- UWP:深色的文字，在淺色背景

`Color.Accent`值在特定平台 （和有時候使用者可選取） 的色彩，其顯示暗色或亮色背景的結果。

## <a name="changing-the-application-color-scheme"></a>變更應用程式色彩配置

各種不同的平台有預設的色彩配置，上述清單中所示。

當以 Android 為目標，就可以藉由指定淺色佈景主題，在 Android.Manifest.xml 檔案中，或藉由切換為暗色調上 light 配置[新增 AppCompat 和材質設計](~/xamarin-forms/platform/android/appcompat-material-design.md)。

適用於 Windows 平台的色彩佈景主題通常由使用者選取，但您可以加入`RequestedTheme`屬性設定為 `Light`或`Dark`平台的 App.xaml 檔案中。 根據預設，包含在 UWP 專案 App.xaml 檔案`RequestedTheme`屬性設為`Light`。

## <a name="font-sizes-and-attributes"></a>字型的大小和屬性

設定[ `FontFamily` ](xref:Xamarin.Forms.Label.FontFamily)屬性`Label`為字串，例如"細明體"以選取字型家族。 不過，您必須指定特定的平台，支援的字型家族，並在這方面不一致的平台。

設定[ `FontSize` ](xref:Xamarin.Forms.Label.FontSize)屬性`Label`到`double`指定字型的大約高度。 請參閱[第 5 章因應大小](chapter05.md)，如需詳細資訊，以智慧方式選擇字型大小。

您也可以取得數個預設平台相依的字型大小。 靜態[ `Device.GetNamedSize` ](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))方法並[多載](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element))兩者都傳回`double`字型大小值適用於平台根據的成員[ `NamedSize` ](xref:Xamarin.Forms.NamedSize)列舉型別 ([`Default`](xref:Xamarin.Forms.NamedSize.Default)， [ `Micro` ](xref:Xamarin.Forms.NamedSize.Micro)， [ `Small` ](xref:Xamarin.Forms.NamedSize.Small)， [ `Medium` ](xref:Xamarin.Forms.NamedSize.Medium)， 並[ `Large` ](xref:Xamarin.Forms.NamedSize.Large))。 從傳回的值`Medium`成員不一定相同`Default`。 [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)名為 sizes 這些範例會顯示的文字。

設定[ `FontAttributes` ](xref:Xamarin.Forms.Label.FontAttributes)屬性`Label`這些成員[ `FontAttributes` ](xref:Xamarin.Forms.FontAttributes)列舉[ `Bold` ](xref:Xamarin.Forms.FontAttributes.Bold)， [ `Italic`](xref:Xamarin.Forms.FontAttributes.Italic)，或[ `None` ](xref:Xamarin.Forms.FontAttributes.None)。 您可以結合`Bold`並`Italic`成員C#位元 OR 運算子。

## <a name="formatted-text"></a>格式化文字

所顯示網頁時，所有目前的範例中，整個文字`Label`已經一致的方式格式化。 若要格式化的文字字串內的變化，不需要設定`Text`屬性`Label`。 相反地，設定[ `FormattedText` ](xref:Xamarin.Forms.Label.FormattedText)型別的物件的屬性[ `FormattedString` ](xref:Xamarin.Forms.FormattedString)。

`FormattedString` 已[ `Spans` ](xref:Xamarin.Forms.FormattedString.Spans)屬於的集合屬性[ `Span` ](xref:Xamarin.Forms.Span)物件。 每個`Span`物件都有它自己[ `Text` ](xref:Xamarin.Forms.Span.Text)， [ `FontFamily` ](xref:Xamarin.Forms.Span.FontFamily)， [ `FontSize` ](xref:Xamarin.Forms.Span.FontSize)， [ `FontAttributes` ](xref:Xamarin.Forms.Span.FontAttributes)， [ `ForegroundColor` ](xref:Xamarin.Forms.Span.ForegroundColor)，和[ `BackgroundColor` ](xref:Xamarin.Forms.Span.BackgroundColor)屬性。

[ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)範例示範如何使用`FormattedText`屬性對於某一行的文字，以及[ **VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)示範整個段落，這套方法，如下所示：

[![變數的三個螢幕擷取畫面會格式化段落](images/ch03fg06-small.png "格式化標籤文字的變數")](images/ch03fg06-large.png#lightbox "變數格式的標籤文字")

[ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程式會使用單一`Label`和`FormattedString`顯示具名的字型的大小，每個平台的所有物件。



## <a name="related-links"></a>相關連結

- [第 3 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第 3 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 3 章F#範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [使用色彩](~/xamarin-forms/user-interface/colors.md)
