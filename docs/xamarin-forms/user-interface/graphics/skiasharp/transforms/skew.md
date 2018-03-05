---
title: "傾斜轉換"
description: "請參閱如何傾斜轉換時，可以在 SkiaSharp 中建立傾斜的圖形物件"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/20/2017
ms.openlocfilehash: b8bb4db49d3800d694724d6be8fe949b55060c21
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="the-skew-transform"></a>傾斜轉換

_請參閱如何傾斜轉換時，可以在 SkiaSharp 中建立傾斜的圖形物件_

在 SkiaSharp，傾斜轉換會將圖形化的物件，例如在這個影像陰影：

![](skew-images/skewexample.png "扭曲扭曲陰影文字程式範例")

傾斜將矩形轉換成 parallelograms，但扭曲的橢圓形仍然是橢圓形。

雖然 Xamarin.Forms 定義屬性，用於平移、 縮放和旋轉，沒有任何對應的屬性在 Xamarin.Forms 中適用於誤差。

[ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/System.Single/System.Single/)方法`SKCanvas`接受扭曲誤差水平及垂直的兩個引數：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二個[ `Skew` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Skew/p/SkiaSharp.SKPoint/)方法會結合這些引數，在單一`SKPoint`值：

```csharp
public void Skew (SKPoint skew)
```

不過，也不太可能，您將使用下列兩種方法之一在隔離狀態。

**扭曲實驗**網頁可讓您試驗誤差值 – 10 到 10 之間的範圍。 文字字串位於左上角的頁面上，從兩個取得誤差值`Slider`項目。 以下是`PaintSurface`中的處理常式[ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs)類別：

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

值`xSkew`引數移位右正數值的文字或負數值的左邊的底部。 值`ySkew`向下移動文字的右邊，正值或負值註冊：

[![](skew-images/skewexperiment-small.png "三個螢幕擷取畫面的扭曲實驗頁面")](skew-images/skewexperiment-large.png "扭曲實驗頁面的三個螢幕擷取畫面")

如果`xSkew`為負`ySkew`，結果是旋轉，但也稍微調整為顯示表示視窗。

轉換公式如下所示：

x' = x + xSkew · y

y' = ySkew ·x + y

例如，對於正`xSkew`值，轉換`x'`值會隨著`y`會增加。 這是什麼情況會讓傾斜。

如果三角形 200 像素寬且 100 像素高位於其左上角的點 （0，0），並且以呈現`xSkew`1.5，下列的平行四邊形結果的值：

![](skew-images/skeweffect.png "對矩形的傾斜轉換效果")

下邊緣座標的`y`值為 100，因此它是向右移動 150 個像素。

非零值`xSkew`或`ySkew`、 點 （0，0） 維持不變。 該點可以視為中心扭曲。 如果需要的其他扭曲中心 （這通常是如此），沒有任何`Skew`提供的方法。 您需要明確地結合`Translate`呼叫`Skew`呼叫。 若要置中在扭曲`px`和`py`，進行下列呼叫：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

複合轉換公式是：

x' = x + xSkew ·(y – py)

y' = ySkew ·(x – px) + y

如果`ySkew`為零，而您只會指定非零值`xSkew`，然後`px`不會使用值。 值為無關，並同樣地針對`ySkew`和`py`。

您可能比較習慣指定傾斜角度的傾斜，例如此圖中的角度 α 為：

![](skew-images/skewangleeffect.png "指出矩形的扭曲角度的傾斜轉換效果")

為 100 像素垂直 150 像素排班的比例是在此範例中的該角度的正切 56.3 度。

XAML 檔案的**扭曲角度實驗**頁面是類似於**扭曲角度**頁面上，除了`Slider`項目範圍從一來-90 到 90 度。 [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)置中文字的上程式碼後置檔案，並使用`Translate`設定頁面的中央扭曲的中心。 一個簡短`SkewDegrees`方法底端的程式碼將轉換的角度來扭曲值：

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

角度接近正數或負數 90 度時，將正切函數接近無限大，但最多約 80 度或相當的角度可用：

[![](skew-images/skewangleexperiment-small.png "扭曲角度實驗頁面的三個螢幕擷取畫面")](skew-images/skewangleexperiment-large.png "扭曲角度實驗頁面的三個螢幕擷取畫面")

小負數水平傾斜可以模擬傾斜或斜體文字，做為**傾斜的文字**頁面示範。 [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)類別示範如何進行：

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

`TextAlign`屬性`SKPaint`設`Center`。 沒有任何轉換，`DrawText`呼叫的座標為 （0，0） 會在左上角位置上的文字與基準水平中央。 `SkewDegrees`扭曲文字水平相對於基準的 20 度。 `Translate`呼叫會將文字的基準的水平中央移到畫布的正中央：

[![](skew-images/obliquetext-small.png "三個螢幕擷取畫面的傾斜的文字頁面")](skew-images/obliquetext-large.png "傾斜的文字頁面的三個螢幕擷取畫面")

**扭曲陰影文字**頁面會示範如何使用 45 度誤差平和垂直縮放比例的組合，請將遠離文字的文字陰影。 以下是相關一部分`PaintSurface`處理常式：

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

陰影會先顯示和文字：

[![](skew-images/skewshadowtext1-small.png "三個螢幕擷取畫面的扭曲陰影效果的文字頁面")](skew-images/skewshadowtext1-large.png "扭曲陰影效果的文字頁面的三個螢幕擷取畫面")

垂直座標傳遞至`DrawText`方法表示相對於基準文字的位置。 這是為中心的扭曲使用相同的垂直座標。 如果文字字串包含容納，這項技術將無法運作。 例如，subsitute 「 陰影 」 和這裡的 「 古怪"這個字的結果：

[![](skew-images/skewshadowtext2-small.png "三個螢幕擷取畫面的扭曲陰影效果的文字頁面，以容納與替代字")](skew-images/skewshadowtext2-large.png "扭曲陰影效果的文字頁面高度了替代字組的三個螢幕擷取畫面")

陰影和文字仍然在基準對齊但效果就看起來不正確。 若要修正此問題，您需要取得文字範圍：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate`呼叫需要容納高度來調整：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

現在從那些容納底部延伸陰影：

[![](skew-images/skewshadowtext3-small.png "調整以容納扭曲陰影效果的文字頁面的三個螢幕擷取畫面")](skew-images/skewshadowtext3-large.png "調整以容納扭曲陰影效果的文字頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
