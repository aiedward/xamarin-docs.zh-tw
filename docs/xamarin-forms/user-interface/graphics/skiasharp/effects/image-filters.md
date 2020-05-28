---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eedbca080fce9f3001a7b1e2358845fd63c6121b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136340"
---
# <a name="skiasharp-image-filters"></a>SkiaSharp 影像篩選

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

影像篩選準則會影響組成影像的所有圖元色彩位。 它們比 mask 篩選更具彈性，只會在 Alpha 通道上運作，如[**SkiaSharp mask 篩選**](mask-filters.md)一文中所述。 若要使用影像篩選，請將的 [`ImageFilter`](xref:SkiaSharp.SKPaint.ImageFilter) 屬性設定 `SKPaint` 為您透過 [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) 呼叫其中一個類別的靜態方法所建立的類型物件。

熟悉遮罩篩選器的最佳方式，就是使用這些靜態方法來進行實驗。 您可以使用遮罩篩選來模糊整個點陣圖：

![模糊範例](image-filters-images/ImageFilterExample.png "模糊範例")

本文也會示範如何使用影像篩選來建立陰影，以及用於浮雕和 engraving 效果。

## <a name="blurring-vector-graphics-and-bitmaps"></a>模糊向量圖形和點陣圖

靜態方法所建立的模糊效果，在 [`SKImageFilter.CreateBlur`](xref:SkiaSharp.SKImageFilter.CreateBlur*) 類別中的模糊方法上具有顯著的優勢 [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) ：影像篩選可以模糊整個點陣圖。 此方法具有下列語法：

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

方法有兩個 sigma 值 &mdash; ，第一個是水準方向的模糊範圍，第二個是垂直方向。 您可以指定另一個影像篩選作為選擇性的第三個引數，以串聯影像篩選。 也可以指定裁剪矩形。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)中的 [**影像模糊實驗**] 頁面包含兩個 `Slider` 可讓您試驗設定各種模糊層級的視圖：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會使用兩個 `Slider` 值， `SKImageFilter.CreateBlur` 針對 `SKPaint` 用來顯示文字和點陣圖的物件呼叫：

```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

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

這三個螢幕擷取畫面顯示和設定的各種設定 `sigmaX` `sigmaY` ：

[![影像模糊實驗](image-filters-images/ImageBlurExperiment.png "影像模糊實驗")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

若要在不同的顯示大小和解析度之間保持模糊的一致性，請將和設定為與套用 `sigmaX` `sigmaY` 模糊之影像的呈現圖元大小成正比的值。

## <a name="drop-shadow"></a>陰影

[`SKImageFilter.CreateDropShadow`](xref:SkiaSharp.SKImageFilter.CreateDropShadow*)靜態方法會 `SKImageFilter` 針對陰影建立物件：

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

將此物件設定為 `ImageFilter` 物件的屬性 `SKPaint` ，而您使用該物件繪製的任何專案，其後面都會有陰影。

`dx`和 `dy` 參數指出陰影的水準和垂直位移，以圖元為單位，來自圖形化物件。 2D 圖形中的慣例是假設來自左上方的光源，這表示這兩個引數都應該是正數，以便將陰影放在繪圖物件的下方和右邊。

`sigmaX`和 `sigmaY` 參數是陰影的模糊因素。

`color`參數是陰影的色彩。 此 `SKColor` 值可以包含透明度。 其中一個可能是色彩值， `SKColors.Black.WithAlpha(0x80)` 可將任何色彩背景變暗。

最後兩個參數是選擇性的。

「**放置陰影實驗**」程式可讓您試驗 `dx` 、、和的值， `dy` `sigmaX` `sigmaY` 以顯示具有陰影的文字字串。 XAML 檔案會具現化四個 `Slider` 視圖來設定這些值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會使用這些值，在藍色文字字串上建立紅色陰影：

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
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

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

以下是程式執行情況：

[![捨棄陰影實驗](image-filters-images/DropShadowExperiment.png "捨棄陰影實驗")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

在最右邊的通用 Windows 平臺螢幕擷取畫面中，負位移值會導致陰影出現在文字的上方和左側。 這會建議右下方的光源，這不是電腦圖形的慣例。 但它似乎不會有任何問題，也許是因為陰影也變得非常模糊，而且看起來比大部分的陰影更 ornamental。

## <a name="lighting-effects"></a>光源效果

`SKImageFilter`類別會定義六個具有類似名稱和參數的方法，如下所示，以增加複雜度的順序：

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

這些方法會建立影像篩選器，以模擬3d 表面上不同種類光源的效果。 結果影像篩選器會照亮二維物件，如同它們存在於3D 空間中一樣，這可能會導致這些物件以提高或凹向或以高光顯示。

`Distant`Light 的方法會假設光線來自遠距離。 基於照亮物件的目的，光源會假設為在3D 空間中以一個一致的方向指向，非常類似于地球小型區域的太陽。 `Point`Light 方法會模擬位於3d 空間中的燈泡，以所有方向發出光線。 `Spot`光線具有位置和方向，非常類似閃光燈。

3D 空間中的位置和方向都是以結構的值 [`SKPoint3`](xref:SkiaSharp.SKPoint3) 來指定，這類似于 `SKPoint` ，但具有三個名為 `X` 、和的屬性 `Y` `Z` 。

這些方法的參數數目和複雜度會使測試變得很棘手。 為了讓您開始使用，[**遠光源實驗**] 頁面可讓您試驗方法的參數 `CreateDistantLightDiffuse` ：

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

此頁面不會使用最後兩個選擇性參數。

XAML 檔案中的三個 `Slider` 視圖可讓您選取 `Z` 值的座標 `SKPoint3` 、 `surfaceScale` 參數和 `kd` 參數（在 API 檔中定義為「擴散光源常數」）：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會取得這三個值，並使用它們來建立影像篩選來顯示文字字串：

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
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

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

的第一個引數 `SKImageFilter.CreateDistantLitDiffuse` 是光線的方向。 正 X 和 Y 座標表示光源指向向右和向下。 正 Z 座標會指向螢幕。 XAML 檔案可讓您選取負的 Z 值，但這只是您可以查看發生什麼情況：在概念上，負 Z 座標會導致光線指向螢幕。 若為任何其他值，則為小型負數值，光源效果會停止運作。

`surfaceScale`引數的範圍可以是-1 到1。 （較高或較低的值沒有進一步的效果）。這些是 Z 軸中的相對值，表示畫布介面的繪圖物件（在此例中為文字字串）的置換。 使用負值，將文字字串在畫布的表面上升高，而將其正數值放入畫布中。

這個 `lightConstant` 值應該是正數。 （此程式允許負值，讓您可以看到它們會導致效果停止運作）。較高的值會導致較密集的光線。

當 `surfaceScale` 是負數（如同 iOS 和 Android 螢幕擷取畫面）時，可以平衡這些因素來取得浮凸效果，而當是正數時，則是陰文效果 `surfaceScale` ，如同右邊的 UWP 螢幕擷取畫面：

[![遠光源實驗](image-filters-images/DistantLightExperiment.png "遠光源實驗")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Android 螢幕擷取畫面的 Z 值為0，表示光源只向下和向右。 背景不會亮起，而且文字字串的表面也不會發亮。 光只會影響文字的邊緣，以提供非常細微的效果。

陽文和陰文文字的替代方法已在[轉譯轉換](../transforms/translate.md)文章中示範：文字字串會以不同的色彩顯示兩次，彼此之間有互相的時差。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
