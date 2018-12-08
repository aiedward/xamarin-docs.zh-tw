---
title: 與 Xamarin.Forms 整合
description: 本文說明如何建立回應觸控的 SkiaSharp 圖形和 Xamarin.Forms 的項目，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 288224F1-7AEE-4148-A88D-A70C03F83D7A
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: c4ca44488a4d10d3936e7bd8e664e3ffcb2a140a
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051726"
---
# <a name="integrating-with-xamarinforms"></a>與 Xamarin.Forms 整合

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_建立回應觸控和 Xamarin.Forms 元素的 SkiaSharp 圖形_

SkiaSharp 圖形可以整合 Xamarin.Forms 的其餘部分，以數種方式。 您可以結合 SkiaSharp 畫布和 Xamarin.Forms 元素在相同頁面上和甚至是位置 Xamarin.Forms 元素 SkiaSharp 畫布頂端：

![](integration-images/integrationexample.png "選取滑桿色彩")

在 Xamarin.Forms 中建立互動式 SkiaSharp 圖形的另一種方法是透過觸控。
中的第二頁[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式有權使用**點選切換填滿**。 它繪製簡單的圓形兩點&mdash;填滿而填滿&mdash;點選切換。 [ `TapToggleFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml.cs)類別可讓您顯示如何修改 SkiaSharp 圖形，以回應使用者輸入。

此頁面上，針對`SKCanvasView`中具現化類別[TapToggleFill.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/TapToggleFillPage.xaml)檔案，也會設定 Xamarin.Forms [ `TapGestureRecognizer` ](xref:Xamarin.Forms.TapGestureRecognizer)檢視：

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

`Tapped`處理常式`TapGestureRecognizer`物件只是切換值的布林值欄位，並呼叫[ `InvalidateSurface` ](xref:SkiaSharp.Views.Forms.SKCanvasView.InvalidateSurface)方法`SKCanvasView`:

```csharp
bool showFill = true;
...
void OnCanvasViewTapped(object sender, EventArgs args)
{
    showFill ^= true;
    (sender as SKCanvasView).InvalidateSurface();
}
```

在呼叫`InvalidateSurface`實際上會產生呼叫`PaintSurface`處理常式，它會使用`showFill`填滿或不會填滿的圓形的欄位：

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

`StrokeWidth`強調差異屬性設定為 50。 您也可以先繪製內部，然後大綱 中，以查看整行的寬度。 根據預設，圖形會判斷可在稍後繪製`PaintSurface`事件處理常式隱匿這些繪製稍早在處理常式。

**色彩探索**頁面示範您也可以與其他 Xamarin.Forms 項目中，整合 SkiaSharp 圖形的方式，同時也示範 SkiaSharp 中定義的色彩的兩個替代方法之間的差異。 靜態[ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl(System.Single,System.Single,System.Single,System.Byte))方法會建立`SKColor`色相-彩度-亮度模型為基礎的值：

```csharp
public static SKColor FromHsl (Single h, Single s, Single l, Byte a)
```

靜態[ `SKColor.FromHsv` ](xref:SkiaSharp.SKColor.FromHsv(System.Single,System.Single,System.Single,System.Byte))方法會建立`SKColor`類似的色相-彩度值模型為基礎的值：

```csharp
public static SKColor FromHsv (Single h, Single s, Single v, Byte a)
```

在這兩種情況下，`h`引數範圍從 0 到 360。 `s`， `l`，和`v`引數的範圍從 0 到 100 之間。 `a` （alpha 版本或不透明度） 引數的範圍從 0 到 255 之間。

[ **ColorExplorePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml)檔案會建立兩個`SKCanvasView`中的物件`StackLayout`與並存`Slider`和`Label`允許使用者選取 HSL 和 HSV 的檢視色彩值：

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

這兩個`SKCanvasView`項目是在單一資料格中`Grid`使用`Label`坐在最上層顯示結果的 RGB 色彩值。

[ **ColorExplorePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ColorExplorePage.xaml.cs)程式碼後置檔案是相當簡單。 共用`ValueChanged`三個處理常式`Slider`項目只要失效兩者`SKCanvasView`項目。 `PaintSurface`處理常式所指定的色彩與清除畫布`Slider`項目，並且也設定了`Label`坐在最上層的`SKCanvasView`項目：

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

HSL 和 HSV 色彩模型中的色調值範圍從 0 到 360，並指出主控項的色調的色彩。 這些是傳統的 rainbow 色彩： 紅色、 橙色、 黃色、 綠色、 藍色、 indigo、 紫色及回到紅色圓圈。

在 HSL 模型中，亮度的 0 值一律為黑色，，和 100 的值一律是白色。 飽和度值為 0，0 到 100 之間的亮度值時階灰階。 增加飽和度新增更多的色彩。 飽和度為 100，而亮度是 50，就會發生純色彩 （即等於 255，另一個等於 0，而第三個從 0 到 255 範圍內的某個元件的 RGB 值）。

在 HSV 模型中，純虛擬的色彩時，產生飽和度] 與 [值為 100。 如果值為 0，任何其他設定，不論色彩為黑色。 飽和度 0 和值的範圍從 0 到 100 時，就會發生灰階。

但是，概略了兩個模型的最佳方式是自行實驗用它們：

[![](integration-images/colorexplore-large.png "色彩 [探索] 頁面的三個螢幕擷取畫面")](integration-images/colorexplore-small.png#lightbox "色彩探索 頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
