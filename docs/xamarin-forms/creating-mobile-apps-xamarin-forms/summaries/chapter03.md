---
title: 第3章摘要。 Deeper into text
description: 使用 Xamarin.表單創建行動應用程式:第 3 章摘要。 Deeper into text
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 2E5581A6-4D3E-4BD5-9FDB-ACBA0F0FC734
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 69415b59bbd376330454302981e3216c236a16bb
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "72980927"
---
# <a name="summary-of-chapter-3-deeper-into-text"></a>第3章摘要。 Deeper into text

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)

本章更深入地探討[`Label`](xref:Xamarin.Forms.Label)視圖,包括顏色、字體和格式。

## <a name="wrapping-paragraphs"></a>換行段落

當[`Text`](xref:Xamarin.Forms.Label.Text)`Label`屬性 包含長文本`Label`時, 自動將其包裝為多行,如[**Baskervilles**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/Baskervilles)範例所示。 您可以嵌入 Unicode 代碼,如 em-dash 的「\u2014」,或 C# 字元(如"\r")以換到新行。

當[`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions)的[`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions)和`Label`屬性設置`LayoutOptions.Fill`為`Label`時, `Label`據說是*受約束的*。 的大小`Label`是其容器的大小。

當`HorizontalOptions`和`VerticalOptions`屬性設置為以外`LayoutOptions.Fill`的值時,的`Label`大小 受呈現文本所需的空間(其`Label`容器為 可用的大小)控制。 `Label`據說是*無約束的*,它決定了它自己的大小。

(注意:*受約束*和*不受約束*的術語可能違反直覺,因為無約束視圖通常小於約束視圖。 此外,這些術語在本書的早期章節中不一致使用。

視圖(如 a)`Label`可以在一個維度中約束,另一個維度可以不受約束。 如果`Label`文本水準約束,則僅對多行進行換行。

如果`Label`受約束,它可能佔用的空間可能比文本所需的空間大得多。 文字可以放置在的區域中`Label`。 將[`HorizontalTextAlignment`](xref:Xamarin.Forms.Label.HorizontalTextAlignment)屬性設置為枚舉[`TextAlignment`](xref:Xamarin.Forms.TextAlignment)[`Start`](xref:Xamarin.Forms.TextAlignment.Start)[`Center`](xref:Xamarin.Forms.TextAlignment.Center)(、 或[`End`](xref:Xamarin.Forms.TextAlignment.Center)) 的成員,以控制段落所有行的對齊方式。 預設值為`Start`,並左對齊文本。

將[`VerticalTextAlignment`](xref:Xamarin.Forms.Label.VerticalTextAlignment)屬性設置為枚舉的`TextAlignment`成員 ,將文本定位在 所佔用區域的頂部、中心`Label`或底部。

將[`LineBreakMode`](xref:Xamarin.Forms.Label.LineBreakMode)[`LineBreakMode`](xref:Xamarin.Forms.LineBreakMode)屬性設置為枚舉的成員[`HeadTruncation`](xref:Xamarin.Forms.LineBreakMode.HeadTruncation)[`MiddleTruncation`](xref:Xamarin.Forms.LineBreakMode.MiddleTruncation)[`TailTruncation`](xref:Xamarin.Forms.LineBreakMode.TailTruncation)[`WordWrap`](xref:Xamarin.Forms.LineBreakMode.WordWrap)[`CharacterWrap`](xref:Xamarin.Forms.LineBreakMode.CharacterWrap)[`NoWrap`](xref:Xamarin.Forms.LineBreakMode.NoWrap)(、、、、、、、 、 或 ) 以控制段落中的多行如何中斷或截斷。

## <a name="text-and-background-colors"></a>文字與背景顏色

將[`TextColor`](xref:Xamarin.Forms.Label.TextColor)與[`BackgroundColor`](xref:Xamarin.Forms.VisualElement.BackgroundColor)屬性`Label`設定為[`Color`](xref:Xamarin.Forms.Color)值以控制文本和背景的顏色。

適用於`BackgroundColor`所佔用的整個區域的背景`Label`。 根據和`HorizontalOptions``VerticalOptions`屬性,該大小可能比顯示文本所需的區域大得多。 `HorizontalOptions`可以使用顏色來試驗的各種`VerticalOptions`值,`HorizontalExeAlignment``VerticalTextAlignment`並 查看它們如何影響`Label`的大小 和位置, 以及`Label`文本在的大小和位置。

## <a name="the-color-structure"></a>色彩結構

該[`Color`](xref:Xamarin.Forms.Color)結構允許您將顏色指定為紅-綠藍 (RGB) 值,或色相飽和度 -亮度 (HSL) 值,或使用顏色名稱。 Alpha 通道也可用於指示透明度。

使用`Color`建構函數指定:

- [灰色陰影](xref:Xamarin.Forms.Color.%23ctor(System.Double))
- [RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double))
- [具有透明度的 RGB 值](xref:Xamarin.Forms.Color.%23ctor(System.Double,System.Double,System.Double,System.Double))

