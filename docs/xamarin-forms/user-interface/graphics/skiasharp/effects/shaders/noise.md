---
title：「SkiaSharp 雜訊和撰寫」描述：「產生 Perlin 的雜訊著色器並與其他著色器結合」。
skiasharp 的 assetid： 90C2D00A-2876-43EA-A836-538C3318CF93 author： davidbritch ms-chap： dabritch ms. date： 08/23/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="skiasharp-noise-and-composing"></a>SkiaSharp 雜訊和撰寫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

簡單向量圖形通常會看起來非自然。 直線、平滑曲線和純色不像真實世界物件的缺陷。 在處理 1982 movie _Tron_的電腦產生的圖形時，電腦科學家 Ken Perlin 開始開發使用隨機進程的演算法，以提供這些影像更逼真的材質。 在1997中，Ken Perlin 贏得學術獎項以進行技術成就。 他的工作也稱為 Perlin 雜訊，並在 SkiaSharp 中受到支援。 以下是範例：

![Perlin 雜訊範例](noise-images/NoiseSample.png "Perlin 雜訊範例")

如您所見，每個圖元都不是隨機色彩值。 從圖元到圖元的持續性會產生隨機圖形。

Skia 中的 Perlin 雜訊支援是以 CSS 和 SVG 的 W3C 規格為基礎。 第8.20 節的[**篩選效果模組層級 1**](https://www.w3.org/TR/filter-effects-1/#feTurbulenceElement)包含 C 程式碼中的基礎 Perlin 雜訊演算法。

## <a name="exploring-perlin-noise"></a>探索 Perlin 雜訊

[`SKShader`](xref:SkiaSharp.SKShader)類別會定義兩個不同的靜態方法，以產生 Perlin 雜訊： [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise*) 和 [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseTurbulence*) 。 參數完全相同：

```csharp
public static SkiaSharp CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);

public static SkiaSharp.SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed);
```

這兩種方法也都存在於具有額外參數的多載版本中 `SKPointI` 。 並排[**Perlin 雜訊**](#tiling-perlin-noise)一節會討論這些多載。

這兩個 `baseFrequency` 引數是在 SkiaSharp 檔中定義的正值，範圍介於0到1之間，但也可以設定為較高的值。 值愈高，水準和垂直方向的隨機影像中的變更就愈大。

`numOctaves`值為1或更高的整數。 它與演算法中的反復專案因素相關。 每個額外的 octave 會提供一項效果，這是前一個 octave 的一半，因此效果會以較高的 octave 值來減少。

`seed`參數是亂數產生器的起點。 雖然指定為浮點值，但會在使用時截斷分數，而0則與1相同。

[ **SkiaSharpFormsDemos**）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的 [ **Perlin 雜訊**] 頁面，可讓您使用 `baseFrequency` 和引數的各種值進行實驗 `numOctaves` 。 以下是 XAML 檔案：

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

它會 `Slider` 針對這兩個引數使用兩個視圖 `baseFrequency` 。 若要展開較低值的範圍，滑杆為對數。 程式碼後置檔案會 `SKShader` 從值的冪計算方法的引數 `Slider` 。 `Label`Views 會顯示計算值：

```csharp
float baseFreqX = (float)Math.Pow(10, baseFrequencyXSlider.Value - 4);
baseFrequencyXText.Text = String.Format("Base Frequency X = {0:F4}", baseFreqX);

float baseFreqY = (float)Math.Pow(10, baseFrequencyYSlider.Value - 4);
baseFrequencyYText.Text = String.Format("Base Frequency Y = {0:F4}", baseFreqY);
```

`Slider`值1對應于0.001， `Slider` 值 os 2 對應于0.01， `Slider` 3 的值對應至0.1，而 `Slider` 值4則對應至1。

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

以下是在 iOS、Android 和通用 Windows 平臺（UWP）裝置上執行的程式。 碎形雜訊會顯示在畫布的上半部。 晃動雜訊位於下半部：

[![Perlin 雜訊](noise-images/PerlinNoise.png "Perlin 雜訊")](noise-images/PerlinNoise-Large.png#lightbox)

相同的引數一律會產生相同的模式，從左上角開始。 當您調整 UWP 視窗的寬度和高度時，這種一致性很明顯。 當 Windows 10 重新繪製螢幕時，畫布上半部的模式會維持不變。

雜訊模式會合並各種程度的透明度。 如果您在呼叫中設定色彩，則透明度會變得很明顯 `canvas.Clear()` 。 該色彩在模式中會變得很明顯。 您也會在[**結合多個著色**](#combining-multiple-shaders)器的區段中看到這種效果。

這些 Perlin 雜訊模式本身很少使用。 它們通常會受到在後續文章中討論的 blend 模式和色彩篩選。

## <a name="tiling-perlin-noise"></a>並排 Perlin 雜訊

建立 Perlin 雜訊的兩個靜態 `SKShader` 方法也存在於多載版本中。 [`CreatePerlinNoiseFractalNoise`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI))和 [`CreatePerlinNoiseTurbulence`](xref:SkiaSharp.SKShader.CreatePerlinNoiseFractalNoise(System.Single,System.Single,System.Int32,System.Single,SkiaSharp.SKPointI)) 多載有額外的 `SKPointI` 參數：

```csharp
public static SKShader CreatePerlinNoiseFractalNoise (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);

public static SKShader CreatePerlinNoiseTurbulence (float baseFrequencyX, float baseFrequencyY, int numOctaves, float seed, SKPointI tileSize);
```

[`SKPointI`](xref:SkiaSharp.SKPointI)結構是熟悉結構的整數版本 [`SKPoint`](xref:SkiaSharp.SKPoint) 。 `SKPointI`定義 `X` `Y` 類型的和屬性， `int` 而不是 `float` 。

這些方法會建立指定大小的重複模式。 在每個磚中，右邊緣與左邊緣相同，而上邊緣則與下邊緣相同。 此特性會在 [並排顯示**Perlin 雜訊**] 頁面中示範。 XAML 檔案與上一個範例類似，但它只具有 `Stepper` 變更 `seed` 引數的 view：

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

程式碼後置檔案會定義磚大小的常數。 `PaintSurface`處理常式會建立該大小的點陣圖和 `SKCanvas` 用於繪製到該點陣圖的。 `SKShader.CreatePerlinNoiseTurbulence`方法會建立具有該磚大小的著色器。 這個著色器會繪製在點陣圖上：

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

建立點陣圖之後，會使用另一個 `SKPaint` 物件，藉由呼叫來建立磚點陣圖模式 `SKShader.CreateBitmap` 。 請注意的兩個引數 `SKShaderTileMode.Repeat` ：

```csharp
paint.Shader = SKShader.CreateBitmap(bitmap,
                                     SKShaderTileMode.Repeat,
                                     SKShaderTileMode.Repeat);
```

這個著色器是用來涵蓋畫布。 最後，使用另一個 `SKPaint` 物件來繪製矩形，以顯示原始點陣圖的大小。

只有 `seed` 參數可從使用者介面選取。 如果在 `seed` 每個平臺上使用相同的模式，則會顯示相同的模式。 不同 `seed` 的值會產生不同的模式：

[![並排顯示 Perlin 雜訊](noise-images/TiledPerlinNoise.png "並排顯示 Perlin 雜訊")](noise-images/TiledPerlinNoise-Large.png#lightbox)

左上角的 200-圖元正方形模式會順暢地流入其他磚。

## <a name="combining-multiple-shaders"></a>結合多個著色器

`SKShader`類別包含一個 [`CreateColor`](xref:SkiaSharp.SKShader.CreateColor*) 方法，可使用指定的純色來建立著色器。 這個著色器本身並不實用，因為您可以直接將該色彩設定為 `Color` 物件的屬性 `SKPaint` ，並將 `Shader` 屬性設定為 null。

這個 `CreateColor` 方法在定義的另一個方法中會變得很有用 `SKShader` 。 這個方法是 [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader)) 結合兩個著色器。 語法如下：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader);
```

`srcShader`（來源著色器）實際上是在 `dstShader` （目的地著色器）之上繪製。 如果來源著色器是單色或漸層沒有透明度，則目的地著色器將完全遮蔽。

Perlin 的雜訊著色器包含透明度。 如果該著色器是來源，則目的地著色器會顯示在透明區域中。

[**撰寫 Perlin 雜訊**] 頁面的 XAML 檔案，與第一個**Perlin 的雜訊**頁幾乎完全相同。 程式碼後置檔案也很類似。 但原始的**Perlin 雜訊**頁面會將的 `Shader` 屬性設定 `SKPaint` 為從靜態和方法傳回的著色器 `CreatePerlinNoiseFractalNoise` `CreatePerlinNoiseTurbulence` 。 這會針對組合著色器的**Perlin 雜訊**頁面 `CreateCompose` 進行呼叫。 目的地是使用建立的實心藍色著色器 `CreateColor` 。 來源是 Perlin 的雜訊著色器：

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

碎形雜訊著色器位於頂端;晃動著色器位於底部：

[![組成 Perlin 雜訊](noise-images/ComposedPerlinNoise.png "組成 Perlin 雜訊")](noise-images/ComposedPerlinNoise-Large.png#lightbox)

請注意，這些著色器的 bluer 量與**Perlin 聲**頁所顯示的不同。 差異說明雜訊著色器中的透明度程度。

方法也有多載 [`CreateCompose`](xref:SkiaSharp.SKShader.CreateCompose(SkiaSharp.SKShader,SkiaSharp.SKShader,SkiaSharp.SKBlendMode)) ：

```csharp
public static SKShader CreateCompose (SKShader dstShader, SKShader srcShader, SKBlendMode blendMode);
```

最後一個參數是列舉的成員 `SKBlendMode` ，這是包含29個成員的列舉，會在[**SkiaSharp 複合和 blend 模式**](../blend-modes/index.md)的下一系列文章中討論。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
