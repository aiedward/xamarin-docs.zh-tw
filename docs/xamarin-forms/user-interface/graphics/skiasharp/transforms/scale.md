---
title: 縮放轉換
description: 即文章將探討 SkiaSharp 縮放轉換的縮放比例，各種規模的物件，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 94105cbb83e4c6eb3558ca3fc55e505ab41f28fe
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615599"
---
# <a name="the-scale-transform"></a>縮放轉換

_探索 SkiaSharp 縮放轉換，調整以各種不同大小的物件_

如您所見中[轉譯轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/translate.md)文件： 平移轉換可以圖形物件從一個位置移到另一個。 相反地，縮放轉換變更圖形化的物件的大小：

![](scale-images/scaleexample.png "高文字的大小調整")

縮放轉換也經常會導致移動了較大的圖形座標。

您稍早看到兩個描述的翻譯因素的影響的轉換公式`dx`和`dy`:

x' = x + dx

y' = y + dy

調整的因素`sx`和`sy`是乘法，而不是加總：

x' = sx ·x

y' = sy ·y

Translate 因素的預設值為 0;縮放比例的預設值為 1。

`SKCanvas`類別會定義四個`Scale`方法。 第一個[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/)方式的情況下，當您想要相同水平和垂直縮放因素：

```csharp
public void Scale (Single s)
```

這就所謂*dbi*100*調整&mdash;調整也就相同的兩個方向。 Dbi*100 調整保留物件的長寬比。

第二個[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/)方法可讓您指定的水平和垂直縮放的不同值：

```csharp
public void Scale (Single sx, Single sy)
```

這會導致*非等向性*縮放比例。
第三個[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/SkiaSharp.SKPoint/)方法會結合兩個的縮放比例，在單一`SKPoint`值：

```csharp
public void Scale (SKPoint size)
```

第四個`Scale`將短時間內所述方法。

**基本的擴展**頁面會示範`Scale`方法。 [ **BasicScalePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) XAML 檔案包含兩個`Slider`元素，讓您選取介於 0 到 10 之間的水平和垂直縮放比例。 [ **BasicScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs)程式碼後置檔案會使用這些值來呼叫`Scale`顯示圓角的矩形圖案並以虛線和調整大小以符合在左上角中的某些文字之前邊角的畫布：

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

您可能會想知道： 縮放比例如何影響從傳回的值`MeasureText`方法的`SKPaint`？ 答案是： 不完全。 `Scale` 是一種方法的`SKCanvas`。 它不會影響您做的任何項目`SKPaint`物件，直到您使用該物件來轉譯在畫布上的項目。

如您所見，所有項目後繪製`Scale`按比例呼叫增加：

