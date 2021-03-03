---
title: 在 SkiaSharp 中繪製簡單的圓形
description: 本文說明在應用程式中 SkiaSharp 繪圖的基本概念，包括畫布和繪圖物件， Xamarin.Forms 並以範例程式碼示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E3A4E373-F65D-45C8-8E77-577A804AC3F8
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 39e9effa1c43a4456a1e75e391e14804e2aa0011
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375092"
---
# <a name="drawing-a-simple-circle-in-skiasharp"></a>在 SkiaSharp 中繪製簡單的圓形

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_瞭解 SkiaSharp 繪圖的基本概念，包括畫布和油漆物件_

本文介紹使用 SkiaSharp 繪製圖形的概念 Xamarin.Forms ，包括建立 `SKCanvasView` 物件來裝載圖形、處理 `PaintSurface` 事件，以及使用 `SKPaint` 物件來指定色彩和其他繪圖屬性。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)套裝程式含這一系列 SkiaSharp 文章的所有範例程式碼。 第一頁是有權的 **簡單圓圈** ，而且會叫用頁面類別 [`SimpleCirclePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs) 。 這段程式碼示範如何在頁面中央繪製圓形，半徑為100圖元。 圓形的輪廓為紅色，而圓形的內部為藍色。

![以紅色框住的藍色圓圈](circle-images/circleexample.png)

[`SimpleCircle`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SimpleCirclePage.cs)Page 類別衍生自 `ContentPage` ，而且包含 `using` SkiaSharp 命名空間的兩個指示詞：

```csharp
using SkiaSharp;
using SkiaSharp.Views.Forms;
```

下列類別的函式會建立 [`SKCanvasView`](xref:SkiaSharp.Views.Forms.SKCanvasView) 物件、附加事件的處理常式， [`PaintSurface`](xref:SkiaSharp.Views.Forms.SKCanvasView.PaintSurface) 並將 `SKCanvasView` 物件設定為頁面的內容：

```csharp
public SimpleCirclePage()
{
    Title = "Simple Circle";

    SKCanvasView canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

會 `SKCanvasView` 佔用頁面的整個內容區域。 您也可以 `SKCanvasView` 將與其他 Xamarin.Forms `View` 衍生項結合，如您在其他範例中所見。

`PaintSurface`事件處理常式是您在其中進行所有繪圖的位置。 當程式正在執行時，可以多次呼叫這個方法，因此它應該維護重新建立圖形顯示所需的所有資訊：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
}

```

[`SKPaintSurfaceEventArgs`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs)事件隨附的物件有兩個屬性：

- [`Info`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Info) 型別為 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo)
- [`Surface`](xref:SkiaSharp.Views.Forms.SKPaintSurfaceEventArgs.Surface) 型別為 [`SKSurface`](xref:SkiaSharp.SKSurface)

`SKImageInfo`結構包含繪圖介面的相關資訊，最重要的是其寬度和高度（以圖元為單位）。 `SKSurface`物件表示繪圖介面本身。 在這個程式中，繪圖介面是影片顯示，但在其他程式中， `SKSurface` 物件也可以代表您使用 SkiaSharp 繪製所在的點陣圖。

最重要的屬性 `SKSurface` 是 [`Canvas`](xref:SkiaSharp.SKSurface.Canvas) 類型 [`SKCanvas`](xref:SkiaSharp.SKCanvas) 。 這個類別是您用來執行實際繪圖的圖形繪製內容。 `SKCanvas`物件會封裝圖形狀態，包括圖形轉換和裁剪。

以下是 `PaintSurface` 事件處理常式的一般開始：

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

此處的目標是要繪製以藍色填滿的紅色圓形。 由於這個特定的圖形影像包含兩種不同的色彩，因此必須透過兩個步驟來完成工作。 第一個步驟是繪製圓形的輪廓。 若要指定線條的色彩和其他特性，您可以建立和初始化 [`SKPaint`](xref:SkiaSharp.SKPaint) 物件：

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

[`Style`](xref:SkiaSharp.SKPaint.Style)屬性工作表示您想要在這種情況下 *繪製* 線條 (，在此案例中為圓形的外框) ，而不是 *填滿* 內部。 列舉的三個成員如下所示 [`SKPaintStyle`](xref:SkiaSharp.SKPaintStyle) ：

- [`Fill`](xref:SkiaSharp.SKPaintStyle.Fill)
- [`Stroke`](xref:SkiaSharp.SKPaintStyle.Stroke)
- [`StrokeAndFill`](xref:SkiaSharp.SKPaintStyle.StrokeAndFill)

預設為 `Fill`。 使用第三個選項來繪製線條，並以相同的色彩填滿內部。

將 [`Color`](xref:SkiaSharp.SKPaint.Color) 屬性設為類型的值 [`SKColor`](xref:SkiaSharp.SKColor) 。 取得值的方法之一 `SKColor` 是 Xamarin.Forms `Color` 使用擴充方法將值轉換成 `SKColor` 值 [`ToSKColor`](xref:SkiaSharp.Views.Forms.Extensions.ToSKColor*) 。 [`Extensions`](xref:SkiaSharp.Views.Forms.Extensions)命名空間中的 `SkiaSharp.Views.Forms` 類別包含在 Xamarin.Forms 值與 SkiaSharp 值之間轉換的其他方法。

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

座標會指定為相對於顯示介面左上角的位置。 X 座標增加至右側，Y 座標會增加。 在有關圖形的討論中，通常會使用數學標記法 (x，y) 來代表點。 點 (0、0) 是顯示介面的左上角，通常稱為「 *原點*」。

的前兩個引數 `DrawCircle` 表示圓形中心的 X 和 Y 座標。 這些會指派給顯示介面的一半寬度和高度，以將圓形的中心放在顯示介面的中央。 第三個引數會指定圓形的半徑，而最後一個引數則是 `SKPaint` 物件。

若要填滿圓形的內部，您可以變更物件的兩個屬性 `SKPaint` ，然後 `DrawCircle` 再呼叫一次。 這段程式碼也會顯示 `SKColor` 從結構的許多欄位之一取得值的替代方式 [`SKColors`](xref:SkiaSharp.SKColors) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    paint.Style = SKPaintStyle.Fill;
    paint.Color = SKColors.Blue;
    canvas.DrawCircle(args.Info.Width / 2, args.Info.Height / 2, 100, paint);
}
```

這次， `DrawCircle` 呼叫會使用物件的新屬性來填滿圓形 `SKPaint` 。

以下是在 iOS 和 Android 上執行的程式：

[![簡單圓圈頁面的三重螢幕擷取畫面](circle-images/simplecircle-small.png)](circle-images/simplecircle-large.png#lightbox "簡單圓圈頁面的三重螢幕擷取畫面")

當您自行執行程式時，您可以橫向切換手機或模擬器，以查看圖形的重繪方式。 每次需要重新繪製圖形時， `PaintSurface` 就會再次呼叫事件處理常式。

您也可以使用漸層或點陣圖磚來將繪圖物件著色。 [**SkiaSharp 著色**](../effects/shaders/index.md)器一節中會討論這些選項。

`SKPaint`物件只是圖形繪圖屬性的集合。 這些物件很輕量。 您可以在 `SKPaint` 此程式執行時重複使用物件，也可以 `SKPaint` 為各種繪圖屬性組合建立多個物件。 您可以在事件處理常式之外建立和初始化這些物件 `PaintSurface` ，也可以將它們儲存為頁面類別中的欄位。

> [!NOTE]
> `SKPaint`類別會定義 [`IsAntialias`](xref:SkiaSharp.SKPaint.IsAntialias) ，以在圖形呈現時啟用消除鋸齒。 消除鋸齒通常會導致視覺效果更平滑，因此您可能會想要將此屬性設定為 `true` 大部分的 `SKPaint` 物件。 為了簡單起見，在大部分的範例頁面中 _不_ 會設定這個屬性。

雖然圓形的外框寬度指定為25圖元 &mdash; 或圓形半徑的四分之一 &mdash; ，但卻有一個很好的原因：該線條寬度的一半是由藍色圓圈遮蔽的一半。 方法的引數會 `DrawCircle` 定義圓形的抽象幾何座標。 藍色內部的大小會調整為最接近圖元的維度，但25個全圖元的外框會跨越在 &mdash; 外內側和一半的幾何圓形一半。

[與 Xamarin.Forms 文章整合](~/xamarin-forms/user-interface/graphics/skiasharp/basics/integration.md)的下一個範例會以視覺化方式示範這一點。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)