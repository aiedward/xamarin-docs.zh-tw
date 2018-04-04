---
title: 效果簡介
description: 效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。 本文介紹效果、 概述效果和自訂轉譯器之間的界限和描述 PlatformEffect 類別。
ms.prod: xamarin
ms.assetid: 30CB8615-8F39-4762-BDB7-333D2B57D112
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 598136f43a23070d8bab04c18106738c9e6b0a52
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-effects"></a>效果簡介

_效果可讓自訂，每個平台的原生控制項，通常用於小型的樣式變更。本文介紹效果、 概述效果和自訂轉譯器之間的界限和描述 PlatformEffect 類別。_

Xamarin.Forms[頁面、 版面配置和控制項](~/xamarin-forms/user-interface/controls/index.md)顯示常見的 API，來描述跨平台行動裝置的使用者介面。 每個頁面、 版面配置和控制項以不同的方式上呈現每個平台使用`Renderer`類別接著會建立原生控制項 （對應至 Xamarin.Forms 表示法），其排列以在畫面上，並將指定的共用中的行為。程式碼。

開發人員可以實作自己的自訂 `Renderer` 類別，以自訂控制項的外觀及/或行為。 不過，實作自訂轉譯器類別來執行簡單的控制項自訂通常是重量回應。 效果簡化此程序，允許更輕鬆地自訂每個平台的原生控制項。

效果在平台專屬專案中建立子類別化`PlatformEffect`控制項，然後再效果所使用的連接到適當的控制項，在 Xamarin.Forms 可攜式類別程式庫 (PCL) 或共用的程式庫專案。

## <a name="why-use-an-effect-over-a-custom-renderer"></a>為什麼要透過自訂轉譯器使用效果？

影響簡化控制項的自訂、 可重複使用，且可以進一步提升重複使用參數化。

可達到含有效果的任何項目也可達到自訂轉譯器。 不過，自訂轉譯器會提供更多的彈性和自訂比效果。 下列指導方針，列出要在其中選擇效果透過自訂轉譯器的情況：

- 變更平台專屬控制項的屬性，以達到所要的結果時，將建議的效果。
- 需要覆寫的平台專屬控制項的方法時，需要自訂轉譯器。
- 需要取代平台專屬控制項實作 Xamarin.Forms 控制項時，需要自訂轉譯器。

## <a name="subclassing-the-platformeffect-class"></a>子類別化 PlatformEffect 類別

下表列出的命名空間`PlatformEffect`上每個平台和其屬性的型別類別：

|平台|命名空間|容器|控制項|
|--- |--- |--- |--- |
|iOS|Xamarin.Forms.Platform.iOS|UIView|UIView|
|Android|Xamarin.Forms.Platform.Android|檢視|檢視|
|Windows Phone 8.1|Xamarin.Forms.Platform.WinRT|FrameworkElement|FrameworkElement|
|通用 Windows 平台 (UWP)|Xamarin.Forms.Platform.UWP|FrameworkElement|FrameworkElement|

每個平台專屬`PlatformEffect`類別會公開下列屬性：

- `Container` – 參照特定平台控制項用來實作版面配置。
- `Control` – 參照特定平台控制項用來實作 Xamarin.Forms 控制項。
- `Element` – 參考 Xamarin.Forms 所呈現的控制項。

效果沒有容器、 控制項或項目，因為它們可以附加到任何項目，它們附加到的類型資訊。 因此，如果效果附加到不支援的項目必須正常降級或擲回例外狀況。 不過， `Container`， `Control`，和`Element`屬性都可以轉換成其實作的型別。 如需有關這些類型，請參閱[轉譯器的基底類別和原生控制項](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md)。

每個平台專屬`PlatformEffect`類別會公開下列方法，必須覆寫來實作效果：

- [`OnAttached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnAttached()/) – 當影響附加至 Xamarin.Forms 控制項時呼叫。 覆寫的版本中每個平台專屬效果類別，這個方法，是控制項的執行自訂，以及例外狀況處理萬一效果無法套用至指定的 Xamarin.Forms 控制項的位置。
- [`OnDetached`](https://developer.xamarin.com/api/member/Xamarin.Forms.Effect.OnDetached()/) – 當效果中斷 Xamarin.Forms 控制項時呼叫。 覆寫的版本中每個平台專屬效果類別，這個方法，是要執行任何效果清除作業，例如取消註冊事件處理常式的地方。

此外，`PlatformEffect`公開[ `OnElementPropertyChanged` ](https://developer.xamarin.com/api/member/Xamarin.Forms.PlatformEffect%3CTContainer,TControl%3E.OnElementPropertyChanged/p/System.ComponentModel.PropertyChangedEventArgs/)方法，也可以覆寫。 元素的屬性變更時，會呼叫這個方法。 這個方法，每個平台專屬效果類別中覆寫的版本是 Xamarin.Forms 控制項上的可繫結的屬性變更回應的位置。 變更的屬性應該一律會進行檢查，為此覆寫可呼叫多次。


## <a name="related-links"></a>相關連結

- [自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
