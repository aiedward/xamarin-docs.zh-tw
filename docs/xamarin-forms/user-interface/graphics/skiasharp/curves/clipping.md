---
標題：「使用路徑和區域裁剪」描述：「本文將說明如何使用 SkiaSharp 路徑將圖形裁剪到特定區域，以及如何建立區域，並以範例程式碼示範此動作。」
skiasharp 的 assetid： 8022FBF9-2208-43DB-94D8-0A4E9A5DA07F author： davidbritch ms-chap： dabritch ms. date： 06/16/2017 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="clipping-with-paths-and-regions"></a>使用路徑和區域裁剪

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用路徑將圖形裁剪到特定區域，以及建立區域_

有時必須將圖形的呈現限制在特定區域。 這就是所謂的*裁剪*。 您可以使用裁剪來取得特殊效果，例如透過鑰匙孔所看到的猴子影像：

![透過鑰匙孔的猴子](clipping-images/clippingsample.png)

[*剪切] 區域*是用來呈現圖形的螢幕區域。 不會轉譯在裁剪區域外部顯示的任何內容。 裁剪區域通常是由矩形或物件所定義 [`SKPath`](xref:SkiaSharp.SKPath) ，但是您也可以使用物件來定義裁剪區域 [`SKRegion`](xref:SkiaSharp.SKRegion) 。 這兩種類型的物件一開始看起來是相關的，因為您可以從路徑建立區域。 不過，您無法從區域建立路徑，而且它們在內部非常不同：路徑是由一連串的線條和曲線組成，而區域則是由一系列的水準掃描行所定義。

