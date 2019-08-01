---
title: SkiaSharp 中路徑的基本概念
description: 本文將探討結合連接的直線和曲線的 SkiaSharp SKPath 物件，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 370fec5b9323187f6345d3e6bf9d3e38145cedff
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652775"
---
# <a name="path-basics-in-skiasharp"></a>SkiaSharp 中路徑的基本概念

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索結合連接的直線和曲線的 SkiaSharp SKPath 物件_

圖形路徑的最重要的功能之一是可以定義多行應該連接時和當他們不應該連接的能力。 差異可能就是有意義的因為這些兩個三角形的短片示範：

![](paths-images/connectedlinesexample.png "兩個三角形顯示連線和中斷的線條之間的差異")

封裝的圖形路徑[ `SKPath` ](xref:SkiaSharp.SKPath)物件。 路徑是一或多個集合*輪廓*。 每個分佈是一堆*連線*直線和曲線。 輪廓未彼此連線，但它們可能會以視覺化方式重疊。 有時單一 contour 可以重疊本身。

Contour 通常開始的下列方法呼叫`SKPath`:

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) 若要開始新的分佈

該方法的引數是單一點，您可以表示為`SKPoint`值，或作為個別的 X 和 Y 座標。 `MoveTo`呼叫會建立在開始點的分佈和初始*目前的點*。 您可以呼叫下列方法，以繼續使用線條或曲線從目前的點來指定在方法中，就會變成新的目前點的點分佈：

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) 若要新增至路徑的一條直線
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) 若要新增的弧形，這是圓形或橢圓形的圓周上的線上
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) 若要新增的三次方貝茲曲線
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) 若要新增的二次方貝茲曲線
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) 若要新增 rational 二次方貝茲曲線，可正確轉譯 conic 區段 （省略符號，parabolas 和 hyperbolas）

這些五個方法都包含描述線條或曲線所需的所有資訊。 每個五個方法和目前的點建立立即在它前面的方法呼叫的方法是搭配。 例如，`LineTo`方法會加入一條直線線段來根據目前的點，因此參數`LineTo`是單一點。

`SKPath`類別也會定義具有相同的名稱為這些六種方法，但方法`R`開頭：

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`代表*相對*。 這些方法具有相同的語法，為對應的方法，而不必`R`但相對於目前的點。 這些是路徑的很方便的繪製類似的組件的多次呼叫的方法中。

Contour 結束再呼叫一次`MoveTo`或是`RMoveTo`，其會開始新的分佈或呼叫`Close`，這會關閉分佈。 `Close`方法會自動附加一條直線從目前點的第一個點的分佈，並將路徑標示為已關閉，這表示，它將不會呈現任何筆觸端點。

開啟和關閉的輪廓之間的差異所示**兩個三角形輪廓**頁面上，使用`SKPath`呈現兩個三角形的兩個分佈物件。 第一個線段是開啟和關閉第二個。 以下是[ `TwoTriangleContoursPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs)類別：

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

呼叫包含的第一個分佈[ `MoveTo` ](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single))使用 X 和 Y 座標，而非`SKPoint`值，後面接著三次呼叫[ `LineTo` ](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single))繪製的三個邊三角形。 第二個分佈都有只有兩個呼叫`LineTo`但完成藉由呼叫 contour [ `Close` ](xref:SkiaSharp.SKPath.Close)，這會關閉的分佈。 差異十分重要：

[![](paths-images/twotrianglecontours-small.png "兩個三角形輪廓頁面的三個螢幕擷取畫面")](paths-images/twotrianglecontours-large.png#lightbox "的兩個三角形輪廓頁面的三個螢幕擷取畫面")

如您所見，第一個分佈顯然是一系列的三個連接的直線，但結束時未連線開頭。 上方，重疊兩條線。 第二個分佈很明顯地關閉，並使用較少的其中一個已完成`LineTo`呼叫因為`Close`方法會自動新增以關閉 線段的最後一行。

`SKCanvas` 只會定義一個[ `DrawPath` ](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint))方法，這在這段示範影片中呼叫兩次以填滿和繪製路徑。 所有的分佈會填滿，即使未關閉的。 一條直線的填滿封閉的路徑的目的，假設之間的開始和結束點的輪廓。 如果您移除最後一個`LineTo`從第一個分佈或移除`Close`從第二個分佈，也就是每個分佈的呼叫必須如同它是一個三角形填滿但將只有兩個邊。

`SKPath` 定義許多其他方法和屬性。 下列方法會新增至路徑，它可能已關閉或未根據方法關閉整個的輪廓：

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) 將曲線上的橢圓形的圓周
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) 若要將另一個路徑新增至目前的路徑
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) 若要反向新增另一個路徑

請記住`SKPath`物件會定義只幾何&mdash;一連串的點和連接。 只有當`SKPath`結合`SKPaint`物件是使用特定的色彩、 筆觸粗細等呈現的路徑。 此外，請記住`SKPaint`物件傳遞給`DrawPath`方法定義特性的完整路徑。 如果您想要繪製項目需要幾個色彩，您必須使用不同的路徑，每一種色彩。

就像筆觸端點所定義的開始和結束行的外觀，由定義兩行之間的連線的外觀*筆劃聯結*。 您可以設定指定這[ `StrokeJoin` ](xref:SkiaSharp.SKPaint.StrokeJoin)屬性`SKPaint`成員[ `SKStrokeJoin` ](xref:SkiaSharp.SKStrokeJoin)列舉型別：

- `Miter` 尖聯結
- `Round` 圓角聯結
- `Bevel` 已關閉聯結

**筆劃聯結**頁面上的顯示這三個描邊與類似的程式碼的聯結**筆觸端點**頁面。 這是`PaintSurface`中的事件處理常式[ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs)類別：

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

以下是執行的程式：

[![](paths-images/strokejoins-small.png "筆劃加入頁面的三個螢幕擷取畫面")](paths-images/strokejoins-large.png#lightbox "筆劃加入頁面的三個螢幕擷取畫面")

斜接角聯結是由井字點，其中線條的連接所組成。 當兩行進入小角時，斜接角聯結可能會變得很長。 若要防止過長斜接角聯結時，斜接角聯結的長度會受到的值[ `StrokeMiter` ](xref:SkiaSharp.SKPaint.StrokeMiter)屬性`SKPaint`。 超過此長度斜接角聯結砍成為斜面聯結。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
