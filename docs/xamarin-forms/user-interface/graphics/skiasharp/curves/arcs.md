---
title: 繪製弧形的三種方式
description: 本文說明如何使用 SkiaSharp 以三種不同的方式來定義弧線，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5f48300b6c974bbbb0106f1afaa6c863f8159c58
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374634"
---
# <a name="three-ways-to-draw-an-arc"></a>繪製弧形的三種方式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用 SkiaSharp 以三種不同的方式定義弧形_

弧形是橢圓形圓周上的曲線，例如這個無限大符號的圓角部分：

![無限大符號](arcs-images/arcsample.png)

儘管該定義的簡易性很簡單，但無法定義能滿足每項需求的弧形繪圖函式，因此在繪製弧線的最佳方式之間，不會有共識。基於這個理由， `SKPath` 類別不會將自己限制為只有一個方法。

`SKPath` 定義 [`AddArc`](xref:SkiaSharp.SKPath.AddArc*) 方法、五個不同 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) 的方法，以及兩個相對的 [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*) 方法。 這些方法分為三個類別，代表三種非常不同的方法來指定弧線。您要使用哪一個取決於可用來定義弧線的資訊，以及此弧線如何與您正在繪製的其他圖形搭配使用。

## <a name="the-angle-arc"></a>角弧線

繪製弧形的角度弧線方法需要您指定一個矩形來包圍橢圓形。 這個橢圓形圓周上的弧線是以表示弧線開頭和其長度的橢圓中心角度來表示。 有兩種不同的方法繪製角度弧形。 這些是 [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) 方法和 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) 方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

這些方法與 Android [`AddArc`](xref:Android.Graphics.Path.AddArc*) 和 [ `ArcTo` ] x： ArcTo * ) 方法相同。 IOS [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) 方法很類似，但受限於圓形圓周上的弧形，而不是一般化至橢圓形。

這兩種方法都是以 `SKRect` 定義橢圓形位置和大小的值為開頭：

![開始角度弧線的橢圓](arcs-images/anglearcoval.png)

弧線是此橢圓形圓周的一部分。

`startAngle`引數是以度為單位的順時針角度，以度為單位，相對於從橢圓形的中央繪製到右邊的水平線條。 `sweepAngle`引數是相對於 `startAngle` 。 以下是 `startAngle` `sweepAngle` 60 度和100度的值，分別為：

![定義角度弧線的角度](arcs-images/anglearcangles.png)

弧線開始于開始角度。 它的長度是由整理角度所控管。 弧線以紅色顯示：

![反白顯示的角度弧線](arcs-images/anglearchighlight.png)

使用或方法新增至路徑的曲線 `AddArc` `ArcTo` 只是橢圓形圓周的一部分：

![角度弧線本身](arcs-images/anglearc.png)

`startAngle`或 `sweepAngle` 引數可以是負數：弧度的正值是順時針的 `sweepAngle` ，而針對負值則是逆時針的。

但是， `AddArc` 不 *not* 會定義封閉的輪廓。 如果您在 `LineTo` 之後呼叫 `AddArc` ，就會從弧線的結尾處繪製一行到方法中的點 `LineTo` ，而相同的也是 true `ArcTo` 。

`AddArc` 自動啟動新的輪廓，而且在功能上相當於使用的最後一個引數進行的呼叫 `ArcTo` `true` ：

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

接著會呼叫最後一個引數 `forceMoveTo` ，並在 arc 的開頭有效地產生 `MoveTo` 呼叫。這會開始新的輪廓。 這不是最後一個引數的情況 `false` ：

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

此版本的會 `ArcTo` 從目前位置到弧形開頭繪製一條線。這表示弧線可以位於較大輪廓的中間位置。

