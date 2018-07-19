---
title: 使用 SkiaSharp 2D 繪圖
description: 本文件概述的跨平台 2D 與 SkiaSharp 繪圖。 它會連結到各種描述 SkiaSharp 的輔助線和各種 Api。
ms.prod: xamarin
ms.assetid: A8A61421-4544-422A-A7E0-9355C67DF21E
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: 0c8cbc14308c8c4131e5aaa2bcc0ddfa798af610
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130916"
---
# <a name="2d-drawing-with-skiasharp"></a>使用 SkiaSharp 2D 繪圖

SkiaSharp 提供功能強大的 C# API 進行 2D 圖形。 它由[Google Skia 文件庫](http://skia.org)，支援 Google Chrome、 Firefox 和 Android 的圖形的堆疊相同文件庫。

[![](images/ide-sml.png "SkiaSharp 提供功能強大的 C# API 進行 2D 圖形")](images/ide.png#lightbox)

SkiaSharp 是可攜式程式庫，並方便隨附[跨平台 NuGet 套件](https://www.nuget.org/packages/SkiaSharp)，並支援下列內建的平台： macOS、 Xamarin.Android、 Xamarin.iOS 和 Windows 桌面。

## <a name="introduction-to-skiasharpgraphics-gamesskiasharpintroductionmd"></a>[SkiaSharp 簡介](~/graphics-games/skiasharp/introduction.md)

SkiaSharp 和範例的核心概念的概觀來呈現圖形、 文字、 點陣圖、 程式碼，並使用映像篩選器。

## <a name="skiasharp-tutorials-for-xamarinformsxamarin-formsuser-interfacegraphicsskiasharpindexmd"></a>[SkiaSharp 適用於 Xamarin.Forms 的教學課程](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)

了解如何使用跨平台圖形呈現在 Xamarin.Forms 中：

- [繪圖基本概念](~/xamarin-forms/user-interface/graphics/skiasharp/basics/index.md)
  * [繪製簡單的圓形](~/xamarin-forms/user-interface/graphics/skiasharp/basics/circle.md)
  * [整合 Xamarin.Forms](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)
  * [像素與裝置無關的單位](~/xamarin-forms/user-interface/graphics/skiasharp/basics/pixels.md)
  * [基本動畫](~/xamarin-forms/user-interface/graphics/skiasharp/basics/animation.md)
  * [整合文字與圖形](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)
  * [點陣圖的基本概念](~/xamarin-forms/user-interface/graphics/skiasharp/basics/bitmaps.md)
- [線條和路徑](~/xamarin-forms/user-interface/graphics/skiasharp/paths/index.md)
  * [線條和筆觸端點](~/xamarin-forms/user-interface/graphics/skiasharp/paths/lines.md)
  * [路徑的基本概念](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)
  * [路徑填滿類型](~/xamarin-forms/user-interface/graphics/skiasharp/paths/fill-types.md)
  * [聚合線條和參數化的方程式](~/xamarin-forms/user-interface/graphics/skiasharp/paths/polylines.md)
  * [點和虛線](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)
  * [手指繪製](~/xamarin-forms/user-interface/graphics/skiasharp/paths/finger-paint.md)
- [轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/index.md)
  * [平移轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)
  * [縮放轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/scale.md)
  * [旋轉轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)
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
- [點陣圖](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/index.md)
  * [顯示點陣圖](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/displaying.md)
  * [建立和繪製點陣圖](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/drawing.md)
  * [裁剪點陣圖](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/cropping.md)
  * [分段的顯示的點陣圖](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/segmented.md)
  * [將點陣圖儲存至檔案](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/saving.md)
  * [存取的點陣圖像素位元](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/pixel-bits.md)
  * [以動畫顯示點陣圖](~/xamarin-forms/user-interface/graphics/skiasharp/bitmaps/animating.md)

## <a name="platform-specific-notesgraphics-gamesskiasharpplatformmd"></a>[平台專用備註](~/graphics-games/skiasharp/platform.md)

此頁面描述 SkiaSharp 的安裝指示，包括 iOS、 Android、 macOS 和 Windows 的不同平台上。

## <a name="api-documentationhttpsdeveloperxamarincomapinamespaceskiasharp"></a>[API 文件](https://developer.xamarin.com/api/namespace/SkiaSharp/)

您可以瀏覽[API 文件](https://developer.xamarin.com/api/namespace/SkiaSharp/)SkiaSharp 我們網站上的。

## <a name="work-in-progress"></a>進行中的工作

SkiaSharp 是進行中的工作，我們正與我們的社群共用。 雖然我們已繫結 Skia API 的重要部分，則會在完成剩餘工時。 我們使用的穩定的 C API 呈現 Skia，和我們的計劃是以繼續參與我們的 api 提供完整的涵蓋範圍的 Skia C 繫結的工作。

幫助我們引導我們繫結的工作，請保留的意見或建議問題在 GitHub 儲存機制[ http://github.com/mono/SkiaSharp ](http://github.com/mono/SkiaSharp)。
