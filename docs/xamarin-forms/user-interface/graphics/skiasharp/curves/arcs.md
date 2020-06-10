---
標題：「繪製弧線的三種方式」描述：「本文將說明如何使用 SkiaSharp 以三種不同的方式來定義弧線，並以範例程式碼示範此動作。」
skiasharp 的 assetid： F1DA55E4-0182-4388-863C-5C340213BF3C author： davidbritch ms-chap： dabritch ms. date： 05/10/2017 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="three-ways-to-draw-an-arc"></a>繪製弧形的三種方式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用 SkiaSharp 以三種不同的方式定義弧形_

弧線是橢圓形圓周上的曲線，例如此無限大符號的圓角部分：

![無限大符號](arcs-images/arcsample.png)

雖然該定義的簡單性，卻無法定義可滿足每個需求的弧線繪製函式，因此在繪製弧線的最佳方式中，圖形系統之間並無共識。基於這個理由， `SKPath` 類別並不會將自己限制為只有一種方法。

`SKPath`定義 [`AddArc`](xref:SkiaSharp.SKPath.AddArc*) 方法、五個不同 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) 的方法，以及兩個相對的 [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*) 方法。 這些方法可分為三個類別，代表用來指定弧線的三種不同方法。您使用哪一個取決於可用來定義弧線的資訊，以及此弧線如何配合您所繪製的其他圖形。

## <a name="the-angle-arc"></a>角度弧線

繪製弧形的角度弧線方法會要求您指定一個矩形來限定橢圓形。 這個橢圓形圓周上的弧線是以橢圓形中心的角度來表示，而這點會指出弧線開頭和長度。 兩種不同的方法繪製角度弧形。 這些是 [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) 方法和 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean)) 方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

這些方法與 Android [`AddArc`](xref:Android.Graphics.Path.AddArc*) 和 [ `ArcTo` ] x： ArcTo *）方法相同。 IOS [`AddArc`](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean)) 方法很類似，但僅限於圓形圓周上的弧形，而不是一般化成橢圓形。

這兩種方法都是以 `SKRect` 定義橢圓形位置和大小的值為開頭：

![開始角度弧線的橢圓形](arcs-images/anglearcoval.png)

弧線是這個橢圓形圓周的一部分。

`startAngle`引數是以度為單位的順時針角度，相對於從橢圓形的中央向右繪製的水平線。 `sweepAngle`引數是相對於 `startAngle` 。 以下是 `startAngle` `sweepAngle` 分別為60度和100度的和值：

![定義角度弧線的角度](arcs-images/anglearcangles.png)

弧線從開始角度開始。 其長度是由掃描角度所控制。 這是以紅色顯示的弧線：

![反白顯示的角度弧線](arcs-images/anglearchighlight.png)

使用或方法加入至路徑的曲線 `AddArc` `ArcTo` 只是橢圓形圓周的一部分：

![角度弧本身](arcs-images/anglearc.png)

`startAngle`或 `sweepAngle` 引數可以是負數：的正值會順時針旋轉 `sweepAngle` ，而負數值則會順時針地逆時針。

不過， `AddArc` 不*not*會定義封閉的輪廓。 如果您在 `LineTo` 之後呼叫 `AddArc` ，則會從弧形尾端到方法中的點繪製線條 `LineTo` ，而這也適用于 `ArcTo` 。

`AddArc`會自動啟動新的輪廓，而且在功能上相當於 `ArcTo` 使用的最終引數呼叫 `true` ：

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

系統會呼叫最後一個引數 `forceMoveTo` ，並有效地導致在 `MoveTo` 弧線開頭進行呼叫。這會開始新的輪廓。 但最後一個引數的情況並非如此 `false` ：

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

這個版本的會將 `ArcTo` 從目前位置到弧線開頭的線條繪製一條線。這表示弧線可以在較大的輪廓中間的某個位置。

