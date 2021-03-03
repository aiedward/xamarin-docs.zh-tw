---
title: SkiaSharp 中的基本動畫
description: 本文說明如何在應用程式中建立 SkiaSharp 圖形的動畫 Xamarin.Forms ，並使用範例程式碼示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 92631f66f729e2d1519bf577accb6f1ce9c1e1b5
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373584"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本動畫

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索如何製作 SkiaSharp 圖形的動畫_

您可以在中建立 SkiaSharp 圖形的動畫 Xamarin.Forms ，因為這會讓 `PaintSurface` 方法定期呼叫，每次繪製圖形的方式稍有不同。 本文稍後會顯示一則動畫，其中含有看似從中央展開的同心圓：

![許多看似從中央擴充的同心圓圓形](animation-images/animationexample.png)

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中的 [ **Pulsating 橢圓形**] 頁面會將橢圓形的兩個軸動畫顯示出來，使其看似 Pulsating，而且您甚至可以控制此 pulsation 的速率。 [**PulsatingEllipsePage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)可具現化和， Xamarin.Forms `Slider` `Label` 以顯示滑杆目前的值。 這是整合 `SKCanvasView` 與其他視圖的常見方式 Xamarin.Forms ：

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

程式碼後端檔案會將物件具現化 `Stopwatch` ，以做為高精確度的時鐘。 覆 `OnAppearing` 寫會將 `pageIsActive` 欄位設定為 `true` ，並呼叫名為的方法 `AnimationLoop` 。 覆 `OnDisappearing` 寫會將該 `pageIsActive` 欄位設定為 `false` ：

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

`AnimationLoop` `Stopwatch` 當為時，方法會啟動和 `pageIsActive` 迴圈 `true` 。 這基本上是「無限迴圈」，頁面為使用中狀態，但不會造成程式停止回應，因為迴圈結束時，會使用運算子的呼叫來 `Task.Delay` `await` 讓程式函式的其他部分。 的引數 `Task.Delay` 會在 1/30 秒之後完成。 這會定義動畫的畫面播放速率。

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

`while`迴圈一開始會從取得迴圈時間 `Slider` 。 這是以秒為單位的時間，例如，5。 第二個語句會計算 `t` *時間* 的值。 若為 `cycleTime` 5， `t` 每隔5秒就會從0增加為1。 第二個語句中的函式引數的 `Math.Sin` 範圍是從0到2π每5秒。 此函式會 `Math.Sin` 傳回值，範圍從0到1，再到 1 &ndash; 和0（每5秒），但值若接近1或-1 則會變得更慢。 系統會加入值1，因此值一律為正數，然後除以2，因此值的範圍從1/2 到1到1/2 到0到1/2，但當值的範圍介於1和0時較慢。 這會儲存在 `scale` 欄位中，而且 `SKCanvasView` 會失效。

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

方法會根據顯示區域的大小，以及根據最大半徑的最小半徑來計算最大半徑。 `scale`值的動畫是介於0和1之間，然後回到0，因此方法會使用它來計算 `xRadius` 和 `yRadius` 之間的範圍 `minRadius` `maxRadius` 。 這些值用來繪製和填滿橢圓形：

[![Pulsating 橢圓形頁面的三重螢幕擷取畫面](animation-images/pulsatingellipse-small.png)](animation-images/pulsatingellipse-large.png#lightbox "Pulsating 橢圓形頁面的三重螢幕擷取畫面")

請注意， `SKPaint` 物件是在區塊中建立的 `using` 。 就像許多 SkiaSharp 類別都是衍生自，它會實作為 `SKPaint` `SKObject` `SKNativeObject` [`IDisposable`](xref:System.IDisposable) 介面。 `SKPaint` 覆寫 `Dispose` 方法以釋放非受控資源。

 放 `SKPaint` 入 `using` 區塊可確保 `Dispose` 在區塊結尾呼叫，以釋出這些未受管理的資源。 當 `SKPaint` .net 垃圾收集行程釋放物件所使用的記憶體，但在動畫程式碼中，最好是以更有條理的方式釋出記憶體。

 在此特定案例中，更好的解決方案是建立兩個 `SKPaint` 物件一次，並將它們儲存為欄位。

這就是 **展開的圓形** 動畫的作用。 [`ExpandingCirclesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)類別一開始會定義數個欄位，包括 `SKPaint` 物件：

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

此程式會根據方法使用不同的動畫方法 Xamarin.Forms `Device.StartTimer` 。 此 `t` 欄位的動畫會每毫秒從0到 1 `cycleTime` ：

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

`PaintSurface`處理常式會使用動畫半徑來繪製五個同心圓。 如果將 `baseRadius` 變數計算為100，則 `t` 從0到1的動畫，五個圓形的半徑會從0增加至100、100至200、200至300、300至400，以及400至500。 大部分的圓形 `strokeWidth` 都是50，但第一個圓形的 `strokeWidth` 動畫是從0到50的動畫。 對於大部分的圓形來說，色彩是藍色，但在最後一個圓形中，色彩是從藍色到透明的動畫。 請注意指定不透明度的函式的第四個引數 `SKColor` ：

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

結果是當等於0時，影像看起來相同 `t` ，因為當 `t` 等於1時，圓形似乎仍繼續持續擴充：

[![展開的圓形頁面的三重螢幕擷取畫面](animation-images/expandingcircles-small.png)](animation-images/expandingcircles-large.png#lightbox "展開的圓形頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)