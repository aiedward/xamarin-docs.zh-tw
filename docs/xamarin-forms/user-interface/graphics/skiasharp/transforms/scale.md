---
title: 縮放轉換
description: 本例文章會探索將物件調整為各種大小的 SkiaSharp 縮放轉換，並使用範例程式碼示範這項功能。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
ms.openlocfilehash: 7dc7a2faabef86aa63b52739edda696efcb54aba
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291890"
---
# <a name="the-scale-transform"></a>縮放轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索將物件調整成各種大小的 SkiaSharp 縮放轉換_

如您在[**翻譯轉換**](translate.md)一文中所見，翻譯轉換可以將繪圖物件從一個位置移到另一個位置。 相反地，尺規轉換會變更繪圖物件的大小：

![](scale-images/scaleexample.png "A tall word scaled in size")

縮放轉換通常也會導致圖形座標在變大時移動。

稍早您已看到兩個轉換公式，描述 `dx` 和 `dy`的轉譯因素的影響：

x ' = x + dx

y ' = y + dy

`sx` 和 `sy` 的縮放因素是乘法，而不是加法相加：

x ' = sx ·x

y ' = sy ·y

轉譯因數的預設值為 0;尺規因數的預設值為1。

`SKCanvas` 類別會定義四個 `Scale` 方法。 第一個[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single))方法適用于您想要相同水準和垂直縮放比例的情況：

```csharp
public void Scale (Single s)
```

這也稱為*isotropic*調整 &mdash; 縮放比例，這兩個方向都相同。 Isotropic 縮放會保留物件的長寬比。

第二個[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single))方法可讓您指定不同的水準和垂直縮放值：

```csharp
public void Scale (Single sx, Single sy)
```

這會導致*進行非*進行中的調整。
第三個[`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint))方法會將兩個調整因數結合成單一 `SKPoint` 值：

```csharp
public void Scale (SKPoint size)
```

第四個 `Scale` 方法將會在稍後說明。

[**基本調整**] 頁面會示範 `Scale` 方法。 [**BasicScalePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml)包含兩個 `Slider` 元素，可讓您選取介於0到10之間的水準和垂直縮放比例。 [**BasicScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)程式碼後置檔案會使用這些值來呼叫 `Scale`，然後才顯示以虛線繪製的圓角矩形，並調整大小以符合畫布左上角的某些文字：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

您可能會想：調整因數如何影響從 `SKPaint`的 `MeasureText` 方法傳回的值？ 答案是：完全不是。 `Scale` 是 `SKCanvas`的方法。 它不會影響您使用 `SKPaint` 物件所做的任何動作，除非您使用該物件在畫布上呈現內容。

如您所見，在 `Scale` 呼叫之後繪製的所有專案會按比例增加：

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

文字、虛線的寬度、該線條中的虛線長度、角落的圓角，以及畫布左邊和上邊緣和圓角矩形之間的10圖元邊界，全都受相同的縮放比例因素所影響。

> [!IMPORTANT]
> 通用 Windows 平臺未正確呈現 anisotropicly 縮放的文字。

對水準和垂直軸對齊的線條而言，非等向調整會使筆劃寬度變得不同。 （這也可以從這個頁面上的第一個影像看出）。如果您不想讓筆劃寬度受到縮放因素的影響，請將它設定為0，不論 `Scale` 設定為何，一律會是一個圖元寬。

縮放比例相對於畫布的左上角。 這可能正是您想要的，但可能不是。 假設您想要將文字和矩形放在畫布上的某處，而您想要將它相對於其中心進行調整。 在此情況下，您可以使用第四版的[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))方法，其中包含兩個額外的參數來指定調整的中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px` 和 `py` 參數定義的點有時稱為「*縮放中心*」，但在 SkiaSharp 檔中稱為「*資料透視點*」。 這是相對於畫布左上角且不受縮放影響的點。 所有調整都會相對於該中心進行。

[[**中央尺規**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)] 頁面會顯示其運作方式。 `PaintSurface` 處理常式與**基本 Scale**程式相似，不同之處在于 `margin` 值會計算為水準置中文字，這表示程式在直向模式中的效果最佳：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

圓角矩形的左上角位於畫布左邊 `margin` 圖元，而從頂端 `margin` 圖元。 `Scale` 方法的最後兩個引數會設定為這些值加上文字的寬度和高度，也就是圓角矩形的寬度和高度。 這表示所有調整都是相對於該矩形的中心：

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

此程式中的 `Slider` 元素的範圍為 &ndash;10 到10。 如您所見，垂直縮放比例的負值（例如，在中央的 Android 螢幕上）會導致物件沿著水準軸翻轉，而其會通過縮放中心。 水準縮放的負數值（例如右側 UWP 畫面中的）會導致物件沿著垂直軸翻轉，而這會通過縮放中心。

具有資料透視點的[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))方法版本，是一系列三個 `Translate` 和 `Scale` 呼叫的快捷方式。 您可能想要查看其運作方式，方法是以下列內容取代 [**中央尺規**] 頁面中的 [`Scale`] 方法：

```csharp
canvas.Translate(-px, -py);
```

這些是資料透視點座標的否定。

