---
title: SkiaSharp 雜訊和撰寫
description: 產生 Perlin 雜訊著色器，並與其他著色器合併。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 00b6251f530a4927d069ae92ec919645a06baf15
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555394"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp 雜訊和撰寫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

簡單向量圖形通常會看起來非自然。 直線、平滑曲線和純色不像真實世界物件的缺陷。 在針對 1982 movie _Tron_的電腦產生的圖形上工作時，電腦科學家 Ken Perlin 開始開發使用隨機進程的演算法，以提供這些影像更逼真的材質。 在1997中，Ken Perlin 贏得了技術成就的學術獎項。 他的工作就是所謂的 Perlin 雜訊，而 SkiaSharp 也有支援。 以下是範例：

![Perlin 雜訊範例](noise-images/NoiseSample.png "Perlin 雜訊範例")

如您所見，每個圖元都不是隨機的色彩值。 從圖元到圖元的持續性會產生隨機形狀。

Skia 中的 Perlin 雜訊支援是以 W3C 規格（適用于 CSS 和 SVG）為基礎。 篩選效果的第8.20 節 [**模組層級 1**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) 包含 C 程式碼中的基礎 Perlin 雜訊演算法。

## <a name="exploring-perlin-noise"></a>探索 Perlin 雜訊

[`SKShader`](xref:SkiaSharp.SKShader)類別會定義兩個不同的靜態方法，以產生 Perlin 雜訊： [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) 和 [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*) 。 參數完全相同：

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

這兩種方法也會存在於具有額外參數的多載版本中 `SKPointI` 。 圖格 [**Perlin 雜訊**](#tiling-perlin-noise) 章節會討論這些多載。

這兩個 `baseFrequency` 引數是 SkiaSharp 檔中定義的正值，範圍從0到1，但也可以設定為較高的值。 值愈高，在水準和垂直方向的隨機影像變更就越大。

`numOctaves`值是1或更高的整數。 這與演算法中的反復專案因數相關。 每個額外的 octave 都有一個效果，也就是上一個 octave 的一半，因此效果會以較高的 octave 值減少。

`seed`參數是亂數產生器的起點。 雖然指定為浮點值，但在使用之前會截斷分數，0與1相同。

[ **SkiaSharpFormsDemos**) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的 [ **Perlin 雜訊**] 頁面，可讓您使用 `baseFrequency` 和引數的各種值進行實驗 `numOctaves` 。 以下是 XAML 檔案：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PerlinNoisePage"
             Title="Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="baseFrequencyXSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyXText"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="baseFrequencyYSlider"
                Maximum="4"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="baseFrequencyYText"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference octavesStepper},
                                  Path=Value,
                                  StringFormat='Number of Octaves: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="octavesStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

它使用兩個兩個 `Slider` `baseFrequency` 引數的視圖。 若要展開較低值的範圍，滑杆為對數。 程式碼後端檔案會計算 `SKShader` 來自值之方法的引數 `Slider` 。 `Label`Views 會顯示計算的值：

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

`Slider`1 的值對應到0.001、 `Slider` 值 os 2 對應到0.01、 `Slider` 3 的值對應至0.1，而 `Slider` 值4對應至1。

以下是包含該程式碼的程式碼後端檔案：

```csharp
public partial class PerlinNoisePage : ContentPage
{
    public PerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader =
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0);

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader =
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0);

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

以下是在 iOS、Android 和通用 Windows 平臺 (UWP) 裝置上執行的程式。 碎形雜訊會顯示在畫布的上半部。 Turbulence 雜訊位於下半部：

[![Perlin 雜訊](noise-images/PerlinNoise.png "Perlin 雜訊")](noise-images/PerlinNoise-Large.png#lightbox)

相同的引數一律會產生相同的模式，從左上角開始。 當您調整 UWP 視窗的寬度和高度時，這項一致性會很明顯。 當 Windows 10 重新繪製畫面時，畫布上半部的模式會維持不變。

雜訊模式會合並各種程度的透明度。 如果您在呼叫中設定色彩，透明度會變得很明顯 `canvas.Clear()` 。 該色彩在模式中會變得很明顯。 您也會在 [**結合多個著色**](#combining-multiple-shaders)器的區段中看到此效果。

這些 Perlin 雜訊模式本身很少使用。 通常會受限於在後續文章中討論的 blend 模式和色彩篩選。

## <a name="tiling-perlin-noise"></a>平平 Perlin 雜訊

建立 Perlin 雜訊的兩個靜態 `SKShader` 方法也存在於多載版本中。 [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))和 [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) 多載有一個額外的 `SKPointI` 參數：

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

[`SKPointI`](xref:SkiaSharp.SKPointI)結構是熟悉結構的整數版本 [`SKPoint`](xref:SkiaSharp.SKPoint) 。 `SKPointI` 定義 `X` `Y` 類型的和屬性， `int` 而不是 `float` 。

這些方法會建立指定大小的重複模式。 在每個磚中，右邊緣與左邊緣相同，而頂端邊緣與下邊緣相同。 此特性會在 [並排顯示的 **Perlin 雜訊** ] 頁面中示範。 XAML 檔案類似于前一個範例，但它只會有 `Stepper` 變更 `seed` 引數的視圖：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.TiledPerlinNoisePage"
             Title="Tiled Perlin Noise">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     HorizontalOptions="Center"
                     Margin="10">

            <Label Text="{Binding Source={x:Reference seedStepper},
                                  Path=Value,
                                  StringFormat='Seed: {0:F0}'}"
                   VerticalOptions="Center" />

            <Stepper x:Name="seedStepper"
                     Minimum="1"
                     ValueChanged="OnStepperValueChanged" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

程式碼後端檔案會針對磚大小定義常數。 `PaintSurface`處理常式會建立該大小的點陣圖，並將 `SKCanvas` 繪製至該點陣圖。 `SKShader.CreatePerlinNoiseTurbulence`方法會建立具有該磚大小的著色器。 此著色器會在點陣圖上繪製：

```csharp
public partial class TiledPerlinNoisePage : ContentPage
{
    const int TILE_SIZE = 200;

