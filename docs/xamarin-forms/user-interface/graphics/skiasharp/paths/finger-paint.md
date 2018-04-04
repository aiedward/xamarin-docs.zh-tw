---
title: 手指繪製
description: 您可以使用手指在畫布上繪製。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: charlespetzold
ms.author: chape
ms.date: 04/05/2017
ms.openlocfilehash: 9f7e3f64122f3b95291973d032aaf507dfbc8fab
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="finger-painting"></a>手指繪製

_您可以使用手指在畫布上繪製。_

`SKPath`物件可以持續更新並顯示。 這項功能可讓用於互動式繪圖，例如 finger-painting 程式中的路徑。

![](finger-paint-images/fingerpaintsample.png "在手指繪製練習")

觸控支援，在 Xamarin.Forms 中的不允許追蹤個別隻手指放在畫面上，，因此 Xamarin.Forms 觸控追蹤效果是開發來提供額外的觸控支援。 文件中說明這種效果[**叫用事件效果**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。 範例程式[**觸控追蹤效果示範**](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)包含使用 SkiaSharp，包括 finger-painting 程式的兩個頁面。

[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)解決方案包含此觸控追蹤事件。 可攜式類別庫專案會包含`TouchEffect`類別`TouchActionType`列舉型別，`TouchActionEventHandler`委派，而`TouchActionEventArgs`類別。 每個平台的專案包含`TouchEffect`類別適用於該平台，則 iOS 專案也包含`TouchRecognizer`類別。

**手指小畫家**頁面**SkiaSharpFormsDemos**是指繪製的簡單的實作。 不允許選取色彩或繪製寬度，它沒有方法可以清除畫布，而且當然您無法儲存您的作品。

[ **FingerPaintPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml)檔案置於`SKCanvasView`中單一儲存格`Grid`並附加`TouchEffect`的`Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Paths.FingerPaintPage"
             Title="Finger Paint">

    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

附加`TouchEffect`直接`SKCanvasView`不會在所有平台運作。

[ **FingerPaintPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FingerPaintPage.xaml.cs)程式碼後置檔案會定義兩個集合，用於儲存`SKPath`物件，以及`SKPaint`轉譯這些路徑的物件：

```csharp
public partial class FingerPaintPage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };

    public FingerPaintPage()
    {
        InitializeComponent();
    }
    ...
}
```

顧名思義，`inProgressPaths`字典會儲存一或多個手指目前所繪製的路徑。 字典索引鍵是附觸控事件的觸控式識別碼。 `completedPaths`欄位是已完成時手指繪製路徑消除從螢幕的路徑的集合。

`TouchAction`處理常式來管理這兩個集合。 第一次手指碰觸的畫面上，新`SKPath`加入至`inProgressPaths`。 該手指移動時，要新增其他的點的路徑。 在手指發行時，路徑會轉移至`completedPaths`集合。 您可以同時繪製多個手指。 為其中一個路徑或集合，每次變更後`SKCanvasView`無效：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                           (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }
}
```

隨附的觸控追蹤事件的點是 Xamarin.Forms 座標。這些必須轉換為 SkiaSharp 座標，也就是像素為單位。 目的，是`ConvertToPixel`方法。

`PaintSurface`處理常式會接著只要轉譯這兩個集合的路徑。 稍早的完整的路徑會出現在進行中的路徑底下：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ,,,
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (SKPath path in completedPaths)
        {
            canvas.DrawPath(path, paint);
        }

        foreach (SKPath path in inProgressPaths.Values)
        {
            canvas.DrawPath(path, paint);
        }
    }
    ...
}
```

您的手指圖畫只會受到您 talent:

[![](finger-paint-images/fingerpaint-small.png "手指 [小畫家] 頁面的三個螢幕擷取畫面")](finger-paint-images/fingerpaint-large.png#lightbox "手指 [小畫家] 頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [觸控追蹤效果示範 （範例）](https://developer.xamarin.com/samples/xamarin-forms/Effects/TouchTrackingEffectDemos/)
- [叫用事件的效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
