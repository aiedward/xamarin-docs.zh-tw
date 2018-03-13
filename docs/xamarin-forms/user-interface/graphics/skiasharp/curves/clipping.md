---
title: "使用路徑和地區的裁剪部分"
description: "特定領域，以及建立區域使用美工圖形的路徑"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: bb99984f93f494cfb5ad3d37ccb25f0b91d0b489
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="clipping-with-paths-and-regions"></a>使用路徑和地區的裁剪部分

_特定領域，以及建立區域使用美工圖形的路徑_

有時候是必要的圖形轉譯限制到特定區域。 這稱為*裁剪*。 您可以使用 裁剪特殊效果，例如透過 keyhole 看到猴子此映像：

![](clipping-images/clippingsample.png "透過 keyhole 猴子")

*的裁剪區域*會轉譯圖形之螢幕的區域。 不會呈現任何裁剪區域外部顯示的項目。 裁剪區域通常由定義[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)物件，但您可以另外定義裁剪區域，使用[ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/)物件。 因為您可以建立區域路徑中這兩種類型的物件，在第一次似乎相關。 不過，您無法建立路徑的地區，而且它們在內部是非常不同： 路徑包含一系列直線和曲線，，而在區域由一系列的水平掃描行所定義。

上面的影像由**透過 Keyhole 猴子**頁面。 [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)類別定義使用 SVG 資料路徑，並使用建構函式從程式資源載入點陣圖：

```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    SKBitmap bitmap;
    SKPath keyholePath = SKPath.ParseSvgPathData(
        "M 300 130 L 250 350 L 450 350 L 400 130 A 70 70 0 1 0 300 130 Z");

    public MonkeyThroughKeyholePage()
    {
        Title = "Monkey through Keyhole";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
        }
    }
    ...
}
```

雖然`keyholePath`物件描述的 keyhole 外框，完全是任意的座標，以及反映路徑資料已也設計時，什麼很方便。 基於這個理由，`PaintSurface`處理常式會取得此路徑，並呼叫界限`Translate`和`Scale`移動路徑螢幕的中央，且能夠幾乎一樣高螢幕：


```csharp
public class MonkeyThroughKeyholePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transform to center and enlarge clip path to window height
        SKRect bounds;
        keyholePath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.98f * info.Height / bounds.Height);
        canvas.Translate(-bounds.MidX, -bounds.MidY);

        // Set the clip path
        canvas.ClipPath(keyholePath);

        // Reset transforms
        canvas.ResetMatrix();

        // Display monkey to fill height of window but maintain aspect ratio
        canvas.DrawBitmap(bitmap,
            new SKRect((info.Width - info.Height) / 2, 0,
                       (info.Width + info.Height) / 2, info.Height));
    }
}
```

但不是會轉譯路徑。 相反地，下列轉換，路徑用來設定此陳述式的裁剪區域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`處理常式則重設呼叫轉換`ResetMatrix`，繪製點陣圖延伸到全螢幕的高度。 此程式碼會假設點陣圖是方形，也就是這類點陣圖。 只有在裁剪路徑所定義的區域內呈現點陣圖：

[![](clipping-images/monkeythroughkeyhole-small.png "透過 Keyhole 頁面猴子的三個螢幕擷取畫面")](clipping-images/monkeythroughkeyhole-large.png#lightbox "透過 Keyhole 頁面猴子的三個螢幕擷取畫面")

裁剪路徑是在轉換受限於作用中時`ClipPath`呼叫方法時，並不到轉換作用中時的圖形化物件 （例如點陣圖） 顯示。 裁剪路徑是以儲存畫布狀態的一部分`Save`方法和還原與`Restore`方法。

## <a name="combining-clipping-paths"></a>結合裁剪路徑

嚴格來說，裁剪區域未 」 設定 「`ClipPath`方法。 相反地，它會結合現有的裁剪路徑開始為和螢幕大小相等的矩形。 您可以取得的裁剪區域中使用的矩形界限[ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/)屬性或[ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/)屬性。 `ClipBounds`屬性會傳回`SKRect`值，反映任何轉換，可能會生效。 `ClipDeviceBounds`屬性會傳回`RectI`值。 這是整數維度的矩形，並描述實際的像素維度中的裁剪區域。

呼叫`ClipPath`減少結合與新區域的裁剪區域的裁剪區域。 完整語法[ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/)方法是：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

另外還有[ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/)結合矩形的裁剪區域的方法：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

根據預設，結果的裁剪區域是現有的裁剪區域交集和`SKPath`或`SKRect`中指定`ClipPath`或`ClipRect`方法。 這示範於**四個圓形交集剪輯**頁面。 `PaintSurface`中的處理常式[ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs)類別重複使用相同`SKPath`物件來建立四個重疊圓形，其中每個可減少透過後續呼叫的裁剪區域`ClipPath`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float size = Math.Min(info.Width, info.Height);
    float radius = 0.4f * size;
    float offset = size / 2 - radius;

    // Translate to center
    canvas.Translate(info.Width / 2, info.Height / 2);

    using (SKPath path = new SKPath())
    {
        path.AddCircle(-offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(-offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, -offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        path.Reset();
        path.AddCircle(offset, offset, radius);
        canvas.ClipPath(path, SKClipOperation.Intersect);

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColors.Blue;
            canvas.DrawPaint(paint);
        }
    }
}
```

