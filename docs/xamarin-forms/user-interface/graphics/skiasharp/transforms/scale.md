---
title: "小數位數轉換"
description: "探索 SkiaSharp 標尺轉換縮放至各種大小的物件"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 39e2084bf9ca888d6e39fc5f02a455d3500e568c
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="the-scale-transform"></a>小數位數轉換

_探索 SkiaSharp 標尺轉換縮放至各種大小的物件_

如您所見中[轉譯轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)文件轉換轉換可以是圖形化的物件從一個位置移到另一個。 相反地，標尺轉換變更圖形物件的大小：

![](scale-images/scaleexample.png "調整的大小調整成高 word")

標尺轉換也經常會導致移動它們，對較大的圖形座標。

您稍早已描述的翻譯因素的影響的兩個轉換公式`dx`和`dy`:

x' = x + dx

y' = y + dy

調整的因素`sx`和`sy`是乘法，而不是加總：

x' = sx ·x

y' = sy ·y

翻譯因素的預設值為 0;縮放比例的預設值為 1。

`SKCanvas`類別會定義四個`Scale`方法。 第一個[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/)的情況下，當您想要的相同水平和垂直縮放因數，方法是：

```csharp
public void Scale (Single s)
```

這稱為*等方向性*調整&mdash;調整也就相同的兩個方向。 等方向性調整保留物件的長寬比。

第二個[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/)方法可讓您指定不同的水平和垂直縮放比例的值：

```csharp
public void Scale (Single sx, Single sy)
```

這會導致*非等向性*縮放比例。
第三個[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/)方法結合在單一的兩個縮放因數`SKPoint`值：

```csharp
public void Scale (SKPoint size)
```

第四個`Scale`方法會在稍後說明。

**基本比例**頁面示範`Scale`方法。 [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) XAML 檔案包含兩個`Slider`項目，可讓您選取 0 到 10 之間的水平和垂直縮放比例。 [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)程式碼後置檔案會使用這些值來呼叫`Scale`顯示圓角的矩形並以虛線圖案和調整大小以符合在左上方的一些文字之前畫布的邊角：

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

您可能會懷疑： 縮放比例如何影響從傳回的值`MeasureText`方法`SKPaint`？ 解決辦法就是： 完全無法執行。 `Scale` 是一種方法的`SKCanvas`。 不會影響所做的任何項目`SKPaint`物件，直到您使用該物件來轉譯在畫布上的項目。

您可以看到，所有項目後繪製`Scale`按比例呼叫增加：

