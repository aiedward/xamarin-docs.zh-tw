---
title: 傾斜轉換
description: 本文說明如何傾斜轉換時，可以在 SkiaSharp，建立傾斜的圖形物件，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: FDD16186-E3B7-4FF6-9BC2-8A2974BFF616
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: ecb07c69b7720f77401bf9bf454ee4b0248ad238
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113816"
---
# <a name="the-skew-transform"></a>傾斜轉換

_了解如何傾斜轉換時，可以在 SkiaSharp 建立傾斜的圖形物件_

SkiaSharp，在傾斜轉換會將圖形化的物件，例如此映像中的 「 影子：

![](skew-images/skewexample.png "扭曲扭曲陰影文字程式範例")

扭曲變成平行四邊形的矩形，但扭曲的橢圓仍是橢圓形。

雖然 Xamarin.Forms 定義平移、 縮放和旋轉的屬性，沒有任何對應的屬性在 Xamarin.Forms 中的扭曲。

[ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(System.Single,System.Single))方法`SKCanvas`接受兩個引數，水平扭曲和垂直扭曲：

```csharp
public void Skew (Single xSkew, Single ySkew)
```

第二個[ `Skew` ](xref:SkiaSharp.SKCanvas.Skew(SkiaSharp.SKPoint))方法會結合在單一的這些引數`SKPoint`值：

```csharp
public void Skew (SKPoint skew)
```

不過，它是不太可能，您要使用這些兩種方法之一在隔離狀態。

**扭曲實驗**網頁可讓您試驗扭曲值 – 10 和 10 之間的範圍。 文字字串位於左上角的頁面上，從兩個取得的扭曲值`Slider`項目。 以下是`PaintSurface`中的處理常式[ `SkewExperimentPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewExperimentPage.xaml.cs)類別：

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

值`xSkew`引數轉換底部的權限的正數值的文字或負值的左邊。 值`ySkew`向下移動在文字右邊，正值或負值註冊：

[![](skew-images/skewexperiment-small.png "扭曲實驗 頁面上的三個螢幕擷取畫面")](skew-images/skewexperiment-large.png#lightbox "的扭曲實驗 頁面上的三個螢幕擷取畫面")

如果`xSkew`值為負`ySkew`值，結果是旋轉，但也會調整為某種程度的 UWP 顯示表示。

轉換公式如下所示：

x' = x + xSkew ·y

y' = ySkew ·x + y

例如，對於正`xSkew`值，轉換`x'`值會隨著`y`會增加。 這是什麼情況導致傾斜。

如果三角形 200 像素寬及 100 像素高的左上角的點 （0，0） 位於，而且以呈現`xSkew`; 值為 1.5，平行四邊形結果如下：

![](skew-images/skeweffect.png "對矩形的傾斜轉換效果")

下邊緣的座標的`y`值為 100，因此它是向右移位 150 個像素。

非零值`xSkew`或`ySkew`，只有點 （0，0） 維持不變。 該時間點可以視為扭曲的中心。 若要為其他項目扭曲的中心 （這通常是如此），沒有任何`Skew`提供的方法。 您必須明確地結合`Translate`呼叫`Skew`呼叫。 要置在扭曲`px`和`py`，進行下列呼叫：

```csharp
canvas.Translate(px, py);
canvas.Skew(xSkew, ySkew);
canvas.Translate(-px, -py);
```

複合轉換公式如下：

x' = x + xSkew ·(y-py)

y' = ySkew ·(x – px) + y

如果`ySkew`為零，則`px`不會使用值。 值為無關，以及同樣`ySkew`和`py`。

您可能會覺得較喜歡指定扭曲角度的傾斜，例如在此圖中的角度 α 為項目：

![](skew-images/skewangleeffect.png "表示矩形的傾斜角度的傾斜轉換效果")

150 像素轉換到 100 像素垂直的比例為在此範例中的該角度的正切 56.3 度。

XAML 檔案**扭曲角度實驗**頁面大致**扭曲角度**頁面上，不同之處在於`Slider`項目範圍是從一來-90 度到 90 度。 [ `SkewAngleExperiment` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SkewAngleExperimentPage.xaml.cs)程式碼後置檔案中心頁面上的文字，並使用`Translate`設定頁面的中央扭曲的中心。 簡短`SkewDegrees`底端的程式碼的方法將轉換的角度來扭曲值：

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

角度接近正面或負面 90 度，正切函數接近無限大，但最多約 80 度左右的角度可用：

[![](skew-images/skewangleexperiment-small.png "扭曲角度實驗 頁面上的三個螢幕擷取畫面")](skew-images/skewangleexperiment-large.png#lightbox "的扭曲角度實驗 頁面上的三個螢幕擷取畫面")

小負水平扭曲可以模擬傾斜或斜體文字，作為**傾斜的文字**頁面會示範。 [ `ObliqueTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ObliqueTextPage.cs)類別示範如何進行：

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

`TextAlign`的屬性`SKPaint`設定為`Center`。 沒有任何轉換，`DrawText`呼叫與座標 （0，0） 則在左上角的位置與水平中央基準線的文字。 `SkewDegrees`扭曲文字水平方式相對於基準的 20 度。 `Translate`呼叫移至畫布的正中央的水平置中的文字的基準：

[![](skew-images/obliquetext-small.png "傾斜的文字頁面的三個螢幕擷取畫面")](skew-images/obliquetext-large.png#lightbox "傾斜的文字頁面的三個螢幕擷取畫面")

**扭曲陰影文字**頁面會示範如何使用 45 度扭曲，垂直縮放比例的組合讓文字遠離使傾斜的文字陰影。 以下是相關一部分`PaintSurface`處理常式：

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

陰影是先顯示和文字：

[![](skew-images/skewshadowtext1-small.png "扭曲陰影效果的文字頁面的三個螢幕擷取畫面")](skew-images/skewshadowtext1-large.png#lightbox "扭曲陰影效果的文字頁面的三個螢幕擷取畫面")

垂直座標傳遞給`DrawText`方法指示的文字基準線的相對位置。 這就是相同的垂直座標，用來扭曲的中心。 如果文字字串中包含的伸尾部分，這項技術將無法運作。 比方說，「 陰影 」 取代 「 怪"這個字，結果如下：

[![](skew-images/skewshadowtext2-small.png "替代的文字高度扭曲陰影效果的文字頁面的三個螢幕擷取畫面")](skew-images/skewshadowtext2-large.png#lightbox "替代的文字高度扭曲陰影效果的文字頁面的三個螢幕擷取畫面")

陰影和文字仍然在基準線，對齊，但效果就看起來不正確。 若要修正此問題，您需要取得文字範圍：

```csharp
SKRect textBounds = new SKRect();
textPaint.MeasureText(text, ref textBounds);
```

`Translate`呼叫需要高度的伸尾部分來調整：

```csharp
canvas.Translate(xText, yText + textBounds.Bottom);
canvas.Skew((float)Math.Tan(-Math.PI / 4), 0);
canvas.Scale(1, 3);
canvas.Translate(-xText, -yText - textBounds.Bottom);
```

現在，陰影始於這些的伸尾部分的底部：

[![](skew-images/skewshadowtext3-small.png "扭曲陰影效果的文字頁面，並進行調整的伸尾部分的三個螢幕擷取畫面")](skew-images/skewshadowtext3-large.png#lightbox "扭曲陰影效果的文字頁面，並進行調整的伸尾部分的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
