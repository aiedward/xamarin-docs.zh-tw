---
title: 2D 繪圖
description: 跨平台 2D 繪圖 SkiaSharp 與
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: f1a40b48ecfb7244aae77cf1b4110ae53490d98c
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="2d-drawing"></a>2D 繪圖

SkiaSharp 提供功能強大的 C# API 進行 2D 圖形。 它由[Google Skia 程式庫](http://skia.org)，提供 Google Chrome、 Firefox 和 Android 的圖形的堆疊相同文件庫。

[![](images/ide-sml.png "SkiaSharp 提供功能強大的 C# API，以進行 2D 圖形")](images/ide.png#lightbox)

SkiaSharp 是可攜式程式庫，並方便地隨附[跨平台的 NuGet 封裝](https://www.nuget.org/packages/SkiaSharp)，並支援下列平台現成： macOS Xamarin.Android、 Xamarin.iOS、 及 Windows 桌面。

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[SkiaSharp 簡介](~/graphics-games/skiasharp/introduction.md)

SkiaSharp 和範例的核心概念的概觀來呈現圖形、 文字、 點陣圖、 程式碼，並使用映像篩選器。

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[Xamarin.Forms SkiaSharp 教學課程](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

了解如何使用跨平台圖形，在 Xamarin.Forms 中呈現：

- [繪圖基本概念](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [繪製簡單的圓形](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [整合 Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [像素與裝置無關的單位](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [基本動畫](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [將文字和圖形的整合](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [點陣圖的基本概念](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [線條和路徑](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [線條和筆觸端點](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [路徑的基本概念](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [路徑填滿類型](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [聚合線條和參數化的方程式](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [句點和連字號](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [手指繪製](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [轉換轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [小數位數轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [旋轉的轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
  * [傾斜轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/skew.md)
  * [矩陣轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)
  * [觸控操作](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)
  * [非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)
  * [3D 旋轉](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)
- [曲線和路徑](~/xamarin-forms/user-interface/graphics/skiasharp/curves/index.md)
  * [繪製弧形的三種方式](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)
  * [三種類型的貝茲曲線](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)
  * [SVG 路徑資料](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)
  * [使用路徑和地區裁剪](~/xamarin-forms/user-interface/graphics/skiasharp/curves/clipping.md)
  * [路徑效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)
  * [路徑及文字](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)
  * [路徑資訊與列舉](~/xamarin-forms/user-interface/graphics/skiasharp/curves/information.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[平台專用備註](~/graphics-games/skiasharp/platform.md)

此頁面描述 SkiaSharp 的安裝指示，包括 iOS、 Android、 macOS 和 Windows 的不同平台上。

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[應用程式開發介面文件](https://developer.xamarin.com/api/namespace/SkiaSharp/)

您可以瀏覽[API 文件](https://developer.xamarin.com/api/namespace/SkiaSharp/)SkiaSharp 我們的網站上的。

## <a name="work-in-progress"></a>進行中的工作

SkiaSharp 是進行中，我們的共用對象我們的社群。 雖然我們已經繫結 Skia API 的重要部分，請完成剩餘工時。 我們使用由 Skia，顯示的 C API 極為穩定，而且我們計劃也會繼續執行參與 Skia api 提供完整的涵蓋範圍的 C 繫結至工作。

若要幫助我們引導我們的繫結工作，請保留註解或建議問題 GitHub 儲存機制上[ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp)。
