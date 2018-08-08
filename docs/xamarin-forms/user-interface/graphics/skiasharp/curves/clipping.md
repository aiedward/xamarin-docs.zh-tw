---
title: 使用路徑和地區裁剪
description: 本文說明如何使用 SkiaSharp 剪輯圖形路徑特定區域，以及如何建立區域，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: charlespetzold
ms.author: chape
ms.date: 06/16/2017
ms.openlocfilehash: 0c07d68535349004eeefeaa18daa9c59b889a6a7
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615284"
---
# <a name="clipping-with-paths-and-regions"></a>使用路徑和地區裁剪

_使用路徑來剪輯圖形，以特定的區域，並建立區域_

它有時候是必要的圖形轉譯限於特定區域。 這就所謂*裁剪*。 您可以使用 裁剪的特殊效果，例如透過 keyhole 看到 monkey 的這個映像：

![](clipping-images/clippingsample.png "透過 keyhole monkey")

*裁剪區域*是在其中呈現圖形在螢幕的區域。 顯示裁剪區域之外的任何項目不會呈現。 的裁剪區域通常由定義[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)物件，但您可以另外定義裁剪區域，使用[ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/)物件。 這兩種類型的物件在第一次會看似相關，因為您可以建立區域路徑中。 不過，您無法建立路徑從區域，而且它們都是內部非常不同： 路徑包含一系列的線條和曲線，區域由一系列的水平的掃描行定義。

