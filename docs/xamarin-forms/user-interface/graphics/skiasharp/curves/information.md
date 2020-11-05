---
title: 路徑資訊與列舉
description: 本文說明如何取得 SkiaSharp 路徑和列舉內容的相關資訊，並以範例程式碼示範。
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 64a81b04961ebb0d03f661e99839b7ed78d710f2
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368670"
---
# <a name="path-information-and-enumeration"></a>路徑資訊與列舉

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_取得路徑和列舉內容的相關資訊_

[`SKPath`](xref:SkiaSharp.SKPath)類別會定義數個屬性和方法，可讓您取得路徑的相關資訊。 [`Bounds`](xref:SkiaSharp.SKPath.Bounds)和 [`TightBounds`](xref:SkiaSharp.SKPath.TightBounds) 屬性 (以及相關的方法) 取得路徑的度量維度。 [`Contains`](xref:SkiaSharp.SKPath.Contains(System.Single,System.Single))方法可讓您判斷某個特定點是否在路徑內。

判斷組成路徑之所有線條和曲線的總長度，有時會很有用。 計算此長度不是演算法簡單的工作，因此名為的整個類別 [`PathMeasure`](xref:SkiaSharp.SKPathMeasure) 都是專門用來命名。

它有時也有助於取得構成路徑的所有繪圖作業和點。 一開始，此功能看起來可能不是必要的：如果您的程式已建立路徑，則程式已經知道內容。 不過，您已經看過路徑 [效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md) 也可以建立路徑，並將 [文字字串轉換成路徑](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 您也可以取得組成這些路徑的所有繪圖作業和點。 其中一個可能的原因是，將演算法轉換套用至所有點，例如，將文字包裝在半球上：

![包裝在半球上的文字](information-images/pathenumerationsample.png)

## <a name="getting-the-path-length"></a>取得路徑長度

在文章 [**路徑和文字**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md) 中，您會看到如何使用 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 方法來繪製文字字串，其基準遵循路徑的過程。 但是，如果您想要將文字的大小調整成精確地符合路徑，該怎麼辦？ 在圓形周圍繪製文字很簡單，因為圓形的圓周很容易計算。 但橢圓形的圓周或貝茲曲線的長度不太簡單。

[`SKPathMeasure`](xref:SkiaSharp.SKPathMeasure)類別可提供協助。 此函式 [會接受](xref:SkiaSharp.SKPathMeasure.%23ctor(SkiaSharp.SKPath,System.Boolean,System.Single)) `SKPath` 引數，而 [`Length`](xref:SkiaSharp.SKPathMeasure.Length) 屬性會顯示其長度。

此類別會在 **路徑長度** 範例中示範，此範例是以 **貝茲曲線** 頁面為基礎。 [**PathLengthPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)是衍生自 `InteractivePage` ，而且包含觸控介面：

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

[**PathLengthPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)程式碼後端檔案可讓您移動四個觸控點，以定義三次方貝茲曲線的結束點和控制點。 有三個欄位定義文字字串、 `SKPaint` 物件和文字的計算寬度：

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

`baseTextWidth`欄位是以10設定為基礎的文字寬度 `TextSize` 。

`PaintSurface`處理常式會繪製貝茲曲線，然後將文字的大小調整為符合其完整長度：

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

`Length`新建立之物件的屬性會取得 `SKPathMeasure` 路徑的長度。 路徑長度會除以 (的值，也 `baseTextWidth` 就是以 10) 的文字大小為依據的文字寬度，然後再乘以基底文字大小10。 結果是一種新的文字大小，可顯示該路徑的文字：

[![路徑長度頁面的三重螢幕擷取畫面](information-images/pathlength-small.png)](information-images/pathlength-large.png#lightbox "路徑長度頁面的三重螢幕擷取畫面")

當貝茲曲線變得更長或更短時，您可以看到文字大小變更。

## <a name="traversing-the-path"></a>遍歷路徑

`SKPathMeasure` 可以做的不只是測量路徑的長度。 針對介於零和路徑長度之間的任何值， `SKPathMeasure` 物件可以取得路徑上的位置，以及該點上路徑曲線的正切函數。 正切函數可作為物件格式的向量 `SKPoint` ，或以物件中封裝的旋轉形式提供 `SKMatrix` 。 以下是 `SKPathMeasure` 以各種彈性的方式取得這項資訊的方法：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

列舉的成員 [`SKPathMeasureMatrixFlags`](xref:SkiaSharp.SKPathMeasureMatrixFlags) 如下：

- `GetPosition`
- `GetTangent`
- `GetPositionAndTangent`

**Unicycle 的半管道** 頁面會在 Unicycle 上建立動畫，該圖似乎會沿著三次貝茲曲線來回傳送：

[![Unicycle Half-Pipe 頁面的三重螢幕擷取畫面](information-images/unicyclehalfpipe-small.png)](information-images/unicyclehalfpipe-large.png#lightbox "Unicycle Half-Pipe 頁面的三重螢幕擷取畫面")

`SKPaint`用來為半形線和 unicycle 進行筆劃的物件會定義為類別中的欄位 `UnicycleHalfPipePage` 。 此外，也定義了 `SKPath` unicycle 的物件：

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

類別包含動畫之和方法的標準覆寫 `OnAppearing` `OnDisappearing` 。 `PaintSurface`處理常式會建立半管道的路徑，然後繪製它。 `SKPathMeasure`接著會根據此路徑建立物件：

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

`PaintSurface`處理常式 `t` 會計算每五秒從0到1的值。 然後，它會使用函式 `Math.Cos` 將該值轉換為 `t` 範圍從0到1的值，再轉換回0，其中0對應至左上方的 unicycle，而1對應至右上方的 unicycle。 余弦函數會導致在管道頂端最慢的速度，以及底部最快的速度。

請注意，這個值 `t` 必須乘以的第一個引數的路徑長度 `GetMatrix` 。 然後，會將矩陣套用至 `SKCanvas` 物件，以繪製 unicycle 路徑。

## <a name="enumerating-the-path"></a>列舉路徑

的兩個內嵌類別可 `SKPath` 讓您列舉路徑的內容。 這些類別為 [`SKPath.Iterator`](xref:SkiaSharp.SKPath.Iterator) 和 [`SKPath.RawIterator`](xref:SkiaSharp.SKPath.RawIterator) 。 這兩個類別非常類似，但 `SKPath.Iterator` 可以消除路徑中長度為零的元素，或接近長度為零的專案。 `RawIterator`在下列範例中使用。

您可以藉由呼叫的方法，取得型別的物件 `SKPath.RawIterator` [`CreateRawIterator`](xref:SkiaSharp.SKPath.CreateRawIterator) `SKPath` 。 您可以重複呼叫方法來完成路徑的列舉 [`Next`](xref:SkiaSharp.SKPath.RawIterator.Next*) 。 將四個值的陣列傳遞給它 `SKPoint` ：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

方法會傳回 `Next` 列舉型別的成員 [`SKPathVerb`](xref:SkiaSharp.SKPathVerb) 。 這些值表示路徑中的特定繪製命令。 插入陣列中的有效點數目取決於此動詞命令：

- `Move` 使用單一點
- `Line` 有兩個點
- `Cubic` 有四個點
- `Quad` 有三個點
- `Conic` 使用三個點 (，同時也呼叫 [`ConicWeight`](xref:SkiaSharp.SKPath.RawIterator.ConicWeight*) 加權的方法) 
- `Close` 使用一個點
- `Done`

指令 `Done` 動詞指出路徑列舉已完成。

請注意，沒有任何 `Arc` 動詞。 這表示在新增至路徑時，所有弧形都會轉換成貝茲曲線。

陣列中的部分資訊 `SKPoint` 是多餘的。 例如，如果 `Move` 動詞後面接著 `Line` 動詞，則伴隨的兩個點中的第一個 `Line` 會與 `Move` 點相同。 在實務上，這種冗余非常有説明。 當您取得 `Cubic` 動詞時，它會伴隨著定義三次貝茲曲線的四個點。 您不需要保留先前動詞所建立的目前位置。

但是，有問題的動詞是 `Close` 。 此命令會將目前位置的直線繪製至先前由命令建立的輪廓開頭 `Move` 。 在理想的情況下， `Close` 動詞應該提供這兩個點，而不是只有一個點。 更糟的是，動詞所附的點 `Close` 一律是 (0，0) 。 當您列舉路徑時，您可能需要保留 `Move` 點和目前的位置。

## <a name="enumerating-flattening-and-malforming"></a>列舉、簡維和 Malforming

有時候您會想要將演算法轉換套用至路徑，以某種方式 malform 它：

![包裝在半球上的文字](information-images/pathenumerationsample.png)

這些字母大多都是由直線所組成，但是這些直線顯然都已變成曲線。 這有何可能？

關鍵是，原始的直線會分成一系列較小的直線。 然後可以用不同的方式操作這些個別的直線，以形成曲線。

為了協助進行此程式， [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 範例包含一個靜態 [`PathExtensions`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs) 類別，其 `Interpolate` 方法會將直線向下細分為許多隻有一個單位長度的短行。 此外，此類別包含數個方法，會將三種類型的貝茲曲線轉換成一系列接近曲線的一系列小型直線。  (的參數化公式會在文章 [**三種類型的貝茲曲線**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)中顯示。 ) 此程式 _稱為簡_ 維曲線：

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
            points[i] = new SKPoint(x, y);
        }

        return points;
    }

    static double Length(SKPoint pt0, SKPoint pt1)
    {
        return Math.Sqrt(Math.Pow(pt1.X - pt0.X, 2) + Math.Pow(pt1.Y - pt0.Y, 2));
    }
}
```

所有這些方法都是從 `CloneWithTransform` 這個類別中也包含的擴充方法參考，如下所示。 這個方法會藉由列舉路徑命令，並根據資料來建立新路徑，來複製路徑。 不過，新路徑只包含 `MoveTo` 和 `LineTo` 呼叫。 所有曲線和直線都會縮減成一系列的小線條。

當呼叫時 `CloneWithTransform` ，您會傳遞至方法 a `Func<SKPoint, SKPoint>` ，也就是具有傳回 `SKPaint` 值之參數的函數 `SKPoint` 。 每個點都會呼叫此函式，以套用自訂演算法轉換：

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

由於複製的路徑會縮減為極少的直線，因此轉換函式具有將直線轉換成曲線的功能。

請注意，方法會保留變數中每個分佈的第一個點 `firstPoint` ，以及變數中每個繪製命令之後的目前位置 `lastPoint` 。 當遇到動詞時，必須使用這些變數來建立最後的結尾行 `Close` 。

**GlobularText** 範例會使用此擴充方法，在3d 效果中以半球環繞文字：

[![Globular 文字頁面的三重螢幕擷取畫面](information-images/globulartext-small.png)](information-images/globulartext-large.png#lightbox "Globular 文字頁面的三重螢幕擷取畫面")

類別的函式會 [`GlobularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs) 執行這類轉換。 它會建立 `SKPaint` 文字的物件，然後 `SKPath` 從方法取得物件 `GetTextPath` 。 這是傳遞至 `CloneWithTransform` 擴充方法的路徑以及轉換函式：

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

轉換函式會先計算兩個名為的值， `longitude` 以及介於 `latitude` 文字頂端和左邊的–π/2 之間的值，到文字右邊和底部的π/2。 這些值的範圍沒有視覺效果，因此會將它們乘以0.75 來減少。  (在沒有這些調整的情況下嘗試程式碼。 文字會在北和南兩極上變得太模糊，且在側邊太細。 ) 三維球形座標會轉換為二維 `x` ，並 `y` 依標準公式座標。

新的路徑會儲存為欄位。 `PaintSurface`然後，處理常式只需要置中和縮放路徑，即可將它顯示在螢幕上：

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

這是非常靈活的技巧。 如果 [**路徑效果**](effects.md) 文章中所述的路徑效果陣列不太包含您認為應該包含的內容，這就是填滿間距的方式。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)