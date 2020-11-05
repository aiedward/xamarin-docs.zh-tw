---
title: 像素與裝置獨立單位
description: 本文將探討 SkiaSharp 座標和座標之間的差異 Xamarin.Forms ，並使用範例程式碼來示範這點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5f12dbd4ef0cfea9d276fd8c939afab585541ece
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373888"
---
# <a name="pixels-and-device-independent-units"></a>像素與裝置獨立單位

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 座標和座標之間的差異 Xamarin.Forms_

本文探討 SkiaSharp 和中所使用之座標系統的差異 Xamarin.Forms 。 您可以取得資訊以在兩個座標系統之間轉換，也可以繪製填滿特定區域的圖形：

![填滿畫面的橢圓形](pixels-images/screenfillexample.png)

如果您曾經 Xamarin.Forms 進行程式設計，您可能會覺得 Xamarin.Forms 座標和大小。 這兩篇文章中所繪製的圓形可能有點小。

這些圓形與大小的 *比較很小* Xamarin.Forms 。 根據預設，SkiaSharp 會以圖元單位繪製，而 Xamarin.Forms 基底座標和大小會在基礎平臺所建立的裝置獨立單位上。 您 Xamarin.Forms 可以在第5章中找到關於座標系統的 (詳細資訊 [。處理使用](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)*建立 Mobile Apps Xamarin.Forms* 之書籍的大小。 ) 

[**SkewSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中的頁面，其具有 **介面大小** ，會使用 SkiaSharp 文字輸出來顯示三個不同來源的顯示介面大小：

- 物件的一般 Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) 和 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 屬性 `SKCanvasView` 。
- [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)物件的屬性 `SKCanvasView` 。
- [`Size`](xref:SkiaSharp.SKImageInfo.Size)值的屬性 `SKImageInfo` ，與 `Width` `Height` 前兩個頁面中使用的和屬性一致。

