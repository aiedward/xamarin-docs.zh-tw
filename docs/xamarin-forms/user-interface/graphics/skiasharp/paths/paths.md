---
title: SkiaSharp 中的路徑基本概念
description: 本文將探討用來結合連接線和曲線的 SkiaSharp SKPath 物件，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6387da5ffa240c2509a2942a1e721def8f8d39b9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555628"
---
# <a name="path-basics-in-skiasharp"></a>SkiaSharp 中的路徑基本概念

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索用來結合連接線和曲線的 SkiaSharp SKPath 物件_

圖形路徑最重要的功能之一，就是能夠定義多行應該連接的時間，以及何時不應該連接。 差別可能很大，因為這兩個三角形的最上方會示範：

![兩個三角形，顯示已連接和已中斷連線的行之間的差異](paths-images/connectedlinesexample.png)

圖形路徑是由物件所封裝 [`SKPath`](xref:SkiaSharp.SKPath) 。 路徑是一或多個 *輪廓*的集合。 每個輪廓都是 *連接* 直線和曲線的集合。 等高線不會彼此連接，但是它們可能會在視覺上重迭。 有時單一輪廓可能會彼此重迭。

輪廓通常會從下列方法的呼叫開始 `SKPath` ：

- [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo*) 開始新的輪廓

該方法的引數是單一點，您可以將其作為 `SKPoint` 值或個別的 X 和 Y 座標來表示。 `MoveTo`呼叫會在輪廓的開頭和初始*目前的點*之間建立點。 您可以呼叫下列方法，以使用從目前點到方法中指定之點的線條或曲線來繼續輪廓，如此就會成為新的目前點：

- [`LineTo`](xref:SkiaSharp.SKPath.LineTo*) 若要在路徑中加入直線
- [`ArcTo`](xref:SkiaSharp.SKPath.ArcTo*) 若要加入弧線，也就是圓形或橢圓形圓周上的線條
- [`CubicTo`](xref:SkiaSharp.SKPath.CubicTo*) 若要加入三次方貝茲曲線
- [`QuadTo`](xref:SkiaSharp.SKPath.QuadTo*) 新增二次方貝茲曲線
- [`ConicTo`](xref:SkiaSharp.SKPath.ConicTo*) 若要新增有理二次方貝茲曲線，可以精確地呈現圓錐區段 (省略號、parabolas 和 hyperbolas) 

這五種方法都不包含描述線條或曲線的所有必要資訊。 這五個方法中的每一個都可以搭配緊接在它前面的方法呼叫所建立的目前點一起運作。 例如， `LineTo` 方法會根據目前的點將直線加入到輪廓中，因此的參數 `LineTo` 只是單點。

`SKPath`類別也會定義方法，這些方法的名稱與這六個方法相同，但 `R` 開頭為：

- [`RMoveTo`](xref:SkiaSharp.SKPath.RMoveTo*)
- [`RLineTo`](xref:SkiaSharp.SKPath.RLineTo*)
- [`RArcTo`](xref:SkiaSharp.SKPath.RArcTo*)
- [`RCubicTo`](xref:SkiaSharp.SKPath.RCubicTo*)
- [`RQuadTo`](xref:SkiaSharp.SKPath.RQuadTo*)
- [`RConicTo`](xref:SkiaSharp.SKPath.RConicTo*)

`R`代表*相對*的。 這些方法的語法與對應的方法相同，但不含， `R` 但是相對於目前的點。 這些很方便用來在您呼叫的方法中繪製類似的路徑部分。

輪廓會以另一個對或的呼叫做為結尾， `MoveTo` `RMoveTo` 也就是開始新的輪廓或呼叫 `Close` ，以關閉輪廓。 `Close`方法會自動從目前的點將直線附加到輪廓的第一個點，並將路徑標示為已關閉，這表示它不會在沒有任何筆觸帽的情況下呈現。

