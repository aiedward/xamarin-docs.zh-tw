---
title: SkiaSharp 遮罩篩選器
description: 了解如何使用遮罩篩選器建立模糊和其他 alpha 的效果。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 940422A1-8BC0-4039-8AD7-26C61320F858
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: d68153cdaad67b407def3ed5bfaddf928ef98bb4
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289087"
---
# <a name="skiasharp-mask-filters"></a>SkiaSharp 遮罩篩選器

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

遮罩篩選條件是管理的幾何和 alpha 色頻的圖形物件的效果。 若要使用的遮罩篩選，將[ `MaskFilter` ](xref:SkiaSharp.SKPaint.MaskFilter)屬性`SKPaint`物件的型別[ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter)您已透過呼叫其中一個`SKMaskFilter`靜態方法。

熟悉遮罩篩選的最佳方式是藉由試驗這些靜態方法。 最有用的遮罩篩選器會建立模糊：

![模糊範例](mask-filters-images/MaskFilterExample.png "模糊範例")

這是唯一的遮罩篩選功能這篇文章中所述。 在下一篇文章[ **SkiaSharp 映像篩選條件**](image-filters.md)也會說明您可能會想要此模糊效果。 

靜態[ `SKMaskFilter.CreateBlur` ](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single))方法具有下列語法：

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

多載可讓您指定用來建立柔邊和避免模糊會與其他圖形物件涵蓋的區域中的矩形之演算法的旗標。

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle) 是列舉型別具有下列成員：

- `Normal`
- `Solid`
- `Outer`
- `Inner`

下列範例會顯示這些樣式的影響。 `sigma`參數指定的模糊程度。 在舊版的 Skia，柔邊的範圍表示半徑值。 半徑值並且適用於您的應用程式，是否有靜態[ `SKMaskFilter.ConvertRadiusToSigma` ](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*)可以從兩個不同轉換的方法。 方法會乘以 0.57735 半徑，並將加入 0.5。

**遮罩模糊實驗**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例可讓您試驗的模糊樣式和標準差的值。 XAML 檔案會具現化`Picker`具有四個`SKBlurStyle`列舉型別成員和`Slider`來指定標準差的值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.MaskBlurExperimentPage"
             Title="Mask Blur Experiment">
    
    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blurStylePicker" 
                Title="Filter Blur Style" 
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlurStyle}">
                    <x:Static Member="skia:SKBlurStyle.Normal" />
                    <x:Static Member="skia:SKBlurStyle.Solid" />
                    <x:Static Member="skia:SKBlurStyle.Outer" />
                    <x:Static Member="skia:SKBlurStyle.Inner" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="sigmaSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaSlider},
                              Path=Value,
                              StringFormat='Sigma = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會使用這些值來建立`SKMaskFilter`物件，並將它設定為`MaskFilter`屬性`SKPaint`物件。 這`SKPaint`物件用來繪製的文字字串和點陣圖：

```csharp
public partial class MaskBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage), 
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public MaskBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
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

        canvas.Clear(SKColors.Pink);

        // Get values from XAML controls
        SKBlurStyle blurStyle =
            (SKBlurStyle)(blurStylePicker.SelectedIndex == -1 ?
                                        0 : blurStylePicker.SelectedItem);

        float sigma = (float)sigmaSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

以下是在 iOS、 Android 和通用 Windows 平台 (UWP)，以執行程式`Normal`模糊樣式和增加`sigma`層級：

[![遮罩模糊實驗-常態](mask-filters-images/MaskBlurExperiment-Normal.png "遮罩模糊實驗-一般")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

您會注意到點陣圖的邊緣會受到柔邊。 `SKMaskFilter`類別不是正確的效果，如果您想要模糊處理整個點陣圖影像。 為此您會想要[ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter)類別中所述的下一篇文章上[ **SkiaSharp 映像篩選**](image-filters.md)。

文字隨著增加的值更模糊`sigma`引數。 在試驗這個程式，您會發現特定`sigma`值，在 Windows 10 desktop 上較極端是柔邊。 因為像素密度較低的行動裝置上比桌上型監視器上，因此文字高度，單位為像素較低，就會發生這項差異。 `sigma`值成正比的模糊程度像素為單位，因此，針對給定`sigma`值的效果是較極端上較低的解析度顯示畫面。 在生產環境應用程式中，您可能需要計算`sigma`圖形的大小成正比的值。 

尋找您的應用程式的最佳模糊層級上決定之前，請嘗試幾個值。 例如，在**遮罩模糊實驗**頁面上，請嘗試設定`sigma`如下所示：

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

現在`Slider`沒有任何作用，但的模糊程度是一致的平台之間：

[![遮罩模糊實驗-一致](mask-filters-images/MaskBlurExperiment-Consistent.png "遮罩模糊實驗-一致")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

所有的螢幕擷取畫面到目前為止已顯示以建立模糊`SKBlurStyle.Normal`列舉成員。 下列螢幕擷取畫面顯示的效果`Solid`， `Outer`，和`Inner`模糊樣式：

[![遮罩模糊實驗](mask-filters-images/MaskBlurExperiment.png "遮罩模糊實驗")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

IOS 螢幕擷取畫面顯示`Solid`樣式：文字字元為實心黑色筆劃，仍存在，這些文字字元外加入柔邊。 

在中間顯示 Android 螢幕擷取畫面`Outer`樣式：（如點陣圖），會刪除本身的字元筆劃及柔邊括住一次出現的文字字元的空白空間。 

UWP 螢幕擷取畫面中的正確顯示`Inner`樣式。 只有文字字元通常佔用的區域柔邊。

[ **SkiaSharp 線性漸層**](shaders/linear-gradient.md#transparency-and-gradients)一文所述**反映漸層停駐**來模仿文字字串的反映使用線形漸層和轉換程式：

[![反映漸層](shaders/linear-gradient-images/ReflectionGradient.png "反映漸層")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

**模糊的反射**頁面會將該程式碼中的單一陳述式：

```csharp
public class BlurryReflectionPage : ContentPage
{
    const string TEXT = "Reflection";

    public BlurryReflectionPage()
    {
        Title = "Blurry Reflection";

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

            // Create a blur mask filter
            paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

新的陳述式會將模糊濾鏡反映文字為基礎的文字大小：

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

此模糊濾鏡原因變得更實際反映：

[![模糊的反射](mask-filters-images/BlurryReflection.png "模糊的反射")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
