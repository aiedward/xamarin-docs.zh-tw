---
title: "基本動畫"
description: "了解如何製作動畫 SkiaSharp 圖形"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 31C96FD6-07E4-4473-A551-24753A5118C3
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: e18ef7c77c1d5c93d53882233c65a56e5567fcd7
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="basic-animation"></a>基本動畫

_了解如何製作動畫 SkiaSharp 圖形_

您可以依據造成 SkiaSharp 圖形，在 Xamarin.Forms`PaintSurface`方法呼叫頻率很高，每當稍有不同繪製圖形。 以下是動畫同心圓看似展開從中心與本文稍後所示：

![](animation-images/animationexample.png "數個同心圓看似展開從中心")

**Pulsating 橢圓形**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)程式繪製橢圓形的兩個軸，使其顯示來 pulsating，而且您甚至可以控制這個 pulsation 速率：


[ **PulsatingEllipsePage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/PulsatingEllipsePage.xaml)檔案會具現化 Xamarin.Forms`Slider`和`Label`來顯示目前滑桿的值。 這是整合的常見方式`SKCanvasView`與其他 Xamarin.Forms 檢視：

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

程式碼後置檔案會具現化`Stopwatch`物件做為高有效位數時鐘。 `OnAppearing`覆寫設定`pageIsActive`欄位設為`true`，並呼叫方法，名為`AnimationLoop`。 `OnDisappearing`覆寫設定的`pageIsActive`欄位設為`false`:

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

`AnimationLoop`方法啟動`Stopwatch`，然後迴圈時`pageIsActive`是`true`。 這是基本上是 「 無限迴圈 」 頁面為作用中，但是它並不會造成程式停止回應，因為迴圈結束時，呼叫`Task.Delay`與`await`運算子，可讓程式函式的其他部分。 引數`Task.Delay`導致其完成之後 1/30 秒。 這會定義動畫的畫面播放速率。

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

`while`迴圈一開始會取得從循環時間`Slider`。 這是以秒為單位，例如，5 的時間。 第二個陳述式會計算值為`t`如*時間*。 如`cycleTime`5，`t`會增加從 0 到 1 每隔 5 秒。 引數`Math.Sin`函式中第二個陳述式的範圍從 0 到 2 π 每隔 5 秒。 `Math.Sin`函式傳回值，這個值為 0，然後以 1 & #x 2013年，則為 1 和 0 每 5 秒，但速度更慢變更時的值是 1 或-1 的值範圍從 0。 值 1 加入的值永遠為正數，因此然後它會除以 2，因此值範圍是從 ½ 設為 1，0 到 ½，但大約 1 和 0 值時，速度較慢的 ½。 這存放在`scale` 欄位中，而`SKCanvasView`失效。

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

方法會計算最大的 radius 根據顯示區域大小，以及根據最大的 radius 最小 radius。 `scale`值以動畫方式介於 0 和 1 之間，再回到 0，因此方法所使用的是，用來計算`xRadius`和`yRadius`的範圍之間`minRadius`和`maxRadius`。 若要繪製填滿橢圓形會使用這些值：

[![](animation-images/pulsatingellipse-small.png "震動橢圓形頁面的三個螢幕擷取畫面")](animation-images/pulsatingellipse-large.png#lightbox "震動橢圓形頁面的三個螢幕擷取畫面")

請注意，`SKPaint`中建立物件`using`區塊。 例如許多 SkiaSharp 類別`SKPaint`衍生自`SKObject`，其衍生自`SKNativeObject`，它會實作[ `IDisposable` ](https://developer.xamarin.com/api/type/System.IDisposable/)介面。 `SKPaint` 覆寫`Dispose`方法來釋放 unmanaged 的資源。

 放置`SKPaint`中`using`區塊可確保`Dispose`呼叫釋放這些 unmanaged 的資源的區塊的結尾。 發生這種情況還是時所使用的記憶體`SKPaint`物件將會釋出.NET 記憶體回收行程，但是在動畫程式碼中，所以最好先稍微積極方式更有條理地釋放記憶體。

 在此特定情況下更好的解決方案，可建立兩個`SKPaint`物件一次，並將它們儲存為欄位。

這就是**展開圓形**動畫沒有。 [ `ExpandingCirclesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/skia-sharp-forms/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/ExpandingCirclesPage.cs)類別開始會定義數個欄位，包括`SKPaint`物件：

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

此程式會使用不同的方法來根據 Xamarin.Forms 動畫`Device.StartTimer`。 `t`欄位以動畫方式從 0 到 1 每`cycleTime`（毫秒):

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

`PaintSurface`處理常式繪製 5 同心圓，具有動畫的半徑。 如果`baseRadius`變數的計算方式為 100，然後為`t`會從 0 動畫為 1，5 的圓形增加從 0 到 100、 100 到 200、 200 至 300、 400 到 300 和 400 到 500 的半徑。 適用於大部分的圓形`strokeWidth`是 50，但第一個圓形`strokeWidth`繪製從 0 到 50。 適用於大部分的圓形，顏色是藍色，但最後的圓形，色彩由藍色動畫為透明：

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

結果是影像看起來相同時`t`等於 0 時為`t`等於 1，而圓形似乎繼續永遠展開：

[![](animation-images/expandingcircles-small.png "展開的圓形呈現頁面的三個螢幕擷取畫面")](animation-images/expandingcircles-large.png#lightbox "展開圓形頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
