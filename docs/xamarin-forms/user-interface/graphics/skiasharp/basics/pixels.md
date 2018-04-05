---
title: 像素與裝置無關的單位
description: 瀏覽 SkiaSharp 座標和 Xamarin.Forms 座標之間的差異
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 26C25BB8-FBE8-4B77-B01D-16A163A16890
author: charlespetzold
ms.author: chape
ms.date: 02/09/2017
ms.openlocfilehash: e01d3e228a0684865fb09dd7a4cbb0e2f0b49125
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="pixels-and-device-independent-units"></a>像素與裝置無關的單位

_瀏覽 SkiaSharp 座標和 Xamarin.Forms 座標之間的差異_

這篇文章探討 SkiaSharp 和 Xamarin.Forms 中使用的座標系統中的差異。 您可以取得的資訊，這兩個座標系統之間進行轉換，並也繪製填滿特定區域的圖形：

![](pixels-images/screenfillexample.png "會在螢幕的橢圓形")

如果您已進行程式設計 Xamarin.Forms 中一段時間，您可能必須感覺 Xamarin.Forms 座標和大小。 在兩個先前發行項中繪製圓形似乎有點小。

這些圓形*是*相 Xamarin.Forms 大小。 根據預設，SkiaSharp 繪製以像素為單位而 Xamarin.Forms 根據座標和大小基礎的平台所建立的裝置獨立單位。(位於 Xamarin.Forms 座標系統的更多有關[第 5 章。處理大小](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter05.md)活頁簿*建立行動應用程式使用 Xamarin.Forms*。)

在頁面[ **SkewSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)程式標題為**介面大小**用於 SkiaSharp 文字輸出顯示三個不同的來源顯示介面的大小：

