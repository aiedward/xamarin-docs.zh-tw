---
title: 三種類型的貝茲曲線
description: 探索如何使用 SkiaSharp 轉譯三次方、 二次方，和 conic 貝茲曲線
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: charlespetzold
ms.author: chape
ms.date: 05/25/2017
ms.openlocfilehash: 312d487111f8e36170c97ca7a29fb91556c72569
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="three-types-of-bzier-curves"></a>三種類型的貝茲曲線

_探索如何使用 SkiaSharp 轉譯三次方、 二次方，和 conic 貝茲曲線_

貝茲曲線命名匹貝茲 (1910年 – 1999) 的法文工程師，在汽車公司 Renault 使用電腦輔助設計車內文的曲線。

貝茲曲線的互動式的設計適合已知： 它們是也運作正常&mdash;亦即，不會導致變成無限或變得不便曲線的 singularities &mdash; ，通常是悅耳. 字元外框輪廓的電腦中的字型，通常被定義的貝茲曲線：

![](beziers-images/beziersample.png "範例貝茲曲線")

維基百科文章[貝茲曲線](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)包含一些有用的資訊。 詞彙*貝茲曲線*實際上是指一系列的類似曲線。 SkiaSharp 支援三種類型的貝茲曲線，稱為*立方*、*二次方*，而*conic*。 Conic 就是所謂*合理二次方程式*。

## <a name="the-cubic-bzier-curve"></a>三次方貝茲曲線

三次方是貝茲曲線的貝茲曲線主旨出現時，大部分的開發人員認為的類型。

