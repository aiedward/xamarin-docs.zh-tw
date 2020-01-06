---
title: 在 SkiaSharp 中繪製簡單的圓形
description: 本文說明 SkiaSharp 繪圖的基本概念，包括 canvas 和油漆物件，以及在 Xamarin 中使用範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: a0ab6a965c2507c01f5b7ebdc3670e6661ca481e
ms.sourcegitcommit: 191f1f3b13a14e2afadcb95126c5f653722f126f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545630"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>在 SkiaSharp 中繪製簡單的圓形

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解 SkiaSharp 繪圖的基本概念，包括畫布和油漆物件_

本文介紹使用 SkiaSharp 在 Xamarin 中繪製圖形的概念，包括建立用來裝載圖形的 `SKCanvasView` 物件、處理 `PaintSurface` 事件，以及使用 `SKPaint` 物件指定色彩和其他繪圖屬性。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)套裝程式含這一系列 SkiaSharp 文章的所有範例程式碼。 第一頁的標題是**簡單的 Circle** ，而且會叫用 page 類別[`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)。 這段程式碼示範如何在頁面中央繪製一個半徑為100圖元的圓形。 圓形的外框是紅色，而圓形的內部是藍色。

![](circle-images/circleexample.png "A blue circle outlined in red")

[`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)頁面類別衍生自 `ContentPage`，且包含兩個適用于 SkiaSharp 命名空間的 `using` 指示詞：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

下列類別的函式會建立[`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView)物件、附加[`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface)事件的處理常式，並將 `SKCanvasView` 物件設定為頁面的內容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvasView` 會佔用頁面的整個內容區域。 您也可以將 `SKCanvasView` 與其他 Xamarin 結合 `View` 衍生，如您在其他範例中所見。

`PaintSurface` 事件處理常式是您進行所有繪製的地方。 當您的程式正在執行時，可以多次呼叫這個方法，因此應該維護重新建立圖形顯示所需的所有資訊：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

事件隨附的[`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs)物件有兩個屬性：

- 類型為 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) 的 [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info)
- 類型為 [`SKSurface`](xref:SkiaSharp.SKSurface) 的 [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface)

`SKImageInfo` 結構包含繪圖介面的相關資訊，最重要的是，其寬度和高度（以圖元為單位）。 `SKSurface` 物件代表繪圖介面本身。 在此程式中，繪圖介面是影片顯示，但是在其他程式中，`SKSurface` 物件也可以代表您用來繪製 SkiaSharp 的點陣圖。

`SKSurface` 的最重要屬性是類型[`SKCanvas`](xref:SkiaSharp.SKCanvas)的[`Canvas`](xref:SkiaSharp.SKSurface.Canvas) 。 這個類別是用來執行實際繪製的圖形繪圖內容。 `SKCanvas` 物件會封裝圖形狀態，其中包括圖形轉換和裁剪。

以下是 `PaintSurface` 事件處理常式的典型開始：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();
    ...
}

```

[`Clear`](xref:SkiaSharp.SKCanvas.Clear)方法會清除具有透明色彩的畫布。 多載可讓您指定畫布的背景色彩。

此處的目標是繪製以藍色填滿的紅色圓圈。 因為此特定圖形影像包含兩種不同的色彩，所以必須以兩個步驟來完成作業。 第一個步驟是繪製圓形的外框。 若要指定線條的色彩和其他特性，您可以建立並初始化[`SKPaint`](xref:SkiaSharp.SKPaint)物件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = Color.Red.ToSKColor(),
        StrokeWidth = 25
    };
    ...
}
```

[`Style`](xref:SkiaSharp.SKPaint.Style)屬性指出您想要對線條（在此案例中為圓形的外框）進行*筆劃繪製*，而不是*填滿*內部。 [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle)列舉的三個成員如下所示：

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

預設為 `Fill`。 使用第三個選項來繪製線條，並使用相同的色彩填滿內部。

將[`Color`](xref:SkiaSharp.SKPaint.Color)屬性設定為[`SKColor`](xref:SkiaSharp.SKColor)類型的值。 取得 `SKColor` 值的方法之一，就是使用[`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*)的擴充方法，將 `Color` 值轉換成 `SKColor` 值。 `SkiaSharp.Views.Forms` 命名空間中的[`Extensions`](xref:SkiaSharp.Views.Forms.Extensions)類別包含其他方法，可在 Xamarin. form 值和 SkiaSharp 值之間轉換。

[`StrokeWidth`](xref:SkiaSharp.SKPaint.StrokeWidth)屬性工作表示線條的粗細。 其設定為25圖元。

您可以使用該 `SKPaint` 物件來繪製圓形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    canvas.DrawCircle(info.Width / 2, info.Height / 2, 100, paint);
    ...
}
```

座標的指定相對於顯示介面的左上角。 X 座標會增加至右方，而 Y 座標則會增加。 在討論圖形時，通常會使用數學標記法（x，y）來表示某個點。 點（0，0）是顯示介面的左上角，通常稱為「*原點*」。

`DrawCircle` 的前兩個引數表示圓形中心的 X 和 Y 座標。 這些會指派給顯示介面的一半寬度和高度，以將圓形的中心放在顯示介面的中央。 第三個引數會指定圓形的半徑，而最後一個引數則是 `SKPaint` 物件。

若要填滿圓形的內部，您可以改變 `SKPaint` 物件的兩個屬性，然後再次呼叫 `DrawCircle`。 這段程式碼也會顯示從[`SKColors`](xref:SkiaSharp.SKColors)結構的許多欄位之一取得 `SKColor` 值的替代方法：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

這次，`DrawCircle` 呼叫會使用 `SKPaint` 物件的新屬性來填滿圓形。

以下是在 iOS 和 Android 上執行的程式：

[![](circle-images/simplecircle-small.png "Triple screenshot of the Simple Circle page")](circle-images/simplecircle-large.png#lightbox "Triple screenshot of the Simple Circle page")

當您自行執行程式時，您可以將手機或模擬器旋轉一側，以查看圖形的重繪方式。 每次重新繪製圖形時，都會再次呼叫 `PaintSurface` 事件處理常式。

您也可以使用漸層或點陣圖磚來彩色繪圖物件。 這些選項會在[**SkiaSharp 著色**](../effects/shaders/index.md)器一節中討論。

`SKPaint` 物件不只是圖形繪製屬性的集合。 這些物件很輕量。 您可以在此程式執行時重複使用 `SKPaint` 物件，也可以針對各種繪製屬性組合建立多個 `SKPaint` 物件。 您可以在 `PaintSurface` 事件處理常式之外建立和初始化這些物件，也可以將它們儲存為頁面類別中的欄位。

> [!NOTE]
> `SKPaint` 類別會定義[`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) ，以在圖形呈現時啟用消除鋸齒功能。 消除鋸齒通常會產生視覺上更平滑的邊緣，因此您可能會想要將此屬性設定為大部分 `SKPaint` 物件中的 `true`。 為了簡單起見，此屬性_不_會在大部分的範例頁面中設定。

雖然圓形外框的寬度是指定為25個圖元 &mdash; 或圓形半徑的一季，&mdash; 外觀較長，而且有很好的理由：線條寬度的一半是以藍色圓圈遮蔽。 `DrawCircle` 方法的引數會定義圓形的抽象幾何座標。 藍色內部的大小會調整為最接近的圖元，但25個圖元的外框會跨越幾何圓圈，而在外的內側和一半 &mdash; 一半。

[與 Xamarin 進行整合](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)一文中的下一個範例會以視覺化方式示範。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
