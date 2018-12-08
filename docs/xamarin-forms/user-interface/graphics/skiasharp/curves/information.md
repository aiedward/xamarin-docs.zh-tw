---
title: 路徑資訊與列舉
description: 本文說明如何取得 SkiaSharp 路徑的相關資訊，並列舉內容，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
ms.openlocfilehash: 98975d51c31b8e8c52d184c631194388cd6cfa87
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053896"
---
# <a name="path-information-and-enumeration"></a>路徑資訊與列舉

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_取得路徑的相關資訊，並列舉內容_

[ `SKPath` ](xref:SkiaSharp.SKPath)類別會定義數個屬性和方法，可讓您取得路徑的相關資訊。 [ `Bounds` ](xref:SkiaSharp.SKPath.Bounds)並[ `TightBounds` ](xref:SkiaSharp.SKPath.TightBounds)屬性 （和相關的方法） 取得 metrical 維度的路徑。 [ `Contains` ](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single))方法可讓您判斷特定的點是否在路徑。

有時候，它是很有用來判斷所有的直線和曲線路徑所組成的總長度。 計算這個長度不是以簡單的工作，因此整個類別名為[ `PathMeasure` ](xref:SkiaSharp.SKPathMeasure)專門提供給它。

最好也有時若要取得所有繪圖作業和組成路徑的點。 一開始，這項功能似乎不必要： 如果您的程式已建立的路徑，該程式已經知道的內容。 不過，您已了解也可以藉由建立路徑[路徑效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)並藉由轉換[為路徑的文字字串](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 您也可以取得所有繪圖作業和構成這些路徑的點。 其中一個可能性是適用於演算法的轉換所有的點，如，而繞半球：

![](information-images/pathenumerationsample.png "包裝半球上的文字")

## <a name="getting-the-path-length"></a>取得路徑長度

文件中[**路徑及文字**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)了解如何使用[ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint))方法來繪製文字字串，其基準遵循的路徑。 但是，如果您要的文字大小，使其精確地符合路徑？ 圓形周圍繪製的文字很容易，因為圓形的圓周是簡單計算。 但的橢圓形的圓周或貝茲曲線的長度不是那麼簡單。

[ `SKPathMeasure` ](xref:SkiaSharp.SKPathMeasure)類別可以幫助。 [建構函式](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single))接受`SKPath`引數，而[ `Length` ](xref:SkiaSharp.SKPathMeasure.Length)屬性會顯示它的長度。

這個類別所示**路徑長度**範例中，此作業取決於**貝茲曲線**頁面。 [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)檔案衍生自`InteractivePage`並包含在觸控式介面：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Curves.PathLengthPage"
                       Title="Path Length">
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</local:InteractivePage>
```

[ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)程式碼後置檔案可讓您將四個觸控點，來定義結束點，以及控制三次方貝茲曲線的點。 三個欄位定義的文字字串，`SKPaint`物件，而導出的文字的寬度：

```csharp
public partial class PathLengthPage : InteractivePage
{
    const string text = "Compute length of path";

    static SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Black,
        TextSize = 10,
    };

    static readonly float baseTextWidth = textPaint.MeasureText(text);
    ...
}
```

`baseTextWidth`欄位是根據文字的寬度`TextSize`設定為 10。

`PaintSurface`處理常式繪製貝茲曲線，並再調整大小以符合沿著其完整長度的文字：

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

        // Get path length
        SKPathMeasure pathMeasure = new SKPathMeasure(path, false, 1);

        // Find new text size
        textPaint.TextSize = pathMeasure.Length / baseTextWidth * 10;

        // Draw text on path
        canvas.DrawTextOnPath(text, path, 0, 0, textPaint);
    }
    ...
}
```

`Length`新建屬性`SKPathMeasure`物件取得路徑的長度。 路徑長度除以`baseTextWidth`值 （即 10 的文字大小所根據的文字的寬度），然後再乘以 10 的基底的文字大小。 結果會是新的文字大小，用於顯示文字沿該路徑：