您可以新增三次方貝茲曲線，以`SKPath`物件使用[ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/SkiaSharp.SKPoint/)方法具有三個`SKPoint`參數，或[ `CubicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CubicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/System.Single/)個別多載`x`和`y`參數：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

曲線開始分佈的目前位置。 完整的三次方貝茲曲線是由四個點定義：

- 起點： 目前點分佈，或 （0，0） 中，如果`MoveTo`尚未呼叫
- 首先要程式控制點：`point1`中`CubicTo`呼叫
- 第二個控制點：`point2`中`CubicTo`呼叫
- 結束點：`point3`中`CubicTo`呼叫

結果曲線開始點開始，而結束於結束點。 曲線通常不會通過兩個控制點。而是它們函式多 like 磁鐵提取朝向它們曲線。

初步的三次方貝茲曲線的最佳方式是實驗。 這是目的**貝茲曲線**頁面上，衍生自`InteractivePage`。 [ **BezierCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)檔案會具現化`SKCanvasView`和`TouchEffect`。 [ **BezierCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)程式碼後置檔案會建立四個`TouchPoint`其建構函式中的物件。 `PaintSurface`事件處理常式建立`SKPath`呈現貝茲曲線，根據四個`TouchPoint`物件，並也繪製虛線正切函數控點從至結束點：

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

這裡執行所有三個平台上：

[![](beziers-images/beziercurve-small.png "頁面貝茲曲線的三個螢幕擷取畫面")](beziers-images/beziercurve-large.png#lightbox "頁面貝茲曲線的三個螢幕擷取畫面")

在數學上，曲線是三次方多項式。 曲線最相交直線在 3 個點。 開始處曲線永遠是正切函數，然後在與相同的方向，從一開始為直線指向第一個控制點。 結束點曲線永遠是正切函數，然後在與相同的方向，從第二個控制項的直線指向結束點。

連接的四個點的凸四邊形一律被限制三次方貝茲曲線。 這稱為*凸殼*。 如果上開始和結束點間的直線，直至控點，貝茲曲線就會轉譯成一條直線。 但曲線也可以跨本身，如從 Windows Mobile 裝置的螢幕擷取畫面所示。

路徑 contour 可以包含多個連接的三次方貝茲曲線，但兩個三次方貝茲曲線之間的連線會平滑，只有當下列三個點都 colinear （也就是上一條直線直至）：

- 第一個曲線的第二個控制點
- 第一個曲線，這也是第二個曲線開始點的結束點
- 第二個曲線的第一個控制點

上的下一個文件中[ **SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)您會發現可簡化將 smooth 已連線的貝茲曲線的定義的功能。

有時候會很有幫助呈現三次方貝茲曲線的基礎參數方程式。 如*t*範圍從 0 到 1，參數化的方程式如下：

x(t) = (1 – t)³x₀ + 3t(1 – t)²x₁ + 3t²(1 – t)x₂ + t³x₃

y(t) = (1-t) ³y₀ + 3t (1-t) ²y₁ + 3t² (1-t) y₂ + t³y₃

最高的 3 指數確認，這些都是三次方 polynomials。 所以可以輕鬆地驗證，當`t`等於 0、 點 (x₀ y₀)，這是起始點，以及當`t`等於 1，重點是 (x₃ y₃)，這是結束點。 附近的起點 (低值`t`) 的第一個控制點 （x₁、 y₁） 具有強式生效，並在接近結束點 (最高值的 ' t ') 的第二個控制點 （x₂、 y₂） 有很大的影響。

## <a name="bzier-curve-approximation-to-circular-arcs"></a>貝茲曲線近似值成圓弧

可能會很方便，用來呈現圓弧的貝茲曲線。三次方貝茲曲線可以近似圓弧很好四分之一圓形最讓整個圓形定義四個連接的貝茲曲線。 發行超過 25 年的前兩篇文章討論這個近似值：

> Tor Dokken box 等，「 的圓形曲度連續的貝茲曲線的良好近似值"*電腦輔助幾何設計 7* (1990)，33 41。

> Michael Goldapp、"圓弧由三次方的 Polynomials 近似值"*電腦輔助幾何設計 8* (1991)，227 238。

下圖顯示四個點標示為`pto`， `pt1`， `pt2`，和`pt3`定義近似圓弧的貝茲曲線 （顯示為紅色）：

![](beziers-images/bezierarc45.png "使用貝茲曲線的圓弧的近似值")

控點線從開始和結束點都正切函數的圓形，貝茲曲線，而且都有一段*L*。上述的第一個發行項會指出最佳的貝茲曲線近似圓弧時該長度*L*計算如下：

L = 4 × tan(α / 4) / 3

下圖顯示某個角度的 45 度，因此 L 等於 0.265。 在程式碼，該值會乘以所需，圓形的半徑。

**貝茲圓弧**頁面可讓您試驗定義的貝茲曲線，來模擬圓弧範圍最多 180 度的角度。 [ **BezierCircularArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)檔案會具現化`SKCanvasView`和`Slider`選取角度。 `PaintSurface`中的事件處理常式[ **BezierCircularArgPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)程式碼後置檔案會使用轉換來設定畫布的中心點 （0，0）。 它繪製在進行比較，該點上置中的圓形，然後再計算貝茲曲線的兩個控制點：

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

開始和結束點 (`point0`和`point3`) 會計算是根據一般的參數化方程式圓形。 因為圓形中心位於 （0，0），這些點也可視為星形向量從圓心到周長。 控點會讓它們在以這些星形向量直角是圓形的正切函數的行上。 在右角向量是只之交換的 X 和 Y 座標，以及其中一個進行負它們的原始向量。

以下是具有三個不同的角度的三個平台上執行的程式：

[![](beziers-images/beziercirculararc-small.png "三重螢幕擷取畫面的頁面貝茲圓弧")](beziers-images/beziercirculararc-large.png#lightbox "貝茲圓弧頁面的三個螢幕擷取畫面")

仔細查看 Windows 行動裝置版 畫面中，而且您會看到的貝茲曲線值得注意的是偏離半圓形角度 180 度，但它看起來以角度為 90 度符合季圓形正常顯示 iOS 畫面時。

計算兩個控制點的座標時相當簡單季圓形導向像這樣：

![](beziers-images/bezierarc90.png "四分之一圓形貝茲曲線的近似值")

如果圓形的半徑為 100，則*L*是 55，而這是要記住的簡單號碼。

**Number 圓形**頁面繪製的圖形間圓形與方形。 在這個陣列定義的第一個資料行中顯示其座標的四個貝茲曲線近似於圓形[ `SquaringTheCirclePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs)類別：

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

