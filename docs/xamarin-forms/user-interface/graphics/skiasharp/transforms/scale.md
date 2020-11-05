---
title: 縮放轉換
description: Thhis 文章會探索將物件縮放為各種大小的 SkiaSharp 規模轉換，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 56254d4a5d255f2db05ff82f224df4ddff04ab1f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373090"
---
# <a name="the-scale-transform"></a>縮放轉換

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 規模轉換以將物件縮放為各種大小_

如您在「 [**轉譯轉換**](translate.md) 」一文所見，「轉譯」轉換可以將繪圖物件從一個位置移到另一個位置。 相反地，尺規轉換會變更繪圖物件的大小：

![大小調整的高文字](scale-images/scaleexample.png)

調整規模轉換通常會導致圖形座標在變大時移動。

稍早，您會看到兩個轉換公式，描述轉譯因素 `dx` 和的結果 `dy` ：

x ' = x + dx

y ' = y + dy

和的縮放 `sx` 比例 `sy` 是乘法，而不是加法：

x ' = sx ·X

y ' = sy ·Y

轉譯因數的預設值為 0;縮放因數的預設值為1。

`SKCanvas`類別會定義四個 `Scale` 方法。 第一個 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) 方法適用于您需要相同水準和垂直縮放比例的案例：

```csharp
public void Scale (Single s)
```

這就是所謂的 *isotropic* 調整 &mdash; 規模調整，雙向調整也相同。 Isotropic 縮放會保留物件的外觀比例。

第二 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) 種方法可讓您指定不同的水準和垂直縮放值：

```csharp
public void Scale (Single sx, Single sy)
```

這 *會導致下一項調整* 。
第三 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) 個方法會在單一值中結合兩個調整因數 `SKPoint` ：

```csharp
public void Scale (SKPoint size)
```

第四個 `Scale` 方法將于稍後說明。

[ **基本規模** ] 頁面會示範 `Scale` 方法。 [**BasicScalePage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml)檔案包含兩個 `Slider` 元素，可讓您選取介於0和10之間的水準和垂直縮放比例。 [**BasicScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)程式碼後端檔案會先使用這些值來呼叫，然後 `Scale` 才會顯示以虛線繪製的圓角矩形，並將其大小調整為符合畫布左上角的一些文字：

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

您可能會感到好奇：調整因數如何影響的方法所傳回的值 `MeasureText` `SKPaint` ？ 答案是：完全不是。 `Scale` 是的方法 `SKCanvas` 。 它不會影響您對物件所做的任何動作， `SKPaint` 直到您使用該物件在畫布上轉譯某個東西為止。

如您所見，在通話之後繪製的所有專案 `Scale` 會按比例增加：

[![基本規模頁面的三重螢幕擷取畫面](scale-images/basicscale-small.png)](scale-images/basicscale-large.png#lightbox "基本規模頁面的三重螢幕擷取畫面")

文字、虛線的寬度、該行的虛線長度、角落的舍入，以及畫布左邊緣和圓角矩形之間的10圖元邊界，全都受相同的縮放比例所影響。

> [!IMPORTANT]
> 通用 Windows 平臺不會正確地轉譯 anisotropicly 縮放文字。

相較于水準軸和垂直軸，非等向調整會使筆劃寬度變成不同的線條。  (這也可以從這個頁面上的第一個影像看出。如果您不希望筆劃寬度受到縮放比例的影響，請將它設定為0，不論設定為何，一律會有一個圖元寬度 ) 。 `Scale`

調整是相對於畫布的左上角。 這可能正是您想要的，但可能不是。 假設您想要將文字和矩形放在畫布上的其他位置，而您想要將它與中心的相對比例相對應。 在此情況下，您可以使用第四個版本的 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) 方法，其中包含兩個額外的參數來指定調整的中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px`和 `py` 參數定義了有時稱為 *縮放中心* 的點，但在 SkiaSharp 檔中稱為「 *資料透視點* 」。 這是相對於畫布左上角的一點，不受縮放影響。 所有調整都會相對於該中心進行。

[**中央規模**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)頁面會顯示其運作方式。 此 `PaintSurface` 處理常式類似于 **基本的調整** 程式，不同之處在于 `margin` 會計算值以水準置中文字，這表示程式在直向模式中的效果最佳：

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

圓角矩形左上角的位置是 `margin` 從畫布左邊的圖元，而從頂端算算的圖元 `margin` 。 方法的最後兩個引數 `Scale` 會設定為這些值加上文字的寬度和高度，也就是圓角矩形的寬度和高度。 這表示所有調整都是相對於該矩形的中心：

[![中央縮放頁面的三重螢幕擷取畫面](scale-images/centeredscale-small.png)](scale-images/centeredscale-large.png#lightbox "中央縮放頁面的三重螢幕擷取畫面")

`Slider`此程式中的元素範圍為 &ndash; 10 到10。 如您所見，垂直縮放 (的負數值（例如，在中央的 Android 畫面上) 會導致物件沿著水準軸翻轉，以通過縮放的中心。 水準縮放 (的負數值（例如在) 右側的 UWP 螢幕中）會使物件沿著垂直軸翻轉，以通過縮放的中心。

[`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single))具有 pivot 點之方法的版本是一系列三個和呼叫的快捷 `Translate` 方式 `Scale` 。 您可能會想要查看其運作方式，方法是 `Scale` 使用下列內容取代 **中央調整** 頁面中的方法：

```csharp
canvas.Translate(-px, -py);
```

這些是 pivot 點座標的否定。

現在再次執行程式。 您會看到矩形和文字會移動，讓中央位於畫布的左上角。 您幾乎可以看到它。 滑杆無法正常運作，因為現在程式完全不會進行調整。

