---
title: ''
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
ms.openlocfilehash: a891ec70f6f83984ed463fe914442758bdf57a2e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139304"
---
# <a name="introduction-to-effects"></a>效果簡介

_效果可讓您自訂每個平臺上的原生控制項，通常用於小型的樣式變更。本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 PlatformEffect 類別。_

Xamarin.Forms[頁面、版面配置和控制項](~/xamarin-forms/user-interface/controls/index.md)呈現通用的 API，以描述跨平臺行動使用者介面。 每個頁面、版面配置和控制項都會使用類別，以不同的方式轉譯在每個平臺上，然後再 `Renderer` 建立原生控制項（對應至 Xamarin.Forms 標記法），將其排列在畫面上，然後加入在共用程式碼中指定的行為。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是繁重的回應方式。 效果可簡化此程序，讓您更輕鬆地在每個平台上自訂原生控制項。

在平臺特定專案中，會藉由將控制項子類別化來建立效果 `PlatformEffect` ，然後藉由將其附加至 .NET Standard 程式庫 Xamarin.Forms 或共用程式庫專案中的適當控制項來取用效果。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>為何優先選擇效果而不是自訂轉譯器？

效果可簡化控制項的自訂、可重複使用，而且可參數化以近一步提高重複使用率。

任何可透過效果完成的工作，也能透過自訂轉譯器完成。 不過，自訂轉譯器所提供的彈性和自訂都遠高於效果。 下列指導方針列出優先選擇效果而不是自訂轉譯器的情況：

- 變更平台特定控制項的屬性會達成所要的結果時，建議使用效果。
- 需要覆寫平台特定控制項的方法時，需要自訂轉譯器。
- 當需要取代用來執行控制項的平臺特定控制項時，需要自訂轉譯器 Xamarin.Forms 。

## <a name="subclassing-the-platformeffect-class"></a>將 PlatformEffect 類別子類別化

下表列出每個平台上 `PlatformEffect` 類別的命名空間，以及其屬性的類型：

|平台|命名空間|容器|控制|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.平臺 iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform. Android|ViewGroup|檢視|
|通用 Windows 平台 (UWP)|Xamarin.Forms.平臺. UWP|FrameworkElement|FrameworkElement|

每個平台特定的 `PlatformEffect` 類別會公開下列屬性：

- `Container` – 參考正在用來實作配置的平台特定控制項。
- `Control`–參考用來執行控制項的平臺特定控制項 Xamarin.Forms 。
- `Element`–參考 Xamarin.Forms 正在呈現的控制項。

效果不會有其所附加之容器、控制項或項目的相關類型資訊，因為它們可以附加至任何項目。 因此，當效果附加至不支援的項目時，它應該會正常降級或擲回例外狀況。 不過，`Container`、`Control` 和 `Element` 屬性可以轉換為其實作類型。 如需這些類型的詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每個平台特定的 `PlatformEffect` 類別會公開下列方法，必須加以覆寫才能實作效果：

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached)–當效果附加至控制項時呼叫 Xamarin.Forms 。 在每個平臺特定的效果類別中，此方法的覆寫版本是執行控制項自訂的位置，以及例外狀況處理，以防效果無法套用至指定的 Xamarin.Forms 控制項。
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached)–從控制項卸離效果時呼叫 Xamarin.Forms 。 在每個平台特定的效果類別中，此方法的覆寫版本是執行任何效果清除 (例如取消註冊事件處理常式) 的位置。

此外，也會 `PlatformEffect` 公開 [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) 方法，這也可以覆寫。 當項目的屬性變更時，就會呼叫此方法。 在每個平臺特定的效果類別中，此方法的覆寫版本是回應控制項上可系結屬性變更的位置 Xamarin.Forms 。 因為此覆寫會呼叫多次，所以請一律檢查變更的屬性。

## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