[ **角度弧線** ] 頁面可讓您使用兩個滑杆來指定開始和清除角度。 XAML 檔案會具現化兩個 `Slider` 元素和 `SKCanvasView` 。 `PaintCanvas` [**AngleArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)檔案中的處理常式會使用 `SKPaint` 定義為欄位的兩個物件來繪製 oval 和弧形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
    float startAngle = (float)startAngleSlider.Value;
    float sweepAngle = (float)sweepAngleSlider.Value;

    canvas.DrawOval(rect, outlinePaint);

    using (SKPath path = new SKPath())
    {
        path.AddArc(rect, startAngle, sweepAngle);
        canvas.DrawPath(path, arcPaint);
    }
}
```

如您所見，開始角度和清除角度都可採用負數值：

[![角度弧線頁面的三重螢幕擷取畫面](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

這種產生弧線的方法演算法最簡單的方式，而且可以輕鬆地衍生出描述弧線的參數方程式。瞭解橢圓形的大小和位置，以及開始和清除角度，可以使用簡單三角函數來計算弧線的開始和結束點：

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

`angle`值為 `startAngle` 或 `startAngle + sweepAngle` 。

如果您知道想要繪製之弧線的角度長度（例如，繪製圓形圖），就最好使用兩個角度來定義弧線。 [ **分裂式圓形圖** ] 頁面會示範這一點。 [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)類別會使用內部類別來定義一些製造的資料和色彩：

```csharp
class ChartData
{
    public ChartData(int value, SKColor color)
    {
        Value = value;
        Color = color;
    }

    public int Value { private set; get; }

    public SKColor Color { private set; get; }
}

ChartData[] chartData =
{
    new ChartData(45, SKColors.Red),
    new ChartData(13, SKColors.Green),
    new ChartData(27, SKColors.Blue),
    new ChartData(19, SKColors.Magenta),
    new ChartData(40, SKColors.Cyan),
    new ChartData(22, SKColors.Brown),
    new ChartData(29, SKColors.Gray)
};

```

`PaintSurface`處理常式會先迴圈處理專案，以計算 `totalValues` 數位。 如此一來，它就可以將每個專案的大小判斷為總計的分數，並將其轉換成角度：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int totalValues = 0;

    foreach (ChartData item in chartData)
    {
        totalValues += item.Value;
    }

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float explodeOffset = 50;
    float radius = Math.Min(info.Width / 2, info.Height / 2) - 2 * explodeOffset;
    SKRect rect = new SKRect(center.X - radius, center.Y - radius,
                             center.X + radius, center.Y + radius);

    float startAngle = 0;

    foreach (ChartData item in chartData)
    {
        float sweepAngle = 360f * item.Value / totalValues;

        using (SKPath path = new SKPath())
        using (SKPaint fillPaint = new SKPaint())
        using (SKPaint outlinePaint = new SKPaint())
        {
            path.MoveTo(center);
            path.ArcTo(rect, startAngle, sweepAngle, false);
            path.Close();

            fillPaint.Style = SKPaintStyle.Fill;
            fillPaint.Color = item.Color;

            outlinePaint.Style = SKPaintStyle.Stroke;
            outlinePaint.StrokeWidth = 5;
            outlinePaint.Color = SKColors.Black;

            // Calculate "explode" transform
            float angle = startAngle + 0.5f * sweepAngle;
            float x = explodeOffset * (float)Math.Cos(Math.PI * angle / 180);
            float y = explodeOffset * (float)Math.Sin(Math.PI * angle / 180);

            canvas.Save();
            canvas.Translate(x, y);

            // Fill and stroke the path
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, outlinePaint);
            canvas.Restore();
        }

        startAngle += sweepAngle;
    }
}
```

為 `SKPath` 每個圓形圖配量建立新的物件。 路徑包含從中央開始的線條， `ArcTo` 以及用來繪製弧線的另一行，另一行則是來自呼叫的中間結果 `Close` 。 此程式會顯示「分裂式」的圓形配量，方法是將它們從中央移出50圖元。 該工作需要每個配量的每個配量之中間點方向的向量：