[![](information-images/pathlength-small.png "路徑長度頁面的三個螢幕擷取畫面")](information-images/pathlength-large.png#lightbox "的路徑長度的頁面上的三個螢幕擷取畫面")

貝茲曲線越長或短，您可以看到變更文字大小。

## <a name="traversing-the-path"></a>周遊的路徑

`SKPathMeasure` 可以執行不只是量值路徑的長度。 每個值介於 0 到路徑的長度，`SKPathMeasure`物件可以在該點取得的位置路徑，以及路徑曲線的正切函數。 正切函數是可用的表單中的向量`SKPoint`物件，或為旋轉封裝在`SKMatrix`物件。 以下是方法的`SKPathMeasure`，以各不相同且彈性的方式取得這項資訊：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

成員[ `SKPathMeasureMatrixFlags` ](xref:SkiaSharp.SKPathMeasureMatrixFlags)列舉型別是：

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

**獨半管道**頁面繪製桿狀圖形上似乎來回寫沿著三次方貝茲曲線獨：

[![](information-images/unicyclehalfpipe-small.png "獨半管道頁面的三個螢幕擷取畫面")](information-images/unicyclehalfpipe-large.png#lightbox "獨半管道頁面的三個螢幕擷取畫面")

`SKPaint`中的欄位定義物件，用於繪製半管道和獨[ `UnicycleHalfPipePage` ]()類別。 也定義是`SKPath`獨物件：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };

    SKPath unicyclePath = SKPath.ParseSvgPathData(
        "M 0 0" +
        "A 25 25 0 0 0 0 -50" +
        "A 25 25 0 0 0 0 0 Z" +
        "M 0 -25 L 0 -100" +
        "A 15 15 0 0 0 0 -130" +
        "A 15 15 0 0 0 0 -100 Z" +
        "M -25 -85 L 25 -85");
    ...
}
```

此類別包含的標準的覆寫`OnAppearing`和`OnDisappearing`動畫的方法。 `PaintSurface`處理常式建立半管道的路徑，然後將它繪製。 `SKPathMeasure`物件然後建立根據此路徑：

```csharp
public class UnicycleHalfPipePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath pipePath = new SKPath())
        {
            pipePath.MoveTo(50, 50);
            pipePath.CubicTo(0, 1.25f * info.Height,
                             info.Width - 0, 1.25f * info.Height,
                             info.Width - 50, 50);

            canvas.DrawPath(pipePath, strokePaint);

            using (SKPathMeasure pathMeasure = new SKPathMeasure(pipePath))
            {
                float length = pathMeasure.Length;

                // Animate t from 0 to 1 every three seconds
                TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
                float t = (float)(timeSpan.TotalSeconds % 5 / 5);

                // t from 0 to 1 to 0 but slower at beginning and end
                t = (float)((1 - Math.Cos(t * 2 * Math.PI)) / 2);

                SKMatrix matrix;
                pathMeasure.GetMatrix(t * length, out matrix,
                                      SKPathMeasureMatrixFlags.GetPositionAndTangent);

                canvas.SetMatrix(matrix);
                canvas.DrawPath(unicyclePath, strokePaint);
            }
        }
    }
}
```

`PaintSurface`處理常式會計算值為`t`，是從 0 到 1 每五秒。 然後它會使用`Math.Cos`將其轉換為值的函式`t`，範圍從 0 到 1，再回復成 0，其中 0 對應到位於左上方，開頭獨右上方獨 1 與對應。 餘弦函式會導致最慢管道頂端和底部最快的速度。

請注意，此值`t`必須第一個引數的路徑長度乘以`GetMatrix`。 矩陣會套用到`SKCanvas`繪製獨路徑的物件。

## <a name="enumerating-the-path"></a>列舉路徑

兩個內嵌的類別`SKPath`可讓您列舉路徑的內容。 這些類別是[ `SKPath.Iterator` ](xref:SkiaSharp.SKPath.Iterator)並[ `SKPath.RawIterator` ](xref:SkiaSharp.SKPath.RawIterator)。 這兩個類別都非常類似，但`SKPath.Iterator`可以排除在路徑中，長度為零，或接近零長度的項目。 `RawIterator`下面的範例中使用。

您可以取得型別的物件`SKPath.RawIterator`藉由呼叫[ `CreateRawIterator` ](xref:SkiaSharp.SKPath.CreateRawIterator)方法`SKPath`。 列舉路徑透過重複呼叫[ `Next` ](xref:SkiaSharp.SKPath.RawIterator.Next*)方法。 將傳遞給它的四個陣列`SKPoint`值：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next`方法傳回的成員[ `SKPathVerb` ](xref:SkiaSharp.SKPathVerb)列舉型別。 這些值會指出特定的繪製命令的路徑中。 陣列中插入有效的點數目取決於此動詞命令：

- `Move` 單點
- `Line` 兩個點
- `Cubic` 具有四個點
- `Quad` 使用三個點
- `Conic` 使用三個點 (也呼叫[ `ConicWeight` ](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*)方法的權數)
- `Close` 一個點
- `Done`

`Done`動詞表示路徑列舉已完成。

請注意，有沒有`Arc`動詞命令。 這表示所有弧線會都轉換成貝茲曲線加入至路徑時。

