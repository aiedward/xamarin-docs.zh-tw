---
title: SkiaSharp 中的手指繪製
description: 本文說明如何使用您的手指在應用程式的 SkiaSharp 畫布上繪製 Xamarin.Forms ，並以範例程式碼示範這項功能。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 56929D74-8F2C-44C6-90E6-3FBABCDC0A4B
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61ae651a2402204f69f642235d74d8d641b47988
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84139018"
---
# <a name="finger-painting-in-skiasharp"></a>SkiaSharp 中的手指繪製

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用您的手指在畫布上繪製。_

`SKPath`物件可以持續更新及顯示。 這項功能可讓路徑用於互動式繪圖，例如在手指繪製程式中。

![](finger-paint-images/fingerpaintsample.png "An exercise in finger painting")

中的觸控支援不 Xamarin.Forms 允許追蹤螢幕上的個別手指，因此已 Xamarin.Forms 開發觸控追蹤效果以提供額外的觸控支援。 這項效果會在[**從效果叫用事件**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)一文中說明。 範例程式[**觸控追蹤效果**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)示範包含兩個使用 SkiaSharp 的頁面，包括手指繪製程式。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)解決方案包含此觸控追蹤事件。 .NET Standard 程式庫專案包含 `TouchEffect` 類別、 `TouchActionType` 列舉、 `TouchActionEventHandler` 委派和 `TouchActionEventArgs` 類別。 每個平臺專案都包含 `TouchEffect` 該平臺的類別; iOS 專案也包含一個 `TouchRecognizer` 類別。

**SkiaSharpFormsDemos**中的**手指繪製**頁面是簡單的手指繪製方式。 它不允許選取 [色彩] 或 [筆觸寬度]，也沒有任何方法可以清除畫布，當然您也無法儲存您的作品。

[**FingerPaintPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml)會將放 `SKCanvasView` 在單一儲存格中 `Grid` ，並將附加 `TouchEffect` 至該資料格 `Grid` ：

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

將 `TouchEffect` 直接附加至 `SKCanvasView` 不會在所有平臺上運作。

[**FingerPaintPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FingerPaintPage.xaml.cs)程式碼後置檔案會定義兩個用於儲存 `SKPath` 物件的集合，以及用 `SKPaint` 來呈現這些路徑的物件：

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

如其名所示， `inProgressPaths` 字典會儲存目前正由一或多個手指繪製的路徑。 字典索引鍵是觸控事件隨附的觸控識別碼。 `completedPaths`欄位是路徑的集合，當從螢幕中繪製路徑的手指已完成時。

此 `TouchAction` 處理常式會管理這兩個集合。 當手指第一次接觸畫面時，會將新的新增 `SKPath` 至 `inProgressPaths` 。 當該手指移動時，會將額外的點新增至路徑。 當手指放開時，會將路徑傳送至 `completedPaths` 集合。 您可以同時使用多個手指繪製。 每次變更為其中一個路徑或集合之後， `SKCanvasView` 就會失效：

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

觸控追蹤事件隨附的點是 Xamarin.Forms 座標，必須轉換成 SkiaSharp 座標，也就是圖元。 這就是方法的目的 `ConvertToPixel` 。

`PaintSurface`然後，處理常式只會轉譯兩個路徑集合。 先前完成的路徑會出現在 [進行中的路徑] 底下：

```csharp
public partial class FingerPaintPage : ContentPage
{
    ...
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

您的手指畫作只受限於您的人才：

[![](finger-paint-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](finger-paint-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

您現在已瞭解如何繪製線條，以及如何使用參數化方來定義曲線。 [**SkiaSharp 曲線和路徑**](../curves/index.md)的後續章節涵蓋支援的各種曲線類型 `SKPath` 。 但有一項實用的必要條件是探索[**SkiaSharp 轉換**](../transforms/index.md)。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [觸控追蹤效果示範（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)
- [從效果叫用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
