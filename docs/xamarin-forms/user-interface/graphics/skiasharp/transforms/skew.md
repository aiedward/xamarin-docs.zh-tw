---
title: 傾斜轉換
description: 本文說明扭曲轉換如何在 SkiaSharp 中建立傾斜的繪圖物件，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ed1bcbed86874362e291fb23ce86dea8992d3408
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563727"
---
# <a name="the-skew-transform"></a>傾斜轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_查看扭曲轉換如何在 SkiaSharp 中建立傾斜的繪圖物件_

在 SkiaSharp 中，扭曲轉換會將繪圖物件（例如此影像中的陰影）傾斜：

![從扭曲陰影文字程式扭曲的範例](skew-images/skewexample.png)

扭曲會將矩形變成平行四邊形，但扭曲的橢圓形仍是橢圓形。

雖然 Xamarin.Forms 定義了轉譯、縮放和旋轉的屬性，但是沒有任何對應的屬性 Xamarin.Forms 用於扭曲。

的 [`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single)) 方法會 `SKCanvas` 接受兩個水準扭曲和垂直扭曲的引數：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二個 [`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint)) 方法會將這些引數合併為單一 `SKPoint` 值：

```csharp
public void Skew (SKPoint skew)
```

不過，您不太可能會在隔離的情況下使用這兩種方法的其中一種。

