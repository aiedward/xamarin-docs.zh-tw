---
title: 行為簡介
description: 行為可讓您將功能新增至使用者介面控制項，而不需將其歸類為子類別。 相反地，功能會在行為類別中實作並附加至控制項，如同控制項本身的一部分。 本文提供行為簡介。
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: d62ba6b025b2fe9865df8279a5e98eba254bb5a2
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70772053"
---
# <a name="introduction-to-behaviors"></a>行為簡介

_行為允許您向使用者介面控件添加功能,而無需對它們進行子類。相反,該功能在行為類中實現,並附加到控件,就好像它是控件本身的一部分一樣。本文介紹了行為。_

行為可讓您實作您通常必須撰寫為程式碼後置的程式碼，因為此程式碼直接與控制項的 API 互動，所以透過此方式可以精確附加至控制項並封裝，以供多個應用程式重複使用。 可用來提供各種功能給控制項，例如：

- 將電子郵件驗證器加入[`Entry`](xref:Xamarin.Forms.Entry)。
- 建立使用點選手勢辨識器的評等控制項。
- 控制動畫。
- 將效果新增至控制項。

行為也可允許更進階的情節。 在「命令」** 內容中，行為是實用的方法，可將控制項連接到命令。 此外，可用來將命令與未設計於與命令互動的控制項建立關聯。 例如，可用來叫用命令以回應事件引發。

Xamarin.Forms 支援兩種不同的行為樣式：

- **Xamarin.Forms 行為** – 衍生自 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別的類別，其中 `T` 是應該套用行為的控制項類型。 如需 Xamarin.Forms 行為的詳細資訊，請參閱 [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重複使用的行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。
- **附加行為**`static`— 具有一個或多個附加屬性的類。 如需附加行為的詳細資訊，請參閱[附加的行為](~/xamarin-forms/app-fundamentals/behaviors/attached.md)。

此指南著重於 Xamarin.Forms 行為，因為這是行為建構的慣用方法。

## <a name="related-links"></a>相關連結

- [行為](xref:Xamarin.Forms.Behavior)
- [行為&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
