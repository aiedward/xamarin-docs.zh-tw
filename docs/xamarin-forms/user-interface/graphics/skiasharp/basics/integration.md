---
title: 整合 Xamarin.Forms
description: 本文說明如何建立 SkiaSharp 圖形來回應觸控和 Xamarin.Forms 元素，並以範例程式碼示範這點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8288ad3238babe1ce6abb6d9517cdae71373d27c
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366603"
---
# <a name="integrating-with-xamarinforms"></a>整合 Xamarin.Forms

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_建立回應觸控和元素的 SkiaSharp 圖形 Xamarin.Forms_

SkiaSharp 圖形可以透過數種方式與其余的圖形整合 Xamarin.Forms 。 您可以將 SkiaSharp 畫布和元素合併在 Xamarin.Forms 相同的頁面上，甚至將專案放 Xamarin.Forms 在 SkiaSharp 畫布頂端：

![使用滑杆選取色彩](integration-images/integrationexample.png)

建立互動式 SkiaSharp 圖形的另一種方法 Xamarin.Forms 是透過觸控。
[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中的第二個頁面有權按下 [**切換填滿**]。 它會以兩種方式繪製簡單圓圈 &mdash; ，而不填滿，並以點一下切換填滿 &mdash; 。 [`TapToggleFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)類別會顯示如何改變 SkiaSharp 圖形以回應使用者輸入。

在這個頁面中， `SKCanvasView` 類別會在 [TapToggleFill](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml) 中具現化，也就是 Xamarin.Forms [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer) 在視圖上設定：

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

`Tapped`物件的處理常式 `TapGestureRecognizer` 只會切換布林值欄位的值，並呼叫的 [`InvalidateSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface) 方法 `SKCanvasView` ：

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

呼叫可 `InvalidateSurface` 有效地產生處理常式的呼叫 `PaintSurface` ，此處理程式 `showFill` 會使用欄位填滿或不填滿圓形：

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

`StrokeWidth`屬性已設為50來強調差異。 您也可以藉由先繪製內部和外框來查看整行寬度。 根據預設，在事件處理常式稍後繪製的圖形圖形會 `PaintSurface` 混淆先前在處理常式中繪製的圖形。

[ **色彩探索** ] 頁面會示範如何將 SkiaSharp 圖形與其他 Xamarin.Forms 元素整合，也會示範兩個替代方法之間的差異，以在 SkiaSharp 中定義色彩。 靜態 [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte)) 方法會根據「 `SKColor` 色調-飽和度-亮度」模型建立一個值：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

靜態 [`SKColor.FromHsv`](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte)) 方法會 `SKColor` 根據類似的色調/飽和度值模型來建立值：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在這兩種情況下，自 `h` 變數的範圍是從0到360。 `s`、 `l` 和 `v` 引數的範圍是從0到100。 `a` (Alpha 或不透明度) 引數的範圍是從0到255。

[**ColorExplorePage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)檔案 `SKCanvasView` 會在並存建立兩個物件， `StackLayout` `Slider` 以及 `Label` 允許使用者選取 HSL 和 HSV 色彩值的視圖：

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

這兩個 `SKCanvasView` 元素位於單一儲存格中， `Grid` 並在 `Label` 上方顯示所產生的 RGB 色彩值。

[**ColorExplorePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)程式碼後端檔案相當簡單。 這三個元素的共用 `ValueChanged` 處理常式 `Slider` 只會使這兩個 `SKCanvasView` 元素失效。 `PaintSurface`處理常式會使用元素所指示的色彩來清除畫布 `Slider` ，也會將位於 `Label` `SKCanvasView` 元素上方的專案設定為：

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

在 HSL 和 HSV 色彩模型中，色調值的範圍是從0到360，並表示色彩的主要色調。 這些是彩虹的傳統色彩：紅色、橙色、黃色、綠色、藍色、靛藍色、紫色，以及圓形中的反紅色。

在 HSL 模型中，亮度的0值一律為黑色，而100值一律為白色。 當飽和度值為0時，介於0和100之間的亮度值為灰色陰影。 增加飽和度會增加更多色彩。 純顏色 (是一個元件等於255的 RGB 值，另一個等於0，而第三個範圍從0到 255) 會在飽和度為100且亮度為50時發生。

在 HSV 模型中，當飽和度和值都是100時，純顏色會產生。 當值是0時，不論任何其他設定為何，色彩都是黑色。 當飽和度為0且值範圍從0到100時，就會發生灰色陰影。

但是，若要讓這兩個模型感到滿意，最好的方法就是自行實驗：

[![[色彩探索] 頁面的三重螢幕擷取畫面](integration-images/colorexplore-large.png)](integration-images/colorexplore-small.png#lightbox "[色彩探索] 頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)