---
標題：「SkiaSharp mask 篩選器」描述：「瞭解如何使用遮罩篩選器來建立模糊和其他 Alpha 效果」。
skiasharp 的 assetid： 940422A1-8BC0-4039-8AD7-26C61320F858 author： davidbritch ms-chap： dabritch ms. date： 08/27/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="skiasharp-mask-filters"></a>SkiaSharp mask 篩選

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

遮罩篩選是操作繪圖物件之幾何和 Alpha 色板的效果。 若要使用遮罩篩選，請將的 [`MaskFilter`](xref:SkiaSharp.SKPaint.MaskFilter) 屬性設定 `SKPaint` 為您透過 [`SKMaskFilter`](xref:SkiaSharp.SKMaskFilter) 呼叫其中一個靜態方法所建立之類型的物件 `SKMaskFilter` 。

熟悉遮罩篩選器的最佳方式，就是使用這些靜態方法來進行實驗。 最有用的遮罩篩選器會建立模糊：

![模糊範例](mask-filters-images/MaskFilterExample.png "模糊範例")

這是本文中所述的唯一遮罩篩選功能。 [**SkiaSharp 影像篩選器**](image-filters.md)的下一篇文章也會描述您可能偏好使用的模糊效果。 

靜態 [`SKMaskFilter.CreateBlur`](xref:SkiaSharp.SKMaskFilter.CreateBlur(SkiaSharp.SKBlurStyle,System.Single)) 方法具有下列語法：

```csharp
public static SKMaskFilter CreateBlur (SKBlurStyle blurStyle, float sigma);
```

多載允許針對用來建立模糊的演算法指定旗標，而矩形則可避免在其他繪圖物件所涵蓋的區域中模糊。

[`SKBlurStyle`](xref:SkiaSharp.SKBlurStyle)是具有下列成員的列舉：

- `Normal`
- `Solid`
- `Outer`
- `Inner`

這些樣式的效果如下列範例所示。 `sigma`參數會指定模糊的範圍。 在較舊版本的 Skia 中，模糊的範圍會以半徑值表示。 如果您的應用程式偏好 radius 值，有一個 [`SKMaskFilter.ConvertRadiusToSigma`](xref:SkiaSharp.SKMaskFilter.ConvertRadiusToSigma*) 可從一個轉換成另一個的靜態方法。 方法會將半徑乘以0.57735，並新增0.5。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的 [**遮罩模糊實驗**] 頁面可讓您使用模糊樣式和 sigma 值來進行實驗。 XAML 檔案會具現化 `Picker` 具有四個 `SKBlurStyle` 列舉成員的，以及用 `Slider` 來指定 sigma 值的：

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

程式碼後置檔案會使用這些值來建立 `SKMaskFilter` 物件，並將它設定為 `MaskFilter` 物件的屬性 `SKPaint` 。 這個 `SKPaint` 物件是用來同時繪製文字字串和點陣圖：

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

以下是在 iOS、Android 和通用 Windows 平臺（UWP）上執行且具有 `Normal` 模糊樣式和增加 `sigma` 層級的程式：

[![遮罩模糊實驗-一般](mask-filters-images/MaskBlurExperiment-Normal.png "遮罩模糊實驗-一般")](mask-filters-images/MaskBlurExperiment-Normal-Large.png#lightbox)

您會注意到，只有點陣圖邊緣會受到模糊的影響。 `SKMaskFilter`如果您想要將整個點陣圖影像模糊，類別不是正確的效果。 針對這種情況，您會想要使用 [`SKImageFilter`](xref:SkiaSharp.SKImageFilter) 類別，如[**SkiaSharp 影像篩選器**](image-filters.md)的下一篇文章中所述。

隨著引數值的增加，文字會變得更模糊 `sigma` 。 在試驗此計畫時，您會注意到對於特定的 `sigma` 值而言，模糊在 Windows 10 桌面上較為極端。 這項差異是因為桌面監視器上的圖元密度低於行動裝置，因此文字高度（以圖元為單位）較低。 此 `sigma` 值與模糊範圍成正比，以圖元為單位，因此針對指定的 `sigma` 值，此效果在較低解析度的顯示上會比較極端。 在生產應用程式中，您可能會想要計算與 `sigma` 圖形大小成正比的值。 

請先嘗試幾個值，再以模糊層級來進行，以查看您的應用程式的最佳效果。 例如，在 [**遮罩模糊實驗**] 頁面中，嘗試設定 `sigma` 如下：

```csharp
sigma = paint.TextSize / 18;
paint.MaskFilter = SKMaskFilter.CreateBlur(blurStyle, sigma);
```

現在 `Slider` 不會有任何作用，但模糊程度在各平臺之間是一致的：

[![遮罩模糊實驗-一致](mask-filters-images/MaskBlurExperiment-Consistent.png "遮罩模糊實驗-一致")](mask-filters-images/MaskBlurExperiment-Consistent-Large.png#lightbox)

目前為止的所有螢幕擷取畫面都顯示了使用列舉成員建立的模糊 `SKBlurStyle.Normal` 。 下列螢幕擷取畫面顯示 `Solid` 、 `Outer` 和 `Inner` 模糊樣式的效果：

[![遮罩模糊實驗](mask-filters-images/MaskBlurExperiment.png "遮罩模糊實驗")](mask-filters-images/MaskBlurExperiment-Large.png#lightbox)

IOS 螢幕擷取畫面顯示 `Solid` 樣式：文字字元仍會顯示為實心黑色筆劃，而模糊會加入至這些文字字元的外部。 

中間的 Android 螢幕擷取畫面 `Outer` 會顯示樣式：字元筆劃本身會被刪除（如同點陣圖），而模糊會括住出現文字字元的空白空間。 

右側的 UWP 螢幕擷取畫面顯示 `Inner` 樣式。 模糊僅限於文字字元通常會佔用的區域。

[**SkiaSharp 線性**](shaders/linear-gradient.md#transparency-and-gradients)漸層文章描述了使用線性漸層和轉換來模擬文字字串反映的**反映梯度**程式：

[![反映梯度](shaders/linear-gradient-images/ReflectionGradient.png "反映梯度")](shaders/linear-gradient-images/ReflectionGradient-Large.png#lightbox)

**模糊反映**頁面會將單一語句加入至該程式碼：

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

新的語句會針對以文字大小為基礎的反映文字加入模糊篩選準則：

```csharp
paint.MaskFilter = SKMaskFilter.CreateBlur(SKBlurStyle.Normal, paint.TextSize / 36);
```

這個模糊篩選會使反映看起來更加逼真：

[![模糊反映](mask-filters-images/BlurryReflection.png "模糊反映")](mask-filters-images/BlurryReflection-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
