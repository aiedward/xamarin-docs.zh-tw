---
title: 不可分離的 blend 模式
description: 使用不可分離的 blend 模式來改變色調、飽和度或亮度。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97FA2730-87C0-4914-8C9F-C64A02CF9EEF
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a77ebb07a09c1bbd2df482c81040f271cdf8f56e
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556343"
---
# <a name="the-non-separable-blend-modes"></a>不可分離的 blend 模式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您在 SkiaSharp 可分隔 [**blend 模式**](separable.md)的文章中所見，可分離的 blend 模式會分別在紅色、綠色和藍色通道上執行作業。 不可分離的 blend 模式則不會。 藉由在色彩的色調、飽和度和亮度層級上操作，不可分離的 blend 模式可以有有趣的方式改變色彩：

![不可分離的範例](non-separable-images/NonSeparableSample.png "不可分離的範例")

## <a name="the-hue-saturation-luminosity-model"></a>色調-飽和度-亮度模型

若要瞭解不可分離的 blend 模式，必須將目的地和來源的圖元視為色相（飽和度）亮度模型中的色彩。  (亮度也稱為亮度。 ) 

HSL 色彩模型已在[**與 Xamarin.Forms **](../../basics/integration.md)該文章中的「整合」和「範例」程式中討論，並可讓您使用 hsl 色彩進行實驗。 您可以 `SKColor` 使用具有靜態方法的色調、飽和度和亮度值來建立值 [`SKColor.FromHsl`](xref:SkiaSharp.SKColor.FromHsl*) 。

色調代表色彩的主要波長。 色調值的範圍是從0到360，並迴圈顯示加法和 subtractive 主要複本：紅色的值為0、黃色為60、綠色為120、青色為180、藍色為240、洋紅色為300，且迴圈回到紅色（360）。

如果沒有任何主要色彩 &mdash; （例如，色彩是白色或黑色）或灰色陰影，則 &mdash; 色調會未定義，且通常設定為0。 

飽和度值的範圍可以從0到100，並指出色彩的純度。 飽和度值100是單純色彩，而小於100的值則會使色彩變得更 grayish。 飽和度值為0會產生灰色陰影。

亮度 (或亮度) 值表示色彩的亮度。 無論其他設定為何，亮度值為0都會是黑色。 同樣地，亮度值100是白色。 

HSL 值 (0，100，50) 是 RGB 值 (FF、00、00) （純紅色）。 HSL 值 (180、100、50) 是 RGB 值 (00、FF、FF) 、純青色。 當飽和度減少時，主要色彩元件會減少，而其他元件則會增加。 在飽和度層級0，所有元件都是相同的，而色彩則是灰色的陰影。 降低亮度以移至黑色;提高亮度以進入白色。

## <a name="the-blend-modes-in-detail"></a>詳細的 blend 模式

就像其他 blend 模式一樣，四個非可分隔的 blend 模式牽涉到目的地 (這通常是點陣圖影像) 和來源（通常是單一色彩或漸層）。 Blend 模式結合了目的地和來源的色調、飽和度和亮度值：

| Blend 模式   | 來源中的元件 | 目的地的元件 |
| ------------ | ---------------------- | --------------------------- |
| `Hue`        | 色調                    | 飽和度和亮度   |
| `Saturation` | 飽和度             | 色調和亮度          |
| `Color`      | 色調和飽和度     | 光度                  | 
| `Luminosity` | 光度             | 色調和飽和度          | 

