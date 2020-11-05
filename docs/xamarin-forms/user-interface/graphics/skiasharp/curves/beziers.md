---
title: 三種類型的貝茲曲線
description: 本文說明如何使用 SkiaSharp 在應用程式中呈現三次、二和圓錐的貝茲曲線 Xamarin.Forms ，並使用範例程式碼來示範。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8FE0F6DC-16BC-435F-9626-DD1790C0145A
author: davidbritch
ms.author: dabritch
ms.date: 05/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aee429b3dcc898ae0663817d52ed5b03d919e239
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375365"
---
# <a name="three-types-of-bzier-curves"></a>三種類型的貝茲曲線

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索如何使用 SkiaSharp 來呈現三次、二個和圓錐的貝茲曲線_

貝茲曲線的名稱是在聖皮爾貝塞爾 (1910 – 1999) ，這是汽車公司 Renault formula one 的法文工程師，他們使用曲線進行電腦輔助設計的車輛主體。

已知的貝茲曲線非常適合用於互動式設計：它們的效果很好，而 &mdash; 不會造成曲線變成無限或難以 singularities， &mdash; 而且通常以賞心悅目滿意：

![樣本貝茲曲線](beziers-images/beziersample.png)

電腦字型的字元外框通常是使用貝茲曲線來定義。

有關 [**貝茲曲線**](https://en.wikipedia.org/wiki/B%C3%A9zier_curve) 的維琪百科文章包含一些實用的背景資訊。 「 *貝茲曲線* 」實際上是指類似曲線的系列。 SkiaSharp 支援三種類型的貝茲曲線，稱為 *立方* 、 *二* 和 *圓錐* 。 圓錐也稱為 *有理數二* 。

## <a name="the-cubic-bzier-curve"></a>三次方貝茲曲線

三種曲線是指當貝茲曲線的主旨出現時，大部分開發人員認為的貝茲曲線類型。

您可以 `SKPath` 使用 [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKPoint)) 具有三個參數的方法 `SKPoint` ，或 [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo(System.Single,System.Single,System.Single,System.Single,System.Single,System.Single)) 具有不同 `x` 和 `y` 參數的多載，將三次貝茲曲線新增至物件：

```csharp
public void CubicTo (SKPoint point1, SKPoint point2, SKPoint point3)

public void CubicTo (Single x1, Single y1, Single x2, Single y2, Single x3, Single y3)
```

曲線會從目前的輪廓點開始。 完整的三次方貝茲曲線是由四個點所定義：

- 起點：輪廓中的目前點，或 (0，0) （如果尚未 `MoveTo` 呼叫）
- 第一個控制點： `point1` 在 `CubicTo` 呼叫中
- 第二個控制點： `point2` 在 `CubicTo` 呼叫中
- 結束點： `point3` 在 `CubicTo` 呼叫中

結果曲線開始于起點，並結束于結束點。 曲線通常不會通過兩個控制點;相反地，控制點的運作方式非常類似磁鐵來拉出曲線。

針對三次貝茲曲線感到感覺的最佳方式是透過實驗。 這是衍生自的 **貝茲曲線** 頁面用途 `InteractivePage` 。 [**BezierCurvePage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml)檔案具現化 `SKCanvasView` 和 `TouchEffect` 。 [**BezierCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCurvePage.xaml.cs)程式碼後端檔案會在其函式中建立四個 `TouchPoint` 物件。 `PaintSurface`事件處理常式會建立， `SKPath` 以根據四個物件來轉譯貝茲曲線 `TouchPoint` ，也會從控制項點將點線相切線繪製到結束點：

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

