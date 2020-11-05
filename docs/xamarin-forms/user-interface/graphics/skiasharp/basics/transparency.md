---
title: SkiaSharp 透明度
description: 使用透明度來合併單一場景中的多個物件。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 837d58508c3f5b14c4c36a867a2aa974a5bf397c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374312"
---
# <a name="skiasharp-transparency"></a>SkiaSharp 透明度

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所見， [`SKPaint`](xref:SkiaSharp.SKPaint) 類別包含 [`Color`](xref:SkiaSharp.SKPaint.Color) 型別的屬性 [`SKColor`](xref:SkiaSharp.SKColor) 。 `SKColor` 包含 Alpha 色板，因此您以值色彩的任何動作都 `SKColor` 可以部分透明。 

SkiaSharp 文章的 [**基本動畫**](animation.md) 中示範了一些透明度。 本文會稍微深入探討如何在單一場景中結合多個物件，這種技術有時也稱為 _混色_ 。 [**SkiaSharp 著色**](../effects/shaders/index.md)器一節中的文章會討論更先進的混合技巧。

當您第一次使用四個參數的函式建立色彩時，可以設定透明度層級 [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) ：

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Alpha 值為0是完全透明的，而 Alpha 值則是完全不透明。 這兩個極端值之間的值會建立部分透明的色彩。

此外， `SKColor` 也會定義一個方便 [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) 的方法，從現有的色彩建立新的色彩，但使用指定的 Alpha 層級：

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的程式 **代碼更多程式碼** 頁面會示範如何使用部分透明文字。 此頁面會在值中併入透明度，以將兩個文字字串淡化 `SKColor` ：

```csharp
public class CodeMoreCodePage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
    double transparency;

    public CodeMoreCodePage ()
    {
        Title = "Code More Code";

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
        const int duration = 5;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        transparency = 0.5 * (1 + Math.Sin(progress * 2 * Math.PI));
        canvasView.InvalidateSurface();

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        const string TEXT1 = "CODE";
        const string TEXT2 = "MORE";

        using (SKPaint paint = new SKPaint())
        {
            // Set text width to fit in width of canvas
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT1);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Center first text string
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT1, ref textBounds);

            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
            canvas.DrawText(TEXT1, xText, yText, paint);

            // Center second text string
            textBounds = new SKRect();
            paint.MeasureText(TEXT2, ref textBounds);

            xText = info.Width / 2 - textBounds.MidX;
            yText = info.Height / 2 - textBounds.MidY;

            paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
            canvas.DrawText(TEXT2, xText, yText, paint);
        }
    }
}
```

在 `transparency` 正弦曲線節奏中，此欄位的動畫會在0和1之間重設。 第一個文字字串顯示時，會以1減去值的計算出 Alpha 值 `transparency` ：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

[`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*)方法會以現有的色彩設定 Alpha 元件，如下所示 `SKColors.Blue` 。 第二個文字字串會使用計算自值本身的 Alpha 值 `transparency` ：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

動畫會在兩個單字之間替換，urging 使用者以「撰寫更多程式碼」 (或可能要求「更多程式碼」 ) ：

[![程式碼更多程式碼](transparency-images/CodeMoreCode.png "程式碼更多程式碼")](transparency-images/CodeMoreCode-Large.png#lightbox)

在前一篇有關 [**SkiaSharp 中點陣圖基本概念**](bitmaps.md)的文章中，您已瞭解如何使用的其中一個方法來顯示點陣圖 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) `SKCanvas` 。 所有 `DrawBitmap` 方法都包含 `SKPaint` 做為最後一個參數的物件。 根據預設，這個參數設定為， `null` 您可以忽略它。 

或者，您也可以設定 `Color` 此物件的屬性， `SKPaint` 以顯示具有某種透明度層級的點陣圖。 在的屬性中設定透明度層級，可 `Color` `SKPaint` 讓您將點陣圖淡入或放大，或將點陣圖溶解至另一個點陣圖。 

點陣圖 **溶解** 頁面中會示範點陣圖透明度。 XAML 檔案具現化 `SKCanvasView` 和 `Slider` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapDissolvePage"
             Title="Bitmap Dissolve">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

程式碼後端檔案會載入兩個位圖資源。 這些點陣圖的大小不同，但外觀比例相同：

```csharp
public partial class BitmapDissolvePage : ContentPage
{
    SKBitmap bitmap1;
    SKBitmap bitmap2;

    public BitmapDissolvePage()
    {
        InitializeComponent();

        // Load two bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg"))
        {
            bitmap1 = SKBitmap.Decode(stream);
        }
        using (Stream stream = assembly.GetManifestResourceStream(
                                "SkiaSharpFormsDemos.Media.FacePalm.jpg"))
        {
            bitmap2 = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find rectangle to fit bitmap
        float scale = Math.Min((float)info.Width / bitmap1.Width,
                                (float)info.Height / bitmap1.Height);
        SKRect rect = SKRect.Create(scale * bitmap1.Width,
                                    scale * bitmap1.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Get progress value from Slider
        float progress = (float)progressSlider.Value;

        // Display two bitmaps with transparency
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = paint.Color.WithAlpha((byte)(0xFF * (1 - progress)));
            canvas.DrawBitmap(bitmap1, rect, paint);

            paint.Color = paint.Color.WithAlpha((byte)(0xFF * progress));
            canvas.DrawBitmap(bitmap2, rect, paint);
        }
    }
}
```

`Color`物件的屬性 `SKPaint` 會設定為兩個位圖的兩個互補 Alpha 層級。 使用 `SKPaint` with 點陣圖時，值的其餘部分並不重要 `Color` 。 重要的是，Alpha 通道。 此處的程式碼只會 `WithAlpha` 在屬性的預設值上呼叫方法 `Color` 。

在 `Slider` 一個點陣圖和另一個點陣圖之間移動溶解：

[![點陣圖溶解](transparency-images/BitmapDissolve.png "點陣圖溶解")](transparency-images/BitmapDissolve-Large.png#lightbox)

在過去幾篇文章中，您已瞭解如何使用 SkiaSharp 繪製文字、圓形、橢圓形、圓角矩形和點陣圖。 下一步是 [SkiaSharp 線和路徑](../paths/index.md) ，您將在其中學習如何在圖形路徑中繪製連接線。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)