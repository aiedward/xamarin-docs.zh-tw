---
title: 使用 Xamarin.Forms 整合
description: 建立回應的觸控和 Xamarin.Forms 元素的 SkiaSharp 圖形
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: 67c4330d8e446a407dec7792fe5f40cdd9d23c22
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="integrating-with-xamarinforms"></a>使用 Xamarin.Forms 整合

_建立回應的觸控和 Xamarin.Forms 元素的 SkiaSharp 圖形_

Xamarin.Forms 其餘幾種方式可以整合 SkiaSharp 圖形。 您可以結合 SkiaSharp 畫布和 Xamarin.Forms 元素在相同頁面上或甚至是位置 Xamarin.Forms 元素 SkiaSharp 畫布上方：

![](integration-images/integrationexample.png "選取的色彩與滑桿")

建立互動式 SkiaSharp 圖形，在 Xamarin.Forms 中的另一種方法是透過觸控。
中的第二頁[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式有權使用**點選切換填滿**。 它繪製簡單的圓形兩種方式&mdash;填滿而填滿&mdash;切換點選。 [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)類別示範如何修改 SkiaSharp 圖形，以回應使用者輸入。

此頁面上，針對`SKCanvasView`類別具現化中[TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml)檔案，也會設定 Xamarin.Forms [ `TapGestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)檢視：

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

請注意`skia`XML 命名空間宣告。

`Tapped`處理常式`TapGestureRecognizer`物件只會切換值的布林值欄位和呼叫[ `InvalidateSurface` ](https://developer.xamarin.com/api/member/SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface()/)方法`SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

若要呼叫`InvalidateSurface`實際上會呼叫`PaintSurface`處理常式，會使用`showFill`填滿或不填滿的圓形的欄位：

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

`StrokeWidth`強調差異屬性設定為 50。 您也可以查看整個線條的寬度，第一次繪製內部和外框。 根據預設，在稍後繪製的圖形圖`PaintSurface`事件處理常式會遮住稍早在此處理常式中繪製的。

**色彩瀏覽**頁面示範您也可以與其他 Xamarin.Forms 項目中，整合 SkiaSharp 圖形的方式，同時也會示範兩種替代方法 SkiaSharp 中定義的色彩之間的差異。 靜態[ `SKColor.FromHsl` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsl/p/System.Single/System.Single/System.Single/System.Byte/)方法會建立`SKColor`明亮飽和度色調則模型為基礎的值：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

靜態[ `SKColor.FromHsv` ](https://developer.xamarin.com/api/member/SkiaSharp.SKColor.FromHsv/p/System.Single/System.Single/System.Single/System.Byte/)方法會建立`SKColor`類似的色調 / 飽和度值模型為基礎的值：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在這兩種情況下，`h`引數範圍從 0 到 360。 `s`， `l`，和`v`引數的範圍從 0 到 100。 `a` （alpha 或不透明度） 引數的範圍從 0 到 255 之間。

[ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)檔案會建立兩個`SKCanvasView`中的物件`StackLayout`與並存`Slider`和`Label`檢視可讓使用者選取 HSL 和HSV 色彩值：

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

這兩個`SKCanvasView`單一儲存格內項目為`Grid`與`Label`坐在最上層顯示結果的 RGB 色彩值。

[ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)程式碼後置檔案是相當簡單。 共用`ValueChanged`三個處理常式`Slider`項目只是使兩者`SKCanvasView`項目。 `PaintSurface`處理常式所指定的色彩與清除畫布`Slider`項目，而且也設`Label`坐最上層的`SKCanvasView`項目：

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

HSL 和 HSV 色彩模型中的色調值範圍從 0 到 360，並指出主控項的色彩色調。 這些是傳統的彩虹圖案的色彩： 紅色、 橙色、 黃色、 綠色、 藍色，indigo、 紫色和回到紅底圓形中的。

在 HSL 模型中，光度 0 的值一定是黑色，而且 100 的值一律為白色。 飽和度值為 0 時，介於 0 到 100 之間的亮度值是灰階。 增加飽和度新增更多的色彩。 飽和度為 100，而亮度是 50，就會發生純色彩 （也就是一個元件等於 255，另一個等於 0，且第三個範圍從 0 到 255 的 RGB 值）。

在 HSV 模型中，純色彩時，產生飽和度和值都是 100。 值為 0，任何其他設定，不論色彩為黑色。 飽和度 0 和值範圍從 0 到 100 時，則會發生灰階。

但是初步的兩個模型的最佳方式是嘗試使用它們自己：

[![](integration-images/colorexplore-large.png "色彩瀏覽頁面的三個螢幕擷取畫面")](integration-images/colorexplore-small.png#lightbox "色彩瀏覽頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
