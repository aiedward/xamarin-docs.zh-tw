---
title: 效果簡介
description: 效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。 本文簡介如何影響、 概述效果與自訂轉譯器之間的界限，並說明 PlatformEffect 類別。
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: d3fa958e999a10832d5fa15e4190077955b0e6df
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997372"
---
# <a name="introduction-to-effects"></a>效果簡介

_效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。本文簡介如何影響、 概述效果與自訂轉譯器之間的界限，並說明 PlatformEffect 類別。_

Xamarin.Forms[頁面、 版面配置和控制項](~/xamarin-forms/user-interface/controls/index.md)提供一個通用的 API，可說明跨平台行動裝置的使用者介面。 每個頁面、 版面配置和控制項不同的方式呈現每個平台使用`Renderer`類別，接著再建立原生控制項 （對應至 Xamarin.Forms 表示法），排列在畫面上，並將指定在共用的行為。程式碼。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是重量回應。 效果簡化此程序，允許更輕鬆地自訂每個平台的原生控制項。

效果在平台專屬專案中建立子類別化`PlatformEffect`使用控制項，然後按一下 效果將它們連結至適當的控制項，Xamarin.Forms.NET Standard 程式庫或共用程式庫專案中。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>為何要使用透過自訂轉譯器的效果？

影響簡化控制項的自訂、 可重複使用，且可以參數化，以進一步提高重複使用。

任何可透過影響的項目也可透過自訂轉譯器。 不過，自訂轉譯器會提供更多的彈性和自訂效果比。 下列指導方針，列出要選擇的效果，而不是自訂轉譯器的情況：

- 變更平台特定控制項的屬性將會達成所要的結果時，將建議的影響。
- 沒有需要的平台特定控制項的方法會覆寫時，需要自訂轉譯器。
- 將平台特定控制項實作 Xamarin.Forms 控制項需要時，需要自訂轉譯器。

## <a name="subclassing-the-platformeffect-class"></a>子類別化 PlatformEffect 類別

下表列出的命名空間`PlatformEffect`上每個平台和其屬性的型別類別：

|平台|命名空間|容器|控制項|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|檢視|
|通用 Windows 平台 (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

每個平台專屬`PlatformEffect`類別會公開下列屬性：

- `Container` – 參考用來實作版面配置中的平台專屬控制項。
- `Control` – 參考用來實作 Xamarin.Forms 控制項中的平台專屬控制項。
- `Element` – 參考所呈現的 Xamarin.Forms 控制項。

效果沒有容器、 控制項或項目，因為它們可以附加到任何項目，它們會附加至的型別資訊。 因此，如果效果附加到它並不支援的項目，它應該正常降級或擲回例外狀況。 不過， `Container`， `Control`，和`Element`屬性可以轉換成其實作的型別。 如需有關這些類型，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每個平台專屬`PlatformEffect`類別會公開下列方法，必須覆寫來實作的效果：

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) – 當影響附加至 Xamarin.Forms 控制項時呼叫。 這個方法，在每個平台特定效果類別中覆寫的版本可供執行自訂的控制項，以及例外狀況處理萬一效果無法套用至指定的 Xamarin.Forms 控制項。
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) – 效果卸離的 Xamarin.Forms 控制項時呼叫。 這個方法，在每個平台特定效果類別中覆寫的版本可供執行任何的效果清除作業，例如取消註冊事件處理常式。

颾魤 ㄛ`PlatformEffect`公開[ `OnElementPropertyChanged` ](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs))也可以覆寫的方法。 項目的屬性已變更時，會呼叫這個方法。 這個方法，在每個平台特定效果類別中覆寫的版本是回應 Xamarin.Forms 控制項上的可繫結的屬性變更的位置。 一律應進行變更之屬性的核取，因為此覆寫可呼叫多次。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
