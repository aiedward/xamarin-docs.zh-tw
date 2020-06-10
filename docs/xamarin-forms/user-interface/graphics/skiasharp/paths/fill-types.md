---
標題：「路徑填滿類型」描述：「本文將探討 SkiaSharp 路徑填滿類型的各種可能效果，並以範例程式碼示範這種情況。」
assetid： 57103A7A-49A2-46AE-894C-7C2664682644 ms. 技術： xamarin skiasharp author： davidbritch ms-chap： dabritch ms. date：03/10/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="the-path-fill-types"></a>路徑填滿類型

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 路徑填滿類型探索可能的不同效果_

路徑中的兩個輪廓可以重迭，而組成單一輪廓的線條可能會重迭。 任何封閉的區域都可能會填滿，但您可能不想要填滿所有封閉的區域。 以下是範例：

![](fill-types-images/filltypeexample.png "Five-pointed star partially filles")

您對此有一些控制。 填入演算法是由的屬性所控制 [`SKFillType`](xref:SkiaSharp.SKPath.FillType) `SKPath` ，您可以將它設定為列舉的成員 [`SKPathFillType`](xref:SkiaSharp.SKPathFillType) ：

- `Winding`，預設值
- `EvenOdd`
- `InverseWinding`
- `InverseEvenOdd`

「纏繞」和「奇數」演算法都會根據從該區域繪製到無限大的假設線，來判斷是否有任何封閉的區域填滿或未填滿。 這一行會跨越組成路徑的一或多個界限行。 使用纏繞模式時，如果在某個方向繪製的邊界線條數目與另一個方向所繪製的直線數進行平衡，則不會填滿該區域。 否則會填滿區域。 如果界限的數目為奇數，則偶數演算法會填滿區域。

使用許多例行路徑時，纏繞演算法通常會填滿路徑的所有封閉區域。 偶數-奇數演算法通常會產生更有趣的結果。

典型的範例是以五個指向的星號，如五個**指向的星星**頁面中所示。 [**FivePointedStarPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Paths/FivePointedStarPage.xaml)會具現化兩個 `Picker` 視圖，以選取路徑填滿類型，以及路徑為已繪製或已填滿，還是要以何種循序執行：

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

程式碼後置檔案會使用這兩個 `Picker` 值來繪製五個指向的星號：

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

一般來說，路徑填滿型別只會影響填滿，而不是筆劃，但這兩種 `Inverse` 模式會影響填滿和筆劃。 針對填滿，這兩個 `Inverse` 類型的填滿區域會 oppositely，讓星星外的區域填滿。 就筆劃而言，這兩個類型會對 `Inverse` 筆劃以外的所有專案進行色彩。 使用這些反填滿類型可能會產生一些奇怪的效果，如 iOS 螢幕擷取畫面所示：

[![](fill-types-images/fivepointedstar-small.png "Triple screenshot of the Five-Pointed Star page")](fill-types-images/fivepointedstar-large.png#lightbox "Triple screenshot of the Five-Pointed Star page")

Android 螢幕擷取畫面顯示一般的奇數和纏繞效果，但是筆劃和填滿的順序也會影響結果。

纏繞演算法是根據繪製線條的方向而定。 通常當您建立路徑時，您可以在指定從某個點繪製線條至另一個位置時，控制該方向。 不過， `SKPath` 類別也 `AddRect` 會定義 `AddCircle` 繪製整個輪廓的方法（例如和）。 為了控制這些物件的繪製方式，方法包含型別為的參數 [`SKPathDirection`](xref:SkiaSharp.SKPathDirection) ，其中有兩個成員：

- `Clockwise`
- `CounterClockwise`

中 `SKPath` 包含參數的方法 `SKPathDirection` 會提供其預設值 `Clockwise` 。

[重**迭的圓形**] 頁面會建立具有四個重迭圓形的路徑，其中包含奇數路徑填滿類型：

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

這是以最少的程式碼建立的有趣映射：

[![](fill-types-images/overlappingcircles-small.png "Triple screenshot of the Overlapping Circles page")](fill-types-images/overlappingcircles-large.png#lightbox "Triple screenshot of the Overlapping Circles page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
