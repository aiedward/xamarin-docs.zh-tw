---
title: 三種類型的貝茲曲線
description: 本文說明如何使用 SkiaSharp 呈現在 Xamarin.Forms 應用程式的三次方、 二次方，以及 conic 貝茲曲線，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
ms.openlocfilehash: 58cf11b2a88e0c399ee197e9c8365d7deafd0f39
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229551"
---
# <a name="three-types-of-bzier-curves"></a>三種類型的貝茲曲線

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_探索如何使用 SkiaSharp 呈現三次方、 二次方，以及 conic 貝茲曲線_

貝茲曲線被命名匹貝茲 (1910年 – 1999)，法文的工程師在汽車公司 Renault，使用電腦輔助設計的車內文的曲線。

貝茲曲線是公認為適用於互動式的設計：它們是正常執行&mdash;亦即，不會導致變成無限或難以曲線的 singularities&mdash;且通常悅耳：

![](beziers-images/beziersample.png "範例貝茲曲線")

使用貝茲曲線，通常被定義字元外框輪廓的電腦為基礎的字型。

在維基百科文章[**貝茲曲線**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)包含一些有用的背景資訊。 詞彙*貝茲曲線*實際上是指一系列的類似的曲線。 SkiaSharp 支援三種類型的貝茲曲線，稱為*三次方*，則*二次方*，而*conic*。 Conic 就是所謂*rational 二次方程式*。

## <a name="the-cubic-bzier-curve"></a>三次方貝茲曲線

三次方是貝茲曲線的貝茲曲線主旨出現時，大部分的開發人員認為的類型。

您可以加入到三次方貝茲曲線`SKPath`物件使用[ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint))具有三個方法`SKPoint`參數，或[ `CubicTo` ](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single))多載，使用不同`x`和`y`參數：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

曲線開始移動平均的分佈。 完整的三次方貝茲曲線是由四個點定義：

- 起始點： 目前的點線段，或 （0，0） 如果`MoveTo`尚未呼叫
- 第一次控制點：`point1`在`CubicTo`呼叫
- 第二個控制點：`point2`在`CubicTo`呼叫
- 結束點：`point3`在`CubicTo`呼叫

結果的曲線的起始點開始，並在結束點結束。 曲線通常不會通過兩個控制點;而是兩個控制點函式非常類似磁鐵提取他們而來的曲線。

