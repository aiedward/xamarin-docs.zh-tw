---
title: Xamarin.Forms 雙螢幕功能
description: 本指南說明 Xamarin.Forms 如何輕鬆地在雙螢幕裝置上啟用應用程式。
ms.prod: xamarin
ms.assetid: f9906e83-f8ae-48f9-997b-e1540b96ee8e
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
ms.openlocfilehash: 1f648297a608c592d90f2c70494ae4496fe73c0f
ms.sourcegitcommit: 07941cf9704ff88cf4087de5ebdea623ff54edb1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77145512"
---
# <a name="xamarinforms-dual-screen"></a>Xamarin.Forms 雙螢幕

![](~/media/shared/preview.png "This API is currently pre-release")

Surface Duo (Android) 和 Surface Neo (Windows 10X) 為觸控應用程式推出了新模式。 Xamarin.Forms 推出了 `TwoPaneView` 和 `DualScreenInfo`，讓您得以充分利用這些新體驗。

## <a name="dual-screen-patternsdesign-patternsmd"></a>[雙螢幕模式](design-patterns.md)

當考慮如何在雙螢幕裝置上最有效利用多個螢幕時，請參閱我們的模式指導方針，以找出最適合您應用程式介面的模式。

## <a name="twopaneviewtwopaneviewmd"></a>[TwoPaneView](twopaneview.md)

Xamarin.Forms `TwoPaneView` 類別 (因同名 UWP 控制項產生的靈感) 推出了以跨平台方式針對雙螢幕裝置最佳化的版面配置。

## <a name="dualscreeninfodual-screen-infomd"></a>[DualScreenInfo](dual-screen-info.md)

為了充分利用雙螢幕裝置，您可能會想要知道檢視所在的窗格、窗格大小、裝置方向、鉸鏈角度等。 `DualScreenInfo` 會提供這些資訊。

## <a name="dualscreenhelperdual-screen-helpermd"></a>[DualScreenHelper](dual-screen-helper.md)
`DualScreenHelper` 可讓您檢查平台是否支援以子母畫面形式開啟新視窗。 若是 Neo，這可讓您在裝置處於撰寫模式時，開啟會以觸控條顯示的視窗。