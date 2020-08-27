---
title: Xamarin.Forms 行為
description: 行為可讓您將功能新增至使用者介面控制項，而不需將其歸類於子類別。 行為以程式碼編寫，並以 XAML 或程式碼的形式新增至控制項。
ms.prod: xamarin
ms.assetid: 42E32AD7-8E3B-48B3-B402-E75B758DA913
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d917d7d6421cfae7fc877c81023a835573fa99b1
ms.sourcegitcommit: a003b036f6fb83818e2ecc9c72a641e3aeb373bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964619"
---
# <a name="no-locxamarinforms-behaviors"></a>Xamarin.Forms 行為

_行為可讓您將功能新增至使用者介面控制項，而不需要將其設為子類別。行為是以程式碼撰寫，並新增至 XAML 或程式碼中的控制項。_

## <a name="introduction-to-behaviors"></a>[行為簡介](introduction.md)

行為可讓您實作您經常需編寫為程式碼後置的程式碼，因為此程式碼直接與控制項的 API 互動，透過此方式可以精確附加至控制項。 本文提供行為簡介。

## <a name="attached-behaviors"></a>[附加的行為](attached.md)

附加的行為是具有一或多個附加屬性的 `static` 類別。 本文示範如何建立和使用附加的行為。

## <a name="no-locxamarinforms-behaviors"></a>[Xamarin.Forms 行為](creating.md)

Xamarin.Forms 行為是藉由衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或類別來建立 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 。 本文示範如何建立和使用 Xamarin.Forms 行為。

## <a name="reusable-effectbehavior"></a>[可重複使用的 EffectBehavior](effect-behavior.md)

對於將效果新增至控制項，行為是很實用的方法，可從程式碼後置檔案中移除以定案效果方式處理的程式碼。 本文將示範如何建立和使用 Xamarin.Forms 行為，以將效果新增至控制項。
