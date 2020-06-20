---
title: SkiaSharp 迴圈漸層
description: 瞭解以圓形為基礎的不同漸層類型。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 418d29010a8cce81d2bb8c365608c54b61739622
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135638"
---
# <a name="the-skiasharp-circular-gradients"></a>SkiaSharp 迴圈漸層

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKShader`](xref:SkiaSharp.SKShader)類別會定義靜態方法，以建立四種不同類型的漸層。 [**SkiaSharp 線性**](linear-gradient.md)漸層文章討論 [`CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) 方法。 本文涵蓋其他三種類型的漸層，這些都是以圓形為基礎。

[`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient*)方法會建立從圓形中央 emanates 的漸層：

![放射漸層範例](circular-gradients-images/RadialGradientSample.png)

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient*)方法會建立一個沿著圓形中心進行掃描的漸層：

![清理漸層範例](circular-gradients-images/SweepGradientSample.png)

第三種類型的漸層很異常。 這稱為雙點圓錐漸層，由 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*) 方法定義。 漸層會從一個圓形延伸至另一個圓圈：

![圓錐漸層範例](circular-gradients-images/ConicalGradientSample.png)

如果兩個圓圈的大小不同，則漸層的格式會是圓錐。

這篇文章會更詳細地探討這些漸層。

## <a name="the-radial-gradient"></a>放射狀漸層

此 [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) 方法具有下列語法：

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

多載 [`CreateRadialGradient`](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 也包括轉換矩陣參數。

前兩個引數會指定圓形的中心和半徑。 漸層從該中心開始，並向外擴充以提供 `radius` 圖元。 發生的情況 `radius` 視 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 引數而定。 `colors`參數是兩個或多個色彩的陣列（就像線上性漸層方法中一樣），而 `colorPos` 是介於0到1之間的整數陣列。 這些整數會指出色彩沿著那一行的相對位置 `radius` 。 您可以將該引數設定為， `null` 使色彩的空間相等。

如果您使用 `CreateRadialGradient` 來填滿圓形，可以將漸層的中央設為圓形的中心，並將漸層的半徑設定為圓形的半徑。 在此情況下， `SKShaderTileMode` 引數不會影響漸層的呈現。 但是，如果漸層填滿的區域大於漸層所定義的圓形，則引數會對在 `SKShaderTileMode` 圓形外發生的動作有深遠的影響。

的效果 `SKShaderMode` 會在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的**放射**漸層頁面中示範。 此頁面的 XAML 檔案會具現化 `Picker` ，可讓您選取列舉的三個成員之一 `SKShaderTileMode` ：

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

程式碼後置檔案會使用放射狀漸層來為整個畫布加上色彩。 漸層的中心是設定為畫布的中央，而半徑設定為100圖元。 漸層只包含兩種色彩：黑色和白色。。

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

這段程式碼會在中央建立具有黑色的漸層，並從中央逐漸淡出白色100圖元。 除了 radius 以外，它還會根據 `SKShaderTileMode` 引數而定：

[![放射漸層](circular-gradients-images/RadialGradient.png "放射漸層")](circular-gradients-images/RadialGradient-Large.png#lightbox)

在這三種情況下，漸層都會填滿畫布。 在左側的 iOS 畫面上，超過半徑的漸層會繼續使用最後一個色彩，也就是白色。 這是的結果 `SKShaderTileMode.Clamp` 。 [Android] 畫面會顯示 `SKShaderTileMode.Repeat` 從中央開始的100圖元的效果，漸層會以第一個色彩（黑色）重新開始。 梯度會重複每100圖元的半徑。 

右側的 [通用 Windows 平臺] 畫面會顯示如何讓漸層成為 `SKShaderTileMode.Mirror` 其他方向。 第一個漸層是從黑色到白色，在100圖元的半徑的中間。 下一個是200圖元半徑的 100-圖元半徑到黑色的白色，而下一個漸層則會再次反轉。

放射狀漸層中可以使用兩種以上的色彩。 **彩虹 Arc**漸層範例會建立八個色彩的陣列，其對應于彩虹和結尾為紅色的色彩，同時也是八個位置值的陣列：

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

假設畫布的寬度和高度的最小值為1000，表示 `rainbowWidth` 該值為250。 `outerRadius`和 `innerRadius` 值會分別設定為1000和750。 這些值會用來計算 `positions` 陣列，八個值的範圍介於 0.75 f 到1之間。 `radius`值是用來為圓形加上筆劃。 875的值表示250圖元的筆劃寬度會在750圖元的半徑和1000圖元的半徑之間擴充：

[![彩虹弧線漸層](circular-gradients-images/RainbowArcGradient.png "彩虹弧線漸層")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

如果您使用此漸層填滿整個畫布，您會看到它在內部半徑內是紅色的。 這是因為 `positions` 陣列的開頭不是0。 第一個色彩用於0到第一個陣列值的位移。 漸層也是紅色，超出外部半徑。 這是磚模式的結果 `Clamp` 。 因為漸層用於粗線的筆劃，所以不會顯示這些紅色區域。

## <a name="radial-gradients-for-masking"></a>遮罩的放射漸層

如同線性漸層，星形漸層可以納入透明或部分透明的色彩。 這項功能適用于稱為_遮罩_的進程，它會隱藏部分影像以強調影像的另一個部分。

[**放射**漸層遮罩] 頁面會顯示範例。 程式會載入其中一個資源點陣圖。 `CENTER`和 `RADIUS` 欄位是從點陣圖的檢查決定，並參考應反白顯示的區域。 `PaintSurface`處理常式一開始會計算一個矩形來顯示點陣圖，然後在該矩形中顯示它：

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

繪製點陣圖之後，有些簡單的程式碼會將 `CENTER` 和轉換 `RADIUS` 成 `center` 和 `radius` ，這會參考點陣圖中反白顯示的區域，而該點陣圖中已縮放並已移動以供顯示。 這些值是用來建立具有該中心和半徑的放射狀漸層。 這兩種色彩從中間開始，並以半徑的前60% 為開頭。 然後漸層會淡入白色：

[![放射漸層遮罩](circular-gradients-images/RadialGradientMask.png "放射漸層遮罩")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

這種方法不是遮罩點陣圖的最佳方式。 問題在於遮罩的色彩大多是白色，其選擇與畫布的背景一致。 如果背景是其他色彩 &mdash; 或可能是漸層本身， &mdash; 則不會相符。 [SkiaSharp Porter-Duff blend 模式](../blend-modes/porter-duff.md)一文中會顯示較佳的遮罩方式。

## <a name="radial-gradients-for-specular-highlights"></a>反射反白顯示的放射漸層

當光線出現圓角表面時，它會以許多方向來反映光，但是有些光線會直接進入檢視器的眼睛。 這通常會在表面上建立模糊白色區域的外觀，稱為_反射醒目_提示。

在3d 圖形中，反光通常是來自用來決定淺色路徑和陰影的演算法所產生的結果。 在二維圖形中，有時會加入反射高光，以建議3D 介面的外觀。 反射反白顯示可以將平面紅色圓形轉換成圓形紅色球形。

星形**反射反白顯示**頁面會使用放射狀漸層來精確地執行此動作。 `PaintSurface`處理常式是藉由計算圓形的半徑，以及兩個 `SKPoint` 值 &mdash; ， `center` 且 `offCenter` 介於中央和圓形左上角的中間之間：

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

此 `CreateRadialGradient` 呼叫會建立以白色開始的漸層， `offCenter` 並在半徑一半的距離以紅色結束。 看起來如下：

[![星形反射反白顯示](circular-gradients-images/RadialSpecularHighlight.png "星形反射反白顯示")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

如果您仔細查看此漸層，您可能會決定其是否有瑕疵。 漸層是以特定點為中心，而您可能想要以較小的對稱方式來反映圓角表面。 在這種情況下，您可能會想要在反射反白[**顯示的圓錐**](#conical-gradients-for-specular-highlights)漸層一節中顯示如下的反射醒目提示。

## <a name="the-sweep-gradient"></a>清理漸層

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[]))方法具有所有漸層建立方法的最簡單語法：

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

它只是一個中心、一個色彩陣列和色彩位置。 漸層會從中心點的右邊開始，並以順時針方向在中央處掃描360度。 請注意，沒有 `SKShaderTileMode` 參數。

[`CreateSweepGradient`](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))同時也提供具有矩陣轉換參數的多載。 您可以將旋轉轉換套用至漸層，以變更開始點。 您也可以套用尺規轉換，將方向從順時針變更為逆時針。

[**清除**漸層] 頁面使用清除漸層來為筆觸寬度為50圖元的圓形加上色彩：

[![清理漸層](circular-gradients-images/SweepGradient.png "清理漸層")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage`類別會定義具有不同色調值之八種色彩的陣列。 請注意，陣列的開頭和結尾都是紅色（色調值為0或360），這會出現在螢幕擷取畫面的最右邊：

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

此程式也會執行 `TapGestureRecognizer` ，以在處理常式的結尾啟用一些 `PaintSurface` 程式碼。 這段程式碼會使用相同的漸層來填滿畫布：

[![清理漸層已滿](circular-gradients-images/SweepGradientFull.png "清理漸層已滿")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

這些螢幕擷取畫面顯示漸層填滿其彩色的任何區域。 如果漸層不是以相同的色彩開始和結束，中心點的右方就會發生不連續的情況。

## <a name="the-two-point-conical-gradient"></a>雙點圓錐漸層

此 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode)) 方法具有下列語法：

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

