---
title: 繪製弧形的三種方式
description: 這篇文章說明如何使用 SkiaSharp 以三個不同的方式，定義弧線，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: davidbritch
ms.author: dabritch
ms.date: 05/10/2017
ms.openlocfilehash: c93441bff02322fb938a67806ba7f5163c8c969e
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511910"
---
# <a name="three-ways-to-draw-an-arc"></a>繪製弧形的三種方式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_了解如何使用 SkiaSharp 三種不同的方式定義弧形_

弧形，是一條曲線上的省略符號，例如捨入的組件，此無限大符號的圓周：

![](arcs-images/arcsample.png "無限大符號")

定義的簡單起見，儘管沒有定義符合所有需求，反正繪圖函式方法，因此，在最佳的方式，來繪製弧形的圖形系統之間沒有共識。基於這個理由，`SKPath`類別不會限制本身只是一種方法。

`SKPath` 定義[ `AddArc` ](xref:SkiaSharp.SKPath.AddArc*)方法中，五個不同[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo*)方法和兩個相對[ `RArcTo` ](xref:SkiaSharp.SKPath.RArcTo*)方法。 這些方法分為三個類別，代表指定弧線的三個非常不同的方法。要使用哪個取決於可用來定義弧形，而這個弧形如何配合您在繪製的圖形資訊。

## <a name="the-angle-arc"></a>角度弧形

繪製弧線的角度弧線方法會要求您指定之界限橢圓形的矩形。 這個橢圓的圓周上弧線會指示從橢圓形的中心指出弧線和其長度的開頭的角度。 兩個不同的方法來繪製角度弧線。 這些是[ `AddArc` ](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single))方法和[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKRect,System.Single,System.Single,System.Boolean))方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

這些方法與 android [`AddArc`](xref:Android.Graphics.Path.AddArc*)和 [`ArcTo`] x: ArcTo *) 方法相同。 IOS [ `AddArc` ](xref:CoreGraphics.CGPath.AddArc(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.Boolean))方法類似，但僅限於弧線圓形的圓周上而不是一般化為橢圓形。

這兩種方法的開頭`SKRect`定義位置和大小的橢圓形的值：

![](arcs-images/anglearcoval.png "開始角度弧形之橢圓形")

弧線屬於這個橢圓的圓周。

`startAngle`引數是順時針旋轉的角度以度數為單位，相對於繪製橢圓形的中心從右邊的水平線。 `sweepAngle`引數是相對於`startAngle`。 以下是`startAngle`和`sweepAngle`值分別為 60 度和 100 度：

![](arcs-images/anglearcangles.png "定義角度弧線角度")

弧形開始的開始角度。 其長度受到掃掠角度。 弧線如下所示以紅色：

![](arcs-images/anglearchighlight.png "反白顯示的角度弧形")

加入具有路徑的曲線`AddArc`或`ArcTo`方法是只要該橢圓形的圓周的一部分：

![](arcs-images/anglearc.png "單獨角度弧形")

`startAngle` 或`sweepAngle`引數可以是負數:負值的`sweepAngle`圓弧會順時針旋轉, 而以順時針方向表示負數值。

不過，`AddArc`並未*不*定義封閉的分佈。 如果您呼叫`LineTo`之後`AddArc`，從弧形結尾點的繪製線條`LineTo`方法，以及相同成立的`ArcTo`。

`AddArc` 自動啟動新的分佈，並在功能上相當於呼叫`ArcTo`的最後一個引數`true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

最後一個引數，會呼叫`forceMoveTo`，並有效地使`MoveTo`呼叫弧線的開頭。開始新的分佈。 也就是不是這樣的最後一個引數`false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

這個版本的`ArcTo`從目前位置中繪製的線條，弧線的開頭。這表示弧形，可以某處是中間的較大的分佈。

**角度弧線**頁面可讓您使用兩個滑桿來指定開始和掃掠角度。 XAML 檔案具現化兩個`Slider`項目和`SKCanvasView`。 `PaintCanvas`中的處理常式[ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)檔案繪製橢圓形和弧形使用兩個`SKPaint`物件定義為欄位：

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

如您所見，開始角度和掃掠角度，可能需要在負值上：

