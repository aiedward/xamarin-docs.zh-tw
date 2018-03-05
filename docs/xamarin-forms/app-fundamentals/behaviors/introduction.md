---
title: "行為簡介"
description: "行為可讓您將功能加入至使用者介面控制項，而它們不必子類別。 相反地，功能是行為類別中實作，而且附加到控制項，如同它是控制項本身的一部分。 本文章提供行為的簡介。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 9a92a80bae49a20b276e0d985845fbf08fe92bec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-behaviors"></a>行為簡介

_行為可讓您將功能加入至使用者介面控制項，而它們不必子類別。相反地，功能是行為類別中實作，而且附加到控制項，如同它是控制項本身的一部分。本文章提供行為的簡介。_

行為可讓您實作您通常具有寫入做為程式碼後置，因為它直接互動的方式，它可以精確地附加到控制項和跨多個應用程式封裝以供重複使用控制項的應用程式開發介面的程式碼。 它們可以用來提供完整控制項的功能，例如：

- 新增電子郵件驗證程式[ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/)。
- 建立評等控制項使用點選筆勢辨識器。
- 控制動畫。
- 加入至控制項的效果。

行為也會啟用更進階的案例。 內容中*指揮*，行為會很實用的控制項連接至命令的方法。 此外，它們可以用來將命令與過去並非設計來與命令互動的控制項產生關聯。 例如，它們可以用來叫用命令，以回應事件引發。

Xamarin.Forms 可支援兩種不同的樣式的行為：

- **Xamarin.Forms 行為**– 類別衍生自[ `Behavior` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)或[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別，其中`T`是的控制項類型的行為應套用。 如需 Xamarin.Forms 行為的詳細資訊，請參閱[Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重複使用行為](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。
- **附加行為**–`static`具有一或多個附加屬性的類別。 如需附加行為的詳細資訊，請參閱[附加行為](~/xamarin-forms/app-fundamentals/behaviors/attached.md)。

本指南著重 Xamarin.Forms 行為，因為它們是行為建構慣用的方法。



## <a name="related-links"></a>相關連結

- [Behavior](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行為<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
