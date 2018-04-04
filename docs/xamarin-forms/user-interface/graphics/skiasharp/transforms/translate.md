---
title: 轉換轉換
description: 了解如何使用轉換轉換移位 SkiaSharp 圖形
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 573848186a8f389ac18e22ea4c3b7d4fe1503449
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="the-translate-transform"></a>轉換轉換

_了解如何使用轉換轉換移位 SkiaSharp 圖形_

最簡單的 SkiaSharp 中的轉換類型為*轉譯*或*轉譯*轉換。 這項轉換會轉移之水平和垂直方向的圖形物件。 某方面來說，轉譯會是最不必要的轉換，因為您通常可以完成相同的效果，只要變更您在 繪圖函式中使用的座標。 呈現時的路徑，不過，所有座標，都會都封裝在路徑中，因此就能輕鬆套用轉換轉換来移位的完整路徑。

翻譯是也適用於動畫以及簡單的文字效果：

![](translate-images/translateexample.png "文字陰影 engraving，和浮凸與轉譯")

[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/)方法中的`SKCanvas`有會導致後續繪製的圖形物件要水平及垂直位移的兩個參數：

```csharp
public void Translate (Single dx, Single dy)
```

這些引數可以是負數。 第二個[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/SkiaSharp.SKPoint/)方法結合在單一的兩個轉譯值`SKPoint`值：

```csharp
public void Translate (SKPoint point)
```

**累積轉譯**頁面[ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式範例示範多個呼叫的`Translate`方法將會累計。 [ `AccumulatedTranslate` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)類別會顯示相同的矩形的 20 版本，每個位移從先前的矩形只足以讓它們沿著對角線 stretch。 以下是`PaintSurface`事件處理常式：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Color = SKColors.Black;
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = 3;

        int rectangleCount = 20;
        SKRect rect = new SKRect(0, 0, 250, 250);
        float xTranslate = (info.Width - rect.Width) / (rectangleCount - 1);
        float yTranslate = (info.Height - rect.Height) / (rectangleCount - 1);

        for (int i = 0; i < rectangleCount; i++)
        {
            canvas.DrawRect(rect, strokePaint);
            canvas.Translate(xTranslate, yTranslate);
        }
    }
}
```

後續的矩形緩慢移動到頁面的下方：

[![](translate-images/accumulatedtranslate-small.png "累積轉譯頁面的三個螢幕擷取畫面")](translate-images/accumulatedtranslate-large.png#lightbox "累積轉譯頁面的三個螢幕擷取畫面")

如果累積的轉譯因素`dx`和`dy`，而且您在 繪圖函式中指定的點是 (`x`， `y`)，然後處呈現圖形物件 (`x'`， `y'`)，其中：

x' = x + dx

y' = y + dy

這些值稱為*轉換公式*翻譯。 預設值`dx`和`dy`對新`SKCanvas`為 0。

通常會使用轉換轉換陰影效果和類似的技術，做為**翻譯文字效果**頁面示範。 以下是相關的部分`PaintSurface`中的處理常式[ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs)類別：

```csharp
float textSize = 150;

