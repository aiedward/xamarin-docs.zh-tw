---
title: "XAML 標記延伸"
description: "擴充屬性會設定從哪一個 XAML 來源的範圍"
ms.topic: article
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: 11889115b65608c750690c33052a9c86f7081e25
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-markup-extensions"></a>XAML 標記延伸

XAML 標記延伸，有助於在擴充的功能和彈性的 XAML，允許項目屬性設定為常值文字字串以外的來源。

例如，通常您設定`Color`屬性`BoxView`如下所示：

```xaml
<BoxView Color="Blue" />
```

或者，您可以將它設定為十六進位的 RGB 色彩值：

```xaml
<BoxView Color="#FF0080" />
```

在任一情況下，文字字串設定為`Color`屬性轉換成`Color`值[ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/)類別。

您可能會改為想要設定`Color`屬性從資源字典中所儲存的值或您已建立，類別的靜態屬性的值或型別的屬性`Color`的另一個元素在頁面上，或從建構分隔色調、 飽和度和亮度的值。

所有這些選項都可以使用 XAML 標記延伸的。 但不要讓片語 「 標記延伸"嚇到您： XAML 標記延伸是*不*XML 延伸模組。 即使使用 XAML 標記延伸，XAML 一律是合法的 XML。 

使用標記延伸的其實只是以不同方式來表示項目的屬性。 XAML 標記延伸是通常在大括號括住的屬性設定識別：

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

在大括號中的任何屬性設定為*一律*XAML 標記延伸。 不過，您會發現，XAML 標記延伸也可以參考而不會使用大括號。

這份文件分割成兩個部分：

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[使用 XAML 標記延伸](consuming.md)  

使用 Xamarin.Forms 中定義的 XAML 標記延伸。

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[建立 XAML 標記延伸](creating.md) 

撰寫您自己自訂的 XAML 標記延伸。



## <a name="related-links"></a>相關連結

- [標記延伸 （範例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Xamarin.Forms 通訊錄中的 XAML 標記延伸的章節](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [資源字典](~/xamarin-forms/xaml/resource-dictionaries.md)
- [動態樣式](~/xamarin-forms/user-interface/styles/dynamic.md)
- [資料繫結](~/xamarin-forms/app-fundamentals/data-binding/index.md)
