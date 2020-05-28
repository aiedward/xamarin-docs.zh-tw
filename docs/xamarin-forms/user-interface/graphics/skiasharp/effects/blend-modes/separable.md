---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c1939c30cbefdbf8d6546761a8c6ac7199bfff62
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139681"
---
# <a name="the-separable-blend-modes"></a>分離的 blend 模式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您在[**SkiaSharp Porter-Duff blend 模式一**](porter-duff.md)文中所見，Porter-Duff blend 模式通常會執行裁剪作業。 可分離的 blend 模式有所不同。 可分離的模式會改變影像的個別紅色、綠色和藍色色彩元件。 可分離的 blend 模式可以混合色彩，以示範紅色、綠色和藍色的組合確實是白色：

![主要色彩](separable-images/SeparableSample.png "主要色彩")

## <a name="lighten-and-darken-two-ways"></a>以兩種方式淡化和變暗 

通常會有稍微暗或太淺的點陣圖。 您可以使用可分離的 blend 模式來淡化或加深 imabe。  事實上，列舉中的兩個可分離 blend 模式 [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) 名為 `Lighten` 和 `Darken` 。 

這兩種模式會在 [**淡化和變暗**] 頁面中示範。 XAML 檔案會具現化兩個 `SKCanvasView` 物件和兩個 `Slider` 視圖：

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

第一個 `SKCanvasView` 和 `Slider` 示範 `SKBlendMode.Lighten` ，第二組示範 `SKBlendMode.Darken` 。 這兩個 `Slider` 視圖會共用相同的 `ValueChanged` 處理常式，而這兩個會 `SKCanvasView` 共用相同的 `PaintSurface` 處理常式。 這兩個事件處理常式都會檢查哪個物件正在引發事件：

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

`PaintSurface`處理常式會計算適合點陣圖的矩形。 處理常式會顯示該點陣圖，然後使用 `SKPaint` 物件，並 `BlendMode` 將其屬性設定為或，以在點陣圖上顯示一個矩形 `SKBlendMode.Lighten` `SKBlendMode.Darken` 。 `Color`屬性是以為基礎的灰色陰影 `Slider` 。 針對 `Lighten` 模式，色彩的範圍是從黑色到白色，但在 `Darken` 模式中，其範圍從白色到黑色。

從左至右的螢幕擷取畫面會顯示越來越大的 `Slider` 值，因為頂端影像變得較淡，而下方影像則變暗：