中的資訊的一些`SKPoint`是多餘的陣列。 例如，如果`Move`動詞後面`Line`動詞命令，則伴隨的兩個點的第一個`Line`等同於`Move`點。 在實務上，此備援是非常有幫助。 當您進入`Cubic`動詞，伴隨著所有四個點定義三次方貝茲曲線。 您不需要保留先前所用的動詞命令所建立的目前位置。

有問題的動詞命令，不過，是`Close`。 此命令從目前的位置繪製直線線段稍早建立的開頭`Move`命令。 在理想情況下，`Close`動詞命令應提供這些兩個點，而不是只是一個點。 更糟的是在於點隨附`Close`動詞命令一律是 （0，0）。 當您列舉路徑時，您可能需要保留`Move`點和目前的位置。

## <a name="enumerating-flattening-and-malforming"></a>列舉，扁平化和 Malforming

有時會需要套用演算法 malform 的路徑將其轉換以某種方式：

![](information-images/pathenumerationsample.png "包裝半球上的文字")

大部分的這些字母所組成的直線，但顯然成曲線 twisted 這些直線。 這是怎麼辦到？

索引鍵是原始的直線，線條分成一系列的較小的直線，線條。 然後可以操作這些個別較小的直線，線條以不同的方式，以形成曲線。 

