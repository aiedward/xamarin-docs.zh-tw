---
title: 行為的簡介
description: 行為可讓您將功能新增至使用者介面控制項，而它們不必子類別。 相反地，功能是行為類別中實作，並附加至控制項，如同它是控制項本身的一部分。 這篇文章提供行為的簡介。
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 176f41d4b7349af2cf7cc49de8ba0789ad2f8c11
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995810"
---
# <a name="introduction-to-behaviors"></a>行為的簡介

_行為可讓您將功能新增至使用者介面控制項，而它們不必子類別。相反地，功能是行為類別中實作，並附加至控制項，如同它是控制項本身的一部分。這篇文章提供行為的簡介。_

行為可讓您實作程式碼，您通常必須撰寫程式碼後置，因為它直接互動的方式，將它簡潔附加到控制項，並跨多個應用程式封裝以供重複使用控制項的 API。 它們可以用來提供完整控制項的功能，例如：

- 新增電子郵件驗證程式，以[ `Entry` ](xref:Xamarin.Forms.Entry)。
- 建立使用點選 筆勢辨識器的評等控制項。
- 控制動畫。
- 加入至控制項的影響。

行為也會啟用更進階的案例。 中的內容*指揮*，行為是一種實用的方法，如控制項連接至命令。 此外，它們可用來使命令無法正確命令與互動的控制項產生關聯。 比方說，它們可以用來叫用命令，以回應事件引發。

Xamarin.Forms 可支援兩種不同的樣式的行為：

- **Xamarin.Forms 表現方式**– 類別衍生自[ `Behavior` ](xref:Xamarin.Forms.Behavior)或是[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別，其中`T`是控制項的型別行為應套用。 如需有關 Xamarin.Forms 表現方式的詳細資訊，請參閱 < [Xamarin.Forms 表現方式](~/xamarin-forms/app-fundamentals/behaviors/creating.md)並[可重複使用行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。
- **附加行為**–`static`具有一或多個附加屬性的類別。 如需有關附加行為的詳細資訊，請參閱[附加行為](~/xamarin-forms/app-fundamentals/behaviors/attached.md)。

本指南著重 Xamarin.Forms 表現方式，因為它們是行為建構的慣用的方法。



## <a name="related-links"></a>相關連結

- [行為](xref:Xamarin.Forms.Behavior)
- [行為&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
