---
title: 可加以分隔的混合模式
description: 您可以使用分隔的混合模式 alter 紅色、 綠色和藍色色彩。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 66D1A537-A247-484E-B5B9-FBCB7838FBE9
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 594e98230d4f4bd8aca27f92f4544f8c59b5f0a2
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061451"
---
# <a name="the-separable-blend-modes"></a>可加以分隔的混合模式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

如您所見文中所[ **SkiaSharp Porter Duff 混合模式**](porter-duff.md)，Porter Duff blend 模式通常會執行裁剪作業。 可分隔的 blend 模式都不同。 分隔模式會改變的個別紅色、 綠色和藍色色彩元件的映像。 可加以分隔的混合模式可以混用來示範紅色、 綠色及藍色的組合是確實白色的色彩：

![主要色彩](separable-images/SeparableSample.png "主要色彩")

## <a name="lighten-and-darken-two-ways"></a>淡化和暗化兩種方式 

通常會有某種程度的點陣圖太暗或太亮。 若要淡化 / 暗化 imabe，您可以使用分隔的混合模式。  事實上，有兩個中的分隔 blend 模式[ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode)名為列舉型別`Lighten`和`Darken`。 

這兩種模式中會示範**淡化和暗化**頁面。 XAML 檔案具現化兩個`SKCanvasView`物件和兩個`Slider`檢視：

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

第一個`SKCanvasView`並`Slider`示範`SKBlendMode.Lighten`，並示範第二組`SKBlendMode.Darken`。 這兩個`Slider`檢視會共用相同`ValueChanged`處理常式，以及兩個`SKCanvasView`共用同一個`PaintSurface`處理常式。 這兩個事件處理常式檢查哪個物件已經引發事件：

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

`PaintSurface`處理常式會計算適用於點陣圖的矩形。 這個處理常式會顯示該點陣圖，並接著會透過點陣圖使用的顯示矩形`SKPaint`物件及其`BlendMode`屬性設定為`SKBlendMode.Lighten`或`SKBlendMode.Darken`。 `Color`屬性是根據灰色陰影`Slider`。 針對`Lighten`模式中，色彩範圍從黑色到白色，而`Darken`其範圍從白色到黑色的模式。

螢幕擷取畫面從左到右顯示日漸變大`Slider`值的最上方的影像取得較輕，而下方影像取得較暗：

