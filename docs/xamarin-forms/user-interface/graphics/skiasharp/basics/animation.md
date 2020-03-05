---
title: SkiaSharp 中的基本動畫
description: 本文說明如何在 Xamarin. Forms 應用程式中建立 SkiaSharp 圖形的動畫，並以範例程式碼示範這項功能。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 80de16a0cf9b601ac3795085b638b9d62812f4d9
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291565"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本動畫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索如何建立 SkiaSharp 圖形的動畫_

您可以藉由定期呼叫 `PaintSurface` 方法，以在 Xamarin 中建立 SkiaSharp 圖形的動畫，每次繪製圖形的方式稍有不同。 以下是本文稍後所示的動畫，其中的同心圓形看起來從中央擴展：

![](animation-images/animationexample.png "Several concentric circles seemingly expanding from the center")

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中的 [ **Pulsating 橢圓形**] 頁面會以動畫顯示橢圓形的兩個軸，使其看似 Pulsating，而且您甚至可以控制此 pulsation 的速率。 [**PulsatingEllipsePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)會具現化 Xamarin. Forms `Slider` 和 `Label` 以顯示滑杆的目前值。 這是整合 `SKCanvasView` 與其他 Xamarin 的常見方式。表單檢視：

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

程式碼後置檔案會具現化 `Stopwatch` 物件，以作為高精確度的時鐘。 `OnAppearing` 覆寫會將 `pageIsActive` 欄位設定為 `true` 並呼叫名為 `AnimationLoop`的方法。 `OnDisappearing` 覆寫會將 `pageIsActive` 欄位設定為 `false`：

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

`AnimationLoop` 方法會在 `true``pageIsActive` 時，啟動 `Stopwatch` 然後迴圈。 這基本上是「無限迴圈」，而頁面處於作用中狀態，但它不會造成程式停止回應，因為迴圈會以 `await` 運算子的 `Task.Delay` 呼叫來結束，讓程式功能的其他部分。 `Task.Delay` 的引數會使其在 1/30 秒後完成。 這會定義動畫的畫面播放速率。

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

`while` 迴圈一開始會從 `Slider`取得週期時間。 這是時間（以秒為單位），例如5。 第二個語句會計算*time*的 `t` 值。 若 `cycleTime` 5，`t` 每5秒從0增加到1。 第二個語句中 `Math.Sin` 函數的引數，範圍是從0到2π，每5秒一次。 `Math.Sin` 函式會傳回0到1之間的值，並返回0，然後每隔5秒 &ndash;1 和0，但如果值接近1或-1，則值會變得更慢。 已加入值1，因此值一律為正數，然後除以2，因此值的範圍是從1/2 到1到1/2 到0到1/2，但當值介於1到0時，則會較慢。 這會儲存在 [`scale`] 欄位中，而 `SKCanvasView` 會無效。

`PaintSurface` 方法會使用此 `scale` 值來計算橢圓形的兩個軸：

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

方法會根據顯示區域的大小，以及根據最大半徑的最小半徑來計算最大半徑。 `scale` 值會在0和1之間產生動畫，並回到0，讓方法用來計算 `xRadius`，並 `yRadius` `minRadius` 和 `maxRadius`之間的範圍。 這些值用來繪製和填滿橢圓形：

[![](animation-images/pulsatingellipse-small.png "Triple screenshot of the Pulsating Ellipse page")](animation-images/pulsatingellipse-large.png#lightbox "Triple screenshot of the Pulsating Ellipse page")

請注意，`SKPaint` 物件是在 `using` 區塊中建立的。 就像許多 SkiaSharp 類別，`SKPaint` 衍生自 `SKObject`，而後者衍生自 `SKNativeObject`，而這會執行[`IDisposable`](xref:System.IDisposable)介面。 `SKPaint` 會覆寫 `Dispose` 方法以釋放非受控資源。

 將 `SKPaint` 放在 `using` 區塊中可確保在區塊結尾呼叫 `Dispose`，以釋放這些非受控資源。 當由 .NET 垃圾收集行程釋放 `SKPaint` 物件所使用的記憶體時，仍會發生這種情況，但在動畫程式碼中，最好是以更有條理的方式主動釋放記憶體。

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

此程式會根據 `Device.StartTimer` 方法，使用不同的動畫方式來進行動畫。 [`t`] 欄位會每隔 `cycleTime` 毫秒，從0到1的動畫：

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

`PaintSurface` 處理常式會以動畫半徑繪製五個同心圓圓形。 如果 `baseRadius` 變數是以100計算，則當 `t` 從0動畫顯示為1時，五個圓形的半徑會從0增加到100、100到200、200到300、300到400和400到500。 對於大部分的圓而言，`strokeWidth` 為50，但在第一個圓形中，`strokeWidth` 會從0到50繪製動畫。 對於大部分的圓形而言，色彩為藍色，但在最後一個圓形中，色彩會從藍色動畫顯示為透明。 請注意指定不透明度之 `SKColor` 的第四個引數：

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

結果就是當 `t` 等於0時，影像看起來會像 `t` 等於1，而圓圈似乎持續不斷地展開：

[![](animation-images/expandingcircles-small.png "Triple screenshot of the Expanding Circles page")](animation-images/expandingcircles-large.png#lightbox "Triple screenshot of the Expanding Circles page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
