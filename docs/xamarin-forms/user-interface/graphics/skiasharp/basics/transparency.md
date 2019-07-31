---
title: SkiaSharp 透明度
description: 您可以使用透明度來結合單一場景中的多個物件。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B62F9487-C30E-4C63-BAB1-4C091FF50378
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4e8c909ce7dfd9c8f6dccbf2685ff2658931b6a5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656349"
---
# <a name="skiasharp-transparency"></a>SkiaSharp 透明度

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所見， [ `SKPaint` ](xref:SkiaSharp.SKPaint)類別包含[ `Color` ](xref:SkiaSharp.SKPaint.Color)屬性的型別[ `SKColor` ](xref:SkiaSharp.SKColor)。 `SKColor` 包含 alpha 色板，因此任何您色彩與`SKColor`值可以是部分透明。 

中所示範的某些透明度[ **SkiaSharp 中的基本動畫**](animation.md)文章。 這篇文章會稍微更深入，進入結合單一場景，有時也稱為技術在多個物件的透明度_透明混色_。 更進階的混用技術的文章中討論[ **SkiaSharp 著色器**](../effects/shaders/index.md)一節。

當您第一次建立色彩，使用四個參數時，您可以設定的透明度[ `SKColor` ](xref:SkiaSharp.SKColor.%23ctor(System.Byte,System.Byte,System.Byte,System.Byte))建構函式：

```csharp
SKColor (byte red, byte green, byte blue, byte alpha);
```

Alpha 值為 0 是完全透明，而完全不透明 alpha 值為 0xFF。 這些兩個極端之間的值建立部分透明的色彩。

颾魤 ㄛ`SKColor`定義方便[ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*)方法，以建立新的色彩，從現有的色彩，但指定的 alpha 層級：

```csharp
SKColor halfTransparentBlue = SKColors.Blue.WithAlpha(0x80);
```

使用半透明的文字所示**程式碼更多程式碼**頁面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例。 此頁面會淡兩個文字字串和縮小透過合併中的 transparency`SKColor`值：

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

`transparency`欄位以動畫顯示到會從 0 到 1，然後再次在幅度節奏。 第一個文字字串會顯示與 alpha 值減去`transparency`1 的值：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * (1 - transparency)));
```

[ `WithAlpha` ](xref:SkiaSharp.SKColor.WithAlpha*)方法上的現有色彩，以下是設定 alpha 元件`SKColors.Blue`。 第二個文字字串會使用從計算的 alpha 值`transparency`值本身：

```csharp
paint.Color = SKColors.Blue.WithAlpha((byte)(0xFF * transparency));
```

動畫交替使用這兩個單字、 urging 「 撰寫程式碼更多 「 使用者 （或可能要求 「 更多的程式碼 」）：

[![更多程式碼的程式碼](transparency-images/CodeMoreCode.png "更多程式碼的程式碼")](transparency-images/CodeMoreCode-Large.png#lightbox)


在上一篇文章中上[ **SkiaSharp 點陣圖基本概念**](bitmaps.md)，您已看到如何顯示點陣圖使用其中一種[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap*)方法`SKCanvas`。 所有`DrawBitmap`方法包括`SKPaint`做為最後一個參數的物件。 根據預設，此參數設為`null`，您可以忽略它。 

或者，您可以設定`Color`屬性的`SKPaint`物件，以顯示某種程度的透明度的點陣圖。 設定層級中的 transparency`Color`屬性`SKPaint`可讓您和縮小，淡入點陣圖，或到另一個溶解一個點陣圖。 

點陣圖透明度所示**點陣圖溶解**頁面。 XAML 檔案會具現化`SKCanvasView`和`Slider`:

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

程式碼後置檔案會載入兩個點陣圖資源。 這些點陣圖不是相同的大小，但它們是相同的外觀比例：

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

`Color`屬性`SKPaint`物件設定為兩個互補 alpha 層級的兩個點陣圖。 使用時`SKPaint`使用點陣圖，並不重要的哪些其他`Color`值。 最重要的是 alpha 色頻。 此處的程式碼只會呼叫`WithAlpha`上的預設值的方法`Color`屬性。

移動`Slider`打倒之間，而另一個點陣圖：

[![點陣圖溶解](transparency-images/BitmapDissolve.png "點陣圖溶解")](transparency-images/BitmapDissolve-Large.png#lightbox)

在過去的幾個文章中，您已看到如何使用 SkiaSharp 繪製文字、 圓形、 橢圓形、 圓角的矩形和點陣圖。 接下來[SkiaSharp 線條和路徑](../paths/index.md)中您將學習如何繪製圖形之路徑中的 連接的直線。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
