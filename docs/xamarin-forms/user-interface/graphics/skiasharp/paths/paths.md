---
title: 路徑的基本概念
description: 瀏覽結合連接的直線和曲線的 SkiaSharp SKPath 物件
ms.prod: xamarin
ms.assetid: A7EDA6C2-3921-4021-89F3-211551E430F1
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 3a828baccda83822237d2564d771bcd89c9099e5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="path-basics"></a>路徑的基本概念

_瀏覽結合連接的直線和曲線的 SkiaSharp SKPath 物件_

能夠定義多行應該連接時，和當他們不應該連接其中一個最重要的受的圖形路徑。 這些兩個三角形的上緣示範可以是完全可見，差異：

![](paths-images/connectedlinesexample.png "兩個三角形顯示連線和中斷的線條之間的差異")

封裝的圖形路徑[ `SKPath` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath/)物件。 路徑是一或多個集合*分佈*。 每個分佈是一組*連接*直線和曲線。 分佈未彼此連線，但它們以視覺化方式可能會重疊。 有時單一分佈可以重疊本身。

以下列方法的呼叫通常開頭分佈`SKPath`:

- `MoveTo` 若要開始新的分佈

該方法的引數是單一點，您可以表示為`SKPoint`或做為個別的 X 和 Y 座標值。 `MoveTo`呼叫建立的點開頭的分佈和初始*目前點*。 您可以呼叫下列方法，以繼續進行的分佈與線條或曲線從目前的位置中指定的方法，就會變成新的目前點的點：

- `LineTo` 要加入的路徑為直線
- `ArcTo` 若要新增的弧形，這是圓形或橢圓形的圓周上的線上
- `CubicTo` 若要加入的三次方貝茲曲線
- `QuadTo` 若要加入的二次方貝茲曲線
- `ConicTo` 若要加入的合理二次方貝茲曲線，可以正確地轉譯 conic 區段 （省略符號、 parabolas 和 hyperbolas）

這些五個方法都包含描述線條或曲線所需的所有資訊。 每個五個方法是與搭配目前立即在它前面的方法呼叫所建立的點。 例如，`LineTo`方法會將直線線段來根據目前的點，因此參數`LineTo`是單一點。

`SKPath`類別也會定義具有相同的名稱為下列六種方法，但具有方法`R`開頭：

- `RMoveTo`
- `RLineTo`
- `RArcTo`
- `RCubicTo`
- `RQuadTo`
- `RConicTo`

`R`代表*相對*。 它們有沒有對應的方法與相同的語法`R`但相對於目前的點。 這些是路徑的很方便用來繪製類似的組件的多次呼叫的方法中。

分佈的結尾是另一個呼叫`MoveTo`或`RMoveTo`，其開始新的分佈或呼叫`Close`，這會關閉分佈。 `Close`方法會自動將附加至直線從目前點的第一個點的分佈，並將路徑標示為已關閉，這表示，它將不會呈現任何筆觸端點。

開啟或關閉分佈之間的差異如下所示**兩個三角形分佈**頁面上，它會使用`SKPath`物件具有兩個分佈來呈現兩個三角形。 第一個分佈是開啟和關閉第二個。 以下是[ `TwoTriangleContours` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/TwoTriangleContoursPage.cs)類別：

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

第一個分佈所組成的呼叫[ `MoveTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.MoveTo/p/System.Single/System.Single/)使用 X 和 Y 座標，而非`SKPoint`值，後面接著三次呼叫[ `LineTo` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.LineTo/p/System.Single/System.Single/)繪製的三個邊三角形。 第二個分佈都有只有兩個呼叫`LineTo`但在完成呼叫分佈[ `Close` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Close()/)，這會關閉分佈。 差異十分重要：

[![](paths-images/twotrianglecontours-small.png "三個螢幕擷取畫面的兩個三角形分佈頁面")](paths-images/twotrianglecontours-large.png#lightbox "的兩個三角形分佈頁面的三個螢幕擷取畫面")

如您所見，第一個分佈顯然是一系列的三個連接的直線，但並不會起始連接結束。 上方，重疊兩行。 第二個分佈會很明顯地關閉，並使用較少的其中一個已完成`LineTo`呼叫因為`Close`方法會自動將關閉分佈的最後一行。

`SKCanvas` 僅定義一個[ `DrawPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawPath/p/SkiaSharp.SKPath/SkiaSharp.SKPaint/)方法，該方法在此示範中呼叫兩次來填滿與繪製路徑。 所有的分佈會填滿，即使未關閉的。 為了填入未封閉的路徑，直線會假設之間的開始和結束點的分佈。 如果您移除最後一個`LineTo`從第一個分佈或移除`Close`從第二個分佈，也就是每個分佈的呼叫將會有填滿，就好像三角形但將只有兩個邊。

`SKPath` 定義許多其他方法和屬性。 下列方法會加入至路徑，它可能已關閉或未根據方法關閉整個分佈：

- `AddRect`
- [`AddRoundedRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddRoundedRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddCircle`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddCircle/p/System.Single/System.Single/System.Single/SkiaSharp.SKPathDirection/)
- [`AddOval`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddOval/p/SkiaSharp.SKRect/SkiaSharp.SKPathDirection/)
- [`AddArc`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddArc/p/SkiaSharp.SKRect/System.Single/System.Single/) 若要加入的橢圓形的圓周上曲線
- `AddPath` 若要加入另一個路徑至目前的路徑
- [`AddPathReverse`](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.AddPathReverse/p/SkiaSharp.SKPath/) 若要加入另一個路徑以反向方向

請注意，`SKPath`物件定義的幾何&mdash;一連串的點和連接。 只有當`SKPath`結合`SKPaint`物件是以特定色彩、 筆劃寬度等呈現的路徑。 此外，請注意，`SKPaint`物件傳遞至`DrawPath`方法所定義的完整路徑的特性。 如果您想要繪製項目需要數個色彩，您必須使用個別的路徑為每個色彩。

兩行之間的連線的外觀就像筆觸端點定義的開始和結束的線條的外觀，由定義*筆劃聯結*。 您可以設定這指定[ `StrokeJoin` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeJoin/)屬性`SKPaint`成員[ `SKStrokeJoin` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeJoin/)列舉型別：

- [`Miter`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Miter/) 尖聯結
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Round/) 圓角聯結
- [`Bevel`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeJoin.Bevel/) 切碎關閉聯結

**筆劃聯結**頁面上的顯示這三個筆觸與類似的程式碼的聯結**筆觸端點**頁面。 這是`PaintSurface`中的事件處理常式[ `StrokeJoinsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeJoinsPage.cs)類別：

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

以下是三個平台上執行的程式：

[![](paths-images/strokejoins-small.png "筆觸加入頁面的三個螢幕擷取畫面")](paths-images/strokejoins-large.png#lightbox "筆劃加入頁面的三個螢幕擷取畫面")

斜接角聯結尖點，其中線條的連接所組成。 當兩行程式碼加入小角時，斜接角聯結可能會變得很冗長。 若要避免過長斜接角聯結時，斜接角聯結的長度會受到值[ `StrokeMiter` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeMiter/)屬性`SKPaint`。 超過此長度斜接角聯結砍變成斜面聯結。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
