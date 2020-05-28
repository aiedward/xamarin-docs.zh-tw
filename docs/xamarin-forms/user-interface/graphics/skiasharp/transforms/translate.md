---
title: ''
description: 本文將探討如何在應用程式中使用「轉譯」轉換來移位 SkiaSharp 圖形 Xamarin.Forms ，並使用範例程式碼示範這項功能。
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0eb3b4a6b37d59363984c9248cc39de91a6819e0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138251"
---
# <a name="the-translate-transform"></a>平移轉換

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用轉譯轉換來移位 SkiaSharp 圖形_

SkiaSharp 中最簡單的轉換類型是「*轉譯*」或「*轉譯*」轉換。 這項轉換會以水準和垂直方向移動繪圖物件。 就意義而言，翻譯是最不必要的轉換，因為您通常只需要變更您在繪圖函式中使用的座標，就可以達到相同的效果。 不過，轉譯路徑時，所有座標都會封裝在路徑中，因此更容易套用翻譯轉換來移位整個路徑。

翻譯也適用于動畫以及簡單的文字效果：

![](translate-images/translateexample.png "Text shadow, engraving, and embossing with translation")

[`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))中的方法 `SKCanvas` 有兩個參數，會導致後續繪製的繪圖物件以水準和垂直方式移動：

```csharp
public void Translate (Single dx, Single dy)
```

這些引數可能是負值。 第二種方法會將 [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) 這兩個轉譯值結合成單一 `SKPoint` 值：

```csharp
public void Translate (SKPoint point)
```

[**SkiaSharpForms**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例程式的 [**累積的翻譯**] 頁面會示範多個 `Translate` 方法呼叫是累計的。 [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)類別會顯示相同矩形的20個版本，每一個矩形的位移就夠大，使其沿著對角線延展。 以下是 `PaintSurface` 事件處理常式：

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

後續的矩形會向下 trickle 頁面：

[![](translate-images/accumulatedtranslate-small.png "Triple screenshot of the Accumulated Translate page")](translate-images/accumulatedtranslate-large.png#lightbox "Triple screenshot of the Accumulated Translate page")

如果累積的轉譯因數是 `dx` 和 `dy` ，而且您在繪圖函式中指定的點是（ `x` ， `y` ），則繪圖物件會在點（ `x'` ，）上轉譯 `y'` ，其中：

x ' = x + dx

y ' = y + dy

這些稱為轉譯的*轉換公式*。 新的和的預設值為 `dx` `dy` `SKCanvas` 0。

通常會使用翻譯轉換來進行陰影效果和類似的技術，如同 [**翻譯文字效果**] 頁面所示。 以下是 `PaintSurface` 類別中處理常式的相關部分 [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) ：

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

在這三個範例中， `Translate` 會呼叫來顯示文字，以從和變數所指定的位置位移 `x` `y` 。 然後再以另一種色彩顯示文字，而不會有任何轉譯效果：

[![](translate-images/translatetexteffects-small.png "Triple screenshot of the Translate Text Effects page")](translate-images/translatetexteffects-large.png#lightbox "Triple screenshot of the Translate Text Effects page")

這三個範例中的每一個都會顯示否定呼叫的不同方式 `Translate` ：

第一個範例是 `Translate` 再次呼叫，但使用負值。 由於 `Translate` 呼叫是累計的，因此這個呼叫順序只會將轉譯總計還原為預設值零。

第二個範例會呼叫 [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) 。 這會使所有轉換回到其預設狀態。

第三個範例會 `SKCanvas` 使用的呼叫來儲存物件的狀態 [`Save`](xref:SkiaSharp.SKCanvas.Save) ，然後使用的呼叫來還原狀態 [`Restore`](xref:SkiaSharp.SKCanvas.Restore) 。 這是操作一系列繪製作業轉換的最具彈性方式。 這些 `Save` 和會呼叫函式，就 `Restore` 像堆疊一樣：您可以呼叫 `Save` 多次，然後以 `Restore` 反向順序呼叫來回到先前的狀態。 `Save`方法會傳回整數，您可以將該整數傳遞給， [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) 以有效地呼叫 `Restore` 多次。 屬性會傳回 [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) 目前已儲存在堆疊上的狀態數目。

您也可以使用 [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) 類別來還原畫布狀態。 這個類別的函式是要在語句中呼叫 `using` ; 畫布狀態會在區塊結尾自動還原 `using` 。

不過，您不需要擔心從一個處理常式呼叫到下一個的轉換 `PaintSurface` 。 每個新的呼叫都會以 `PaintSurface` 預設轉換傳遞全新的 `SKCanvas` 物件。

轉換的另一個常見用法， `Translate` 是用來呈現原本使用可方便繪製的座標來建立的視覺物件。 例如，您可能會想要指定類比時鐘的座標，並在點（0，0）上置中。 然後，您可以使用 [轉換] 來顯示您想要的時鐘。 這項技術會在 [**Hendecagram Array**] 頁面中示範。 [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs)類別一開始會建立 `SKPath` 11 個指向星形的物件。 `HendecagramPath`物件會定義為公用、靜態和唯讀，讓它可以從其他示範程式存取。 它是在靜態的函式中建立的：

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

如果星形的中心是點（0，0），則星形的所有點都在該點周圍的圓圈上。 每個點都是一個角度的正弦和余弦值的組合，會增加 5/11ths 的360度。 （您也可以將角度增加 2/11、3/11ths，或圓形的 4/11），以建立11個指向的星形。該圓形的半徑設定為100。

如果轉譯此路徑時沒有任何轉換，則中心將會定位於的左上角 `SKCanvas` ，而且只會顯示其中的季。 的 `PaintSurface` 處理常式 `HendecagramPage` 改 `Translate` 為使用來並排顯示具有多個星星複本的畫布，每一個都是隨機著色的：

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

結果如下︰

[![](translate-images/hendecagramarray-small.png "Triple screenshot of the Hendecagram Array page")](translate-images/hendecagramarray-large.png#lightbox "Triple screenshot of the Hendecagram Array page")

動畫通常包含轉換。 [ **Hendecagram 動畫**] 頁面會將圓形中的11個星形向周圍移動。 類別會以 [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) 一些欄位開始，並覆寫 `OnAppearing` 和 `OnDisappearing` 方法來啟動和停止 Xamarin.Forms 計時器：

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

`angle`欄位會每5秒從0度到360度的動畫。 `PaintSurface`處理常式會 `angle` 以兩種方式使用屬性：指定方法中色彩的色調 `SKColor.FromHsl` ，以及做為和方法的引數， `Math.Sin` `Math.Cos` 以管理星星的位置：

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

`PaintSurface`處理常式會呼叫 `Translate` 方法兩次，先轉譯成畫布的中央，然後轉譯成以（0，0）為中心的圓形圓周。 圓形的半徑設定為盡可能大，同時仍然保留頁面範圍內的星號：

[![](translate-images/hendecagramanimation-small.png "Triple screenshot of the Hendecagram Animation page")](translate-images/hendecagramanimation-large.png#lightbox "Triple screenshot of the Hendecagram Animation page")

請注意，星形會維持與頁面中央相同的方向。 完全不會旋轉。 這是旋轉轉換的作業。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
