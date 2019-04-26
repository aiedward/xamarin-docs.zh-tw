---
title: 建議在 Xamarin.Forms 中的前置詞的 XAML 命名空間
description: XmlnsPrefixAttribute 類別可以供控制項作者，以指定要與 XAML 用法的 XAML 命名空間，產生關聯的建議前置詞。
ms.prod: xamarin
ms.assetid: 7B315BEC-7A35-48F4-A9C7-EF40255E95FF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2019
ms.openlocfilehash: 33f18b3f9c9ddb6ab31ca92e2f192ffad783ec0c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075090"
---
# <a name="xaml-namespace-recommended-prefixes-in-xamarinforms"></a>建議在 Xamarin.Forms 中的前置詞的 XAML 命名空間

`XmlnsPrefixAttribute`控制項作者會使用類別來指定要與 XAML 用法的 XAML 命名空間，產生關聯的建議前置詞。 前置詞時，支援物件樹狀結構序列化至 XAML，或與設計環境互動時具有 XAML 編輯功能。 例如: 

- 可以使用 XAML 文字編輯器`XmlnsPrefixAttribute`做為初始的 XAML 命名空間的提示`xmlns`對應。
- 可以使用 XAML 設計環境`XmlnsPrefixAttribute`將對應新增到 XAML 中，拖曳物件從 [工具箱] 拖曳至視覺化設計介面上的時。

建議的命名空間前置詞應該定義的組件層級`XmlnsPrefixAttribute`兩個引數的建構函式： 指定的 XAML 命名空間識別項的字串，並指定建議的前置詞的字串：

```csharp
[assembly: XmlnsPrefix("http://xamarin.com/schemas/2014/forms", "xf")]
```

前置詞應該使用短字串，因為前置詞通常會套用至所有已序列化的項目來自於 XAML 命名空間。 因此，前置詞字串長度可以有明顯的影響，序列化的 XAML 輸出的大小。

> [!NOTE]
> 多個`XmlnsPrefixAttribute`可以套用至組件。 例如，如果您有定義一個以上的 XAML 命名空間類型的組件時，您可以定義每個 XAML 命名空間的不同前置詞值。

## <a name="related-links"></a>相關連結

- [XAML 自訂命名空間結構描述](custom-namespace-schemas.md)
- [在 Xamarin.Forms 中的 XAML 命名空間](namespaces.md)