[![](scale-images/basicscale-small.png "基本的調整規模 頁面的三個螢幕擷取畫面")](scale-images/basicscale-large.png#lightbox "的基本的調整規模 頁面的三個螢幕擷取畫面")

文字，虛線的長度，邊角和 10 個像素間之邊界的左邊和頂端邊緣的畫布和圓角的矩形的捨入的該行中連字號的寬度受限於所有相同的縮放比例。

> [!IMPORTANT]
> 通用 Windows 平台不會正確轉譯 anisotropicly 縮放的文字。

非等向性調整原因變成不同的行筆觸寬度與對齊水平和垂直軸。 （這也是明顯的是從第一個映像，在此頁面上。）如果您不想筆觸粗細會受到縮放比例，將它設定為 0 和它一律為一個像素寬不論`Scale`設定。

調整為相對於畫布左上角。 這可能是完全您要的結果，但它可能不是。 假設您想要放置在畫布上的文字和矩形的任一處，而您想要調整相對於其中心點。 在此情況下，您可以使用的第四個新版[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/System.Single/System.Single/)方法，其中包含兩個額外的參數來指定縮放的中心：

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

`px`並`py`參數定義的點，有時也稱為*縮放中心*但 SkiaSharp 在文件指*樞紐點*。 這是相對於左上角的點不會受到縮放比例的畫布。 所有縮放比例就會發生相對於該中心。

[**置中對齊的縮放比例**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs)頁面會顯示其運作方式。 `PaintSurface`處理常式是類似**基本的擴展**程式不同之處在於`margin`值計算將文字置中以水平的方式，這表示程式適合在直向模式：

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

圓角矩形的左上角位於`margin`從畫布左邊的像素和`margin`從頂端的像素為單位。 最後兩個引數`Scale`方法會設定為這些值再加上高度與寬度的文字，這也是圓角矩形的寬度和高度。 這表示所有縮放比例是相對於該矩形的中心：

[![](scale-images/centeredscale-small.png "置中對齊的調整規模 頁面的三個螢幕擷取畫面")](scale-images/centeredscale-large.png#lightbox "的置中對齊的調整規模 頁面的三個螢幕擷取畫面")

`Slider`在這個程式中的項目有不同的&ndash;10 到 10。 如您所見，負值的垂直縮放比例 （例如在 Android 上畫面中間） 會導致要翻轉繞著水平軸縮放的中心為通過的物件。 負值的水平調整 （例如 [UWP] 畫面右側） 導致翻轉繞著垂直軸縮放的中心為通過的物件。

這個第四個版本的`Scale`方法實際上是捷徑。 您可能要查看其運作方式取代`Scale`中此程式碼使用下列方法：

```csharp
canvas.Translate(-px, -py);
```

這些都是否定的樞紐分析點座標。

現在再次執行程式。 您會看到矩形和文字方塊，會移位使中央畫布左上角。 您幾乎可以看到它。 滑桿不當然適用，因為現在程式無法完全擴充。

現在，加入基本`Scale`（不含縮放的中心） 呼叫*之前*的`Translate`呼叫：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

如果您已熟悉此練習中其他圖形程式設計系統中，您可能會認為這是錯誤，但不是。 Skia 後續轉換呼叫有點以不同方式處理來自您可能的熟悉。

使用連續`Scale`和`Translate`呼叫、 圓角矩形的中心是仍在左上角，但您現在可以調整其相對於左上角的畫布，這也是圓角矩形的中心。

現在，是在那之前`Scale`呼叫新增另一個`Translate`呼叫置中顯示的值：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

這會調整的結果設為原始位置。 三個呼叫會相當於：

```csharp
canvas.Scale(sx, sy, px, py);
```

個別的轉換是複合性的因此總轉換公式是：

 x' = sx ·(x – px) + 像素

 y' = sy ·(y-py) + py

請記住，預設值`sx`和`sy`都是 1。 它很容易就能確信這些公式無法轉換的樞紐分析點 （像素，py）。 它會保留在相同的位置，相對於在畫布。

當您合併`Translate`和`Scale`呼叫，順序會影響結果。 如果`Translate`後出現`Scale`，轉譯因素會有效地調整縮放比例。 如果`Translate`前面`Scale`，轉譯因素不會進行縮放。 此程序會稍微比較清楚 (雖然更數學) 時引進轉換矩陣的主旨。

`SKPath`類別會定義唯讀[ `Bounds` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.Bounds/)屬性，傳回`SKRect`座標的範圍定義的路徑中。 例如，當`Bounds`屬性取自稍早建立的 hendecagram 路徑`Left`並`Top`矩形的內容是大約 – 100，`Right`和`Bottom`屬性大約 100，而`Width`和`Height`屬性都是大約 200。 （因為星星的點由半徑為 100 的圓形，但只有最上層的重點是平行具有水平或垂直軸大部分的實際值是一些較低）。

這項資訊的可用性表示，應該可以衍生小數位數，並轉譯因素適用於調整畫布大小的路徑。 [**非等向性調整**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs)頁可示範這 11 星形。 *非等向性*擴展表示它是不相等的水平和垂直方向，這表示星號將不會保留其原始外觀比例。 以下是相關的程式碼中`PaintSurface`處理常式：

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

`pathBounds`矩形會靠近頂端的 這個程式碼，取得和更新版本搭配使用的畫布的高度與寬度`Scale`呼叫。 藉由呈現時本身的呼叫會調整路徑的座標`DrawPath`呼叫但星號將置於畫布右上角。 它需要向下和左移位。 這是工作的`Translate`呼叫。 這兩個屬性的`pathBounds`是大約 – 100，因此轉譯因素約 100 個。 因為`Translate`呼叫之後`Scale`呼叫時，這些值會有效地調整縮放比例，讓它們將星形的中心移至畫布的正中央：

[![](scale-images/anisotropicscaling-small.png "非等向性調整頁面的三個螢幕擷取畫面")](scale-images/anisotropicscaling-large.png#lightbox "非等向性調整頁面的三個螢幕擷取畫面")

另一種方式可以思考`Scale`並`Translate`呼叫是要判斷在反向序列的效果：`Translate`呼叫移位的路徑，讓它成為完整可見但導向畫布左上角。 `Scale`方法接著會該星號左上角相對較大。

事實上，它會出現星號是稍微大於畫布。 問題在於筆觸粗細。 `Bounds`屬性`SKPath`表示維度的座標編碼在路徑中，而這就是程式會使用調整其規模。 轉譯路徑時使用特定的筆劃的寬度，呈現的路徑。 大於畫布。

若要修正此問題，您需要的補償。 此程式中的其中一個簡單方法是新增下列陳述式權限，再`Scale`呼叫：

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

這會增加`pathBounds`1.5 單位四邊的矩形。 這是合理的解決方案，會捨入筆劃聯結時，才。 斜接角聯結可以是較長，而且很難計算。

您也可以使用類似的技巧，以及文字，作為**非等向性文字**頁面會示範。 以下是相關的部分`PaintSurface`處理常式[ `AnisotropicTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs)類別：

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

它是類似的邏輯，而且文字會展開，以從傳回的文字範圍矩形的頁面大小`MeasureText`（這是稍微大於實際的文字）：

[![](scale-images/anisotropictext-small.png "非等向性測試頁的三個螢幕擷取畫面")](scale-images/anisotropictext-large.png#lightbox "的非等向性測試頁的三個螢幕擷取畫面")

如果您需要保留的圖形物件的外觀比例，您會想要使用 dbi*100 縮放。 **Dbi*100 調整**頁面所示範的是此為 11 星形。 就概念而言，具有 dbi*100 調整頁面的中央顯示圖形物件的步驟如下：

- 轉譯圖形物件左上角的中心。
- 縮放的水平和垂直頁面尺寸除以 圖形物件維度的最小值為基礎的物件。
- 轉譯在物件中央的縮放至頁面的中心。

[ `IsotropicScalingPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs)顯示星號之前以反向順序執行下列步驟：

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

程式碼也會顯示星號十次，每次減少縮放因素 10%，然後逐漸從紅色變成藍色中變更 色彩：

[![](scale-images/isotropicscaling-small.png "Dbi*100 調整頁面的三個螢幕擷取畫面")](scale-images/isotropicscaling-large.png#lightbox "Dbi*100 調整頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