什麼是這些四個圓圈的交集：

[![](clipping-images//fourcircleintersectclip-small.png "三個螢幕擷取畫面的四個圓形交集剪輯頁面")](clipping-images/fourcircleintersectclip-large.png#lightbox "的四個圓形交集剪輯頁面的三個螢幕擷取畫面")

[ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/)列舉型別的只有兩個成員：

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) 從現有的裁剪區域中移除指定的路徑或矩形

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) 與指定的路徑或使用現有的裁剪區域的矩形的交集

如果您取代四個`SKClipOperation.Intersect`中的引數`FourCircleIntersectClipPage`類別`SKClipOperation.Difference`，您會看到下列訊息：

[![](clipping-images//fourcircledifferenceclip-small.png "差異作業四個圓形交集剪輯頁面的三個螢幕擷取畫面")](clipping-images/fourcircledifferenceclip-large.png#lightbox "四個圓形交集剪輯頁面差異作業的三個螢幕擷取畫面")

已從裁剪區域設定了四個重疊圓形。

**裁剪作業**頁面說明這兩項操作以只對圓形之間的差異。 在左側的第一個圓形會加入至預設裁剪作業的裁剪區域`Intersect`，而右邊的第二個圓圈會指示的文字標籤裁剪作業一起新增至裁剪區域：

[![](clipping-images//clipoperations-small.png "[裁剪作業] 頁面的三個螢幕擷取畫面")](clipping-images/clipoperations-large.png#lightbox "裁剪作業 頁面的三個螢幕擷取畫面")

[ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs)類別會定義兩個`SKPaint`物件做為欄位，然後將螢幕分成兩個矩形區域。 這些區域是電話是否處於直向或橫向模式而有所不同。 `DisplayClipOp`類別，然後顯示的文字和呼叫`ClipPath`與兩個圓形路徑來說明每一個剪輯作業：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;

    foreach (SKClipOperation clipOp in Enum.GetValues(typeof(SKClipOperation)))
    {
        // Portrait mode
        if (info.Height > info.Width)
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width, y + info.Height / 2), clipOp);
            y += info.Height / 2;
        }
        // Landscape mode
        else
        {
            DisplayClipOp(canvas, new SKRect(x, y, x + info.Width / 2, y + info.Height), clipOp);
            x += info.Width / 2;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKClipOperation clipOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(clipOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    canvas.Save();

    using (SKPath path1 = new SKPath())
    {
        path1.AddCircle(xCenter - radius / 2, yCenter, radius);
        canvas.ClipPath(path1);

        using (SKPath path2 = new SKPath())
        {
            path2.AddCircle(xCenter + radius / 2, yCenter, radius);
            canvas.ClipPath(path2, clipOp);

            canvas.DrawPaint(fillPaint);
        }
    }

    canvas.Restore();
}
```

呼叫`DrawPaint`通常會導致要的填滿整張畫布`SKPaint`物件，但在此情況下，方法就會繪製的裁剪區域中。

## <a name="exploring-regions"></a>瀏覽區域

如果您有探索的 API 文件`SKCanvas`，您可能已注意到的多載`ClipPath`和`ClipRect`但相反地，上述的方法類似的方法有名稱為的參數[ `SKRegionOperation`](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/)而不是`SKClipOperation`。 `SKRegionOperation` 有六個成員，提供稍微更具彈性的結合路徑形式的裁剪區域：

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

不過，多載`ClipPath`和`ClipRect`與`SKRegionOperation`參數已經過時，而且無法使用。

您仍然可以使用`SKRegionOperation`列舉型別，但它會要求您定義的裁剪區域[ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/)物件。

新建立`SKRegion`物件描述的空白區域。 通常是第一個物件上的呼叫[ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/)使區域描述矩形區域。 參數以`SetRect`是`SKRectI`值 & #x 2014年; 矩形值與整數屬性。 您可以接著呼叫[ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/)與`SKPath`物件。 這會建立內部的路徑相同，但裁剪至初始的矩形區域的區域。

`SKRegionOperation`列舉只派上用場時呼叫其中一種[ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/)方法多載，這種：

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

您所做的區域`Op`上的呼叫結合做為參數，根據指定的地區`SKRegionOperation`成員。 當您最後一個區域適用於裁剪，您可以將其設定為使用畫布的裁剪區域[ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/)方法`SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

下列螢幕擷取畫面顯示六個區域作業為基礎的裁剪區域。 左邊的圓形是地區，`Op`上，呼叫方法，而右邊的圓形是傳遞至該區域`Op`方法：

[![](clipping-images//regionoperations-small.png "區域操作頁面的三個螢幕擷取畫面")](clipping-images/regionoperations-large.png#lightbox "區域操作頁面的三個螢幕擷取畫面")

是這些所有可能性結合這些兩個圓形的嗎？ 產生的影像中視為組合的三個元件，會出現在它自己本身全部放`Difference`， `Intersect`，和`ReverseDifference`作業。 第三方，兩個或 8 個組合的總數。 遺失兩個是原始的地區 (因而不呼叫`Op`完全) 與完全空白的區域。

它是很難使用區域來裁剪，因為您必須先建立該路徑，請從的路徑，然後按一下 區域，然後再將合併多個區域。 整體結構**區域操作**頁面是非常類似於**裁剪作業**但[ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs)類別分成螢幕六個區域和顯示要用於此工作的區域所需的額外工作：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float x = 0;
    float y = 0;
    float width = info.Height > info.Width ? info.Width / 2 : info.Width / 3;
    float height = info.Height > info.Width ? info.Height / 3 : info.Height / 2;

    foreach (SKRegionOperation regionOp in Enum.GetValues(typeof(SKRegionOperation)))
    {
        DisplayClipOp(canvas, new SKRect(x, y, x + width, y + height), regionOp);

        if ((x += width) >= info.Width)
        {
            x = 0;
            y += height;
        }
    }
}

void DisplayClipOp(SKCanvas canvas, SKRect rect, SKRegionOperation regionOp)
{
    float textSize = textPaint.TextSize;
    canvas.DrawText(regionOp.ToString(), rect.MidX, rect.Top + textSize, textPaint);
    rect.Top += textSize;

    float radius = 0.9f * Math.Min(rect.Width / 3, rect.Height / 2);
    float xCenter = rect.MidX;
    float yCenter = rect.MidY;

    SKRectI recti = new SKRectI((int)rect.Left, (int)rect.Top,
                                (int)rect.Right, (int)rect.Bottom);

    using (SKRegion wholeRectRegion = new SKRegion())
    {
        wholeRectRegion.SetRect(recti);

        using (SKRegion region1 = new SKRegion(wholeRectRegion))
        using (SKRegion region2 = new SKRegion(wholeRectRegion))
        {
            using (SKPath path1 = new SKPath())
            {
                path1.AddCircle(xCenter - radius / 2, yCenter, radius);
                region1.SetPath(path1);
            }

            using (SKPath path2 = new SKPath())
            {
                path2.AddCircle(xCenter + radius / 2, yCenter, radius);
                region2.SetPath(path2);
            }

            region1.Op(region2, regionOp);

            canvas.Save();
            canvas.ClipRegion(region1);
            canvas.DrawPaint(fillPaint);
            canvas.Restore();
        }
    }
}
```

以下是大差異`ClipPath`方法和`ClipRegion`方法：

> [!IMPORTANT]
> 不同於`ClipPath`方法，`ClipRegion`方法不會受到轉換。

若要了解此差異的基本原理，最好先了解哪些區域。 如果您已仔細考慮有關如何裁剪作業或區域操作可能會在內部實作，它可能似乎很複雜。 組合可能非常複雜的多個路徑時，並可能是因為演算法惡夢外框的結果的路徑。

但是，如果每個路徑已縮減為一系列的水平掃描線，例如舊式個 tube 電視中大幅簡化此作業。 每個掃描線是只要水平線起始點與結束點。 例如，圓形半徑為 10 能分解成 20 水平掃描行，其中每個在左邊的圓形在開始和結束的右側部分。 結合兩個圓形與任何區域作業變成非常簡單，因為它是簡單的檢查對應的掃描線之每對的開始和結束的座標。

這是什麼區域是： 定義區域的水平掃描行一連串。

不過，當區域已縮減為一系列的掃描程式碼行，這些線條根據特定像素維度的掃描。 嚴格來說，區域不是向量圖形物件。 很接近在本質上一個壓縮的單色點陣圖比的路徑。 因此，區域無法縮放或旋轉而不會遺失精確度，並因此不會轉換時使用的裁剪區域。

不過，您可以將轉換套用到進行繪製的區域。 **區域小畫家**程式 vividly 示範的區域內部的本質。 [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)類別會建立`SKRegion`物件根據`SKPath`10 單元 radius 圓形。 轉型，然後展開該圓形來填滿頁面：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int radius = 10;

    // Create circular path
    using (SKPath circlePath = new SKPath())
    {
        circlePath.AddCircle(0, 0, radius);

        // Create circular region
        using (SKRegion circleRegion = new SKRegion())
        {
            circleRegion.SetRect(new SKRectI(-radius, -radius, radius, radius));
            circleRegion.SetPath(circlePath);

            // Set transform to move it to center and scale up
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(Math.Min(info.Width / 2, info.Height / 2) / radius);

            // Fill region
            using (SKPaint fillPaint = new SKPaint())
            {
                fillPaint.Style = SKPaintStyle.Fill;
                fillPaint.Color = SKColors.Orange;

                canvas.DrawRegion(circleRegion, fillPaint);
            }

            // Stroke path for comparison
            using (SKPaint strokePaint = new SKPaint())
            {
                strokePaint.Style = SKPaintStyle.Stroke;
                strokePaint.Color = SKColors.Blue;
                strokePaint.StrokeWidth = 0.1f;

                canvas.DrawPath(circlePath, strokePaint);
            }
        }
    }
}
```

`DrawRegion`呼叫填滿的區域以橘色表示，雖然`DrawPath`呼叫 strokes 的原始路徑，以藍色進行比較：

[![](clipping-images//regionpaint-small.png "區域 [小畫家] 頁面的三個螢幕擷取畫面")](clipping-images/regionpaint-large.png#lightbox "區域 [小畫家] 頁面的三個螢幕擷取畫面")

區域顯然是一系列的不連續的座標。

如果您不需要使用轉換之裁剪區域，您可以使用區域的剪輯，做**四-分葉苜蓿**頁面示範。 [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)類別建構複合區域從四個循環的區域、 裁剪區域中，為設定該複合的區域，然後繪製以一系列的 360 直線 emanating 從頁面的中央：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float xCenter = info.Width / 2;
    float yCenter = info.Height / 2;
    float radius = 0.24f * Math.Min(info.Width, info.Height);

    using (SKRegion wholeScreenRegion = new SKRegion())
    {
        wholeScreenRegion.SetRect(new SKRectI(0, 0, info.Width, info.Height));

        using (SKRegion leftRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion rightRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion topRegion = new SKRegion(wholeScreenRegion))
        using (SKRegion bottomRegion = new SKRegion(wholeScreenRegion))
        {
            using (SKPath circlePath = new SKPath())
            {
                // Make basic circle path
                circlePath.AddCircle(xCenter, yCenter, radius);

                // Left leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, 0));
                leftRegion.SetPath(circlePath);

                // Right leaf
                circlePath.Transform(SKMatrix.MakeTranslation(2 * radius, 0));
                rightRegion.SetPath(circlePath);

                // Make union of right with left
                leftRegion.Op(rightRegion, SKRegionOperation.Union);

                // Top leaf
                circlePath.Transform(SKMatrix.MakeTranslation(-radius, -radius));
                topRegion.SetPath(circlePath);

                // Combine with bottom leaf
                circlePath.Transform(SKMatrix.MakeTranslation(0, 2 * radius));
                bottomRegion.SetPath(circlePath);

                // Make union of top with bottom
                bottomRegion.Op(topRegion, SKRegionOperation.Union);

                // Exclusive-OR left and right with top and bottom
                leftRegion.Op(bottomRegion, SKRegionOperation.XOR);

                // Set that as clip region
                canvas.ClipRegion(leftRegion);

                // Set transform for drawing lines from center
                canvas.Translate(xCenter, yCenter);

                // Draw 360 lines
                for (double angle = 0; angle < 360; angle++)
                {
                    float x = 2 * radius * (float)Math.Cos(Math.PI * angle / 180);
                    float y = 2 * radius * (float)Math.Sin(Math.PI * angle / 180);

                    using (SKPaint strokePaint = new SKPaint())
                    {
                        strokePaint.Color = SKColors.Green;
                        strokePaint.StrokeWidth = 2;

                        canvas.DrawLine(0, 0, x, y, strokePaint);
                    }
                }
            }
        }
    }
}
```

它實際上不像是四-分葉苜蓿 」，但是可能會難以呈現，而不裁剪影像：

[![](clipping-images//fourleafclover-small.png "三個螢幕擷取畫面的四個-分葉苜蓿頁面")](clipping-images/fourleafclover-large.png#lightbox "四-分葉苜蓿頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