第二個資料行包含定義其區域大約是圓形的區域相同正方形的四個貝茲曲線的座標。 (繪製正方形與*確切*與指定的圓形區域是傳統無法解決幾何問題的[number 圓形](https://en.wikipedia.org/wiki/Squaring_the_circle)。)呈現包含貝茲曲線的方格，為每個曲線的兩個控制點相同，且都是 colinear 有開始和結束點，因此貝茲曲線會轉譯為一條直線。

陣列的第三個資料行是插補的動畫值。 頁面設定 16 毫秒的計時器和`PaintSurface`的速率會呼叫處理常式：

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

根據 sinusoidally 穩定值的插補點`t`。 插補的點再用來建構一系列的四個連接的貝茲曲線。 以下是動畫顯示為正方形從圓形進度的三個平台上執行：

[![](beziers-images/squaringthecircle-small.png "3 倍的螢幕擷取畫面的 Squaring 圓形頁面")](beziers-images/squaringthecircle-large.png#lightbox "Squaring 的三個螢幕擷取畫面 [圓形] 頁面")

沒有以演算法有足夠的彈性來呈現為圓弧及直線的曲線就無法使用這類動畫。

**貝茲無限大**頁面也會利用貝茲曲線的能力，來模擬圓弧。以下是`PaintSurface`處理常式[ `BezierInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs)類別：

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

可能是很好的練習來繪製圖形紙張上的這些座標，以查看它們相關。 無限符號的重心是點 （0，0），而且在兩個迴圈具有的中心點 （–150，0） 和 150 (0） 和半徑為 100。 一系列`CubicTo`命令，您可以看到 X 座標的控制點採用性 –95 和 –205 的值 （這些值是 –150 加號和減號 55） 時，205 和 95 (150 加號和減號 55)，以及 250 –250 右側和左側邊。 唯一的例外狀況時，無限大符號跨越本身中央。 在此情況下，控點會有與 50 和 –50 拉直出曲線中央附近的組合。

以下是所有三個平台上的無限大符號：

[![](beziers-images/bezierinfinity-small.png "三個螢幕擷取畫面的貝茲無限大頁面")](beziers-images/bezierinfinity-large.png#lightbox "頁面貝茲無限大的三個螢幕擷取畫面")

它是稍微順暢朝中央所呈現的無限大符號**弧線無限大**頁面[**三個方法可以繪製弧形**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)發行項。

## <a name="the-quadratic-bzier-curve"></a>二次方貝茲曲線

二次方貝茲曲線有只有一個控制點，且只有三個點定義曲線： 起始點、 程式控制點和結束點。 參數化的方程式是三次方貝茲曲線，非常類似，不同之處在於最高的指數，是 2，因此曲線是二次方多項式：

x(t) = (1 – t)²x₀ + 2t(1 – t)x₁ + t²x₂

y(t) = (1-t) ²y₀ + 2t (1-t) y₁ + t²y₂

若要新增的二次方貝茲曲線的路徑，請使用[ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/)方法或[ `QuadTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.QuadTo/p/System.Single/System.Single/System.Single/System.Single/)分別使用不同的多載`x`和`y`座標：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法會從目前的位置，以便新增曲線`point2`與`point1`為控制項控點。

您可以試驗二次方貝茲曲線**二次方曲線**頁面上，非常類似於**貝茲曲線**頁面上，除了它有只有三個觸控點。 以下是`PaintSurface`中的處理常式[ **QuadraticCurve.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs)程式碼後置檔案：

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

和以下所有的三個平台上執行它：

[![](beziers-images/quadraticcurve-small.png "三個螢幕擷取畫面的二次方曲線頁面")](beziers-images/quadraticcurve-large.png#lightbox "二次方曲線頁面的三個螢幕擷取畫面")

點線在曲線開始點和結束點的正切函數，且符合在控制項控點。

如果您需要的一般形狀，曲線，但您偏好的一個控制點而非兩個方便性，不論二次方貝茲適合。 二次方貝茲呈現比任何其他曲線，這就是為什麼它在內部使用 Skia 中呈現橢圓弧形更有效率。

不過，二次方貝茲曲線的形狀不橢圓形，這就是為什麼多個二次方 Béziers 才能顯示概略的橢圓形弧線。二次方貝茲會先 parabola 的區段。

## <a name="the-conic-bzier-curve"></a>Conic 貝茲曲線

Conic 貝茲曲線&mdash;也稱為合理二次方貝茲曲線&mdash;是加入較新的系列的貝茲曲線。 例如二次方貝茲曲線，合理的二次方貝茲曲線牽涉到起點、 結束點和一個控制點。 但也需要合理的二次方貝茲曲線*加權*值。 它會呼叫*合理*二次方，因為參數化的公式都涉及比例。

參數化的方程式 X 和 Y 是共用相同的分母的比例。 以下是對於分母的方程式*t*範圍從 0 到 1 和加權值是*w*:

d(t) = (1-t) ² + 2wt(1 – t) + t²

理論上來說，合理二次方程式可以包含三個不同的加權值，各供一個三個詞彙中，但這些可以簡化為只在一個中介詞彙的加權值。

X 和 Y 座標的參數化的方程式是與參數化的方程式的二次方貝茲類似的不同之處在於中間詞彙也包含的加權值，以及運算式除以分母：

x(t) = ((1 – t) ²x₀ + 2wt (1-t) x₁ + t²x₂)) ÷ d(t)

y(t) = ((1 – t) ²y₀ + 2wt (1-t) y₁ + t²y₂)) ÷ d(t)

也稱為合理的二次方貝茲曲線*conics*因為它們只可以代表任何 conic 區段的區段&mdash;hyperbolas、 parabolas、 橢圓形和圓形。

若要新增合理的二次方貝茲曲線的路徑，請使用[ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/)方法或[ `ConicTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ConicTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/)分別使用不同的多載`x`和`y`座標：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

請注意最終`weight`參數。

**Conic 曲線**頁面可讓您試驗這些曲線。 `ConicCurvePage` 類別衍生自 `InteractivePage`。 [ **ConicCurvePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)檔案會具現化`Slider`選取 2 – 2 之間的加權值。 [ **ConicCurvePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)程式碼後置檔案會建立三個`TouchPoint`物件，而`PaintSurface`處理常式只會轉譯結果曲線正切函數的行至控制項重點：

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

這裡執行所有三個平台上：

[![](beziers-images/coniccurve-small.png "三個螢幕擷取畫面的 Conic 曲線頁面")](beziers-images/coniccurve-large.png#lightbox "Conic 曲線頁面的三個螢幕擷取畫面")

如您所見，控制項控點，就好像的加權就更高版本時提取更接近曲線。 加權值是零，當曲線就會變成直線從起始點的結束點。

理論上，若為負加權允許，以及導致曲線，以彎*離開*從控制項控點。 不過，加權為 – 1 或低於原因變成負數的特定值的參數化的方程式在分母*t*。 可能是基於這個理由，若為負加權，會略過`ConicTo`方法。 **Conic 曲線**程式可讓您設定為負加權，但您可以看到所進行實驗，若為負加權的權數為零，相同的效果，也會造成呈現直線。

它是很容易就能衍生的控制項控點和加權來使用`ConicTo`方法來繪製圓弧最多 （但不是包括） 半圓形。 在下列圖表中，從開始和結束點的切線符合在控制項控點。

![](beziers-images/conicarc.png "Conic 弧線的呈現圓弧")

您可以使用三角函數來判斷的距離，從圓心的控制點： 它是除以一半的角度 α 的餘弦，圓形的半徑。 若要開始和結束點之間繪製圓弧，將加權設定為該相同一半的角度餘弦。 請注意，如果角度是 180 度，然後切線永遠無法符合加權值是零。 不過，如角度小於 180 度、 數學可以正常運作。

**Conic 圓弧**頁面為其示範。 [ **ConicCircularArc.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)檔案會具現化`Slider`選取角度。 `PaintSurface`中的處理常式[ **ConicCircularArc.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)控制項控點和重量，計算程式碼後置檔案：

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

如您所見，有無差別 visual`ConicTo`路徑顯示為紅色，且基礎圓圈顯示參考：

[![](beziers-images/coniccirculararc-small.png "三個螢幕擷取畫面的 Conic 圓弧頁面")](beziers-images/coniccirculararc-large.png#lightbox "Conic 圓弧頁面的三個螢幕擷取畫面")

但是設 180 度和數學失敗的角度。

可惜的是在此情況下，`ConicTo`不支援為負加權，因為可以使用另一個呼叫完成 （根據參數化的方程式） 理論上，圓形`ConicTo`具有相同的點但負值的權重。 這樣可讓具有兩個只建立整個圓形`ConicTo`曲線根據任何角度之間 （但不是包括） 零度和 180 度。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
