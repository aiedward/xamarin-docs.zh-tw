---
title: SkiaSharp 平台專屬附註
description: 本文件說明與 SkiaSharp 有關的特定平台的詳細資料。 它會提供範例程式碼適用於 iOS、 Android、 macOS、 Windows 和 Xamarin.Forms。
ms.prod: xamarin
ms.techonology: xamarin-skiasharp
ms.assetid: 1D90E0B3-A3A8-4286-BC54-9D67188A1C6C
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: 05c6ae6553a2e869b9eb7e038abd7b1c34350551
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615804"
---
# <a name="skiasharp-platform-specific-notes"></a>SkiaSharp 平台專屬附註

下列範例手動配置的映像的緩衝區，這為了說明常見的平台模式也就是平台所提供的現有 RBGA 緩衝區上繪製。

您不需要使用這個慣用語，如果您不想要。  沒有多載會建立及管理您的映像的支援儲存體。

## <a name="ios"></a>iOS

```csharp
var screenScale = UIScreen.MainScreen.Scale;
var width = (int)(Bounds.Width * screenScale);
var height = (int)(Bounds.Height * screenScale);

IntPtr buff = System.Runtime.InteropServices.Marshal.AllocCoTaskMem (width * height * 4);
try {
  using (var surface = SKSurface.Create (width, height, SKImageInfo.PlatformColorType, SKAlphaType.Premul, buff, width * 4)) {
    var skcanvas = surface.Canvas;
    skcanvas.Scale ((float)screenScale, (float)screenScale);
    using (new SKAutoCanvasRestore (skcanvas, true)) {
      // DoDraw (skcanvas);
    }
  }
  using (var colorSpace = CGColorSpace.CreateDeviceRGB ())
  using (var bContext = new CGBitmapContext (buff, width, height, 8, width * 4, colorSpace, (CGImageAlphaInfo)bitmapInfo))
  using (var image = bContext.ToImage ())
  using (var context = UIGraphics.GetCurrentContext ()) {
    // flip the image for CGContext.DrawImage
    context.TranslateCTM (0, Frame.Height);
    context.ScaleCTM (1, -1);
    context.DrawImage (Bounds, image);
  }
} finally {
  if (buff != IntPtr.Zero) {
    System.Runtime.InteropServices.Marshal.FreeCoTaskMem (buff);
  }
}
```

## <a name="android"></a>Android

```csharp
var width = (float)skiaView.Width;
var height = (float)skiaView.Height;

using (var bitmap = Bitmap.CreateBitmap (canvas.Width, canvas.Height, Bitmap.Config.Argb8888)) {
  try {
    using (var surface = SKSurface.Create (canvas.Width, canvas.Height, SKColorType.Rgba_8888, SKAlphaType.Premul, bitmap.LockPixels (), canvas.Width * 4)) {
      var skcanvas = surface.Canvas;
      skcanvas.Scale (((float)canvas.Width)/width, ((float)canvas.Height)/height);
      // DoDraw (skcanvas);
    }
  } finally {
    bitmap.UnlockPixels ();
  }
  canvas.DrawBitmap (bitmap, 0, 0, null);
}
```

## <a name="macos"></a>macOS

```csharp
var screenScale = (int)NSScreen.MainScreen.BackingScaleFactor * 2;
var width = (int)Bounds.Width * screenScale;
var height = (int)Bounds.Height * screenScale;

IntPtr buff = System.Runtime.InteropServices.Marshal.AllocCoTaskMem (width * height * 4);
try {
  using (var surface = SKSurface.Create (width, height, SKColorType.Rgba_8888, SKAlphaType.Premul, buff, width * 4)) {
    var skcanvas = surface.Canvas;
    skcanvas.Scale (screenScale, screenScale);
    // DoDraw (skcanvas);
  }
  int flag = ((int)CoreGraphics.CGBitmapFlags.ByteOrderDefault) | ((int)CoreGraphics.CGImageAlphaInfo.PremultipliedLast);
  using (var colorSpace = CoreGraphics.CGColorSpace.CreateDeviceRGB ())
  using (var bContext = new CoreGraphics.CGBitmapContext (buff, width, height, 8, width * 4, colorSpace, (CoreGraphics.CGImageAlphaInfo) flag))
  using (var image = bContext.ToImage ())
  using (var context = NSGraphicsContext.CurrentContext.GraphicsPort) {
    context.DrawImage (Bounds, image);
  }
} finally {
  if (buff != IntPtr.Zero) {
    System.Runtime.InteropServices.Marshal.FreeCoTaskMem (buff);
  }
}
```

## <a name="windows-desktop--mac-desktop"></a>Windows 桌面 / Mac 桌面

```csharp
var width = Width;
var height = Height;

using (var bitmap = new Bitmap(width, height, PixelFormat.Format32bppPArgb)) {
  var data = bitmap.LockBits(new Rectangle(0, 0, width, height), ImageLockMode.WriteOnly, bitmap.PixelFormat);
  using (var surface = SKSurface.Create(width, height, SKImageInfo.PlatformColorType, SKAlphaType.Premul, data.Scan0, width * 4)) {
    var skcanvas = surface.Canvas;
    // DoDraw (skcanvas);
  }
  bitmap.UnlockBits(data);
  e.Graphics.DrawImage(bitmap, new Rectangle(0, 0, Width, Height));
}
```

## <a name="xamarinforms"></a>Xamarin.Forms

SkiaSharp 納入您的 Xamarin.Forms 應用程式，請參閱指南[Xamarin.Forms 中的使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)。

## <a name="related-links"></a>相關連結

- [SkiaSharp iOS 活頁簿](https://developer.xamarin.com/workbooks/graphics/skiasharp/logo/skialogo-ios.workbook)
