---
title: XAML 命名空間建議的前置詞Xamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 71ae523f40f3f7529c12f853778404e224fbae30
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138134"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>XAML 命名空間建議的前置詞Xamarin.Forms

`XmlnsPrefixAttribute`控制項作者可以使用類別來指定與 xaml 命名空間相關聯的建議前置詞，以供 xaml 使用。 當支援 XAML 的物件樹狀結構序列化，或與具有 XAML 編輯功能的設計環境互動時，前置詞會很有用。 例如：

- XAML 文字編輯器可以使用 `XmlnsPrefixAttribute` 做為初始 XAML 命名空間對應的提示 `xmlns` 。
- XAML 設計環境可以使用，在將 `XmlnsPrefixAttribute` 物件從 [工具箱] 拖曳到視覺化設計介面時，加入 XAML 的對應。

建議的命名空間前置詞應在元件層級使用具有 `XmlnsPrefixAttribute` 下列兩個引數的函式定義：指定 XAML 命名空間識別碼的字串，以及指定建議前置詞的字串：

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

首碼應該使用短字串，因為前置詞通常會套用至來自 XAML 命名空間的所有序列化元素。 因此，前置字串長度對序列化 XAML 輸出的大小可能會有明顯的影響。

> [!NOTE]
> 一個以上 `XmlnsPrefixAttribute` 的可以套用至元件。 例如，如果您的元件定義了一個以上的 XAML 命名空間的類型，您可以為每個 XAML 命名空間定義不同的前置詞值。

## <a name="related-links"></a>相關連結

- [XAML 自訂命名空間結構描述](custom-namespace-schemas.md)
- [中的 XAML 命名空間Xamarin.Forms](namespaces.md)
