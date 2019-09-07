---
title: 線條和筆觸端點
description: 本文說明如何使用 SkiaSharp 繪製線條使用不同的筆觸端點在 Xamarin.Forms 應用程式，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: cc62ca4656a845a261c56424aa1ea1331c994994
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70759210"
---
# <a name="lines-and-stroke-caps"></a>線條和筆觸端點

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何使用 SkiaSharp 繪製線條使用不同的筆觸端點_

SkiaSharp，在轉譯程式會非常不同於轉譯一系列連接的直線。 即使在繪製單一線條，不過，通常很讓行特定的筆劃寬度所需。 隨著這幾行範圍更廣，行尾的外觀也變得很重要。 一行結尾的外觀會呼叫*筆觸端點*:

![](lines-images/strokecapsexample.png "三個筆劃 cap 選項")

繪製單一線條`SKCanvas`定義簡單[ `DrawLine` ](xref:SkiaSharp.SKCanvas.DrawLine(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint))方法的起始和結束的那一行的座標，其引數指出`SKPaint`物件：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

根據預設， [ `StrokeWidth` ](xref:SkiaSharp.SKPaint.StrokeWidth)屬性的新具現化`SKPaint`物件為 0，且具有值為 1，呈現一個像素的線條粗細中相同的效果。 因此您可能會想要設定顯示在高解析度的裝置，例如手機上非常精簡`StrokeWidth`到較大的值。 但是一旦您開始繪製可調整大小粗細的線條，就會引發另一個問題：這些粗線的開始和結束應該如何呈現？

在呼叫開始和結束的行的外觀*線條端點*或者 Skia，在*筆觸端點*。 在此內容中的 「 cap"是指一種 hat&mdash;位於該行結尾的項目。 您設定[ `StrokeCap` ](xref:SkiaSharp.SKPaint.StrokeCap)屬性`SKPaint`物件的下列成員的其中一個[ `SKStrokeCap` ](xref:SkiaSharp.SKStrokeCap)列舉型別：

- `Butt` （預設值）
- `Square`
- `Round`

這些最佳說明與範例程式。 **SkiaSharp 線條和路徑**一節[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式開始與頁面標題為**筆觸端點**根據[ `StrokeCapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs)類別。 此頁面定義`PaintSurface`迴圈的三個成員的事件處理常式`SKStrokeCap`列舉型別，來顯示列舉成員的名稱，並繪製一條線使用該筆觸端點：

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

針對每個成員`SKStrokeCap`列舉型別，這個處理常式繪製兩行，其中一個筆劃粗細是 50 個像素並另一行筆觸粗細是兩個像素定位在最上層。 此第二行的目的是要說明的幾何的開始和結束的線條的粗細和筆觸端點的獨立的一行：

[![](lines-images/strokecaps-small.png "筆觸端點 頁面的三個螢幕擷取畫面")](lines-images/strokecaps-large.png#lightbox "筆觸端點 頁面的三個螢幕擷取畫面")

如您所見，`Square`和`Round`筆觸端點有效地擴充一半筆觸粗細在一行的開頭和結尾一次一行的長度。 需要判斷轉譯的圖形物件的維度時，此延伸模組變得重要。

`SKCanvas`類別也包含繪製多行是有點罕見的另一種方法：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`參數是陣列`SKPoint`值並`mode`隸屬[ `SKPointMode` ](xref:SkiaSharp.SKPointMode)列舉型別，有三個成員：

- `Points` 要呈現個別的點
- `Lines` 連接點每一對
- `Polygon` 所有連續點的連線

**多行**頁面會示範這個方法。 [ **MultipleLinesPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml)檔案具現化兩個`Picker`檢視，可讓您選取的成員`SKPointMode`列舉型別和成員`SKStrokeCap`列舉型別：

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

請注意 SkiaSharp 命名空間宣告會稍有不同，因為`SkiaSharp`命名空間需要參考的成員`SKPointMode`和`SKStrokeCap`列舉型別。 `SelectedIndexChanged`兩者的處理常式`Picker`檢視只會導致無效`SKCanvasView`物件：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

這個處理常式需要檢查是否存在`SKCanvasView`物件因為這是第一次的事件處理常式時呼叫`SelectedIndex`屬性`Picker`設定為 0，在 XAML 檔案中，而且會出現之前`SKCanvasView`已具現化。

`PaintSurface`處理常式會取得兩個列舉值從`Picker`檢視：

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

螢幕擷取畫面顯示各種`Picker`選取項目：

[![](lines-images/multiplelines-small.png "多行 頁面的三個螢幕擷取畫面")](lines-images/multiplelines-large.png#lightbox "多行 頁面的三個螢幕擷取畫面")

左側顯示在 iPhone 如何`SKPointMode.Points`列舉成員會導致`DrawPoints`為了轉譯每個中的點`SKPoint`陣列為方形線條端點是否`Butt`或`Square`。 圓形線條端點是否呈現`Round`。

當您改為使用`SKPointMode.Lines`，在 [中心] 的 Android 畫面所示`DrawPoints`方法的兩個配對之間繪製線條`SKPoint`值，在此情況下使用指定的直線帽緣， `Round`。

UWP 螢幕擷取畫面顯示的結果`SKPointMode.Polygon`值。 陣列中的連續點之間繪製一條線，但如果您非常仔細看，您會看到這幾行未連線。 每個這些個別的行開始和結尾指定的線條端點。 如果您選取`Round`大寫字、 行可能會連線，但他們真的不連線。

線條是連線或未連接是使用的圖形路徑的重要層面。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
