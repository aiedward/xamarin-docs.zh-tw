---
title: 傾斜轉換
description: 本文說明扭曲轉換如何在 SkiaSharp 中建立傾斜的繪圖物件，並以範例程式碼示範這項功能。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: c3d7e6e0c0e3230c11e2e96baa9efa57ac988c83
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545658"
---
# <a name="the-skew-transform"></a>傾斜轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解扭曲轉換如何在 SkiaSharp 中建立傾斜的繪圖物件_

在 SkiaSharp 中，扭曲轉換會傾斜繪圖物件，例如此影像中的陰影：

![](skew-images/skewexample.png "An example of skewing from the Skew Shadow Text program")

扭曲會將矩形變成平行四邊形，但扭曲的橢圓形仍然是橢圓形。

雖然 Xamarin 會定義轉譯、縮放和旋轉的屬性，但在 Xamarin 中沒有對應的屬性。扭曲的形式。

`SKCanvas` 的[`Skew`](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single))方法會接受兩個引數來進行水準扭曲和垂直扭曲：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二個[`Skew`](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint))方法會將這些引數結合成單一 `SKPoint` 值：

```csharp
public void Skew (SKPoint skew)
```

不過，您不太可能會在隔離中使用這兩種方法的其中一種。

[**扭曲實驗**] 頁面可讓您試驗範圍介於–10到10之間的扭曲值。 文字字串位於頁面的左上角，其中包含從兩個 `Slider` 元素取得的扭曲值。 以下是[`SkewExperimentPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs)類別中的 `PaintSurface` 處理常式：

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

`xSkew` 引數的值會將右邊的正值或 left 的文字右移，以取得負數值。 `ySkew` 的值會向下移動正值的正數值或負數值的右邊：

[![](skew-images/skewexperiment-small.png "Triple screenshot of the Skew Experiment page")](skew-images/skewexperiment-large.png#lightbox "Triple screenshot of the Skew Experiment page")

如果 `xSkew` 值是 `ySkew` 值的負值，則結果會是旋轉，但也會稍微調整。

轉換公式如下所示：

x ' = x + xSkew ·y

y ' = ySkew ·x + y

例如，如果是正 `xSkew` 值，轉換後的 `x'` 值會隨著 `y` 增加而增加。 這就是造成傾斜的原因。

如果三角形200圖元寬和100圖元偏高，其位置在點（0，0）的左上角，且以 `xSkew` 值1.5 轉譯，則會顯示下列平行四邊形結果：

![](skew-images/skeweffect.png "The effect of the skew transform on a rectangle")

下邊緣的座標具有100的 `y` 值，因此會將150圖元向右移位。

對於 `xSkew` 或 `ySkew`的非零值，只有點（0，0）維持不變。 該點可以視為扭曲的中心。 如果您需要扭曲的中心是其他東西（通常是如此），則不會有任何 `Skew` 方法可提供該專案。 您必須明確地結合 `Translate` 呼叫與 `Skew` 呼叫。 若要在 `px` 和 `py`將扭曲置中，請進行下列呼叫：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

複合轉換公式為：

x ' = x + xSkew ·（y – .py）

y ' = ySkew ·（x – px） + y

如果 `ySkew` 為零，則不會使用 `px` 值。 此值並不相關，也同樣適用于 `ySkew` 和 `py`。

您可能會更習慣將扭曲指定為傾斜角度，例如此圖中的角度α：

![](skew-images/skewangleeffect.png "The effect of the skew transform on a rectangle with a skewing angle indicated")

150-圖元轉換成100圖元垂直的比率是該角度的正切函數，在此範例中為56.3 度。

[**扭曲角度實驗**] 頁面的 XAML 檔案與 [**扭曲角度**] 頁面相似，不同之處在于 `Slider` 元素的範圍介於–90度到90度之間。 [`SkewAngleExperiment`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)程式碼後置檔案會將文字放在頁面上，並使用 `Translate` 將扭曲的中心設定為頁面的中央。 程式碼底部的簡短 `SkewDegrees` 方法會將角度轉換成扭曲值：

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

當角度接近正面或負面90度時，正切方法會無限大，但最多可達80度的角度，或可供使用：

[![](skew-images/skewangleexperiment-small.png "Triple screenshot of the Skew Angle Experiment page")](skew-images/skewangleexperiment-large.png#lightbox "Triple screenshot of the Skew Angle Experiment page")

較小的負水準扭曲可以模仿傾斜或斜體文字，如**傾斜文字**頁面所示。 [`ObliqueTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)類別會顯示其完成方式：

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

`SKPaint` 的 `TextAlign` 屬性設定為 [`Center`]。 如果沒有任何轉換，使用（0，0）座標的 `DrawText` 呼叫，會將文字放在左上角的基準水準中心。 `SkewDegrees` 相對於基準，以水準方式將文字扭曲為20度。 `Translate` 呼叫會將文字基線的水準中心移至畫布的中央：

[![](skew-images/obliquetext-small.png "Triple screenshot of the Oblique Text page")](skew-images/obliquetext-large.png#lightbox "Triple screenshot of the Oblique Text page")

[**扭曲陰影文字**] 頁面會示範如何使用45度扭曲和垂直尺規的組合，讓文字陰影遠離文字。 以下是 `PaintSurface` 處理常式的相關部分：

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

陰影會先顯示，然後是文字：

[![](skew-images/skewshadowtext1-small.png "Triple screenshot of the Skew Shadow Text page")](skew-images/skewshadowtext1-large.png#lightbox "Triple screenshot of the Skew Shadow Text page")

傳遞至 `DrawText` 方法的垂直座標會指出相對於基準的文字位置。 這是用於扭曲中心的相同垂直座標。 如果文字字串包含下行，這項技術將無法使用。 例如，將 "古怪" 這個字替換為 "Shadow"，以下是結果：

[![](skew-images/skewshadowtext2-small.png "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")](skew-images/skewshadowtext2-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with an alternative word with descenders")

陰影和文字仍會對齊基準，但效果只會顯示錯誤。 若要修正此問題，您必須取得文字界限：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate` 呼叫必須以下行的高度來調整：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

現在陰影會從下行底部延伸：

[![](skew-images/skewshadowtext3-small.png "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")](skew-images/skewshadowtext3-large.png#lightbox "Triple screenshot of the Skew Shadow Text page with adjustments for descenders")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
