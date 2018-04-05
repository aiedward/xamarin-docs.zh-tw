---
title: 路徑資訊和列舉
description: 取得路徑的相關資訊，並列舉內容
ms.prod: xamarin
ms.assetid: 8E8C5C6A-F324-4155-8652-7A77D231B3E5
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 09/12/2017
ms.openlocfilehash: 82ac4ea49462c7520219e1a621ea3946297b1b45
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="path-information-and-enumeration"></a>路徑資訊和列舉

_取得路徑的相關資訊，並列舉內容_

[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)類別會定義數個屬性和方法，可讓您取得路徑的相關資訊。 [ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/)和[ `TightBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.TightBounds/)屬性 （和相關的方法） 取得 metrical 維度的路徑。 [ `Contains` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Contains/p/System.Single/System.Single/)方法可讓您判斷是否在路徑的某個特定點。

可能會很有用來判斷所有線條和曲線路徑所組成的總長度。 這是不以演算法簡單的工作，所以整個類別命名為[ `PathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/)專用於它。

它也是有時候有用來取得所有繪圖作業和組成路徑的點。 首先，此功能似乎不必要： 如果您的程式已建立路徑，此程式已經知道內容。 不過，您所見路徑也可以建立由[路徑效果](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)和轉換[文字字串轉換成路徑](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)。 您也可以取得所有繪圖作業和構成這些路徑的點。 一個可能的原因是演算法的轉換套用至所有點。 這可讓技術，例如繞半球：

![](information-images/pathenumerationsample.png "包裝半球上的文字")

## <a name="getting-the-path-length"></a>取得路徑長度

發行項中[**路徑和文字**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/text-paths.md)您可了解如何使用[ `DrawTextOnPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawTextOnPath/p/System.String/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPaint/)方法來繪製文字字串，其基準線之後採取的路徑。 但要是您希望調整文字大小，使其精確地符合路徑？ 圓形周圍繪製文字，這很簡單，因為是計算簡單的圓形的圓周。 但橢圓形的圓周或貝茲曲線的長度不是這麼簡單。 

[ `SKPathMeasure` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasure/)類別可以幫助。 [建構函式](https://developer.xamarin.com/api/constructor/SkiaSharp.SKPathMeasure.SKPathMeasure/p/SkiaSharp.SKPath/System.Boolean/System.Single/)接受`SKPath`引數，而[ `Length` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPathMeasure.Length/)屬性會顯示它的長度。

這示範於**路徑長度**範例，根據**貝茲曲線**頁面。 [ **PathLengthPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml)檔案衍生自`InteractivePage`並包含觸控介面：

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

[ **PathLengthPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathLengthPage.xaml.cs)程式碼後置檔案，可讓您將四個觸控點，來定義結束點，以及控制三次方貝茲曲線的點。 三個欄位定義文字字串，`SKPaint`物件，並計算的寬度的文字：

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

`PaintSurface`處理常式會繪製貝茲曲線，然後調整大小以符合沿著其完整長度的文字：

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

`Length`屬性新建的`SKPathMeasure`物件取得路徑的長度。 這除以`baseTextWidth`值 （此為根據的文字大小 10 文字的寬度），並接著會乘以 10 的基底的文字大小。 結果會是新的文字大小來顯示文字沿該路徑：

[![](information-images/pathlength-small.png "路徑長度頁面的三個螢幕擷取畫面")](information-images/pathlength-large.png#lightbox "的路徑長度的頁面上的三個螢幕擷取畫面")

貝茲曲線越長或短，您可以看到文字大小變更。

## <a name="traversing-the-path"></a>周遊的路徑

`SKPathMeasure` 可以執行動作不只是量值的路徑長度。 任何介於 0 到路徑的長度值`SKPathMeasure`物件可以在該點取得的位置路徑，以及路徑曲線的正切值。 將正切函數可做為向量的形式`SKPoint`物件，或為旋轉封裝在`SKMatrix`物件。 以下是方法的`SKPathMeasure`，以各種且彈性的方式取得這項資訊：

```csharp
Boolean GetPosition (Single distance, out SKPoint position)

Boolean GetTangent (Single distance, out SKPoint tangent)

Boolean GetPositionAndTangent (Single distance, out SKPoint position, out SKPoint tangent)

Boolean GetMatrix (Single distance, out SKMatrix matrix, SKPathMeasureMatrixFlags flag)
```

[ `SKPathMeasureMatrixFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathMeasureMatrixFlags/)是：

- [`GetPosition`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPosition/)
- [`GetTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)
- [`GetPositionAndTangent`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathMeasureMatrixFlags.GetPositionAndTangent/)

**獨半管道**頁面上似乎寫來回沿著三次方貝茲曲線獨桿狀圖形以動畫方式顯示：

[![](information-images/unicyclehalfpipe-small.png "獨半管道頁面的三個螢幕擷取畫面")](information-images/unicyclehalfpipe-large.png#lightbox "獨半管道頁面的三個螢幕擷取畫面")

`SKPaint`用來繪製半管道和獨物件定義中的欄位為[ `UnicycleHalfPipePage` ]()類別。 也定義是`SKPath`獨物件：

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

這個類別包含的標準的覆寫`OnAppearing`和`OnDisappearing`動畫的方法。 `PaintSurface`處理常式建立半管道的路徑，然後繪製它。 `SKPathMeasure`物件然後建立根據這個路徑：

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

`PaintSurface`處理常式會計算值為`t`，是從 0 到 1 每五秒。 然後它會使用`Math.Cos`函式可將其轉換為`t`，範圍從 0 到 1 再回到 0，其中 0 相當於在左上方，開頭獨 1 對應到右上方獨時。 餘弦函數會導致要慢的排在管道的頂端和底部最快的速度。

請注意，這個值`t`必須乘以第一個引數的路徑長度`GetMatrix`。 矩陣會套用到`SKCanvas`繪製獨路徑的物件。

## <a name="enumerating-the-path"></a>列舉路徑

兩個內嵌的類別`SKPath`可讓您列舉路徑的內容。 這些類別是[ `SKPath.Iterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+Iterator/)和[ `SKPath.RawIterator` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath+RawIterator/)。 兩個類別是非常類似，但`SKPath.Iterator`可以排除長度為零，或接近零長度的路徑中的項目。 `RawIterator`下面的範例中使用。

