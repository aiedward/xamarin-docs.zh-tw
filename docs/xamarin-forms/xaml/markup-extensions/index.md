---
title: XAML 標記延伸
description: 本文說明如何使用 Xamarin.Forms xaml 標記延伸，藉由允許從常值文字字串以外的來源設定專案屬性，以擴充 xaml 的功能和彈性。
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 98eb35697aee9022a837a7b0b531edb0c53b2239
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562141"
---
# <a name="xaml-markup-extensions"></a>XAML 標記延伸

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 標記延伸模組可讓您從常值文字字串以外的來源設定專案屬性，以擴充 XAML 的功能和彈性。

例如，您通常會將的 `Color` 屬性設定 `BoxView` 如下：

```xaml
<BoxView Color="Blue" />
```

或者，您可以將它設定為十六進位的 RGB 色彩值：

```xaml
<BoxView Color="#FF0080" />
```

在任一種情況下，設定為屬性的文字字串 `Color` 會 `Color` 由類別轉換成值 [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) 。

您可能會想要改為從 `Color` 儲存在資源字典中的值，或從您所建立之類別的靜態屬性值，或從頁面上另一個專案類型的屬性，或從 `Color` 個別的色調、飽和度和亮度值來設定屬性。

您可以使用 XAML 標記延伸來使用這些選項。 但是不要讓片語「標記延伸」怪嚇人您： XAML 標記延伸 *不* 是 XML 的延伸。 即使使用 XAML 標記延伸，XAML 一律是合法的 XML。

標記延伸其實只是表達元素屬性的不同方法。 XAML 標記延伸通常是以大括弧括住的屬性設定來識別：

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

大括弧中的任何屬性設定 *一律* 是 XAML 標記延伸。 不過，如您所見，您也可以在不使用大括弧的情況下參考 XAML 標記延伸。

本文分為兩個部分：

## <a name="consuming-xaml-markup-extensions"></a>[使用 XAML 標記延伸](consuming.md)  

使用中定義的 XAML 標記延伸 Xamarin.Forms 。

## <a name="creating-xaml-markup-extensions"></a>[建立 XAML 標記延伸](creating.md)

撰寫您自己的自訂 XAML 標記延伸。

## <a name="related-links"></a>相關連結

- [標記延伸 (範例) ](/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [書籍中的 XAML 標記延伸章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)