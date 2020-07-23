---
title: 整合文字與圖形
description: 本文說明如何判斷呈現的文字字串大小，以將文字與 SkiaSharp 圖形整合到 Xamarin.Forms 應用程式中，並使用範例程式碼示範這項操作。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a98c7210f2e71f6f26d53da3555f3f9b5e016952
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86935976"
---
# <a name="integrating-text-and-graphics"></a>整合文字與圖形

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何判斷呈現的文字字串大小，以整合文字與 SkiaSharp 圖形_

本文示範如何測量文字、將文字調整為特定大小，以及將文字與其他圖形整合：

![以矩形括住的文字](text-images/textandgraphicsexample.png)

該影像也包含一個圓角矩形。 SkiaSharp `Canvas` 類別包含 [`DrawRect`](xref:SkiaSharp.SKCanvas.DrawRect*) 繪製矩形和方法的方法， [`DrawRoundRect`](xref:SkiaSharp.SKCanvas.DrawRoundRect*) 以繪製具有圓角的矩形。 這些方法可讓您將矩形定義為 `SKRect` 值或其他方式。

框住的**文字**頁面會將頁面上的簡短文字字串置中，並使用由一對圓角矩形組成的框架來括住。 [`FramedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs)類別會顯示其完成方式。

在 SkiaSharp 中，您可以使用 `SKPaint` 類別來設定文字和字型屬性，但您也可以使用它來取得轉譯的文字大小。 下列 `PaintSurface` 事件處理常式的開頭會呼叫兩個不同 `MeasureText` 的方法。 第一次 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) 呼叫具有簡單的 `string` 引數，並根據目前的字型屬性傳回文字的圖元寬度。 然後，程式 `TextSize` `SKPaint` 會根據呈現的寬度、目前的 `TextSize` 屬性和顯示區域的寬度，計算物件的新屬性。 這項計算的目的是要將 `TextSize` 文字字串轉譯成畫面寬度的90%：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

第二個 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) 呼叫有 `SKRect` 引數，因此它會同時取得轉譯文字的寬度和高度。 `Height`這個值的屬性 `SKRect` 取決於文字字串中是否有大寫字母、ascenders 和下行。 `Height`系統會針對文字字串 "mom"、「cat」和「狗」報告不同的值，例如。

`Left` `Top` `SKRect` 如果文字是由 `DrawText` X 和 Y 位置為0的呼叫所顯示，結構的和屬性會指出所呈現文字左上角的座標。 例如，當此程式在 iPhone 7 模擬器上執行時， `TextSize` 會在第一次呼叫之後，將值90.6254 指派給計算結果 `MeasureText` 。 `SKRect`從的第二個呼叫取得的值 `MeasureText` 具有下列屬性值：

- `Left`= 6
- `Top` = &ndash;68
- `Width`= 664.8214
- `Height`= 88;

請記住，您傳遞給方法的 X 和 Y 座標會 `DrawText` 在基準上指定文字的左邊。 此 `Top` 值表示文字會擴充高於該基準的68圖元，並（從88減去68）到基準底下的20圖元。 `Left`值為6表示文字會在呼叫中 X 值的右邊開始6個圖元 `DrawText` 。 這允許標準的字元間間距。 如果您想要在顯示的左上角顯示文字 snugly，請傳遞這些 `Left` 和值的否定 `Top` 做為的 X 和 Y 座標 `DrawText` ，在此範例中為 &ndash; 6 和68。

`SKRect`結構會定義數個便利的屬性和方法，其中有些是用於處理常式的其餘部分 `PaintSurface` 。 `MidX`和 `MidY` 值表示矩形中心的座標。 （在 iPhone 7 範例中，這些值是338.4107 和 &ndash; 24）。下列程式碼會使用這些值來進行座標的最簡單計算，以便在顯示時將文字置中：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

`SKImageInfo`Info 結構也 [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) 會定義類型的屬性 `SKRect` ，因此您也可以計算和，如下所 `xText` `yText` 示：

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

`PaintSurface`處理常式會在兩次呼叫時結束 `DrawRoundRect` ，這兩個都需要的引數 `SKRect` 。 這個 `SKRect` 值是以 `SKRect` 從方法取得的值為基礎 `MeasureText` ，但不能相同。 首先，它必須稍微大一點，讓圓角矩形不會在文字邊緣上繪製。 其次，必須在空間中移動，讓 `Left` 和 `Top` 值對應到要放置矩形的左上角。 這兩個作業是由 [`Offset`](xref:SkiaSharp.SKRect.Offset*) 定義的和方法來完成 [`Inflate`](xref:SkiaSharp.SKRect.Inflate*) `SKRect` ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

接下來，方法的其餘部分是直接的。 它會為框線建立另一個 `SKPaint` 物件，並呼叫 `DrawRoundRect` 兩次。 第二個呼叫會使用以另一個10圖元放大的矩形。 第一個呼叫指定了20圖元的圓角半徑。 第二個具有30圖元的圓角半徑，因此它們看起來是平行的：

 [![框文字頁面的三重螢幕擷取畫面](text-images/framedtext-small.png)](text-images/framedtext-large.png#lightbox "框文字頁面的三重螢幕擷取畫面")

您可以將手機或模擬器旋轉一側，以查看大小增加的文字和畫面。

如果您只需要在螢幕上將一些文字置中，就可以在不測量文字的情況下完成。 相反地，請將的 [`TextAlign`](xref:SkiaSharp.SKPaint.TextAlign) 屬性設定 `SKPaint` 為列舉成員 [`SKTextAlign.Center`](xref:SkiaSharp.SKTextAlign) 。 您在方法中指定的 X 座標， `DrawText` 會指出文字水準置中的位置。 如果您將畫面的中間點傳遞至 `DrawText` 方法，文字將會水準置中，而且*幾乎*垂直置中，因為基準會垂直置中。

文字的處理方式與任何其他繪圖物件很相似。 其中一個簡單的選項是顯示文字字元的外框：

[![外框文字頁面的三重螢幕擷取畫面](text-images/outlinedtext-small.png)](text-images/outlinedtext-large.png#lightbox "外框文字頁面的三重螢幕擷取畫面")

只要將物件的 normal `Style` 屬性 `SKPaint` 從的預設設定變更 `SKPaintStyle.Fill` 為 `SKPaintStyle.Stroke` ，然後指定筆觸寬度，就可以完成這項作業。 `PaintSurface`**大綱文字**頁面的處理常式會顯示其完成方式：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

另一個通用的繪圖物件是點陣圖。 這是[**SkiaSharp 點陣圖**](../bitmaps/index.md)一節中深度涵蓋的大型主題，但下一篇文章（SkiaSharp 中的[**Bitmap 基本概念**](bitmaps.md)）提供簡潔簡介。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
