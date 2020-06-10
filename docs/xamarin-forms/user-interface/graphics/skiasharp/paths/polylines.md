---
title： "折線和參數方程式描述：" 本文說明如何使用 SkiaSharp 來轉譯您可以使用參數化方程式定義的任何行，並以範例程式碼示範此動作。
assetid： 85AEBB33-E954-4364-A6E1-808FAB197BEE ms. 技術： xamarin skiasharp author： davidbritch ms-chap： dabritch ms. date：03/10/2017 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="polylines-and-parametric-equations"></a>聚合線條和參數化的方程式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 轉譯任何可使用參數化方程式定義的線條_

在本指南的[**SkiaSharp 曲線和路徑**](../curves/index.md)一節中，您將會看到 [`SKPath`](xref:SkiaSharp.SKPath) 定義來轉譯特定類型曲線的各種方法。 不過，有時必須繪製一種不受直接支援的曲線 `SKPath` 。 在這種情況下，您可以使用「折線」（連接線的集合）繪製任何可以數學方式定義的曲線。 如果您讓這幾行夠小且夠大，結果看起來會像是曲線。 這種螺旋實際上是3600行：

![](polylines-images/spiralexample.png "A spiral")

一般來說，最好是根據一對參數方程式來定義曲線。 這些是 X 和 Y 座標的方程式，視第三個變數而定，有時會 `t` 針對時間進行呼叫。 例如，下列參數化方程式會定義半徑為1的圓形，並在0到1的*t*點（0，0）上居中。

`x = cos(2πt)`

`y = sin(2πt)`

 如果您想要大於1的半徑，可以只將正弦和余弦值乘以該半徑，如果您需要將中心移至另一個位置，請新增這些值：

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

針對具有與水準和垂直平行之軸的橢圓形，涉及兩個半徑：

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

然後，您可以將對等的 SkiaSharp 程式碼放在計算各個點的迴圈中，並將它們新增至路徑。 下列 SkiaSharp 程式碼 `SKPath` 會針對填滿顯示介面的橢圓形建立物件。 迴圈會直接迴圈執行360度。 中心是顯示介面寬度和高度的一半，因此是兩個半徑：

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

這會導致橢圓形定義成360的小線條。 轉譯時，外觀會平滑。

當然，您不需要使用折線來建立橢圓形，因為包含可 `SKPath` `AddOval` 為您執行此工作的方法。 但您可能會想要繪製一個不是由提供的視覺物件 `SKPath` 。

[ **Archimedean 螺旋**] 頁面具有與橢圓形程式碼類似的程式碼，但有很重要的差異。 它會在圓形10次的360度前後迴圈，並持續調整半徑：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

結果也稱為*算術螺旋*，因為每個迴圈之間的位移是常數：

[![](polylines-images/archimedeanspiral-small.png "Triple screenshot of the Archimedean Spiral page")](polylines-images/archimedeanspiral-large.png#lightbox "Triple screenshot of the Archimedean Spiral page")

請注意， `SKPath` 是在區塊中建立的 `using` 。 這 `SKPath` 會耗用比先前程式中的物件更多的記憶體 `SKPath` ，這表示 `using` 區塊較適合用來處置任何非受控資源。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
