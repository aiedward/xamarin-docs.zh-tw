---
title: XAML 標記延伸
description: 本文說明如何使用 Xamarin.Forms XAML 標記延伸來擴充的威力與彈性的 XAML，藉由設定從來源常值文字字串以外的項目屬性。
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 3a28963852b2b36be0a9751722b7f184c340d3e9
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563390"
---
# <a name="xaml-markup-extensions"></a>XAML 標記延伸

XAML 標記延伸模組可協助藉由使用元素屬性來設定從常值文字字串以外的來源延伸的威力與彈性的 XAML。

例如，通常您設定`Color`屬性`BoxView`如下所示：

```xaml
<BoxView Color="Blue" />
```

或者，您可以將它設定為十六進位的 RGB 色彩值：

```xaml
<BoxView Color="#FF0080" />
```

在任一情況下，文字字串設定為`Color`屬性會轉換成`Color`值[ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter)類別。

您可能會改為想要設定`Color`屬性值儲存在資源字典中，或您已建立，類別的靜態屬性的值或型別的屬性`Color`的另一個項目在頁面上，或從建構分隔色調、 飽和度和亮度值。

所有這些選項都可以使用 XAML 標記延伸的。 但不要讓片語 「 標記延伸模組 」 情況嚇到了： XAML 標記延伸模組*不*XML 延伸模組。 即使使用 XAML 標記延伸，XAML 永遠是合法的 XML。

使用標記延伸的其實只是以不同方式來表示項目的屬性。 XAML 標記延伸是通常是可辨識的大括號括住的屬性設定：

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

在大括號中的任何屬性設定為*一律*XAML 標記延伸。 不過，如您所見，XAML 標記延伸也可以參考而不使用大括號。

這篇文章會分成兩個部分：

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[使用 XAML 標記延伸](consuming.md)  

使用 Xamarin.Forms 中定義的 XAML 標記延伸。

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[建立 XAML 標記延伸](creating.md)

撰寫您自己自訂的 XAML 標記延伸模組。



## <a name="related-links"></a>相關連結

- [標記延伸模組 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Xamarin.Forms 通訊錄中的 XAML 標記延伸模組的章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