若要協助進行此程序中， [ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例包含靜態[ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs)類別`Interpolate`細分的方法許多較短的行就是只有一個單位的長度成直線。 此外，此類別包含數種方法，轉換成一系列的小的直線，線條估計曲線的貝茲曲線的三種類型。 (一文中提供的參數化的公式[**三種類型的貝茲曲線**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)。)這個過程就叫做_壓平合併_曲線：

```csharp
static class PathExtensions
{
    ...
    static SKPoint[] Interpolate(SKPoint pt0, SKPoint pt1)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * pt0.X + t * pt1.X;
            float y = (1 - t) * pt0.Y + t * pt1.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenCubic(SKPoint pt0, SKPoint pt1, SKPoint pt2, SKPoint pt3)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2) + Length(pt2, pt3));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * (1 - t) * pt0.X +
                        3 * t * (1 - t) * (1 - t) * pt1.X +
                        3 * t * t * (1 - t) * pt2.X +
                        t * t * t * pt3.X;
            float y = (1 - t) * (1 - t) * (1 - t) * pt0.Y +
                        3 * t * (1 - t) * (1 - t) * pt1.Y +
                        3 * t * t * (1 - t) * pt2.Y +
                        t * t * t * pt3.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenQuadratic(SKPoint pt0, SKPoint pt1, SKPoint pt2)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static SKPoint[] FlattenConic(SKPoint pt0, SKPoint pt1, SKPoint pt2, float weight)
    {
        int count = (int)Math.Max(1, Length(pt0, pt1) + Length(pt1, pt2));
        SKPoint[] points = new SKPoint[count];

        for (int i = 0; i < count; i++)
        {
            float t = (i + 1f) / count;
            float denominator = (1 - t) * (1 - t) + 2 * weight * t * (1 - t) + t * t;
            float x = (1 - t) * (1 - t) * pt0.X + 2 * weight * t * (1 - t) * pt1.X + t * t * pt2.X;
            float y = (1 - t) * (1 - t) * pt0.Y + 2 * weight * t * (1 - t) * pt1.Y + t * t * pt2.Y;
            x /= denominator;
            y /= denominator;
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

所有這些方法被參考的擴充方法`CloneWithTransform`也包含在這個類別，並如下所示。 這個方法會複製為路徑列舉路徑命令，並建構新的路徑，以資料為依據。 不過，新的路徑中只包含`MoveTo`和`LineTo`呼叫。 所有的曲線和直線縮減為一系列的小型線條。

當呼叫`CloneWithTransform`，您傳遞給方法`Func<SKPoint, SKPoint>`，這是使用函式`SKPaint`傳回的參數`SKPoint`值。 每個點，以套用自訂演算法的轉換，會呼叫此函數：

```csharp
static class PathExtensions
{
    public static SKPath CloneWithTransform(this SKPath pathIn, Func<SKPoint, SKPoint> transform)
    {
        SKPath pathOut = new SKPath();

        using (SKPath.RawIterator iterator = pathIn.CreateRawIterator())
        {
            SKPoint[] points = new SKPoint[4];
            SKPathVerb pathVerb = SKPathVerb.Move;
            SKPoint firstPoint = new SKPoint();
            SKPoint lastPoint = new SKPoint();

            while ((pathVerb = iterator.Next(points)) != SKPathVerb.Done)
            {
                switch (pathVerb)
                {
                    case SKPathVerb.Move:
                        pathOut.MoveTo(transform(points[0]));
                        firstPoint = lastPoint = points[0];
                        break;

                    case SKPathVerb.Line:
                        SKPoint[] linePoints = Interpolate(points[0], points[1]);

                        foreach (SKPoint pt in linePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[1];
                        break;

                    case SKPathVerb.Cubic:
                        SKPoint[] cubicPoints = FlattenCubic(points[0], points[1], points[2], points[3]);

                        foreach (SKPoint pt in cubicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[3];
                        break;

                    case SKPathVerb.Quad:
                        SKPoint[] quadPoints = FlattenQuadratic(points[0], points[1], points[2]);

                        foreach (SKPoint pt in quadPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Conic:
                        SKPoint[] conicPoints = FlattenConic(points[0], points[1], points[2], iterator.ConicWeight());

                        foreach (SKPoint pt in conicPoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        lastPoint = points[2];
                        break;

                    case SKPathVerb.Close:
                        SKPoint[] closePoints = Interpolate(lastPoint, firstPoint);

                        foreach (SKPoint pt in closePoints)
                        {
                            pathOut.LineTo(transform(pt));
                        }

                        firstPoint = lastPoint = new SKPoint(0, 0);
                        pathOut.Close();
                        break;
                }
            }
        }
        return pathOut;
    }
    ...
}
```

複製的路徑會減少到小的直線，因為轉換函式會有將直線，線條轉換成曲線的能力。

請注意，方法會保留第一個點的呼叫的變數中的每個分佈`firstPoint`和目前的位置，在每個變數中繪製命令`lastPoint`。 這些變數是必要建構最後的結尾行時`Close`遇到動詞命令。

**GlobularText**範例會使用這個延伸模組方法看似繞半球 3D 的作用中：

[![](information-images/globulartext-small.png "Globular 文字頁面的三個螢幕擷取畫面")](information-images/globulartext-large.png#lightbox "Globular 文字頁面的三個螢幕擷取畫面")

[ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs)類別建構函式會執行這項轉換。 它會建立`SKPaint`物件的文字，並再取得`SKPath`物件從`GetTextPath`方法。 這是傳遞至的路徑`CloneWithTransform`以及的轉換函式的擴充方法：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;

    public GlobularTextPage()
    {
        Title = "Globular Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.Typeface = SKTypeface.FromFamilyName("Times New Roman");
            textPaint.TextSize = 100;

            using (SKPath textPath = textPaint.GetTextPath("HELLO", 0, 0))
            {
                SKRect textPathBounds;
                textPath.GetBounds(out textPathBounds);

                globePath = textPath.CloneWithTransform((SKPoint pt) =>
                {
                    double longitude = (Math.PI / textPathBounds.Width) *
                                            (pt.X - textPathBounds.Left) - Math.PI / 2;
                    double latitude = (Math.PI / textPathBounds.Height) *
                                            (pt.Y - textPathBounds.Top) - Math.PI / 2;

                    longitude *= 0.75;
                    latitude *= 0.75;

                    float x = (float)(Math.Cos(latitude) * Math.Sin(longitude));
                    float y = (float)Math.Sin(latitude);

                    return new SKPoint(x, y);
                });
            }
        }
    }
    ...
}
```

轉換函式會先計算兩個名為的值`longitude`和`latitude`– π/2 上方和左邊的文字，從範圍到 π/2，在右側和底部的文字。 這些值的範圍不是視覺上令人滿意的所以它們會減少乘以 0.75。 （請嘗試這些調整沒有程式碼。 文字會變得太模糊的北部和中南部兩極，且太精簡側邊。）這些更低的球面的三維座標會轉換為二維`x`和`y`座標標準的公式。

新的路徑儲存為欄位。 `PaintSurface`處理常式則只需要置中與縮放以顯示在螢幕上的路徑：

```csharp
public class GlobularTextPage : ContentPage
{
    SKPath globePath;
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint pathPaint = new SKPaint())
        {
            pathPaint.Style = SKPaintStyle.Fill;
            pathPaint.Color = SKColors.Blue;
            pathPaint.StrokeWidth = 3;
            pathPaint.IsAntialias = true;

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(0.45f * Math.Min(info.Width, info.Height));     // radius
            canvas.DrawPath(globePath, pathPaint);
        }
    }
}
```

這是非常靈活的技巧。 如果路徑效果的陣列中所述[**路徑效果**](effects.md)文章相當不包含項目您認為應包含，這是要填滿間距的方法。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
