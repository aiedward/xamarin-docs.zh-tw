---
title: 聚合線條和參數化的方程式
description: 使用 SkiaSharp 轉譯任何一行您可以定義之參數的方程式
ms.topic: article
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: dce14ead2de6a21b99209ed12fbb99729734149a
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="polylines-and-parametric-equations"></a>聚合線條和參數化的方程式

_使用 SkiaSharp 轉譯任何一行您可以定義之參數的方程式_

在本指南稍後的部分，您會看到的各種方法，`SKPath`定義來呈現特定類型的曲線。 不過，則有時需要繪製曲線不直接支援的一種`SKPath`。 在這種情況下，您可以使用多線 （連接的直線的集合） 來繪製以數學方式可以定義任何曲線。 如果您讓行夠小，而且許多足夠，結果看起來像曲線。 此老舊是實際的 3600 小行：

![](polylines-images/spiralexample.png "老舊")

通常最好定義參數化的方程式的一組根據曲線。 這些是方程式的 X 和 Y 座標，取決於第三個變數，有時也稱為`t`時間。 例如，下列參數的方程式定義圓形半徑為 （0，0） 的點置中對齊的 1 *t*從 0 到 1:

 x = cos(2πt) y = sin(2πt)

 如果您想大於 1 的半徑，只是可以的正弦函數 」 與 「 餘弦值乘以該 radius，及您需要將中央移到其他位置，如果加入這些值：

 x = xCenter + radius·cos(2πt) y = yCenter + radius·sin(2πt)

要水平及垂直軸平行橢圓形，牽涉到兩個半徑：

x = xCenter + xRadius·cos(2πt) y = yCenter + yRadius·sin(2πt)

然後，您就可以計算不同的點，並新增至路徑的迴圈中放置對等的 SkiaSharp 程式碼。 下列 SkiaSharp 程式碼會建立`SKPath`橢圓形填滿顯示介面的物件。 迴圈循環 360 度直接。 中心 是一半的寬度和高度顯示介面，並因此是兩個半徑：

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

這會導致 360 小行所定義的橢圓形。 轉譯時，它會顯示平滑。

當然，您不需要建立橢圓形使用的聚合線條，因為`SKPath`包含`AddOval`會為您的方法。 但您可能想要繪製的視覺物件不由提供`SKPath`。

**Archimedean 老舊**頁面具有類似的程式碼的省略符號的程式碼，但重要的差異。 它迴圈循環 360 度的圓形 10 次，持續調整半徑：

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

也稱為結果*算術老舊*因為每個迴圈之間的位移是常數：

[![](polylines-images/archimedeanspiral-small.png "三個螢幕擷取畫面的 Archimedean 老舊頁面")](polylines-images/archimedeanspiral-large.png#lightbox "Archimedean 老舊頁面的三個螢幕擷取畫面")

請注意，`SKPath`中建立`using`區塊。 這`SKPath`會消耗更多的記憶體比`SKPath`物件在先前的程式中，建議`using`區塊會更適當地處置任何 unmanaged 的資源。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
