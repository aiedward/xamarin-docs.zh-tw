---
title： "三種類型的貝茲曲線" 描述： "本文說明如何使用 SkiaSharp 來轉譯應用程式中的三、二次方和圓錐曲線 Xamarin.Forms ，並使用範例程式碼示範此動作。
skiasharp assetid： 8FE0F6DC-16BC-435F-9626-DD1790C0145A 作者： davidbritch ms。作者： dabritch ms。日期：05/25/2017 不是-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="three-types-of-bzier-curves"></a>三種類型的貝茲曲線

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索如何使用 SkiaSharp 呈現三重、二次方和圓錐的貝茲曲線_

Bezier 曲線是在聖皮爾 Bezier （1910–1999）之後命名，這是汽車公司 Renault 的法文工程師，其使用汽車內電腦輔助設計的曲線。

貝茲曲線的功能非常適合互動式設計：它們的行為也不太 singularities，因此 &mdash; 不會造成曲線變得無限或難以 &mdash; 執行，而且通常會內賞心悅目滿意的效果：

![樣本貝茲曲線](beziers-images/beziersample.png)

以電腦為基礎的字型的字元外框，通常是以貝茲曲線來定義。

有關[**貝茲曲線**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve)的維琪百科文章包含一些實用的背景資訊。 「*貝茲曲線*」一詞實際上是指類似曲線的系列。 SkiaSharp 支援三種類型的貝茲曲線，稱為「*三*角」、「*二次*」和「*圓錐*」。 圓錐也稱為*有理數二次*方。

## <a name="the-cubic-bzier-curve"></a>三次方貝茲曲線

這三種類型是大部分開發人員在貝茲曲線的主旨出現時所想要的貝茲曲線類型。

您可以 `SKPath` 使用 [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) 具有三個參數的方法 `SKPoint` ，或 [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) 具有個別 `x` 和 `y` 參數的多載，將三次方貝茲曲線新增至物件：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

曲線會從等高線的目前點開始。 完整的三次方貝茲曲線是由四個點所定義：

- 起點：在輪廓中的目前點，如果尚未呼叫，則為（0，0） `MoveTo`
- 第一個控制點： `point1` 呼叫中的 `CubicTo`
- 第二個控制點： `point2` 在 `CubicTo` 呼叫中
- 結束點： `point3` 在 `CubicTo` 呼叫中

結果曲線會從起點開始，並在結束點結束。 曲線通常不會通過這兩個控制點;相反地，控制點的運作方式很像磁鐵，可以向它們拉出曲線。

若要瞭解三次方貝茲曲線的最佳方式，就是透過實驗。 這是 [**貝茲曲線**] 頁面的用途，其衍生自 `InteractivePage` 。 [**BezierCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)會具現化 `SKCanvasView` 和 `TouchEffect` 。 [**BezierCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)程式碼後置檔案會在其函式中建立四個 `TouchPoint` 物件。 `PaintSurface`事件處理常式會建立 `SKPath` 來轉譯以四個物件為基礎的貝茲曲線 `TouchPoint` ，而且也會從控制點將圓點的正切線條繪製到結束點：

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

它正在執行：