using (SKPaint textPaint = new SKPaint())
{
    textPaint.Style = SKPaintStyle.Fill;
    textPaint.TextSize = textSize;
    textPaint.FakeBoldText = true;

    float x = 10;
    float y = textSize;

    // Shadow
    canvas.Translate(10, 10);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("SHADOW", x, y, textPaint);
    canvas.Translate(-10, -10);
    textPaint.Color = SKColors.Pink;
    canvas.DrawText("SHADOW", x, y, textPaint);

    y += 2 * textSize;

    // Engrave
    canvas.Translate(-5, -5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("ENGRAVE", x, y, textPaint);
    canvas.ResetMatrix();
    textPaint.Color = SKColors.White;
    canvas.DrawText("ENGRAVE", x, y, textPaint);

    y += 2 * textSize;

    // Emboss
    canvas.Save();
    canvas.Translate(5, 5);
    textPaint.Color = SKColors.Black;
    canvas.DrawText("EMBOSS", x, y, textPaint);
    canvas.Restore();
    textPaint.Color = SKColors.White;
    canvas.DrawText("EMBOSS", x, y, textPaint);
}
```

中的三個範例中，每個`Translate`針對顯示的文字位移所指定的位置從呼叫`x`和`y`變數。 然後在另一種色彩不會影響轉譯時再次顯示文字：

[![](translate-images/translatetexteffects-small.png "翻譯文字效果頁面的三個螢幕擷取畫面")](translate-images/translatetexteffects-large.png#lightbox "翻譯文字效果頁面的三個螢幕擷取畫面")

每三個範例示範以不同方式的補數`Translate`呼叫：

第一個範例只會呼叫`Translate`一次，但與負數值。 因為`Translate`呼叫是累計的這一系列的呼叫只是將總轉譯還原為預設值為零。

第二個範例會呼叫[ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix()/)。 這會導致傳回設為預設狀態的所有轉換。

第三個範例儲存之狀態的`SKCanvas`物件呼叫[ `Save` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Save()/) ，然後再還原呼叫狀態[ `Restore` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Restore/)。 這是最具彈性的方式來操作繪圖作業的一系列的轉換。 這些`Save`和`Restore`呼叫函式，像堆疊一樣： 您可以呼叫`Save`多個階段，然後再呼叫`Restore`相反順序，以回到先前的狀態。 `Save`方法會傳回一個整數，而您可以傳遞至該整數[ `RestoreToCount` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RestoreToCount/)有效地呼叫`Restore`多次。 [ `SaveCount` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.SaveCount/)屬性會傳回目前儲存在堆疊上的狀態數目。

不過，您不需要擔心轉換執行從一個呼叫的`PaintSurface`至下一個處理常式。 每個新呼叫`PaintSurface`傳遞從頭`SKCanvas`預設轉換的物件。

另一個常見用途`Translate`轉換是呈現視覺物件已原先建立使用座標，這會很方便的繪圖。 例如，您可以指定中心點 （0，0） 與類比時鐘的座標。 您接著可以使用轉換來顯示您想要的位置。 這示範於 [**Hendecagram 陣列**] 頁面。 [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs)類別一開始會建立`SKPath`11 星形的物件。 `HendecagramPath`物件定義為公用、 靜態以及唯讀，因此它可以從其他示範程式存取。 它會在靜態建構函式所建立的：

```csharp
public class HendecagramArrayPage : ContentPage
{
    ...
    public static readonly SKPath HendecagramPath;

    static HendecagramArrayPage()
    {
        // Create 11-pointed star
        HendecagramPath = new SKPath();
        for (int i = 0; i < 11; i++)
        {
            double angle = 5 * i * 2 * Math.PI / 11;
            SKPoint pt = new SKPoint(100 * (float)Math.Sin(angle),
                                    -100 * (float)Math.Cos(angle));
            if (i == 0)
            {
                HendecagramPath.MoveTo(pt);
            }
            else
            {
                HendecagramPath.LineTo(pt);
            }
        }
        HendecagramPath.Close();
    }
}
```

如果將星形的中心點 （0，0），所有星形點都會圍繞該點的圓形。 每個點是增加 5/11ths 為 360 度的角度的正弦函數和餘弦值的組合。 (它，也可以藉由增加 2 的角度建立 11 星形/11，3/11ths 或 4/11 的圓形。)該圓形的半徑會設為 100。

如果此路徑呈現沒有任何轉換，就會在中心放置左上角的`SKCanvas`，而且它的季會是可見的。 `PaintSurface`處理常式的`HendecagramPage`改為使用`Translate`來並排顯示畫布與星形的多個複本，每個隨機著色：

```csharp
public class HendecagramArrayPage : ContentPage
{
    Random random = new Random();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            for (int x = 100; x < info.Width + 100; x += 200)
                for (int y = 100; y < info.Height + 100; y += 200)
                {
                    // Set random color
                    byte[] bytes = new byte[3];
                    random.NextBytes(bytes);
                    paint.Color = new SKColor(bytes[0], bytes[1], bytes[2]);

                    // Display the hendecagram
                    canvas.Save();
                    canvas.Translate(x, y);
                    canvas.DrawPath(HendecagramPath, paint);
                    canvas.Restore();
                }
        }
    }
}

```

以下是結果：

[![](translate-images/hendecagramarray-small.png "三個螢幕擷取畫面的 Hendecagram 陣列頁面")](translate-images/hendecagramarray-large.png#lightbox "Hendecagram 陣列頁面的三個螢幕擷取畫面")

動畫通常需要轉換。 **Hendecagram 動畫**頁面移動 11 星形的圓形。 [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs)類別開頭的某些欄位，並覆寫的`OnAppearing`和`OnDisappearing`啟動和停止 Xamarin.Forms 計時器的方法：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    const double cycleTime = 5000;      // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float angle;

    public HendecagramAnimationPage()
    {
        Title = "Hedecagram Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            double t = stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime;
            angle = (float)(360 * t);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }

            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

`angle`欄位以動畫方式從 0 到 360 度每隔 5 秒。 `PaintSurface`處理常式會使用`angle`屬性有兩種： 指定的色彩色調`SKColor.FromHsl`方法，並做為引數`Math.Sin`和`Math.Cos`控管的星狀位置的方法：

```csharp
public class HendecagramAnimationPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.Translate(info.Width / 2, info.Height / 2);
        float radius = (float)Math.Min(info.Width, info.Height) / 2 - 100;

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Fill;
            paint.Color = SKColor.FromHsl(angle, 100, 50);

            float x = radius * (float)Math.Sin(Math.PI * angle / 180);
            float y = -radius * (float)Math.Cos(Math.PI * angle / 180);
            canvas.Translate(x, y);
            canvas.DrawPath(HendecagramPage.HendecagramPath, paint);
        }
    }
}
```

`PaintSurface`處理常式呼叫`Translate`方法兩次，首先要轉譯為中心的畫布，然後再轉譯成圍繞圓形的圓周 （0，0）。 圓形的半徑會設定為盡量大同時保留頁面的範圍內的星號：

[![](translate-images/hendecagramanimation-small.png "三個螢幕擷取畫面的 Hendecagram 動畫頁面")](translate-images/hendecagramanimation-large.png#lightbox "Hendecagram 動畫頁面的三個螢幕擷取畫面")

請注意當星狀時它將著重於頁面的中心會維護相同的方向。 它不會完全旋轉。 這是旋轉轉換的工作。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
