---
title: 繪製弧形的三種方式
description: 了解如何使用三種不同的方式定義弧形 SkiaSharp
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F1DA55E4-0182-4388-863C-5C340213BF3C
author: charlespetzold
ms.author: chape
ms.date: 05/10/2017
ms.openlocfilehash: c6fd0f905aceb9dddc4047abc6ad2722adf2d8e9
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="three-ways-to-draw-an-arc"></a>繪製弧形的三種方式

_了解如何使用三種不同的方式定義弧形 SkiaSharp_

弧形是省略符號，例如此無限符號的捨入的組件的周長上曲線：

![](arcs-images/arcsample.png "無限大符號")

定義的簡單起見，儘管沒有方法可定義符合需求，弧線繪圖函式，因此，若要繪製弧形，最好的圖形系統之間沒有共識。基於這個理由，`SKPath`類別不會限制本身只有一個方法。

`SKPath` 定義`AddArc`方法時，五個不同`ArcTo`方法和兩個相對`RArcTo`方法。 這些方法可分成三個類別，代表指定弧線的三個非常不同的方法。您使用取決於可用來定義在弧線和此弧線如何配合您所繪製的圖形資訊。

## <a name="the-angle-arc"></a>角度弧線

繪製弧形的角度弧線方法會要求您指定的橢圓形的矩形。 上的這個橢圓形弧線會以從管理中心進行開頭弧線和其長度的橢圓形的角度。 兩個不同方法來繪製角度弧形。 這些是[ `AddArc` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/)方法和[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKRect/System.Single/System.Single/System.Boolean/)方法：

```csharp
public void AddArc (SKRect oval, Single startAngle, Single sweepAngle)

public void ArcTo (SKRect oval, Single startAngle, Single sweepAngle, Boolean forceMoveTo)
```

這些方法都是相同的 Android [ `AddArc` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.AddArc/p/Android.Graphics.RectF/System.Single/System.Single/)和[ `ArcTo` ](https://developer.xamarin.com/api/member/Android.Graphics.Path.ArcTo/p/Android.Graphics.RectF/System.Single/System.Single/System.Boolean/)方法。 IOS [ `AddArc` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArc/p/System.Boolean/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/)方法類似，但僅限於圓形的圓周上的弧形而非一般化以橢圓形。

這兩種方法的開頭`SKRect`位置和大小的橢圓形所定義的值：

![](arcs-images/anglearcoval.png "開始角度弧形 oval")

弧線屬於此橢圓形的圓周。

`startAngle`引數是順時針旋轉角度 （度數） 相對於繪製橢圓形的中心從右方的水平線。 `sweepAngle`引數是相對於`startAngle`。 以下是`startAngle`和`sweepAngle`值的 60 到 100 的度數，分別：

![](arcs-images/anglearcangles.png "定義角度弧形角度")

弧線的開始角度時間開始。 它的長度皆受到掃掠角度：

![](arcs-images/anglearchighlight.png "反白顯示的角度弧線")

加入與路徑中的曲線`AddArc`或`ArcTo`方法為只需該橢圓形的周長，如下所示，以紅色的一部分：

![](arcs-images/anglearc.png "單獨使用角度弧線")

`startAngle`或`sweepAngle`引數可以是負數： 弧線是正值的順時針`sweepAngle`和負值逆時針旋轉。

不過，`AddArc`沒有*不*定義已關閉的分佈。 如果您呼叫`LineTo`之後`AddArc`，一條線會繪製弧線的端點中的點`LineTo`方法，以及相同成立的`ArcTo`。

`AddArc` 自動啟動新的分佈，並在功能上等於呼叫`ArcTo`的最後一個引數`true`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, true);
```

最後一個引數，會呼叫`forceMoveTo`，並有效地讓`MoveTo`呼叫弧線的開頭。開始新的分佈。 也就是不是這樣的最後一個引數`false`:

```csharp
path.ArcTo (oval, startAngle, sweepAngle, false);
```

這個版本的`ArcTo`從目前的位置繪製一條線弧線的開頭。這表示弧線可以是某處中間的較大的分佈。

**角度弧線**頁面可讓您使用兩個滑桿來指定開始和掃掠角度。 XAML 檔案會呈現兩個`Slider`項目和`SKCanvasView`。 `PaintCanvas`中的處理常式[ **AngleArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AngleArcPage.xaml.cs)檔案繪製橢圓形和弧形使用兩個`SKPaint`做為欄位定義的物件：

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

當您所見，開始角度和掃掠角度可能要花在負值上：

[![](arcs-images/anglearc-small.png "三個螢幕擷取畫面的角度弧線頁面")](arcs-images/anglearc-large.png#lightbox "角度弧線頁面的三個螢幕擷取畫面")

這個方法來產生弧形是以演算法最簡單，所以可以輕鬆地衍生參數描述弧線的方程式。了解的大小和位置的橢圓形和開始和掃掠角度，開始和結束點弧線的計算使用簡單的三角函數：

x = oval.MidX + (oval.Width / 2) * cos(angle)

y = 橢圓形。MidY + (oval。高度 / 2) * sin(angle)

`angle`值`startAngle`或`startAngle + sweepAngle`。

使用兩個的角度，以定義弧形最適用於您知道您想要繪製，比方說，讓圓形圖的弧度角度長度的情況。 **分裂式圓形圖**頁面為其示範。 [ `ExplodedPieChartPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ExplodedPieChartPage.cs)類別使用的內部類別來定義一些優質的資料和色彩：

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

