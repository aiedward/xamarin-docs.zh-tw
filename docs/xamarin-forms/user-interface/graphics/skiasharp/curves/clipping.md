---
title: 使用路徑和區域裁剪
description: 本文說明如何使用 SkiaSharp 路徑將圖形裁剪到特定區域，以及建立區域，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 79c32595053cc80ffae91b5434e18690ca2dce4a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370711"
---
# <a name="clipping-with-paths-and-regions"></a>使用路徑和區域裁剪

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用路徑將圖形裁剪至特定區域，以及建立區域_

有時必須將圖形轉譯限制為特定區域。 這稱為「 *裁剪* 」。 您可以使用裁剪來取得特殊效果，例如透過鑰匙孔看到的猴子影像：

![鑰匙孔的猴子](clipping-images/clippingsample.png)

*裁剪區域* 是呈現圖形的螢幕區域。 在裁剪區域以外顯示的任何部分都不會呈現。 裁剪區域通常是由矩形或物件所定義 [`SKPath`](xref:SkiaSharp.SKPath) ，但您也可以使用物件來定義裁剪區域 [`SKRegion`](xref:SkiaSharp.SKRegion) 。 這兩種類型的物件一開始似乎是相關的，因為您可以從路徑建立區域。 不過，您無法從區域建立路徑，而且它們會在內部非常不同：路徑包含一連串的線條和曲線，而區域是由一系列的水準掃描線條所定義。

上圖是 **透過鑰匙孔** 頁面建立的猴子。 [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)類別會使用 SVG 資料定義路徑，並使用此函式從程式資源載入點陣圖：

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

雖然 `keyholePath` 物件描述的是鑰匙孔的外框，但座標完全是任意的，而且會反映在設計路徑資料時的方便。 基於這個理由， `PaintSurface` 處理常式會取得此路徑的界限並呼叫 `Translate` ，並 `Scale` 將路徑移至畫面的中央，使其幾乎與螢幕高度一樣：

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

但不會轉譯路徑。 相反地，在轉換之後，路徑會用來設定具有下列語句的裁剪區域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`然後，處理常式會使用的呼叫重設轉換 `ResetMatrix` ，並繪製點陣圖以延伸至螢幕的完整高度。 這段程式碼假設點陣圖是正方形，這是這個特殊的點陣圖。 點陣圖只會在裁剪路徑所定義的區域內轉譯：

[![透過鑰匙孔頁面的三重螢幕擷取畫面](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

當呼叫方法時，裁剪路徑會受到作用中的轉換 `ClipPath` ，而不是在繪圖物件 (（例如點陣圖) ）時生效的轉換。 裁剪路徑是以 `Save` 方法儲存並使用方法還原之畫布狀態的一部分 `Restore` 。

## <a name="combining-clipping-paths"></a>合併裁剪路徑

嚴格來說，方法不會「設定」裁剪區域 `ClipPath` 。 相反地，它會與現有的裁剪路徑結合，它是以大小等於畫布的矩形來開始。 您可以使用屬性或屬性來取得裁剪區域的矩形界限 [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) 。 屬性會傳回可 `ClipBounds` `SKRect` 反映任何可能作用中轉換的值。 屬性會傳回 `ClipDeviceBounds` `RectI` 值。 這是具有整數維度的矩形，並且描述實際圖元維度中的裁剪區域。

藉 `ClipPath` 由合併裁剪區域與新區域來減少裁剪區域的任何呼叫。 方法的完整語法 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) 為：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

此外，也有一種 [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) 方法可結合裁剪區域與矩形：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

根據預設，產生的裁剪區域是在 `SKPath` `SKRect` 或方法中指定之現有裁剪區域和或的交集 `ClipPath` `ClipRect` 。 這會在 **四個圓圈交集剪輯** 頁面中示範。 `PaintSurface`類別中的處理常式會 [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) 重複使用相同的 `SKPath` 物件來建立四個重迭的圓形，其中每個圓形都會透過連續呼叫來減少裁剪區域 `ClipPath` ：

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

剩下的就是這四個圓形的交集：

[![四個圓形交集剪輯頁面的三重螢幕擷取畫面](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

[`SKClipOperation`](xref:SkiaSharp.SKClipOperation)列舉只有兩個成員：

- `Difference` 從現有的裁剪區域移除指定的路徑或矩形

- `Intersect` 與指定的路徑或矩形與現有的裁剪區域相交

如果您使用取代類別中的四個 `SKClipOperation.Intersect` 引數 `FourCircleIntersectClipPage` `SKClipOperation.Difference` ，您將會看到下列內容：

[![具有差異作業之四個圓形相交剪輯頁面的三重螢幕擷取畫面](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

已從裁剪區域移除四個重迭的圓形。

[ **剪輯操作** ] 頁面說明這兩個作業之間的差異，只是一對圓形。 左邊的第一個圓形會加入至具有預設剪輯作業的裁剪區域 `Intersect` 中，而右邊的第二個圓圈則會新增至裁剪區域，其中包含由文字標籤指出的剪輯作業：

[![[剪輯操作] 頁面的三重螢幕擷取畫面](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

類別會將 [`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) 兩個 `SKPaint` 物件定義為欄位，然後將畫面分割成兩個矩形區域。 這些區域會因電話為直向或橫向模式而有所不同。 `DisplayClipOp`然後，類別會顯示文字，然後 `ClipPath` 使用兩個圓形路徑進行呼叫，以說明每個剪輯操作：

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