[![[分裂式圓形圖] 頁面的三重螢幕擷取畫面](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

若要在沒有「爆炸」的情況下查看其外觀，只要將 `Translate` 呼叫批註：

[![不含爆炸之分裂式圓形圖頁面的三重螢幕擷取畫面](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>正切曲線

所支援的第二種弧線類型 `SKPath` 是 *正切* 函數，因此會呼叫，因為弧形是與兩個連接線相切的圓形圓周。

您可以使用  [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) 兩個參數來呼叫方法 `SKPoint` ，或使用 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) 不同的 `Single` 點參數多載，將正切函數新增至路徑：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

此 `ArcTo` 方法類似 PostScript [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) (頁面 532) 函式和 iOS [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) 方法。

此 `ArcTo` 方法牽涉到三個點：

- 如果尚未呼叫，則輪廓的目前點或點 (0，0) `MoveTo`
- 方法的第一個點引數 `ArcTo` ，稱為 *邊角點*
- 的第二個點引數 `ArcTo` ，稱為「 *目的地點* 」：

![開始反正切弧線的三個點](arcs-images/tangentarcthreepoints.png)

這三個點會定義兩個連接線：

![連接反正切弧線三個點的線條](arcs-images/tangentarcconnectinglines.png)

如果這三個點是 colinear 的，也就是說 &mdash; ，如果它們位於相同的直線上，則 &mdash; 不會繪製任何弧線。

`ArcTo`方法也包含 `radius` 參數。 這會定義圓形的半徑：

![正切曲線的圓形](arcs-images/tangentarccircle.png)

省略號弧線未一般化。

如果這兩條線在任何角度都符合，則可以在這些線條之間插入圓形，讓它能夠與這兩行相切：

![兩行之間的正切圓弧圓形](arcs-images/tangentarctangentcircle.png)

新增到輪廓的曲線不會觸及方法中指定的任何點 `ArcTo` 。 它包含從目前點到第一個正切函數的直線，以及結束于第二個正切點（以紅色顯示）的弧線：

![兩行之間反白顯示的正切曲線](arcs-images/tangentarchighlight.png)

以下是新增到輪廓的最後直線和弧線：

![兩行之間反白顯示的正切曲線](arcs-images/tangentarc.png)

等高線可以從第二個正切點繼續。

[ **相切弧線** ] 頁面可讓您試驗反正切值。這是數個衍生自的頁面中的第一個 [`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs) ，它會定義一些方便的 `SKPaint` 物件並執行 `TouchPoint` 處理：

```csharp
public class InteractivePage : ContentPage
{
    protected SKCanvasView baseCanvasView;
    protected TouchPoint[] touchPoints;

    protected SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3
    };

    protected SKPaint redStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 15
    };

    protected SKPaint dottedStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    };

    protected void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = baseCanvasView.CanvasSize.Width / (float)baseCanvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            baseCanvasView.InvalidateSurface();
        }
    }
}
```

`TangentArcPage` 類別衍生自 `InteractivePage`。 [**TangentArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)檔中的函式負責具現化和初始化 `touchPoints` 陣列，並將 `baseCanvasView`) 中的 (設定 `InteractivePage` 為 `SKCanvasView` [**TangentArcPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)檔案中具現化的物件：

```csharp
public partial class TangentArcPage : InteractivePage
{
    public TangentArcPage()
    {
        touchPoints = new TouchPoint[3];

        for (int i = 0; i < 3; i++)
        {
            TouchPoint touchPoint = new TouchPoint
            {
                Center = new SKPoint(i == 0 ? 100 : 500,
                                     i != 2 ? 100 : 500)
            };
            touchPoints[i] = touchPoint;
        }

        InitializeComponent();

        baseCanvasView = canvasView;
        radiusSlider.Value = 100;
    }

