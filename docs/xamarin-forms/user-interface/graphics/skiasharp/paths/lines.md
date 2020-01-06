---
title: 線條和筆觸端點
description: 本文說明如何使用 SkiaSharp，在 Xamarin 中以不同的筆觸帽繪製線條。表單應用程式，並使用範例程式碼示範這項功能。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: 724a79e618321f97257718bf56dd1fdd18f73563
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545601"
---
# <a name="lines-and-stroke-caps"></a>線條和筆觸端點

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用 SkiaSharp 以不同的筆觸帽繪製線條_

在 SkiaSharp 中，轉譯單行與轉譯一系列連接的直線非常不同。 不過，即使繪製單行，通常還是必須提供特定筆劃寬度的線條。 當這些線條變寬時，線條末端的外觀也會變得很重要。 行尾的外觀稱為「*筆劃端點*」：

![](lines-images/strokecapsexample.png "The three stroke caps options")

對於繪製單行，`SKCanvas` 會定義簡單的[`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint))方法，其引數表示行的開始和結束座標與 `SKPaint` 物件：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

根據預設，新具現化之 `SKPaint` 物件的[`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth)屬性為0，這在以粗細呈現某個圖元的線條時，其效果與1的值相同。 這在高解析度裝置（如手機）上看起來非常精簡，因此您可能會想要將 `StrokeWidth` 設定為較大的值。 但是一旦您開始繪製可調整大小粗細的線條，就會引發另一個問題：如何呈現這些粗線的開始和結束？

行開頭和結尾的外觀稱為*線條端點*，或 Skia 中的*筆劃*端點。 此內容中的「端點」一詞指的是一種 hat，&mdash; 位於行尾的東西。 您將 `SKPaint` 物件的[`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap)屬性設定為[`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap)列舉的下列其中一個成員：

- `Butt` （預設值）
- `Square`
- `Round`

這是一個範例程式的最佳說明。 [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式的 [ **SkiaSharp 程式列] 和 [路徑**] 區段，會以[`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs)類別**為基礎的**封面頁面開始。 這個頁面會定義一個 `PaintSurface` 事件處理常式，以對 `SKStrokeCap` 列舉的三個成員進行迴圈，同時顯示列舉成員的名稱，並使用該筆劃端點繪製線條：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 75,
        TextAlign = SKTextAlign.Center
    };

    SKPaint thickLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 50
    };

    SKPaint thinLinePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2
    };

    float xText = info.Width / 2;
    float xLine1 = 100;
    float xLine2 = info.Width - xLine1;
    float y = textPaint.FontSpacing;

    foreach (SKStrokeCap strokeCap in Enum.GetValues(typeof(SKStrokeCap)))
    {
        // Display text
        canvas.DrawText(strokeCap.ToString(), xText, y, textPaint);
        y += textPaint.FontSpacing;

        // Display thick line
        thickLinePaint.StrokeCap = strokeCap;
        canvas.DrawLine(xLine1, y, xLine2, y, thickLinePaint);

        // Display thin line
        canvas.DrawLine(xLine1, y, xLine2, y, thinLinePaint);
        y += 2 * textPaint.FontSpacing;
    }
}
```

對於 `SKStrokeCap` 列舉的每個成員而言，處理常式會繪製兩條線，一個具有50圖元的筆觸粗細，另一個則以兩個圖元的筆觸粗細放在上方。 第二行的目的是要說明與線條粗細和筆劃端點無關的線條幾何起點和終點：

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

如您所見，`Square` 和 `Round` 筆觸的端點，會有效地將行的長度延伸到行首的最後一半的筆劃寬度，然後再以結尾。 當需要判斷轉譯之繪圖物件的維度時，此延伸模組會變得很重要。

`SKCanvas` 類別也包含另一種方法，可繪製多行，但有些什麼嗎：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points` 參數是 `SKPoint` 值的陣列，而 `mode` 是[`SKPointMode`](xref:SkiaSharp.SKPointMode)列舉的成員，其中有三個成員：

- 呈現個別點的 `Points`
- `Lines` 連接每一對點
- 連接所有連續點的 `Polygon`

[**多行**] 頁面會示範這個方法。 [**MultipleLinesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml)會具現化兩個 `Picker` 視圖，讓您選取 `SKPointMode` 列舉的成員，以及 `SKStrokeCap` 列舉的成員：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.MultipleLinesPage"
             Title="Multiple Lines">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="pointModePicker"
                Title="Point Mode"
                Grid.Row="0"
                Grid.Column="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPointMode}">
                    <x:Static Member="skia:SKPointMode.Points" />
                    <x:Static Member="skia:SKPointMode.Lines" />
                    <x:Static Member="skia:SKPointMode.Polygon" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKStrokeCap}">
                    <x:Static Member="skia:SKStrokeCap.Butt" />
                    <x:Static Member="skia:SKStrokeCap.Round" />
                    <x:Static Member="skia:SKStrokeCap.Square" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                PaintSurface="OnCanvasViewPaintSurface"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

請注意，SkiaSharp 命名空間宣告有點不同，因為需要 `SkiaSharp` 命名空間，才能參考 `SKPointMode` 和 `SKStrokeCap` 列舉的成員。 `Picker` views 的 `SelectedIndexChanged` 處理常式只會使 `SKCanvasView` 物件失效：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

這個處理常式必須檢查 `SKCanvasView` 物件是否存在，因為當 `Picker` 的 `SelectedIndex` 屬性在 XAML 檔案中設定為0，而且在 `SKCanvasView` 已具現化之前發生，就會先呼叫事件處理常式。

`PaintSurface` 處理常式會從 `Picker` views 取得兩個列舉值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    // Create an array of points scattered through the page
    SKPoint[] points = new SKPoint[10];

    for (int i = 0; i < 2; i++)
    {
        float x = (0.1f + 0.8f * i) * info.Width;

        for (int j = 0; j < 5; j++)
        {
            float y = (0.1f + 0.2f * j) * info.Height;
            points[2 * j + i] = new SKPoint(x, y);
        }
    }

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.DarkOrchid,
        StrokeWidth = 50,
        StrokeCap = (SKStrokeCap)strokeCapPicker.SelectedItem
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = (SKPointMode)pointModePicker.SelectedItem;
    canvas.DrawPoints(pointMode, points, paint);
}
```

螢幕擷取畫面顯示各種 `Picker` 選項：

[![](lines-images/multiplelines-small.png "Triple screenshot of the Multiple Lines page")](lines-images/multiplelines-large.png#lightbox "Triple screenshot of the Multiple Lines page")

左側的 iPhone 會顯示 `SKPointMode.Points` 列舉成員如何使 `DrawPoints` 將 `SKPoint` 陣列中的每個點轉譯為正方形（如果行帽 `Butt` 或 `Square`）。 如果行帽 `Round`，則會呈現圓形。

Android 螢幕擷取畫面會顯示 `SKPointMode.Lines`的結果。 `DrawPoints` 方法會使用指定的行帽（在此案例中為 `Round`），在每一對 `SKPoint` 值之間繪製線條。

當您改為使用 `SKPointMode.Polygon`時，會在陣列中的後續點之間繪製線條，但如果您看起來非常密切，就會看到這些線條並未連接。 這些個別的每一行都是以指定的行帽作為開頭和結尾。 如果您選取 [`Round` caps]，可能會有線路連線，但它們實際上並未連接。

線路是否已連接或未連接是使用圖形路徑的重要層面。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
