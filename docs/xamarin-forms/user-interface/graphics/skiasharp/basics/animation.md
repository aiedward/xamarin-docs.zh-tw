---
title: ''
description: 本文說明如何在應用程式中以動畫顯示 SkiaSharp 圖形 Xamarin.Forms ，並使用範例程式碼示範這項功能。
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a59f65655772768860ce29128f14a48641abc26
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84134273"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本動畫

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索如何建立 SkiaSharp 圖形的動畫_

您可以在中建立 SkiaSharp 圖形的動畫，其方式是 Xamarin.Forms `PaintSurface` 定期呼叫方法，每次繪製圖形的方式稍有不同。 以下是本文稍後所示的動畫，其中的同心圓形看起來從中央擴展：

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中的 [ **Pulsating 橢圓形**] 頁面會以動畫顯示橢圓形的兩個軸，使其看似 Pulsating，而且您甚至可以控制此 pulsation 的速率。 [**PulsatingEllipsePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)會具現化和， Xamarin.Forms `Slider` `Label` 以顯示滑杆的目前值。 這是整合 `SKCanvasView` 與其他視圖的常見方法 Xamarin.Forms ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.PulsatingEllipsePage"
             Title="Pulsating Ellipse">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Slider x:Name="slider"
                Grid.Row="0"
                Maximum="10"
                Minimum="0.1"
                Value="5"
                Margin="20, 0" />

        <Label Grid.Row="1"
               Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Cycle time = {0:F1} seconds'}"
               HorizontalTextAlignment="Center" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

程式碼後置檔案會將物件具現化 `Stopwatch` ，以作為高精確度的時鐘。 覆 `OnAppearing` 寫會將 `pageIsActive` 欄位設定為 `true` ，並呼叫名為的方法 `AnimationLoop` 。 覆 `OnDisappearing` 寫會將該 `pageIsActive` 欄位設定為 `false` ：

```csharp
Stopwatch stopwatch = new Stopwatch();
bool pageIsActive;
float scale;            // ranges from 0 to 1 to 0

public PulsatingEllipsePage()
{
    InitializeComponent();
}

protected override void OnAppearing()
{
    base.OnAppearing();
    pageIsActive = true;
    AnimationLoop();
}

protected override void OnDisappearing()
{
    base.OnDisappearing();
    pageIsActive = false;
}
```

`AnimationLoop`當為時，方法 `Stopwatch` 會啟動，然後再迴圈 `pageIsActive` `true` 。 這基本上是「無限迴圈」，而頁面處於作用中狀態，但它不會造成程式停止回應，因為迴圈會以運算子呼叫 `Task.Delay` `await` ，這可讓程式功能的其他部分繼續進行。 的引數 `Task.Delay` 會使其在 1/30 秒後完成。 這會定義動畫的畫面播放速率。

```csharp
async Task AnimationLoop()
{
    stopwatch.Start();

    while (pageIsActive)
    {
        double cycleTime = slider.Value;
        double t = stopwatch.Elapsed.TotalSeconds % cycleTime / cycleTime;
        scale = (1 + (float)Math.Sin(2 * Math.PI * t)) / 2;
        canvasView.InvalidateSurface();
        await Task.Delay(TimeSpan.FromSeconds(1.0 / 30));
    }

    stopwatch.Stop();
}

```

`while`迴圈一開始會從取得週期時間 `Slider` 。 這是時間（以秒為單位），例如5。 第二個語句會計算 `t` for *time*的值。 若為 `cycleTime` 5， `t` 每5秒從0增加到1。 第二個語句中的函式引數的 `Math.Sin` 範圍是從0到2π，每5秒一次。 函式 `Math.Sin` 會傳回值，範圍從0到1，回到0，然後 &ndash; 每隔5秒為1和0，但如果值接近1或-1，則值會變得更慢。 已加入值1，因此值一律為正數，然後除以2，因此值的範圍是從1/2 到1到1/2 到0到1/2，但當值介於1到0時，則會較慢。 這會儲存在 `scale` 欄位中，而且 `SKCanvasView` 會無效。

