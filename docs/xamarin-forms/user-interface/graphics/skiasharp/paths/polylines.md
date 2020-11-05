---
title: 聚合線條和參數化的方程式
description: 本文說明如何使用 SkiaSharp 轉譯任何您可以使用參數化方程式定義的行，並使用範例程式碼來示範此程式碼。
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2b5fbae64d1cef6fb39fbe505b77fbc931803349
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375261"
---
# <a name="polylines-and-parametric-equations"></a>聚合線條和參數化的方程式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用 SkiaSharp 來呈現您可以使用參數方程式定義的任何線條_

在本指南的 [ [**SkiaSharp 曲線和路徑**](../curves/index.md) ] 區段中，您會看到 [`SKPath`](xref:SkiaSharp.SKPath) 定義以呈現特定曲線類型的各種方法。 但是，有時候必須繪製一種不是直接支援的曲線類型 `SKPath` 。 在這種情況下，您可以使用折線 (連接線的集合) 來繪製任何可以數學方式定義的曲線。 如果您讓線條夠小且夠多，結果看起來就像曲線。 這個螺旋其實是3600的小行：

![螺旋線](polylines-images/spiralexample.png)

一般而言，最好是根據一對參數方程式定義曲線。 這些是 X 和 Y 座標的方程式，相依于第三個變數，有時會 `t` 針對時間呼叫。 例如，下列參數化方程式會定義半徑為1的圓形，並以0到1的 *t* (0、0) 的點為中心。

`x = cos(2πt)`

`y = sin(2πt)`

 如果您想要大於1的半徑，可以只將正弦和余弦值乘以該半徑，如果您需要將中心移動到另一個位置，請新增這些值：

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

如果橢圓形的軸與水準和垂直平行，則牽涉到兩個半徑：

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

然後，您可以將對等的 SkiaSharp 程式碼放在計算各種點的迴圈中，並將它們新增至路徑。 下列 SkiaSharp 程式碼會建立 `SKPath` 填滿顯示介面之橢圓形的物件。 迴圈會直接迴圈執行360度。 中央是顯示介面的一半寬度和高度，而這兩個半徑如下：

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

這會產生360個小線條所定義的橢圓形。 轉譯時，它會呈現凹凸貼圖。

當然，您不需要使用聚合線條建立橢圓形，因為 `SKPath` 包含 `AddOval` 可為您執行此工作的方法。 但是，您可能會想要繪製不是由提供的視覺物件 `SKPath` 。

**Archimedean 螺旋線** 頁面的程式碼類似于橢圓形程式碼，但有很重要的差異。 它會圍繞圓形的360度旋轉10次，持續調整半徑：

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

結果也稱為 *算術螺旋線* ，因為每個迴圈之間的位移都是常數：

[![Archimedean 螺旋線頁面的三重螢幕擷取畫面](polylines-images/archimedeanspiral-small.png)](polylines-images/archimedeanspiral-large.png#lightbox "Archimedean 螺旋線頁面的三重螢幕擷取畫面")

請注意， `SKPath` 會在區塊中建立 `using` 。 這 `SKPath` 會耗用比先前程式中的物件更多的記憶體 `SKPath` ，這表示 `using` 區塊更適合處置任何未受管理的資源。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)