若要概略了三次方貝茲曲線，最好是測試。 這是目的**貝茲曲線**頁面上，衍生自`InteractivePage`。 [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)檔案會具現化`SKCanvasView`和`TouchEffect`。 [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)程式碼後置檔案會建立四個`TouchPoint`其建構函式中的物件。 `PaintSurface`事件處理常式會建立`SKPath`呈現根據四個貝茲曲線`TouchPoint`物件，而且也繪製虛線的正切函數控點從端點：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with cubic Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.CubicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     touchPoints[3].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[2].Center.X,
                    touchPoints[2].Center.Y,
                    touchPoints[3].Center.X,
                    touchPoints[3].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
       touchPoint.Paint(canvas);
    }
}
```

以下執行：

[![](beziers-images/beziercurve-small.png "貝茲曲線頁面的三個螢幕擷取畫面")](beziers-images/beziercurve-large.png#lightbox "的貝茲曲線頁面的三個螢幕擷取畫面")

以數學方式，曲線是三次方多項式次方。 曲線最相交於三個點的直線。 起始點，在曲線一律為，然後在方向相同的正切函數，一條直線開始點的第一個控制點。 結束點曲線一律為，然後在方向相同的正切函數，從第二個控制項一條直線指向結束點。

連接四個點的凸方形一律會受限於三次方貝茲曲線。 這就叫做*凸殼*。 如果控制項點位於上開始和結束點之間的直線，貝茲曲線就會轉譯為一條直線。 但曲線也可以跨越本身，如第三個螢幕擷取畫面所示。

路徑 contour 可以包含多個連接的三次方貝茲曲線，但兩個三次方貝茲曲線之間的連線會 smooth 只有 colinear 下列三個點 （也就是上一條直線之間）：

- 第一個曲線的第二個控制點
- 第一個曲線，這也是第二個曲線的起始點的結束點
- 第二個曲線的第一個控制點

在下一篇文章上[ **SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)，您會發現的機能，以便順利連接的貝茲曲線的定義。

有時會很有幫助呈現三次方貝茲曲線基礎參數化的方程式。 針對*t*範圍從 0 到 1，參數化的方程式如下所示：

x(t) = (1 – t) ³x₀ + 3t (1 – t) ²x₁ + 3t² (1 – t) x₂ + t³x₃

y(t) = (1 – t) ³y₀ + 3t (1 – t) ²y₁ + 3t² (1 – t) y₂ + t³y₃

3 的最高的指數確認，這些都是三次方 polynomials。 就可以輕鬆地驗證，當`t`等於 0，重點是 (x₀ y₀)，這是起始點，以及當`t`等於 1，重點是 (x₃ y₃)，這是結束點。 附近的起始點 (低值`t`)，第一個控制點 （x₁、 y₁） 具有強式生效，並近乎結束點 (最高值的 ' t ') 的第二個控制點 （x₂、 y₂） 有很大的影響。

## <a name="bezier-curve-approximation-to-circular-arcs"></a>圓弧的貝茲曲線最近似值

可能會很方便地使用貝茲曲線來呈現圓弧線段。三次方貝茲曲線可以近似圓弧線段很高達四分之一圓形，因此四個連接的貝茲曲線可以定義整個圓形。 已發行超過 25 年多前的兩篇文章討論這項估計值：

> Tor Dokken 等，「 適當近似值圓形的曲率連續的貝茲曲線，「*電腦輔助幾何設計 7* (1990)，33 41。

> Michael Goldapp、"近似值圓弧三次方 Polynomials，由 「*電腦輔助的幾何設計 8* （1991 年以來），227 238。

下圖顯示四個點標示`pto`， `pt1`， `pt2`，和`pt3`定義近似圓弧線段的貝茲曲線 （以紅色顯示）：

![](beziers-images/bezierarc45.png "貝茲曲線的圓弧線段的近似值")

中開始和結束點的程式行，以控點會圓形，貝茲曲線的正切函數，而且必須一段*L*。上述第一篇文章指出最佳的貝茲曲線接近圓弧線段時，長度*L*計算如下：

L = 4 × tan(α / 4) / 3

此圖顯示 45 度的角度，因此 L 等於 0.265。 在程式碼，該值會乘以需圓形的半徑。

**貝茲圓弧** 頁面可讓您實驗定義的貝茲曲線，來模擬圓弧範圍最多 180 度的角度。 [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)檔案會具現化`SKCanvasView`和`Slider`可供選取的角度。 `PaintSurface`中的事件處理常式[ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)程式碼後置檔案會使用轉換來設定畫布的中心點 （0，0）。 它繪製圓形，相較之下，該點上置中，然後計算貝茲曲線的兩個控點：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 3;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate length of control point line
    float length = radius * 4 * (float)Math.Tan(Math.PI * angle / 180 / 4) / 3;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the end points
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point3 = new SKPoint(radius * sin, radius * cos);

    // Find the control points
    SKPoint point0Normalized = Normalize(point0);
    SKPoint point1 = point0 + new SKPoint(length * point0Normalized.Y,
                                          -length * point0Normalized.X);

    SKPoint point3Normalized = Normalize(point3);
    SKPoint point2 = point3 + new SKPoint(-length * point3Normalized.Y,
                                          length * point3Normalized.X);

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);
    canvas.DrawCircle(point3.X, point3.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point3.X, point3.Y, point2.X, point2.Y, dottedStroke);

    // Draw the Bezier curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.CubicTo(point1, point2, point3);
        canvas.DrawPath(path, redStroke);
    }
}

// Vector methods
SKPoint Normalize(SKPoint v)
{
    float magnitude = Magnitude(v);
    return new SKPoint(v.X / magnitude, v.Y / magnitude);
}

float Magnitude(SKPoint v)
{
    return (float)Math.Sqrt(v.X * v.X + v.Y * v.Y);
}

```