`PaintSurface`處理常式中第一次循環的項目，來計算`totalValues`數目。 它可以決定每個項目大小為總計的分數，並轉換成角度：

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

新`SKPath`物件建立的每個圓形圖配量。 路徑包含中心中的行則`ArcTo`中心結果回繪製弧形，而另一條`Close`呼叫。 這個程式會顯示 「 分裂 」 的圓形圖扇區移動它們所有中央 50 像素。 這項工作需要掃掠角度的中點方向的向量的每個配量：

[![](arcs-images/explodedpiechart-small.png "分裂式圓形圖圖表頁面的三個螢幕擷取畫面")](arcs-images/explodedpiechart-large.png#lightbox "分裂式圓形圖圖表頁面的三個螢幕擷取畫面")

若要查看看起來沒有"爆炸 」，只是標記為註解`Translate`呼叫：

[![](arcs-images/explodedpiechartunexploded-small.png "三個螢幕擷取畫面的分裂式圓形圖沒有頁面爆炸")](arcs-images/explodedpiechartunexploded-large.png#lightbox "的分裂式圓形圖沒有頁面爆炸 Triple 螢幕擷取畫面")

## <a name="the-tangent-arc"></a>正切函數的弧形

支援弧線的第二個類型`SKPath`是*正切弧線*，緣由弧線是以兩個連接的直線的正切函數的一個圓形的圓周。

正切函數弧線，會呼叫加入至路徑[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/SkiaSharp.SKPoint/System.Single/)方法具有兩個`SKPoint`參數，或[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/System.Single/System.Single/)分別使用不同的多載`Single`參數重點：

```csharp
public void ArcTo (SKPoint point1, SKPoint point2, Single radius)

public void ArcTo (Single x1, Single y1, Single x2, Single y2, Single radius)
```

這`ArcTo`方法很類似 PostScript [ `arct` ](https://www.adobe.com/products/postscript/pdfs/PLRM.pdf) （PDF 文件中的頁面 532） 函式和 iOS [ `AddArcToPoint` ](https://developer.xamarin.com/api/member/CoreGraphics.CGPath.AddArcToPoint/p/System.nfloat/System.nfloat/System.nfloat/System.nfloat/System.nfloat/)方法。

`ArcTo`方法牽涉到三個點：

- 如果目前點的分佈或點 （0，0）`MoveTo`尚未呼叫
- 第一個點引數`ArcTo`呼叫的方法*邊角的點*
- 第二個點引數，若要`ArcTo`，稱為*目的地點*:

![](arcs-images/tangentarcthreepoints.png "三個點來開始的正切函數弧線")

下列三個點定義兩個連接的直線：

![](arcs-images/tangentarcconnectinglines.png "連接的正切函數弧線的三個點的線條")

如果三個點 colinear&mdash;亦即，如果它們位於相同的直線&mdash;沒有弧線要繪製。

`ArcTo`方法還包括`radius`參數。 這會定義圓形的半徑：

![](arcs-images/tangentarccircle.png "將正切函數弧線的圓形")

正切函數的弧形不被通用於橢圓形。

如果兩個線條符合任何角度，該圓形可以插入這些行之間，使其正切至這兩行：

![](arcs-images/tangentarctangentcircle.png "兩行之間的正切函數的弧形圓形")

加入至分佈曲線不會變更其中一個在指定的點`ArcTo`方法。 它包含目前點的第一個正切函數點和結束在第二個正切點上的弧形的直線行：

![](arcs-images/tangentarchighlight.png "反白顯示正切函數弧線兩行之間")

以下是最終的直線和新增至分佈的弧形：

![](arcs-images/tangentarc.png "反白顯示正切函數弧線兩行之間")

可以從第二個正切函數點繼續進行分佈。

**正切弧線**頁面可讓您試驗正切函數的弧形。這是衍生自的數個頁面中的第一個[ `InteractivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/InteractivePage.cs)，而後者可定義幾個便利`SKPaint`物件，並執行`TouchPoint`處理：

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

`TangentArcPage` 類別衍生自 `InteractivePage`。 在建構函式[ **TangentArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml.cs)檔案負責具現化，並初始化`touchPoints`陣列，以及設定`baseCanvasView`(中`InteractivePage`) 至`SKCanvasView`中具現化物件[ **TangentArcPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TangentArcPage.xaml)檔案：

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

`PaintSurface`處理常式會使用`ArcTo`繪製弧線的方法為基礎的觸控點和`Slider`，但也會利用演算法會計算的圓圈，以角度為基礎：

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

以下是**正切弧線**所有三個平台上執行的頁面：

[![](arcs-images/tangentarc-small.png "三個螢幕擷取畫面的正切函數弧線頁面")](arcs-images/tangentarc-large.png#lightbox "正切弧線頁面的三個螢幕擷取畫面")

在 Windows Mobile 裝置上，三個點幾乎 colinear，而且弧線便相當小。

正切函數的弧形是適合用來建立例如圓角矩形的圓的角。 因為`SKPath`已經包含`AddRoundedRect`方法，**四捨五入 Heptagon**頁面會示範如何使用`ArcTo`捨入七個邊的多邊形的邊角。 （程式碼一般化的任何規則的多邊形）。

`PaintSurface`處理常式的[ `RoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RoundedHeptagonPage.cs)類別包含一個`for`迴圈來計算 heptagon，第二個來計算從這些七個邊的中點的七個頂點的座標頂點。 然後這些中間點用來建構路徑：

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

以下是三個平台上執行的程式：

[![](arcs-images/roundedheptagon-small.png "三個螢幕擷取畫面的捨入 Heptagon 頁面")](arcs-images/roundedheptagon-large.png#lightbox "四捨五入 Heptagon 頁面的三個螢幕擷取畫面")

## <a name="the-elliptical-arc"></a>橢圓形弧線

橢圓形弧線，會呼叫加入至路徑[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/SkiaSharp.SKPoint/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/SkiaSharp.SKPoint/)方法具有兩個`SKPoint`參數，或[ `ArcTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ArcTo/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathArcSize/SkiaSharp.SKPathDirection/System.Single/System.Single/)多載具有不同的 X 和 Y 座標：

```csharp
public void ArcTo (SKPoint r, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, SKPoint xy)

public void ArcTo (Single rx, Single ry, Single xAxisRotate, SKPathArcSize largeArc, SKPathDirection sweep, Single x, Single y)
```

橢圓形弧線是一致[橢圓形弧線](http://www.w3.org/TR/SVG11/paths.html#PathDataEllipticalArcCommands)納入向量圖形 (SVG)] 和 [通用 Windows 平台[ `ArcSegment` ](/uwp/api/Windows.UI.Xaml.Media.ArcSegment/)類別。

這些`ArcTo`方法為目前點的分佈的兩個點之間繪製弧形，和最後一個參數`ArcTo`方法 (`xy`參數或個別`x`和`y`參數):

![](arcs-images/ellipticalarcpoints.png "兩個定義的橢圓形弧線的點")

第一個點參數`ArcTo`方法 (`r`，或`rx`和`ry`) 根本不是點，但改為指定的橢圓形; 水平及垂直半徑

![](arcs-images/ellipticalarcellipse.png "所定義的橢圓形弧線的橢圓形")

`xAxisRotate`參數是順時針旋轉這個橢圓形的角度數目：

![](arcs-images/ellipticalarctiltedellipse.png "所定義的橢圓形弧線的傾斜的橢圓形")

如果這個傾斜的橢圓形然後器，讓它所接觸的兩個點，由兩個不同的弧形連接點：

![](arcs-images/ellipticalarcellipse1.png "橢圓形弧線的第一個集合")

這些兩個弧形可以區分兩種方式： top 弧線大於底部弧線，同時為從左到右繪製弧形，最上層繪製弧形順時針方向時以逆時針方向繪製下弧形。

此外，也可以符合的省略符號，另一種方法的兩個點之間：

![](arcs-images/ellipticalarcellipse2.png "橢圓形弧線的第二個集合")

現在沒有的較小弧線順時針旋轉，繪製的上方和下方繪製的較大弧線逆時針旋轉。

因此可以由四種方法的總計傾斜的橢圓形所定義的弧形連接這些兩個點：

![](arcs-images/ellipticalarccolors.png "所有四個橢圓形弧線")

這些四個弧形的區別在於四種組合[ `SKPathArcSize` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathArcSize/)和[ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/)列舉型別引數`ArcTo`方法：

- 紅色： SKPathArcSize.Large 和 SKPathDirection.Clockwise
- 綠色： SKPathArcSize.Small 和 SKPathDirection.Clockwise
- 藍色： SKPathArcSize.Small 和 SKPathDirection.CounterClockwise
- 洋紅色： SKPathArcSize.Large 和 SKPathDirection.CounterClockwise

如果傾斜的橢圓形不是足以容納兩個點之間，然後它會一致地縮放夠大，直到。 只有兩個唯一的弧形在此情況下連接兩個點。 這些可以區別以`SKPathDirection`參數。

雖然這個方法來定義弧形聽起來複雜在遇到第一個項目，它是可定義與旋轉橢圓形弧線的唯一方法，而且這通常是當您需要與分佈的其他部分整合弧形最簡單的方法。

**橢圓形弧線**頁面可讓您以互動方式設定兩個點的大小和旋轉的橢圓形。 `EllipticalArcPage`類別衍生自`InteractivePage`，而`PaintSurface`中的處理常式[ **EllipticalArcPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/EllipticalArcPage.xaml.cs)程式碼後置檔案繪製四個弧形：

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

這裡與其執行所在的三個平台：

[![](arcs-images/ellipticalarc-small.png "三個螢幕擷取畫面的橢圓形弧線頁面")](arcs-images/ellipticalarc-large.png#lightbox "橢圓形弧線頁面的三個螢幕擷取畫面")

**弧線無限大**頁面使用橢圓形弧線来繪製無限大符號。 無限大符號根據兩個具有 100 個單位，並以 100 為單位的半徑圓形：

![](arcs-images/infinitycircles.png "兩個圓形")

這兩個圓形的正切彼此交叉兩行︰

![](arcs-images/infinitycircleslines.png "兩個圓形正切函數的行")

無限大符號是部分這些圓形與兩行的組合。 若要使用橢圓形弧線要繪製無限大符號，就必須決定兩個線條的圓圈的正切的座標。

建構一個圓形中的右矩形：

![](arcs-images/infinitytriangle.png "兩個圓形切線與內嵌的圓形")

圓形的半徑是 100 為單位，而三角形斜邊的 150 單位，因此角度 α 是分割成 150 或 41.8 度 100 的反正弦值 （數值的反正弦值）。 150 次的餘弦 41.8 度或 112，也會形成畢氏三元理論由計算三角形的另一端的長度。

正切函數點的座標然後可使用這項資訊來計算：

x = 112·cos(41.8) = 83

y = 112·sin(41.8) = 75

四個的正切函數點都只需要繪製圓形半徑為 100 （0，0） 在點上置中對齊無限大符號：

![](arcs-images/infinitycoordinates.png "正切函數的線條和座標的兩個圓形")

`PaintSurface`中的處理常式[ `ArcInfinityPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ArcInfinityPage.cs)類別放置無限大符號以便 （0，0） 點放置在頁面的中心和縮放的螢幕大小的路徑：

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

程式碼會使用`Bounds`屬性`SKPath`來判斷它在畫布的大小調整無限大正弦值的維度：

[![](arcs-images/arcinfinity-small.png "弧線無限大頁面的三個螢幕擷取畫面")](arcs-images/arcinfinity-large.png#lightbox "弧線無限大頁面的三個螢幕擷取畫面")

結果看起來更小，這表示`Bounds`屬性`SKPath`報告大於路徑的大小。

就內部而言，Skia 近似於使用多個二次方貝茲曲線的弧線。 這些曲線 （您會看到下一節） 包含控管如何繪製曲線，但不屬於轉譯曲線控制點。 `Bounds`屬性包含這些控點。

若要取得更緊密地調整，請使用`TightBounds`屬性，排除的控點。 以下是程式執行以橫向模式，並且使用`TightBounds`屬性取得路徑界限：

[![](arcs-images/arcinfinitytightbounds-small.png "弧線無限大頁面緊密的繫結的三個螢幕擷取畫面")](arcs-images/arcinfinitytightbounds-large.png#lightbox "弧線無限大頁面緊密的繫結的三個螢幕擷取畫面")

雖然數學 smooth 弧形和直線之間的連線，但似乎有點突然弧線直線的變更。 更好的無限大符號會呈現於下一個頁面。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