參數是`double`介於 0 到 1 的值。

您還可以使用多種靜態方法來建立`Color`值:

- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Double,System.Double,System.Double))對於`double`0 到 1 的 RGB 值
- [`Color.FromRgb`](xref:Xamarin.Forms.Color.FromRgb(System.Int32,System.Int32,System.Int32))對於從 0 到 255 的整數 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Double,System.Double,System.Double,System.Double))具有`double`透明度的 RGB 值
- [`Color.FromRgba`](xref:Xamarin.Forms.Color.FromRgba(System.Int32,System.Int32,System.Int32,System.Int32))用於具有透明度的整數 RGB 值
- [`Color.FromHsla`](xref:Xamarin.Forms.Color.FromHsla(System.Double,System.Double,System.Double,System.Double))具有`double`透明度的 HSL 值
- [`Color.FromUint`](xref:Xamarin.Forms.Color.FromUint(System.UInt32))`uint`計算為 (B = \* 256 (G = 256 \* \* (R = 256 A))的值)
- [`Color.FromHex`](xref:Xamarin.Forms.Color.FromHex(System.String))對於"#AARRGGBB"或"#RRGGBB"或"#ARGB"或"#RGB"形式的十六進位數位`string`,其中每個字母對應於 Alpha、紅色、綠色和藍色通道的十六進位數位。 此方法主要用於 XAML 顏色轉換,如第 7[章 XAML 與代碼](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md)中所述。

建立後,`Color`值是不可改變的。 顏色的特徵可以從以下屬性中獲得:

- [`R`](xref:Xamarin.Forms.Color.R)
- [`G`](xref:Xamarin.Forms.Color.G)
- [`B`](xref:Xamarin.Forms.Color.B)
- [`A`](xref:Xamarin.Forms.Color.A)
- [`Hue`](xref:Xamarin.Forms.Color.Hue)
- [`Saturation`](xref:Xamarin.Forms.Color.Saturation)
- [`Luminosity`](xref:Xamarin.Forms.Color.Luminosity)

這些都是`double`從 0 到 1 的值。

`Color`還定義了 240 公共靜態隻讀欄位,用於通用顏色。 在這本書編寫時,只有17種常見的顏色可用。

另一個公共靜態唯讀欄位定義所有顏色通道設定為零的顏色:

- [`Color.Transparent`](xref:Xamarin.Forms.Color.Transparent)

幾種實體方法允許修改現有顏色以建立新顏色:

- [`AddLuminosity`](xref:Xamarin.Forms.Color.AddLuminosity(System.Double))
- [`MultiplyAlpha`](xref:Xamarin.Forms.Color.MultiplyAlpha(System.Double))
- [`WithHue`](xref:Xamarin.Forms.Color.WithHue(System.Double))
- [`WithLuminosity`](xref:Xamarin.Forms.Color.WithLuminosity(System.Double))
- [`WithSaturation`](xref:Xamarin.Forms.Color.WithSaturation(System.Double))

最後,兩個靜態唯讀屬性定義特殊顏色值:

- [`Color.Default`](xref:Xamarin.Forms.Color.Default),所有通道設置為&ndash;1
- [`Color.Accent`](xref:Xamarin.Forms.Color.Accent)

`Color.Default`旨在強制實施平臺的色彩配置,因此在不同的平臺上的不同上下文中具有不同的含義。 預設情況下,平台色彩配置為:

- iOS:淺色背景上的暗文字
- Android:深色背景(在書中)或淺色背景上的深色文本(通過範例代碼存儲庫**的主**分支中的 AppCompat 進行材料設計)
- UWP:淺色背景上的暗文字

該`Color.Accent`值會導致特定於平台(有時是用戶可選擇)的顏色在深色或淺色背景上可見。

## <a name="changing-the-application-color-scheme"></a>變更應用程式顏色機制

如上文清單所示,各種平臺具有預設色彩配置。

