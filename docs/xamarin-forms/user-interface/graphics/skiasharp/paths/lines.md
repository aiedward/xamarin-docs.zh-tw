---
title: 線條和筆觸端點
description: 本文說明如何使用 SkiaSharp 在應用程式中繪製具有不同筆觸帽的線條 Xamarin.Forms ，並使用範例程式碼來示範此程式碼。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 75d19e41243076da127d58defdabeca908d900cd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556083"
---
# <a name="lines-and-stroke-caps"></a>線條和筆觸端點

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解如何使用 SkiaSharp 繪製具有不同筆觸帽的線條_

在 SkiaSharp 中，轉譯單一線條與轉譯一連串連接的直線非常不同。 但是，即使是在繪製單一程式列時，通常還是必須為線條提供特定的筆觸寬度。 當這些行會變寬時，行尾的外觀也變得很重要。 該行結尾的外觀稱為「 *筆劃端點*」：

![三個筆觸帽選項](lines-images/strokecapsexample.png)

若要繪製單一行，請 `SKCanvas` 定義簡單的 [`DrawLine`](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) 方法，其引數會以物件表示線條的開始和結束座標 `SKPaint` ：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

根據預設， [`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth) 新具現化物件的屬性 `SKPaint` 是0，其效果與在以粗細的一個圖元轉譯一行時的值相同。 這在高解析度的裝置（例如手機）上看起來非常精簡，因此您可能會想要將設定 `StrokeWidth` 為較大的值。 但是，一旦您開始繪製大小調整粗細的線條，就會引發另一個問題：如何轉譯這些粗線的開頭和結尾？

行開頭和結尾的外觀稱為 *行帽* ，在 Skia 中則是 *筆劃端點*。 此內容中的「端點」一詞是指位於該行結尾的一種 hat &mdash; 東西。 您可以將 [`StrokeCap`](xref:SkiaSharp.SKPaint.StrokeCap) 物件的屬性設 `SKPaint` 為列舉的下列其中一個成員 [`SKStrokeCap`](xref:SkiaSharp.SKStrokeCap) ：

- `Butt` (預設) 
- `Square`
- `Round`

這些最適合使用範例程式來說明。 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式的 [ **SkiaSharp 行和路徑**] 區段會從標題為 [**筆劃 Caps** ] 的頁面開始，以類別為基礎 [`StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/StrokeCapsPage.cs) 。 此頁面會定義 `PaintSurface` 事件處理常式，此處理程式會在列舉的三個成員中執行迴圈 `SKStrokeCap` ，並顯示列舉成員的名稱，並使用該筆劃端點繪製一條線：

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

針對列舉的每個成員 `SKStrokeCap` ，此處理程式會繪製兩個線條，一個筆觸粗細為50圖元，另一行位於頂端，且筆觸粗細為兩個圖元。 第二行的目的是要說明線條粗細和筆劃端點以外之線條的幾何開頭和結尾：

[![[筆劃 Caps] 頁面的三重螢幕擷取畫面](lines-images/strokecaps-small.png)](lines-images/strokecaps-large.png#lightbox "[筆劃 Caps] 頁面的三重螢幕擷取畫面")

如您所見， `Square` 和 `Round` 筆觸帽會有效地將行的長度延伸到行首的一半筆劃寬度和結尾。 當需要判斷轉譯繪圖物件的維度時，此擴充功能就變得很重要。

此 `SKCanvas` 類別也包含另一種方法，可讓您繪製有點什麼嗎的多行：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`參數是值的陣列 `SKPoint` `mode` ，而且是列舉的成員 [`SKPointMode`](xref:SkiaSharp.SKPointMode) ，其中有三個成員：

- `Points` 轉譯個別的點
- `Lines` 連接每一對點
- `Polygon` 連接所有連續的點

[ **多行** ] 頁面會示範這個方法。 [**MultipleLinesPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/MultipleLinesPage.xaml)檔案具現化兩個 `Picker` 視圖，可讓您選取列舉的成員 `SKPointMode` 和列舉的成員 `SKStrokeCap` ：

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

請注意，SkiaSharp 命名空間宣告有點不同，因為 `SkiaSharp` 參考和列舉的成員需要命名空間 `SKPointMode` `SKStrokeCap` 。 `SelectedIndexChanged`這兩個視圖的處理常式 `Picker` 只會使 `SKCanvasView` 物件失效：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

這個處理常式必須檢查物件是否存在 `SKCanvasView` ，因為當的 `SelectedIndex` 屬性在 XAML 檔案 `Picker` 中設定為0，而且在具現化之前發生時，會先呼叫事件處理常式 `SKCanvasView` 。

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

螢幕擷取畫面顯示各種 `Picker` 選擇：

[![[多行] 頁面的三重螢幕擷取畫面](lines-images/multiplelines-small.png)](lines-images/multiplelines-large.png#lightbox "[多行] 頁面的三重螢幕擷取畫面")

左邊的 iPhone `SKPointMode.Points` 會顯示當行帽為或時，列舉成員會如何將 `DrawPoints` 陣列中的每個點轉譯 `SKPoint` 為正方形 `Butt` `Square` 。 如果行端點為，則會呈現圓形 `Round` 。

Android 螢幕擷取畫面顯示的結果 `SKPointMode.Lines` 。 `DrawPoints`此方法會 `SKPoint` 使用指定的行帽（在此案例中），在每一對值之間繪製線條 `Round` 。

當您改為使用時 `SKPointMode.Polygon` ，會在陣列中的後續點之間繪製線條，但如果看起來非常接近，您會看到這些線條並未連接。 這些個別的每一行都會以指定的行帽開始及結束。 如果您選取 [ `Round` caps （cap）]，這些線條可能會顯示為 [已連接]，但實際上沒有連接。

是否連接或未連接線條是使用圖形路徑的重要層面。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)