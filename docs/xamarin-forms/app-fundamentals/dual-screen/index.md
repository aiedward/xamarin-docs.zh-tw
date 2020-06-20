---
title: Xamarin.Forms雙畫面
description: 本指南說明如何建立 Xamarin.Forms 雙畫面裝置的應用程式。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aeaaeb732adaea45446d6baf833027801abf4d2a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138901"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms雙畫面

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) 和 Surface Neo (Windows 10X) 為觸控應用程式推出了新模式。 Xamarin.Forms包含 `TwoPaneView` 和 `DualScreenInfo` 類別，讓您可以為這些裝置開發應用程式。

## <a name="dual-screen-design-patterns"></a>[雙畫面設計模式](design-patterns.md)

當您在考慮如何於雙螢幕裝置上最有效利用多個螢幕時，請參閱此模式指導方針，以找出最適合您應用程式介面的模式。

## <a name="dual-screen-layout"></a>[雙螢幕版面配置](twopaneview.md)

Xamarin.Forms `TwoPaneView` 由相同名稱的 UWP 控制項所啟發的類別，是針對雙畫面裝置優化的跨平台佈建。

## <a name="dual-screen-device-capabilities"></a>[雙螢幕裝置功能](dual-screen-info.md)

`DualScreenInfo` 類別可讓您決定檢視所在的窗格、窗格大小、裝置方向、鉸鏈角度等。

## <a name="dual-screen-platform-helpers"></a>[雙螢幕平台協助程式](dual-screen-helper.md)

`DualScreenHelper` 類別可讓您確認平台是否支援以子母畫面形式開啟新視窗。 若為 Neo，這可讓您在裝置處於撰寫模式時，開啟會以觸控條顯示的視窗。

## <a name="dual-screen-triggers"></a>[雙螢幕觸發程序](triggers.md)

[`Xamarin.Forms.DualScreen`](xref:Xamarin.Forms.DualScreen)命名空間包含兩個狀態觸發程式，會 [`VisualState`](xref:Xamarin.Forms.VisualState) 在附加的版面配置或視窗的視圖模式變更時觸發變更。