[![淡化和變暗](separable-images/LightenAndDarken.png "淡化和變暗")](separable-images/LightenAndDarken-Large.png#lightbox)

此程式示範使用可分離 blend 模式的一般方式：目的地是某種影像，通常是點陣圖。 來源是使用 `SKPaint` 物件的矩形，其 `BlendMode` 屬性設定為可分離的 blend 模式。 矩形可以是純色（如這裡所示）或漸層。 透明度通常_不_會與可分離的 blend 模式搭配使用。

當您嘗試使用此程式時，您會發現這兩種 blend 模式不會將影像淡化並均勻變暗。 相反地， `Slider` 似乎會設定某種類型的臨界值。 例如，當您增加模式的 `Slider` `Lighten` 時，影像的較暗區域會先亮，而較亮的區域則維持不變。

針對 `Lighten` 模式，如果目的地圖元是 RGB 色彩值（Dr、Dg、Db），而來源圖元是色彩（Sr，Sg，Sb），則輸出為（或，Og，Ob），其計算方式如下：

 `Or = max(Dr, Sr)` `Og = max(Dg, Sg)`
 `Ob = max(Db, Sb)`

分別針對紅色、綠色和藍色，結果會是目的地和來源的較大。 這會產生將目的地的深色區域變亮的效果。

此 `Darken` 模式類似，不同之處在于結果是目的地和來源的較小者：

 `Or = min(Dr, Sr)` `Og = min(Dg, Sg)`
 `Ob = min(Db, Sb)`

紅色、綠色和藍色元件分別各自處理，這就是為什麼這些 blend 模式稱為可_分離_的 blend 模式。 基於這個理由，縮寫**Dc**和**Sc**可以用於目的地和來源色彩，並瞭解計算會分別套用到每個紅色、綠色和藍色元件。

下表顯示所有可分離的 blend 模式，並簡短說明其用途。 第二個數據行顯示不會產生變更的來源色彩：

| Blend 模式   | 沒有變更 | 作業 |
| ---
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------ |---標題：描述： ms. 生產： assetid： author： ms. author： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

----- |---標題：描述： ms. 生產： assetid： author： ms. author： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

----- | |`Plus`       |黑色 |藉由新增色彩來將其變亮： Sc + Dc | |`Modulate`   |白色 |以乘以色彩來變暗： Sc ·Dc | |`Screen`     |黑色 |補充產品： Sc + Dc &ndash; Sc ·Dc | |`Overlay`    |灰色 |反向的 `HardLight` | | `Darken`    |白色 |最小色彩： min （Sc，Dc） | |`Lighten`    |黑色 |最大色彩：最大值（Sc，Dc） | |`ColorDodge` |黑色 |根據來源的變亮目的地 | |`ColorBurn`  |白色 |根據來源變暗目的地 | |`HardLight`  |灰色 |類似于粗糙焦點的效果 | |`SoftLight`  |灰色 |類似于軟性焦點的效果 | |`Difference` |黑色 |減去較淡的： Abs （Dc &ndash; Sc） | | `Exclusion` |黑色 |類似 `Difference` 但較低對比度 | | `Multiply`  |白色 |以乘以色彩來變暗： Sc ·Dc |

如需更詳細的演算法，請參閱 W3C 撰寫[**和混合層級 1**](https://www.w3.org/TR/compositing-1/)規格和 Skia [**SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)，雖然這兩個來源中的標記法並不相同。 請記住， `Plus` 通常會被視為 Porter Duff blend 模式，而 `Modulate` 不是 W3C 規格的一部分。

如果來源是透明的，則針對所有可分離的 blend 模式 `Modulate` （除外），blend 模式不會有任何作用。 如先前所見， `Modulate` blend 模式會將 Alpha 色板納入乘法。 否則，與 `Modulate` 具有相同的效果 `Multiply` 。 

請注意兩種模式 `ColorDodge` ，名為和 `ColorBurn` 。 在攝影暗房實務中，會以_減淡_和_燒錄_的字為來源。 放大鏡會透過一個負面的光源來進行相片列印。 如果沒有光線，列印就會是白色。 列印會變暗，因為列印有較長的一段時間。 列印人員通常會使用一或多個物件來封鎖部分列印的某些光線，讓該區域更亮。 這就是所謂的_躲過_。 相反地，有一個洞中的不透明材質（或手中大部分的光線），可以用來在特定位置將更多光源帶到變暗，稱為「_燒錄_」。

**減淡和燒錄**程式非常類似于**淡化和變暗**。 XAML 檔案的結構相同，但具有不同的元素名稱，而程式碼後置檔案同樣類似，但這兩種混合模式的效果相當不同：

[![減淡和燒錄](separable-images/DodgeAndBurn.png "減淡和燒錄")](separable-images/DodgeAndBurn-Large.png#lightbox)

若為較小的 `Slider` 值， `Lighten` 模式會先使深色區域變亮，同時以 `ColorDodge` 更一致的方式來比較。

影像處理應用程式通常可讓躲過和燒錄僅限於特定區域，就像在暗房中一樣。 這可以透過漸層或以灰色陰影的點陣圖來完成。

## <a name="exploring-the-separable-blend-modes"></a>探索分離的 blend 模式

[可**分離的 Blend 模式**] 頁面，可讓您檢查所有可分離的 blend 模式。 它會使用其中一個 blend 模式來顯示點陣圖目的地和彩色矩形來源。 

XAML 檔案會定義 `Picker` （以選取 blend 模式）和四個滑杆。 前三個滑杆可讓您設定來源的紅色、綠色和藍色元件。 第四個滑杆的目的是要藉由設定灰色網底來覆寫這些值。 系統不會識別個別的滑杆，但色彩會指出其功能：

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

程式碼後置檔案會載入其中一個點陣圖資源，並將它繪製兩次，一次在畫布的上半部，並再次出現在畫布的下半部：

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

在處理常式的底部 `PaintSurface` ，會使用選取的 blend 模式和選取的色彩，在第二個位圖上繪製一個矩形。 您可以比較底端的已修改點陣圖和頂端的原始點陣圖：

[![可分隔的混合模式](separable-images/SeparableBlendModes.png "可分隔的混合模式")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>加法和 subtractive 主要色彩

[**主要色彩**] 頁面會繪製三個重迭的紅色、綠色和藍色圓形：

[![附加主要色彩](separable-images/PrimaryColors-Additive.png "附加主要色彩")](separable-images/PrimaryColors-Additive.png#lightbox)

這些是附加的主要色彩。 任何兩個的組合都會產生青色、洋紅和黃色，而三個的組合則是白色。

這三個圓圈是以 `SKBlendMode.Plus` 模式繪製，但是您也可以使用 `Screen` 、 `Lighten` 或 `Difference` 來取得相同的效果。 程式如下：

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

套裝程式含 `TabGestureRecognizer` 。 當您點擊或按一下畫面時，程式會使用 `SKBlendMode.Multiply` 來顯示三個 subtractive 主要複本：

[![Subtractive 主要色彩](separable-images/PrimaryColors-Subtractive.png "Subtractive 主要色彩")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

`Darken`此模式也適用于相同的效果。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