[![](arcs-images/anglearc-small.png "角度的反正頁面的三個螢幕擷取畫面")](arcs-images/anglearc-large.png#lightbox "角度弧線頁面的三個螢幕擷取畫面")

這個方法，來產生弧線是以最簡單，就可以輕鬆地衍生參數化的方程式描述弧形。了解的大小和位置的橢圓形，] 和 [開始] 及 [掃掠角度，開始和結束點弧線的計算使用簡單的三角函數：

`x = oval.MidX + (oval.Width / 2) * cos(angle)`

`y = oval.MidY + (oval.Height / 2) * sin(angle)`

`angle`值是`startAngle`或`startAngle + sweepAngle`。

使用兩個定義在弧線角度最適用於您知道您想要繪製，比方說，若要製作圓形圖的弧線的 angular 的長度。 **分裂式圓形圖**示範這項 頁面。 [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)類別使用的內部類別來定義一些製作的資料和色彩：

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

`PaintSurface`處理常式中第一次執行迴圈來計算的項目`totalValues`數目。 它可以為分數的總數，決定每個項目大小，並將其轉換為角度：

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

新`SKPath`物件建立每個圓形圖配量。 路徑包含一行，以從管理中心，則`ArcTo`繪製弧線和另一行回 center 結果`Close`呼叫。 這個程式會顯示 「 分裂 」 的圓形圖配量的移動它們所有影片中心 x 50 像素。 這項工作需要每個配量中的中間點掃掠角度的方向的向量：

[![](arcs-images/explodedpiechart-small.png "分裂式圓形圖頁面的三個螢幕擷取畫面")](arcs-images/explodedpiechart-large.png#lightbox "的分裂式圓形圖頁面的三個螢幕擷取畫面")

若要查看是什麼樣子沒有 「 暴增 」，只是標記為註解`Translate`呼叫：

[![](arcs-images/explodedpiechartunexploded-small.png "分裂式圓形圖頁面，而不需要擴張的三個螢幕擷取畫面")](arcs-images/explodedpiechartunexploded-large.png#lightbox "的分裂式圓形圖頁面，而不需要擴張的三個螢幕擷取畫面")

## <a name="the-tangent-arc"></a>弧形正切函數

所支援的弧線的第二類`SKPath`是*正切函數的弧形*，因此呼叫，因為弧線是以兩個連接的直線的正切函數的圓形的圓周。

正切函數的弧線，會藉由呼叫加入至路徑[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,SkiaSharp.SKPoint,System.Single))具有兩個方法`SKPoint`參數，或有[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,System.Single,System.Single))使用不同的多載`Single`參數重點：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

這`ArcTo`PostScript 方法大致[ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) （頁面 532） 函式和 iOS [ `AddArcToPoint` ](xref:CoreGraphics.CGPath.AddArcToPoint(System.nfloat,System.nfloat,System.nfloat,System.nfloat,System.nfloat))方法。

`ArcTo`方法牽涉到三個點：

- 目前點的分佈或點 （0，0），如果`MoveTo`尚未呼叫
- 第一個點引數`ArcTo`方法，稱為*角點*
- 第二個點引數`ArcTo`，稱為*目的地點*:

![](arcs-images/tangentarcthreepoints.png "開始正切函數的弧形的三個點")

下列三個點定義兩個連接的線條：

![](arcs-images/tangentarcconnectinglines.png "連接的正切函數的弧線的三個點的線條")

如果三個點 colinear&mdash;也就是如果它們也都必須位於相同的直線&mdash;會繪製任何弧形。

`ArcTo`方法也包含`radius`參數。 這會定義於圓形的半徑：

![](arcs-images/tangentarccircle.png "正切函數的弧形的圓形")

正切函數的弧形的橢圓形從未一般化。

如果任何角度，符合的兩行，該圓形可以插入這幾行之間，使其正切為兩行：

![](arcs-images/tangentarctangentcircle.png "兩行之間的正切函數的弧形圓形")

新增至分佈曲線並未接觸中指定的點任一`ArcTo`方法。 它包含一條直線從目前點的第一個的正切函數點，並結束於第二個的正切函數點，如下所示，以紅色弧線：

![](arcs-images/tangentarchighlight.png "這兩行之間的反白顯示正切函數弧度")

以下是最終的直線和新增至分佈的弧形：

![](arcs-images/tangentarc.png "這兩行之間的反白顯示正切函數弧度")

Contour 可以從第二個的正切函數點繼續。

**正切函數弧線**頁面可讓您試驗弧形正切函數。這是衍生自數個頁面的第一個[ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs)，它會定義幾個實用`SKPaint`物件，並執行`TouchPoint`處理：

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

`TangentArcPage` 類別衍生自 `InteractivePage`。 建構函式[ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)檔案會負責具現化並初始化`touchPoints`陣列，並設定`baseCanvasView`(在`InteractivePage`) 至`SKCanvasView`中的物件具現化[ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)檔案：

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

`PaintSurface`處理常式會使用`ArcTo`繪製弧線的方法為基礎的觸控點和`Slider`，但也演算法來計算圓形的角度為基礎：

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

以下是**正切函數弧線**執行頁面：

[![](arcs-images/tangentarc-small.png "反正切函數頁面的三個螢幕擷取畫面")](arcs-images/tangentarc-large.png#lightbox "的弧形正切函數頁面的三個螢幕擷取畫面")

弧形正切函數是適合用來建立圓的角，例如一個圓角矩形。 因為`SKPath`已經包含`AddRoundedRect`方法，**四捨五入 Heptagon**頁面會示範如何使用`ArcTo`捨入七個邊的多邊形的邊角。 （程式碼被一般化的任何規則的多邊形）。

`PaintSurface`處理常式[ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs)類別包含一個`for`迴圈來計算 heptagon 和第二個計算的七個側邊，從這些端點的七個頂點的座標頂點。 這些情況的中間點則用來建構路徑中：

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

以下是執行的程式：

[![](arcs-images/roundedheptagon-small.png "捨入 Heptagon 頁面的三個螢幕擷取畫面")](arcs-images/roundedheptagon-large.png#lightbox "捨入 Heptagon 頁面的三個螢幕擷取畫面")

## <a name="the-elliptical-arc"></a>橢圓形弧線

橢圓形弧線，會藉由呼叫加入至路徑[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,SkiaSharp.SKPoint))方法，有兩個`SKPoint`參數，或有[ `ArcTo` ](xref:SkiaSharp.SKPath.ArcTo(System.Single,System.Single,System.Single,SkiaSharp.SKPathArcSize,SkiaSharp.SKPathDirection,System.Single,System.Single))多載使用個別的 X 和 Y 座標：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

橢圓形弧線是配合[橢圓形弧線](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands)納入可縮放向量圖形 (SVG) 和通用 Windows 平台[ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/)類別。

這些`ArcTo`方法的目前點的分佈，兩個點之間繪製弧形和最後一個參數`ArcTo`方法 (`xy`參數或個別`x`和`y`參數):

![](arcs-images/ellipticalarcpoints.png "兩個點定義橢圓形弧線")

第一個點參數`ArcTo`方法 (`r`，或`rx`和`ry`) 根本不是點，但改為指定的橢圓形; 水平及垂直半徑

![](arcs-images/ellipticalarcellipse.png "定義橢圓弧形的橢圓形")

`xAxisRotate`參數是順時針旋轉這個橢圓形的度數的數目：

![](arcs-images/ellipticalarctiltedellipse.png "傾斜定義橢圓弧形的橢圓形")

如果這個傾斜的橢圓形然後定位，讓它碰觸的兩個點，由兩個不同的弧線連接點：

![](arcs-images/ellipticalarcellipse1.png "第一組橢圓弧形")

這兩個弧線可以透過兩種方式來區別:上弧形會大於下弧線, 而當弧線由左至右繪製時, 會以順時針方向繪製上弧形, 而下弧線則以逆時針方向繪製。

此外，也可以符合另一種方法的兩個點之間的橢圓形：

![](arcs-images/ellipticalarcellipse2.png "第二組橢圓弧形")

現在沒有較小的弧形順時針旋轉，繪製的頂端和底部繪製的較大弧線以逆時針方向。

因此共有四種方法的傾斜的橢圓形所定義的弧形可以連線這些兩個點：

![](arcs-images/ellipticalarccolors.png "所有的四個橢圓弧形")

四種組合來區別這些四個弧形[ `SKPathArcSize` ](xref:SkiaSharp.SKPathArcSize)並[ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection)列舉型別引數`ArcTo`方法：

- 安全SKPathArcSize 大型和 SKPathDirection。順時針
- 環保SKPathArcSize Small 和 SKPathDirection。順時針
- 藍天SKPathArcSize Small 和 SKPathDirection。逆時針
- 深SKPathArcSize 大型和 SKPathDirection。逆時針

如果傾斜的橢圓形不是大到足以容納兩個點之間，然後它統一縮放直到夠大。 只有兩個唯一的弧形在此情況下連接的兩個點。 這些可以區別與`SKPathDirection`參數。

雖然這個方法，來定義弧形聽起來複雜，在初次遇到，它是可定義具有旋轉的橢圓形弧線的唯一方法，而且通常很簡單的方法時您需要整合圓弧線段的其他組件。

**橢圓形弧線**頁面可讓您以互動方式設定兩個點的大小和橢圓形的旋轉。 `EllipticalArcPage`類別衍生自`InteractivePage`，而`PaintSurface`中的處理常式[ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)程式碼後置檔案會繪製四個弧形：

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

以下執行：

[![](arcs-images/ellipticalarc-small.png "橢圓形弧線頁面的三個螢幕擷取畫面")](arcs-images/ellipticalarc-large.png#lightbox "橢圓形弧線頁面的三個螢幕擷取畫面")

**弧線無限大**頁面使用橢圓形弧線来繪製的無限值正負號。 無限大登根據兩個圓形隔開 100 個單位的 100 個單位的半徑：

![](arcs-images/infinitycircles.png "兩個圓形")

跨越彼此的兩行是以這兩個圓形的正切函數：

![](arcs-images/infinitycircleslines.png "兩個圓形正切函數的行")

無限大符號是組件，這些圓形，以及兩行的組合。 若要使用橢圓形弧線要繪製的無限值正負號，就必須判斷兩條線的圓形的正切所在的座標。

建構在一個圓形的正確矩形：

![](arcs-images/infinitytriangle.png "兩個圓形正切函數的行和內嵌的圓形")

圓形的半徑 100 單位，而三角形斜邊 150 單位，所以角度 α 是 100，150，或 41.8 度分割的反正弦值 （數值的反正弦值）。 150 次的餘弦函數 41.8 度或 112，由畢氏三元理論，也可以計算三角形的另一端的長度。

正切函數點的座標則可以使用這項資訊來計算：

`x = 112·cos(41.8) = 83`

`y = 112·sin(41.8) = 75`

四個的正切函數點都只需要點 （0，0） 上繪製置中對齊的無限大號，具有為 100 的圓形半徑：

![](arcs-images/infinitycoordinates.png "正切函數的行與座標的兩個圓形")

`PaintSurface`中的處理常式[ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs)類別放置無限大登以便 （0，0） 點位於中央的頁面上，並調整成螢幕大小的路徑：

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

程式碼會使用`Bounds`屬性`SKPath`來決定維度的無限大的正弦值，調整其規模大小的畫布：

[![](arcs-images/arcinfinity-small.png "弧線的無限值 頁面的三個螢幕擷取畫面")](arcs-images/arcinfinity-large.png#lightbox "弧線無限大頁面的三個螢幕擷取畫面")

結果看起來有點小，這暗示著`Bounds`屬性`SKPath`報告大於路徑的大小。

就內部而言，Skia 基本原則而論使用多個二次方貝茲曲線的弧線。 這些曲線 （如您所見下一節） 包含控管如何繪製曲線，但不屬於轉譯曲線的控制點。 `Bounds`屬性包含這些控點。

若要取得更緊密的調整，請使用`TightBounds`屬性，它會排除的控點。 以下是程式在橫向模式中執行，並使用`TightBounds`屬性取得路徑界限：

[![](arcs-images/arcinfinitytightbounds-small.png "弧線無限大頁面緊密繫結的三個螢幕擷取畫面")](arcs-images/arcinfinitytightbounds-large.png#lightbox "緊密繫結弧線無限大頁面的三個螢幕擷取畫面")

雖然以數學方式 smooth 弧線和直線，線條之間的連線，從弧線變更為直線似乎有點突然。 更好的無限大登上時，會在下一篇文章[**三種類型的貝茲曲線**](beziers.md)。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