藉由建立上圖**Monkey 透過 Keyhole**頁面。 [ `MonkeyThroughKeyholePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)類別會定義使用 SVG 資料的路徑，並使用建構函式，從程式資源載入點陣圖：

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
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

雖然`keyholePath`物件描述 keyhole 的外框、 完全是任意的座標，以及反映路徑資料已設計時，項目很方便。 基於這個理由，`PaintSurface`處理常式會取得此路徑和呼叫界限`Translate`和`Scale`將路徑移至螢幕的中央，且能夠幾乎一樣高的畫面：


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

但不是會轉譯路徑。 相反地，下列轉換，路徑用來設定與這個陳述式的裁剪區域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`處理常式接著會重設的轉換，藉由呼叫`ResetMatrix`及繪製點陣圖，以延伸至全螢幕的高度。 這段程式碼會假設點陣圖是方形，也就是這個特定的點陣圖。 點陣圖為只在裁剪路徑所定義的區域內呈現：

[![](clipping-images/monkeythroughkeyhole-small.png "三重的螢幕擷取畫面的透過 Keyhole 頁面 Monkey")](clipping-images/monkeythroughkeyhole-large.png#lightbox "透過 Keyhole 頁面 Monkey 的三個螢幕擷取畫面")

裁剪路徑適用於進行轉換時`ClipPath`呼叫方法，而不轉換至作用中的圖形化的物件 （例如點陣圖） 顯示時。 裁剪路徑是以儲存畫布狀態的一部分`Save`方法和還原與`Restore`方法。

## <a name="combining-clipping-paths"></a>結合裁剪路徑

嚴格來說，裁剪區域不會 」 設定 「`ClipPath`方法。 相反地，它被結合現有的裁剪路徑，一開始畫面大小相等的矩形。 您可以取得的裁剪區域中使用的矩形界限[ `ClipBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipBounds/)屬性或有[ `ClipDeviceBounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.ClipDeviceBounds/)屬性。 `ClipBounds`屬性會傳回`SKRect`值，以反映任何轉換，可能是作用中。 `ClipDeviceBounds`屬性會傳回`RectI`值。 這是整數維度的矩形，並描述在實際的像素尺寸的裁剪區域。

呼叫`ClipPath`減少藉由結合新的區域的裁剪區域的裁剪區域。 完整語法[ `ClipPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipPath/p/SkiaSharp.SKPath/SkiaSharp.SKClipOperation/System.Boolean/)方法是：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

另外還有[ `ClipRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRect/p/SkiaSharp.SKRect/SkiaSharp.SKClipOperation/System.Boolean/)結合矩形中的裁剪區域的方法：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

根據預設，結果的裁剪區域是現有的裁剪區域的交集，`SKPath`或是`SKRect`中指定`ClipPath`或`ClipRect`方法。 這示範於**四個圓形交集剪輯**頁面。 `PaintSurface`中的處理常式[ `FourCircleInteresectClipPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs)類別會重複使用相同`SKPath`物件來建立四個重疊圓形，其中每一個可減少透過後續呼叫的裁剪區域`ClipPath`:

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

剩下的是下列四個社交圈的交集：

[![](clipping-images//fourcircleintersectclip-small.png "四個圓形交集剪輯頁面的三個螢幕擷取畫面")](clipping-images/fourcircleintersectclip-large.png#lightbox "的四個圓形交集剪輯頁面的三個螢幕擷取畫面")

[ `SKClipOperation` ](https://developer.xamarin.com/api/type/SkiaSharp.SKClipOperation/)列舉型別有只有兩個成員：

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Difference/) 從現有的裁剪區域中移除指定的路徑或矩形

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKClipOperation.Intersect/) 與指定的路徑或使用現有的裁剪區域的矩形的交集

如果您將四個`SKClipOperation.Intersect`中的引數`FourCircleIntersectClipPage`類別`SKClipOperation.Difference`，您會看到下列訊息：

[![](clipping-images//fourcircledifferenceclip-small.png "差異作業具有四個圓形交集剪輯頁面三個螢幕擷取畫面")](clipping-images/fourcircledifferenceclip-large.png#lightbox "差異運算的四個圓形交集剪輯頁面的三個螢幕擷取畫面")

已從裁剪區域設定了四個重疊圓形。

**剪輯作業**頁面說明這兩項作業只是一組的圓形之間的差異。 在左側的第一個圓圈會新增至預設剪輯作業的裁剪區域`Intersect`，而右邊的第二個圓圈會新增至裁剪區域的文字標籤所指定的裁剪作業：

[![](clipping-images//clipoperations-small.png "剪輯作業頁面的三個螢幕擷取畫面")](clipping-images/clipoperations-large.png#lightbox "剪輯作業頁面的三個螢幕擷取畫面")

[ `ClipOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs)類別會定義兩個`SKPaint`物件做為欄位，並再將螢幕分成兩個矩形區域。 這些區域是行動電話是否處於直向或橫向模式而有所不同。 `DisplayClipOp`類別，然後顯示的文字和呼叫`ClipPath`來說明每一個剪輯作業的兩個圓形路徑：

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

呼叫`DrawPaint`通常會導致的填滿整個畫布`SKPaint`物件，但在此情況下，方法就會繪製的裁剪區域中。

## <a name="exploring-regions"></a>瀏覽區域

如果您已探索的 API 文件`SKCanvas`，您可能已經注意到的多載`ClipPath`並`ClipRect`但上面所述的方法類似的方法有一個名為參數[ `SKRegionOperation`](https://developer.xamarin.com/api/type/SkiaSharp.SKRegionOperation/)而非`SKClipOperation`。 `SKRegionOperation` 有六個成員，提供更多彈性結合形式裁剪區域路徑：

- [`Difference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Difference/)

- [`Intersect`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Intersect/)

- [`Union`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Union/)

- [`XOR`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.XOR/)

- [`ReverseDifference`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.ReverseDifference/)

- [`Replace`](https://developer.xamarin.com/api/field/SkiaSharp.SKRegionOperation.Replace/)

不過，多載`ClipPath`並`ClipRect`與`SKRegionOperation`參數已經過時，而且不能用。

您仍然可以使用`SKRegionOperation`列舉型別，但它會要求您定義的裁剪區域[ `SKRegion` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRegion/)物件。

新建立`SKRegion`物件描述的空白區域。 通常是第一個物件上的呼叫[ `SetRect` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetRect/p/SkiaSharp.SKRectI/)以便區域描述矩形的區域。 參數`SetRect`已`SKRectI`值&mdash;矩形值與整數屬性。 您可以接著呼叫[ `SetPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.SetPath/p/SkiaSharp.SKPath/SkiaSharp.SKRegion/)使用`SKPath`物件。 這會建立內部的路徑相同，但裁剪到初始的矩形區域的區域。

`SKRegionOperation`列舉型別只派上用場時您呼叫其中一個[ `Op` ](https://developer.xamarin.com/api/member/SkiaSharp.SKRegion.Op/p/SkiaSharp.SKRegion/SkiaSharp.SKRegionOperation/)方法多載，這類：

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

您正在使用的區域`Op`上的呼叫會結合為根據參數所指定的地區`SKRegionOperation`成員。 當您最後取得區域適用於裁剪時，則您可以設定為畫布使用裁剪區域[ `ClipRegion` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ClipRegion/p/SkiaSharp.SKRegion/SkiaSharp.SKClipOperation/)方法`SKCanvas`:

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

下列螢幕擷取畫面顯示六個區域的作業為基礎的裁剪區域。 左邊的圓形是區域所`Op`上，呼叫方法，而正確的圓形是傳遞至的區域`Op`方法：

[![](clipping-images//regionoperations-small.png "區域操作頁面的三個螢幕擷取畫面")](clipping-images/regionoperations-large.png#lightbox "區域操作頁面的三個螢幕擷取畫面")

是這些所有結合這些兩個圓形的可能性？ 產生的映像視為其本身中的三個元件，組合`Difference`， `Intersect`，和`ReverseDifference`作業。 組合的總數是兩到三次方，或是 8 個。 遺失的兩個是原始區域 (這會促使不呼叫`Op`完全) 和完全空白的區域。

是很難用於因為您必須先從該路徑中，建立 路徑，然後按一下 區域，然後再結合多個區域的裁剪區域。 整體結構**區域操作**頁面是非常類似於**剪輯作業**但[ `RegionOperationsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs)類別將畫面，分成六個區域和顯示額外的工作，才能使用此工作的區域：

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

以下是 最大的差別`ClipPath`方法和`ClipRegion`方法：

> [!IMPORTANT]
> 不同於`ClipPath`方法，`ClipRegion`方法不會受到轉換。

若要了解這項差異的基本原理，最好先了解哪些區域。 如果您考慮如何剪輯作業或區域操作可能會在內部實作，它可能看起來很複雜。 正在合併數個可能非常複雜的路徑，以及外框的結果的路徑可能是演算法的夢魘。

但是，如果每個路徑會縮減為一系列的水平的掃描線，例如舊式真空 tube 電視中大幅簡化此作業。 每一條掃描線是只要一條水平線起始點與結束點。 比方說，圓形半徑為 10 能分解成 20 的水平的掃描行，其中每一個位於圓形的左側部分在開始和結束的右側部分。 結合兩個圓形區域中的任何作業變成非常簡單，因為它是簡單的檢查對應的掃描線的每個組的開始和結束座標。

這是功能區域： 一系列的水平的掃描線，定義一個區域。

不過，當區域會縮減為一系列的掃描程式行，這些線條根據特定的像素尺寸的掃描。 嚴格來說，區域不是向量圖形物件。 很接近在本質上比路徑壓縮單色點陣圖。 因此，區域無法縮放或旋轉而不會遺失精確度，並因此不會轉換時用來裁剪區域。

不過，您可以將轉換套用到進行繪製的區域。 **區域小畫家**程式生動示範的區域內部的本質。 [ `RegionPaintPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)類別會建立`SKRegion`物件根據`SKPath`10 單位 radius 圓形。 轉換，然後展開該圓形來填滿頁面：

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

`DrawRegion`呼叫會填滿的區域以橘色表示，雖然`DrawPath`呼叫以進行比較的藍色 strokes 的原始路徑：

[![](clipping-images//regionpaint-small.png "區域 [小畫家] 頁面的三個螢幕擷取畫面")](clipping-images/regionpaint-large.png#lightbox "區域 [小畫家] 頁面的三個螢幕擷取畫面")

區域是一系列的不連續的座標。

如果您不需要使用與您的裁剪區域的轉換，您可以為剪輯，使用區域**四個-很**頁面會示範。 [ `FourLeafCloverPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)類別建構的複合區域從四個循環的區域、 裁剪區域中，以設定該複合的區域，然後繪製以一系列的 360 動畫從頁面中央的直線，線條：

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

它真的看起來不像四個-很，但可能難以呈現而不裁剪的影像：

[![](clipping-images//fourleafclover-small.png "四個-很頁面的三個螢幕擷取畫面")](clipping-images/fourleafclover-large.png#lightbox "的四個-很頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