請參閱適用于演算法的 W3C 撰寫 [**和混合層級 1**](https://www.w3.org/TR/compositing-1/) 規格。

[ **不可分隔的混合模式** ] 頁面包含 `Picker` ，可選取其中一個 Blend 模式和三個 `Slider` 視圖來選取 HSL 色彩：

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

為了節省空間，程式的 `Slider` 使用者介面中不會識別這三個視圖。 您必須記住，順序是色調、飽和度和亮度。 頁面底部有兩個 `Label` 視圖顯示 HSL 和 RGB 色彩值。

程式碼後端檔案會載入其中一個點陣圖資源，在畫布上顯示最大的大小，然後以矩形涵蓋畫布。 矩形色彩是以三個視圖為基礎 `Slider` ，而 blend 模式則是在中選取的 `Picker` ：

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

請注意，此程式不會顯示三個滑杆所選取的 HSL 色彩值。 相反地，它會從這些滑杆建立色彩值，然後使用 [`ToHsl`](xref:SkiaSharp.SKColor.ToHsl*) 方法來取得色調、飽和度和亮度的值。 這是因為 `FromHsl` 方法會將 HSL 色彩轉換成 RGB 色彩，這會儲存在 `SKColor` 結構內部。 `ToHsl`方法會從 RGB 轉換成 HSL，但結果不一定是原始值。 

例如， `FromHsl` 將 HSL 值 (180、50、0) 轉換為 RGB 色彩 (0、0、0) ，因為 `Luminosity` 是零。 `ToHsl`方法會將 RGB 色彩 (0、0、0) 轉換成 HSL 色 (0、0、0) ，因為色調和飽和度值是不相關的。 使用這個程式時，您可以更清楚地看到程式所使用之 HSL 色彩的標記法，而不是您使用滑杆指定的色彩。

`SKBlendModes.Hue`Blend 模式會使用來源的色調層級，同時保留目的地的飽和度和亮度等級。 當您測試這個 blend 模式時，飽和度和亮度滑杆必須設定為0或100以外的地方，因為在這些情況下，不會唯一定義色調。

[![不可分離的 Blend 模式-色調](non-separable-images/NonSeparableBlendModes-Hue.png "不可分離的 Blend 模式-色調")](non-separable-images/NonSeparableBlendModes-Hue-Large.png#lightbox)

當您使用 [將滑杆設定為 0 (與左側) 的 iOS 螢幕擷取畫面一樣，所有專案都會變成 reddish。 但這並不表示影像完全不會出現綠色和藍色。 顯然結果中仍有灰色陰影存在。 例如，RGB 色彩 (40、40、C0) 相當於 HSL 色 (240、50、50) 。 色調是藍色，但飽和度值50表示也有紅色和綠色的元件。 如果色調設定為0（含 `SKBlendModes.Hue` ），則 HSL 色彩會是 (0、50、50) ，也就是 RGB 色彩 (c0、40、40) 。 仍有藍色和綠色的元件，但現在主要元件為紅色。

`SKBlendModes.Saturation`Blend 模式會將來源的飽和度層級與目的地的色調和亮度結合。 和色調一樣，如果亮度為0或100，則不會妥善定義飽和度。 理論上，這兩個極端值之間的任何亮度設定都應該可以運作。 不過，亮度設定似乎會影響結果。 將亮度設定為50，您就可以看到如何設定圖片的飽和度層級：

[![不可分離的 Blend 模式-飽和度](non-separable-images/NonSeparableBlendModes-Saturation.png "不可分離的 Blend 模式-飽和度")](non-separable-images/NonSeparableBlendModes-Saturation-Large.png#lightbox)

您可以使用這個 blend 模式來增加單調影像的色彩飽和度，也可以將飽和度減少到零 (，就像在左側的 iOS 螢幕擷取畫面中，) 針對只包含灰色陰影的結果影像。

`SKBlendModes.Color`Blend 模式會保留目的地的亮度，但會使用來源的色調和飽和度。 同樣地，這表示極端值之間的任何亮度滑杆設定都應該可以運作。 

[![不可分離的 Blend 模式-色彩](non-separable-images/NonSeparableBlendModes-Color.png "不可分離的 Blend 模式-色彩")](non-separable-images/NonSeparableBlendModes-Color-Large.png#lightbox)

您很快就會看到這個 blend 模式的應用程式。

最後， `SKBlendModes.Luminosity` blend 模式相對於 `SKBlendModes.Color` 。 它會保留目的地的色調和飽和度，但會使用來源的亮度。 `Luminosity`Blend 模式是最具神秘的批次：色調和飽和度滑杆會影響影像，但即使是在中度亮度，影像也不會相異：

[![不可分離的 Blend 模式-亮度](non-separable-images/NonSeparableBlendModes-Luminosity.png "不可分離的 Blend 模式-亮度")](non-separable-images/NonSeparableBlendModes-Luminosity-Large.png#lightbox)

理論上，增加或減少影像的亮度應該使其變得更淺或更暗。 有趣的是， [Skia **SkBlendMode 參考**中的亮度範例](https://skia.org/user/api/SkBlendMode_Reference#Luminosity)相當類似。

一般來說，您不會想要使用其中一個不可分離的 blend 模式，其來源是由套用至整個目的地影像的單一色彩所組成。 效果就太大了。 您會想要將效果限制為影像的一個部分。 在這種情況下，來源可能會併入 transparancy，或許來源會限制為較小的圖形。

## <a name="a-matte-for-a-separable-mode"></a>可分離模式的遮罩

以下是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 範例中包含為資源的其中一個點陣圖。 檔案名為 **Banana.jpg**：

![Banana 的猴子](non-separable-images/Banana.jpg "Banana 的猴子")

您可以建立只包含 banana 的遮罩。 這也是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 範例中的資源。 檔案名為 **BananaMatte.png**：

![Banana 亞光](non-separable-images/BananaMatte.png "Banana 亞光")

除了黑色 banana 圖形之外，點陣圖的其餘部分也是透明的。

**藍色 Banana**頁面使用該遮罩來改變猴子所持有之 Banana 的色調和飽和度，但變更影像中的其他內容。 

在下列 `BlueBananaPage` 類別中，會將 **Banana.jpg** 點陣圖載入為欄位。 此函式會載入 **BananaMatte.png** 點陣圖作為 `matteBitmap` 物件，但不會將該物件保留在函式之外。 相反地，會建立名為的第三個位圖 `blueBananaBitmap` 。 `matteBitmap`會在之後繪製， `blueBananaBitmap` `SKPaint` `Color` 並將其設為藍色，並 `BlendMode` 將其設定為 `SKBlendMode.SrcIn` 。 的 `blueBananaBitmap` 內容大多保持透明，但具有純純藍色的 banana 影像：

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

`PaintSurface`處理常式會以包含 banana 的猴子來繪製點陣圖。 這段程式碼後面接著顯示 `blueBananaBitmap` with `SKBlendMode.Color` 。 在 banana 的介面上，每個圖元的色調和飽和度都會以藍色（對應于色調值240和飽和度值100）取代。 但亮度仍維持不變，這表示即使 banana 的新色彩，也會繼續擁有實際的材質：

[![藍色 Banana](non-separable-images/BlueBanana.png "藍色 Banana")](non-separable-images/BlueBanana-Large.png#lightbox)

請嘗試將 blend 模式變更為 `SKBlendMode.Saturation` 。 Banana 維持黃色，但卻是更密集的黃色。 在實際的應用程式中，這可能是比將 banana 藍色更理想的效果。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)