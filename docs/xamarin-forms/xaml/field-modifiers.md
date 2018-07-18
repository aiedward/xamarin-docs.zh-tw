---
title: Xamarin.Forms 中的 XAML 欄位修飾詞
description: X:fieldmodifier 命名空間屬性會指定產生的具名 XAML 項目欄位的存取層級。
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209501"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Xamarin.Forms 中的 XAML 欄位修飾詞

_`x:FieldModifier`命名空間屬性會指定產生的具名 XAML 項目欄位的存取層級。_

## <a name="overview"></a>總覽

屬性的有效值為：

- `Public` -指定產生的 XAML 項目欄位是`public`。
- `NotPublic` -指定產生的 XAML 項目欄位是`internal`組件。

如果您未設定屬性的值，將會產生的欄位項目`private`。

必須符合下列條件`x:FieldModifier`来處理的屬性：

- 最上層的 XAML 項目必須是有效`x:Class`。
- 目前的 XAML 項目具有`x:Name`指定。

下列 XAML 顯示設定屬性的範例：

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> `x:FieldModifier`屬性不能用來指定 XAML 類別的存取層級。
