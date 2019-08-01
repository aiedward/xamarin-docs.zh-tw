---
title: SkiaSharp 循環的漸層
description: 深入了解不同類型的圓形為基礎的漸層。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 400AE23A-6A0B-4FA8-BD6B-DE4146B04732
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: df45908af9e2d13dcaeff732e8e8b1b49c523934
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647663"
---
# <a name="the-skiasharp-circular-gradients"></a>SkiaSharp 循環的漸層

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[ `SKShader` ](xref:SkiaSharp.SKShader)類別定義來建立漸層的四種不同類型的靜態方法。 [ **SkiaSharp 線性漸層**](linear-gradient.md)文章討論[ `CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*)方法。 本文章涵蓋其他三種類型的漸層，全部都根據圓形。

[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient*)方法會建立從圓形的中心發源漸層：

![放射狀漸層停駐的範例](circular-gradients-images/RadialGradientSample.png)

[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient*)方法會建立在周圍的圓形中心的掃掠的漸層：

![掃掠漸層停駐範例](circular-gradients-images/SweepGradientSample.png)

漸層的第三個類型是很正常的。 它會呼叫兩個點的模式漸層，並由此[ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient*)方法。 從一個圓形的漸層是到另一個延伸：

![模式的漸層停駐範例](circular-gradients-images/ConicalGradientSample.png)

如果兩個圓形是不同的大小，然後漸層的形式圓錐圖。

這篇文章將探討這些詳細的漸層。

## <a name="the-radial-gradient"></a>放射狀漸層

[ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有下列語法：

```csharp
public static SKShader CreateRadialGradient (SKPoint center, 
                                             Single radius, 
                                             SKColor[] colors, 
                                             Single[] colorPos, 
                                             SKShaderTileMode mode)
```

A [ `CreateRadialGradient` ](xref:SkiaSharp.SKShader.CreateRadialGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))多載也會包含轉換矩陣參數。

前兩個引數指定圓形的半徑 center。 漸層開始該中心，並且針對向外擴充`radius`像素為單位。 超過怎麼辦`radius`而定[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)引數。 `colors`參數是陣列的兩個或多個色彩 （如同線性漸層停駐方法），和`colorPos`是介於 0 到 1 的整數的陣列。 這些整數表示的色彩，沿著的相對位置`radius`列。 您可以將該引數設定為`null`以同樣的空間色彩。

如果您使用`CreateRadialGradient`填滿的圓形，您可以設定漸層中心的圓圈，中心和圓形的半徑漸層的半徑。 在此情況下，`SKShaderTileMode`引數具有不會影響呈現之漸層。 但是，如果大於圓形定義漸層的漸層填滿的區域則`SKShaderTileMode`引數具有深遠影響圓圈外發生的事件。

效果`SKShaderMode`所示**放射狀漸層**頁面[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例。 此頁面的 XAML 檔案會具現化`Picker`，可讓您選取其中一個的三個成員`SKShaderTileMode`列舉型別：

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

程式碼後置檔案色彩整個畫布使用放射狀漸層。 漸層中心設為中心的畫布，radius 設定為 100 像素為單位。 漸層是由兩個色彩，黑白所組成：

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

此程式碼會建立具有黑色在中心，成白色 100 像素中心漸逐漸淡漸層。 超過該 radius 的情況取決於`SKShaderTileMode`引數：

[![放射狀漸層](circular-gradients-images/RadialGradient.png "放射狀漸層")](circular-gradients-images/RadialGradient-Large.png#lightbox)

在所有三個情況下，漸層填滿畫布。 在左側的 [iOS] 畫面上超過 radius 漸層會繼續進行最後的色彩為白色。 所產生的`SKShaderTileMode.Clamp`。 [Android] 畫面會顯示下列`SKShaderTileMode.Repeat`各項的效果:從中央起的100圖元開始, 漸層會以第一個色彩 (也就是黑色) 重新開始。 漸層會重複半徑的每個 100 像素。 

在右側顯示的 [通用 Windows 平台] 畫面如何`SKShaderTileMode.Mirror`造成的漸層來替代指示。 第一個漸層是從中央黑色到白色，在 100 像素為單位的半徑。 下一步是從 100 像素 radius 為黑色 200 像素半徑，在白色與下一個漸層會反轉一次。

放射狀漸層中，您可以使用兩個以上的色彩。 **彩虹弧線漸層停駐**範例會建立對應至彩虹和紅色，做為結尾的色彩的八種色彩的陣列以及八個位置值的陣列：

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

假設寬度的最小值，並在畫布上的高度為 1000，這表示`rainbowWidth`值為 250。 `outerRadius`和`innerRadius`值都會設為 1000年和 750，分別。 這些值可用來計算`positions`陣列; 0.75f 到 1 的八個的值範圍內。 `radius`值會用於繪製圓形。 875 250 像素筆觸粗細擴充 750 個像素的半徑和 1000年像素為單位的半徑之間的表示值：

[![Rainbow 弧線漸層](circular-gradients-images/RainbowArcGradient.png "彩虹弧線漸層")](circular-gradients-images/RainbowArcGradient-Large.png#lightbox)

如果您這個漸層填滿整個畫布，您會看到它是紅色內半徑內。 這是因為`positions`陣列不會從 0 開始。 第一個色彩用於 0 到第一個陣列值的位移。 漸層也是紅色超過外部 radius。 所產生的`Clamp`並排顯示模式。 使用之漸層繪製粗線，因為這些紅色區域看不到。

## <a name="radial-gradients-for-masking"></a>進行遮罩處理的放射狀漸層

線形漸層，例如放射狀漸層可以將透明或部分透明的色彩。 這項功能可用於這個程序稱為_遮罩_，這會隱藏強調另一個組件映像的映像的一部分。

**放射狀漸層停駐的遮罩**頁面會顯示範例。 在程式載入資源點陣圖的其中一個。 `CENTER`和`RADIUS`欄位所決定的點陣圖檢查，並參考應該會反白顯示的區域。 `PaintSurface`處理常式中一開始會計算要顯示的點陣圖矩形，然後顯示該矩形中：

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

後繪製點陣圖，一些簡單的程式碼會轉換`CENTER`並`RADIUS`要`center`和`radius`，指縮放及顯示移位的點陣圖中醒目提示的區域。 這些值會用來建立具有該中心和 radius 的放射狀漸層。 兩個色彩開始透明 center，以及第一個 60%的半徑。 漸層接著會逐漸淡化為白色：

[![放射狀漸層停駐的遮罩](circular-gradients-images/RadialGradientMask.png "放射狀漸層停駐的遮罩")](circular-gradients-images/RadialGradientMask-Large.png#lightbox)

這個方法並不加上遮罩點陣圖的最佳方式。 問題在於遮罩大多具有白色，選擇用來比對畫布的背景色彩。 如果背景是某些其他色彩&mdash;或 可能的漸層本身&mdash;它不會相符。 更好的方法，來遮罩所示的發行項[SkiaSharp Porter Duff 混合模式](../blend-modes/porter-duff.md)。

## <a name="radial-gradients-for-specular-highlights"></a>放射狀漸層的反射反白顯示

當光線會發生捨入的介面時，它反映出許多方向的光線，但部分的燈號彈出直接在檢視器的眼睛。 這通常稱為介面上建立模糊的白色區域的外觀_反射反白顯示_。

在 3d 圖形中，反射反白顯示通常是因為用來判斷淺的路徑和網底的演算法。 在二維圖形中，反射亮部會有時會新增至建議的 3D 曲面的外觀。 反射反白顯示可以轉換圓角的紅色圓球的單層紅色圓圈。

**星形反射反白顯示**頁面要精確地說，使用放射狀漸層。 `PaintSurface`處理常式就藉由計算的圓形，以及兩個半徑`SKPoint`值&mdash;`center`和`offCenter`是偶數中心點，圓形的左上角邊緣：

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

`CreateRadialGradient`呼叫會建立漸層開頭看`offCenter`具有白色、 紅色在一半的半徑距離的結束點。 看起來如下：

[![星形的反射亮](circular-gradients-images/RadialSpecularHighlight.png "星形反射反白顯示")](circular-gradients-images/RadialSpecularHighlight-Large.png#lightbox)

如果您仔細查看這個漸層中，您可能會決定它有瑕疵。 漸層的重心是特定的點，並可能會希望能減少對稱以反映捨入的介面。 在此情況下，您可能會偏好反射反白顯示，如下一節所示[**反射反白顯示的漸層模式**](#conical-gradients-for-specular-highlights)。

## <a name="the-sweep-gradient"></a>掃掠漸層

[ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[]))方法具有漸層建立的所有方法的最簡單的語法：

```csharp
public static SKShader CreateSweepGradient (SKPoint center, 
                                            SKColor[] colors, 
                                            Single[] colorPos)
```

它是只在中心色彩的陣列，色彩的位置。 漸層的中心點的右邊開始，和掃掠 360 度順時針旋轉中心周圍。 請注意，有沒有`SKShaderTileMode`參數。

A [ `CreateSweepGradient` ](xref:SkiaSharp.SKShader.CreateSweepGradient(SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKMatrix))矩陣轉換參數多載也會提供。 若要變更的起始點的漸層，您可以套用的旋轉變形。 您也可以套用到以逆時針方向，順時針方向從變更方向縮放轉換。

**掃掠漸層停駐**頁面用以掃掠漸層色彩的圓形，筆劃寬度為 50 個像素：

[![漸層的掃掠](circular-gradients-images/SweepGradient.png "掃掠漸層")](circular-gradients-images/SweepGradient-Large.png#lightbox)

`SweepGradientPage`類別會定義具有不同的色調值八種色彩的陣列。 請注意陣列開始，並結束 red （0 或 360 的色調值），它會顯示在最右邊的螢幕擷取畫面中：

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

程式也會實作`TapGestureRecognizer`，可讓一些程式碼的結尾`PaintSurface`處理常式。 此程式碼會使用相同的漸層填滿畫布：

[![掃掠漸層停駐全文](circular-gradients-images/SweepGradientFull.png "掃掠漸層停駐的全文")](circular-gradients-images/SweepGradientFull-Large.png#lightbox)

這些螢幕擷取畫面示範，漸層填滿任何色彩為它。 如果漸層尚未開始的相同色彩的結尾，會有連續的中心點的右邊。

## <a name="the-two-point-conical-gradient"></a>兩個點的模式漸層

[ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode))方法具有下列語法：

```csharp
public static SKShader CreateTwoPointConicalGradient (SKPoint startCenter, 
                                                      Single startRadius, 
                                                      SKPoint endCenter, 
                                                      Single endRadius, 
                                                      SKColor[] colors, 
                                                      Single[] colorPos, 
                                                      SKShaderTileMode mode)
```

參數開頭為中心點和稱為的兩個圓形的半徑_開始_圓形並_結束_圓形。 其餘的三個參數都相同`CreateLinearGradient`和`CreateRadialGradient`。 A [ `CreateTwoPointConicalGradient` ](xref:SkiaSharp.SKShader.CreateTwoPointConicalGradient(SkiaSharp.SKPoint,System.Single,SkiaSharp.SKPoint,System.Single,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))多載包含矩陣轉換。

漸層開始入門圓形，並結束圓形當做結尾。 `SKShaderTileMode`參數會控管會怎樣超過兩個圓形。 兩個點的模式漸層是唯一不完全填滿區域的漸層。 如果兩個圓形的半徑相同，漸層僅限於具有等同於圓形的直徑寬度的矩形。 如果兩個圓形會有不同的半徑，漸層會形成圓錐式。

很可能您會想要試驗之兩個點的模式漸層，因此**漸層模式**分頁衍生自`InteractivePage`允許兩個觸控點移動的兩個圓形的半徑：

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

程式碼後置檔案會定義兩個`TouchPoint`物件具有固定的 50 到 100 的半徑：

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

`colors`陣列是紅色、 綠色和藍色。 底端的程式碼`PaintSurface`處理常式繪製兩個觸控點，因為黑色圓形，讓它們不妨礙漸層。

請注意，`DrawRect`呼叫使用之漸層色彩整個畫布。 在一般的情況下，不過，大部分的畫布仍會維持周圍的漸層，未上色。 以下是顯示三個可能的組態程式：

[![模式的漸層](circular-gradients-images/ConicalGradient.png "圓錐漸層")](circular-gradients-images/ConicalGradient-Large.png#lightbox)

在左側的 [iOS] 畫面會顯示的效果`SKShaderTileMode`設定`Clamp`。 漸層開始的邊緣為相反側邊為第二個圓形最接近的小圓圈內的紅色。 `Clamp`值也會讓繼續的點 cone 紅色。 漸層結束以藍色邊緣外部的較大圓形最接近的第一個圓圈，但會繼續以藍色的圓圈中及更新版本。

Android 的畫面很類似，但`SKShaderTileMode`的`Repeat`。 現在就更清楚的漸層的第一個圓圈內開始和結束第二個圓圈外。 `Repeat`設定會導致重複一次以紅色圓圈內的較大的漸層。

UWP 畫面會顯示較小的圓形移完全的較大的圓圈內時，會發生什麼事。 漸層不再做圓錐式，並改為填滿整個區域。 效果類似於放射狀漸層中，但如果較小的圓形不完全置於較大的圓形，它是不對稱。

當一個圓圈巢狀方式置於另一個，但卻很適合的反射反白顯示，您可能會懷疑漸層的實際效益。

## <a name="conical-gradients-for-specular-highlights"></a>反射反白顯示的漸層模式

稍早在本文中，您會看到如何使用放射狀漸層建立的反射反白顯示。 您也可以基於此目的，使用兩個點的模式漸層，您可能會偏好它的樣子：

[![模式的反射亮](circular-gradients-images/ConicalSpecularHighlight.png "圓錐反射反白顯示")](circular-gradients-images/ConicalSpecularHighlight-Large.png#lightbox)

不對稱的外觀更建議物件的捨入的表面。 

中的繪圖程式碼**圓錐反射反白顯示**頁面是相同**星形反射反白顯示**除了著色器的頁面：

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

兩個圓形必須的中心`offCenter`和`center`。 在置中的圓形`center`相關聯，包含整個球，但位於圓形的半徑`offCenter`一個半徑為一個像素。 漸層有效地在該點開始和結束邊緣的球。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