您可以取得型別的物件`SKPath.RawIterator`藉由呼叫[ `CreateRawIterator` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.CreateRawIterator()/)方法`SKPath`。 列舉路徑透過重複呼叫[ `Next` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.Next/p/SkiaSharp.SKPoint[]/)方法。 傳遞給它的四個陣列`SKPoint`值：

```csharp
SKPoint[] points = new SKPoint[4];
...
SKPathVerb pathVerb = rawIterator.Next(points);
```

`Next`方法傳回的成員[ `SKPathVerb` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathVerb/)列舉型別。 這些值會指出特定的繪圖命令的路徑中。 在陣列中插入的有效點數目取決於此動詞命令：

- [`Move`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Move/) 與單一點
- [`Line`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Line/) 兩個指標
- [`Cubic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Cubic/) 具有四個點
- [`Quad`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Quad/) 具有三個點
- [`Conic`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Conic/) 具有三個點 (也呼叫[ `ConicWeight` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath+RawIterator.ConicWeight/)方法的權數)
- [`Close`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Close/) 與一個點
- [`Done`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathVerb.Done/)

`Done`動詞表示列舉已完成。

請注意，有任何`Arc`動詞命令。 這表示所有弧形會都轉換成貝茲曲線時加入至路徑。

中的部分資訊`SKPoint`是多餘的陣列。 例如，如果`Move`後面接著動詞`Line`動詞命令，接著隨附的兩個點的第一個`Line`相同`Move`點。 實際上，這種冗餘會很有幫助。 當您取得`Cubic`動詞命令，伴隨著所有四個控制點會定義三次方貝茲曲線。 您不需要保留先前的動詞命令所建立的目前位置。

有問題的動作，不過，是`Close`。 此命令從目前的位置繪製直線的稍早建立的分佈開頭`Move`命令。 在理想情況下，`Close`動詞命令應該提供這些兩個點，而不是一個點。 更糟的是在於點隨附`Close`動詞一定是 （0，0）。 這表示，當您透過路徑列舉時，您可能需要保留`Move`點和目前的位置。

靜態[ `PathExtensions` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathExtensions.cs)類別包含數種方法，將三種類型的貝茲曲線轉換成一系列的近似曲線的小直線。 (發行項中顯示的參數化的公式[**三種類型的貝茲曲線**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/beziers.md)。)`Interpolate`細分成許多較短的行就是只有一個單位的長度為直線的方法：

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

所有這些方法被參考的擴充方法`CloneWithTransform`如下所示。 這個方法會複製路徑列舉路徑命令，並建構資料為基礎的新路徑。 不過，新的路徑中只包含`MoveTo`和`LineTo`呼叫。 所有的曲線和直線縮減為一系列的小型線條。

呼叫時`CloneWithTransform`，傳遞至方法`Func<SKPoint, SKPoint>`，這是與函式`SKPaint`傳回參數`SKPoint`值。 每個點来套用自訂演算式轉換呼叫此函式：

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

複製的路徑會減少到小的直線，因為轉換函式會有轉換成曲線直線的功能。

請注意，此方法會保留第一個點的呼叫在變數中的每個分佈`firstPoint`和目前的位置之後每個, 變數中繪製命令`lastPoint`。 這些是所需要建構最後的結尾行何時`Close`遇到動詞命令。

**GlobularText**範例會使用此擴充方法看似繞半球 3D 作用中：

[![](information-images/globulartext-small.png "三個螢幕擷取畫面的 Globular 文字頁面")](information-images/globulartext-large.png#lightbox "Globular 文字頁面的三個螢幕擷取畫面")

[ `GlobularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/GlobularTextPage.cs)類別建構函式會執行這項轉換。 它會建立`SKPaint`物件的文字，並接著取得`SKPath`物件從`GetTextPath`方法。 這是傳遞至的路徑`CloneWithTransform`擴充方法，以及轉換函式： 

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

轉換函式會先計算名為兩個值`longitude`和`latitude`，範圍從-π/2 上方和左邊的文字，到 π/2 右側和底部的文字。 這些值的範圍不是以視覺化方式令人滿意的所以它們會乘以 0.75 減少。 （請嘗試這些調整沒有程式碼。 文字會變得太模糊的北部和南兩極，且太精簡側邊。）這些三維球形座標會轉換為二維`x`和`y`標準公式的座標。

新路徑儲存為欄位。 `PaintSurface`處理常式則只需要置中與縮放顯示在螢幕上的路徑：

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

這是非常靈活的技巧。 如果路徑效果的陣列中所述[**路徑效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章相當不能包含您認為應該包含的項目這是要填滿間距的方式。

## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