[![](scale-images/basicscale-small.png "基本的小數位數頁的三個螢幕擷取畫面")](scale-images/basicscale-large.png#lightbox "基本調整] 頁面的三個螢幕擷取畫面")

文字、 虛線的長度的角落和 10 個像素間之邊界的左端和頂端邊緣的畫布和圓角的矩形的圓的該行中連字號的寬度受限於所有相同的縮放比例。

> [!IMPORTANT]
> 通用 Windows 平台無法正確呈現 anisotropicly 縮放的文字。

非等向性調整原因筆劃寬度變成不同的行對齊水平及垂直軸。 （這也是從這個頁面上的第一個影像明顯。）如果您不想筆劃寬度的縮放因數會受到，設為 0，它一律是一個像素寬不論`Scale`設定。

調整為相對於畫布左上角。 這可能是完全您要的結果，但不是可能。 假設您想要放在畫布上的文字和其他位置的矩形，而您想要調整相對於其中心。 在此情況下，您可以使用的第四個版本[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/)方法，其中包含兩個額外的參數，以指定的縮放比例中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px`和`py`參數定義的點，有時也稱為*調整 center*但 SkiaSharp 在文件指*樞紐點*。 這是相對於左上角的縮放比例不會影響畫布的點。 所有的調整，就會發生相對於該中心。

[**置中的小數位數**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)頁面會顯示其運作方式。 `PaintSurface`處理常式會是類似於**基本比例**程式除了`margin`值就會計算成文字的水平置中這表示，程式最適合在直向模式中：

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

圓角矩形的左上角位於`margin`畫布左側的像素和`margin`像素。 最後兩個引數`Scale`方法設定為這些值再加上高度與寬度的文字，這也是圓角矩形的寬度和高度。 這表示所有縮放比例是相對於該矩形的中心：

[![](scale-images/centeredscale-small.png "置中的小數位數頁的三個螢幕擷取畫面")](scale-images/centeredscale-large.png#lightbox "的置中調整] 頁面的三個螢幕擷取畫面")

`Slider`項目，此程式中的範圍是&ndash;10 到 10。 如您所見，垂直調整 （例如在 Android 上畫面中央） 的負值會導致物件翻轉通過縮放的中心水平軸。 水平縮放比例 （例如 Windows 上的螢幕右邊） 的負值會導致翻轉通過縮放中心的垂直軸周圍的物件。

這個第四個版本的`Scale`方法是實際的捷徑。 您可能想要查看其運作所取代的方式`Scale`在此程式碼以下列方法：

```csharp
canvas.Translate(-px, -py);
```

這些是樞紐分析點座標的否定。

現在再次執行程式。 您會看到以便畫布左上角的中心] 是都要移動的矩形和文字。 您幾乎可以看到它。 滑桿不當然運作，因為現在程式不會完全擴充。

現在，加入基本`Scale`呼叫 （而不需要調整的中心）*之前*，`Translate`呼叫：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您熟悉此練習中其他圖形程式設計系統，您可能會認為這是錯誤，但它不是。 Skia 處理後續轉換呼叫的方式稍有不同從您可能的熟悉。

以後續`Scale`和`Translate`呼叫、 圓角矩形的中心仍然在左上角，但是您現在可以調整其相對於畫布，同時也是中央圓角矩形的左上角。

現在，之前會先`Scale`呼叫加入另一個`Translate`呼叫置中顯示的值：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

這會調整的結果設為原始位置。 三個呼叫會相當於：

```csharp
canvas.Scale(sx, sy, px, py);
```

個別的轉型會更嚴重，如此總轉換公式是：

 x' = sx ·(x – px) + 像素

 y' = sy ·(y – py) + py

請注意，預設值`sx`和`sy`都是 1。 所以可以輕鬆地說服自己的樞紐分析點 （像素、 py） 不會受到轉換這些公式。 它會保留在相同的位置，相對於畫布。

當您結合`Translate`和`Scale`呼叫順序很重要的。 如果`Translate`之後`Scale`，轉譯因素會有效地調整縮放比例。 如果`Translate`前面`Scale`，縮放不是轉譯因素。 此程序會成為稍微更清楚 (雖然更數學) 時導入了轉換矩陣的主旨。

`SKPath`類別定義的唯讀[ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/)屬性，傳回`SKRect`座標的範圍定義的路徑中。 例如，當`Bounds`屬性取自稍早建立的 hendecagram 路徑`Left`和`Top`矩形屬性是大約 – 100，`Right`和`Bottom`屬性大約 100，而`Width`和`Height`屬性都是大約為 200。 （因為星星的點會由圓形半徑為 100，但只有最上層的點是水平或垂直軸與平行處理大部分的實際值是較小）。

這項資訊的可用性，表示它應該是可衍生小數位數，並轉譯因素適用於調整的路徑畫布的大小。 [**非等向性調整**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs)頁可示範這 11 星形。 *非等向性*小數位數表示它是不相等之水平和垂直方向，這表示星形將不會保留其原始長寬比。 以下是相關的程式碼`PaintSurface`處理常式：

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

`pathBounds`矩形是取得最上方的 [這個程式碼，並再搭配稍後在畫布上的高度與寬度`Scale`呼叫。 單獨使用時的呼叫會調整路徑的座標所呈現時`DrawPath`呼叫但星形將置於畫布的右上角。 它需要往下及往左移位。 這是作業的`Translate`呼叫。 這兩個屬性的`pathBounds`位於大約 – 100，因此轉譯因素約 100 個。 因為`Translate`呼叫之後`Scale`呼叫時，這些值會有效地縮放調整的因素，使其在畫布的正移動星形的中心：

[![](scale-images/anisotropicscaling-small.png "非等向性調整頁面的三個螢幕擷取畫面")](scale-images/anisotropicscaling-large.png#lightbox "非等向性調整頁面的三個螢幕擷取畫面")

另一種方式可以思考`Scale`和`Translate`呼叫是要判斷在反向序列的效果：`Translate`呼叫移位路徑，讓它成為完整可見但導向畫布左上角。 `Scale`方法然後讓該星號左上角相對較大。

實際上，它會顯示星號是稍微大於畫布。 問題在於筆劃寬度。 `Bounds`屬性`SKPath`指出座標的維度編碼在路徑中，而這就是程式會使用它調整。 在特定的筆劃寬度轉譯路徑時，呈現的路徑。 大於畫布。

若要修正此問題，您需要補償的。 此程式中的其中一個簡單方法是新增下列陳述式權限，然後再`Scale`呼叫：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

這會增加`pathBounds`1.5 單位四邊的矩形。 這是合理的解決方案只有時筆劃聯結會捨入。 斜接角聯結可以是較長，而且很難計算。

您也可以使用類似的技術，包含文字，做為**非等向性文字**頁面示範。 以下是相關的部分`PaintSurface`處理常式[ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs)類別：

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

它是類似的邏輯，而且文字會展開，以根據從傳回的文字範圍矩形的頁面大小`MeasureText`（此為稍微大於實際的文字）：

[![](scale-images/anisotropictext-small.png "非等向性測試頁的三個螢幕擷取畫面")](scale-images/anisotropictext-large.png#lightbox "非等向性測試頁的三個螢幕擷取畫面")

如果您需要保留外觀比例的圖形物件，您要使用等方向性的比例。 **等方向性調整**頁面示範如何執行這 11 星形。 就概念而言，等方向性調整頁面的中央顯示圖形物件的步驟如下：

- 轉換中心的左上角的圖形化物件。
- 調整水平及垂直頁面尺寸除以圖形物件維度的最小值為基礎的物件。
- 轉換中心的 [縮放至頁面的中心的物件。

[ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)之前顯示星號以相反順序執行下列步驟：

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

程式碼也會顯示星號十次，每次減少縮放因數 10%，並逐漸將色彩變更為藍色的紅色從：

[![](scale-images/isotropicscaling-small.png "等方向性調整頁面的三個螢幕擷取畫面")](scale-images/isotropicscaling-large.png#lightbox "等方向性調整頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