[ **扭曲實驗** ] 頁面可讓您試驗範圍介於-10 和10之間的扭曲值。 文字字串會定位在頁面的左上角，而且會從兩個元素取得誤差值 `Slider` 。 以下是 `PaintSurface` 類別中的處理常式 [`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        canvas.Skew((float)xSkewSlider.Value, (float)ySkewSlider.Value);
        canvas.DrawText(text, 0, -textBounds.Top, textPaint);
    }
}
```

引數的值會將 `xSkew` 文字右邊的下移至正數值，或向左移動負數值。 `ySkew`針對正值或負數值，將文字右邊的右移值：

[![[扭曲實驗] 頁面的三重螢幕擷取畫面](skew-images/skewexperiment-small.png)](skew-images/skewexperiment-large.png#lightbox "[扭曲實驗] 頁面的三重螢幕擷取畫面")

如果 `xSkew` 值是值的負值 `ySkew` ，則結果為旋轉，但也會稍微調整。

轉換公式如下所示：

x ' = x + xSkew ·Y

y ' = ySkew ·x + y

例如，如果是正 `xSkew` 數值，轉換的 `x'` 值會增加 `y` 。 這就是造成傾斜的原因。

如果三角形200圖元寬和100圖元偏高的位置是以其左上角為 (0、0) ，而且會以 `xSkew` 1.5 的值轉譯，則會產生下列平行處理結果：

![矩形上的扭曲轉換效果](skew-images/skeweffect.png)

下邊緣的座標具有 `y` 100 的值，因此會將150圖元向右移位。

若為或的非零 `xSkew` 值 `ySkew` ，則只有 (0，0) 的點會維持不變。 這點可以被視為扭曲的中心。 如果您需要將扭曲的中心設為其他 (通常是) ，則沒有任何 `Skew` 方法可提供該功能。 您必須明確地將 `Translate` 呼叫與呼叫合併 `Skew` 。 若要將扭曲置於 `px` 和的中央 `py` ，請進行下列呼叫：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

複合轉換公式如下：

x ' = x + xSkew · (y – .py) 

y ' = ySkew · (x-px) + y

如果 `ySkew` 為零，則 `px` 不會使用此值。 值是不相關的，而且類似于 `ySkew` 和 `py` 。

您可能覺得更習慣將扭曲指定為傾斜角度，例如下圖中的角度α：

![在矩形上以扭曲角度表示的扭曲轉換效果](skew-images/skewangleeffect.png)

150圖元轉換至 100-圖元垂直的比率是該角度的正切函數，在此範例中為56.3 度。

[ **扭曲角度實驗** ] 頁面的 XAML 檔案類似于 [ **扭曲角度** ] 頁面，不同之處在于專案的 `Slider` 範圍是從–90度到90度。 [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)程式碼後置檔案會將頁面上的文字置中，並使用 `Translate` 將扭曲中心設定為頁面的中心。 程式 `SkewDegrees` 代碼底部的短方法會將角度轉換成扭曲值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 200
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        string text = "SKEW";
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;

        canvas.Translate(xCenter, yCenter);
        SkewDegrees(canvas, xSkewSlider.Value, ySkewSlider.Value);
        canvas.Translate(-xCenter, -yCenter);
        canvas.DrawText(text, xText, yText, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

當角度接近正面或負面的90度時，正切函數的方法會是無限大的，但最多可達80度或可供使用的角度：

[![[扭曲角度實驗] 頁面的三重螢幕擷取畫面](skew-images/skewangleexperiment-small.png)](skew-images/skewangleexperiment-large.png#lightbox "[扭曲角度實驗] 頁面的三重螢幕擷取畫面")

小負的負水準扭曲可以模仿傾斜或斜體文字，如 **傾斜文字** 頁面所示。 [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)類別會顯示完成的方式：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Maroon,
        TextAlign = SKTextAlign.Center,
        TextSize = info.Width / 8       // empirically determined
    })
    {
        canvas.Translate(info.Width / 2, info.Height / 2);
        SkewDegrees(canvas, -20, 0);
        canvas.DrawText(Title, 0, 0, textPaint);
    }
}

void SkewDegrees(SKCanvas canvas, double xDegrees, double yDegrees)
{
    canvas.Skew((float)Math.Tan(Math.PI * xDegrees / 180),
                (float)Math.Tan(Math.PI * yDegrees / 180));
}
```

的 `TextAlign` 屬性 `SKPaint` 會設定為 `Center` 。 若沒有任何轉換，則 `DrawText` 以 (0，0) 座標的呼叫會將文字的水準中心放置於左上角。 `SkewDegrees`相對於基準，會將文字的水準誤差水準20度。 此 `Translate` 呼叫會將文字基準的水準中心移至畫布中央：

[![傾斜文字頁面的三重螢幕擷取畫面](skew-images/obliquetext-small.png)](skew-images/obliquetext-large.png#lightbox "傾斜文字頁面的三重螢幕擷取畫面")

[ **扭曲陰影文字** ] 頁面會示範如何使用45度扭曲和垂直尺規的組合，以建立從文字傾斜的文字陰影。 以下是處理常式的相關部分 `PaintSurface` ：

```csharp
using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = info.Width / 6;   // empirically determined

    // Common to shadow and text
    string text = "Shadow";
    float xText = 20;
    float yText = info.Height / 2;

    // Shadow
    textPaint.Color = SKColors.LightGray;
    canvas.Save();
    canvas.Translate(xText, yText);
    canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
    canvas.Scale(1, 3);
    canvas.Translate(-xText, -yText);
    canvas.DrawText(text, xText, yText, textPaint);
    canvas.Restore();

    // Text
    textPaint.Color = SKColors.Blue;
    canvas.DrawText(text, xText, yText, textPaint);
}
```

陰影會先顯示，然後顯示文字：

[![扭曲陰影文字頁面的三重螢幕擷取畫面](skew-images/skewshadowtext1-small.png)](skew-images/skewshadowtext1-large.png#lightbox "扭曲陰影文字頁面的三重螢幕擷取畫面")

傳遞至方法的垂直座標 `DrawText` 表示文字相對於基準的位置。 這是用於扭曲中心的相同垂直座標。 如果文字字串包含下行字元，這項技術將無法運作。 例如，以 "古怪" 取代 "Shadow"，以下是結果：

[![扭曲陰影文字頁面的三個螢幕擷取畫面，其中包含下行的替代單字](skew-images/skewshadowtext2-small.png)](skew-images/skewshadowtext2-large.png#lightbox "扭曲陰影文字頁面的三個螢幕擷取畫面，其中包含下行的替代單字")

陰影和文字仍會在基準中對齊，但是效果看起來就是錯誤。 若要修正此問題，您需要取得文字界限：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate`呼叫需要依下行的高度調整：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

現在陰影會從這些下行底部延伸：

[![扭曲陰影文字頁面的三個螢幕擷取畫面，其中包含下行的調整](skew-images/skewshadowtext3-small.png)](skew-images/skewshadowtext3-large.png#lightbox "扭曲陰影文字頁面的三個螢幕擷取畫面，其中包含下行的調整")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)