[`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)類別會顯示如何顯示這些值。 此函式會將 `SKCanvasView` 物件儲存為欄位，讓它可以在 `PaintSurface` 事件處理常式中存取：

```csharp
SKCanvasView canvasView;

public SurfaceSizePage()
{
    Title = "Surface Size";

    canvasView = new SKCanvasView();
    canvasView.PaintSurface += OnCanvasViewPaintSurface;
    Content = canvasView;
}
```

`SKCanvas` 包含六種不同 `DrawText` 的方法，但此 [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) 方法最簡單：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

您可以指定文字字串、X 和 Y 座標，其中文字的開始位置和 `SKPaint` 物件。 X 座標會指定文字左邊的位置，但請注意： Y 座標指定文字 *基準* 的位置。 如果您曾經手寫了行紙，基準就是字元所在的行，而下下行 (例如字母 g、p、q 和 y) 下降的行。

`SKPaint`物件可讓您指定文字、字型系列和文字大小的色彩。 根據預設， [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) 屬性的值為12，這會在高解析度裝置（例如手機）上產生微小的文字。 除了最簡單的應用程式之外，您還需要一些有關您所顯示文字大小的資訊。 `SKPaint`類別會定義一個 [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) 屬性和數個 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) 方法，但是針對較不特殊的需求，屬性會 [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) 提供建議的值來為連續的文字行間距。

下列 `PaintSurface` 處理常式會建立 `SKPaint` `TextSize` 40 圖元的物件，這是從 ascenders 頂端到下行底部的文字所需垂直高度。 物件傳回的 `FontSpacing` 值 `SKPaint` 稍微大於大約47圖元。

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPaint paint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 40
    };

    float fontSpacing = paint.FontSpacing;
    float x = 20;               // left margin
    float y = fontSpacing;      // first baseline
    float indent = 100;

    canvas.DrawText("SKCanvasView Height and Width:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(String.Format("{0:F2} x {1:F2}",
                                  canvasView.Width, canvasView.Height),
                    x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKCanvasView CanvasSize:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(canvasView.CanvasSize.ToString(), x + indent, y, paint);
    y += fontSpacing * 2;
    canvas.DrawText("SKImageInfo Size:", x, y, paint);
    y += fontSpacing;
    canvas.DrawText(info.Size.ToString(), x + indent, y, paint);
}
```

方法會開始第一行的文字，其 X 座標為 20 (左邊的一個小界) 和 Y 座標 `fontSpacing` ，也就是在顯示介面頂端顯示第一行文字的完整高度所需的部分。 每次呼叫之後 `DrawText` ，Y 座標會以一或兩個遞增的方式增加 `fontSpacing` 。

以下是程式執行情況：

[![[介面大小] 頁面的三重螢幕擷取畫面](pixels-images/surfacesize-small.png)](pixels-images/surfacesize-large.png#lightbox "[介面大小] 頁面的三重螢幕擷取畫面")

如您所見，在 `CanvasSize` `SKCanvasView` `Size` 報告圖元維度時，的屬性和 `SKImageInfo` 值的屬性都是一致的。 的 `Height` 和 `Width` 屬性 `SKCanvasView` 為 Xamarin.Forms 屬性，並以平臺所定義裝置獨立單位來報表檢視的大小。

左側的 iOS 7 模擬器在每個裝置獨立單位中都有兩個圖元，而中間的 Android 結點5每個單位有三個圖元。 這就是為什麼稍早顯示的簡單圓圈在不同的平臺上有不同的大小。

如果您想要完全使用與裝置無關的單位，可以將的 `IgnorePixelScaling` 屬性設定 `SKCanvasView` 為 `true` 。 不過，您可能不喜歡結果。 SkiaSharp 會以較小的裝置介面呈現圖形，其圖元大小等於裝置獨立單位中的視圖大小。  (例如，SkiaSharp 會在第5部使用 360 x 512 圖元的顯示介面。 ) 它會相應放大影像大小，進而產生明顯的點陣圖 jaggies。

為了維持相同的影像解析度，更好的解決方法是撰寫您自己的簡單函式，以便在兩個座標系統之間進行轉換。

除了 `DrawCircle` 方法之外， `SKCanvas` 也會定義兩個 `DrawOval` 繪製橢圓形的方法。 橢圓形是由兩個半徑所定義，而不是單一半徑。 這些稱為 *主要半徑* 和 *次要半徑* 。 `DrawOval`方法會繪製一個橢圓形，其中的兩個半徑平行至 X 和 Y 軸。  (如果您需要繪製的橢圓具有不平行至 X 和 Y 軸的軸，您可以使用旋轉轉換，如文章中所 [**討論的旋轉轉換或圖形**](../transforms/rotate.md) 路徑，如文章中的 [**三種繪製弧線**](../curves/arcs.md)) 的方式所述。 方法的這個多載 [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) 會命名兩個半徑參數 `rx` ，並 `ry` 指出它們平行于 X 和 Y 軸：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

是否可以繪製填滿顯示介面的橢圓形？ [ **橢圓形填滿** ] 頁面會示範如何進行。 `PaintSurface` [**EllipseFillPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)類別中的事件處理常式會從和值減去一半的筆觸寬度， `xRadius` `yRadius` 以容納整個橢圓形和其在顯示介面內的輪廓：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float strokeWidth = 50;
    float xRadius = (info.Width - strokeWidth) / 2;
    float yRadius = (info.Height - strokeWidth) / 2;

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = strokeWidth
    };
    canvas.DrawOval(info.Width / 2, info.Height / 2, xRadius, yRadius, paint);
}
```

它正在執行：

[![[介面大小] 頁面的三重螢幕擷取畫面](pixels-images/ellipsefill-small.png)](pixels-images/ellipsefill-large.png#lightbox "[介面大小] 頁面的三重螢幕擷取畫面")

另 [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) 一個方法有 [`SKRect`](xref:SkiaSharp.SKRect) 引數，這是以其左上角和右下角的 X 和 Y 座標定義的矩形。 Oval 會填滿該矩形，這表示它可能會在 **橢圓形填滿** 頁面中使用，如下所示：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

但是，會截斷四邊上橢圓形外框的所有邊緣。 您必須根據來調整所有的函式 `SKRect` 引數， `strokeWidth` 才能讓此工作正確：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)