現在再次執行程式。 您會看到矩形和文字已移動，使中心位於畫布的左上角。 您幾乎不會看到它。 滑杆並不適用，因為現在程式根本不會進行調整。

現在，在該 `Translate` 呼叫*之前*，新增基本 `Scale` 呼叫（不含縮放中心）：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您在其他圖形程式設計系統中熟悉此練習，您可能會認為這是錯誤的，但卻不是。 Skia 會處理後續的轉換呼叫，與您可能熟悉的方式稍有不同。

在後續的 `Scale` 和 `Translate` 呼叫中，圓角矩形的中心仍在左上角，但您現在可以相對於畫布的左上角（也就是圓角矩形的中心）調整它的大小。

現在，在 `Scale` 呼叫之前，請先新增另一個 `Translate` 呼叫，並將其置中值：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

這會調整的結果設為原始位置。 這三個呼叫相當於：

```csharp
canvas.Scale(sx, sy, px, py);
```

個別轉換會複合，讓總轉換公式為：

 x ' = sx ·（x – px） + px

 y ' = sy ·（y – .py） + .py

請記住，`sx` 和 `sy` 的預設值是1。 您很容易就能相信，這些公式不會轉換資料透視點（px，.py）。 它會保留在與畫布相對的相同位置。

當您結合 `Translate` 和 `Scale` 呼叫時，順序會很重要。 如果 `Translate` 是在 `Scale`之後，則轉譯因數會以縮放比例有效地縮放。 如果 `Translate` 在 `Scale`之前，則不會調整轉譯因素。 當引進了轉換矩陣的主旨時，此程式會變得更清楚（雖然更是數學）。

`SKPath` 類別定義一個唯讀的[`Bounds`](xref:SkiaSharp.SKPath.Bounds)屬性，它會傳回定義路徑中座標範圍的 `SKRect`。 例如，當 `Bounds` 屬性是從稍早建立的 hendecagram 路徑取得時，矩形的 `Left` 和 `Top` 屬性大約是–100，`Right` 和 `Bottom` 屬性大約是100，而 `Width` 和 `Height` 屬性大約是200。 （大部分的實際值都比較小，因為星星的點是由半徑為100的圓形所定義，但是最大的點則是與水準或垂直軸平行處理）。

這項資訊的可用性意味著應該能夠衍生適合用來調整畫布大小之路徑的縮放和轉譯因素。 [非等向[**調整**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs)] 頁面會使用11個指向的星形來示範這一點。 「*上階」* 尺規表示其水準和垂直方向不相等，這表示星形不會保留原始的外觀比例。 以下是 `PaintSurface` 處理常式中的相關程式碼：

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

`pathBounds` 矩形會在此程式碼頂端附近取得，然後在稍後使用 `Scale` 呼叫中畫布的寬度和高度。 該呼叫本身會在 `DrawPath` 呼叫轉譯時調整路徑的座標，但星形會以畫布的右上角為中心。 它需要向下和向左移動。 這是 `Translate` 呼叫的工作。 `pathBounds` 的兩個屬性大約是–100，因此轉譯因素大約是100。 因為 `Translate` 呼叫是在 `Scale` 呼叫之後，所以這些值會以縮放比例有效地調整，因此會將星形的中心移至畫布的中央：

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

另一種您可以思考 `Scale` 和 `Translate` 呼叫的方法，是以反向順序來判斷效果： `Translate` 呼叫會移動路徑，使其變成完全可見，但會在畫布的左上角導向。 然後 `Scale` 方法會使該星形相對於左上角更大。

其實星形似乎比畫布小一點。 問題是筆觸寬度。 `SKPath` 的 `Bounds` 屬性會指出在路徑中編碼的座標維度，這就是程式用來調整它的方式。 當以特定筆觸寬度轉譯路徑時，轉譯的路徑會大於畫布。

若要修正此問題，您必須對該進行補償。 在此程式中，有一個簡單的方法，就是在 `Scale` 呼叫之前，新增下列語句：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

這會將 [`pathBounds`] 矩形增加到四個邊的1.5 個單位。 只有當筆劃聯結已進位時，這才是合理的解決方案。 斜切聯結可能較長，而且很容易計算。

您也可以搭配文字使用類似的技術，如 [非結合性**文字**] 頁面所示。 以下是來自[`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs)類別的 `PaintSurface` 處理常式的相關部分：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

這是類似的邏輯，而且文字會根據從 `MeasureText` 傳回的文字界限矩形（比實際的文字小一點），展開至頁面的大小：

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

如果您需要保留繪圖物件的長寬比，您會想要使用 isotropic 縮放。 **Isotropic 調整**頁面會針對11個指向的星號示範這一點。 在概念上，使用 isotropic 縮放在頁面中央顯示繪圖物件的步驟如下：

- 將繪圖物件的中央轉譯為左上角。
- 根據水準和垂直頁面尺寸的最小值，以繪圖物件維度來縮放物件。
- 將縮放物件的中央轉譯為頁面的中央。

[`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)在顯示星星之前，會以反向循序執行這些步驟：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

此程式碼也會顯示星號10次，每次減少10% 的縮放比例，並逐漸將色彩從紅色變更為藍色：

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
