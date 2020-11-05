---
title: 分離的 blend 模式
description: 使用可分離的 blend 模式來改變紅色、綠色和藍色色彩。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3480c2fe2ef94a2a1beee9a924a59cd90d3a42b3
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370802"
---
# <a name="the-separable-blend-modes"></a>分離的 blend 模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您在 [**SkiaSharp Porter-Duff blend 模式**](porter-duff.md)的文章中所見，Porter-Duff blend 模式通常會執行裁剪作業。 可分離的 blend 模式不同。 可分離模式會改變影像的個別紅色、綠色和藍色色彩元件。 可分離的 blend 模式可以混合色彩，以示範紅色、綠色和藍色的組合的確是白色：

![原色](separable-images/SeparableSample.png "原色")

## <a name="lighten-and-darken-two-ways"></a>以兩種方式淡化和變暗 

通常會有稍微太暗或太淺的點陣圖。 您可以使用可分離的 blend 模式，將 imabe 淡化或變暗。  的確，列舉中的兩個可分隔 blend 模式 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) 名為 `Lighten` 和 `Darken` 。 

這兩種模式會在 [ **淡化] 和 [變暗** ] 頁面中示範。 XAML 檔案會具現化兩個 `SKCanvasView` 物件和兩個 `Slider` 視圖：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.LightenAndDarkenPage"
             Title="Lighten and Darken">
    <StackLayout>
        <skia:SKCanvasView x:Name="lightenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="lightenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />

        <skia:SKCanvasView x:Name="darkenCanvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="darkenSlider"
                Margin="10"
                ValueChanged="OnSliderValueChanged" />
    </StackLayout>
