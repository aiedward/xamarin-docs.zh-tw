---
title: "線條和筆觸端點"
description: "了解如何使用 SkiaSharp 使用不同的筆觸端點繪製線條"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1F854DDD-5D1B-4DE4-BD2D-584439429FDB
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 9a3090873569db2466db9ab25cc105ea59401df3
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="lines-and-stroke-caps"></a>線條和筆觸端點

_了解如何使用 SkiaSharp 使用不同的筆觸端點繪製線條_

在 SkiaSharp，繪製單一線條是非常不同轉譯一系列連接的直線。 即使當繪製單一線條，不過，通常是為了提供特定筆劃寬度和寬的線條的線條，更重要會成為一條線，呼叫端的外觀*筆觸端點*:

![](lines-images/strokecapsexample.png "三個筆劃 cap 選項")

用於繪製單一線條`SKCanvas`定義簡單[ `DrawLine` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawLine/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/)方法的引數表示的起始和結束的那一行座標`SKPaint`物件：

```csharp
canvas.DrawLine (x0, y0, x1, y1, paint);
```

根據預設，`StrokeWidth`屬性的新具現化`SKPaint`物件為 0，其具有值為 1 中呈現一個像素的線條粗細中相同的效果。 因此您可能會想要設定顯示針對高解析度裝置，例如手機，非常細`StrokeWidth`到較大的值。 但是，一旦您開始繪製可調整大小的粗細的線條，會引發另一個問題： 如何應該開始和結束這些粗線的呈現？

啟動和行尾的外觀會呼叫*線條端點*或在 Skia，*筆觸端點*。 在此內容中的 「 cap 」 這個字是指一種 hat & #x 2014;項目置放在桌上一行的結尾。 您設定[ `StrokeCap` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.StrokeCap/)屬性`SKPaint`物件的下列成員的其中一個[ `SKStrokeCap` ](https://developer.xamarin.com/api/type/SkiaSharp.SKStrokeCap/)列舉型別：

- [`Butt`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Butt/) （預設值）
- [`Square`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)
- [`Round`](https://developer.xamarin.com/api/field/SkiaSharp.SKStrokeCap.Round/)

這些最好說明與範例程式。 第二個區段的 [首頁] 的[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)程式開始與頁面標題為**筆觸端點**根據[ `StrokeCapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/StrokeCapsPage.cs)類別。 此頁面定義`PaintSurface`迴圈的三個成員的事件處理常式`SKStrokeCap`列舉型別，顯示列舉成員的名稱和繪製線條，使用該筆劃的端點：

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

針對每個成員`SKStrokeCap`列舉型別，此處理常式繪製兩行，一個 50 像素和另一個位於頂端，使用 2 個像素的筆劃粗細的線條的筆劃粗細。 此第二行僅供示範幾何的開始與結束線條的粗細和筆觸端點獨立的一行：

[![](lines-images/strokecaps-small.png "三個螢幕擷取畫面的筆觸端點頁面")](lines-images/strokecaps-large.png "筆觸端點頁面的三個螢幕擷取畫面")

如您所見，`Square`和`Round`筆觸端點有效地擴充行長度的筆劃寬度的一半在一行的開頭和結尾一次。 需要判斷呈現的圖形物件的維度時，這項擴充功能變得很重要。

`SKCanvas`類別還包括繪製多個線條會稍微罕見的另一個方法：

```csharp
DrawPoints (SKPointMode mode, points, paint)
```

`points`參數為陣列`SKPoint`值和`mode`隸屬[ `SKPointMode` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPointMode/)列舉型別，有三個成員：

- [`Points`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Points/) 要呈現個別的點
- [`Lines`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Lines/) 連線點每一對
- [`Polygon`](https://developer.xamarin.com/api/field/SkiaSharp.SKPointMode.Polygon/) 所有連續點的連線

**多行**頁面會示範這個方法。 [ `MultipleLinesPage` XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/MultipleLinesPage.xaml)會呈現兩個`Picker`檢視，可讓您選取的成員`SKPointMode`列舉型別和成員`SKStrokeCap`列舉型別：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.MultipleLinesPage"
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
            <Picker.Items>
                <x:String>Points</x:String>
                <x:String>Lines</x:String>
                <x:String>Polygon</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="strokeCapPicker"
                Title="Stroke Cap"
                Grid.Row="0"
                Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>Butt</x:String>
                <x:String>Round</x:String>
                <x:String>Square</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2" />
    </Grid>
</ContentPage>
```

`SelectedIndexChanged`兩者的處理常式`Picker`檢視只會導致無效`SKCanvasView`物件：

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs args)
{
    if (canvasView != null)
    {
        canvasView.InvalidateSurface();
    }
}
```

這個處理常式需要檢查是否存在`SKCanvasView`因為事件處理常式是第一個呼叫物件時`SelectedIndex`屬性`Picker`設為 0，在 XAML 檔案中，而且會出現之前`SKCanvasView`具現化。

`PaintSurface`處理常式存取泛型的方法，以取得兩個選取的項目，從`Picker`檢視，並將它們轉換為列舉值：

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
        StrokeCap = GetPickerItem<SKStrokeCap>(strokeCapPicker)
    };

    // Render the points by calling DrawPoints
    SKPointMode pointMode = GetPickerItem<SKPointMode>(pointModePicker);
    canvas.DrawPoints(pointMode, points, paint);
}

T GetPickerItem<T>(Picker picker)
{
    if (picker.SelectedIndex == -1)
    {
        return default(T);
    }
    return (T)Enum.Parse(typeof(T), picker.Items[picker.SelectedIndex]);
}
```

螢幕擷取畫面顯示各種`Picker`三個平台上的選取項目：

[![](lines-images/multiplelines-small.png "多行網頁的三個螢幕擷取畫面")](lines-images/multiplelines-large.png "多行網頁的三個螢幕擷取畫面")

在左邊顯示 iPhone 如何`SKPointMode.Points`列舉成員會造成`DrawPoints`來呈現每個在點`SKPoint`線條端點是否為方形陣列`Butt`或`Square`。 線條端點時，會轉譯圓形`Round`。

當您改為使用`SKPointMode.Lines`，如在中心，Android 螢幕上顯示`DrawPoints`方法每對之間繪製一條線`SKPoint`值，在此情況下使用指定的線條端點`Round`。

Windows 行動裝置顯示的結果`SKPointMode.Polygon`值。 陣列中的連續點之間繪製一條線，但如果您非常仔細看，您會看到這些線條未連線。 每個別一行的開始和結束與指定的線條端點時。 如果您選取`Round`cap，線條可能似乎可以連接，但實際上未連接。

線條會連接或未連接是使用圖形路徑的重要層面。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
