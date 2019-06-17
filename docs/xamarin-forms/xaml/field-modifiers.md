---
title: 在 Xamarin.Forms 中 XAML 欄位修飾詞
description: X:fieldmodifier 命名空間屬性指定具名 XAML 項目產生欄位的存取層級。
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075294"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML 欄位修飾詞

_`x:FieldModifier`命名空間屬性指定具名 XAML 項目產生欄位的存取層級。_

## <a name="overview"></a>總覽

屬性的有效值為：

- `Public` – 指定的 XAML 項目所產生的欄位是`public`。
- `NotPublic` – 指定的 XAML 項目所產生的欄位是`internal`組件。

如果未設定屬性的值，所產生的欄位項目會`private`。

必須符合下列條件`x:FieldModifier`要處理的屬性：

- 最上層的 XAML 項目必須是有效`x:Class`。
- 目前的 XAML 項目具有`x:Name`指定。

下列 XAML 顯示的設定屬性的範例：

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> `x:FieldModifier`屬性不能用來指定 XAML 類別的存取層級。