開始和結束點 (`point0`和`point3`) 會計算是根據圓形一般參數化的方程式。 因為圓形中心位於 （0，0），這些點也可視為星形向量從圓形的中心來圓周。 控制點位於行的正切函數為圓形，讓它們為直角成這些星形的向量。 呈直角到另一個向量是只要原始向量與交換的 X 和 Y 座標和其中所做的負值。

以下是執行不同的角度的程式：

[![](beziers-images/beziercirculararc-small.png "貝茲圓弧頁面的三個螢幕擷取畫面")](beziers-images/beziercirculararc-large.png#lightbox "的貝茲圓弧頁面的三個螢幕擷取畫面")

仔細看看第三個螢幕擷取畫面，您會看到，貝茲曲線值得注意的是衍生自半圓形的角度為 180 度，但 iOS 畫面會顯示它似乎符合四分之一圓形正常，角度為 90 度時。

計算兩個控制點座標是相當簡單，導向這類四分之一圓形時：

![](beziers-images/bezierarc90.png "貝茲曲線的四分之一圓形的近似值")

如果圓形的半徑為 100，則*L* 55，而且是簡單的數字，要記住。

**Number 圓形**頁面以動畫顯示的圖形之間的圓形和正方形。 在這個陣列定義的第一個資料行中顯示其座標的四個貝茲曲線近似於圓形[ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs)類別：

```csharp
public class SquaringTheCirclePage : ContentPage
{
    SKPoint[,] points =
    {
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() },
        { new SKPoint(  55,  100), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,   55), new SKPoint( 62.5f,  62.5f), new SKPoint() },
        { new SKPoint( 100,    0), new SKPoint(   125,      0), new SKPoint() },
        { new SKPoint( 100,  -55), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(  55, -100), new SKPoint( 62.5f, -62.5f), new SKPoint() },
        { new SKPoint(   0, -100), new SKPoint(     0,   -125), new SKPoint() },
        { new SKPoint( -55, -100), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,  -55), new SKPoint(-62.5f, -62.5f), new SKPoint() },
        { new SKPoint(-100,    0), new SKPoint(  -125,      0), new SKPoint() },
        { new SKPoint(-100,   55), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint( -55,  100), new SKPoint(-62.5f,  62.5f), new SKPoint() },
        { new SKPoint(   0,  100), new SKPoint(     0,    125), new SKPoint() }
    };
    ...
}
```

第二個資料行包含定義區域大約是圓形的面積相同正方形的四個貝茲曲線的座標。 (繪製的正方形*確切*區域中指定的圓形是的傳統無法解決幾何的問題[number 圓形](https://en.wikipedia.org/wiki/Squaring_the_circle)。)轉譯貝茲曲線的正方形，每條曲線的兩個控制點相同，且都是 colinear 開始與結束點，因此貝茲曲線會轉譯成一條直線。

動畫的插補值是陣列的第三個資料行。 頁面將計時器設為 16 毫秒，而`PaintSurface`處理常式會呼叫的速率：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    canvas.Translate(info.Width / 2, info.Height / 2);
    canvas.Scale(Math.Min(info.Width / 300, info.Height / 300));

    // Interpolate
    TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
    float t = (float)(timeSpan.TotalSeconds % 3 / 3);   // 0 to 1 every 3 seconds
    t = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;     // 0 to 1 to 0 sinusoidally

    for (int i = 0; i < 13; i++)
    {
        points[i, 2] = new SKPoint(
            (1 - t) * points[i, 0].X + t * points[i, 1].X,
            (1 - t) * points[i, 0].Y + t * points[i, 1].Y);
    }

    // Create the path and draw it
    using (SKPath path = new SKPath())
    {
        path.MoveTo(points[0, 2]);

        for (int i = 1; i < 13; i += 3)
        {
            path.CubicTo(points[i, 2], points[i + 1, 2], points[i + 2, 2]);
        }
        path.Close();

        canvas.DrawPath(path, cyanFill);
        canvas.DrawPath(path, blueStroke);
    }
}
```

根據 sinusoidally 不穩定的值插補點`t`。 插補的點接著會用於建構一系列的四個連接的貝茲曲線。 以下是執行動畫：

[![](beziers-images/squaringthecircle-small.png "三重的螢幕擷取畫面的 Squaring 圓形頁面")](beziers-images/squaringthecircle-large.png#lightbox "Squaring 的三個螢幕擷取畫面 [圓形] 頁面")

這類動畫是不可能沒有又靈活地轉譯為圓弧及直線，線條的曲線。

**貝茲無限大**頁面也會利用貝茲曲線的能力，來模擬圓弧線段。以下是`PaintSurface`處理常式[ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs)類別：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.MoveTo(0, 0);                                // Center
        path.CubicTo(  50,  -50,   95, -100,  150, -100); // To top of right loop
        path.CubicTo( 205, -100,  250,  -55,  250,    0); // To far right of right loop
        path.CubicTo( 250,   55,  205,  100,  150,  100); // To bottom of right loop
        path.CubicTo(  95,  100,   50,   50,    0,    0); // Back to center  
        path.CubicTo( -50,  -50,  -95, -100, -150, -100); // To top of left loop
        path.CubicTo(-205, -100, -250,  -55, -250,    0); // To far left of left loop
        path.CubicTo(-250,   55, -205,  100, -150,  100); // To bottom of left loop
        path.CubicTo( -95,  100,  -50,   50,    0,    0); // Back to center
        path.Close();

        SKRect pathBounds = path.Bounds;
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.9f * Math.Min(info.Width / pathBounds.Width,
                                     info.Height / pathBounds.Height));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 5;

            canvas.DrawPath(path, paint);
        }
    }
}
```

它可能是很好的演練來繪製圖形紙張上的這些座標，以查看它們相關。 無限大符號為中心點 （0，0），還有兩個迴圈的中心 （–150，0） 和 150 (0） 和半徑為 100。 中的一系列`CubicTo`命令，您可以看到 X 座標的控制點，採用 –95 和 –205 的值 （這些值都 –150 加號和減號 55） 205 和 95 (150 加號和減號 55)，以及 250 和左右兩邊的 –250。 唯一的例外狀況時，infinity 登跨越本身在中心。 在此情況下，控點會有 50 和組合 –50 矯正出中央附近的曲線的座標。

以下是無限大符號：

[![](beziers-images/bezierinfinity-small.png "貝茲無限大頁面的三個螢幕擷取畫面")](beziers-images/bezierinfinity-large.png#lightbox "的貝茲無限大頁面的三個螢幕擷取畫面")

它是有點順暢朝中央所呈現的無限大符號**弧線無限大**頁面上，從[**繪製弧形的三種方式**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章。

## <a name="the-quadratic-bzier-curve"></a>二次方貝茲曲線

二次方貝茲曲線只有一個的控制點，而且只需要三個點定義曲線︰ 起點、 控制項控點和結束點。 參數化的方程式是三次方貝茲曲線，非常類似，不同之處在於最高的指數是 2，因此曲線是二次方多項式次方：

x(t) = (1 – t) ²x₀ + 2t (1 – t) x₁ + t²x₂

y(t) = (1 – t) ²y₀ + 2t (1 – t) y₁ + t²y₂

若要新增的二次方貝茲曲線的路徑，請使用[ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint))方法或[ `QuadTo` ](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single))使用不同的多載`x`和`y`座標：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法會從目前的位置向新增曲線`point2`與`point1`為控制項控點。

您可以實驗使用的二次方貝茲曲線**二次方曲線**頁面上，這是非常類似於**貝茲曲線**頁面上，但它有三個觸控點。 以下是`PaintSurface`中的處理常式[ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs)程式碼後置檔案：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with quadratic Bezier
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.QuadTo(touchPoints[1].Center,
                    touchPoints[2].Center);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

並執行以下：

[![](beziers-images/quadraticcurve-small.png "二次方曲線頁面的三個螢幕擷取畫面")](beziers-images/quadraticcurve-large.png#lightbox "二次方曲線頁面的三個螢幕擷取畫面")

點線是起點和終點曲線的正切函數，並符合在控制項控點。

如果您需要的曲線的一般形狀，但您偏好只在一個控制點而非兩個的便利性，二次方貝茲是很好。 二次方貝茲呈現比任何其他曲線，這就是為什麼它用於在內部 Skia 呈現橢圓弧形更有效率。

不過，二次方貝茲曲線的圖形不是橢圓形，這也是為什麼多個二次方 Béziers 才能近似橢圓形弧線。二次方貝茲會 parabola 的區段。

## <a name="the-conic-bzier-curve"></a>Conic 貝茲曲線

Conic 貝茲曲線&mdash;也稱為 rational 二次方貝茲曲線&mdash;是較新的貝茲曲線的系列。 二次方貝茲曲線，例如起點、 結束點和一個的控制點，牽涉到合理的二次方貝茲曲線。 但也需要合理的二次方貝茲曲線*權數*值。 它會呼叫*rational*二次方，因為參數化的公式牽涉到的比例。

參數化的方程式 X 和 Y 是共用相同的分母的比例。 以下是方程式的分母*t*範圍從 0 1 」 和 「 加權值是*w*:

d(t) = (1 – t) ² + 2wt(1 – t) + t²

理論上，rational 二次方程式可以包含三個不同的加權值，分別用於三個詞彙，但這些可以簡化成只在一個中介詞彙的加權值。

但是也包含中間詞彙的加權值，而運算式除以分母，X 和 Y 座標參數化的方程式是類似的二次方貝茲參數化的方程式：

x(t) = ((1 – t) ²x₀ + 2wt (1 – t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1 – t) y₁ + t²y₂)) ÷ d(t)

也稱為有理數的二次方貝茲曲線*conics*因為它們可以完全代表任何 conic 區段的區段&mdash;hyperbolas、 parabolas、 橢圓形和圓形。

若要新增有理數的二次方貝茲曲線的路徑，請使用[ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single))方法或[ `ConicTo` ](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single))使用不同的多載`x`和`y`座標：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

請注意最後`weight`參數。

**Conic 曲線**頁面可讓您試驗這些曲線。 `ConicCurvePage` 類別衍生自 `InteractivePage`。 [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)檔案會具現化`Slider`選取 – 2 到 2 之間的加權值。 [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)程式碼後置檔案會建立三個`TouchPoint`物件，而`PaintSurface`處理常式只是呈現給控制項的切線產生曲線重點：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Draw path with conic curve
    using (SKPath path = new SKPath())
    {
        path.MoveTo(touchPoints[0].Center);
        path.ConicTo(touchPoints[1].Center,
                     touchPoints[2].Center,
                     (float)weightSlider.Value);

        canvas.DrawPath(path, strokePaint);
    }

    // Draw tangent lines
    canvas.DrawLine(touchPoints[0].Center.X,
                    touchPoints[0].Center.Y,
                    touchPoints[1].Center.X,
                    touchPoints[1].Center.Y, dottedStrokePaint);

    canvas.DrawLine(touchPoints[1].Center.X,
                    touchPoints[1].Center.Y,
                    touchPoints[2].Center.X,
                    touchPoints[2].Center.Y, dottedStrokePaint);

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}
```

以下執行：

[![](beziers-images/coniccurve-small.png "Conic 曲線頁面的三個螢幕擷取畫面")](beziers-images/coniccurve-large.png#lightbox "Conic 曲線頁面的三個螢幕擷取畫面")

如您所見，控制項控點會似乎較高權數時提取朝向它更多曲線。 當加權為零時，曲線就會變成一條直線開始點至結束點。

理論上，負加權允許，而導致要東改西改的曲線*離開*從控制項控點。 不過，加權為-1 或以下原因變成負數之特定值的參數化的方程式中的分母*t*。 可能基於這個理由，負加權時，會忽略`ConicTo`方法。 **Conic 曲線**程式可讓您設定負加權，但如您所見的實驗，負加權有加權設為零，相同的效果，而且會導致要呈現一條直線。

它是很容易就能衍生的控制項控點和要使用的權數`ConicTo`方法，最多可繪製圓弧線段 （但不是包括） 半圓形。 在下列圖表中，從開始和結束點的切線符合在控制項控點。

![](beziers-images/conicarc.png "Conic 弧線的呈現圓弧線段")

若要判斷之間距離的控制項控點，圓形的中心，您可以使用三角函數：它是角度的除以餘弦值的一半 α 圓形的半徑。 若要開始和結束點之間繪製圓弧線段，設定權數為該相同的一半的角度的餘弦值。 請注意，如果角度為 180 度，然後永遠無法符合的切線加權為零。 但如角度小於 180 度、 數學可正常運作。

**Conic 圓弧**示範這項 頁面。 [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)檔案會具現化`Slider`可供選取的角度。 `PaintSurface`中的處理常式[ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)的控制點和重量，計算程式碼後置檔案：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    // Draw the circle
    float radius = Math.Min(info.Width, info.Height) / 4;
    canvas.DrawCircle(0, 0, radius, blackStroke);

    // Get the value of the Slider
    float angle = (float)angleSlider.Value;

    // Calculate sin and cosine for half that angle
    float sin = (float)Math.Sin(Math.PI * angle / 180 / 2);
    float cos = (float)Math.Cos(Math.PI * angle / 180 / 2);

    // Find the points and weight
    SKPoint point0 = new SKPoint(-radius * sin, radius * cos);
    SKPoint point1 = new SKPoint(0, radius / cos);
    SKPoint point2 = new SKPoint(radius * sin, radius * cos);
    float weight = cos;

    // Draw the points
    canvas.DrawCircle(point0.X, point0.Y, 10, blackFill);
    canvas.DrawCircle(point1.X, point1.Y, 10, blackFill);
    canvas.DrawCircle(point2.X, point2.Y, 10, blackFill);

    // Draw the tangent lines
    canvas.DrawLine(point0.X, point0.Y, point1.X, point1.Y, dottedStroke);
    canvas.DrawLine(point2.X, point2.Y, point1.X, point1.Y, dottedStroke);

    // Draw the conic
    using (SKPath path = new SKPath())
    {
        path.MoveTo(point0);
        path.ConicTo(point1, point2, weight);
        canvas.DrawPath(path, redStroke);
    }
}
```

如您所見，沒有任何視覺化差異`ConicTo`以紅色顯示的路徑，且顯示供參考的基礎圓圈：

[![](beziers-images/coniccirculararc-small.png "Conic 圓弧頁面的三個螢幕擷取畫面")](beziers-images/coniccirculararc-large.png#lightbox "Conic 圓弧頁面的三個螢幕擷取畫面")

但是，角度設 180 度，以及數學失敗。

可惜的是在此情況下，`ConicTo`不支援負加權，因為可以使用另一個呼叫完成 （根據參數化的方程式） 的理論上來說，圓形`ConicTo`擁有相同的點，但負值的加權。 這可讓使用正是其中兩個建立完整的圓形`ConicTo`曲線根據任何角度之間 （但不是包括） 零度和 180 度。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
