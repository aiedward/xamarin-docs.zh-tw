---
title: 線條和筆觸端點
description: 本文說明如何使用 SkiaSharp 在應用程式中以不同的筆觸帽繪製線條 Xamarin.Forms ，並使用範例程式碼示範這項功能。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 87b97ad913e08c42d16bbf055f168c07b9bd60e8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137199"
---
# <a name="lines-and-stroke-caps"></a>線條和筆觸端點

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用 SkiaSharp 以不同的筆觸帽繪製線條_

在 SkiaSharp 中，轉譯單行與轉譯一系列連接的直線非常不同。 不過，即使繪製單行，通常還是必須提供特定筆劃寬度的線條。 當這些線條變寬時，線條末端的外觀也會變得很重要。 行尾的外觀稱為「*筆劃端點*」：

![](lines-images/strokecapsexample.png "The three stroke caps options")

對於繪製單行，會 `SKCanvas` 定義一個簡單的 [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) 方法，其引數會以物件表示行的開始和結束座標 `SKPaint` 。

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

根據預設，新具現 [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) 化物件的屬性 `SKPaint` 為0，其作用與在以粗細呈現某個圖元的線條時的值為1。 這在高解析度裝置（如手機）上看起來非常精簡，因此您可能會想要將設定 `StrokeWidth` 為較大的值。 但是一旦您開始繪製可調整大小粗細的線條，就會引發另一個問題：如何呈現這些粗線的開始和結束？

行開頭和結尾的外觀稱為*線條端點*，或 Skia 中的*筆劃*端點。 此內容中的「端點」一詞指的是一種 &mdash; 位於行尾的 hat。 您可以將 [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) 物件的屬性設定 `SKPaint` 為列舉的下列其中一個成員 [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) ：

- `Butt`（預設值）
- `Square`
- `Round`

這是一個範例程式的最佳說明。 [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式的 [ **SkiaSharp 行] 和 [路徑**] 區段會以類別**為基礎的**頁面上開始 [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) 。 此頁面會定義 `PaintSurface` 事件處理常式，以迴圈執行列舉的三個成員 `SKStrokeCap` ，同時顯示列舉成員的名稱，並使用該筆劃端點繪製線條：

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

針對列舉的每個成員 `SKStrokeCap` ，處理常式會繪製兩條線，其中一個具有50圖元的筆觸粗細，另一行則以兩個圖元的筆觸粗細放置在上方。 第二行的目的是要說明與線條粗細和筆劃端點無關的線條幾何起點和終點：

[![](lines-images/strokecaps-small.png "Triple screenshot of the Stroke Caps page")](lines-images/strokecaps-large.png#lightbox "Triple screenshot of the Stroke Caps page")

如您所見， `Square` 和 `Round` 筆觸端點會有效地將行的長度延伸到行首的最後一半的筆劃寬度，並在結尾處再次擴充。 當需要判斷轉譯之繪圖物件的維度時，此延伸模組會變得很重要。

`SKCanvas`類別也包含另一個方法，可用於繪製有點什麼嗎的多行：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`參數是值的陣列， `SKPoint` 而且 `mode` 是列舉的成員 [`SKPointMode`](xref:SkiaSharp.SKPointMode) ，其中有三個成員：

- `Points`呈現個別點
- `Lines`連接每一對點
- `Polygon`連接所有連續點

[**多行**] 頁面會示範這個方法。 [**MultipleLinesPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml)會具現化兩個 `Picker` 視圖，讓您選取列舉的成員 `SKPointMode` 和列舉的成員 `SKStrokeCap` ：

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

請注意，SkiaSharp 命名空間宣告有點不同，因為 `SkiaSharp` 需要命名空間才能參考和列舉的成員 `SKPointMode` `SKStrokeCap` 。 `SelectedIndexChanged`這兩個 views 的處理常式 `Picker` 只會使 `SKCanvasView` 物件失效：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

這個處理常式必須檢查物件是否存在 `SKCanvasView` ，因為當的 `SelectedIndex` 屬性在 XAML 檔案 `Picker` 中設定為0，且在具現化之前發生時，會先呼叫事件處理常式 `SKCanvasView` 。

`PaintSurface`處理常式會從 views 取得兩個列舉值 `Picker` ：

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

左側的 iPhone `SKPointMode.Points` 會顯示列舉成員如何導致將 `DrawPoints` 陣列中的每個點轉譯 `SKPoint` 為正方形（如果行端點為 `Butt` 或） `Square` 。 如果線條端點為，則會呈現圓形 `Round` 。

Android 螢幕擷取畫面顯示的結果 `SKPointMode.Lines` 。 `DrawPoints`方法會 `SKPoint` 使用指定的行帽（在此案例中為），在每一對值之間繪製線條 `Round` 。

當您改用時 `SKPointMode.Polygon` ，會在陣列中的連續點之間繪製線條，但如果您看起來非常密切，就會看到這些線條並未連接。 這些個別的每一行都是以指定的行帽作為開頭和結尾。 如果您選取 [ `Round` 上限]，線條可能會看似 [已連接]，但它們實際上不會連線。

線路是否已連接或未連接是使用圖形路徑的重要層面。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