參數的開頭為中間點，而半徑代表兩個圓形，稱為_開始_圓形和_結束_圓形。 其餘三個參數與 `CreateLinearGradient` 和相同 `CreateRadialGradient` 。 多載 [`CreateTwoPointConicalGradient`](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 包括矩陣轉換。

漸層從開始圓形開始，並在結束圓形結束。 `SKShaderTileMode`參數會控制在兩個圓形以外的情況。 雙點的圓錐漸層是唯一不會完全填滿區域的漸層。 如果兩個圓形具有相同的半徑，則會將漸層限制為寬度與圓形直徑相同的矩形。 如果兩個圓圈有不同的半徑，漸層會形成一個錐形。

您很可能會想要試驗雙點的圓錐漸層，因此，**圓錐**漸層頁面衍生自， `InteractivePage` 以允許在兩個圓形半徑前後移動兩個觸控點：

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

程式碼後置檔案會定義 `TouchPoint` 具有固定半徑50和100的兩個物件：

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

`colors`陣列是紅色、綠色和藍色。 接近處理程式底部的程式碼 `PaintSurface` 會將兩個觸控點繪製為黑色圓形，使其不會妨礙漸層。

請注意，呼叫會使用漸層 `DrawRect` 來為整個畫布上色。 不過，在一般情況下，漸層中的大部分畫布仍然是 uncolored 的。 以下是顯示三種可能設定的程式：

[![圓錐漸層](circular-gradients-images/ConicalGradient.png "圓錐漸層")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

左側的 iOS 畫面會顯示設定的效果 `SKShaderTileMode` `Clamp` 。 漸層在較小圓圈的邊緣（相對於最接近第二個圓形的側邊）中以紅色開頭。 此 `Clamp` 值也會造成紅色，以繼續到錐形的點。 在最靠近第一個圓形之較大圓形的外部邊緣，漸層會以藍色結束，但會在該圓形內以藍色繼續，並在該圓圈的後面。

Android 畫面類似，但具有 `SKShaderTileMode` 的 `Repeat` 。 現在更清楚的是，漸層是從第一個圓形開始，並在第二個圓形之外結束。 此 `Repeat` 設定會導致漸層在較大圓圈內以紅色重複出現。

UWP 畫面會顯示將較小的圓形完全移到較大的圓形內時，會發生什麼事。 漸層停駐于錐形，而是填滿整個區域。 效果類似于星形漸層，但如果較小的圓形不是在較大的圓形內，則不對稱。

當某個圓形已內嵌在另一個圓圈時，您可能會不確定漸層的實際實用性，但它非常適合反射醒目提示。

## <a name="conical-gradients-for-specular-highlights"></a>反射反白顯示的圓錐漸層

稍早在本文中，您已瞭解如何使用放射狀漸層來建立反射醒目提示。 您也可以針對此目的使用雙點的圓錐漸層，而且您可能會偏好其外觀：

[![圓錐反射反白顯示](circular-gradients-images/ConicalSpecularHighlight.png "圓錐反射反白顯示")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

不對稱的外觀更能建議物件的圓角表面。 

**圓錐反射醒目**提示頁面中的繪圖程式碼與星形**反射反白顯示**頁面相同，但著色器除外：

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

這兩個圓形的中心是 `offCenter` 和 `center` 。 以為中心的圓形 `center` 與包含整個球的半徑相關聯，但以為中心的圓形 `offCenter` 只有一個圖元的半徑。 漸層有效地從該點開始，並在球的邊緣結束。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