呼叫 `DrawPaint` 通常會讓整個畫布以該物件填滿 `SKPaint` ，但在此情況下，方法只會在裁剪區域內繪製。

## <a name="exploring-regions"></a>探索區域

您也可以根據物件來定義裁剪區域 [`SKRegion`](xref:SkiaSharp.SKRegion) 。

新建立的 `SKRegion` 物件會描述空白區域。 通常物件的第一個呼叫是 [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) 讓區域描述矩形區域。 的參數 `SetRect` 是 `SKRectI` &mdash; 具有整數座標的矩形值，因為它會以圖元為單位來指定矩形。 然後，您可以 [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) 使用物件來呼叫 `SKPath` 。 這會建立與路徑內部相同但已裁剪至初始矩形區域的區域。

您也可以藉由呼叫其中一個方法多載來修改區域 [`Op`](xref:SkiaSharp.SKRegion.Op*) ，例如：

```csharp
public Boolean Op(SKRegion region, SKRegionOperation op)
```

[`SKRegionOperation`](xref:SkiaSharp.SKRegionOperation)列舉類似于， `SKClipOperation` 但有更多成員：

- `Difference`

- `Intersect`

- `Union`

- `XOR`

- `ReverseDifference`

- `Replace`

您正在進行呼叫的區域 `Op` 會與指定為以該成員為基礎之參數的區域結合 `SKRegionOperation` 。 當您最後取得適合剪切的區域時，您可以使用的方法，將其設定為畫布的裁剪區域 [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) `SKCanvas` ：

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

下列螢幕擷取畫面顯示以六個區域作業為基礎的裁剪區域。 左邊的圓形是呼叫方法的區域 `Op` ，右邊的圓形則是傳遞給方法的區域 `Op` ：

[![[區域操作] 頁面的三重螢幕擷取畫面](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

這些是結合這兩個圓形的可能性嗎？ 請將結果影像視為三個元件的組合，而這些元件本身會在 `Difference` 、 `Intersect` 和作業中看到 `ReverseDifference` 。 組合總數是第三個乘冪的2個，或八個。 遺漏的兩個是原始區域 (因為未 `Op` 在所有) 和完全空白的區域中呼叫所產生的結果。

因為您需要先建立路徑，然後再從該路徑建立區域，然後結合多個區域，所以難以使用區域進行裁剪。 [ **區域作業** ] 頁面的整體結構與 **剪輯作業** 非常類似，但類別會將 [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) 螢幕分割為六個區域，並顯示為此工作使用區域所需的額外工作：

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

以下是方法與方法之間的大差異 `ClipPath` `ClipRegion` ：

> [!IMPORTANT]
> 不同于 `ClipPath` 方法， `ClipRegion` 方法不會受轉換影響。

若要瞭解這項差異的原理，請務必瞭解區域的意義。 如果您已經思考過如何在內部執行剪輯作業或區域作業，可能看起來很複雜。 可能會結合數個可能非常複雜的路徑，而結果路徑的外框可能會造成演算法的麻煩。

如果每個路徑都縮減成一連串的水準掃描行（例如舊式的全像電視），這項作業就會大幅簡化。 每個掃描行都只是一條具有起點和終點的水平線。 例如，半徑為10圖元的圓形可以分解成20個水準掃描行，每個都從圓形的左邊部分開始，然後結束于右邊的部分。 將兩個圓圈與任何區域作業結合，會變得非常簡單，因為它只是檢查每一對對應掃描行的開始和結束座標。

區域為：定義區域的一連串水準掃描線條。

但是，當區域縮減為一系列的掃描線時，這些掃描行會以特定的圖元維度為基礎。 嚴格來說，區域不是向量繪圖物件。 它的本質與壓縮的單色點陣圖更接近路徑。 因此，區域無法調整或旋轉，而不會遺失精確度，因此在用於裁剪區域時不會轉換它們。

不過，您可以將轉換套用至區域以供繪製之用。 **區域繪圖** 程式生動示範區域的內部本質。 [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)類別會 `SKRegion` 根據10個 `SKPath` 單位半徑圓形的來建立物件。 接著，轉換會展開該圓形以填滿頁面：

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

`DrawRegion`呼叫會以橙色填滿區域，而 `DrawPath` 呼叫會以藍色的原始路徑進行比較以進行比較：

[![區域油漆頁面的三重螢幕擷取畫面](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

區域顯然是一系列的離散座標。

如果您不需要在與裁剪區域連接時使用轉換，您可以使用區域進行裁剪，如 **四個分葉四葉草** 頁面所示。 [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)類別會從四個迴圈區域來建立複合區域、將該複合區域設定為裁剪區域，然後從頁面中央繪製一連串的360直線 mouseleave：

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

它其實看起來不像四分葉四葉草，但是它是一個可能難以轉譯而不需要裁剪的影像：

[![Four-Leaf 四葉草頁面的三重螢幕擷取畫面](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)