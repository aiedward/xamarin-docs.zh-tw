---
title: 路徑填滿類型
description: 探索不同的效果可能 SkiaSharp 路徑填滿類型
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 88b9dacef7a77d5f18908bdcb696e5172ceaa8c7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="the-path-fill-types"></a>路徑填滿類型

_探索不同的效果可能 SkiaSharp 路徑填滿類型_

在路徑中的兩個分佈可以重疊，而且行組成的單一分佈可以重疊。 可能會填滿括住的任何區域，但您可能不想要填滿所有括住的區域。 以下為範例：

![](fill-types-images/filltypeexample.png "五個指向星級部分 filles")

您可以透過這有點控制。 填滿演算法由[ `SKFillType` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPath.FillType/)屬性`SKPath`，您將它設定成員的[ `SKPathFillType` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathFillType/)列舉型別：

- [`Winding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.Winding/)預設值
- [`EvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.EvenOdd/)
- [`InverseWinding`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseWinding/)
- [`InverseEvenOdd`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathFillType.InverseEvenOdd/)

捲繞和奇偶演算法決定任何括住的區域填滿或未根據假設線條從該區域為無限大填入。 該行跨越一或多個界限線條的路徑。 捲繞模式中，如果繪製單一方向平衡其他方向，則在區域中繪製的直線數中的界限行數不是填滿。 否則會填滿區域。 如果界限行數是奇數，則奇偶演算法會填滿區域。

許多例行的路徑，捲繞演算法通常會填滿所有括住的區域路徑。 奇偶演算法通常會產生更有趣的結果。

典型的範例如示，是五角星形， **Five-Pointed 星狀**頁面。 [FivePointedStarPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/LinesAndPaths/FivePointedStarPage.xaml)檔案會呈現兩個`Picker`檢視來選取路徑填滿類型和路徑是否遭到圖案或填滿或兩者，以及依照何種：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.FivePointedStarPage"
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
            <Picker.Items>
                <x:String>Winding</x:String>
                <x:String>EvenOdd</x:String>
                <x:String>InverseWinding</x:String>
                <x:String>InverseEvenOdd</x:String>
            </Picker.Items>
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
            <Picker.Items>
                <x:String>Fill only</x:String>
                <x:String>Stroke only</x:String>
                <x:String>Stroke then Fill</x:String>
                <x:String>Fill then Stroke</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="1"
                           Grid.Column="0"
                           Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

程式碼後置檔案會使用這兩者`Picker`繪製五個角星形的值：

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
        FillType = (SKPathFillType)Enum.Parse(typeof(SKPathFillType),
                        fillTypePicker.Items[fillTypePicker.SelectedIndex])
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

    switch (drawingModePicker.SelectedIndex)
    {
        case 0:
            canvas.DrawPath(path, fillPaint);
            break;

        case 1:
            canvas.DrawPath(path, strokePaint);
            break;

        case 2:
            canvas.DrawPath(path, strokePaint);
            canvas.DrawPath(path, fillPaint);
            break;

        case 3:
            canvas.DrawPath(path, fillPaint);
            canvas.DrawPath(path, strokePaint);
            break;
    }
}
```

一般而言，路徑填滿類型應該只填滿和不筆觸，但這兩個會影響`Inverse`模式會影響填滿和筆劃。 填滿，兩個`Inverse`類型填滿區域的號，以便填滿星形外面的區域。 如筆劃，兩個`Inverse`類型色彩筆劃以外的所有內容。 使用這些反向填滿類型可能會產生一些奇怪的效果，如 iOS 螢幕擷取畫面所示：

[![](fill-types-images/fivepointedstar-small.png "Five-Pointed 星狀頁面的三個螢幕擷取畫面")](fill-types-images/fivepointedstar-large.png#lightbox "Five-Pointed 星狀頁面的三個螢幕擷取畫面")

Android 和 Windows 行動裝置的螢幕擷取畫面顯示一般奇偶和捲繞效果，但筆觸與填滿的順序也會影響結果。

捲繞演算法會相依於線條會繪製的方向。 通常當您建立的路徑，您可以控制該方向與您指定行取自到另一個點。 不過，`SKPath`類別也可以定義像方法`AddRect`和`AddCircle`，繪製整個分佈。 若要控制這些物件如何繪製，方法包括類型參數的[ `SKPathDirection` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathDirection/)，其中包含兩個成員：

- [`Clockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.Clockwise/)
- [`CounterClockwise`](https://developer.xamarin.com/api/field/SkiaSharp.SKPathDirection.CounterClockwise/)

中的方法`SKPath`包括`SKPathDirection`參數提供預設值是`Clockwise`。

**重疊圓形**頁面使用奇偶路徑填滿類型的四個重疊圓形建立路徑：

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

它會建立最少的程式碼的有趣影像：

[![](fill-types-images/overlappingcircles-small.png "三個重疊圓形頁面螢幕擷取畫面")](fill-types-images/overlappingcircles-large.png#lightbox "重疊圓形頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
