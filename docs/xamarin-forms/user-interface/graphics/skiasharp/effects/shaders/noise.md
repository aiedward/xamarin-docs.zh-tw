---
title: SkiaSharp 雜訊和撰寫
description: 產生 Perlin 雜訊著色器，並結合其他著色器。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 90C2D00A-2876-43EA-A836-538C3318CF93
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 4801aa12acf8eca2384cc5b41d677f7cb0bdd90d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61292913"
---
# <a name="skiasharp-noise-and-composing"></a>SkiaSharp 雜訊和撰寫

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

簡單的向量圖形，通常看起來不自然。 直線，線條平滑的曲線，純色不類似真實世界物件缺陷的而。 使用的電腦產生的圖形： 1982年電影時，才能_仲恩_，電腦科學家 Ken Perlin 開始開發使用隨機的程序讓這些映像更真實的紋理的演算法。 在 1997 年 Ken Perlin 會贏得技術分別 Academy 獎。 他的工作都可稱為 Perlin 雜訊和 SkiaSharp 中支援它。 以下為範例：

![Perlin 雜訊範例](noise-images/NoiseSample.png "Perlin 雜訊範例")

如您所見，每個像素不是隨機的色彩值。 從像素的持續性，以像素會產生隨機的圖形。

Skia Perlin 雜訊的支援以 W3C 規格 CSS 和 SVG。 區段 8.20 [**篩選器效果模組層級 1** ](http://www.w3.org/TR/filter-effects-1/#feTurbulenceElement) C 程式碼中包含的基礎 Perlin 雜訊演算法。

## <a name="exploring-perlin-noise"></a>瀏覽 Perlin 雜訊

[ `SKShader` ](xref:SkiaSharp.SKShader)類別會定義兩個不同的靜態方法，來產生 Perlin 雜訊： [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*)並[ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*)。 參數完全相同：

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

這兩種方法也存在於多載版本，加上一個`SKPointI`參數。 一節[**並排顯示 Perlin 雜訊**](#tiling-perlin-noise)討論這些多載。

這兩個`baseFrequency`引數皆為範圍從 0 到 1，SkiaSharp 文件中定義的正數值，但它們可以設定為較高的值。 值越大，隨機的映像中的水平和垂直方向的愈大的變更。

`numOctaves`值是 1 或更高版本的整數。 它與演算法中的反覆項目的因素。 每個額外的 octave 貢獻是一半的上一個 octave，因此效果就會減少，較高的 octave 值的效果。

`seed`參數是亂數產生器的起始點。 雖然指定為浮點值，這個分數會遭到截斷，才能使用它，而且 0 與 1 相同。

**Perlin 雜訊**頁面[ **SkiaSharpFormsDemos**)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例可讓您試驗各種值`baseFrequency`和`numOctaves`引數。 以下是 XAML 檔案：

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

它會使用兩個`Slider`兩個檢視`baseFrequency`引數。 若要展開的較低的值範圍，滑桿是對數。 程式碼後置檔案會計算的引數`SKShader`方法的次方`Slider`值。 `Label`檢視顯示計算的值：

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

A`Slider`為 1 的值會對應到 0.001， `Slider` os 2 的值對應至 0.01， `Slider` 3 的值會對應到 0.1 之間，和`Slider`對應到 1 的值為 4。

以下是包含該程式碼的程式碼後置檔案：

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

以下是裝置 iOS、 Android 和通用 Windows 平台 (UWP) 上執行的程式。 碎形雜訊會顯示在畫布的上半部。 動盪一同雜訊會在底部下半部：

[![Perlin 雜訊](noise-images/PerlinNoise.png "Perlin 雜訊")](noise-images/PerlinNoise-Large.png#lightbox)

相同的引數一定會產生相同的模式在左上角開始。 當您調整 [UWP] 視窗的高度與寬度時，這種一致性是明顯。 Windows 10 中，會重新繪製畫面，因為在畫布的上半部中的模式維持不變。

雜訊模式納入不同程度的透明度。 透明度就很明顯，如果您設定色彩`canvas.Clear()`呼叫。 該色彩會成為顯著的模式。 您也會看到此區段中的效果[**結合多個著色器**](#combining-multiple-shaders)。

這些 Perlin 雜訊模式很少會單獨使用。 通常，它們受制於混合模式和更新版本的文章中討論的色彩篩選。

## <a name="tiling-perlin-noise"></a>並排顯示 Perlin 雜訊

兩個靜態`SKShader`建立 Perlin 雜訊的方法也存在於多載版本。 [ `CreatePerlinNoiseFractalNoise` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))並[ `CreatePerlinNoiseTurbulence` ](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))多載包含有額外`SKPointI`參數：

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

[ `SKPointI` ](xref:SkiaSharp.SKPointI)結構是熟悉的整數版本[ `SKPoint` ](xref:SkiaSharp.SKPoint)結構。 `SKPointI` 定義`X`並`Y`型別的屬性`int`而非`float`。

這些方法會建立指定大小之重複花紋。 每個圖格中，右邊緣等同於左邊緣，且上邊緣與相同的下邊緣。 這項特性所示**並排顯示 Perlin 雜訊**頁面。 XAML 檔案類似於先前的範例中，但它只有`Stepper`變更檢視`seed`引數：

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

程式碼後置檔案中定義的常數，並排顯示大小。 `PaintSurface`處理常式會建立該大小的點陣圖和`SKCanvas`以便繪製到那個點陣圖。 `SKShader.CreatePerlinNoiseTurbulence`方法建立著色器，該圖格大小。 這個著色器會繪製點陣圖：

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

點陣圖建立之後，另一個後`SKPaint`物件用來建立並排顯示的點陣圖模式藉由呼叫`SKShader.CreateBitmap`。 請注意兩個引數的`SKShaderTileMode.Repeat`:

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

這個著色器用來涵蓋在畫布。 最後，另一個`SKPaint`物件用來繪製矩形，顯示原始點陣圖的大小。

只有`seed`參數是可選取的使用者介面中。 如果相同`seed`模式會使用每個平台上，它們會顯示相同的模式。 不同`seed`值會導致不同的模式：

[![並排顯示 Perlin 雜訊](noise-images/TiledPerlinNoise.png "並排 Perlin 雜訊")](noise-images/TiledPerlinNoise-Large.png#lightbox)

左上角中的 200 像素正方形模式順暢地流動到其他牌。

## <a name="combining-multiple-shaders"></a>結合多個著色器

`SKShader`類別包含[ `CreateColor` ](xref:SkiaSharp.SKShader.CreateColor*)方法，以建立著色器，使用指定的純色。 這個著色器不單獨使用時非常有用，因為您只可以將該色彩設定為`Color`的屬性`SKPaint`物件，並設定`Shader`屬性設為 null。

這`CreateColor`方法會在另一個方法很有用，`SKShader`定義。 這個方法很[ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader))，其中結合了兩個著色器。 以下是語法：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

`srcShader` （來源著色器） 有效地繪製頂端`dstShader`（目的地著色器）。 如果來源著色器是純色或不透明的漸層時，會被完全遮住目的地著色器。

Perlin 雜訊著色器包含透明度。 如果該著色器是來源、 目的地著色器會顯示透過透明區域。

**組成 Perlin 雜訊**頁面上有 XAML 檔案，幾乎等同於第一個**Perlin 雜訊**頁面。 程式碼後置檔案也是類似的。 而原始檔案**Perlin 雜訊**頁面上設定`Shader`屬性`SKPaint`著色器傳回的靜態`CreatePerlinNoiseFractalNoise`和`CreatePerlinNoiseTurbulence`方法。 這**組成 Perlin 雜訊**頁面上呼叫`CreateCompose`組合著色器。 目的地是純色的藍色著色器，以建立使用`CreateColor`。 來源是 Perlin 雜訊著色器：

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

碎形雜訊著色器是在最上層;下方是動盪一同著色器：

[![組成 Perlin 雜訊](noise-images/ComposedPerlinNoise.png "組成 Perlin 雜訊")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

請注意，多少 bluer 這些著色器是顯示還要**Perlin 雜訊**頁面。 差異說明的雜訊著色器中的透明度。

另外還有多載[ `CreateCompose` ](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode))方法：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

最後一個參數是隸屬`SKBlendMode`列舉型別、 列舉型別會在下一系列的文章中討論的 29 成員[ **SkiaSharp 複合 （compositing） 和 blend 模式**](../blend-modes/index.md)。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
