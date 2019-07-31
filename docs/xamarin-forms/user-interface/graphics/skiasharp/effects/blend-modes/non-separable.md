---
title: 非可加以分隔的混合模式
description: 使用非分隔混合模式來變更色調、 飽和度或亮度。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9054539b08da89c0f7d8a93150866fb1b41e63f1
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642787"
---
# <a name="the-non-separable-blend-modes"></a>非可加以分隔的混合模式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所見文中所[ **SkiaSharp 分隔混合模式**](separable.md)，可加以分隔的 blend 模式分開執行的紅色、 綠色和藍色的通道上的作業。 非可加以分隔的 blend 模式則否。 操作在色彩的色調、 飽和度和亮度層級時，非可加以分隔的 blend 模式就可以將色彩改變以有趣的方式：

![非可加以分隔的範例](non-separable-images/NonSeparableSample.png "非可加以分隔的範例")

## <a name="the-hue-saturation-luminosity-model"></a>色相-彩度-亮度模型

若要了解非可加以分隔的混合模式，就必須將目的地和來源的像素視為色相-彩度-亮度模型中的色彩。 （明暗度也稱為亮度。）

一文中討論的 HSL 色彩模型[**整合 Xamarin.Forms** ](../../basics/integration.md) ，並在該文章中的範例程式允許實驗 HSL 色彩。 您可以建立`SKColor`值使用色調、 飽和度和亮度值搭配靜態[ `SKColor.FromHsl` ](xref:SkiaSharp.SKColor.FromHsl*)方法。

色調表示主控項的 wavelength 的色彩。 色調值的範圍是從0到 360, 並會迴圈執行加法和 subtractive 主要複本:紅色為 0, 黃色為 60, 綠色為 120, 青色為 180, 藍色為 240, 洋紅色為 300, 而週期在360回到紅色。

如果沒有任何主要色彩&mdash;例如，色彩是白色或黑色或灰色陰影&mdash;色調會未定義，且通常設定為 0。 

飽和度的值可以範圍從 0 到 100，並指出色彩的純度。 100 的飽和度值是血統純正的色彩，而值低於 100 會導致變成更灰的色彩。 飽和度值為 0 會導致淺灰色。

亮度 （或亮度） 值會指出如何亮的色彩就是。 亮度值為 0 是黑色，不論其他設定。 同樣地，100 的亮度值為白色。 

HSL 值 （0、 100、 50） 是 RGB 值 (FF、 00、 00)，也就是純紅色。 RGB 值 (從 00，FF，FF)，純青色 （180、 100、 50） 的 HSL 值。 飽和度就會減少，就會減少主要色彩元件和其他元件會增加。 在飽和度層級為 0，所有的元件相同，而且色彩為灰階。 減少黑色; 要前往的明暗度增加前往空白的明暗度。

## <a name="the-blend-modes-in-detail"></a>混合模式，在 詳細資料

像其他混合模式 （這通常是點陣圖影像） 的目的地和來源，通常是單色或漸層，牽涉到四個非可加以分隔的混合模式。 混合模式結合目的地和來源的色調、 飽和度和亮度值：

