---
title: 像素與裝置獨立單位
description: 本文將探討 SkiaSharp 座標與座標之間的差異 Xamarin.Forms ，並以範例程式碼示範這點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6d01018f4393ac5562220fa1f9524bc0d9872c67
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137666"
---
# <a name="pixels-and-device-independent-units"></a>像素與裝置獨立單位

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 座標與座標之間的差異 Xamarin.Forms_

本文將探討 SkiaSharp 和中所使用之座標系統的差異 Xamarin.Forms 。 您可以取得要在兩個座標系統之間轉換的資訊，同時繪製填滿特定區域的圖形：

![](pixels-images/screenfillexample.png "An oval that fills the screen")

如果您已經在中 Xamarin.Forms 進行程式設計，您可能會覺得 Xamarin.Forms 座標和大小。 這兩篇文章中所繪製的圓，對您而言可能有點小。

這些圓形*與*大小比較小 Xamarin.Forms 。 根據預設，SkiaSharp 會以圖元為單位繪製，而 Xamarin.Forms 基底座標和大小則是由基礎平臺所建立的裝置獨立單位所組成。 （如需有關座標系統的詳細資訊， Xamarin.Forms 請參閱[第5章。處理](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)使用*建立 Mobile Apps Xamarin.Forms *的書籍大小）。

[**SkewSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)程式中具有「**介面大小**」的頁面會使用 SkiaSharp 文字輸出，以顯示三個不同來源的顯示介面大小：

- 物件的一般 Xamarin.Forms [`Width`](xref:Xamarin.Forms.VisualElement.Width) 和 [`Height`](xref:Xamarin.Forms.VisualElement.Height) 屬性 `SKCanvasView` 。
- [`CanvasSize`](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)物件的屬性 `SKCanvasView` 。
- [`Size`](xref:SkiaSharp.SKImageInfo.Size)值的屬性 `SKImageInfo` ，與 `Width` `Height` 兩個先前頁面中使用的和屬性一致。

[`SurfaceSizePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)類別會顯示如何顯示這些值。 此函式會將 `SKCanvasView` 物件儲存為欄位，以便在 `PaintSurface` 事件處理常式中存取它：

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

`SKCanvas`包含六種不同 `DrawText` 的方法，但這 [`DrawText`](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint)) 是最簡單的方法：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

您可以指定文字字串、要開始文字的 X 和 Y 座標，以及 `SKPaint` 物件。 X 座標會指定文字左邊的位置，但請注意： Y 座標會指定文字*基準*的位置。 如果您曾經以手寫方式撰寫紙張，則基準會是字元所在的行，而低於下面的下行（例如字母 g、p、q 和 y）。

`SKPaint`物件可讓您指定文字的色彩、字型系列和文字大小。 根據預設， [`TextSize`](xref:SkiaSharp.SKPaint.TextSize) 屬性的值為12，這會在高解析度的裝置（例如手機）上產生微小的文字。 除了最簡單的應用程式以外，您還需要一些關於所顯示文字大小的資訊。 `SKPaint`類別會定義 [`FontMetrics`](xref:SkiaSharp.SKPaint.FontMetrics) 屬性和數個 [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) 方法，但對於較不復雜的需求，屬性會 [`FontSpacing`](xref:SkiaSharp.SKPaint.FontSpacing) 提供建議的值，讓連續的文字行間距。

下列 `PaintSurface` 處理常式會建立 `SKPaint` `TextSize` 40 圖元的物件，這是從 ascenders 頂端到下行底部所需文字的垂直高度。 物件傳回的 `FontSpacing` 值 `SKPaint` 比起大約47圖元小一點。

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

方法會開始第一行，X 座標為20（針對左邊的小界）和 Y 座標（） `fontSpacing` ，這比在顯示介面頂端顯示第一行文字的完整高度所需的少出一些。 每次呼叫之後 `DrawText` ，Y 座標會增加一或兩個增量 `fontSpacing` 。

以下是程式執行情況：

[![](pixels-images/surfacesize-small.png "Triple screenshot of the Surface Size  page")](pixels-images/surfacesize-large.png#lightbox "Triple screenshot of the Surface Size  page")

如您所見，在 `CanvasSize` `SKCanvasView` `Size` 報告圖元維度時，的屬性和 `SKImageInfo` 值的屬性是一致的。 的 `Height` 和 `Width` 屬性 `SKCanvasView` 是 Xamarin.Forms 屬性，並以平臺所定義的裝置獨立單位來報表檢視的大小。

左側的 iOS 7 模擬器在每個裝置獨立單位中有兩個圖元，而中間的 Android 結點5每個單位有三個圖元。 這就是為什麼稍早顯示的簡單圓形在不同的平臺上有不同的大小。

如果您想要完全使用裝置獨立單位，可以將的 `IgnorePixelScaling` 屬性設定 `SKCanvasView` 為 `true` 。 不過，您可能不喜歡結果。 SkiaSharp 會在較小的裝置介面上轉譯圖形，其圖元大小等於裝置獨立單位中的視圖大小。 （例如，SkiaSharp 會在 [結點 5] 上使用 360 x 512 圖元的顯示介面）。然後，它會相應增加該影像的大小，因而產生明顯的點陣圖 jaggies。

為了維持相同的影像解析度，較好的解決方案是撰寫您自己的簡單函式，以便在兩個座標系統之間進行轉換。

除了 `DrawCircle` 方法之外， `SKCanvas` 也會定義兩個 `DrawOval` 繪製橢圓形的方法。 橢圓形是由兩個半徑所定義，而不是單一半徑。 這些稱為「*主要半徑*」和「*次要半徑*」。 `DrawOval`方法會繪製一個橢圓形，其中的兩個半徑平行處理 X 和 Y 軸。 （如果您需要繪製的橢圓形具有不平行于 X 和 Y 軸的軸，您可以使用旋轉轉換一文中所述的旋轉[**轉換或圖形**](../transforms/rotate.md)路徑，如這篇[**繪製弧線的三種方式**](../curves/arcs.md)中所述）。 此方法的多載 [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint)) 會將兩個半徑參數命名 `rx` `ry` 為，以表示它們會平行處理 X 和 Y 軸：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

可以繪製填滿顯示介面的橢圓形嗎？ [**橢圓形填滿**] 頁面會示範如何進行。 `PaintSurface` [**EllipseFillPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)類別中的事件處理常式會將和值減去一半的筆劃寬度， `xRadius` `yRadius` 以符合整個橢圓形和其在顯示介面中的外框：

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

[![](pixels-images/ellipsefill-small.png "Triple screenshot of the Surface Size  page")](pixels-images/ellipsefill-large.png#lightbox "Triple screenshot of the Surface Size  page")

另 [`DrawOval`](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint)) 一個方法有 [`SKRect`](xref:SkiaSharp.SKRect) 引數，這是根據其左上角和右下角的 X 和 Y 座標來定義的矩形。 橢圓形會填滿該矩形，這表示可以在**橢圓形填滿**頁中使用它，如下所示：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

不過，這會截斷四邊之橢圓形外框的所有邊緣。 您需要根據來調整所有的函式 `SKRect` 引數， `strokeWidth` 讓此工作正確：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
