---
title: SkiaSharp 透明度
description: 使用 [透明度] 在單一場景中結合多個物件。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 735aae1b9d94865bd34450861bd6c57b08c420c2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84134715"
---
# <a name="skiasharp-transparency"></a>SkiaSharp 透明度

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所見， [`SKPaint`](xref:SkiaSharp.SKPaint) 類別包含型別為 [`Color`](xref:SkiaSharp.SKPaint.Color) 的屬性 [`SKColor`](xref:SkiaSharp.SKColor) 。 `SKColor`包含 Alpha 色板，因此任何您使用某個值的色彩都 `SKColor` 可以部分透明。 

部分透明度已在 SkiaSharp 文章的[**基本動畫**](animation.md)中示範。 本文更深入探討在單一場景中結合多個物件的透明度，這項技術有時稱為「_混合_」。 [**SkiaSharp 著色**](../effects/shaders/index.md)器一節中的文章會討論更多先進的混合技術。

當您第一次使用四個參數的函式建立色彩時，可以設定透明度層級 [`SKColor`](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte)) ：

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Alpha 值為0會完全透明，而 Alpha 值為完全不透明。 這兩個極端之間的值會建立部分透明的色彩。

此外， `SKColor` 會定義一個方便的 [`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*) 方法，從現有的色彩建立新的色彩，但使用指定的 Alpha 層級：

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的程式**代碼更多程式碼**頁面中，會示範如何使用部分透明的文字。 此頁面會在值中併入透明度，以將兩個文字字串淡入和放大 `SKColor` ：

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

在 `transparency` sinusoidal 節奏中，此欄位會以動畫方式從0變更為1，然後再次返回。 第一個文字字串顯示時，會以數值減去1的值來計算 Alpha 值 `transparency` ：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

[`WithAlpha`](xref:SkiaSharp.SKColor.WithAlpha*)方法會設定現有色彩的 Alpha 元件，其為 `SKColors.Blue` 。 第二個文字字串會使用從值本身計算出來的 Alpha 值 `transparency` ：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

動畫會在兩個字之間交替，urging 使用者以「撰寫更多的程式碼」（或可能會要求「更多程式碼」）：

[![撰寫更多程式碼程式碼](transparency-images/CodeMoreCode.png "撰寫更多程式碼程式碼")](transparency-images/CodeMoreCode-Large.png#lightbox)

在上一篇有關[**SkiaSharp 點陣圖基本概念**](bitmaps.md)的文章中，您已瞭解如何使用的其中一種方法來顯示點陣圖 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap*) `SKCanvas` 。 所有 `DrawBitmap` 方法都包含 `SKPaint` 物件做為最後一個參數。 根據預設，此參數設定為， `null` 而您可以忽略它。 

或者，您可以設定 `Color` 此物件的屬性， `SKPaint` 以顯示具有某種程度透明度的點陣圖。 在的屬性中設定透明度層級可 `Color` `SKPaint` 讓您淡入和縮小點陣圖，或將一個點陣圖分解成另一個。 

點陣圖的透明度會在**點陣圖溶解**頁面中示範。 XAML 檔案會具現化 `SKCanvasView` 和 `Slider` ：

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

程式碼後置檔案會載入兩個位圖資源。 這些點陣圖的大小不相同，但兩者的外觀比例相同：

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

`Color` `SKPaint` 針對兩個位圖，物件的屬性會設定為兩個互補的 Alpha 層級。 使用 `SKPaint` with 點陣圖時，其值的其餘部分並不重要 `Color` 。 所有重要的都是 Alpha 色板。 此處的程式碼只會 `WithAlpha` 在屬性的預設值上呼叫方法 `Color` 。

在 `Slider` 一個點陣圖之間移動溶解：

[![點陣圖溶解](transparency-images/BitmapDissolve.png "點陣圖溶解")](transparency-images/BitmapDissolve-Large.png#lightbox)

在過去幾篇文章中，您已經瞭解如何使用 SkiaSharp 來繪製文字、圓形、橢圓形、圓角矩形和點陣圖。 下一步是[SkiaSharp 線條和路徑](../paths/index.md)，您將在其中學習如何在圖形路徑中繪製連接的線條。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
