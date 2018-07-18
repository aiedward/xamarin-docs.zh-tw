---
title: SkiaSharp 中的基本動畫
description: 本文說明如何以動畫顯示 SkiaSharp 圖形在 Xamarin.Forms 應用程式，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 393716e17b042224f2b0bae8c526132489af26c6
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994807"
---
# <a name="basic-animation-in-skiasharp"></a>SkiaSharp 中的基本動畫

_探索如何以動畫顯示 SkiaSharp 圖形_

您可以動畫 SkiaSharp 在 Xamarin.Forms 中的圖形顯示造成`PaintSurface`每個方法呼叫頻率很高，時間稍有不同繪製圖形。 以下是動畫看似展開從管理中心的同心圓這篇文章稍後所示：

![](animation-images/animationexample.png "數個同心圓看似擴充從中心")

**Pulsating 橢圓形**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式繪製橢圓形的兩個軸，使它看起來像是 pulsating，和您甚至可以控制此 pulsation 的速率：


[ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)檔案會具現化 Xamarin.Forms`Slider`和`Label`顯示滑桿的目前值。 這是常見的方式來整合`SKCanvasView`與其他 Xamarin.Forms 檢視：

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

程式碼後置檔案會具現化`Stopwatch`物件做為高有效位數時鐘。 `OnAppearing`設定會覆寫`pageIsActive`欄位設為`true`，並呼叫方法，名為`AnimationLoop`。 `OnDisappearing`覆寫集`pageIsActive`欄位設為`false`:

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

`AnimationLoop`方法啟動`Stopwatch`，然後在迴圈`pageIsActive`是`true`。 這是基本上是 「 無限迴圈 」，而頁面為作用中，但它不會造成程式停止回應，因為迴圈結尾呼叫`Task.Delay`與`await`運算子，可讓程式函式的其他部分。 引數`Task.Delay`導致其在 1/30 秒後完成。 這會定義動畫的畫面播放速率。

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

`while`迴圈，一開始會取得從循環時間`Slider`。 這是以秒為單位，例如，5 的時間。 第二個陳述式會計算的值`t`for*時間*。 針對`cycleTime`為 5，`t`會增加從 0 到 1 每隔 5 秒。 引數`Math.Sin`函式，在第二個陳述式的範圍從 0 到 2 π 每隔 5 秒。 `Math.Sin`函式傳回值，這個值範圍從 0 到 1 後到 0，然後到&ndash;1 和 0 每隔 5 秒，但速度變更時的值是 1 或-1 的值。 值 1 加入的值一律是正數，因此再它除以 2，因此值範圍從 ½ ½ 為 ½，但約 1 和 0 的值時，速度較慢的 0 到 1。 這會儲存在`scale`欄位中，而`SKCanvasView`失效。

`PaintSurface`方法會使用此`scale`來計算兩個橢圓形的軸的值：

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

方法會計算根據顯示區域的大小上限 radius 和最大的 radius 為基礎的最小半徑。 `scale`值以動畫顯示介於 0 和 1 之間，再回復成 0，因此這個方法所使用的是，來計算`xRadius`並`yRadius`其範圍之間`minRadius`和`maxRadius`。 這些值會用來繪製並填滿橢圓形：

[![](animation-images/pulsatingellipse-small.png "震動的省略符號 頁面的三個螢幕擷取畫面")](animation-images/pulsatingellipse-large.png#lightbox "震動的省略符號 頁面的三個螢幕擷取畫面")

請注意，`SKPaint`中建立物件`using`區塊。 和許多 SkiaSharp 類別一樣`SKPaint`衍生自`SKObject`，其衍生自`SKNativeObject`，它會實作[ `IDisposable` ](xref:System.IDisposable)介面。 `SKPaint` 覆寫`Dispose`方法來釋放 unmanaged 的資源。

 將放`SKPaint`中`using`區塊可確保`Dispose`稱為釋放這些 unmanaged 的資源區塊的結尾。 發生這種情況還是時所使用的記憶體`SKPaint`物件釋放.NET 記憶體回收行程，但在動畫的程式碼中，最好是有點主動在釋放記憶體，以更有條理地的方式。

 更好的解決方案，在這個案例會建立兩個`SKPaint`物件一次，並將它們儲存為欄位。

這正是**展開圓形**動畫沒有。 [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)類別一開始會定義數個欄位，包括`SKPaint`物件：

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

此程式會使用不同的方法來以 Xamarin.Forms 為基礎的動畫`Device.StartTimer`。 `t`欄位以動畫顯示從 0 到 1 每`cycleTime`毫秒：

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

`PaintSurface`處理常式繪製 5 個同心圓，具有動畫效果的半徑。 如果`baseRadius`變數的計算方式為 100，然後為`t`從 0 至 1，5 的圓形增加從 0 到 100、 100 到 200、 200 到 300、 300 到 400 和 400 到 500 部半徑動畫顯示。 適用於大部分的圓圈`strokeWidth`是 50，但第一個圓形`strokeWidth`會從 0 展示動畫至 50。 對於大部分的圓圈，藍色的色彩就是，但最後一個圓形，色彩會從以動畫顯示藍色為透明：

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

結果就是影像看起來相同 when`t`等於 0 時一樣，因此`t`等於 1，而圓形似乎持續不斷擴展：

[![](animation-images/expandingcircles-small.png "展開的圓形頁面的三個螢幕擷取畫面")](animation-images/expandingcircles-large.png#lightbox "展開圓形頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