</ContentPage>
```

第一個 `SKCanvasView` 和 `Slider` 示範 `SKBlendMode.Lighten` ，以及第二個配對示範 `SKBlendMode.Darken` 。 這兩個 `Slider` 視圖會共用相同的 `ValueChanged` 處理常式，而這兩個會 `SKCanvasView` 共用相同的 `PaintSurface` 處理常式。 這兩個事件處理常式都會檢查哪個物件正在引發事件：

```csharp
public partial class LightenAndDarkenPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                typeof(SeparableBlendModesPage),
                "SkiaSharpFormsDemos.Media.Banana.jpg");

    public LightenAndDarkenPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if ((Slider)sender == lightenSlider)
        {
            lightenCanvasView.InvalidateSurface();
        }
        else
        {
            darkenCanvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest size rectangle in canvas
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);
        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);
        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap
        canvas.DrawBitmap(bitmap, rect);

        // Display gray rectangle with blend mode
        using (SKPaint paint = new SKPaint())
        {
            if ((SKCanvasView)sender == lightenCanvasView)
            {
                byte value = (byte)(255 * lightenSlider.Value);
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Lighten;
            }
            else
            {
                byte value = (byte)(255 * (1 - darkenSlider.Value));
                paint.Color = new SKColor(value, value, value);
                paint.BlendMode = SKBlendMode.Darken;
            }

            canvas.DrawRect(rect, paint);
        }
    }
}
```

`PaintSurface`處理常式會計算適合點陣圖的矩形。 處理常式會顯示該點陣圖，然後使用 `SKPaint` 物件，並 `BlendMode` 將其屬性設定為或，以在點陣圖上顯示矩形 `SKBlendMode.Lighten` `SKBlendMode.Darken` 。 `Color`屬性是以灰色陰影為基礎 `Slider` 。 在此 `Lighten` 模式中，色彩的範圍是從黑色到白色，但在此 `Darken` 模式中，其範圍從白色到黑色。

從左至右的螢幕擷取畫面會顯示越來越大的 `Slider` 值，因為最上層影像會變得較小，而底部影像會變暗：

[![淡化和變暗](separable-images/LightenAndDarken.png "淡化和變暗")](separable-images/LightenAndDarken-Large.png#lightbox)

此程式示範使用可分隔 blend 模式的一般方式：目的地是某種影像，通常是點陣圖。 來源是使用 `SKPaint` 物件（其 `BlendMode` 屬性設定為可分隔 blend 模式）所顯示的矩形。 矩形可以是純色 (因為它在此) 或漸層。 透明效果通常 _不_ 會與可分隔的 blend 模式搭配使用。

當您試驗這個程式時，您會發現這兩種混合模式不會將影像淡化並均勻地變暗。 相反地， `Slider` 似乎會設定某種類型的臨界值。 例如，當您增加 `Slider` `Lighten` 模式時，影像的較深區域會先亮，而較亮的區域則保持不變。

在此 `Lighten` 模式中，如果目的地圖元是 RGB 色彩值 (Dr、Dg、Db) 和來源圖元為 (Sr、Sg、Sb) 的色彩，則輸出為 (或 Og，Ob) 計算方式如下：

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

針對紅色、綠色和藍色，結果會是目的地和來源的較大。 這會產生將目的地的暗灰色區域先亮的效果。

`Darken`模式類似，不同之處在于結果是目的地和來源的較小者：

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

紅色、綠色和藍色的元件都是分開處理的，這就是為什麼這些 blend 模式稱為可 _分隔 blend 模式_ 的原因。 基於這個理由，縮寫 **Dc** 和 **Sc** 可用於目的地和來源色彩，並瞭解每個紅色、綠色和藍色元件的計算分別適用于每個紅色、綠色和藍色元件。

下表顯示所有可分隔的 blend 模式，以及其用途的簡短說明。 第二個數據行顯示不產生任何變更的來源色彩：

| Blend 模式   | 沒有變更 | 作業 |
| ------------ | --------- | --------- |
| `Plus`       | 黑色     | 藉由新增色彩來明暗： Sc + Dc |
| `Modulate`   | 白色     | 使用乘法色來變暗： Sc ·直流 | 
| `Screen`     | 黑色     | 補充產品的補充： Sc + Dc &ndash; Sc ·直流 |
| `Overlay`    | 灰色      | 反向 `HardLight` |
| `Darken`     | 白色     | 色彩的最小值： min (Sc，Dc)  |
| `Lighten`    | 黑色     | 最大色彩：最大 (Sc、Dc)  |
| `ColorDodge` | 黑色     | 根據來源的變亮目的地 |
| `ColorBurn`  | 白色     | 根據來源將目的地變暗 | 
| `HardLight`  | 灰色      | 類似于粗糙焦點的效果 |
| `SoftLight`  | 灰色      | 類似于軟焦點的效果 | 
| `Difference` | 黑色     | 從較淺的： Abs (Dc Sc 減去較深的： &ndash;)  | 
| `Exclusion`  | 黑色     | 類似于 `Difference` 但較低對比度 |
| `Multiply`   | 白色     | 使用乘法色來變暗： Sc ·直流 |

您可以在 W3C [**複合和混合層級 1**](https://www.w3.org/TR/compositing-1/) 規格和 Skia [**SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)中找到更詳細的演算法，雖然這兩個來源中的標記法不同。 請記住， `Plus` 通常會被視為 Porter-Duff blend 模式，而 `Modulate` 不是 W3C 規格的一部分。

如果來源是透明的，則除了以外的所有可分隔 blend 模式之外， `Modulate` blend 模式沒有任何作用。 如您先前所見， `Modulate` blend 模式會將 Alpha 色板併入乘法中。 否則，會 `Modulate` 有相同的效果 `Multiply` 。 

請注意兩個名為和的模式 `ColorDodge` `ColorBurn` 。 在攝影暗房實務中，字組會 _減_ 到和 _燒錄_ 。 放大鏡會透過以光燈的方式來進行攝影列印。 如果沒有輕量，列印就是白色。 列印較長一段時間時，列印會變暗。 列印製作者通常會使用手或小型物件來封鎖某些光線落在列印的特定部分，使該區域變得更淺。 這就是所謂的 _躲過_ 。 相反地，在其中具有洞的不透明材質 (或手上封鎖大部分的燈光) 可用來將更多光線導向特定的位置，以使其變得更暗，稱為「 _燒錄_ 」。

**減減和燒錄** 程式非常類似于 **淡化和變暗** 。 XAML 檔案的結構相同，但具有不同的元素名稱，而程式碼後端檔案也相當類似，但是這兩種混合模式的效果相當不同：

[![減減和燒錄](separable-images/DodgeAndBurn.png "減減和燒錄")](separable-images/DodgeAndBurn-Large.png#lightbox)

針對較小的 `Slider` 值，此 `Lighten` 模式會先使深色區域變暗，同時 `ColorDodge` 更一致。

影像處理應用程式通常可讓躲過和燒錄限制在特定區域，就像在暗房中一樣。 這可以透過漸層或具有不同灰色陰影的點陣圖來完成。

## <a name="exploring-the-separable-blend-modes"></a>探索分離的 blend 模式

[可 **分離的 Blend 模式** ] 頁面可讓您檢查所有可分隔的 blend 模式。 它會使用其中一種 blend 模式來顯示點陣圖目的地和彩色矩形來源。 

XAML 檔案會定義 `Picker` (來選取 blend 模式) 和四個滑杆。 前三個滑杆可讓您設定來源的紅色、綠色和藍色元件。 第四個滑杆的目的是要藉由設定灰色陰影來覆寫這些值。 未識別個別的滑杆，但色彩表示其功能：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.SeparableBlendModesPage"
             Title="Separable Blend Modes">

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
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                    <x:Static Member="skia:SKBlendMode.Screen" />
                    <x:Static Member="skia:SKBlendMode.Overlay" />
                    <x:Static Member="skia:SKBlendMode.Darken" />
                    <x:Static Member="skia:SKBlendMode.Lighten" />
                    <x:Static Member="skia:SKBlendMode.ColorDodge" />
                    <x:Static Member="skia:SKBlendMode.ColorBurn" />
                    <x:Static Member="skia:SKBlendMode.HardLight" />
                    <x:Static Member="skia:SKBlendMode.SoftLight" />
                    <x:Static Member="skia:SKBlendMode.Difference" />
                    <x:Static Member="skia:SKBlendMode.Exclusion" />
                    <x:Static Member="skia:SKBlendMode.Multiply" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Slider x:Name="redSlider"
                MinimumTrackColor="Red"
                MaximumTrackColor="Red"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="greenSlider"
                MinimumTrackColor="Green"
                MaximumTrackColor="Green"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="blueSlider"
                MinimumTrackColor="Blue"
                MaximumTrackColor="Blue"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Slider x:Name="graySlider"
                MinimumTrackColor="Gray"
                MaximumTrackColor="Gray"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label x:Name="colorLabel"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

程式碼後端檔案會載入其中一個點陣圖資源，並繪製兩次，一次在畫布的上半部，然後再次出現在畫布的下半部：

```csharp
public partial class SeparableBlendModesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(SeparableBlendModesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg"); 

    public SeparableBlendModesPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        if (sender == graySlider)
        {
            redSlider.Value = greenSlider.Value = blueSlider.Value = graySlider.Value;
        }

        colorLabel.Text = String.Format("Color = {0:X2} {1:X2} {2:X2}",
                                        (byte)(255 * redSlider.Value),
                                        (byte)(255 * greenSlider.Value),
                                        (byte)(255 * blueSlider.Value));

        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Draw bitmap in top half
        SKRect rect = new SKRect(0, 0, info.Width, info.Height / 2);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Draw bitmap in bottom halr
        rect = new SKRect(0, info.Height / 2, info.Width, info.Height);
        canvas.DrawBitmap(bitmap, rect, BitmapStretch.Uniform);

        // Get values from XAML controls
        SKBlendMode blendMode =
            (SKBlendMode)(blendModePicker.SelectedIndex == -1 ?
                                        0 : blendModePicker.SelectedItem);

        SKColor color = new SKColor((byte)(255 * redSlider.Value),
                                    (byte)(255 * greenSlider.Value),
                                    (byte)(255 * blueSlider.Value));

        // Draw rectangle with blend mode in bottom half
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = color;
            paint.BlendMode = blendMode;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

在 `PaintSurface` 處理常式底部，會使用選取的 blend 模式和選取的色彩，在第二個位圖上繪製一個矩形。 您可以比較底部的已修改點陣圖和頂端的原始點陣圖：

[![可分隔的混合模式](separable-images/SeparableBlendModes.png "可分隔的混合模式")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>加法和 subtractive 的主要色彩

[ **主要色彩** ] 頁面會以紅色、綠色和藍色繪製三個重迭的圓形：

[![加法主要色彩](separable-images/PrimaryColors-Additive.png "加法主要色彩")](separable-images/PrimaryColors-Additive.png#lightbox)

這些是附加的主要色彩。 任兩個的組合會產生青色、洋紅和黃色，且全部三個的組合都是白色。

這三個圓形是以模式繪製的 `SKBlendMode.Plus` ，但您也可以使用 `Screen` 、 `Lighten` 或 `Difference` 來取得相同效果。 程式如下：

```csharp
public class PrimaryColorsPage : ContentPage
{
    bool isSubtractive;

    public PrimaryColorsPage ()
    {
        Title = "Primary Colors";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Switch between additive and subtractive primaries at tap
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isSubtractive ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        float radius = Math.Min(info.Width, info.Height) / 4;
        float distance = 0.8f * radius;     // from canvas center to circle center
        SKPoint center1 = center + 
            new SKPoint(distance * (float)Math.Cos(9 * Math.PI / 6),
                        distance * (float)Math.Sin(9 * Math.PI / 6));
        SKPoint center2 = center +
            new SKPoint(distance * (float)Math.Cos(1 * Math.PI / 6),
                        distance * (float)Math.Sin(1 * Math.PI / 6));
        SKPoint center3 = center +
            new SKPoint(distance * (float)Math.Cos(5 * Math.PI / 6),
                        distance * (float)Math.Sin(5 * Math.PI / 6));

        using (SKPaint paint = new SKPaint())
        {
            if (!isSubtractive)
            {
                paint.BlendMode = SKBlendMode.Plus; 
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Red;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Lime;    // == (00, FF, 00)
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Blue;
                canvas.DrawCircle(center3, radius, paint);
            }
            else
            {
                paint.BlendMode = SKBlendMode.Multiply
                System.Diagnostics.Debug.WriteLine(paint.BlendMode);

                paint.Color = SKColors.Cyan;
                canvas.DrawCircle(center1, radius, paint);

                paint.Color = SKColors.Magenta;
                canvas.DrawCircle(center2, radius, paint);

                paint.Color = SKColors.Yellow;
                canvas.DrawCircle(center3, radius, paint);
            }
        }
    }
}
```

此套裝程式含 `TabGestureRecognizer` 。 當您按一下或按一下螢幕時，程式會使用 `SKBlendMode.Multiply` 來顯示三個 subtractive 主要複本：

[![Subtractive 主要色彩](separable-images/PrimaryColors-Subtractive.png "Subtractive 主要色彩")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

`Darken`此模式也適用于相同的效果。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)