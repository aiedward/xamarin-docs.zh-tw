---
title: SkiaSharp 圓形漸層
description: 瞭解根據圓形的不同漸層類型。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ec84ac906ac146f37ba5b161a898582ce483bc95
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556668"
---
# <a name="the-skiasharp-circular-gradients"></a>SkiaSharp 圓形漸層

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKShader`](xref:SkiaSharp.SKShader)類別會定義靜態方法，以建立四種不同類型的漸層。 [**SkiaSharp 線性**](linear-gradient.md)漸層文章會討論 [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) 方法。 本文涵蓋其他三種類型的漸層，這些都是以圓形為基礎。

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*)方法會建立從圓形中央 emanates 的漸層：

![放射漸層範例](circular-gradients-images/RadialGradientSample.png)

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*)方法會建立在圓形中心周圍掃過的漸層：

![整理漸層範例](circular-gradients-images/SweepGradientSample.png)

第三種類型的漸層相當不尋常。 它稱為雙點圓錐漸層，且由 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) 方法定義。 漸層會從一個圓形延伸至另一個圓形：

![圓錐漸層樣本](circular-gradients-images/ConicalGradientSample.png)

如果這兩個圓形不同的大小，則漸層會採用錐形的形式。

本文將更詳細地探討這些漸層。

## <a name="the-radial-gradient"></a>放射狀漸層

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有下列語法：

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

多載 [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 也包含轉換矩陣參數。

前兩個引數會指定圓形的中心和半徑。 漸層開始于該中心，並向外擴充以取得 `radius` 圖元。 發生的情況 `radius` 取決於 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 引數。 `colors`參數是兩個或多個色彩的陣列 (如同線性漸層方法) ，而且 `colorPos` 是介於0到1範圍內的整數陣列。 這些整數表示沿著該行之色彩的相對位置 `radius` 。 您可以將該引數設定為，使 `null` 色彩的空間相同。

如果您使用 `CreateRadialGradient` 填滿圓形，則可以將漸層的中心設定為圓形的中心，並將漸層的半徑設定為圓形半徑。 在此情況下，引數對漸層轉譯 `SKShaderTileMode` 沒有任何作用。 但是，如果漸層填滿的區域大於漸層所定義的圓形，則引數會對圓圈以外的部分產生 `SKShaderTileMode` 重大影響。

的效果 `SKShaderMode` 會在[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的**Radial Gradient**星形漸層頁面中示範。 此頁面的 XAML 檔案具現化 `Picker` ，可讓您選取列舉的三個成員之一 `SKShaderTileMode` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.RadialGradientPage"
             Title="Radial Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="0"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

程式碼後端檔案會使用放射狀漸層來將整個畫布色彩。 漸層的中心設定為畫布的中心，而半徑設定為100圖元。 漸層只由兩個色彩（黑色和白色）所組成：

```csharp
public partial class RadialGradientPage : ContentPage
{
    public RadialGradientPage ()
    {
        InitializeComponent ();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                new SKPoint(info.Rect.MidX, info.Rect.MidY),
                                100,
                                new SKColor[] { SKColors.Black, SKColors.White },
                                null,
                                tileMode);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

這段程式碼會在中央建立具有黑色的漸層，逐漸漸淡至中央的白色100圖元。 超過該半徑的情況取決於 `SKShaderTileMode` 引數：

[![放射漸層](circular-gradients-images/RadialGradient.png "放射漸層")](circular-gradients-images/RadialGradient-Large.png#lightbox)

在這三種情況下，漸層會填滿畫布。 在左側的 iOS 畫面上，超出半徑的漸層會以最後的色彩（即白色）繼續。 這是的結果 `SKShaderTileMode.Clamp` 。 Android 畫面會顯示 `SKShaderTileMode.Repeat` 下列效果：從中央為100圖元，漸層會以第一種色彩（黑色）開始。 漸層會重複每100個半徑的圖元。 

右側的通用 Windows 平臺畫面會顯示如何讓漸層成為 `SKShaderTileMode.Mirror` 替代方向。 第一個漸層是從中央到白色的黑色，半徑為100圖元。 下一步是在200圖元半徑的 100-圖元半徑至黑色的白色，並再次反轉下一個漸層。

您可以在星形漸層中使用兩個以上的色彩。 **彩虹弧線**漸層範例會建立八個色彩的陣列，其對應至彩虹的色彩，並以紅色結束，也是八個位置值的陣列：

```csharp
public class RainbowArcGradientPage : ContentPage
{
    public RainbowArcGradientPage ()
    {
        Title = "Rainbow Arc Gradient";

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
            float rainbowWidth = Math.Min(info.Width, info.Height) / 4f;

            // Center of arc and gradient is lower-right corner
            SKPoint center = new SKPoint(info.Width, info.Height);

            // Find outer, inner, and middle radius
            float outerRadius = Math.Min(info.Width, info.Height);
            float innerRadius = outerRadius - rainbowWidth;
            float radius = outerRadius - rainbowWidth / 2;

            // Calculate the colors and positions
            SKColor[] colors = new SKColor[8];
            float[] positions = new float[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
                positions[i] = (i + (7f - i) * innerRadius / outerRadius) / 7f;
            }

            // Create sweep gradient based on center and outer radius
            paint.Shader = SKShader.CreateRadialGradient(center, 
                                                         outerRadius, 
                                                         colors, 
                                                         positions, 
                                                         SKShaderTileMode.Clamp);
            // Draw a circle with a wide line
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = rainbowWidth;

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

假設畫布的寬度和高度的最小值是1000，這表示此 `rainbowWidth` 值為250。 `outerRadius`和 `innerRadius` 值分別設定為1000和750。 這些值是用來計算 `positions` 陣列; 八個值的範圍從 0.75 f 到1。 此 `radius` 值用來對圓形進行筆劃。 875的值表示250圖元筆劃寬度會在半徑750圖元與半徑1000圖元之間擴充：

[![彩虹弧線漸層](circular-gradients-images/RainbowArcGradient.png "彩虹弧線漸層")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

如果您使用此漸層填滿整個畫布，您會看到它在內部半徑內是紅色的。 這是因為 `positions` 陣列的開頭不是0。 第一個色彩是用於0到第一個陣列值的位移。 漸層在外部半徑之外也是紅色。 這是磚模式的結果 `Clamp` 。 因為漸層是用來將粗線描邊，所以不會顯示這些紅色區域。

## <a name="radial-gradients-for-masking"></a>遮罩的放射漸層

如同線性漸層，放射狀漸層可以併入透明或部分透明的色彩。 這項功能適用于稱為 _遮罩_的進程，它會隱藏部分影像以強調影像的另一個部分。

**放射狀**漸層遮罩頁面會顯示範例。 此程式會載入其中一個資源點陣圖。 `CENTER`和 `RADIUS` 欄位是由點陣圖檢查和參考應該反白顯示的區域所決定。 `PaintSurface`處理常式一開始會先計算矩形以顯示點陣圖，然後將它顯示在該矩形中：

```csharp
public class RadialGradientMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(RadialGradientMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public RadialGradientMaskPage ()
    {
        Title = "Radial Gradient Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                                (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                     scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Transparent,
                                                SKColors.White },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            // Display rectangle using that gradient
            canvas.DrawRect(rect, paint);
        }
    }
}
```

繪製點陣圖之後，某些簡單的程式碼會將 `CENTER` 和轉換 `RADIUS` 成 `center` 和 `radius` ，這會參考點陣圖中醒目提示的區域，而該區域已針對顯示進行調整和移動。 這些值是用來建立具有該中心和半徑的放射漸層。 這兩種色彩從中央的透明開始，以及半徑的前60%。 漸層接著會淡化為白色：

[![放射漸層遮罩](circular-gradients-images/RadialGradientMask.png "放射漸層遮罩")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

這種方法不是遮罩點陣圖的最佳方式。 問題在於遮罩的色彩大多都有白色，也就是為了符合畫布的背景而選擇的色彩。 如果背景是其他色彩 &mdash; 或可能是漸層本身， &mdash; 則不會相符。 更好的遮罩方法會顯示在 [SkiaSharp Porter-Duff blend 模式](../blend-modes/porter-duff.md)的文章中。

## <a name="radial-gradients-for-specular-highlights"></a>放射反白顯示的星形漸層

當光線出現圓角表面時，它會以許多方向反映燈光，但有些光線會直接進入檢視器的眼睛。 這通常會在表面上建立一個模糊白色區域的外觀，稱為 _反射醒目_提示。

在三維圖形中，高光通常是由用來判斷淺色路徑和陰影的演算法所產生。 在二維圖形中，有時會新增高光，以建議3D 介面的外觀。 反光可將平面紅色圓形轉換成圓形紅色球形。

**放射放射狀醒目**提示頁面會使用星形漸層來精確處理。 `PaintSurface`處理常式是藉由計算圓形的半徑，以及 `SKPoint` &mdash; `center` `offCenter` 中間與圓形左上角中間的兩個值。

```csharp
public class RadialSpecularHighlightPage : ContentPage
{
    public RadialSpecularHighlightPage()
    {
        Title = "Radial Specular Highlight";

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

        float radius = 0.4f * Math.Min(info.Width, info.Height);
        SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
        SKPoint offCenter = center - new SKPoint(radius / 2, radius / 2);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                offCenter,
                                radius / 2,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

`CreateRadialGradient`呼叫會建立從該點開始的漸層（ `offCenter` 以白色為起點），並在半徑的一半距離以紅色結尾。 其看起來會像下面這樣：

[![放射星形高光](circular-gradients-images/RadialSpecularHighlight.png "放射星形高光")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

如果您仔細看看此漸層，您可能會決定它是否有缺陷。 漸層會以特定的點為中心，而且您可能會想要以較少的對稱來反映圓角表面。 在這種情況下，您可能會想要在顯示高光漸層的 [**圓錐**](#conical-gradients-for-specular-highlights)漸層區段中顯示如下的反射醒目提示。

## <a name="the-sweep-gradient"></a>掃色漸層

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[]))方法具有所有漸層建立方法的最簡單語法：

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

它只是中央、色彩陣列和色彩位置。 漸層會從中心點的右邊開始，並在中央周圍以順向方向掃約360度。 請注意，沒有任何 `SKShaderTileMode` 參數。

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))具有矩陣轉換參數的多載也可供使用。 您可以將旋轉轉換套用至漸層，以變更起始點。 您也可以套用縮放轉換來將方向從順時針變更為逆時針。

