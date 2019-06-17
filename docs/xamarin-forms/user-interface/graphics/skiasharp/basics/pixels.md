---
title: 像素與裝置獨立單位
description: 本文將探討 SkiaSharp 座標和 Xamarin.Forms 座標之間的差異，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: davidbritch
ms.author: dabritch
ms.date: 02/09/2017
ms.openlocfilehash: c1e4a76a70dcac3414d384469f25bad7908ae77f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019934"
---
# <a name="pixels-and-device-independent-units"></a>像素與裝置獨立單位

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_探索 SkiaSharp 座標和 Xamarin.Forms 座標之間的差異_

本文章探討 SkiaSharp 和 Xamarin.Forms 中使用的座標系統中的差異。 您可以取得資訊，以兩個座標系統之間進行轉換，並也繪製填滿特定區域的圖形：

![](pixels-images/screenfillexample.png "橢圓形的填滿畫面")

如果您已進行程式設計在 Xamarin.Forms 中一段時間，您可能必須了解 Xamarin.Forms 座標和大小。 在兩個先前的文章中繪製圓形似乎有點小。

這些圓形*是*小型相較於 Xamarin.Forms 大小。 根據預設，SkiaSharp 繪製像素為單位而 Xamarin.Forms 為基礎的座標和大小基礎的平台所建立的裝置獨立單位。 (Xamarin.Forms 座標系統的更多有關可在[第 5 章。因應大小](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)活頁簿*使用 Xamarin.Forms 建立行動應用程式*。)

中的網頁[ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式資格**介面大小**使用 SkiaSharp 文字輸出顯示三個不同來源的顯示表面的大小：

- 一般的 Xamarin.Forms [ `Width` ](xref:Xamarin.Forms.VisualElement.Width)並[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)屬性`SKCanvasView`物件。
- [ `CanvasSize` ](xref:SkiaSharp.Views.Forms.SKCanvasView.CanvasSize)屬性`SKCanvasView`物件。
- [ `Size` ](xref:SkiaSharp.SKImageInfo.Size)屬性`SKImageInfo`值，也就是與一致`Width`和`Height`兩個先前的頁面中所使用的屬性。

[ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)類別示範如何顯示這些值。 建構函式可節省`SKCanvasView`物件做為欄位，因此可於`PaintSurface`事件處理常式：

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

`SKCanvas` 包含六個不同`DrawText`方法，但這[ `DrawText` ](xref:SkiaSharp.SKCanvas.DrawText(System.String,System.Single,System.Single,SkiaSharp.SKPaint))是最簡單的方法：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

您指定的文字字串，其中的文字是若要開始，X 和 Y 座標和`SKPaint`物件。 X 座標指定文字的左邊位置，但監看式出：Y 座標指定的位置*基準*的文字。 如果您曾經撰寫以手動方式在劃線的紙上，基準都是一行的字元站台，而且以下的伸尾部分 （例如字母 g、 p、 q 和 y 上的那些） 下降。

`SKPaint`物件可讓您指定的文字、 字型家族和文字大小的色彩。 根據預設， [ `TextSize` ](xref:SkiaSharp.SKPaint.TextSize)屬性具有值為 12，會導致高解析度手機等裝置。 在任何東西的最簡單的應用程式，但您也需要大小一些資訊，您可以在顯示的文字。 `SKPaint`類別會定義[ `FontMetrics` ](xref:SkiaSharp.SKPaint.FontMetrics)屬性和數個[ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String))方法，但較少花俏的需求，如[ `FontSpacing` ](xref:SkiaSharp.SKPaint.FontSpacing)屬性間距連續文字行提供建議的值。

下列`PaintSurface`處理常式會建立`SKPaint`物件`TextSize`40 像素，也就是所需的伸尾部分的底部包含上格頂端的文字的垂直高度。 `FontSpacing`值`SKPaint`物件傳回大於有點，關於 47 像素為單位。

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

這個方法一開始的第一行文字與 20 （適用於在左邊的小邊界） 的 X 座標和 Y 座標的`fontSpacing`，這是比什麼，才能在顯示介面的頂端顯示完整文字的第一行的高度。 在每次呼叫之後`DrawText`，一或兩個遞增量增加的 Y 座標`fontSpacing`。

以下是執行的程式：

[![](pixels-images/surfacesize-small.png "三重的螢幕擷取畫面的介面大小頁面")](pixels-images/surfacesize-large.png#lightbox "介面大小頁面的三個螢幕擷取畫面")

如您所見，`CanvasSize`的屬性`SKCanvasView`並`Size`屬性`SKImageInfo`值都是一致中報告的像素尺寸。 `Height`並`Width`屬性的`SKCanvasView`Xamarin.Forms 屬性和報告檢視中定義的平台與裝置無關單位的大小。

左邊的 iOS 7 模擬器有兩個像素，每個裝置獨立單位，以及 Android Nexus 5 中心有三個像素，每個單位。 這就是為什麼稍早所示的簡單圓形不同平台上有不同的大小。

如果您希望完全以裝置獨立單位運作，則可以藉由設定`IgnorePixelScaling`的屬性`SKCanvasView`至`true`。 不過，您可能會不喜歡結果。 SkiaSharp 呈現較小的 「 裝置 」 介面，以像素大小等於裝置獨立單位中檢視的大小上的圖形。 （比方說，SkiaSharp 會 Nexus 5 上使用顯示介面的 360 x 512 像素為單位）。它接著會相應增加的大小，導致明顯的點陣圖 jaggies 該映像。

若要維持相同的影像解析度，更好的解決方案是撰寫您自己兩個座標系統之間進行轉換的簡單函式。

除了`DrawCircle`方法中，`SKCanvas`也會定義兩個`DrawOval`繪製橢圓形的方法。 橢圓形是由兩個的半徑，而不是單一的 radius 定義。 這些值稱為*主要 radius*並*次要 radius*。 `DrawOval`方法繪製橢圓形具有兩個半徑 X 和 Y 軸平行。 (如果您需要具有不是並行的 X 和 Y 軸的軸繪製橢圓形，如本文所述，您可以使用的旋轉變形[ **Rotatetransform** ](../transforms/rotate.md)或圖形路徑中所述發行項[**繪製弧形的三種方式**](../curves/arcs.md))。 這個多載[ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(System.Single,System.Single,System.Single,System.Single,SkiaSharp.SKPaint))方法名稱的兩個半徑參數`rx`和`ry`，表示它們是並行的 X 和 Y 軸：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

是否可以繪製橢圓形，以填滿顯示介面？ **填滿橢圓形**頁面會示範如何。 `PaintSurface`中的事件處理常式[ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)類別減去從一半筆觸粗細`xRadius`並`yRadius`值以符合整個的省略符號並將其外框內顯示介面：

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

以下執行：

[![](pixels-images/ellipsefill-small.png "三重的螢幕擷取畫面的介面大小頁面")](pixels-images/ellipsefill-large.png#lightbox "介面大小頁面的三個螢幕擷取畫面")

另[ `DrawOval` ](xref:SkiaSharp.SKCanvas.DrawOval(SkiaSharp.SKRect,SkiaSharp.SKPaint))方法具有[ `SKRect` ](xref:SkiaSharp.SKRect)引數，也就是根據其左上角和右下角的 X 和 Y 座標所定義的矩形。 Oval 填滿該矩形中，這表示，它可能可以將它用於**填滿橢圓形**頁面中的，例如：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

不過，所會截斷在四個邊的橢圓形外框的所有邊緣。 您需要調整所有`SKRect`建構函式引數根據`strokeWidth`進行這項工作的權限：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