[![貝茲曲線頁面的三重螢幕擷取畫面](beziers-images/beziercurve-small.png)](beziers-images/beziercurve-large.png#lightbox)

在數學上，曲線是三個多項式。 曲線最多隻會在三個點相交一條直線。 在起點，曲線一律會與從起點到第一個控制點之間的直線相切，而且會以相同的方向進行。 在結束點，曲線一律會與第二個控制點的直線相切地相切到最後一個點。

立體貝茲曲線一律會由連接四個點的凸四邊形所限制。 這稱為 *凸* 殼。 如果控制點位於起點和終點之間的直線，則貝茲曲線會轉譯為直線。 但曲線也可以橫跨本身，如第三個螢幕擷取畫面所示。

路徑輪廓可以包含多個連接的三次方貝茲曲線，但只有在下列三個點 colinear (也就是在直線) 上時，兩個三次方貝茲曲線之間的連接才會變得平滑：

- 第一個曲線的第二個控制點
- 第一個曲線的結束點，也就是第二個曲線的起點。
- 第二個曲線的第一個控制點

[**在下一篇文章中，您**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)將探索可讓您輕鬆定義順暢連接貝茲曲線的設施。

有時候，知道呈現三次方貝茲曲線的基礎參數方程式會很有用。 針對 *t* 範圍從0到1，參數化方程式如下所示：

x (t) = (1 – t) ³ x ₀ + 3t (1 – t) ² x ₁ + 3t ² (1 – t) x ₂ + t ³ x ₃

y (t) = (1 – t) ³ y ₀ + 3t (1 – t) ² y ₁ + 3t ² (1 – t) y ₂ + t ³ y ₃

3的最高指數會確認這些是三 polynomials。 您可以輕鬆地確認當 `t` 等於0時，點是 (x ₀，y ₀) ，也就是起點，而 `t` 等於1，則點是 (x ₃，y ₃) ，也就是結束點。 接近起點 () 的低值 `t` 、第一個控制點 (x ₁、y ₁) 具有強烈效果，而接近結束點 (最高值 ' t ' ) 第二個控制點 (x ₂，y ₂) 具有強烈效果。

## <a name="bezier-curve-approximation-to-circular-arcs"></a>貝茲曲線近似值至圓形弧形

有時候，使用貝茲曲線呈現圓弧是很方便的。三次方貝茲曲線可將圓弧的近似程度最高到四分之一圓形，因此四個連接的貝茲曲線可以定義整個圓。 這項近似值將在25年前發行的兩篇文章中討論：

> Tor Dokken，例如，「Curvature-Continuous 貝茲曲線的圓形良好近似值」、 *電腦輔助幾何設計 7* (1990) 、33-41。

> Michael Goldapp，「每三立方 Polynomials 的圓形弧形近似值」、 *電腦輔助幾何設計 8* (1991) 、227-238。

下圖顯示四個標示為 `pto` 、 `pt1` 、 `pt2` 和 `pt3` 定義貝茲曲線的點， (以紅色) 顯示，接近圓弧：

![使用貝茲曲線的圓弧近似值](beziers-images/bezierarc45.png)

從開始和結束點到控制點的線條會與圓形和貝茲曲線相切，且長度為 *L* 。上面提及的第一篇文章指出，當該長度 *L* 的計算方式與下列類似時，貝茲曲線最接近圓形弧線：

L = 4 × tan (α/4) /3

圖例顯示45度的角度，因此 L 等於0.265。 在程式碼中，該值會乘以所需的圓形半徑。

[ **貝塞爾圓弧** ] 頁面可讓您試驗如何定義貝茲曲線，以接近最多180度的角度圓弧形。 [**BezierCircularArcPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml)可具現化 `SKCanvasView` 和， `Slider` 以選取角度。 `PaintSurface` [**BezierCircularArgPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierCircularArcPage.xaml.cs)程式碼後置檔案中的事件處理常式會使用轉換，將點 (0，0) 設定為畫布的中心。 它會繪製以該點為中心的圓形以進行比較，然後計算貝茲曲線的兩個控制點：

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

 (和) 的起點和 `point0` 終點 `point3` 會根據圓形的一般參數方程式來計算。 因為圓形是以 (0、0) 為中心，所以這些點也可以視為從圓形中央到圓周的放射狀向量。 控制點位於與圓形相切的線條上，因此它們在這些放射狀向量的角度都是正確的。 以右角為另一個向量，就是已交換 X 和 Y 座標的原始向量，而其中一個會成為負數。

以下是以不同角度執行的程式：

[![貝塞爾圓弧頁面的三重螢幕擷取畫面](beziers-images/beziercirculararc-small.png)](beziers-images/beziercirculararc-large.png#lightbox)

仔細看一下第三個螢幕擷取畫面，您會看到當角度為180度時，貝茲曲線與半圓相偏離，但 iOS 畫面顯示當角度為90度時，看起來像四分之一的圓形。

當季圓形的方向如下時，計算兩個控制點的座標相當簡單：

![具有貝茲曲線的季圓形近似值](beziers-images/bezierarc90.png)

如果圓形的半徑是100，則 *L* 是55，這是容易記住的數位。

[ **圓形** ] 頁面會在圓形與正方形之間建立圖表的動畫。 圓形的近似值是四個貝茲曲線，其座標會顯示在類別中這個陣列定義的第一個資料行 [`SquaringTheCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/SquaringTheCirclePage.cs) ：

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

第二個數據行包含四個貝茲曲線的座標，這些曲線定義區域與圓形區域大約相同的正方形。  (將正方形繪製為給定圓形的精確區域，就是將 [圓形](https://en.wikipedia.org/wiki/Squaring_the_circle)*精確度* 的傳統無法解決幾何問題。 ) 若要呈現具有貝茲曲線的正方形，則每個曲線的兩個控制點都相同，而且會以起點和終點 colinear，因此會以直線線呈現貝茲曲線。

陣列的第三個數據行是用於動畫的插補值。 此頁面會設定16毫秒的計時器，並 `PaintSurface` 以該速率呼叫處理常式：

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

這些點會根據的 sinusoidally 不穩定值進行插補 `t` 。 然後，插補點會用來建立一連串四個連接的貝茲曲線。 以下是正在執行的動畫：

[![以圓形頁面為平方的三重螢幕擷取畫面](beziers-images/squaringthecircle-small.png)](beziers-images/squaringthecircle-large.png#lightbox)

這種動畫不可能有演算法有彈性的曲線，足以轉譯成圓弧和直線。

**貝塞爾** 曲線頁面也會利用貝茲曲線的功能來逼近圓弧。以下是 `PaintSurface` 來自類別的處理常式 [`BezierInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/BezierInfinityPage.cs) ：

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

這可能是在圖形紙上繪製這些座標以查看它們如何相關的理想做法。 無限大符號的中心圍繞點 (0、0) ，而兩個迴圈的中心為 ( –150、0) 和 (150、0) 和半徑100。 在這一系列的 `CubicTo` 命令中，您可以看到控制點的 X 座標（以-95 和–205的值為單位） (這些值為–150加上減去 55) 、205和 95 (150 加和減 55) ，以及右邊和-250，以及右方和左邊。 唯一的例外狀況是當無限大符號本身在中心內時。 在這種情況下，控制點的座標具有50和–50的組合，以將中央的曲線拉近。

以下是無限大符號：

[![貝塞爾無限大頁面的三重螢幕擷取畫面](beziers-images/bezierinfinity-small.png)](beziers-images/bezierinfinity-large.png#lightbox)

從 [**三種用來繪製弧形文章的方式來**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)看，中間點會比 **弧線無限大** 頁面所轉譯的無限大符號稍微平滑。

## <a name="the-quadratic-bzier-curve"></a>二次方貝茲曲線

二次方貝茲曲線只有一個控制點，而曲線的定義只是三個點：起點、控制點和結束點。 參數方程式非常類似于三次方貝茲曲線，不同之處在于最高的指數是2，因此曲線是二多項式：

x (t) = (1 – t) ² x ₀ + t.inv.2t (1 – t) x ₁ + t ² x ₂

y (t) = (1 – t) ² y ₀ + t.inv.2t (1 – t) y ₁ + t ² y ₂

若要將二次方貝茲曲線新增至路徑，請使用方法或多載搭配 [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint)) [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo(System.Single,System.Single,System.Single,System.Single)) 個別 `x` 和 `y` 座標：

```csharp
public void QuadTo (SKPoint point1, SKPoint point2)

public void QuadTo (Single x1, Single y1, Single x2, Single y2)
```

方法會將目前位置的曲線新增至 `point2` `point1` 做為控制點的。

您可以使用 **二次曲線** 頁面來試驗二次方貝茲曲線，這與 **貝茲曲線** 頁面非常類似，但只有三個觸控點。 以下是 `PaintSurface` [**QuadraticCurve.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/QuadraticCurvePage.xaml.cs) 程式碼後端檔案中的處理常式：

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

[![二次曲線頁面的三重螢幕擷取畫面](beziers-images/quadraticcurve-small.png)](beziers-images/quadraticcurve-large.png#lightbox)

虛線會與起點和終點的曲線相切，並符合控制點。

如果您需要一個「一般」圖形的曲線，但您偏好只是一個控制點而不是兩個的便利性，則二次方貝塞爾很好用。 二次貝塞爾呈現的效果比任何其他曲線更有效率，這也是為什麼在 Skia 內部用來呈現橢圓弧線的原因。

但是，二次方貝茲曲線的形狀並非橢圓，這也是為什麼需要多個二次 Béziers 來接近橢圓弧線的原因。二次方程式是 parabola 的區段。

## <a name="the-conic-bzier-curve"></a>圓錐貝茲曲線

圓錐貝茲曲線 &mdash; 也稱為有理二次方貝茲曲線 &mdash; ，這是一系列貝茲曲線的相對最近加法。 如同二次方貝茲曲線，有理數二次方貝茲曲線牽涉到一個起點、一個結束點和一個控制點。 但有理數二次方貝茲曲線也需要 *加權* 值。 因為參數化公式牽涉到比例，所以稱為 *有理數* 二。

X 和 Y 的參數方程式是共用相同分母的比率。 以下是 *t* 的分母方程式範圍，範圍介於0到1，而權數值為 *w* ：

d (t) = (1 – t) ² + 2wt (1 – t) + t ²

理論上，有理數二次可能牽涉到三個不同的加權值（三個詞彙各一個），但這三個詞彙可以簡化為中間詞彙上的一個加權值。

X 和 Y 座標的參數方程式類似于二次方貝茲曲線的參數方程式，不同之處在于中間詞彙也包含權數值，而運算式則除以分母：

x (t) = ( # B3 1 – t) ² x ₀ + 2wt (1 – t) x ₁ + t ² x ₂) # A8 ÷ d (t) 

y (t) = ( # B3 1 – t) ² y ₀ + 2wt (1 – t) y ₁ + t ² y ₂) # A8 ÷ d (t) 

合理的二次方貝茲曲線也稱為 *conics* ，因為它們可以精確呈現任何圓錐區段 &mdash; hyperbolas、parabolas、橢圓形和圓形的區段。

若要在路徑中新增有理二次方貝茲曲線，請使用方法或多載搭配 [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo(System.Single,System.Single,System.Single,System.Single,System.Single)) 個別 `x` 和 `y` 座標：

```csharp
public void ConicTo (SKPoint point1, SKPoint point2, Single weight)

public void ConicTo (Single x1, Single y1, Single x2, Single y2, Single weight)
```

請注意最後一個 `weight` 參數。

[ **圓錐曲線** ] 頁面可讓您試驗這些曲線。 `ConicCurvePage` 類別衍生自 `InteractivePage`。 [**ConicCurvePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml)可具現化， `Slider` 以選取介於-2 和2之間的加權值。 [**ConicCurvePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCurvePage.xaml.cs)程式碼後端檔案會建立三個 `TouchPoint` 物件，而 `PaintSurface` 處理常式只會將具有正切線條的結果曲線轉譯成控制點：

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

如您所見，當權數較高時，控制點似乎會將曲線拉回更多。 當權數為零時，曲線就會變成從起點到結束點的直線。

理論上，允許負加權，並使曲線 *遠離* 控制點。 不過，[-1] 或 [低於] 的權數會導致參數方程式中的分母針對 *t* 的特定值變成負值。 可能基於這個原因，方法中會忽略負加權 `ConicTo` 。 「 **圓錐曲線** 」程式可讓您設定負加權，但您可以藉由實驗來查看，負值加權的效果與零的粗細相同，而且會造成直線的呈現。

您很容易就能衍生控制點和權數，以使用 `ConicTo` 方法繪製圓形弧形到 (，但不包括) 的半圓。 在下圖中，起點和終點的正切線條符合控制點。

![圓弧的圓錐弧線轉譯](beziers-images/conicarc.png)

您可以使用三角函數來判斷控制點與圓形中心之間的距離：它是圓形的半徑除以角度α的半余弦。 若要在起點和終點之間繪製圓弧，請將權數設定為該角度一半的相同余弦。 請注意，如果角度為180度，則正切線條永遠不會符合，且權數為零。 但是針對小於180度的角度，數學運算會正常運作。

[ **圓錐圓弧** ] 頁面會示範這一點。 [**ConicCircularArc**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml)可具現化， `Slider` 以選取角度。 `PaintSurface` [**ConicCircularArc.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConicCircularArcPage.xaml.cs)程式碼後端檔案中的處理常式會計算控制點和權數：

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

如您所見，紅色顯示的 `ConicTo` 路徑與顯示為參考的基礎圓圈之間沒有任何視覺差異：

[![圓錐圓弧頁面的三重螢幕擷取畫面](beziers-images/coniccirculararc-small.png)](beziers-images/coniccirculararc-large.png#lightbox)

但是，將角度設為180度，而數學會失敗。

在這種情況下，這種情況並不 `ConicTo` 支援負值加權，因為理論上 (根據參數化方程式) ，因此可以使用另一個對相同的點呼叫， `ConicTo` 但加權值為負值來完成圓圈。 如此一來，就可以 `ConicTo` 根據 (之間的任何角度（但不包括) 零度和180度）來建立具有兩個曲線的整個圓圈。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)