[![淡化和暗化](separable-images/LightenAndDarken.png "淡化和暗化")](separable-images/LightenAndDarken-Large.png#lightbox)

此程式示範一般方式使用分隔的混合模式： 目的地是某種形式，通常是點陣圖影像。 來源是使用顯示矩形`SKPaint`物件及其`BlendMode`屬性設定為可分隔的混合模式。 （如以下所示），矩形可以是單色或漸層。 透明_不_一般與分隔 blend 模式搭配使用。

實驗與此程式時，您會發現，這兩個混合模式不要淡化和統一暗化影像。 相反地，`Slider`似乎設定某種形式的臨界值。 例如，當您增加`Slider`針對`Lighten`模式中，映像較暗區域光第一次時取得較淺的區域維持不變。

針對`Lighten`模式中，如果目的像素的 RGB 色彩值 (Dr，通訊群組，Db)，且來源像素的色彩 (Sr，Sg，Sb)，則輸出為 （或 Og，Ob） 計算，如下所示：

 = 最大值 （Dr，Sr） 或 Og = max （通訊群組，Sg） Ob = max （Db、 Sb）

另外，紅色、 綠色和藍色較大的目的地和來源的結果。 這會產生第一次 lightening 目的地的深色區域的影響。

`Darken`模式很相似，不同之處在於結果會是較小的目的地和來源：

 或 = 最小值 （Dr，Sr） Og = 最小值 （通訊群組，Sg） Ob = 最小值 （Db，Sb）

紅色、 綠色和藍色元件是每個分開處理的這就是為什麼這些混合模式指_分隔_混合模式。 基於這個理由，縮寫**Dc**並**Sc**可用於目的地和來源色彩和理解，計算套用至每個紅色、 綠色和藍色元件分開。

下表顯示所有可加以分隔的 blend 模式及其用途的簡短說明。 第二個資料行顯示不產生任何變更的來源色彩：

| 混合模式   | 沒有變更 | 運算 |
| ------------ | --------- | --------- |
| `Plus`       | 黑色     | 藉由新增色彩淡化： Sc + Dc |
| `Modulate`   | 白皮書     | 乘以色彩會變暗： Sc·Dc | 
| `Screen`     | 黑色     | 補充的補充項目乘積： Sc + Dc &ndash; Sc·Dc |
| `Overlay`    | 灰色      | 相反值 `HardLight` |
| `Darken`     | 白皮書     | 色彩的最小值： 最小值 （Sc，Dc） |
| `Lighten`    | 黑色     | 色彩的最大值： 最大值 （Dc 中的 Sc） |
| `ColorDodge` | 黑色     | 變亮來源為基礎的目的地 |
| `ColorBurn`  | 白皮書     | 來源為基礎的目的地會變暗 | 
| `HardLight`  | 灰色      | 類似於惡劣的焦點的效果 |
| `SoftLight`  | 灰色      | 類似於虛焦點的效果 | 
| `Difference` | 黑色     | 減去從較深： Abs (Dc &ndash; Sc) | 
| `Exclusion`  | 黑色     | 類似於`Difference`但較低對比 |
| `Multiply`   | 白皮書     | 乘以色彩會變暗： Sc·Dc |

更詳細的演算法，請參閱 W3C [**複合 （compositing） 和混合層級 1** ](https://www.w3.org/TR/compositing-1/)規格和 Skia [ **SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)，不過這些兩個來源中的標記並不相同。 請記住`Plus`通常被視為 Porter Duff 混合模式中，和`Modulate`不是 W3C 規格的一部分。

如果來源是透明的然後針對所有分隔混合模式除了`Modulate`，混合模式沒有任何作用。 您稍早看到`Modulate`混合模式包含乘法運算中的 alpha 色板。 否則，請`Modulate`具有相同的效果`Multiply`。 

請注意名為兩種模式`ColorDodge`和`ColorBurn`。 單字_閃躲前方_並_燒錄_在相片暗房做法產生。 加大可如何透過為負數的光線，讓相片列印。 沒有光線，列印為白色。 列印取得深，更多的光線落在較長的一段時間的列印。 列印製作者通常使用手動或小型物件封鎖一些從落在 [列印]，讓較淺該區域中的某個部分的燈。 這就所謂_躲過_。 相反地，使用它 （或封鎖大部分的燈光的指針） 中的一個洞的不透明資料可用來直接變暗，叫做 特定的位置中的多個 light_燒錄_。

**閃躲前方和 Burn**程式是非常類似於**淡化和暗化**。 XAML 檔案結構相同，但是有不同的項目名稱，且程式碼後置檔案同樣相當類似，但這兩個混合模式的影響是很不一樣：

[![巧妙迴避和 Burn](separable-images/DodgeAndBurn.png "閃躲前方和 Burn")](separable-images/DodgeAndBurn-Large.png#lightbox)

為小型`Slider`值，`Lighten`模式調淡暗區第一，雖然`ColorDodge`調淡更一致的方式。

躲過和燒錄至限制為特定區域，就像在暗房，通常可讓映像處理應用程式。 這可藉由漸層，或各種灰色陰影的點陣圖。

## <a name="exploring-the-separable-blend-modes"></a>探索可加以分隔的混合模式

**分隔的混合模式**頁面可讓您檢查所有可加以分隔的混合模式。 它會顯示點陣圖目的地和彩色的矩形來源使用其中一種混合模式。 

XAML 檔案會定義`Picker`（以選取混合模式） 和四個滑桿。 前三個滑桿可讓您設定來源的紅色、 綠色和藍色元件。 第四個滑桿被要設定灰色陰影來覆寫這些值。 不會識別個別的滑桿，但色彩表示其功能：

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

程式碼後置檔案載入其中一個點陣圖資源，並將它繪製兩次，一次在畫布的上半部和一次下方的畫布下半部：

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

底端`PaintSurface`處理常式中，矩形會繪製在選取的混合模式與所選取之的色彩的第二個點陣圖之上。 您可以比較底部與原始點陣圖頂端的已修改的點陣圖：

[![可加以分隔的混合模式](separable-images/SeparableBlendModes.png "分隔的混合模式")](separable-images/SeparableBlendModes-Large.png#lightbox)

## <a name="additive-and-subtractive-primary-colors"></a>加總，而且涉及刪減的主要色彩

**主要色彩**頁面繪製紅色、 綠色及藍色的三個重疊圓形：

[![加總的主要色彩](separable-images/PrimaryColors-Additive.png "加總的主要色彩")](separable-images/PrimaryColors-Additive.png#lightbox)

這些是加總的主要色彩。 任何兩個的組合產生青色、 洋紅及黃色，而所有的三種組合是白色。

下列三個社交圈會以繪製`SKBlendMode.Plus`模式，但您也可以使用`Screen`， `Lighten`，或`Difference`效果相同。 以下是程式：

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

此程式包括`TabGestureRecognizer`。 當您點選或按一下  畫面時，程式會使用`SKBlendMode.Multiply`顯示三個涉及刪減的主要複本：

[![涉及刪減的主要色彩](separable-images/PrimaryColors-Subtractive.png "涉及刪減的主要色彩")](separable-images/PrimaryColors-Subtractive-Large.png#lightbox)

`Darken`模式也適用於此相同的效果。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
