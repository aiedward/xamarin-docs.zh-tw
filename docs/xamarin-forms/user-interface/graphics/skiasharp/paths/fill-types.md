---
title: 路徑填滿類型
description: 本文章檢查不同的效果可能使用 SkiaSharp 路徑填滿類型時，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d99bb4722beb86c3cd2f49077fb778b8d8f61397
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53051804"
---
# <a name="the-path-fill-types"></a>路徑填滿類型

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_探索不同的效果設有 SkiaSharp 路徑填滿類型_

路徑中的兩個分佈可以重疊，而且行組成的單一分佈可以重疊。 可能可以填入任何括住的區域，但您可能不想以填滿所有括住的區域。 以下為範例：

![](fill-types-images/filltypeexample.png "五個指向部分星級 filles")

您可以透過這有點控制。 填滿演算法由[ `SKFillType` ](xref:SkiaSharp.SKPath.FillType)屬性`SKPath`，其中您將設定為隸屬[ `SKPathFillType` ](xref:SkiaSharp.SKPathFillType)列舉型別：

- `Winding`預設值
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

捲繞和奇偶演算法判斷括住的任何區域是填滿或未填入 根據假設一行取自該區域，為無限大。 那一行會跨越一或多個界限組成線條的路徑。 捲曲的模式中，如果繪製單一方向餘額，放大在另一個方向，則區域中繪製的線條數目的界限行數不是填滿。 否則會填滿區域。 如果界限行數是奇數，則奇偶演算法會填滿區域。

許多例行的路徑，捲繞演算法通常會填滿所有括住的區域路徑。 奇偶演算法通常會產生更有趣的結果。

典型的範例是五角星形，如所示**Five-Pointed 星狀**頁面。 [ **FivePointedStarPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml)檔案具現化兩個`Picker`檢視來選取路徑填滿類型和路徑是將描邊還是填滿或兩者，以及依照什麼順序：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Paths.FivePointedStarPage"
             Title="Five-Pointed Star">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Picker x:Name="fillTypePicker"
                Title="Path Fill Type"
                Grid.Row="0"
                Grid.Column="0"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKPathFillType}">
                    <x:Static Member="skia:SKPathFillType.Winding" />
                    <x:Static Member="skia:SKPathFillType.EvenOdd" />
                    <x:Static Member="skia:SKPathFillType.InverseWinding" />
                    <x:Static Member="skia:SKPathFillType.InverseEvenOdd" />
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Picker x:Name="drawingModePicker"
                Title="Drawing Mode"
                Grid.Row="0"
                Grid.Column="1"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Fill only</x:String>
                    <x:String>Stroke only</x:String>
                    <x:String>Stroke then Fill</x:String>
                    <x:String>Fill then Stroke</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skiaforms:SKCanvasView x:Name="canvasView"
                                Grid.Row="1"
                                Grid.Column="0"
                                Grid.ColumnSpan="2"
                                PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

程式碼後置檔案會使用這兩者`Picker`繪製 5 角星形的值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = 0.45f * Math.Min(info.Width, info.Height);

    SKPath path = new SKPath
    {
        FillType = (SKPathFillType)fillTypePicker.SelectedItem
    };
    path.MoveTo(info.Width / 2, info.Height / 2 - radius);

    for (int i = 1; i < 5; i++)
    {
        // angle from vertical
        double angle = i * 4 * Math.PI / 5;
        path.LineTo(center + new SKPoint(radius * (float)Math.Sin(angle), 
                                        -radius * (float)Math.Cos(angle)));
    }
    path.Close();

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 50,
        StrokeJoin = SKStrokeJoin.Round
    };

    SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    switch ((string)drawingModePicker.SelectedItem)
    {
        case "Fill only":
            canvas.DrawPath(path, fillPaint);
            break;

        case "Stroke only":
            canvas.DrawPath(path, strokePaint);
            break;

        case "Stroke then Fill":
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case "Fill then Stroke":
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

一般而言，路徑填滿類型應該會填滿並不是使用筆劃，但這兩個會影響`Inverse`模式會影響填滿和筆劃。 填滿，兩個`Inverse`類型填滿區域的號，讓區域外星狀填滿。 針對這兩個的筆劃`Inverse`類型色彩筆劃以外的所有內容。 使用這些反向的填滿類型可能會產生一些奇怪的效果，如 iOS 螢幕擷取畫面所示：

[![](fill-types-images/fivepointedstar-small.png "Five-Pointed 星狀頁面的三個螢幕擷取畫面")](fill-types-images/fivepointedstar-large.png#lightbox "Five-Pointed 星狀頁面的三個螢幕擷取畫面")

Android 和 UWP 螢幕擷取畫面顯示典型的奇偶和捲繞效果，但筆觸和填滿的順序也會影響結果。

捲曲的演算法是取決於繪製線條的方向。 通常當您要建立的路徑，您可以控制該方向與您指定行取自到另一個點。 不過，`SKPath`類別也會定義方法，例如`AddRect`和`AddCircle`，繪製整個分佈。 若要控制如何繪製這些物件，方法包含參數的型別[ `SKPathDirection` ](xref:SkiaSharp.SKPathDirection)，其中包含兩個成員：

- `Clockwise`
- `CounterClockwise`

中的方法`SKPath`，包括`SKPathDirection`參數會指定它，預設值為`Clockwise`。

**重疊圓形**頁面會使用四個重疊圓形奇偶路徑填滿類型建立路徑：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(info.Width, info.Height) / 4;

    SKPath path = new SKPath
    {
        FillType = SKPathFillType.EvenOdd
    };

    path.AddCircle(center.X - radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X - radius / 2, center.Y + radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y - radius / 2, radius);
    path.AddCircle(center.X + radius / 2, center.Y + radius / 2, radius);

    SKPaint paint = new SKPaint()
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Cyan
    };

    canvas.DrawPath(path, paint);

    paint.Style = SKPaintStyle.Stroke;
    paint.StrokeWidth = 10;
    paint.Color = SKColors.Magenta;

    canvas.DrawPath(path, paint);
}
```

它會建立最少的程式碼的有趣映像：

[![](fill-types-images/overlappingcircles-small.png "三個重疊圓形頁面螢幕擷取畫面")](fill-types-images/overlappingcircles-large.png#lightbox "重疊圓形頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