現在 `Scale` ，在呼叫 *之前* ，新增基本呼叫 (沒有縮放中心) `Translate` ：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您已經熟悉其他圖形程式設計系統中的這個練習，您可能會認為這是錯誤的，但不是。 Skia 會處理後續的轉換呼叫，與您可能熟悉的方式稍有不同。

在後續 `Scale` 和 `Translate` 呼叫之後，圓角矩形的中心仍然位於左上角，但您現在可以將它調整為相對於畫布左上角的位置，也就是圓角矩形的中心。

現在，在 `Scale` 呼叫之前，先新增另一個具有置中 `Translate` 值的呼叫：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

這會將縮放的結果移回原始位置。 這三個呼叫相當於：

```csharp
canvas.Scale(sx, sy, px, py);
```

個別的轉換很複雜，因此轉換公式總計為：

 x ' = sx · (x-px) + px

 y ' = sy · (y – .py) + .py

請記住，和的預設值 `sx` `sy` 是1。 您可以輕鬆地說服資料透視點 (px、.py) 不會被這些公式轉換。 它會保留在相對於畫布的相同位置。

當您結合 `Translate` 和 `Scale` 呼叫時，順序會很重要。 如果是在 `Translate` 之後 `Scale` ，則轉譯因數會有效地以縮放星號調整。 如果是在 `Translate` 之前 `Scale` ，則不會調整轉譯因數。 這項程式變得更清楚， (雖然引進了轉換矩陣的主旨，但有更高的數學) 。

`SKPath`類別會定義唯讀 [`Bounds`](xref:SkiaSharp.SKPath.Bounds) 屬性，此屬性會傳回 `SKRect` 定義路徑中座標範圍的。 例如，當 `Bounds` 屬性是從稍早建立的 hendecagram 路徑取得時， `Left` 矩形的和 `Top` 屬性大約是-100， `Right` 而和屬性大約是 `Bottom` 100，而且 `Width` 和 `Height` 屬性大約是200。  (大部分的實際值都比較少，因為星形的點是由半徑為100的圓形所定義，但最大點則是平行于水準或垂直軸。 ) 

這項資訊的可用性意味著應該可以衍生適合用來調整畫布大小之路徑的調整和轉譯因素。 [非等向 [**調整**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) ] 頁面會以11指向的星星來示範這一點。 非 *等向比例* 表示它不會以水準和垂直方向，這表示星號不會保留其原始外觀比例。 以下是處理常式中的相關 `PaintSurface` 程式碼：

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

`pathBounds`矩形會在此程式碼的頂端取得，然後在稍後搭配呼叫中畫布的寬度和高度使用 `Scale` 。 當呼叫轉譯路徑時，該呼叫本身將會調整路徑的座標， `DrawPath` 但星號將會在畫布右上角置中。 它必須向下和向下移動。 這是呼叫的作業 `Translate` 。 這兩個屬性 `pathBounds` 大約是-100，因此轉譯因素大約是100。 由於 `Translate` 呼叫是在呼叫之後 `Scale` ，因此這些值實際上會以縮放比例進行調整，因此它們會將星號的中心移至畫布的中心：

[![非等向調整頁面的三重螢幕擷取畫面](scale-images/anisotropicscaling-small.png)](scale-images/anisotropicscaling-large.png#lightbox "非等向調整頁面的三重螢幕擷取畫面")

另一種您可以考慮和呼叫的方式， `Scale` `Translate` 就是以反向順序來決定效果：呼叫會將 `Translate` 路徑移位，使其變成完全可見，但在畫布的左上角進行導向。 `Scale`方法接著會讓該星號更大，相對於左上角。

其實星號會比畫布稍微大一點。 問題在於筆觸寬度。 的 `Bounds` 屬性 `SKPath` （property）會指出路徑中編碼之座標的維度，而這就是程式用來調整它的大小。 當路徑以特定筆劃寬度轉譯時，轉譯的路徑會比畫布大。

若要修正這個問題，您必須彌補這個問題。 在這個程式中，一個簡單的方法是在呼叫之前加入下列語句 `Scale` ：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

這會將 `pathBounds` 矩形以1.5 單位增加到四側。 只有在四捨五入筆劃聯結時，才會使用這個合理的解決方案。 斜接聯結可能較長，因此難以計算。

您也可以使用與文字類似的技巧，如同「下型 **文字** 」頁面所示。 以下是 `PaintSurface` 來自類別的處理常式的相關部分 [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) ：

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

它是類似的邏輯，而且文字會根據 (所傳回的文字界限矩形（ `MeasureText` 比實際的文字) 小一點）來展開頁面大小：

[![非等向測試頁面的三重螢幕擷取畫面](scale-images/anisotropictext-small.png)](scale-images/anisotropictext-large.png#lightbox "非等向測試頁面的三重螢幕擷取畫面")

如果您需要保留繪圖物件的外觀比例，您會想要使用 isotropic 調整。 [ **Isotropic 縮放比例** ] 頁面會針對11指向的星星來示範這一點。 從概念上來說，在頁面中央以 isotropic 縮放顯示繪圖物件的步驟如下：

- 將繪圖物件的中心轉譯為左上角。
- 根據水準和垂直頁面維度的最小值，以繪圖物件維度為基礎來縮放物件。
- 將縮放物件的中心轉譯為頁面的中心。

會 [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) 以反向循序執行這些步驟，然後顯示星號：

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

程式碼也會顯示星星10次，每次減少10% 的縮放比例，並逐漸將色彩從紅色變更為藍色：

[![Isotropic 縮放頁面的三重螢幕擷取畫面](scale-images/isotropicscaling-small.png)](scale-images/isotropicscaling-large.png#lightbox "Isotropic 縮放頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)