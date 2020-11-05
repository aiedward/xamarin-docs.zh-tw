---
title: 平移轉換
description: 本文將探討如何使用轉譯轉換來轉換應用程式中的 SkiaSharp 圖形 Xamarin.Forms ，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fdb59cf8b40c62bc4375a12368ed871898497adf
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368587"
---
# <a name="the-translate-transform"></a>平移轉換

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用翻譯轉換來轉換 SkiaSharp 圖形_

最簡單的 SkiaSharp 轉換類型是 *轉譯* 或 *轉譯* 轉換。 這項轉換會以水準和垂直方向來移動繪圖物件。 就概念而言，轉譯是最不必要的轉換，因為您只要變更繪圖函式中所使用的座標，就可以達到相同的效果。 不過，當轉譯路徑時，所有座標都會封裝在路徑中，因此更容易套用轉譯轉換來轉換整個路徑。

轉譯也適用于動畫和簡單的文字效果：

![使用翻譯的文字陰影、engraving 和浮雕](translate-images/translateexample.png)

[`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))中的方法 `SKCanvas` 有兩個參數，這些參數會造成後續繪製的繪圖物件以水準和垂直方向移動：

```csharp
public void Translate (Single dx, Single dy)
```

這些引數可能是負數。 第二個方法會將 [`Translate`](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint)) 兩個轉譯值結合成單一 `SKPoint` 值：

```csharp
public void Translate (SKPoint point)
```

[**SkiaSharpForms**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例程式的 **累積轉譯** 頁面會示範方法的多個呼叫 `Translate` 是累計的。 [`AccumulatedTranslatePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)類別會顯示相同矩形的20個版本，每一個都是從上一個矩形算出的位移，以便沿著對角線伸展。 以下是 `PaintSurface` 事件處理常式：

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

[![累積轉譯頁面的三重螢幕擷取畫面](translate-images/accumulatedtranslate-small.png)](translate-images/accumulatedtranslate-large.png#lightbox "累積轉譯頁面的三重螢幕擷取畫面")

如果累積的轉譯因數為 `dx` 和 `dy` ，而且您在繪圖函式中指定的點是 (`x` ， `y`) ，則繪圖物件會在 (`x'` 、 `y'`) 、where 的點轉譯：

x ' = x + dx

y ' = y + dy

這些稱為轉譯的 *轉換公式* 。 新的和的預設值為 `dx` `dy` `SKCanvas` 0。

使用翻譯轉換來進行陰影效果和類似的技術，如同 **翻譯文字效果** 頁面所示。 以下是 `PaintSurface` 類別中處理常式的相關部分 [`TranslateTextEffectsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs) ：

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

在這三個範例中， `Translate` 會呼叫以顯示文字，以從和變數指定的位置位移 `x` `y` 。 然後以另一種色彩再次顯示文字，而不會產生轉譯效果：

[![[翻譯文字效果] 頁面的三重螢幕擷取畫面](translate-images/translatetexteffects-small.png)](translate-images/translatetexteffects-large.png#lightbox "[翻譯文字效果] 頁面的三重螢幕擷取畫面")

這三個範例中的每一個都顯示否定呼叫的不同方式 `Translate` ：

第一個範例只會 `Translate` 再呼叫一次，但有負數值。 由於 `Translate` 呼叫是累積的，因此此呼叫順序只會將總轉譯還原為預設值零。

第二個範例會呼叫 [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) 。 這會導致所有轉換回到其預設狀態。

第三個範例會 `SKCanvas` 使用的呼叫來儲存物件的狀態 [`Save`](xref:SkiaSharp.SKCanvas.Save) ，然後使用的呼叫來還原狀態 [`Restore`](xref:SkiaSharp.SKCanvas.Restore) 。 這是操作一連串繪圖作業轉換的最多功能方式。 這些 `Save` 和 `Restore` 呼叫函式就像堆疊一樣：您可以呼叫 `Save` 多次，然後 `Restore` 以反向順序呼叫以返回先前的狀態。 `Save`方法會傳回整數，而且您可以將該整數傳遞給， [`RestoreToCount`](xref:SkiaSharp.SKCanvas.RestoreToCount*) 以有效率地呼叫 `Restore` 多次。 屬性會傳回 [`SaveCount`](xref:SkiaSharp.SKCanvas.SaveCount) 目前儲存在堆疊上的狀態數目。

您也可以使用 [`SKAutoCanvasRestore`](xref:SkiaSharp.SKAutoCanvasRestore) 類別來還原畫布狀態。 此類別的函式可在語句中呼叫，而 `using` 畫布狀態會在區塊結尾自動還原 `using` 。

不過，您不需要擔心從 `PaintSurface` 處理常式呼叫到下一個處理常式的轉換。 每個新的呼叫都會 `PaintSurface` 傳遞 `SKCanvas` 具有預設轉換的全新物件。

轉換的另一種常見用法 `Translate` ，是為了轉譯最初使用可方便繪圖的座標建立的視覺物件。 例如，您可能想要指定具有中間點的類比時鐘座標， (0，0) 。 然後，您可以使用轉換來顯示您想要的頻率。 這項技術會在 [ **Hendecagram 陣列** ] 頁面中示範。 [`HendecagramArrayPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramArrayPage.cs)類別一開始會建立 `SKPath` 11 個指向星形的物件。 `HendecagramPath`物件會定義為公用、靜態和唯讀，以便從其他示範程式進行存取。 它是在靜態的函式中建立的：

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

如果星號的中心是 (0，0) 的點，則星號的所有點都位於圍繞該點的圓形上。 每個點都是角度的正弦和余弦值的組合，以360度的 5/11ths 增加。  (也可以藉由將圓形的角度增加為 2/11、3/11ths 或 4/11 來建立11形的星星。 ) 該圓形的半徑設定為100。

如果轉譯此路徑時沒有任何轉換，中心會放置在的左上角 `SKCanvas` ，而且只會顯示其中的四分之一。 的 `PaintSurface` 處理常式 `HendecagramPage` 改為使用， `Translate` 以並排顯示具有多個星星複本的畫布，每一個都有隨機著色：

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

[![[Hendecagram 陣列] 頁面的三重螢幕擷取畫面](translate-images/hendecagramarray-small.png)](translate-images/hendecagramarray-large.png#lightbox "[Hendecagram 陣列] 頁面的三重螢幕擷取畫面")

動畫通常牽涉到轉換。 [ **Hendecagram 動畫** ] 頁面會在圓形中移動11指的星形。 此 [`HendecagramAnimationPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs) 類別一開始會有一些欄位，以及 `OnAppearing` 用 `OnDisappearing` 來啟動和停止計時器的方法覆寫 Xamarin.Forms ：

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

此 `angle` 欄位每5秒從0度到360度的動畫。 `PaintSurface`處理常式會 `angle` 以兩種方式使用屬性：指定方法中色彩的色調 `SKColor.FromHsl` ，以及做為和方法的引數， `Math.Sin` `Math.Cos` 以管理星星的位置：

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

`PaintSurface`處理常式會呼叫 `Translate` 方法兩次，先將其轉譯為畫布的中心，然後轉譯為以 (0，0) 為中心的圓形圓周。 圓形的半徑設定為盡可能大，同時仍將星星保持在頁面的範圍內：

[![Hendecagram 動畫頁面的三重螢幕擷取畫面](translate-images/hendecagramanimation-small.png)](translate-images/hendecagramanimation-large.png#lightbox "Hendecagram 動畫頁面的三重螢幕擷取畫面")

請注意，星形會維持與頁面中心相同的方向。 完全不會輪替。 這是旋轉轉換的作業。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)