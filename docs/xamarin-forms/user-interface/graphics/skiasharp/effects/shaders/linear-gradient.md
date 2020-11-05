---
title: SkiaSharp 線性漸層
description: 探索如何筆劃線條或填滿區域的漸層，其中包含兩種色彩的漸層組合。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9d2ac2eec5422068243ac43e78092e25c10652f5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366772"
---
# <a name="the-skiasharp-linear-gradient"></a>SkiaSharp 線性漸層

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKPaint`](xref:SkiaSharp.SKPaint)類別 [`Color`](xref:SkiaSharp.SKPaint.Color) 會定義用來筆觸線條或以純色填滿區域的屬性。 您也可以使用漸層來筆觸線或填滿 _區域，這些_ 漸層會逐漸混合色彩：

![線性漸層範例](linear-gradient-images/LinearGradientSample.png "線性漸層範例")

最基本的漸層類型是 _線性_ 漸層。 色彩的 blend 會線上條 (稱為漸層 _線條_ ) 從某個點到另一個點。 垂直對齊漸層的線條具有相同的色彩。 您可以使用這兩種靜態方法的其中一個來建立線性漸層 [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) 。 這兩個多載之間的差異在於其中一個多載包含矩陣轉換，另一個則沒有。 

這些方法會傳回 [`SKShader`](xref:SkiaSharp.SKShader) 您設定為之屬性的型別物件 [`Shader`](xref:SkiaSharp.SKPaint.Shader) `SKPaint` 。 如果 `Shader` 屬性為非 null，則會覆寫 `Color` 屬性。 使用這個物件所填滿的任何線條或任何區域都是以漸層 `SKPaint` 而非純色為基礎。

> [!NOTE]
> `Shader`當您 `SKPaint` 在呼叫中包含物件時，會忽略屬性 `DrawBitmap` 。 您可以使用的 `Color` 屬性 `SKPaint` 來設定顯示點陣圖 (的透明度層級，如 [顯示 SkiaSharp 點陣圖](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)) 一文所述，但是您無法使用 `Shader` 屬性來顯示具有漸層透明度的點陣圖。 其他技巧可用於顯示具有漸層透明度的點陣圖：這些是在 [SkiaSharp 圓形](circular-gradients.md#radial-gradients-for-masking) 漸層和 [SkiaSharp 合成和 blend 模式](../blend-modes/porter-duff.md#gradient-transparency-and-transitions)的文章中說明。

## <a name="corner-to-corner-gradients"></a>邊角漸層

線性漸層通常會從矩形的一個角落延伸到另一個角落。 如果起始點是矩形的左上角，則漸層可以擴充：

- 垂直向左下角
- 水準對齊右上角
- 斜向右下角

在 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的 **SkiaSharp 著色器和其他效果** 區段的第一頁中，會示範對角式線性漸層。 **邊角** 漸層頁面會在其函式 `SKCanvasView` 中建立。 此 `PaintSurface` 處理常式會 `SKPaint` 在語句中建立物件 `using` ，然後定義位於畫布中央的 300-圖元方形矩形：

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

的 `Shader` 屬性 `SKPaint` 會被指派 `SKShader` 靜態方法的傳回值 `SKShader.CreateLinearGradient` 。 五個引數如下所示：

- 漸層的起點，設定為矩形的左上角。
- 漸層的結束點，在這裡設定為矩形的右下角
- 構成漸層的兩個或多個色彩的陣列
- 值的陣列 `float` ，表示漸層中色彩的相對位置。
- 列舉的成員，指出漸層的 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 行為如何超出漸層線的結尾

建立漸層物件之後，方法會 `DrawRect` 使用 `SKPaint` 包含著色器的物件來繪製300圖元的正方形。 在此，其會在 iOS、Android 和通用 Windows 平臺 (UWP) 上執行：

[![邊角漸層](linear-gradient-images/CornerToCornerGradient.png "邊角漸層")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

漸層線條是由指定為前兩個引數的兩個點所定義。 請注意，這些點是相對於 _畫布_ ，而 _不_ 是與漸層一起顯示的繪圖物件。 沿著漸層線，色彩會在右下角逐漸從紅色轉換成藍色。 任何與漸層垂直的線條都有常數色彩。

`float`指定為第四個引數的值陣列與色彩陣列有一對一的對應關係。 這些值會指出出現這些色彩之漸層的相對位置。 在這裡，0表示在漸層 `Red` 行的開頭髮生，1表示在 `Blue` 行尾發生。 數位必須是遞增的，且必須在0到1的範圍內。 如果它們不在該範圍內，則會調整為在該範圍內。

陣列中的兩個值可以設定為0和1以外的值。 試試看：

```csharp
new float[] { 0.25f, 0.75f }
```

現在，漸層的第一季是純紅色，而最後一季是純藍色。 紅色和藍色的混合僅限於漸層的中間一半。

一般來說，您會想要將這些位置值的空間平均從0到1。 如果是這種情況，您可以直接提供 `null` 作為的第四個引數 `CreateLinearGradient` 。

雖然此漸層是在 300-圖元正方形矩形的兩個角落之間定義的，但並不限於填滿該矩形。 **邊角** 漸層頁面包含一些額外的程式碼，可回應頁面上的點擊或滑鼠點擊。 此 `drawBackground` 欄位會在每個點間切換 `true` `false` 。 如果值為 `true` ，則 `PaintSurface` 處理常式會使用相同的 `SKPaint` 物件填滿整個畫布，然後繪製表示較小矩形的黑色矩形： 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

以下是您在點擊畫面之後看到的內容：

[![邊角漸層已滿](linear-gradient-images/CornerToCornerGradientFull.png "邊角漸層已滿")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

請注意，漸層會在定義漸層線的點以外的相同模式中重複。 發生此重複的原因是最後一個引數為 `CreateLinearGradient` `SKShaderTileMode.Repeat` 。  (您很快就會看到其他選項。 ) 

另請注意，您用來指定漸層線的點不是唯一的。 垂直至漸層的線條具有相同的色彩，因此您可以為相同效果指定無限數目的漸層。 例如，以水準漸層填滿矩形時，您可以指定左上角和右上角，或在任何兩個點（甚至是和平行至這些線條）中，指定左右左右角。

## <a name="interactively-experiment"></a>以互動方式實驗

您可以使用 **互動式線性** 漸層頁面，以互動方式實驗線性漸層。 此頁面會使用 `InteractivePage` 本文中所介紹的 [**類別來繪製弧線**](../../curves/arcs.md)。 `InteractivePage` [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) 會處理事件，以維護 `TouchPoint` 可隨著手指或滑鼠移動的物件集合。

XAML 檔案 `TouchEffect` 會附加至的父系， `SKCanvasView` 也包含可 `Picker` 讓您選取列舉的三個成員之一 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

程式碼後端檔案中的函式會 `TouchPoint` 為線性漸層的開始和結束點建立兩個物件。 `PaintSurface`處理常式會定義三種色彩的陣列 (從紅色到綠色到藍色的漸層) ，並從取得目前的 `SKShaderTileMode` `Picker` ：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

`PaintSurface`處理常式會 `SKShader` 從所有該資訊建立物件，並使用它來為整個畫布上色。 值的陣列 `float` 設定為 `null` 。 否則，若要使用同樣的空間，您可以將該參數設定為值為0、0.5 和1的陣列。

大部分的 `PaintSurface` 處理常式都是專門用來顯示數個物件：觸控點為空心圓、漸層線條，以及與觸控點的漸層直線垂直的線條：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

連接這兩個接觸點的漸層線條很容易繪製，但垂直線需要更多工作。 漸層線會轉換成向量，並正規化為具有一個單位的長度，然後以90度旋轉。 然後，會將該向量的長度指定為200圖元。 它是用來繪製從觸控點延伸至漸層直線的四行。

垂直線與漸層的開頭和結尾相符。 這幾行以外的情況取決於列舉的設定 `SKShaderTileMode` ：

[![互動式線性漸層](linear-gradient-images/InteractiveLinearGradient.png "互動式線性漸層")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

三個螢幕擷取畫面顯示三個不同值的結果 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 。 IOS 螢幕擷取畫面顯示 `SKShaderTileMode.Clamp` ，只是擴充漸層框線的色彩。 `SKShaderTileMode.Repeat`Android 螢幕擷取畫面中的選項會顯示如何重複漸層模式。 `SKShaderTileMode.Mirror`UWP 螢幕擷取畫面中的選項也會重複此模式，但每次都會反轉模式，而不會產生色彩不連續。

## <a name="gradients-on-gradients"></a>漸層梯度

`SKShader`類別不會定義任何公用屬性或方法，但除外 `Dispose` 。 `SKShader`因此，其靜態方法所建立的物件是不可變的。 即使您對兩個不同的物件使用相同的漸層，但您可能會想要稍微改變漸層。 若要這樣做，您必須建立新的 `SKShader` 物件。

漸層 **文字** 頁面會顯示同時具有類似漸層色彩的文字和背景：

[![漸層文字](linear-gradient-images/GradientText.png "漸層文字")](linear-gradient-images/GradientText-Large.png#lightbox)

漸層的唯一差異在於起點和終點。 用來顯示文字的漸層是以文字周框的邊角的兩個點為基礎。 在背景中，這兩個點是以整個畫布為基礎。 此程式碼如下：

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

`Shader` `SKPaint` 先設定物件的屬性，以顯示漸層來涵蓋背景。 漸層點會設定為畫布的左上角和右下角。

程式碼會設定 `TextSize` 物件的屬性， `SKPaint` 讓文字顯示在畫布寬度的90%。 文字範圍用來計算 `xText` `yText` 傳遞給方法的值，並將 `DrawText` 文字置中。

不過，第二個呼叫的漸 `CreateLinearGradient` 層點必須參考顯示時相對於畫布的文字左上角和右下角。 這是藉由 `textBounds` 將矩形與相同 `xText` 和值移位來完成 `yText` ：

```csharp
textBounds.Offset(xText, yText);
```

現在矩形的左上角和右下角可以用來設定漸層的起點和終點。

## <a name="animating-a-gradient"></a>製作漸層動畫

有幾種方式可以建立漸層的動畫。 其中一個方法是建立起點和終點的動畫。 漸層 **動畫** 頁面會在以畫布為中心的圓形周圍移動兩個點。 此圓形的半徑是畫布寬度或高度的一半，以較小者為准。 起點和終點在此圓形上彼此相反，而漸層會以圖格模式從白色變成黑色 `Mirror` ：

[![漸層動畫](linear-gradient-images/GradientAnimation.png "漸層動畫")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

此函數會建立 `SKCanvasView` 。 `OnAppearing`和 `OnDisappearing` 方法會處理動畫邏輯：

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

`OnTimerTick`方法 `angle` 會計算每3秒從0到2π的動畫值。 

以下是計算兩個漸層點的方法之一。 名為的 `SKPoint` 值 `vector` 會計算為從畫布的中心延伸至圓形半徑的某個點。 這個向量的方向是以角度的正弦和余弦值為基礎。 接著會計算兩個相反的漸層點：一個點是藉由從中心點減去該向量來計算，而其他點則是藉由將向量新增至中心點來計算：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

有些不同的方法需要較少的程式碼。 這種方法會使用多載 [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 方法搭配矩陣轉換做為最後一個引數。 此方法是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 範例中的版本：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

如果畫布的寬度小於高度，則兩個漸層點會設定為 (0、0) 和 (`info.Width` ，0) 。 以最後一個引數形式傳遞的旋轉轉換，可 `CreateLinearGradient` 有效地將這兩個點圍繞在螢幕的中央。

請注意，如果角度是0，就不會有旋轉，而兩個漸層點是畫布的左上角和右上角。 這些點不是計算方式相同的漸層點，如先前的呼叫所示 `CreateLinearGradient` 。 但是，這些點會 _平行_ 至 bisects 畫布中央的水準漸層線條，並且會產生相同的漸層。

**彩虹漸層**

**彩虹** 漸層頁面從畫布左上角到右下角繪製一個彩虹。 但這個彩虹漸層並不像真實的彩虹。 它是直接而非曲線的，但它是以八個 HSL (色相-飽和度-亮度) 色彩，由迴圈從0到360的色調值所決定：

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

該程式碼是處理常式的一部分， `PaintSurface` 如下所示。 處理常式一開始會建立一個路徑，該路徑會定義從畫布左上角延伸至右下角的六側多邊形：

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

方法中的兩個漸層點 `CreateLinearGradient` 是根據定義此路徑的兩個點：兩個點都接近左上角。 第一個是在畫布的上邊緣，而第二個位于畫布的左邊緣。 結果如下︰

[![彩虹漸層錯誤](linear-gradient-images/RainbowGradientFaulty.png "彩虹漸層錯誤")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

這是個有趣的影像，但不是很重要。 問題在於建立線性漸層時，常數色彩的線條會垂直對齊漸層。 漸層線條是以圖的左上邊和左邊的點為基礎，而該行通常不會與延伸至右下角的圖表邊緣保持垂直。 只有當畫布是正方形時，此方法才會運作。

若要建立適當的彩虹漸層，漸層線條必須垂直于彩虹的邊緣。 這是比較相關的計算。 必須定義平行于圖長邊的向量。 向量會旋轉90度，使其與該邊垂直旋轉。 然後藉由乘以來將其重設為圖形的寬度 `rainbowWidth` 。 這兩個漸層點是根據圖側邊的點來計算，而該點會加上向量。 以下是在 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的 **彩虹** 漸層頁面中顯示的程式碼：

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

現在，彩虹色彩會與圖對齊：

[![彩虹漸層](linear-gradient-images/RainbowGradient.png "彩虹漸層")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**無限大色彩**

[ **無限大色彩** ] 頁面也會使用彩虹漸層。 此頁面會使用 [**第三種類型的貝茲曲線**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs)所述的路徑物件來繪製無限大符號。 然後，影像會以動畫的彩虹漸層著色，以在影像上持續地進行掃描。

此函式會建立 `SKPath` 描述無限大符號的物件。 建立路徑之後，此函式也可以取得路徑的矩形界限。 然後，它會計算名為的值 `gradientCycleLength` 。 如果漸層是根據矩形的左上角和右下角 `pathBounds` ，則此 `gradientCycleLength` 值為漸層模式的水準寬度總計：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

此函式也會建立 `colors` 彩虹和物件的陣列 `SKCanvasView` 。

和方法的覆寫會 `OnAppearing` `OnDisappearing` 執行動畫的額外負荷。 `OnTimerTick`方法會 `offset` 從0到 `gradientCycleLength` 每兩秒繪製一次欄位：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

最後， `PaintSurface` 處理常式會轉譯無限大符號。 因為路徑包含圍繞著中心點的負數和正面座標 (0，0) ，所以 `Translate` 畫布上的轉換是用來將它移至中央。 轉譯轉換之後會 `Scale` 套用一個轉換，以套用調整比例，使無限大的正負號盡可能大，同時仍維持在畫布寬度和高度的95% 內。 

請注意， `STROKE_WIDTH` 常數會新增至路徑周框矩形的寬度和高度。 路徑將會以此寬度的線條來繪製，所以轉譯的無限大大小會以四邊的寬度一半增加：

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

查看作為前兩個引數傳遞的點 `SKShader.CreateLinearGradient` 。 這些點是以原始的路徑周框為基礎。 第一個重點是 (&ndash; 250、 &ndash; 100) ，而第二個是 (250，100) 。 在 SkiaSharp 內部，這些點會受限於目前的畫布轉換，因此它們會以顯示的無限大符號正確地對齊。

如果沒有的最後一個引數 `CreateLinearGradient` ，您會看到從無限大符號的左上角延伸至右下方的彩虹漸層。  (實際上，漸層會從左上角延伸至周框的右下角。 轉譯的無限大正負號大於周框的 `STROKE_WIDTH` 所有兩側值的一半。 因為漸層在開頭和結尾是紅色的，而漸層是使用來建立的 `SKShaderTileMode.Repeat` ，所以差異並不明顯。 ) 

使用的最後一個引數，漸層 `CreateLinearGradient` 模式會持續跨影像進行掃描：

[![無限大色彩](linear-gradient-images/InfinityColors.png "無限大色彩")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>透明度和漸層

參與漸層的色彩可以包含透明度。 漸層的漸層可以從色彩淡成透明，而不是從某種色彩淡到另一種色彩的漸層。 

您可以使用這項技術來獲得一些有趣的效果。 其中一個典型範例顯示圖形化物件與其反映：

[![反映漸層](linear-gradient-images/ReflectionGradient.png "反映漸層")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

下拉式清單中的文字會以漸層的漸層呈現，其頂端有50% 的透明效果，底部有完全透明的透明。 這些透明度層級會與0x80 和0的 Alpha 值相關聯。

反映漸層 `PaintSurface` 頁面 **Reflection Gradient** 中的處理常式會將文字大小調整為畫布寬度的90%。 然後，它會計算 `xText` 和 `yText` 值以將文字置於水準置中，但在相對於頁面垂直中央的基準上：

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

這些值 `xText` 和 `yText` 值都是用來在 `DrawText` 處理常式底部的呼叫中顯示反映的文字的相同值 `PaintSurface` 。 不過，在該程式碼之前，您會看到的方法呼叫 `Scale` `SKCanvas` 。 這個 `Scale` 方法會水準縮放 1 (不會執行任何) 動作，而是以垂直方式將 &ndash; 所有專案反向反轉。 旋轉中心會設定為點 (0， `yText`) ，其中 `yText` 是畫布的垂直中心，最初是以 `info.Height` 2 除以2。

請記住，Skia 會在畫布轉換之前使用漸層來將繪圖物件著色。 繪製 unreflected 文字之後， `textBounds` 會將矩形移位，使其對應至顯示的文字：

```csharp
textBounds.Offset(xText, yText);
```

`CreateLinearGradient`呼叫會定義從矩形頂端到底部的漸層。 漸層是從完全透明的藍色 (`paint.Color.WithAlpha(0)`) 為50% 透明藍色 (`paint.Color.WithAlpha(0x80)`) 。 畫布轉換會將文字向下反轉，因此50% 透明藍色會從基準開始，並在文字頂端變成透明。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)