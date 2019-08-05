---
title: 效果簡介
description: 效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。 本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 PlatformEffect 類別。
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 8fae741f308a8269facef5ca84d34adb48868aec
ms.sourcegitcommit: c75c1d2132a4f46a7b38e454d5f24705165026bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2019
ms.locfileid: "68486016"
---
# <a name="introduction-to-effects"></a>效果簡介

_效果可讓您自訂每個平台上的原生控制項，通常用於小型的樣式變更。本文提供效果簡介、概述效果與自訂轉譯器之間的界限，並描述 PlatformEffect 類別。_

Xamarin.Forms [頁面、配置和控制項](~/xamarin-forms/user-interface/controls/index.md)提供一個通用 API 來描述跨平台行動裝置使用者介面。 系統會在每個平台上使用 `Renderer` 類別，以不同的方式轉譯每個頁面、配置和控制項，進而建立原生控制項 (對應至 Xamarin.Forms 表示方式)、將其排列在畫面上，然後新增在共用程式碼中指定的行為。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是繁重的回應方式。 效果可簡化此程序，讓您更輕鬆地在每個平台上自訂原生控制項。

系統會在平台特定專案中，透過將 `PlatformEffect` 控制項子類別化來建立效果，然後透過將其附加至 Xamarin.Forms .NET Standard 程式庫或共用程式庫專案中的適當控制項來取用效果。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>為何優先選擇效果而不是自訂轉譯器？

效果可簡化控制項的自訂、可重複使用，而且可參數化以近一步提高重複使用率。

任何可透過效果完成的工作，也能透過自訂轉譯器完成。 不過，自訂轉譯器所提供的彈性和自訂都遠高於效果。 下列指導方針列出優先選擇效果而不是自訂轉譯器的情況：

- 變更平台特定控制項的屬性會達成所要的結果時，建議使用效果。
- 需要覆寫平台特定控制項的方法時，需要自訂轉譯器。
- 需要取代實作 Xamarin.Forms 控制項的平台特定控制項時，需要自訂轉譯器。

## <a name="subclassing-the-platformeffect-class"></a>將 PlatformEffect 類別子類別化

下表列出每個平台上 `PlatformEffect` 類別的命名空間，以及其屬性的類型：

|平台|命名空間|容器|控制|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|ViewGroup|檢視|
|通用 Windows 平台 (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

每個平台特定的 `PlatformEffect` 類別會公開下列屬性：

- `Container` – 參考正在用來實作配置的平台特定控制項。
- `Control` – 參考正在用來實作 Xamarin.Forms 控制項的平台特定控制項。
- `Element` – 參考正在轉譯的 Xamarin.Forms 控制項。

效果不會有其所附加之容器、控制項或項目的相關類型資訊，因為它們可以附加至任何項目。 因此，當效果附加至不支援的項目時，它應該會正常降級或擲回例外狀況。 不過，`Container`、`Control` 和 `Element` 屬性可以轉換為其實作類型。 如需這些類型的詳細資訊，請參閱[轉譯器基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每個平台特定的 `PlatformEffect` 類別會公開下列方法，必須加以覆寫才能實作效果：

- [`OnAttached`](xref:Xamarin.Forms.Effect.OnAttached) – 當效果附加至 Xamarin.Forms 控制項時呼叫。 在每個平台專屬的效果類別中，這個方法覆寫的版本就是執行控制項自訂的位置，也可以用來處理無法將效果套用至指定 Xamarin.Forms 控制項時的例外狀況。
- [`OnDetached`](xref:Xamarin.Forms.Effect.OnDetached) – 當效果從 Xamarin.Forms 控制項中斷連結時呼叫。 在每個平台特定的效果類別中，此方法的覆寫版本是執行任何效果清除 (例如取消註冊事件處理常式) 的位置。

此外，`PlatformEffect` 會公開 [`OnElementPropertyChanged`](xref:Xamarin.Forms.PlatformEffect`2.OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs)) 方法，該方法也可能會遭到覆寫。 當項目的屬性變更時，就會呼叫此方法。 在每個平台特定的效果類別中，此方法的覆寫版本是回應 Xamarin.Forms 控制項上可繫結屬性變更的位置。 因為此覆寫會呼叫多次，所以請一律檢查變更的屬性。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