- 一般 Xamarin.Forms [ `Width` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Width/)和[ `Height` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Height/)屬性`SKCanvasView`物件。
- [ `CanvasSize` ](https://developer.xamarin.com/api/property/SkiaSharp.Views.Forms.SKCanvasView.CanvasSize/)屬性`SKCanvasView`物件。
- [ `Size` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Size/)屬性`SKImageInfo`值，這是與一致`Width`和`Height`用於兩個的前幾頁的內容。

[ `SurfaceSizePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/SurfaceSizePage.cs)類別示範如何顯示這些值。 此建構函式會儲存`SKCanvasView`物件做為欄位，以便在存取`PaintSurface`事件處理常式：

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

`SKCanvas` 包含六個不同`DrawText`方法，但這[ `DrawText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawText/p/System.String/System.Single/System.Single/SkiaSharp.SKPaint/)是最簡單的方法：

```csharp
public void DrawText (String text, Single x, Single y, SKPaint paint)
```

您指定的文字字串，X 和 Y 座標的文字為開頭，並以`SKPaint`物件。 X 座標指定左側文字的位置，但注意： 的 Y 座標指定的位置*基準*的文字。 如果您曾經在劃線的紙上書寫以手動方式，基準線是一行的字元 sit，而且下 （例如字母 g、 p、 q 和 y 上的那些） 哪些容納下降。

`SKPaint`物件可讓您指定的文字、 字型系列和字型色彩。 根據預設， [ `TextSize` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.TextSize/)屬性的值為 12，而導致高解析度裝置，例如手機上非常小的文字。 在以外的任何選項的最簡單的應用程式，您還需要您顯示的文字大小某些資訊。 `SKPaint`類別會定義[ `FontMetrics` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontMetrics/)屬性和數個[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/)方法，但小於美觀的需求，如[ `FontSpacing` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.FontSpacing/)屬性間距後續每一行文字提供建議的值。

下列`PaintSurface`處理常式會建立`SKPaint`物件`TextSize`為 40 像素，也就是從頂端上升部位要容納底部的文字所需的垂直高度。 `FontSpacing`值`SKPaint`物件傳回稍微大於，關於 47 像素為單位。

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

這個方法一開始的第一行的文字 （適用於在左邊的小邊界） 20 X 座標和 Y 座標的`fontSpacing`，這是比什麼是為了顯示在顯示介面的頂端第一行文字的全部高度。 若要每次呼叫之後`DrawText`，一個或兩個為增量來增加的 Y 座標`fontSpacing`。

以下是所有三個平台上執行的程式：

[![](pixels-images/surfacesize-small.png "介面大小頁面的三個螢幕擷取畫面")](pixels-images/surfacesize-large.png#lightbox "介面大小頁面的三個螢幕擷取畫面")

如您所見，`CanvasSize`屬性`SKCanvasView`和`Size`屬性`SKImageInfo`值都是一致 reporting 像素維度中。 `Height`和`Width`屬性`SKCanvasView`Xamarin.Forms 屬性和報表平台所定義的裝置獨立單位檢視的大小。

在左邊的 iOS 7 模擬器具備 2 像素為單位，每個裝置獨立單位、 Android Nexus 5 中央具有 3 像素為單位，每個單位，且右邊 Nokia Lumia 925 2.25 像素為單位，每個單位。 為什麼簡單 circle 關於相同大小的 iPhone 上顯示先前看起來，Windows phone，但較小的 Android 手機上。

如果您想要完全以裝置無關的單位運作，您可以藉由設定`IgnorePixelScaling`屬性`SKCanvasView`至`true`。 不過，您可能會不喜歡結果。 SkiaSharp 呈現較小裝置在介面上，以像素大小等於以裝置獨立單位的大小檢視的圖形。 （例如，SkiaSharp 會 Nexus 5 上使用顯示介面的 360 x 512 像素為單位）。它接著會依據該映像的大小，造成明顯的點陣圖 jaggies。

若要維持相同的影像解析度，更好的解決方案是撰寫您自己的簡單函式，兩個座標系統之間進行轉換。

除了`DrawCircle`方法，`SKCanvas`也會定義兩個`DrawOval`繪製橢圓形的方法。 由兩個半徑，而不是單一 radius 定義橢圓形。 這些值稱為*主要 radius*和*次要 radius*。 `DrawOval`方法繪製橢圓形具有兩個半徑 X 和 Y 軸平行。 這項限制可以克服轉換或使用的圖形路徑 （涵蓋的更新版本），但[這`DrawOval`方法](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/System.Single/System.Single/System.Single/System.Single/SkiaSharp.SKPaint/)名稱的兩個半徑引數`rx`和`ry`，表示它們是平行X 和 Y 軸：

```csharp
public void DrawOval (Single cx, Single cy, Single rx, Single ry, SKPaint paint)
```

它可能會繪製填滿顯示介面的橢圓形？ **填滿橢圓形**頁面會示範如何。 `PaintSurface`中的事件處理常式[ **EllipseFillPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/EllipseFillPage.xaml.cs)類別減去的筆劃寬度的一半從`xRadius`和`yRadius`值以符合整個橢圓形和其顯示介面中將概略說明：

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

這裡與其執行所在的三個平台：

[![](pixels-images/ellipsefill-small.png "介面大小頁面的三個螢幕擷取畫面")](pixels-images/ellipsefill-large.png#lightbox "介面大小頁面的三個螢幕擷取畫面")

[其他`DrawOval`方法](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawOval/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)具有[ `SGRect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKRect/)引數，這是根據其左上角和右下角的 X 和 Y 座標所定義的矩形。 Oval 填滿的矩形，這表示，它可能是可以使用在**填滿橢圓形**頁面如下：

```csharp
SKRect rect = new SKRect(0, 0, info.Width, info.Height);
canvas.DrawOval(rect, paint);
```

不過，會截斷上四個邊的橢圓形的外框的所有邊緣。 您需要調整所有`SKRect`基礎建構函式引數`strokeWidth`才能讓此作業的權限：

```csharp
SKRect rect = new SKRect(strokeWidth / 2,
                         strokeWidth / 2,
                         info.Width - strokeWidth / 2,
                         info.Height - strokeWidth / 2);
canvas.DrawOval(rect, paint);
```


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