`PaintSurface`方法會使用此 `scale` 值來計算橢圓形的兩個軸：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float maxRadius = 0.75f * Math.Min(info.Width, info.Height) / 2;
    float minRadius = 0.25f * maxRadius;

    float xRadius = minRadius * scale + maxRadius * (1 - scale);
    float yRadius = maxRadius * scale + minRadius * (1 - scale);

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.Color = SKColors.Blue;
        paint.StrokeWidth = 50;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);

        paint.Style = SKPaintStyle.Fill;
        paint.Color = SKColors.SkyBlue;
        canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
    }
}
```

方法會根據顯示區域的大小，以及根據最大半徑的最小半徑來計算最大半徑。 `scale`值會在0和1之間產生動畫，並回到0，因此方法會使用它來計算 `xRadius` 和 `yRadius` ，範圍介於 `minRadius` 和之間 `maxRadius` 。 這些值用來繪製和填滿橢圓形：

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

請注意， `SKPaint` 物件是在區塊中建立的 `using` 。 就像許多 SkiaSharp 類別 `SKPaint` 衍生自 `SKObject` ，後者衍生自 `SKNativeObject` ，後者會執行 [`IDisposable`](xref:System.IDisposable) 介面。 `SKPaint`覆寫 `Dispose` 方法以釋放非受控資源。

 放 `SKPaint` 入 `using` 區塊可確保在 `Dispose` 區塊結尾呼叫，以釋放這些非受控資源。 當物件所使用的記憶體由 .NET 垃圾收集行程釋放時，仍會發生這種情況 `SKPaint` ，但在動畫程式碼中，最好是以更有條理的方式主動釋放記憶體。

 在此特定情況下，較好的解決方案是建立兩個 `SKPaint` 物件一次，並將它們儲存為欄位。

這就是**展開的圓形**動畫的作用。 [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)類別一開始會定義數個欄位，包括 `SKPaint` 物件：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    const double cycleTime = 1000;       // in milliseconds

    SKCanvasView canvasView;
    Stopwatch stopwatch = new Stopwatch();
    bool pageIsActive;
    float t;
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke
    };

    public ExpandingCirclesPage()
    {
        Title = "Expanding Circles";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ...
}
```

此程式會根據方法使用不同的動畫方法 Xamarin.Forms `Device.StartTimer` 。 `t`欄位的動畫是從0到1，每 `cycleTime` 毫秒：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;
        stopwatch.Start();

        Device.StartTimer(TimeSpan.FromMilliseconds(33), () =>
        {
            t = (float)(stopwatch.Elapsed.TotalMilliseconds % cycleTime / cycleTime);
            canvasView.InvalidateSurface();

            if (!pageIsActive)
            {
                stopwatch.Stop();
            }
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

此 `PaintSurface` 處理常式會以動畫半徑繪製五個同心圓圓形。 如果 `baseRadius` 變數是以100計算，然後 `t` 從0到1的動畫，則五個圓的半徑會從0到100、100到200、200到300、300到400和400到500。 對於大部分的圓而言， `strokeWidth` 是50，但在第一個圓形中，會 `strokeWidth` 從0到50繪製動畫。 對於大部分的圓形而言，色彩為藍色，但在最後一個圓形中，色彩會從藍色動畫顯示為透明。 請注意指定不透明度之函數的第四個引數 `SKColor` ：

```csharp
public class ExpandingCirclesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
        float baseRadius = Math.Min(info.Width, info.Height) / 12;

        for (int circle = 0; circle < 5; circle++)
        {
            float radius = baseRadius * (circle + t);

            paint.StrokeWidth = baseRadius / 2 * (circle == 0 ? t : 1);
            paint.Color = new SKColor(0, 0, 255,
                (byte)(255 * (circle == 4 ? (1 - t) : 1)));

            canvas.DrawCircle(center.X, center.Y, radius, paint);
        }
    }
}
```

結果就是當等於0時，影像看起來會 `t` 像是 `t` 等於1，而圓圈似乎會繼續持續擴充：

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
