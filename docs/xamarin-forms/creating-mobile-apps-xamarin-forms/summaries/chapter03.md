---
title: 第 3 章的摘要。 更深入成文字
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 第 3 章摘要。 更深入成文字
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: f0e6063b6ce6038a6f6def67c27347ca024e72f6
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241483"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第 3 章的摘要。 更深入成文字

這一章探討[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)包括色彩、 字型及格式的更深入的檢視。

## <a name="wrapping-paragraphs"></a>段落自動換行

當[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.Text/)屬性`Label`包含長文字，`Label`會自動折行它多行所示[ **Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)範例。 您可以內嵌 Unicode 代碼，例如 '\u2014' em dash 或 C# 字元，例如 '\r' 至新的一行中斷。

當[ `HorizontalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.HorizontalOptions/)和[ `VerticalOptions` ](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/)屬性`Label`設為`LayoutOptions.Fill`、 的整體大小`Label`空間由，其容器會提供資料。 `Label`即為*限制*。 大小`Label`是其容器的大小。

當`HorizontalOptions`和`VerticalOptions`以外屬性設定為值`LayoutOptions.Fill`，大小為`Label`呈現的文字，最多提供以供其容器的大小所需的空間由`Label`。 `Label`即為*受條件約束*，它會決定其本身的大小。

(注意： 條款*限制*和*受條件約束*可能違反直覺，因為未受限制的檢視是通常小於限制檢視。 此外，這些詞彙不會使用一致的書籍 》 中。）

例如，檢視`Label`可以限制一個維度中，並在其他未受限。 A`Label`將只將文字換行在多行上如果限制水平。

如果`Label`是限制，其可能佔用相當多的空間比所需的文字。 文字可以放置區內整體`Label`。 設定[ `HorizontalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.HorizontalTextAlignment/)屬性成員的[ `TextAlignment` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TextAlignment/)列舉型別 ([`Start`](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Start/)， [ `Center` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)，或[ `End` ](https://developer.xamarin.com/api/field/Xamarin.Forms.TextAlignment.Center/)) 來控制了段落中的所有行的對齊方式。 預設值是`Start`為靠左對齊文字。

設定[ `VerticalTextAlignment` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/)屬性成員的`TextAlignment`列舉型別將文字上方、 中心，或所佔用的區域底部放置`Label`。

設定[ `LineBreakMode` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/)屬性成員的[ `LineBreakMode` ](https://developer.xamarin.com/api/type/Xamarin.Forms.LineBreakMode/)列舉型別 ([`WordWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.WordWrap/)， [ `CharacterWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.CharacterWrap/)， [ `NoWrap` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/)， [ `HeadTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.HeadTruncation/)， [ `MiddleTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.MiddleTruncation/)，或[ `TailTruncation` ](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.TailTruncation/)) 至控制項的多個段落中斷的線條或會被截斷的方式。

## <a name="text-and-background-colors"></a>文字和背景色彩

設定[ `TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/)和[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.BackgroundColor/)屬性`Label`至[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)控制的文字和背景色彩的值。

`BackgroundColor`適用於所佔用的整個區域的背景`Label`。 取決於`HorizontalOptions`和`VerticalOptions`屬性、 大小可能遠超過： 將文字顯示所需的區域。 您可以使用色彩來實驗的各種值`HorizontalOptions`， `VerticalOptions`， `HorizontalExeAlignment`，和`VerticalTextAlignment`如何影響的大小和位置`Label`，大小和位置內的文字和`Label`。

## <a name="the-color-structure"></a>色彩結構

[ `Color` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Color/)結構可讓您指定的色彩，以紅-綠-藍 (RGB) 值或色調飽和度-明暗度 (HSL) 值，或使用的色彩名稱。 也可用於表示透明度 Alpha 色板。

使用`Color`建構函式來指定：

- [灰色陰影](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/)
- [RGB 值](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/)
- [透明度 RGB 值](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Color.Color/p/System.Double/System.Double/System.Double/System.Double/)

引數是`double`範圍從 0 到 1 的值。

您也可以使用數種靜態方法來建立`Color`值：

- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Double/System.Double/System.Double/) 如`double`介於 0 到 1 的 RGB 值
- [`Color.FromRgb`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgb/p/System.Int32/System.Int32/System.Int32/) 對於從 0 到 255 之間的整數 RGB 值
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Double/System.Double/System.Double/System.Double/) 如`double`透明度的 RGB 值
- [`Color.FromRgba`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromRgba/p/System.Int32/System.Int32/System.Int32/System.Int32/) 對於具有透明度整數 RGB 值
- [`Color.FromHsla`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHsla/p/System.Double/System.Double/System.Double/System.Double/) 如`double`透明度 HSL 值
- [`Color.FromUint`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromUint/p/System.UInt32/) 如`uint`值計算成 (B + 256 * (G + 256 * (R + 256 * A)))
- [`Color.FromHex`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.FromHex/p/System.String/) 如`string`表單中的十六進位數字的格式"#AARRGGBB"或"#RRGGBB"或"#ARGB"或"#RGB"，其中每個字母都會對應到的十六進位數字的 alpha、 紅色、 綠色和藍色的通道。 這個方法是主要用於 XAML 色彩轉換中所述[第 7 章、 XAML 與程式碼](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)。

一旦建立，`Color`值是不變。 可以從下列屬性中取得的色彩特性：

- [`R`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.R/)
- [`G`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.G/)
- [`B`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.B/)
- [`A`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.A/)
- [`Hue`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Hue/)
- [`Saturation`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Saturation/)
- [`Luminosity`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Luminosity/)

這些全部都是`double`範圍從 0 到 1 的值。

`Color` 也會定義 240 公用靜態唯讀欄位的常見的色彩。 在撰寫活頁簿時，只有 17 常見的色彩可供使用。

另一個公用靜態唯讀欄位會定義的色彩設定為零的所有色彩通道：

- [`Color.Transparent`](https://developer.xamarin.com/api/field/Xamarin.Forms.Color.Transparent/)

數個執行個體方法允許修改現有的色彩，以建立新的色彩：

- [`AddLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.AddLuminosity/p/System.Double/)
- [`MultiplyAlpha`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.MultiplyAlpha/p/System.Double/)
- [`WithHue`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithHue/p/System.Double/)
- [`WithLuminosity`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithLuminosity/p/System.Double/)
- [`WithSaturation`](https://developer.xamarin.com/api/member/Xamarin.Forms.Color.WithSaturation/p/System.Double/)

最後，兩個靜態的唯讀屬性會定義特殊的色彩值：

- [`Color.Default`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Default/)所有通道都設&ndash;1
- [`Color.Accent`](https://developer.xamarin.com/api/property/Xamarin.Forms.Color.Accent/)

`Color.Default` 為了強制執行的平台的色彩配置，因此在不同平台上的不同內容中有不同的意義。 預設平台色彩配置是：

- iOS： 深色淺色背景上的文字
- Android： 淺上 （在活頁簿） 的深色背景文字或淺色背景上深色的文字 (透過在 AppCompat 材料設計**主要**的範例程式碼儲存機制分支)
- UWP： 淺色背景深色的文字
- Windows 8.1： 深色背景的淺文字
- Windows Phone 8.1： 深色背景的淺文字

`Color.Accent`值，會顯示暗色或亮色背景的平台專屬 （和有時候使用者可選取） 色彩的結果。

## <a name="changing-the-application-color-scheme"></a>變更應用程式色彩配置

各種平台都有預設的色彩配置上述清單中所示。

當目標為 Android，就可以藉由指定淺色佈景主題，或在 Android.Manifest.xml 檔案中，切換到深的陰影的光線配置[加入 AppCompat 和材料設計](~/xamarin-forms/platform/android/appcompat.md)。

對於 Windows 平台，色彩佈景主題通常由使用者選取，但您可以加入`RequestedTheme`屬性設定為 `Light`或`Dark`平台的 App.xaml 檔案中。 根據預設，App.xaml 檔案 UWP 專案中的包含`RequestedTheme`屬性設為`Light`。

## <a name="font-sizes-and-attributes"></a>字型的大小和屬性

設定[ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontFamily/)屬性`Label`為字串，例如"細明體"來選取字型家族。 不過，您必須指定特定的平台，支援在字型家族，而且在這方面所不一致的平台。

設定[ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontSize/)屬性`Label`至`double`指定大約字型的高度。 請參閱[第 5 章處理大小](chapter05.md)，如需詳細資訊，以聰明的方式選擇字型大小。

您也可以取得數個預設平台相依的字型大小。 靜態[ `Device.GetNamedSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/System.Type/)方法和[多載](https://developer.xamarin.com/api/member/Xamarin.Forms.Device.GetNamedSize/p/Xamarin.Forms.NamedSize/Xamarin.Forms.Element/)兩者都傳回`double`字型大小值適用於平台根據的成員[ `NamedSize` ](https://developer.xamarin.com/api/type/Xamarin.Forms.NamedSize/)列舉型別 ([`Default`](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Default/)， [ `Micro` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Micro/)， [ `Small` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Small/)， [ `Medium` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Medium/)， 和[ `Large` ](https://developer.xamarin.com/api/field/Xamarin.Forms.NamedSize.Large/))。 從傳回的值`Medium`成員不一定相同`Default`。 [ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)名為 sizes 這些範例會顯示的文字。

設定[ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FontAttributes/)屬性`Label`給這些成員[ `FontAttributes` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FontAttributes/)列舉型別， [ `Bold` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Bold/)， [ `Italic`](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.Italic/)，或[ `None` ](https://developer.xamarin.com/api/field/Xamarin.Forms.FontAttributes.None/)。 您可以結合`Bold`和`Italic`使用 C# 位元 OR 運算子的成員。

## <a name="formatted-text"></a>格式化文字

在所有目前的範例中，整個文字顯示`Label`已經一致的方式格式化。 若要格式化的文字字串內的變化，不需要設定`Text`屬性`Label`。 相反地，設定[ `FormattedText` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.FormattedText/)屬性型別的物件[ `FormattedString` ](https://developer.xamarin.com/api/type/Xamarin.Forms.FormattedString/)。

`FormattedString` 具有[ `Spans` ](https://developer.xamarin.com/api/property/Xamarin.Forms.FormattedString.Spans/)屬性的集合[ `Span` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Span/)物件。 每個`Span`物件都有它自己[ `Text` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.Text/)， [ `FontFamily` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontFamily/)， [ `FontSize` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontSize/)， [ `FontAttributes` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.FontAttributes/)， [ `ForegroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.ForegroundColor/)，和[ `BackgroundColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Span.BackgroundColor/)屬性。

[ **VariableFormattedText** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)範例將示範如何使用`FormattedText`單行文字，屬性和[ **VariableFormattedParagraph**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)示範整個段落的技巧，如下所示：

[![變數的三個螢幕擷取畫面格式化段落](images/ch03fg06-small.png "變數格式的標籤文字")](images/ch03fg06-large.png#lightbox "變數格式的標籤文字")

[ **NamedFontSizes** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)程式會使用單一`Label`和`FormattedString`顯示具名的字型的大小，每個平台的所有物件。



## <a name="related-links"></a>相關連結

- [第 3 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第 3 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第 3 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [Label](~/xamarin-forms/user-interface/text/label.md)
- [使用色彩](~/xamarin-forms/user-interface/colors.md)
