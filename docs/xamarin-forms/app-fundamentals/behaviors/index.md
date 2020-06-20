---
title: Xamarin.Forms問題
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
ms.openlocfilehash: 83952982bd163725fb931c860cac3e267726315c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135807"
---
# <a name="xamarinforms-behaviors"></a>Xamarin.Forms問題

_行為可讓您將功能新增至使用者介面控制項，而不需要將其設為子類別。行為是以程式碼撰寫，並加入至 XAML 或程式碼中的控制項。_

## <a name="introduction-to-behaviors"></a>[行為簡介](introduction.md)

行為可讓您實作您經常需編寫為程式碼後置的程式碼，因為此程式碼直接與控制項的 API 互動，透過此方式可以精確附加至控制項。 本文提供行為簡介。

## <a name="attached-behaviors"></a>[附加的行為](attached.md)

附加的行為是具有一或多個附加屬性的 `static` 類別。 本文示範如何建立和使用附加的行為。

## <a name="xamarinforms-behaviorscreatingmd"></a>[Xamarin.Forms問題](creating.md)

Xamarin.Forms行為是藉由衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或類別來建立 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 。 本文示範如何建立和使用 Xamarin.Forms 行為。

## <a name="reusable-behaviors"></a>[可重複使用的行為](reusable/index.md)

行為可跨多個應用程式重複使用。 這些文章說明如何建立實用的行為，藉此執行常用功能。
