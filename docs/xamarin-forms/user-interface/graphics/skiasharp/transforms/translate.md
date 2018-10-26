---
title: 平移轉換
description: 本文檢驗如何使用平移轉換移位 SkiaSharp 圖形，在 Xamarin.Forms 應用程式，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: BD28ADA1-49F9-44E2-A548-46024A29882F
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 2171c8f0b2926a645fb98df52bae2391449bf89c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120927"
---
# <a name="the-translate-transform"></a>平移轉換

_了解如何使用平移轉換移位 SkiaSharp 圖形_

最簡單的型別中 SkiaSharp 轉換*轉譯*或是*轉譯*轉換。 這項轉換會轉移水平和垂直方向的圖形物件。 就某方面來說，轉譯會是最不必要的轉換，因為您通常可以完成相同的效果，只要變更您在繪圖函式中使用的座標。 當轉譯路徑，不過，所有座標會都封裝在路徑中，因此很容易套用平移轉換来移位的完整路徑。

翻譯是也適用於動畫以及簡單的文字效果：

![](translate-images/translateexample.png "文字陰影 engraving，和浮凸與轉譯")

[ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single))方法中的`SKCanvas`有會導致要水平和垂直位移的後續繪製的圖形物件的兩個參數：

```csharp
public void Translate (Single dx, Single dy)
```

這些引數可以是負數。 第二個[ `Translate` ](xref:SkiaSharp.SKCanvas.Translate(SkiaSharp.SKPoint))方法會結合在單一的兩個轉譯值`SKPoint`值：

```csharp
public void Translate (SKPoint point)
```

**累積轉譯**頁[ **SkiaSharpForms** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例程式會示範多個呼叫的`Translate`方法將會累計。 [ `AccumulatedTranslatePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AccumulatedTranslatePage.cs)類別會顯示相同的矩形的 20 版本，每個位移從先前的矩形剛好夠讓它們沿著對角線 stretch。 以下是`PaintSurface`事件處理常式：

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

如果累積的轉譯因素`dx`和`dy`，而且您在繪圖函式中指定的點是 (`x`， `y`)，則轉譯的圖形化的物件之處 (`x'`， `y'`)，其中：

x' = x + dx

y' = y + dy

這些值稱為*轉換公式*翻譯。 預設值`dx`並`dy`新`SKCanvas`都是 0。

通常會使用平移轉換陰影效果和類似的技巧，作為**翻譯文字效果**頁面會示範。 以下是相關的部分`PaintSurface`中的處理常式[ `TranslateTextEffectsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TranslateTextEffectsPage.cs)類別：

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

在每三個範例中，`Translate`呼叫來顯示的文字位移所指定的位置從`x`和`y`變數。 然後在另一種色彩不會影響轉譯時再次顯示文字：

[![](translate-images/translatetexteffects-small.png "轉譯文字效果頁面的三個螢幕擷取畫面")](translate-images/translatetexteffects-large.png#lightbox "轉譯文字效果頁面的三個螢幕擷取畫面")

每三個範例示範以不同方式的否定`Translate`呼叫：

第一個範例只會呼叫`Translate`一次，但與負數值。 因為`Translate`呼叫是累計、 這一系列的呼叫只是將總轉譯還原為預設值為零。

第二個範例會呼叫[ `ResetMatrix` ](xref:SkiaSharp.SKCanvas.ResetMatrix)。 這會導致所有轉換回到其預設狀態。

第三個範例會將儲存的狀態`SKCanvas`藉由呼叫物件[ `Save` ](xref:SkiaSharp.SKCanvas.Save) ，然後再還原的狀態，藉由呼叫[ `Restore` ](xref:SkiaSharp.SKCanvas.Restore)。 這是最具彈性的方式來操作的繪圖作業的一系列轉換。 這些`Save`並`Restore`呼叫函式，像堆疊一樣： 您可以呼叫`Save`多個時間，然後呼叫`Restore`在反向序列，以返回先前的狀態。 `Save`方法會傳回一個整數，而您可以傳遞至該整數[ `RestoreToCount` ](xref:SkiaSharp.SKCanvas.RestoreToCount*)有效地呼叫`Restore`多次。 [ `SaveCount` ](xref:SkiaSharp.SKCanvas.SaveCount)屬性會傳回目前儲存在堆疊上的狀態數目。

您也可以使用[ `SKAutoCanvasRestore` ](xref:SkiaSharp.SKAutoCanvasRestore)還原畫布狀態的類別。 這個類別的建構函式要呼叫`using`陳述式; 畫布狀態就會自動還原結尾`using`區塊。 

不過，您不需要擔心如何轉換移一個呼叫從`PaintSurface`處理常式，以便下一步。 每個新的呼叫來`PaintSurface`提供的全新`SKCanvas`物件使用預設轉換。

另一個常見用法`Translate`轉換是轉譯視覺物件原本是使用建立很方便進行繪圖的座標。 例如，您可以指定座標的類比時鐘的中心點 （0，0）。 您接著可以使用轉換來顯示時鐘您想要的位置。 這項技術會示範在 [**Hendecagram 陣列**] 頁面。 [ `HendecagramArrayPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramPage.cs)類別一開始先建立`SKPath`11 星形的物件。 `HendecagramPath`物件會定義為公用、 靜態的且為唯讀狀態，使它可以存取來自其他示範程式。 它會建立在靜態建構函式：

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

如果將星形的中心點 （0，0），星號的所有點就會都位於圍繞該點的圓形。 每個點都增加 5/11ths 360 度的角度的正弦和餘弦函數值的組合。 (它，也可以藉由增加 2 的角度建立 11 星形/11 日，3/11ths 或 4 個/第 11 的圓形。)該圓形的半徑會設定為 100。

中央如果此路徑呈現沒有任何轉換，將會位於左上角`SKCanvas`，它的一季都看得到。 `PaintSurface`處理常式`HendecagramPage`改為使用`Translate`並排顯示在畫布的星狀多個複本，每個隨機著色：

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

結果如下：

[![](translate-images/hendecagramarray-small.png "Hendecagram 陣列 頁面的三個螢幕擷取畫面")](translate-images/hendecagramarray-large.png#lightbox "Hendecagram 陣列 頁面的三個螢幕擷取畫面")

動畫通常涉及轉換。 **Hendecagram 動畫**頁面移動 11 角星形的圓形。 [ `HendecagramAnimationPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/HendecagramAnimationPage.cs)類別的某些欄位的開頭，且會覆寫的`OnAppearing`和`OnDisappearing`啟動和停止 Xamarin.Forms 計時器的方法：

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

`angle`欄位從以動畫顯示 0 度到 360 度每隔 5 秒。 `PaintSurface`處理常式會使用`angle`屬性有兩種： 指定的色彩色調`SKColor.FromHsl`方法，並做為引數`Math.Sin`和`Math.Cos`控管的星狀位置的方法：

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

`PaintSurface`處理常式會呼叫`Translate`方法兩次，先要轉譯為中心的畫布，然後再翻譯成環繞於圓形的圓周 （0，0）。 圓形的半徑會設定為盡可能在保持頁面的範圍內的星號：

[![](translate-images/hendecagramanimation-small.png "Hendecagram 動畫頁面的三個螢幕擷取畫面")](translate-images/hendecagramanimation-large.png#lightbox "Hendecagram 動畫頁面的三個螢幕擷取畫面")

請注意它圍繞在頁面中央，星號會維護相同的方向。 它完全不會旋轉。 這是作業的旋轉轉換。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