    void sliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

`PaintSurface`處理常式會使用 `ArcTo` 方法根據觸控點和來繪製弧線 `Slider` ，但也會演算法計算角度所依據的圓形：

```csharp
public partial class TangentArcPage : InteractivePage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw the two lines that meet at an angle
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.LineTo(touchPoints[1].Center);
            path.LineTo(touchPoints[2].Center);
            canvas.DrawPath(path, dottedStrokePaint);
        }

        // Draw the circle that the arc wraps around
        float radius = (float)radiusSlider.Value;

        SKPoint v1 = Normalize(touchPoints[0].Center - touchPoints[1].Center);
        SKPoint v2 = Normalize(touchPoints[2].Center - touchPoints[1].Center);

        double dotProduct = v1.X * v2.X + v1.Y * v2.Y;
        double angleBetween = Math.Acos(dotProduct);
        float hypotenuse = radius / (float)Math.Sin(angleBetween / 2);
        SKPoint vMid = Normalize(new SKPoint((v1.X + v2.X) / 2, (v1.Y + v2.Y) / 2));
        SKPoint center = new SKPoint(touchPoints[1].Center.X + vMid.X * hypotenuse,
                                     touchPoints[1].Center.Y + vMid.Y * hypotenuse);

        canvas.DrawCircle(center.X, center.Y, radius, this.strokePaint);

        // Draw the tangent arc
        using (SKPath path = new SKPath())
        {
            path.MoveTo(touchPoints[0].Center);
            path.ArcTo(touchPoints[1].Center, touchPoints[2].Center, radius);
            canvas.DrawPath(path, redStrokePaint);
        }

        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
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
}
```

以下是執行的 **正切曲線** 頁面：

[![正切曲線頁面的三重螢幕擷取畫面](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

正切曲線適合用來建立圓角，例如圓角矩形。 因為 `SKPath` 已包含 `AddRoundedRect` 方法，所以 **圓角 Heptagon** 頁面會示範如何使用 `ArcTo` 來進位出七邊多邊形的角落。  (程式碼會針對任何一般多邊形進行一般化。 ) 

`PaintSurface`類別的處理常式 [`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) 包含一個 `for` 迴圈來計算 heptagon 七個頂點的座標，另一個則是從這些頂點計算七邊的中點。 接著會使用這些等點來建立路徑：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float cornerRadius = 100;
    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPoint[] vertices = new SKPoint[numVertices];
    SKPoint[] midPoints = new SKPoint[numVertices];

    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    // Coordinates of the midpoints of the sides connecting the vertices
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        int prevVertex = (vertex + numVertices - 1) % numVertices;
        midPoints[vertex] = new SKPoint((vertices[prevVertex].X + vertices[vertex].X) / 2,
                                        (vertices[prevVertex].Y + vertices[vertex].Y) / 2);
    }

    // Create the path
    using (SKPath path = new SKPath())
    {
        // Begin at the first midpoint
        path.MoveTo(midPoints[0]);

        for (int vertex = 0; vertex < numVertices; vertex++)
        {
            SKPoint nextMidPoint = midPoints[(vertex + 1) % numVertices];

            // Draws a line from the current point, and then the arc
            path.ArcTo(vertices[vertex], nextMidPoint, cornerRadius);

            // Connect the arc with the next midpoint
            path.LineTo(nextMidPoint);
        }
        path.Close();

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);
        }
    }
}

```

以下是程式執行情況：

[![舍入 Heptagon 頁面的三重螢幕擷取畫面](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>橢圓形弧線

使用 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) 具有兩個參數之方法的呼叫 `SKPoint` ，或  [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) 具有個別 X 和 Y 座標的多載，就會將橢圓形弧線新增至路徑：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

橢圓弧線與可擴充向量圖形中包含的 [橢圓弧線](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands) 一致 (SVG) 和通用 Windows 平臺 [`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) 類別。

這些 `ArcTo` 方法會在兩個點之間繪製一個弧線，也就是目前的輪廓點，而方法的最後一個參數 `ArcTo` (`xy` 參數或個別的 `x` 和 `y` 參數) ：

![定義橢圓形弧線的兩個點](arcs-images/ellipticalarcpoints.png)

方法 (的第一個點參數 `ArcTo` `r` ，或) 不是 `rx` `ry` 任何點，但改為指定橢圓形的水準和垂直半徑;

![定義橢圓形弧線的橢圓形](arcs-images/ellipticalarcellipse.png)

`xAxisRotate`參數是要旋轉這個橢圓形的順時針度數數目：

![定義橢圓形弧線的傾斜橢圓形](arcs-images/ellipticalarctiltedellipse.png)

如果這個傾斜橢圓形接著定位，使其觸及兩個點，則點會由兩個不同的弧形連接：

![第一組橢圓形弧線](arcs-images/ellipticalarcellipse1.png)

這兩個弧形可以用兩種方式來區分：頂端弧線大於下弧形，而且當弧線從左至右繪製時，會以順向方向繪製最上方的弧線，而下弧線則是以逆時針方向繪製。

您也可以在兩個點之間以另一種方式容納橢圓形：

![第二組橢圓形弧線](arcs-images/ellipticalarcellipse2.png)

現在在上方有一個較小的弧形，以順時針方向繪製，而底部的弧線較大，則會以逆時針方向繪製。

因此，這兩個點可以藉由傾斜橢圓形定義的弧線來連接，總共四個方法：

![四個橢圓形弧線](arcs-images/ellipticalarccolors.png)

這四個弧形是以方法的四個 [`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize) 和 [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) 列舉型別引數組合來區分 `ArcTo` ：

- red： SKPathArcSize. 大型和 SKPathDirection
- 綠色： SKPathArcSize Small 和 SKPathDirection
- blue： SKPathArcSize、Small 和 SKPathDirection. 逆時針
- 洋紅： SKPathArcSize. 大型和 SKPathDirection. 逆時針

如果傾斜橢圓形不夠大，無法容納在兩個點之間，則會進行一致調整，直到夠大。 在這種情況下，只有兩個唯一的弧形會連接兩個點。 這些可以使用參數來區分 `SKPathDirection` 。

雖然這種定義弧線音效的方法是在第一次遇到複雜的情況，但它是唯一允許以旋轉橢圓形定義弧線的方法，而且當您需要將弧線與等高線的其他部分整合時，這通常是最簡單的方法。

**橢圓形弧線** 頁面可讓您以互動方式設定這兩個點，以及橢圓形的大小和旋轉。 `EllipticalArcPage`類別衍生自 `InteractivePage` ，而 `PaintSurface` [**EllipticalArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)程式碼後端檔案中的處理常式會繪製四個弧線：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        int colorIndex = 0;
        SKPoint ellipseSize = new SKPoint((float)xRadiusSlider.Value,
                                          (float)yRadiusSlider.Value);
        float rotation = (float)rotationSlider.Value;

        foreach (SKPathArcSize arcSize in Enum.GetValues(typeof(SKPathArcSize)))
            foreach (SKPathDirection direction in Enum.GetValues(typeof(SKPathDirection)))
            {
                path.MoveTo(touchPoints[0].Center);
                path.ArcTo(ellipseSize, rotation,
                           arcSize, direction,
                           touchPoints[1].Center);

                strokePaint.Color = colors[colorIndex++];
                canvas.DrawPath(path, strokePaint);
                path.Reset();
            }
    }

    foreach (TouchPoint touchPoint in touchPoints)
    {
        touchPoint.Paint(canvas);
    }
}

```

它正在執行：

[![橢圓形弧線頁面的三重螢幕擷取畫面](arcs-images/ellipticalarc-small.png)](arcs-images/ellipticalarc-large.png#lightbox)

「 **弧線無限大** 」頁面使用橢圓弧線來繪製無限大符號。 無限大符號是以兩個圓圈為基礎，半徑為100單位，以100單位分隔：

![兩個圓圈](arcs-images/infinitycircles.png)

兩條彼此相交的線條會與兩個圓形相切：

![具有正切線條的兩個圓圈](arcs-images/infinitycircleslines.png)

無限大符號是這些圓形的部分和兩行的組合。 若要使用橢圓形弧線來繪製無限大符號，必須判斷兩行要與圓形相切的座標。

在其中一個圓形中建立右矩形：

![具有正切線條和內嵌圓形的兩個圓圈](arcs-images/infinitytriangle.png)

圓形的半徑是100個單位，三角形的斜邊是150個單位，因此角度α是反正弦 (反正弦) 100 除以150或41.8 度。 三角形另一端的長度是150乘以41.8 度的余弦值或112，也可以透過畢氏定理來計算。

然後可以使用下列資訊來計算正切函數的座標：

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

您必須使用四個正切函數來繪製以點為中心的無限大符號， (0，0) ，圓形半徑為100：

![具有正切線條和座標的兩個圓圈](arcs-images/infinitycoordinates.png)

`PaintSurface`類別中的處理常式 [`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) 會放置無限大符號，讓 (0、0) 點位於頁面中央，並將路徑調整為螢幕大小：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPath path = new SKPath())
    {
        path.LineTo(83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.CounterClockwise, 83, -75);
        path.LineTo(-83, 75);
        path.ArcTo(100, 100, 0, SKPathArcSize.Large, SKPathDirection.Clockwise, -83, -75);
        path.Close();

        // Use path.TightBounds for coordinates without control points
        SKRect pathBounds = path.Bounds;

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / pathBounds.Width,
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

程式碼會使用的 `Bounds` 屬性 `SKPath` 來判斷無限正弦值的維度，以將其調整為畫布大小：

[![[弧線無限大] 頁面的三重螢幕擷取畫面](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

結果看起來有點小，這表示的 `Bounds` 屬性 `SKPath` 報告的大小大於路徑。

就內部而言，Skia 會使用多個二次方貝茲曲線來接近弧線。 您將在下一節中看到這些曲線 (，) 包含控管如何繪製曲線，但不屬於所呈現曲線的控制點。 `Bounds`屬性包含這些控制點。

若要取得更緊密的大小，請使用 `TightBounds` 屬性，以排除控制點。 以下是在橫向模式中執行的程式，並使用 `TightBounds` 屬性來取得路徑界限：

[![具有緊密範圍之 Arc 無限大頁面的三重螢幕擷取畫面](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

雖然弧形和直線之間的連接在數學上是平滑的，但從弧線到直線的變更可能有點突然。 [**下列三種貝茲曲線類型**](beziers.md)的下一篇文章會提供更好的無限大符號。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)