[![[貝茲曲線] 頁面的三重螢幕擷取畫面](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

在數學上，曲線是三個多項式。 曲線最多隻會在三個點的直線相交。 在起點，曲線一律會在從起點到第一個控制點的直線中，與相同的方向相切。 在結束點，曲線一律會從第二個控制點到結束點的直線，並以與相同的方向來正切。

三次方貝茲曲線一律是由連接四個點的凸四邊形所限制。 這稱為*凸*殼。 如果控制點位於起點和終點之間的直線，則貝茲曲線會呈現為直線。 但曲線也可以跨越自己，如第三個螢幕擷取畫面所示。

路徑等高線可以包含多個連接的三次方貝茲曲線，但只有在下列三個點 colinear （也就是在直線上）時，兩個三次方貝茲曲線之間的連接才會平滑：

- 第一個曲線的第二個控制點
- 第一個曲線的結束點，也是第二個曲線的起點。
- 第二個曲線的第一個控制點

在下一篇有關[**SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)的文章中，您將會發現一個設備，讓您能夠輕鬆定義平滑連線的貝茲曲線。

有時，知道會呈現三次方貝茲曲線的基礎參數方程式，會很有用。 對於範圍從0到1的*t* ，參數化方程式如下所示：

x （t） = （1– t）³ x ₀ + 3t （1– t）² x ₁ + 3t ²（1– t） x ₂ + t ³ x ₃

y （t） = （1– t）³ y ₀ + 3t （1– t）² y ₁ + 3t ²（1– t） y ₂ + t ³ y ₃

最高的指數3會確認這些是三 polynomials。 當 `t` 等於0時，點是（x ₀，y ₀），也就是起點，而當 `t` 等於1時，點是（x ₃，y ₃），也就是結束點。 接近起點（針對較低的值 `t` ），第一個控制點（x ₁、y ₁）的效果很強，而接近結束點（高值為 ' t '）時，第二個控制點（x ₂、y ₂）會有強烈的效果。

## <a name="bezier-curve-approximation-to-circular-arcs"></a>圓弧的貝茲曲線近似值

有時候，使用貝茲曲線來呈現圓弧會很方便。三次方貝茲曲線可能會大致地逼近圓弧，因此四個連接的貝茲曲線可以定義整個圓形。 這項近似值會在25年前發佈的兩篇文章中討論：

> Tor Dokken，加上「曲線的良好近似值-連續的貝茲曲線」、「*電腦輔助幾何設計 7* （1990）、33-41。

> Michael Goldapp，「依三次的圓弧 Polynomials」，*電腦輔助幾何設計 8* （1991），227-238。

下圖顯示四個點，分別標示了、、 `pto` `pt1` `pt2` 和 `pt3` 定義一個近似圓弧的貝茲曲線（以紅色顯示）：

![以貝茲曲線繪製圓弧的近似值](beziers-images/bezierarc45.png)

從起點和終點到控制點的線條會與圓形或貝茲曲線相切，而且其長度為*L*。上述第一個提及的文章指出，當長度*L*的計算方式如下時，貝茲曲線最適合圓弧：

L = 4 × tan （α/4）/3

下圖顯示45度的角度，因此 L 等於0.265。 在程式碼中，該值會乘以所需的圓形半徑。

[**貝塞爾**圓弧] 頁面可讓您試驗定義貝茲曲線，使其角度接近最高達180度的圓弧。 [**BezierCircularArcPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)會具現化和， `SKCanvasView` `Slider` 以選取角度。 在 `PaintSurface` [**BezierCircularArgPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)程式碼後置檔案中的事件處理常式會使用轉換，將點（0，0）設為畫布的中心。 它會繪製以該點為中心的圓形以進行比較，然後計算貝茲曲線的兩個控制點：

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

開始和結束點（ `point0` 和 `point3` ）是根據圓形的一般參數方程式來計算。 因為圓形是以（0，0）為中心，所以這些點也可以視為從圓形中央到圓周的放射狀向量。 控制點會在與圓形相切的線條上，因此它們會以適當的角度指向這些放射狀向量。 指向另一個角度的向量就是已交換 X 和 Y 座標的原始向量，而其中一個設為負值。

以下是以不同角度執行的程式：

[![[貝塞爾圓弧] 頁面的三重螢幕擷取畫面](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

請仔細查看第三個螢幕擷取畫面，您會看到當角度為180度時，貝茲曲線與半圓的偏差很明顯，但 iOS 畫面顯示在角度為90度時，它似乎符合四分之一圓。

當季圓形的方向如下時，計算兩個控制點的座標相當簡單：

![具有貝茲曲線的季圓近似值](beziers-images/bezierarc90.png)

如果圓形的半徑為100，則*L*是55，而這是很容易記住的數位。

「**圓形**」頁面會在圓形和正方形之間繪製圖形。 圓形的近似值是四個貝茲曲線，其座標會顯示在類別中此陣列定義的第一個資料行中 [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) ：

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

第二個數據行包含四個貝茲曲線的座標，其定義的正方形與圓形的面積大致相同。 （以*確切*的面積繪製正方形，做為指定的圓形，這是用來[求出圓形](https://en.wikipedia.org/wiki/Squaring_the_circle)的傳統死結幾何問題）。對於呈現具有貝茲曲線的正方形，每個曲線的兩個控制點都相同，而且會與起點和終點 colinear，因此會以直線呈現貝茲曲線。

陣列的第三個數據行是用於動畫的插補值。 此頁面會將計時器設定為16毫秒，並 `PaintSurface` 以該速率呼叫處理常式：

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

這些點是以的 sinusoidally 不穩定值為基礎的插補 `t` 。 然後，插補點會用來建立一連串四個連接的貝茲曲線。 以下是執行的動畫：

[![將 [圓形] 頁面重數的三向螢幕擷取畫面](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

如果曲線的以演算法方式彈性足以轉譯成圓弧和直線，這種動畫就不可能發生。

[**貝塞爾無限大**] 頁面也會利用貝茲曲線的功能來逼近圓弧。以下是 `PaintSurface` 來自類別的處理常式 [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) ：

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

這可能是在圖表紙張上繪製這些座標以查看其相關方式的最佳做法。 無限大符號以點（0，0）為中心，而兩個迴圈的中心為（–150，0）和（150，0），而半徑為100。 在這一系列的 `CubicTo` 命令中，您可以看到控制點的 X 座標採用–95和–205的值（這些值包括–150加和減55）、205和95（150 plus 和減號55），以及向右和左側的250和–250。 唯一的例外是當無限大的符號在中央時，會將其本身相交。 在此情況下，控制點具有結合50和–50的座標，可將曲線拉近中央。

以下是無限大符號：

[![貝塞爾無限大頁面的三重螢幕擷取畫面](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

從[**三種繪製弧線文章的方式來**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)看，中心比「 **arc 無限大**」頁面所轉譯的無限大符號稍微平滑。

## <a name="the-quadratic-bzier-curve"></a>二次方貝茲曲線

二次方貝茲曲線只有一個控制點，而曲線只是由三個點所定義：起點、控制點和終點。 參數化方程式與三次貝茲曲線非常類似，不同之處在于最高的指數是2，因此曲線是二次多項式：

x （t） = （1– t）² x ₀ + 2t （1– t） x ₁ + t ² x ₂

y （t） = （1– t）² y ₀ + 2t （1– t） y ₁ + t ² y ₂

若要將二次方貝茲曲線加入至路徑，請使用方法或多載， [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) 並使用個別的 `x` `y` 座標：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法會將從目前位置到的曲線， `point2` 加入 `point1` 做為控制點。

您可以使用**二次方曲線**頁面來實驗二次方貝茲曲線，這非常類似于 [**貝茲曲線**] 頁面，但只有三個觸控點。 以下是 `PaintSurface` [**QuadraticCurve.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs)程式碼後置檔案中的處理常式：

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

而且它正在執行：

[![二次方曲線頁面的三重螢幕擷取畫面](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

點線會在起點和終點的曲線上相切，並在控制點上符合。

如果您需要一般圖形的曲線，但您偏好只是一個控制點（而不是兩個）的便利性，則二次方貝塞爾就很好用。 二次方貝塞爾的呈現比其他任何曲線更有效率，這就是為什麼它會在 Skia 內部用來轉譯橢圓形弧形。

不過，二次方貝茲曲線的形狀不是橢圓形，這就是為什麼需要多個二次方 Béziers 來接近橢圓形弧線的原因。二次方貝塞爾會改為 parabola 的區段。

## <a name="the-conic-bzier-curve"></a>圓錐貝茲曲線

圓錐的貝茲曲線 &mdash; 也稱為有理數二次方貝茲曲線 &mdash; ，這是一系列貝茲曲線的相對最近新增項。 就像二次方貝茲曲線，有理數二次方貝茲曲線牽涉到起點、終點和一個控制點。 但有理數二次方貝茲曲線也需要*權數*值。 因為參數化公式牽涉到比例，所以稱為*有理數*二次方。

X 和 Y 的參數化方程式是共用相同分母的比率。 以下是*t*範圍從0到1以及權數值為*w*的分母方程式：

d （t） = （1– t）² + 2wt （1– t） + t ²

理論上，有理二分可能牽涉到三個不同的加權值，每一個都有三個詞彙，但可以簡化為中間詞彙的一個權數值。

X 和 Y 座標的參數方程式類似于二次方貝茲曲線的參數方程式，不同之處在于中間詞彙也包含權數值，而運算式則除以分母：

x （t） = （（1– t）² x ₀ + 2wt （1– t） x ₁ + t ² x ₂））÷ d （t）

y （t） = （（1– t）² y ₀ + 2wt （1– t） y ₁ + t ² y ₂））÷ d （t）

有理數二次方貝茲曲線也稱為*conics* ，因為它們可以完全代表任何圓錐區段的線段 &mdash; hyperbolas、parabolas、橢圓形和圓形。

若要在路徑中加入有理數二次方貝茲曲線，請使用方法或多載搭配 [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) 個別 `x` 和 `y` 座標：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

請注意最後一個 `weight` 參數。

[**圓錐曲線**] 頁面可讓您試驗這些曲線。 `ConicCurvePage` 類別衍生自 `InteractivePage`。 [**ConicCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)會具現化 `Slider` ，以選取–2到2之間的權數值。 [**ConicCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)程式碼後置檔案會建立三個 `TouchPoint` 物件，而 `PaintSurface` 處理常式只會將具有正切線條的結果曲線轉譯為控制點：

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

它正在執行：

[![[圓錐曲線] 頁面的三重螢幕擷取畫面](beziers-images/coniccurve-small.png)](beziers-images/coniccurve-large.png#lightbox)

如您所見，當權數較高時，控制點似乎會將曲線拉往更多的位置。 當權數為零時，曲線會從起點到終點的直線。

理論上，允許負權數，並使曲線*遠離*控制點。 不過，-1 或以下的權數會使參數方程式中的分母成為負數，以用於*t*的特定值。 這可能是因為在方法中會忽略負權數 `ConicTo` 。 「**圓錐曲線**」程式可讓您設定負權數，但如您所見，您可以藉由實驗來看出，負加權的效果等同于零的加權，並會導致呈現直線。

您很容易就能衍生控制點和權數，以使用 `ConicTo` 方法來繪製圓弧（但不包括）半圓。 在下圖中，起點和終點的正切線條符合控制點。

![圓弧的圓錐弧線呈現](beziers-images/conicarc.png)

您可以使用三角函數來判斷控制點與圓形中心的距離：它是圓形半徑除以α角度的余弦值。 若要在起點和終點之間繪製圓弧，請將權數設定為該角度的相同余弦值。 請注意，如果角度是180度，則正切線條永遠不會符合，而權數則為零。 但針對小於180度的角度，數學運算會正常運作。

[**圓錐**圓弧] 頁面會示範這種情況。 [**ConicCircularArc**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)會具現化 `Slider` 以選取角度。 `PaintSurface` [**ConicCircularArc.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)程式碼後置檔案中的處理常式會計算控制點和權數：

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

如您所見， `ConicTo` 紅色所顯示的路徑和用來參考的基礎圓形之間沒有視覺差異：

[![圓錐圓弧頁的三向螢幕擷取畫面](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

但將角度設定為180度，而數學運算失敗。

在此情況下，這種情況並不 `ConicTo` 支援負權數，因為理論上（根據參數化方程式），可以使用相同點的另一個呼叫來完成圓形， `ConicTo` 但權數的負值值則是。 這可讓您 `ConicTo` 根據介於（但不包括）零度和180度之間的任何角度，建立只包含兩條曲線的整個圓形。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