清除漸 **層頁面使用** 清除漸層來將筆劃寬度為50圖元的圓形上色：

[![掃色漸層](circular-gradients-images/SweepGradient.png "掃色漸層")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage`類別會定義具有不同色調值之八種色彩的陣列。 請注意，陣列的開頭和結尾會是紅色 (有0或 360) 的色調值，出現在螢幕擷取畫面的最右邊：

```csharp
public class SweepGradientPage : ContentPage
{
    bool drawBackground;

    public SweepGradientPage ()
    {
        Title = "Sweep Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Define an array of rainbow colors
            SKColor[] colors = new SKColor[8];

            for (int i = 0; i < colors.Length; i++)
            {
                colors[i] = SKColor.FromHsl(i * 360f / 7, 100, 50);
            }

            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);

            // Create sweep gradient based on center of canvas
            paint.Shader = SKShader.CreateSweepGradient(center, colors, null);

            // Draw a circle with a wide line
            const int strokeWidth = 50;
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = strokeWidth;

            float radius = (Math.Min(info.Width, info.Height) - strokeWidth) / 2;
            canvas.DrawCircle(center, radius, paint);

            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                paint.Style = SKPaintStyle.Fill;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

此程式也會執行 `TapGestureRecognizer` ，以在處理常式的結尾啟用某些 `PaintSurface` 程式碼。 此程式碼會使用相同的漸層來填滿畫布：

[![整理漸層已滿](circular-gradients-images/SweepGradientFull.png "整理漸層已滿")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

這些螢幕擷取畫面會示範漸層會填滿其色彩的任何區域。 如果漸層的開頭和結尾不是相同的色彩，則中心點右邊會出現不一致的情況。

## <a name="the-two-point-conical-gradient"></a>雙點圓錐漸層

[`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有下列語法：

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

這些參數的開頭為中間點，而半徑為兩個圓形，稱為 _開始_ 圓形和 _結束_ 圓圈。 其餘三個參數與 `CreateLinearGradient` 和相同 `CreateRadialGradient` 。 多載 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 包含矩陣轉換。

漸層開始于開始圓圈，並結束于結束圓圈。 `SKShaderTileMode`參數會控制在兩個圓圈以外的情況。 雙點圓錐漸層是唯一未完全填滿區域的漸層。 如果兩個圓形的半徑相同，則漸層會限制為寬度與圓形直徑相同的矩形。 如果兩個圓形的半徑不同，漸層會形成一個錐形。

您可能會想要試驗雙點的圓錐漸層，所以會從的 **錐** 漸層頁面衍生， `InteractivePage` 以允許針對兩個圓形半徑四處移動兩個觸控點：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.ConicalGradientPage"
                       Title="Conical Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>
        
        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

程式碼後端檔案會定義 `TouchPoint` 具有固定半徑50和100的兩個物件：

```csharp
public partial class ConicalGradientPage : InteractivePage
{
    const int RADIUS1 = 50;
    const int RADIUS2 = 100;

    public ConicalGradientPage ()
    {
        touchPoints = new TouchPoint[2];

        touchPoints[0] = new TouchPoint
        {
            Center = new SKPoint(100, 100),
            Radius = RADIUS1
        };

        touchPoints[1] = new TouchPoint
        {
            Center = new SKPoint(300, 300),
            Radius = RADIUS2
        };

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode = 
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ? 
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(touchPoints[0].Center,
                                                                  RADIUS1,
                                                                  touchPoints[1].Center,
                                                                  RADIUS2,
                                                                  colors,
                                                                  null,
                                                                  tileMode);
            canvas.DrawRect(info.Rect, paint);
        }

        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }
        }
    }
}
```

`colors`陣列是紅色、綠色和藍色。 接近處理程式底部的程式碼 `PaintSurface` 會將兩個觸控點繪製為黑色圓形，使其不會對漸層。

請注意，呼叫會使用漸層 `DrawRect` 來為整個畫布上色。 但是在一般情況下，漸層的大部分畫布仍 uncolored。 以下是顯示三個可能設定的程式：

[![圓錐漸層](circular-gradients-images/ConicalGradient.png "圓錐漸層")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

左側的 iOS 畫面會顯示 `SKShaderTileMode` 設定的效果 `Clamp` 。 漸層在較小圓形邊緣內以紅色開頭，相對於最接近第二個圓形的側邊。 `Clamp`值也會造成紅色，以繼續到錐形點。 漸層的漸層會以藍色在最接近第一個圓形的最大圓圈的外部邊緣，但在該圓圈和之後繼續。

Android 畫面類似于，但使用 `SKShaderTileMode` 的是 `Repeat` 。 現在，漸層的漸層會在第一個圓形內開始，並在第二個圓形之外結束。 此 `Repeat` 設定會導致漸層在較大的圓形內以紅色重複執行。

UWP 畫面會顯示當較小的圓形完全移至較大的圓形內部時，會發生什麼事。 漸層會停駐在錐形的範圍，而改為填滿整個區域。 效果類似于放射狀漸層，但如果較小的圓形未在較大的圓形內完全置中，則為非對稱。

當某個圓形嵌套在另一個圓形時，您可能會無法確定漸層的實際實用性，但它很適合用來顯示高光。

## <a name="conical-gradients-for-specular-highlights"></a>高光漸層的錐漸層

稍早在本文中，您已瞭解如何使用放射狀漸層來建立反射醒目提示。 您也可以針對此用途使用雙點圓錐漸層，而且您可能會偏好它的外觀：

[![圓錐反光高光](circular-gradients-images/ConicalSpecularHighlight.png "圓錐反光高光")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

非對稱的外觀更能建議物件的圓角表面。 

**圓錐狀醒目**提示頁面中的繪圖程式碼與星形**高光**頁面相同（著色器除外）：

```csharp
public class ConicalSpecularHighlightPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateTwoPointConicalGradient(
                                offCenter,
                                1,
                                center,
                                radius,
                                new SKColor[] { SKColors.White, SKColors.Red },
                                null,
                                SKShaderTileMode.Clamp);

            canvas.DrawCircle(center, radius, paint);
        }
    }
}
```

這兩個圓形具有和的中心 `offCenter` `center` 。 置中的圓形 `center` 與包含整個球的半徑相關聯，但以 x 為中心的圓形的 `offCenter` 半徑只有一個圖元。 漸層實際上會從該點開始，並結束于球的邊緣。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)