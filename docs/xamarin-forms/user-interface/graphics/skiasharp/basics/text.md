---
title: 整合文字與圖形
description: 這篇文章說明如何決定要與 SkiaSharp 圖形的文字新增到 Xamarin.Forms 應用程式，呈現的文字字串的大小，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: d23b4dbb97f4f98ff0361bb056e394bb7cbd941a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645649"
---
# <a name="integrating-text-and-graphics"></a>整合文字與圖形

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_了解如何決定要與 SkiaSharp 圖形中的文字轉譯的文字字串的大小_

這篇文章會示範如何測量文字檔、 調整為較特定的大小，文字及與其他圖形中的文字：

![](text-images/textandgraphicsexample.png "矩形框住的文字")

該映像也包含一個圓角的矩形。 SkiaSharp`Canvas`類別包含[ `DrawRect` ](xref:SkiaSharp.SKCanvas.DrawRect*)方法來繪製矩形並[ `DrawRoundRect` ](xref:SkiaSharp.SKCanvas.DrawRoundRect*)方法來繪製的矩形的圓角。 這些方法可讓要定義為矩形`SKRect`值或以其他方式。

**做為外框文字**頁面中心上的頁面和它與畫面格組成一組圓角矩形包圍的簡短文字字串。 [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs)類別示範如何進行。

SkiaSharp，在您使用`SKPaint`類別來設定文字和字型屬性，但您也可以使用它來取得所呈現的文字大小。 下列開頭`PaintSurface`事件處理常式會呼叫兩個不同`MeasureText`方法。 第一個[ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String))呼叫中有一個簡單的`string`引數並傳回文字的像素寬度會根據目前的字型屬性。 程式接著再計算新`TextSize`的屬性`SKPaint`物件會根據該轉譯的寬度，目前`TextSize`屬性，以及顯示區域的寬度。 此計算要設定`TextSize`以便呈現在螢幕的寬度的 90%的文字字串：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

第二個[ `MeasureText` ](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@))呼叫有`SKRect`引數，因此它會取得寬度和高度所呈現的文字。 `Height`屬性的`SKRect`值取決於是否有大寫字母，包含上格和文字字串中的伸尾部分。 不同`Height`會報告值的文字字串"mom"、"cat"，和"dog"，例如。

`Left`並`Top`的屬性`SKRect`結構表示所呈現的文字的左上角的座標，如果文字顯示`DrawText`呼叫，其為 0 的 X 和 Y 位置。 例如，當此程式是模擬器上執行 iPhone 7，`TextSize`指派之後的第一個呼叫計算的結果值 90.6254 `MeasureText`。 `SKRect`從第二個呼叫中取得值`MeasureText`具有下列屬性值：

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

請記住 X 和 Y 座標您傳遞給`DrawText`方法指定基準線文字的左邊。 `Top`值會指出文字會擴充該基準和 （減去 68 it 從 88） 的 68 個像素低於基準的 20 個像素。 `Left` 6 的值會指出文字的開端是右邊的 X 值的六個像素`DrawText`呼叫。 這可讓一般字元內間距。 如果您想要顯示的左上角 snugly 顯示文字，則會傳遞這些否定`Left`並`Top`值的 X 和 Y 座標`DrawText`，在此範例中， &ndash;6 和 68。

`SKRect`結構會定義數個實用的屬性和方法，其中一部分中的其餘部分使用`PaintSurface`處理常式。 `MidX`和`MidY`值表示的矩形的中心座標。 (在 iPhone 7 範例中，這些值為 338.4107 和&ndash;24。)下列程式碼會在顯示的文字置中座標的最簡單的計算中使用這些值：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

`SKImageInfo`資訊結構也會定義[ `Rect` ](xref:SkiaSharp.SKImageInfo.Rect)型別的屬性`SKRect`，因此您也可以計算`xText`並`yText`如下所示：

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

`PaintSurface`隨附兩個呼叫的處理常式`DrawRoundRect`，這兩者都需要引數的`SKRect`。 這`SKRect`值根據`SKRect`求值`MeasureText`方法，但它不能相同。 首先，它必須使上邊緣的文字繪製圓角的矩形，並不會變大。 它需要移位空間中的第二，以便`Left`和`Top`值會對應至所在放置矩形左上角。 這兩個作業透過[ `Offset` ](xref:SkiaSharp.SKRect.Offset*)並[ `Inflate` ](xref:SkiaSharp.SKRect.Inflate*)所定義的方法`SKRect`:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

接下來，此方法的其餘部分會是簡單易懂。 它會建立另一個`SKPaint`物件的框線和呼叫`DrawRoundRect`兩次。 第二次呼叫會使用另一個 10 個像素擴大矩形。 第一次呼叫指定 20 個像素的圓角半徑。 第二個會具有圓角半徑 30 像素，讓它們似乎平行：

 [![](text-images/framedtext-small.png "框架處理的文字頁面的三個螢幕擷取畫面")](text-images/framedtext-large.png#lightbox "框住的文字頁面的三個螢幕擷取畫面")

您可以開啟您的電話或模擬器側邊，以查看的文字和框架的大小而增加。

如果您只需要在螢幕上的部分文字置中，您可以不大約測量的文字。 相反地，設定[ `TextAlign` ](xref:SkiaSharp.SKPaint.TextAlign)屬性`SKPaint`列舉成員[ `SKTextAlign.Center` ](xref:SkiaSharp.SKTextAlign)。 您在中指定的 X 座標`DrawText`方法則表示文字的水平中央位置。 如果您傳遞至螢幕的中間點`DrawText`方法中，文字會水平置中與*幾乎*垂直置中對齊，因為基準將垂直置中。

文字可以視為更像任何其他圖形化的物件。 若要顯示的文字字元外框是一個簡單的選項：

[![](text-images/outlinedtext-small.png "三倍外框文字頁面螢幕的擷取畫面")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

這是藉由變更一般`Style`屬性`SKPaint`從其預設值的物件`SKPaintStyle.Fill`到`SKPaintStyle.Stroke`，並指定筆劃的寬度。 `PaintSurface`處理常式**外框文字**頁面會顯示如何進行：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

另一個常見的圖形物件是點陣圖。 這是一個龐大的主題涵蓋一節中深入了解[ **SkiaSharp 點陣圖**](../bitmaps/index.md)，下一篇文章中，但[ **SkiaSharp 點陣圖基本概念**](bitmaps.md)，提供簡潔的簡介。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
