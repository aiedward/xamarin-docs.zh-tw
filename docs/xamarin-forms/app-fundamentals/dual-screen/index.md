---
title: Xamarin.Forms 雙螢幕
description: 本指南說明如何為雙螢幕裝置建立 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 41a7ef4e447bb71582264b4e73629566d3ffd4e7
ms.sourcegitcommit: 524fc148bad17272bda83c50775771daa45bfd7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77480516"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms 雙螢幕

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) 和 Surface Neo (Windows 10X) 為觸控應用程式推出了新模式。 Xamarin.Forms 包含 `TwoPaneView` 與 `DualScreenInfo` 類別，可讓您為這些裝置開發應用程式。

## <a name="dual-screen-patterns"></a>[雙螢幕模式](design-patterns.md)

當您在考慮如何於雙螢幕裝置上最有效利用多個螢幕時，請參閱此模式指導方針，以找出最適合您應用程式介面的模式。

## <a name="twopaneview"></a>[TwoPaneView](twopaneview.md)

Xamarin.Forms `TwoPaneView` 類別 (因同名 UWP 控制項產生的靈感) 為針對雙螢幕裝置最佳化的跨平台版面配置。

## <a name="dualscreeninfo"></a>[DualScreenInfo](dual-screen-info.md)

`DualScreenInfo` 類別可讓您決定檢視所在的窗格、窗格大小、裝置方向、鉸鏈角度等。

## <a name="dualscreenhelper"></a>[DualScreenHelper](dual-screen-helper.md)

`DualScreenHelper` 類別可讓您確認平台是否支援以子母畫面形式開啟新視窗。 若為 Neo，這可讓您在裝置處於撰寫模式時，開啟會以觸控條顯示的視窗。