上圖是**透過鑰匙孔**頁面建立的猴子。 [`MonkeyThroughKeyholePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/MonkeyThroughKeyholePage.cs)類別會使用 SVG 資料定義路徑，並使用此函式從程式資源載入點陣圖：

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

雖然 `keyholePath` 物件描述的是鑰匙孔的外框，但座標完全是任意的，而且會反映設計路徑資料時的便利。 基於這個理由， `PaintSurface` 處理常式會取得此路徑的界限，並呼叫 `Translate` 和 `Scale` 以將路徑移動到畫面中央，讓它與螢幕的高度相同：

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

但不會呈現路徑。 相反地，在轉換之後，會使用路徑來設定含有下列語句的裁剪區域：

```csharp
canvas.ClipPath(keyholePath);
```

`PaintSurface`然後，處理常式會使用的呼叫來重設轉換 `ResetMatrix` ，並繪製點陣圖以擴充到螢幕的完整高度。 此程式碼假設點陣圖為方形，此點陣圖為。 點陣圖只會在裁剪路徑所定義的區域內轉譯：

[![透過 [鑰匙孔] 頁面的三重螢幕擷取畫面](clipping-images/monkeythroughkeyhole-small.png)](clipping-images/monkeythroughkeyhole-large.png#lightbox)

當呼叫方法時，裁剪路徑會受到作用中的轉換 `ClipPath` ，而不是在顯示繪圖物件（例如點陣圖）時生效的轉換。 裁剪路徑是畫布狀態的一部分，會與方法一起儲存 `Save` 並使用 `Restore` 方法還原。

## <a name="combining-clipping-paths"></a>合併裁剪路徑

嚴格來說，方法不會「設定」裁剪區域 `ClipPath` 。 相反地，它會與現有的裁剪路徑結合，其開頭為與畫布大小相等的矩形。 您可以使用屬性或屬性，取得裁剪區域的矩形界限 [`ClipBounds`](xref:SkiaSharp.SKCanvas.ClipBounds) [`ClipDeviceBounds`](xref:SkiaSharp.SKCanvas.ClipDeviceBounds) 。 `ClipBounds`屬性 `SKRect` 會傳回值，以反映任何可能作用中的轉換。 屬性會傳回 `ClipDeviceBounds` `RectI` 值。 這是具有整數維度的矩形，並描述實際圖元維度中的裁剪區域。

任何呼叫都會 `ClipPath` 合併裁剪區域與新區域，以減少裁剪區域。 方法的完整語法 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath(SkiaSharp.SKPath,SkiaSharp.SKClipOperation,System.Boolean)) 為：

```csharp
public void ClipPath(SKPath path, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

另外還有一個 [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect(SkiaSharp.SKRect,SkiaSharp.SKClipOperation,System.Boolean)) 結合裁剪區域與一個矩形的方法：

```csharp
public Void ClipRect(SKRect rect, SKClipOperation operation = SKClipOperation.Intersect, Boolean antialias = false);
```

根據預設，產生的裁剪區域是現有裁剪區域和 `SKPath` `SKRect` 或方法中所指定或的交集 `ClipPath` `ClipRect` 。 這會在**四個圓圈相交剪輯**頁面中進行示範。 `PaintSurface`類別中的處理常式會 [`FourCircleInteresectClipPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourCircleIntersectClipPage.cs) 重複使用相同的 `SKPath` 物件來建立四個重迭的圓形，每一個都會透過後續呼叫來減少裁剪區域 `ClipPath` ：

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

左邊是這四個圓形的交集：

[![四個 Circle 相交剪輯頁面的三向螢幕擷取畫面](clipping-images//fourcircleintersectclip-small.png)](clipping-images/fourcircleintersectclip-large.png#lightbox)

[`SKClipOperation`](xref:SkiaSharp.SKClipOperation)列舉只有兩個成員：

- `Difference`從現有的裁剪區域移除指定的路徑或矩形

- `Intersect`將指定的路徑或矩形與現有的裁剪區域交集

如果您將類別中的四個 `SKClipOperation.Intersect` 引數取代 `FourCircleIntersectClipPage` 為 `SKClipOperation.Difference` ，則會看到下列內容：

[![四個圓圈相交剪輯頁面與差異作業的三向螢幕擷取畫面](clipping-images//fourcircledifferenceclip-small.png)](clipping-images/fourcircledifferenceclip-large.png#lightbox)

已從裁剪區域中移除四個重迭的圓形。

[**剪輯作業**] 頁面說明這兩個作業之間只有一對圓形的差異。 左側的第一個圓圈會加入至具有預設剪輯作業的裁剪區域 `Intersect` ，而右側的第二個圓形則會加入至含有文字標籤所指示之剪輯作業的裁剪區域：

[![[剪輯作業] 頁面的三重螢幕擷取畫面](clipping-images//clipoperations-small.png)](clipping-images/clipoperations-large.png#lightbox)

類別會將 [`ClipOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClipOperationsPage.cs) 兩個 `SKPaint` 物件定義為欄位，然後將螢幕分割成兩個矩形區域。 根據電話是直向或橫向模式，這些區域會有所不同。 `DisplayClipOp`類別接著會顯示文字，並 `ClipPath` 使用兩個圓形路徑呼叫來說明每個剪輯作業：

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

呼叫 `DrawPaint` 通常會使整個畫布填滿該 `SKPaint` 物件，但在此情況下，方法只會在裁剪區域內繪製。

## <a name="exploring-regions"></a>探索區域

您也可以根據物件來定義裁剪區域 [`SKRegion`](xref:SkiaSharp.SKRegion) 。

新建立的 `SKRegion` 物件描述空白區域。 通常，第一次呼叫物件是， [`SetRect`](xref:SkiaSharp.SKRegion.SetRect(SkiaSharp.SKRectI)) 區域會描述矩形區域。 的參數 `SetRect` 是 `SKRectI` &mdash; 具有整數座標的矩形值，因為它會指定矩形（以圖元為單位）。 然後，您可以 [`SetPath`](xref:SkiaSharp.SKRegion.SetPath(SkiaSharp.SKPath,SkiaSharp.SKRegion)) 使用 `SKPath` 物件呼叫。 這會建立與路徑內部相同的區域，但會裁剪到初始的矩形區域。

您也可以呼叫其中一個方法多載來修改該區域 [`Op`](xref:SkiaSharp.SKRegion.Op*) ，如下所示：

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

您要進行呼叫的區域， `Op` 會與根據成員指定為參數的區域結合 `SKRegionOperation` 。 當您最後取得適合用於裁剪的區域時，您可以使用的方法，將其設定為畫布的裁剪區域 [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion(SkiaSharp.SKRegion,SkiaSharp.SKClipOperation)) `SKCanvas` ：

```csharp
public void ClipRegion(SKRegion region, SKClipOperation operation = SKClipOperation.Intersect)
```

下列螢幕擷取畫面顯示以六個區域作業為基礎的裁剪區域。 左圓圈是 `Op` 呼叫方法的區域，而右 circle 則是傳遞給方法的區域 `Op` ：

[![[區域作業] 頁面的三重螢幕擷取畫面](clipping-images//regionoperations-small.png)](clipping-images/regionoperations-large.png#lightbox)

這些是結合這兩個圓形的可能性嗎？ 將產生的映射視為三個元件的組合，其本身會出現在 `Difference` 、 `Intersect` 和 `ReverseDifference` 作業中。 總組合數是第三個乘冪，或八個。 遺漏的兩個是原始區域（不會呼叫全部的結果 `Op` ）和完全空白的區域。

使用區域進行裁剪會比較困難，因為您需要先建立路徑，然後再從該路徑中移除區域，再結合多個區域。 [**區域作業**] 頁面的整體結構與**剪輯作業**非常類似，但類別會將 [`RegionOperationsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionOperationsPage.cs) 螢幕分割成六個區域，並顯示針對此作業使用區域所需的額外工作量：

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

方法與方法之間有很大的差異 `ClipPath` `ClipRegion` ：

> [!IMPORTANT]
> 不同于 `ClipPath` 方法， `ClipRegion` 方法不會受到轉換的影響。

若要瞭解這種差異的基本原理，請務必瞭解什麼是區域。 如果您已考慮剪輯作業或區域作業在內部的執行方式，可能會很複雜。 結合了數個可能非常複雜的路徑，而結果路徑的外框可能會有極大的演算法。

如果每個路徑都縮減成一系列的水準掃描線（例如，在舊式的清理電子管電視中），這項作業就會大幅簡化。 每個掃描行只是一個具有起點和一個結束點的水平線。 例如，半徑為10圖元的圓形可以分解成20個水準掃描線條，其中每個都是從圓形的左邊部分開始，並在右邊結束。 結合兩個圓形與任何區域作業會變得非常簡單，因為它只是檢查每一對對應掃描線的開始和結束座標。

這是區域的意義：一系列定義區域的水準掃描線條。

不過，當區域縮小為一系列的掃描線時，這些掃描線條會以特定圖元維度為基礎。 嚴格來說，此區域不是向量繪圖物件。 比起路徑，其本質上與壓縮的單色點陣圖更接近。 因此，區域無法縮放或旋轉，而不會失去精確度，因此在用於裁剪區域時，不會轉換它們。

不過，您可以將轉換套用到區域以供繪製之用。 **區域繪製**程式生動會示範區域的內部本質。 [`RegionPaintPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/RegionPaintPage.cs)類別會 `SKRegion` 根據 `SKPath` 10 單位半徑圓形的來建立物件。 然後，轉換會展開該圓形以填滿頁面：

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

此 `DrawRegion` 呼叫會以橙色填滿區域，而 `DrawPath` 呼叫會將原始路徑以藍色表示以進行比較：

[![[區域繪製] 頁面的三重螢幕擷取畫面](clipping-images//regionpaint-small.png)](clipping-images/regionpaint-large.png#lightbox)

區域顯然是一系列的離散座標。

如果您不需要在與裁剪區域連接時使用轉換，可以使用區域進行裁剪，如**四分葉四葉草**頁面所示。 [`FourLeafCloverPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/FourLeafCloverPage.cs)類別會從四個迴圈區域來建立複合區域、將該複合區域設定為裁剪區域，然後從頁面中央繪製一連串的360直線 mouseleave：

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

它看起來不像四個分葉的四葉草，但它是一個可能難以轉譯而不需要裁剪的影像：

[![四個分葉四葉草頁面的三向螢幕擷取畫面](clipping-images//fourleafclover-small.png)](clipping-images/fourleafclover-large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