| 混合模式   | 從來源元件 | 從目的地元件 |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | 色調                    | 飽和度和亮度   |
| `Saturation` | 飽和度             | 色調] 和 [亮度          |
| `Color`      | 色調及彩     | 亮度                  | 
| `Luminosity` | 亮度             | 色調及彩          | 

請參閱 W3C [**複合 （compositing） 和混合層級 1** ](https://www.w3.org/TR/compositing-1/)演算法的規格。

**非分隔的混合模式**頁面包含`Picker`以選取其中一種混合模式和三個`Slider`檢視，以選取 HSL 色彩：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.NonSeparableBlendModesPage"
             Title="Non-Separable Blend Modes">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10, 0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Hue" />
                    <x:Static Member="skia:SKBlendMode.Saturation" />
                    <x:Static Member="skia:SKBlendMode.Color" />
                    <x:Static Member="skia:SKBlendMode.Luminosity" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="hueSlider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="satSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="lumSlider"
                Maximum="100"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <StackLayout Orientation="Horizontal">
            <Label x:Name="hslLabel"
                   HorizontalOptions="CenterAndExpand" />

            <Label x:Name="rgbLabel"
                   HorizontalOptions="CenterAndExpand" />

        </StackLayout>
    </StackLayout>
</ContentPage>
```

為了節省空間，其中三個`Slider`檢視不會在使用者介面的程式識別。 您必須記住的順序是色調、 飽和度和亮度。 兩個`Label`在頁面底部的檢視會顯示 HSL 和 RGB 色彩值。

程式碼後置檔案會載入其中一個點陣圖資源時，會顯示，盡可能在畫布上，並接著說明如何包含矩形的畫布。 矩形的色彩根據三`Slider`檢視和混合模式是在中選取一個`Picker`:

```csharp
public partial class NonSeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(NonSeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKColor color;

    public NonSeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        // Calculate new color based on sliders
        color = SKColor.FromHsl((float)hueSlider.Value,
                                (float)satSlider.Value,
                                (float)lumSlider.Value);

        // Use labels to display HSL and RGB color values
        color.ToHsl(out float hue, out float sat, out float lum);

        hslLabel.Text = String.Format("HSL = {0:F0} {1:F0} {2:F0}",
                                      hue, sat, lum);

        rgbLabel.Text = String.Format("RGB = {0:X2} {1:X2} {2:X2}",
                                      color.Red, color.Green, color.Blue);

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        // Get blend mode from Picker
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

請注意程式不會為已選取三個滑桿所顯示的 HSL 色彩值。 相反地，建立這些滑桿色彩值，並接著會使用[ `ToHsl` ](xref:SkiaSharp.SKColor.ToHsl*)方法，以取得色調、 飽和度和亮度值。 這是因為`FromHsl`方法會將 HSL 色彩轉換為 RGB 色彩，它會在內部儲存`SKColor`結構。 `ToHsl`方法會將轉換 rgb HSL，但是結果不一定會將原始值。 

例如， `FromHsl` HSL 值 （180，50，0） 將 RGB 色彩 （0，0，0），因為`Luminosity`為零。 `ToHsl`方法會轉換成 （0，0，0） 的 HSL 色彩的 RGB 色彩 （0，0，0） 因為色調及彩度值無關。 當使用此程式，最好是，您會看到程式正在使用而不您指定使用滑桿的 HSL 色彩的表示法。

`SKBlendModes.Hue`混合模式會使用 Hue 層級之來源的同時保留目的地的飽和度和亮度層級。 當您測試此混合模式時，飽和度和亮度滑桿必須設定為 0 或 100 以外因為在這些情況下，Hue 不唯一定義。

[![非可加以分隔的混合模式-色調](non-separable-images/NonSeparableBlendModes-Hue.png "非可加以分隔的混合模式-Hue")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

當您使用 （如同 iOS 螢幕擷取畫面左邊） 將滑桿設為 0，所有項目會變成紅。 但這不表示該影像是完全不存在的綠色和藍色。 很顯然有仍然灰階出現在結果中。 比方說，RGB 色彩 （40，40，C0） 就相當於 HSL 色彩 （240、 50，50）。 色調是藍色，但為 50 的彩度值會指出有紅色和綠色元件時，也。 如果色調設為 0 的`SKBlendModes.Hue`，HSL 色彩是 （0、 50，50），這是 RGB 色彩 (C0，40，40)。 還是藍色與綠色元件，但現在主要元件是紅色。

`SKBlendModes.Saturation`混合模式會結合飽和度的層級來源的 Hue 和目的地的亮度。 像是色調、 飽和度不是妥善定義如果亮度是 0 或 100。 在理論上，應可使用任何這些兩個極端之間的亮度設定。 不過，亮度設定似乎會影響結果超過預期。 設定為 50，亮度，您可以看到您可以設定飽和度的層級圖片的方式：

[![非可加以分隔的混合模式-飽和度](non-separable-images/NonSeparableBlendModes-Saturation.png "非可加以分隔的混合模式-飽和度")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

您可以使用此混合模式增加的色彩飽和度的鈍的映像，或您可以減少至零 （例如 iOS 螢幕擷取畫面左邊） 飽和度僅灰階所組成的結果映像。

`SKBlendModes.Color`混合模式會保留目的地的亮度，但使用色調及彩的來源。 同樣地，這表示應該使用 亮度滑桿極端之間的任何設定。 

[![非可加以分隔的混合模式-色彩](non-separable-images/NonSeparableBlendModes-Color.png "非可加以分隔的混合模式-色彩")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

您很快會看到此混合模式應用程式。

最後， `SKBlendModes.Luminosity` blend 模式是相對於`SKBlendModes.Color`。 它會保留色調及彩的目的地，但使用的來源的明暗度。 `Luminosity` Blend 模式是批次中最神秘的一種:色調和飽和度滑杆會影響影像, 但即使是中度亮度, 影像也不會相異:

[![非可加以分隔的混合模式-亮度](non-separable-images/NonSeparableBlendModes-Luminosity.png "非可加以分隔的混合模式-亮度")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

理論上，增加或減少的影像亮度變得較淺或較深。 有趣的是，[亮度範例 Skia **SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference#Luminosity)相當類似。

它通常不是您會想要使用其中一種非可加以分隔的混合模式與組成單一色彩套用至整個的目的端影像的來源。 效果就只是太大。 您會想要限制效果映像的一部分。 在此情況下，來源可能會納入透明度，或可能是來源會受限於較小的圖形。

## <a name="a-matte-for-a-separable-mode"></a>分隔模式草蓆

以下是其中一個包含做為資源中的點陣圖[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例。 檔名**Banana.jpg**:

![Banana Monkey](non-separable-images/Banana.jpg "Banana Monkey")

可以建立包含只 banana 草蓆。 這也是中的資源[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例。 檔名**BananaMatte.png**:

![Banana 草蓆](non-separable-images/BananaMatte.png "Banana 草蓆")

除了黑色 banana 圖形、 點陣圖的其餘部分都是透明的。

**藍色 Banana**頁面會使用該草蓆變更色調及彩持有 monkey，香蕉的但變更映像中的其他任何項目。 

在下列`BlueBananaPage`類別， **Banana.jpg**載入點陣圖做為欄位。 建構函式載入**BananaMatte.png**點陣圖為`matteBitmap`物件，但它不會保留該建構函式之外的物件。 相反地，第三個點陣圖的名為`blueBananaBitmap`建立。 `matteBitmap`上繪製`blueBananaBitmap`後面`SKPaint`具有其`Color`設定為藍色並將其`BlendMode`設為`SKBlendMode.SrcIn`。 `blueBananaBitmap`大致保持透明的但使用的純色純藍色映像 banana:

```csharp
public class BlueBananaPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BlueBananaPage),
        "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap blueBananaBitmap;

    public BlueBananaPage()
    {
        Title = "Blue Banana";

        // Load banana matte bitmap (black on transparent)
        SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
            typeof(BlueBananaPage),
            "SkiaSharpFormsDemos.Media.BananaMatte.png");

        // Create a bitmap with a solid blue banana and transparent otherwise
        blueBananaBitmap = new SKBitmap(matteBitmap.Width, matteBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(blueBananaBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(matteBitmap, new SKPoint(0, 0));

            using (SKPaint paint = new SKPaint())
            {
                paint.Color = SKColors.Blue;
                paint.BlendMode = SKBlendMode.SrcIn;
                canvas.DrawPaint(paint);
            }
        }

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

        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);

        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = SKBlendMode.Color;
            canvas.DrawBitmap(blueBananaBitmap, 
                              info.Rect, 
                              BitmapStretch.Uniform, 
                              paint: paint);
        }
    }
}
```

`PaintSurface`處理常式與保留 banana monkey 繪製點陣圖。 此程式碼後面的顯示`blueBananaBitmap`與`SKBlendMode.Color`。 透過香蕉的介面，每個像素色調及彩度會由藍色實線，這會對應至 240 的色調值和 100 的飽和度值取代。 亮度，不過，維持不變，這表示 banana，繼續讓實際的紋理，儘管其新的色彩：

[![藍色 Banana](non-separable-images/BlueBanana.png "藍色 Banana")](non-separable-images/BlueBanana-Large.png#lightbox)

請嘗試變更至混合模式`SKBlendMode.Saturation`。 黃色香蕉，維持但愈深黃色。 在實際應用程式中，這可能是更好的效果比開啟 banana 藍色。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
