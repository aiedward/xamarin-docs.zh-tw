---
title: SkiaSharp 簡介
description: 這提供 SkiaSharp 背後的概念簡介
ms.prod: xamarin
ms.assetid: 19506F08-2603-465E-A806-6BD01638DE90
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 09/14/2017
ms.openlocfilehash: b16792a506b131be07c52275e3f40cbb8d5fca94
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="an-introduction-to-skiasharp"></a>SkiaSharp 簡介

_這提供 SkiaSharp 背後的概念簡介_

SkiaSharp 提供功能強大且豐富 2D 圖形 API，可用來轉譯成 2D 緩衝區。  您可以使用這些來實作自訂使用者介面項目與 2D 圖形可以被包含於您的應用程式。  SkiaSharp 是.NET 繫結至[Skia](https://skia.org)程式庫和繼承的功能和此媒體櫃的電源。

媒體櫃是跨平台目前可供[NuGet 封裝](https://www.nuget.org/packages/SkiaSharp)，您可以將它加入您的專案中加入 NuGet 參考。

若要繪製，您的程式碼會建立`SkCanvas`用來描述在其中繪製作業會發生的介面。

## <a name="obtaining-an-skcanvas"></a>取得 SKCanvas

```csharp
using (var surface = SKSurface.Create (width: 640, height: 480, SKImageInfo.PlatformColorType, SKAlphaType.Premul)) {
    SKCanvas myCanvas = surface.Canvas;

    // Your drawing code goes here.
}
```

## <a name="drawing-on-skcanvas"></a>SKCanvas 的繪圖

`SKCanvas`使用夠用類似於其他繪圖繪圖模型的模型，您可能已經熟悉，它與選擇性透明度通道使用的色彩，並可以繪製線條、 弧線、 文字和影像。

以下是幾個可以使用 SkiaSharp 許多不同項目。  在範例中，下列變數`canvas`的型別 SKCanvas。

### <a name="drawing-xamagon"></a>繪圖 Xamagon

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

使用 SkiaSharp 詳細資訊可以找到上[線上應用程式開發介面文件](https://developer.xamarin.com/api/namespace/SkiaSharp/)


## <a name="related-links"></a>相關連結

- [SkiaSharp iOS 活頁簿](https://developer.xamarin.com/workbooks/graphics/skiasharp/logo/skialogo-ios.workbook)
