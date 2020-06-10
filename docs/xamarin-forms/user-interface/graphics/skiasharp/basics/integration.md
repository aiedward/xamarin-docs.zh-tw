---
標題：「與」 Xamarin.Forms 描述：」本文說明如何建立回應觸控和元素的 SkiaSharp 圖形 Xamarin.Forms ，並以範例程式碼示範這點。
skiasharp assetid： 288224F1-7AEE-4148-A88D-A70C03F83D7A author： davidbritch ms. 作者： dabritch ms。日期：02/09/2017 不是-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="integrating-with-xamarinforms"></a>與整合Xamarin.Forms

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_建立回應觸控和元素的 SkiaSharp 圖形 Xamarin.Forms_

SkiaSharp 圖形可以透過數種方式與其余部分整合 Xamarin.Forms 。 您可以將 SkiaSharp 畫布和元素結合在 Xamarin.Forms 相同頁面上，甚至將 Xamarin.Forms 元素放置在 SkiaSharp 畫布的頂端：

![](integration-images/integrationexample.png "Selecting a color with sliders")

在中建立互動式 SkiaSharp 圖形的另一種方法 Xamarin.Forms 是透過觸控。
[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中的第二個頁面有權**利用 [切換填滿**]。 它會以兩種方式繪製簡單的圓形 &mdash; ，而不需要填滿，並以點按方式切換填滿 &mdash; 。 [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)類別會顯示如何改變 SkiaSharp 圖形，以回應使用者輸入。

在此頁面中， `SKCanvasView` 類別會在[TapToggleFill](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml)中具現化，這也會在 Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 視圖上設定：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.TapToggleFillPage"
             Title="Tap Toggle Fill">

    <skia:SKCanvasView PaintSurface="OnCanvasViewPaintSurface">
        <skia:SKCanvasView.GestureRecognizers>
            <TapGestureRecognizer Tapped="OnCanvasViewTapped" />
        </skia:SKCanvasView.GestureRecognizers>
    </skia:SKCanvasView>
</ContentPage>
```

請注意 `skia` XML 命名空間宣告。

`Tapped`物件的處理常式 `TapGestureRecognizer` 只會切換 Boolean 欄位的值，並呼叫的 [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) 方法 `SKCanvasView` ：

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

呼叫會 `InvalidateSurface` 有效地產生對 `PaintSurface` 處理常式的呼叫，而這 `showFill` 會使用欄位來填滿或不填滿圓形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 50
    };
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);

    if (showFill)
    {
        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.Blue;
        canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    }
}
```

`StrokeWidth`屬性已設定為50，以強調差異。 您也可以藉由先繪製內部，然後是外框，來查看整行寬度。 根據預設，在事件處理常式中稍後繪製的圖形會 `PaintSurface` 遮蔽先前在處理常式中所繪製的圖表。

[**色彩探索**] 頁面會示範如何整合 SkiaSharp 圖形與其他專案 Xamarin.Forms ，也會示範在 SkiaSharp 中定義色彩的兩個替代方法之間的差異。 靜態方法會根據 [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) `SKColor` 色調-飽和度-亮度模型來建立值：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

靜態 [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) 方法會 `SKColor` 根據類似的色調-飽和度-值模型來建立值：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在這兩種情況下， `h` 引數的範圍是從0到360。 `s`、 `l` 和 `v` 引數的範圍介於0到100之間。 `a`（Alpha 或不透明度）引數的範圍介於0到255之間。

[**ColorExplorePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)會在並排建立兩個 `SKCanvasView` 物件， `StackLayout` `Slider` 以及 `Label` 允許使用者選取 HSL 和 HSV 色彩值的 views：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Basics.ColorExplorePage"
             Title="Color Explore">
    <StackLayout>
        <!-- Hue slider -->
        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference hueSlider},
                              Path=Value,
                              StringFormat='Hue = {0:F0}'}" />

        <!-- Saturation slider -->
        <Slider x:Name="saturationSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference saturationSlider},
                              Path=Value,
                              StringFormat='Saturation = {0:F0}'}" />

        <!-- Lightness slider -->
        <Slider x:Name="lightnessSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference lightnessSlider},
                              Path=Value,
                              StringFormat='Lightness = {0:F0}'}" />

        <!-- HSL canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hslCanvasView"
                               PaintSurface="OnHslCanvasViewPaintSurface" />

            <Label x:Name="hslLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>

        <!-- Value slider -->
        <Slider x:Name="valueSlider"
                Maximum="100"
                Margin="20, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label HorizontalTextAlignment="Center"
               Text="{Binding Source={x:Reference valueSlider},
                              Path=Value,
                              StringFormat='Value = {0:F0}'}" />

        <!-- HSV canvas view -->
        <Grid VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="hsvCanvasView"
                               PaintSurface="OnHsvCanvasViewPaintSurface" />

            <Label x:Name="hsvLabel"
                   HorizontalOptions="Center"
                   VerticalOptions="Center"
                   BackgroundColor="Black"
                   TextColor="White" />
        </Grid>
    </StackLayout>
</ContentPage>
```

這兩個 `SKCanvasView` 元素位於單一儲存格中，其位於 `Grid` 上方， `Label` 以顯示結果的 RGB 色彩值。

[**ColorExplorePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)程式碼後置檔案相當簡單。 這三個元素的共用 `ValueChanged` 處理常式 `Slider` 只會使這兩個 `SKCanvasView` 元素失效。 `PaintSurface`處理常式會清除畫布，其中包含專案所指示的色彩 `Slider` ，同時也會將 `Label` 坐在元素的上方 `SKCanvasView` ：

```csharp
public partial class ColorExplorePage : ContentPage
{
    public ColorExplorePage()
    {
        InitializeComponent();

        hueSlider.Value = 0;
        saturationSlider.Value = 100;
        lightnessSlider.Value = 50;
        valueSlider.Value = 100;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        hslCanvasView.InvalidateSurface();
        hsvCanvasView.InvalidateSurface();
    }

    void OnHslCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsl((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)lightnessSlider.Value);
        args.Surface.Canvas.Clear(color);

        hslLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }

    void OnHsvCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKColor color = SKColor.FromHsv((float)hueSlider.Value,
                                        (float)saturationSlider.Value,
                                        (float)valueSlider.Value);
        args.Surface.Canvas.Clear(color);

        hsvLabel.Text = String.Format(" RGB = {0:X2}-{1:X2}-{2:X2} ",
                                      color.Red, color.Green, color.Blue);
    }
}
```

在 HSL 和 HSV 色彩模型中，色調值的範圍是從0到360，表示色彩的主要色調。 這些是彩虹的傳統色彩：紅色、橙色、黃色、綠色、藍色、靛藍色、紫羅蘭，以及從圓形到紅色的反白顯示。

在 HSL 模型中，亮度的0值一律為黑色，而100值一律為白色。 當飽和度值為0時，介於0到100之間的亮度值為灰色的色度。 增加飽和度會增加更多色彩。 純色（其為 RGB 值，其中一個元件等於255、另一個等於0，而第三個範圍介於0到255）則會在飽和度為100且亮度為50時發生。

在 HSV 模型中，當飽和度和值都是100時，純粹色彩就會產生。 當值為0時，不論任何其他設定為何，色彩都會是黑色。 當飽和度為0，且值的範圍從0到100時，就會發生灰色陰影。

但是要瞭解這兩個模型的最佳方式，就是自行試驗：

[![](integration-images/colorexplore-large.png "Triple screenshot of the Color Explore page")](integration-images/colorexplore-small.png#lightbox "Triple screenshot of the Color Explore page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