在定位 Android 時,可以通過在 Android.Manifest.xml 檔中指定燈光主題或[添加 AppCompat 和材料設計](~/xamarin-forms/platform/android/appcompat-material-design.md)來切換到暗燈方案。

對於 Windows 平台,顏色主題通常由使用者選擇,但您`RequestedTheme`可以將 屬性集`Light``Dark`添加到平臺 的 App.xaml 檔中。 默認情況下,UWP 專案中的 App.xaml 檔`RequestedTheme``Light`包含設置為的屬性。

## <a name="font-sizes-and-attributes"></a>字型大小與屬性

將[`FontFamily`](xref:Xamarin.Forms.Label.FontFamily)的屬性`Label`設置為字串(如"時間羅馬")以選擇字體系列。 但是,您需要指定特定平臺上支援的字體系列,並且這些平臺在這方面不一致。

將[`FontSize`](xref:Xamarin.Forms.Label.FontSize)的`Label`屬性`double`設定為 ,以指定字型的大致高度。 有關智慧選擇字體大小的更多詳細資訊,請參閱[第 5 章"處理大小](chapter05.md)"。

您也可以獲取多個預設的平台相關字體大小之一。 [`Device.GetNamedSize`](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,System.Type))靜態方法和[重載](xref:Xamarin.Forms.Device.GetNamedSize(Xamarin.Forms.NamedSize,Xamarin.Forms.Element))都返回`double`一個[`NamedSize`](xref:Xamarin.Forms.NamedSize)字體大小 值,該值基於枚舉[`Micro`](xref:Xamarin.Forms.NamedSize.Micro)[`Small`](xref:Xamarin.Forms.NamedSize.Small)[`Medium`](xref:Xamarin.Forms.NamedSize.Medium)[`Large`](xref:Xamarin.Forms.NamedSize.Large)[`Default`](xref:Xamarin.Forms.NamedSize.Default)的成員(、、、、、、 和 )來適合平臺。 從`Medium`成員返回的值不一定與`Default`相同。 [**「命名字體」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)範例顯示具有這些命名大小的文字。

將[`FontAttributes`](xref:Xamarin.Forms.Label.FontAttributes)`Label`的屬性設定為這些[`FontAttributes`](xref:Xamarin.Forms.FontAttributes)Entt[`Bold`](xref:Xamarin.Forms.FontAttributes.Bold)的[`Italic`](xref:Xamarin.Forms.FontAttributes.Italic)[`None`](xref:Xamarin.Forms.FontAttributes.None)成員, 或 。 您可以將`Bold``Italic`和成員與 C# 位元或運算符合並。

## <a name="formatted-text"></a>格式化文字

在迄今為止的所有示例中,所`Label`顯示的整個文本都統一了格式。 要更改文字字串中的格式,請不要設置`Text`的屬性。 `Label` 相反,設定[`FormattedText`](xref:Xamarin.Forms.Label.FormattedText)為類型[`FormattedString`](xref:Xamarin.Forms.FormattedString)的物件 。

`FormattedString`具有[`Spans`](xref:Xamarin.Forms.FormattedString.Spans)物件集合的屬性[`Span`](xref:Xamarin.Forms.Span)。 每個`Span`[`Text`](xref:Xamarin.Forms.Span.Text)物件[`FontFamily`](xref:Xamarin.Forms.Span.FontFamily)都有[`FontSize`](xref:Xamarin.Forms.Span.FontSize)[`FontAttributes`](xref:Xamarin.Forms.Span.FontAttributes)[`ForegroundColor`](xref:Xamarin.Forms.Span.ForegroundColor)自己的[`BackgroundColor`](xref:Xamarin.Forms.Span.BackgroundColor)、、、和 屬性。

[**變數格式文字**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormText)範例展示對單一字文字`FormattedText`行使用屬性,[**而「可變格式」 段落**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/VarFormPara)演示了整個段落的技術,如下所示:

[![可變格式段落的三重螢幕擷取](images/ch03fg06-small.png "可變格式化標籤文字")](images/ch03fg06-large.png#lightbox "可變格式化標籤文字")

[**命名字體計劃**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/NamedFontSizes)使用單`Label`個 和`FormattedString`一個 對象來顯示每個平臺的所有命名字體大小。

## <a name="related-links"></a>相關連結

- [第三章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch03-Apr2016.pdf)
- [第三章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03)
- [第三章 F# 樣品](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter03/FS)
- [標籤](~/xamarin-forms/user-interface/text/label.md)
- [使用色彩](~/xamarin-forms/user-interface/colors.md)
