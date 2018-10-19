---
title: SkiaSharp 平台無關的範例
description: 本文件提供核心 SkiaSharp 概念的簡介。 特別是，它討論取得和 SKCanvas 上繪製。
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
author: davidbritch
ms.author: dabritch
ms.date: 10/03/2018
ms.openlocfilehash: 4d0e57b98a479112b9fdf4f9c503418f3966cc73
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615485"
---
# <a name="skiasharp-platform-independent-examples"></a>SkiaSharp 平台無關的範例

_這提供平台無關的簡介 SkiaSharp 的基本概念_

SkiaSharp 提供豐富且功能強大 2D 圖形 API，可用來呈現到 2D 的緩衝區。  您可以使用這些來實作自訂使用者介面項目和可整合到您的應用程式的 2D 圖形。 SkiaSharp 是.NET 繫結至[Skia](https://skia.org)程式庫會繼承此文件庫的強大的功能。

程式庫是跨平台目前可供[NuGet 套件](https://www.nuget.org/packages/SkiaSharp)，您可以將它加入您的專案的 NuGet 參考。

若要繪製，您的程式碼會建立`SkCanvas`其中描述在其中繪製作業會進行的介面。

## <a name="obtaining-an-skcanvas"></a>取得 SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>SKCanvas 的繪圖

`SKCanvas`使用繪圖模型精神類似於其他繪圖模型，您可能很熟悉，它會與選擇性的透明度通道使用的色彩，並可以繪製線條、 弧線、 文字和影像。

以下是幾個可以使用 SkiaSharp 許多不同的因素。  下列變數範例`canvas`屬於 SKCanvas 的型別。

### <a name="drawing-xamagon"></a>繪製 Xamagon

這個範例會繪製 Xamarin 的標誌 Xamagon:

```csharp
// clear the canvas / fill with white
canvas.Clear (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x2c, 0x3e, 0x50);
  paint.StrokeCap = SKStrokeCap.Round;

  // create the Xamagon path
  using (var path = new SKPath ()) {
    path.MoveTo (71.4311121f, 56f);
    path.CubicTo (68.6763107f, 56.0058575f, 65.9796704f, 57.5737917f, 64.5928855f, 59.965729f);
    path.LineTo (43.0238921f, 97.5342563f);
    path.CubicTo (41.6587026f, 99.9325978f, 41.6587026f, 103.067402f, 43.0238921f, 105.465744f);
    path.LineTo (64.5928855f, 143.034271f);
    path.CubicTo (65.9798162f, 145.426228f, 68.6763107f, 146.994582f, 71.4311121f, 147f);
    path.LineTo (114.568946f, 147f);
    path.CubicTo (117.323748f, 146.994143f, 120.020241f, 145.426228f, 121.407172f, 143.034271f);
    path.LineTo (142.976161f, 105.465744f);
    path.CubicTo (144.34135f, 103.067402f, 144.341209f, 99.9325978f, 142.976161f, 97.5342563f);
    path.LineTo (121.407172f, 59.965729f);
    path.CubicTo (120.020241f, 57.5737917f, 117.323748f, 56.0054182f, 114.568946f, 56f);
    path.LineTo (71.4311121f, 56f);
    path.Close ();

    // draw the Xamagon path
    canvas.DrawPath (path, paint);
  }
}
```

### <a name="drawing-text"></a>繪製文字

```csharp
// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// set up drawing tools
using (var paint = new SKPaint ()) {
  paint.TextSize = 64.0f;
  paint.IsAntialias = true;
  paint.Color = new SKColor (0x42, 0x81, 0xA4);
  paint.IsStroke = false;

  // draw the text
  canvas.DrawText ("Skia", 0.0f, 64.0f, paint);
}
```

### <a name="drawing-bitmaps"></a>繪製點陣圖

```csharp
Stream fileStream = File.OpenRead ("MyImage.png");

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  canvas.DrawBitmap(bitmap, SKRect.Create(Width, Height), paint);
}
```

### <a name="drawing-with-image-filters"></a>使用映像篩選器的繪圖

```csharp
Stream fileStream = File.OpenRead ("MyImage.png"); // open a stream to an image file

// clear the canvas / fill with white
canvas.DrawColor (SKColors.White);

// decode the bitmap from the stream
using (var stream = new SKManagedStream(fileStream))
using (var bitmap = SKBitmap.Decode(stream))
using (var paint = new SKPaint()) {
  // create the image filter
  using (var filter = SKImageFilter.CreateBlur(5, 5)) {
    paint.ImageFilter = filter;

    // draw the bitmap through the filter
    canvas.DrawBitmap(bitmap, SKRect.Create(width, height), paint);
  }
}
```

## <a name="more-information"></a>詳細資訊

使用 SkiaSharp 的詳細資訊可在[API 文件](https://docs.microsoft.com/dotnet/api/skiasharp)
