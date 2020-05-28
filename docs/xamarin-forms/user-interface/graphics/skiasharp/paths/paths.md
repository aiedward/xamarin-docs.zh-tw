---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6ceac2d866e67af5cf3496fcf8c072ae83ecfe38
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140240"
---
# <a name="path-basics-in-skiasharp"></a>SkiaSharp 中的路徑基本概念

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp SKPath 物件以合併連接的線條和曲線_

圖形路徑最重要的功能之一，就是能夠定義何時應連接多行，以及它們何時不應該連接。 差別可能很大，因為這兩個三角形的頂端示範：

![](paths-images/connectedlinesexample.png "Two triangles showing the difference between connected and disconnected lines")

圖形路徑是由物件所封裝 [`SKPath`](xref:SkiaSharp.SKPath) 。 「路徑」（path）是一或多個*分佈*的集合。 每個輪廓都是*連接*的直線和曲線的集合。 等高線不會彼此連接，但可能會以視覺方式重迭。 有時候，單一輪廓可能會重迭。

通常會從呼叫下列的方法開始 `SKPath` ：

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*)開始新的輪廓

該方法的引數是單一點，您可以將它當做 `SKPoint` 值或個別的 X 和 Y 座標來表示。 `MoveTo`呼叫會在輪廓的開頭和初始*目前的點*之間建立一個點。 您可以呼叫下列方法，以使用從目前點到方法中所指定點的線條或曲線來繼續輪廓，這會變成新的目前點：

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*)若要在路徑中加入直線
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*)若要加入弧線，這是圓形或橢圓形圓周上的線條
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*)若要加入三次方貝茲曲線
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*)若要新增二次方貝茲曲線
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*)若要加入有理數二次方貝茲曲線，可以精確地呈現圓錐區段（橢圓形、parabolas 和 hyperbolas）

這五種方法都不包含描述線條或曲線所需的所有資訊。 這五種方法中的每一種都可以搭配先前的方法呼叫所建立的目前點使用。 例如， `LineTo` 方法會根據目前的點將直線加入至輪廓，因此的參數 `LineTo` 只會是單一點。

`SKPath`類別也會定義與這六種方法具有相同名稱，但開頭為的方法 `R` ：

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`代表*相對*的。 這些方法與對應的方法具有相同的語法，但不含，而 `R` 是相對於目前的點。 這些可方便您在呼叫多次的方法中繪製路徑的相似部分。

等高線會以另一個呼叫或來結束 `MoveTo` `RMoveTo` ，這會開始新的輪廓，或呼叫以 `Close` 關閉輪廓。 `Close`方法會自動從目前的點將直線附加至輪廓的第一個點，並將路徑標示為已關閉，這表示它會在沒有任何筆觸帽的情況下呈現。

[開啟] 和 [封閉] 分布圖的差異會在**兩個三角形**的 [分佈] 頁面中說明，其使用 `SKPath` 具有兩個輪廓的物件來呈現兩個三角形。 第一個等高線是開啟的，第二個是關閉的。 以下是 [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) 類別：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

第一個輪廓包含 [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) 使用 X 和 Y 座標而非值的呼叫 `SKPoint` ，後面接著三個呼叫來 [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) 繪製三角形的三邊。 第二個輪廓只有兩個呼叫 `LineTo` ，但它會透過呼叫來完成輪廓 [`Close`](xref:SkiaSharp.SKPath.Close) ，這會關閉輪廓。 差異很大：

[![](paths-images/twotrianglecontours-small.png "Triple screenshot of the Two Triangle Contours page")](paths-images/twotrianglecontours-large.png#lightbox "Triple screenshot of the Two Triangle Contours page")

如您所見，第一條輪廓很明顯是一連串的三條線，但結尾不會與一開始進行連接。 這兩行會在頂端重迭。 第二個輪廓明顯地封閉，並使用較少的呼叫來完成， `LineTo` 因為 `Close` 方法會自動新增最後一行來關閉輪廓。

`SKCanvas`只定義一個 [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) 方法，在此示範中呼叫兩次，以填滿和繪製路徑。 所有的分佈都會填滿，即使尚未關閉也是一樣。 為了填補未封閉的路徑，會假設在輪廓的起點和終點之間存在直線。 如果您 `LineTo` 從第一個輪廓中移除最後一個，或 `Close` 從第二個輪廓中移除該呼叫，則每個等高線只會有兩個邊，但會填滿為三角形。

`SKPath`定義許多其他方法和屬性。 下列方法會將整個輪廓新增至路徑，這可能會因方法而關閉或關閉：

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))若要在橢圓形的圓周上加入曲線
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*)若要將另一個路徑加入至目前的路徑
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath))若要反向新增另一個路徑

請記住， `SKPath` 物件只會定義 &mdash; 一系列點和連接的幾何。 只有當與 `SKPath` 物件合併時 `SKPaint` ，才是以特定色彩、筆劃寬度等等呈現的路徑。 此外，請記住， `SKPaint` 傳遞至方法的物件會 `DrawPath` 定義整個路徑的特性。 如果您想要繪製一些需要多個色彩的專案，您必須針對每個色彩使用不同的路徑。

就像線條的開始和結束的外觀是由筆劃端點所定義，兩條線之間的連接外觀是由*筆劃聯結*所定義。 您可以藉由將的 [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) 屬性設定 `SKPaint` 為列舉的成員來指定 [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) ：

- `Miter`針對角聯結
- `Round`若為圓角聯結
- `Bevel`針對切碎時聯結

[**筆觸**聯結] 頁面會顯示這三個以類似 [**筆劃 Caps** ] 頁面的程式碼進行的筆觸聯結。 這是 `PaintSurface` 類別中的事件處理常式 [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

以下是程式執行情況：

[![](paths-images/strokejoins-small.png "Triple screenshot of the Stroke Joins page")](paths-images/strokejoins-large.png#lightbox "Triple screenshot of the Stroke Joins page")

「斜切聯結」是由線條連接的一個尖點所組成。 當兩條線以較小的角度聯結時，斜切聯結可能會變得相當長。 為避免過長的斜接聯結，斜接角聯接的長度會受到的屬性值所限制 [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) `SKPaint` 。 超過此長度的斜接聯結會切碎為 off，使其成為斜面聯結。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
