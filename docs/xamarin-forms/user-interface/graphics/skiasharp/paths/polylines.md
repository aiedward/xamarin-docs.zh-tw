---
title: 聚合線條和參數化的方程式
description: 這篇文章說明如何使用 SkiaSharp 呈現至任何行您可以定義參數化的方程式，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d5896a9d4f1aac2ea90d544d638e4adf68d24140
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2018
ms.locfileid: "39615791"
---
# <a name="polylines-and-parametric-equations"></a>聚合線條和參數化的方程式

_呈現任何列，您可以定義參數化的方程式中使用 SkiaSharp_

在[ **SkiaSharp 曲線和路徑**](../curves/index.md)一節的本指南中，您會看到的各種方法， [ `SKPath` ](xref:SkiaSharp.SKPath)定義來呈現特定類型的曲線。 不過，有時候是必要的繪製不直接支援的曲線類型`SKPath`。 在此情況下，您可以使用聚合線 （已連接的線條的集合） 來繪製以數學方式可以定義任何曲線。 如果您讓行夠小，許多足夠，結果將看起來曲線。 此螺旋是實際 3,600 的少幾行：

![](polylines-images/spiralexample.png "螺旋")

通常最好是定義一組參數化的方程式方面的曲線。 這些是方程式的 X 和 Y 座標，取決於第三個變數，有時也稱為`t`時間。 比方說，下列參數化的方程式定義半徑為 1 的中心點 （0，0） 位於圓形*t*從 0 到 1:

x = cos(2πt)

y = sin(2πt)

 如果您想 radius 大於 1，可以只的正弦和餘弦函數值乘以該 radius，，和如果您需要將中央移到其他位置，將這些值加入：

x = xCenter + radius·cos(2πt)

y = yCenter + radius·sin(2πt)

針對至水平及垂直軸平行橢圓形，牽涉到兩個的半徑：

x = xCenter + xRadius·cos(2πt)

y = yCenter + yRadius·sin(2πt)

然後，您可以將對等的 SkiaSharp 程式碼放在迴圈中，會計算不同的點，以及將這些路徑。 下列 SkiaSharp 程式碼會建立`SKPath`橢圓形的填滿顯示介面的物件。 迴圈循環 360 度直接。 Center 是一半的寬度和高度顯示介面上，因此是兩個的半徑：

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

這會導致 360 小線條所定義的橢圓形。 轉譯時，它會顯示平滑。

當然，您不需要建立使用多線，因為橢圓形`SKPath`包含`AddOval`會為您的方法。 但您可能想要繪製的視覺物件不由提供`SKPath`。

**Archimedean 螺旋**頁面有類似的程式碼的省略符號的程式碼，但重要的差異。 它迴圈循環 360 度的圓形 10 次，持續調整半徑：

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

其結果也稱為*算術螺旋*因為每個迴圈之間的位移保持不變：

[![](polylines-images/archimedeanspiral-small.png "Archimedean 螺旋頁面的三個螢幕擷取畫面")](polylines-images/archimedeanspiral-large.png#lightbox "Archimedean 螺旋頁面的三個螢幕擷取畫面")

請注意，`SKPath`中建立`using`區塊。 這`SKPath`耗用記憶體比`SKPath`物件，在先前的程式中，暗示著`using`區塊會更適當處置任何 unmanaged 的資源。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
