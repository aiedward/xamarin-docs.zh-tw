---
title: ''
description: 本文說明如何使用 Xamarin.Forms xaml 標記延伸，藉由允許從常值文字字串以外的來源設定元素屬性，來擴充 xaml 的能力和彈性。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 568cffc335f28b1a47f3278ad061d851ebef84b6
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130386"
---
# <a name="xaml-markup-extensions"></a>XAML 標記延伸

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

XAML 標記延伸可讓您從常值文字字串以外的來源設定元素屬性，以協助擴充 XAML 的能力和彈性。

例如，您通常會將的 `Color` 屬性設定 `BoxView` 如下：

```xaml
<BoxView Color="Blue" />
```

或者，您可以將它設定為十六進位 RGB 色彩值：

```xaml
<BoxView Color="#FF0080" />
```

不論是哪一種情況，設定為屬性的文字字串 `Color` 都會 `Color` 由類別轉換成值 [`ColorTypeConverter`](xref:Xamarin.Forms.ColorTypeConverter) 。

您可能想要改為 `Color` 從儲存在資源字典中的值，或從您所建立之類別的靜態屬性值，或從頁面上另一個元素類型的屬性，或從 `Color` 個別的色調、飽和度和亮度值來設定屬性。

所有這些選項都可以使用 XAML 標記延伸。 但是別讓片語「標記延伸」怪嚇人您： XAML 標記延伸並*不*是 XML 的延伸。 即使使用 XAML 標記延伸，XAML 永遠都是合法的 XML。

標記延伸其實只是一種不同的方式來表達元素的屬性。 XAML 標記延伸通常是以大括弧括住的屬性設定來識別：

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

大括弧內的任何屬性設定*一律*是 XAML 標記延伸。 不過，如您所見，您也可以參考 XAML 標記延伸，而不使用大括弧。

本文分成兩個部分：

## <a name="consuming-xaml-markup-extensions"></a>[使用 XAML 標記延伸](consuming.md)  

使用中定義的 XAML 標記延伸 Xamarin.Forms 。

## <a name="creating-xaml-markup-extensions"></a>[建立 XAML 標記延伸](creating.md)

撰寫您自己的自訂 XAML 標記延伸。

## <a name="related-links"></a>相關連結

- [標記延伸（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [書籍中的 XAML 標記延伸章節 Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料系結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