[**角度弧線**] 頁面可讓您使用兩個滑杆來指定開始和掃描角度。 XAML 檔案會具現化兩個 `Slider` 元素和 `SKCanvasView` 。 `PaintCanvas` [**AngleArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)檔案中的處理常式會使用 `SKPaint` 定義為欄位的兩個物件，繪製橢圓形和弧線：

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

如您所見，開始角度和掃描角度可能會採用負值：

[![[角度弧線] 頁面的三重螢幕擷取畫面](arcs-images/anglearc-small.png)](arcs-images/anglearc-large.png#lightbox)

這種產生弧線的方法以演算法方式最簡單，而且可以輕鬆地衍生出描述弧線的參數方程式。知道橢圓形的大小和位置，以及開始和清除角度，可以使用簡單的三角函數來計算弧線的開始和結束點：

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

`angle`值為 `startAngle` 或 `startAngle + sweepAngle` 。

使用兩個角度來定義弧線，最適合您知道要繪製之弧線的角度長度的情況，例如，製作圓形圖。 [**分裂式圓形圖**] 頁面會示範這種情況。 [`ExplodedPieChartPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)類別會使用內部類別來定義一些製造的資料和色彩：

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

`PaintSurface`處理常式會先迴圈執行專案來計算 `totalValues` 數位。 如此一來，它就可以將每個專案的大小判斷為總計的分數，並將其轉換成角度：

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

`SKPath`針對每個圓形圖配量會建立新的物件。 路徑包含來自中央的線條，然後是用 `ArcTo` 來繪製弧線的，另一條則是從呼叫中傳回的中心結果 `Close` 。 此程式會顯示「分解」的圓形圖，其方式是將其從中央向外移動50圖元。 該工作需要向量以每個配量的掃描角度的中點的方向：

[![分裂式圓形圖頁面的三重螢幕擷取畫面](arcs-images/explodedpiechart-small.png)](arcs-images/explodedpiechart-large.png#lightbox)

若要在沒有「激增」的情況下查看它看起來的樣子，只要將 `Translate` 呼叫批註：

[![不含激增的分裂式圓形圖頁面的三向螢幕擷取畫面](arcs-images/explodedpiechartunexploded-small.png)](arcs-images/explodedpiechartunexploded-large.png#lightbox)

## <a name="the-tangent-arc"></a>反正切圓弧

所支援的第二種類型 `SKPath` 是*正切弧線*，因此會呼叫，因為弧線是圓的圓周，會與兩條連接的線條相切。

在具有 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single)) 兩個參數的方法呼叫 `SKPoint` 或 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single)) 具有不同點之個別參數的多載的情況下，會將正切弧線加入至路徑 `Single` ：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

這個 `ArcTo` 方法類似 PostScript [`arct`](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) （page 532）函數和 iOS [`AddArcToPoint`](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat)) 方法。

此 `ArcTo` 方法牽涉到三個點：

- 輪廓的目前點，如果尚未呼叫，則為點（0，0） `MoveTo`
- 方法的第一個點引數 `ArcTo` ，稱為*角落點*
- 的第二個點引數 `ArcTo` ，稱為*目的地點*：

![開始正切弧線的三個點](arcs-images/tangentarcthreepoints.png)

這三個點會定義兩條連接的線條：

![連接正切弧線之三個點的線條](arcs-images/tangentarcconnectinglines.png)

如果 colinear 的三個點 &mdash; 是，如果它們位於同一條直線上，則 &mdash; 不會繪製弧線。

此 `ArcTo` 方法也包含 `radius` 參數。 這會定義圓形的半徑：

![反正切圓弧的圓形](arcs-images/tangentarccircle.png)

反正切圓弧不會針對橢圓形進行一般化。

如果這兩條線符合任何角度，則可以在這些線條之間插入圓形，使其與這兩行相切：

![兩條線之間的正切弧線圓](arcs-images/tangentarctangentcircle.png)

加入至輪廓的曲線不會觸及方法中所指定的任何點 `ArcTo` 。 其中包含從目前點到第一個正切點的直線，以及在第二個正切點結束的弧線，以紅色顯示：

![兩行之間反白顯示的反正切弧線](arcs-images/tangentarchighlight.png)

以下是新增至輪廓的最後一條直線和弧線：

![兩行之間反白顯示的反正切弧線](arcs-images/tangentarc.png)

等高線可以從第二個正切點繼續。

[**正切弧線**] 頁面可讓您試驗反正切的弧線。這是衍生自的幾個頁面中的第一個 [`InteractivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs) ，它會定義一些方便的 `SKPaint` 物件並執行 `TouchPoint` 處理：

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

`TangentArcPage` 類別衍生自 `InteractivePage`。 [**TangentArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)檔中的函式會負責具現化及初始化 `touchPoints` 陣列，並將 `baseCanvasView` （在中 `InteractivePage` ）設定為 TangentArcPage 檔案中具現化的 `SKCanvasView` 物件： [**TangentArcPage.xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)

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

`PaintSurface`處理常式會使用 `ArcTo` 方法，根據觸控點和來繪製弧線 `Slider` ，但以演算法方式也會計算角度所依據的圓形：

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

以下是正在執行的**正切弧線**頁面：

[![正切弧線頁面的三重螢幕擷取畫面](arcs-images/tangentarc-small.png)](arcs-images/tangentarc-large.png#lightbox)

正切曲線非常適合用來建立圓角，例如圓角矩形。 因為 `SKPath` 已包含 `AddRoundedRect` 方法，所以 [**圓角 Heptagon** ] 頁面會示範如何使用來 `ArcTo` 進位七邊多邊形的圓角。 （程式碼會針對任何一般多邊形進行一般化）。

此 `PaintSurface` 類別的處理常式 [`RoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs) 包含一個 `for` 迴圈來計算 heptagon 的七個頂點座標，而第二個則用來計算這些頂點七邊的中點。 接著會使用這些中點來建立路徑：

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

[![[圓角 Heptagon] 頁面的三重螢幕擷取畫面](arcs-images/roundedheptagon-small.png)](arcs-images/roundedheptagon-large.png#lightbox)

## <a name="the-elliptical-arc"></a>橢圓形弧線

在具有 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint)) 兩個參數的方法呼叫 `SKPoint` 或 [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single)) 具有不同 X 和 Y 座標的多載的情況下，會將橢圓形弧線加入至路徑：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

橢圓形弧線與可延展向量圖形（SVG）和通用 Windows 平臺類別中包含的[橢圓形弧線](https://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands)一致 [`ArcSegment`](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/) 。

這些 `ArcTo` 方法會在兩個點之間繪製一個圓弧，也就是輪廓的目前點，以及方法的最後一個參數 `ArcTo` （ `xy` 參數或個別的 `x` 和 `y` 參數）：

![定義橢圓形弧線的兩個點](arcs-images/ellipticalarcpoints.png)

方法的第一個點參數 `ArcTo` （ `r` 、或 `rx` 和 `ry` ）不是全部的點，而是指定橢圓形的水準和垂直半徑;

![定義橢圓形弧線的橢圓形](arcs-images/ellipticalarcellipse.png)

`xAxisRotate`參數是要旋轉此橢圓形的順時針度數數目：

![定義橢圓形弧線的傾斜橢圓形](arcs-images/ellipticalarctiltedellipse.png)

如果這個傾斜橢圓形接著定位，使其觸及兩個點，則點會以兩個不同的弧線連接：

![第一組橢圓形弧形](arcs-images/ellipticalarcellipse1.png)

這兩個弧線可以透過兩種方式來區別：上弧線大於下弧線，而當弧線由左至右繪製時，會以順時針方向繪製上弧形，而下弧線則以逆時針方向繪製。

您也可以用另一種方式來配合兩個點之間的橢圓形：

![第二組橢圓形弧形](arcs-images/ellipticalarcellipse2.png)

在上方會有較小的弧線，以順時針方向繪製，而底部繪製的弧線較大。

因此，這兩個點可以透過由傾斜橢圓形定義的弧線來連接，共有四種方式：

![全部四個橢圓形弧](arcs-images/ellipticalarccolors.png)

這四個弧形會以方法的四個 [`SKPathArcSize`](xref:SkiaSharp.SKPathArcSize) 和 [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) 列舉型別引數組合來區別 `ArcTo` ：

- red： SKPathArcSize. 大型和 SKPathDirection。順時針
- 綠色： SKPathArcSize. Small 和 SKPathDirection。順時針
- blue： SKPathArcSize. Small 和 SKPathDirection。逆時針
- 洋紅： SKPathArcSize. 大型和 SKPathDirection。逆時針

如果傾斜橢圓形的大小不足以容納兩個點，則它會經過一致調整，直到夠大。 在這種情況下，只有兩個唯一的弧線會連接這兩個點。 這些可以與 `SKPathDirection` 參數區別。

雖然這種定義 arc 的方法在第一次遇到時變得很複雜，但它是唯一允許使用旋轉的橢圓形來定義弧線的方法，而且當您需要將弧線與輪廓的其他部分整合時，這通常是最簡單的方法。

[**橢圓形弧線**] 頁面可讓您以互動方式設定兩個點，以及橢圓形的大小和旋轉。 `EllipticalArcPage`類別衍生自 `InteractivePage` ，而 `PaintSurface` [**EllipticalArcPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)程式碼後置檔案中的處理常式繪製四個弧形：

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

**Arc 無限大**頁面會使用橢圓形弧線來繪製無限大符號。 無限大符號是以兩個圓圈為基礎，而半徑為100單位，並以100單位分隔：

![兩個圓形](arcs-images/infinitycircles.png)

彼此相交的兩條線會與兩個圓形相切：

![具有正切線條的兩個圓形](arcs-images/infinitycircleslines.png)

無限大符號是這些圓形的部分和兩行的組合。 若要使用橢圓形弧線繪製無限大符號，必須判斷兩條線與圓形之間的座標。

在其中一個圓形中，建立一個直角矩形：

![具有正切線條和內嵌圓形的兩個圓形](arcs-images/infinitytriangle.png)

圓形的半徑是100單位，而三角形的斜邊是150單位，因此角度α是100除以150或41.8 度的反正弦（反正弦）。 三角形另一端的長度是41.8 度或112的余弦值150倍，也可以由畢氏定理計算。

接著，您可以使用下列資訊來計算正切函數的座標：

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

這四個正切點都是繪製以點（0，0）為中心的無限號，而圓半徑為100的必要動作：

![具有正切線條和座標的兩個圓形](arcs-images/infinitycoordinates.png)

`PaintSurface`類別中的處理常式 [`ArcInfinityPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs) 會放置無限大符號，讓（0，0）點位於頁面的中央，並將路徑調整為螢幕大小：

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

程式碼會使用的 `Bounds` 屬性 `SKPath` 來判斷無限大正弦的維度，以將其調整為畫布的大小：

[![[Arc 無限大] 頁面的三重螢幕擷取畫面](arcs-images/arcinfinity-small.png)](arcs-images/arcinfinity-large.png#lightbox)

結果看起來很小，這表示的 `Bounds` 屬性 `SKPath` 會報告大於路徑的大小。

就內部而言，Skia 會接近使用多個二次方貝茲曲線的弧線。 這些曲線（如您在下一節中所見）包含控制點，可控制繪製曲線的方式，但不屬於呈現曲線的一部分。 `Bounds`屬性包含這些控制點。

若要取得更緊密的大小，請使用 `TightBounds` 屬性，這會排除控制點。 以下是在橫向模式中執行的程式，並使用 `TightBounds` 屬性來取得路徑界限：

[![具有緊密界限之 Arc 無限大頁面的三向螢幕擷取畫面](arcs-images/arcinfinitytightbounds-small.png)](arcs-images/arcinfinitytightbounds-large.png#lightbox)

雖然弧形和直線之間的連接會以數學方式平滑，但從弧線到直線的變更似乎有點突然。 在下一篇文章中，有[**三種類型的貝茲曲線**](beziers.md)會提供更好的無限大正負號。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