    public TiledPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get seed value from stepper
        float seed = (float)seedStepper.Value;

        SKRect tileRect = new SKRect(0, 0, TILE_SIZE, TILE_SIZE);

        using (SKBitmap bitmap = new SKBitmap(TILE_SIZE, TILE_SIZE))
        {
            using (SKCanvas bitmapCanvas = new SKCanvas(bitmap))
            {
                bitmapCanvas.Clear();

                // Draw tiled turbulence noise on bitmap
                using (SKPaint paint = new SKPaint())
                {
                    paint.Shader = SKShader.CreatePerlinNoiseTurbulence(
                                        0.02f, 0.02f, 1, seed,
                                        new SKPointI(TILE_SIZE, TILE_SIZE));

                    bitmapCanvas.DrawRect(tileRect, paint);
                }
            }

            // Draw tiled bitmap shader on canvas
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
                canvas.DrawRect(info.Rect, paint);
            }

            // Draw rectangle showing tile
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 2;

                canvas.DrawRect(tileRect, paint);
            }
        }
    }
}
```

建立點陣圖之後，會使用另一個 `SKPaint` 物件來呼叫來建立並排點陣圖模式 `SKShader.CreateBitmap` 。 請注意下列兩個引數 `SKShaderTileMode.Repeat` ：

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

此著色器用來涵蓋畫布。 最後，另一個 `SKPaint` 物件是用來對顯示原始點陣圖大小的矩形進行筆觸。

只有 `seed` 參數可以從使用者介面選取。 如果在 `seed` 每個平臺上使用相同的模式，則會顯示相同的模式。 不同 `seed` 的值會導致不同的模式：

[![平 Perlin 雜訊](noise-images/TiledPerlinNoise.png "平 Perlin 雜訊")](noise-images/TiledPerlinNoise-Large.png#lightbox)

左上角的 200-圖元正方形模式會順暢地流入其他磚。

## <a name="combining-multiple-shaders"></a>結合多個著色器

`SKShader`類別包含一個 [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) 方法，此方法會使用指定的純色建立著色器。 此著色器本身不太實用，因為您可以只將該色彩設定為 `Color` 物件的屬性 `SKPaint` ，並將 `Shader` 屬性設定為 null。

這個 `CreateColor` 方法在定義的另一個方法中變得很有用 `SKShader` 。 這是 [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)) 結合兩個著色器的方法。 語法如下：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

`srcShader` (來源著色器) 實際上是在 `dstShader` (目的地著色器) 上繪製。 如果來源著色器是純色或沒有透明度的漸層，則會完全遮蔽目的地著色器。

Perlin 雜訊著色器包含透明度。 如果該著色器是來源，則目的地著色器會透過透明區域顯示。

**組成的 Perlin 雜訊**頁面具有與第一個**Perlin 雜訊**頁面幾乎相同的 XAML 檔案。 程式碼後端檔案也很類似。 但是，原始的 **Perlin 雜訊** 頁面會將的 `Shader` 屬性設定 `SKPaint` 為從靜態和方法傳回的著色器 `CreatePerlinNoiseFractalNoise` `CreatePerlinNoiseTurbulence` 。 這會為組合著色器 **撰寫 Perlin 雜訊** 頁呼叫 `CreateCompose` 。 目的地是使用所建立的立體藍色著色器 `CreateColor` 。 來源是 Perlin 雜訊著色器：

```csharp
public partial class ComposedPerlinNoisePage : ContentPage
{
    public ComposedPerlinNoisePage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnStepperValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders and stepper
        float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
        baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

        float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
        baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);

        int numOctaves = (int)octavesStepper.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseFractalNoise(baseFreqX,
                                                       baseFreqY,
                                                       numOctaves,
                                                       0));

            SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
            canvas.DrawRect(rect, paint);

            paint.Shader = SKShader.CreateCompose(
                SKShader.CreateColor(SKColors.Blue),
                SKShader.CreatePerlinNoiseTurbulence(baseFreqX,
                                                     baseFreqY,
                                                     numOctaves,
                                                     0));

            rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

碎形雜訊著色器位於頂端;turbulence 著色器位於底部：

[![組成 Perlin 雜訊](noise-images/ComposedPerlinNoise.png "組成 Perlin 雜訊")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

請注意，這些著色器的 bluer 量與 **Perlin 雜訊** 頁面所顯示的不同。 差異說明雜訊著色器中的透明度。

另外還有方法的多載 [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) ：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

最後一個參數是列舉的成員 `SKBlendMode` ，這是包含29個成員的列舉，在 [**SkiaSharp 複合和 blend 模式**](../blend-modes/index.md)的下一篇文章中有討論。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)