開啟和關閉等高線之間的差異會顯示在 **兩個三角形** 的 [分佈] 頁面中，它會使用 `SKPath` 具有兩個輪廓的物件來呈現兩個三角形。 第一個輪廓是開啟的，第二個則關閉。 以下是 [`TwoTriangleContoursPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/TwoTriangleContoursPage.cs) 類別：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create the path
    SKPath path = new SKPath();

    // Define the first contour
    path.MoveTo(0.5f * info.Width, 0.1f * info.Height);
    path.LineTo(0.2f * info.Width, 0.4f * info.Height);
    path.LineTo(0.8f * info.Width, 0.4f * info.Height);
    path.LineTo(0.5f * info.Width, 0.1f * info.Height);

    // Define the second contour
    path.MoveTo(0.5f * info.Width, 0.6f * info.Height);
    path.LineTo(0.2f * info.Width, 0.9f * info.Height);
    path.LineTo(0.8f * info.Width, 0.9f * info.Height);
    path.Close();

    // Create two SKPaint objects
    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Magenta,
        StrokeWidth = 50
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    // Fill and stroke the path
    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

第一個輪廓包含 [`MoveTo`](xref:SkiaSharp.SKPath.MoveTo(System.Single,System.Single)) 使用 X 和 Y 座標而非值的呼叫 `SKPoint` ，接著三個呼叫來 [`LineTo`](xref:SkiaSharp.SKPath.LineTo(System.Single,System.Single)) 繪製三角形的三方。 第二個輪廓只有兩個呼叫 `LineTo` ，但它會透過呼叫來完成輪廓 [`Close`](xref:SkiaSharp.SKPath.Close) ，以關閉輪廓。 差異很大：

[![兩個三角形分佈頁面的三個螢幕擷取畫面](paths-images/twotrianglecontours-small.png)](paths-images/twotrianglecontours-large.png#lightbox "兩個三角形分佈頁面的三個螢幕擷取畫面")

如您所見，第一個輪廓顯然是三個連接線的一連串，但最後並不會與開頭連接。 這兩行會在頂端重迭。 第二個輪廓顯然是封閉的，並且使用一個較少的呼叫來完成， `LineTo` 因為 `Close` 方法會自動加入最後一行來關閉輪廓。

`SKCanvas` 只定義一個 [`DrawPath`](xref:SkiaSharp.SKCanvas.DrawPath(SkiaSharp.SKPath,SkiaSharp.SKPaint)) 方法，在此示範中呼叫兩次以填滿和筆觸路徑。 所有的輪廓都會填滿，甚至是未關閉的輪廓。 基於填滿未封閉路徑的目的，會假設在輪廓的起點與終點之間存在直線。 如果您 `LineTo` 從第一個輪廓中移除最後一個，或從第二個輪廓中移除 `Close` 呼叫，則每個輪廓都只會有兩個邊，但會填滿為三角形。

`SKPath` 定義許多其他方法和屬性。 下列方法會將整個分佈新增至路徑，這可能會根據方法而關閉或未關閉：

- [`AddRect`](xref:SkiaSharp.SKPath.AddRect*)
- [`AddRoundedRect`](xref:SkiaSharp.SKPath.AddRoundedRect(SkiaSharp.SKRect,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddCircle`](xref:SkiaSharp.SKPath.AddCircle(System.Single,System.Single,System.Single,SkiaSharp.SKPathDirection))
- [`AddOval`](xref:SkiaSharp.SKPath.AddOval(SkiaSharp.SKRect,SkiaSharp.SKPathDirection))
- [`AddArc`](xref:SkiaSharp.SKPath.AddArc(SkiaSharp.SKRect,System.Single,System.Single)) 在橢圓形的圓周上加入曲線
- [`AddPath`](xref:SkiaSharp.SKPath.AddPath*) 將另一個路徑新增至目前的路徑
- [`AddPathReverse`](xref:SkiaSharp.SKPath.AddPathReverse(SkiaSharp.SKPath)) 若要反向新增另一個路徑

請記住， `SKPath` 物件只會定義一 &mdash; 系列點和連接的幾何。 只有當與 `SKPath` 物件結合時 `SKPaint` ，才會以特定色彩、筆劃寬度等等來呈現路徑。 此外，請記住， `SKPaint` 傳遞給方法的物件會 `DrawPath` 定義整個路徑的特性。 如果您想要繪製需要數個色彩的內容，您必須針對每個色彩使用不同的路徑。

就像線條開始和結束的外觀是由筆劃端點所定義，兩行之間的連接外觀是由 *筆劃聯結*所定義。 您可以藉由將的 [`StrokeJoin`](xref:SkiaSharp.SKPaint.StrokeJoin) 屬性設定 `SKPaint` 為列舉的成員來指定 [`SKStrokeJoin`](xref:SkiaSharp.SKStrokeJoin) ：

- `Miter` 針對角聯結
- `Round` 針對舍入聯結
- `Bevel` 針對切碎的聯結

[ **筆觸** 聯結] 頁面會顯示這三個與程式碼相似的筆觸聯結，類似于 [ **筆觸帽** ] 頁面。 這是 `PaintSurface` 類別中的事件處理常式 [`StrokeJoinsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeJoinsPage.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Right
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width - 100;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = 2 * textPaint.FontSpacing;
    string[] strStrokeJoins = { "Miter", "Round", "Bevel" };

    foreach (string strStrokeJoin in strStrokeJoins)
    {
        // Display text
        canvas.DrawText(strStrokeJoin, xText, y, textPaint);

        // Get stroke-join value
        SKStrokeJoin strokeJoin;
        Enum.TryParse(strStrokeJoin, out strokeJoin);

        // Create path
        SKPath path = new SKPath();
        path.MoveTo(xLine1, y - 80);
        path.LineTo(xLine1, y + 80);
        path.LineTo(xLine2, y + 80);

        // Display thick line
        thickLinePaint.StrokeJoin = strokeJoin;
        canvas.DrawPath(path, thickLinePaint);

        // Display thin line
        canvas.DrawPath(path, thinLinePaint);
        y += 3 * textPaint.FontSpacing;
    }
}
```

以下是程式執行情況：

[![[筆觸聯結] 頁面的三重螢幕擷取畫面](paths-images/strokejoins-small.png)](paths-images/strokejoins-large.png#lightbox "[筆觸聯結] 頁面的三重螢幕擷取畫面")

斜接聯結是由線條連接的一點點所組成。 當兩行以小角度聯結時，斜接聯結可能會變成相當長的時間。 為了避免過度長的斜接聯結，斜接聯結的長度會受到的屬性值所限制 [`StrokeMiter`](xref:SkiaSharp.SKPaint.StrokeMiter) `SKPaint` 。 超出此長度的斜接聯結會切碎 off 成為斜面聯結。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)