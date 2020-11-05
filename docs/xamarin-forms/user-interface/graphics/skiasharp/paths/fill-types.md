---
title: 路徑填滿類型
description: 本文將探討 SkiaSharp 路徑填滿類型可能產生的不同效果，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.assetid: 57103A7A-49A2-46AE-894C-7C2664682644
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 63833e80b15bb27ab656825b4b1f6b1e3eb31670
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367812"
---
# <a name="the-path-fill-types"></a>路徑填滿類型

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 路徑填滿類型可能有的不同效果_

路徑中的兩個輪廓可能會重迭，而組成單一輪廓的線條可能會重迭。 所有的封閉區域都可能填滿，但您可能不想填滿所有的封閉區域。 以下是範例：

![五個指向的星星部分 filles](fill-types-images/filltypeexample.png)

您對此有很小的控制權。 填滿演算法是由 [`SKFillType`](xref:SkiaSharp.SKPath.FillType) `SKPath` 您設定為列舉成員的屬性所控制 [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) ：

- `Winding`（預設值）
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

繞組和奇數演算法會根據從該區域繪製的假設線來決定是否填滿或未填滿任何封閉區域。 該行會跨過組成路徑的一或多個界限線。 使用纏繞模式時，如果以一個方向繪製的界限線數目，就會以另一個方向繪製的行數進行平衡，則不會填滿區域。 否則會填滿區域。 如果界限線的數目是奇數，則奇數演算法會填滿區域。

使用許多常式路徑時，纏繞演算法通常會填滿路徑的所有封閉區域。 奇數演算法通常會產生更有趣的結果。

典型的範例是五指向的星星，如 **五個指向的星星** 頁面所示。 [**FivePointedStarPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml)檔案會具現化兩個 `Picker` 視圖，以選取路徑填滿類型，以及是否要將路徑繪製或填滿或填滿，以及順序：

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

程式碼後端檔案會使用這兩個 `Picker` 值來繪製五個指向的星星：

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

一般情況下，路徑填滿類型應該只影響填滿而非筆劃，但這兩種 `Inverse` 模式會影響填滿和筆劃。 填滿時，這兩個 `Inverse` 類型會填滿區域 oppositely，以填滿星號外的區域。 針對筆觸，這兩種類型會將 `Inverse` 筆劃以外的所有專案全部上色。 使用這些反向填滿類型可能會產生一些奇怪的效果，如 iOS 螢幕擷取畫面所示：

[![Five-Pointed 星星頁面的三重螢幕擷取畫面](fill-types-images/fivepointedstar-small.png)](fill-types-images/fivepointedstar-large.png#lightbox "Five-Pointed 星星頁面的三重螢幕擷取畫面")

Android 螢幕擷取畫面顯示一般的偶奇數和繞組效果，但是筆劃和填滿的順序也會影響結果。

纏繞演算法取決於繪製線條的方向。 當您建立路徑時，通常會在您指定將線條從某個點繪製到另一個點時控制該方向。 不過，此 `SKPath` 類別也 `AddRect` `AddCircle` 會定義繪製整個分佈的方法，例如和。 若要控制這些物件的繪製方式，方法包含類型的參數 [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) ，其中有兩個成員：

- `Clockwise`
- `CounterClockwise`

中 `SKPath` 包含參數的方法 `SKPathDirection` 會提供預設值 `Clockwise` 。

[重 **迭的圓形** ] 頁面會以具有偶數奇數路徑填滿類型的四個重迭圓形來建立路徑：

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

這是以最少程式碼建立的有趣映射：

[![重迭的圓形頁面的三重螢幕擷取畫面](fill-types-images/overlappingcircles-small.png)](fill-types-images/overlappingcircles-large.png#lightbox "重迭的圓形頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)