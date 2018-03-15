---
title: "將文字和圖形的整合"
description: "請參閱如何判斷要整合 SkiaSharp 圖形中的文字呈現的文字字串的大小"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B5AC82-7736-4AD8-AA16-FE43E18D203C
author: charlespetzold
ms.author: chape
ms.date: 03/10/2017
ms.openlocfilehash: 1d174e441cd46255d62283521e7db2802b49072f
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="integrating-text-and-graphics"></a>將文字和圖形的整合

_請參閱如何判斷要整合 SkiaSharp 圖形中的文字呈現的文字字串的大小_

這篇文章會示範如何測量的文字可能和調整其規模的文字來為特定的大小，整合其他圖形的文字：

![](text-images/textandgraphicsexample.png "矩形的文字")

SkiaSharp`Canvas`類別也包含方法，以繪製矩形 ([`DrawRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRect/p/SkiaSharp.SKRect/SkiaSharp.SKPaint/)) 和具有圓角矩形 ([`DrawRoundRect`](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawRoundRect/p/SkiaSharp.SKRect/System.Single/System.Single/SkiaSharp.SKPaint/))。 這些方法需要的矩形，定義為`SKRect`值。

**包覆文字**頁面中心上的頁面與包圍它與畫面格組成圓角矩形的一組的簡短文字字串。 [ `FramedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs)類別顯示其進行方式。

在您使用在 SkiaSharp`SKPaint`類別，以設定文字和字型屬性，但您也可以使用它來取得文字的呈現的大小。 下列開頭`PaintSurface`事件處理常式會呼叫兩個不同`MeasureText`方法。 第一個[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/)呼叫有一個簡單`string`引數和傳回文字的像素寬度會根據目前的字型屬性。 該程式再計算新`TextSize`屬性`SKPaint`物件會根據該呈現的寬度，目前`TextSize`屬性，並顯示區域的寬度。 這要設定`TextSize`，讓呈現在螢幕的寬度的 90%的文字字串：

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
    SKRect textBounds;
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

第二個[ `MeasureText` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.MeasureText/p/System.String/SkiaSharp.SKRect@/)呼叫有`SKRect`引數，因此它會取得寬度和高度所呈現的文字。 `Height`屬性這`SKRect`值取決於大寫字母、 上升部位，以及容納文字字串中的是否存在。 不同`Height`會報告值的文字字串"mom"、"cat"，以及"dog"，例如。

`Left`和`Top`屬性`SKRect`結構表示所呈現文字的左上角的座標，如果文字顯示`DrawText`以呼叫為 0 的 X 和 Y 位置。 例如，當此程式上執行 iPhone 7 模擬器，`TextSize`指派計算後的第一個呼叫的結果值 90.6254 `MeasureText`。 `SKRect`值取自第二個呼叫`MeasureText`具有下列屬性值：

- `Left` = 6
- `Top` = &ndash;68
- `Width` = 664.8214
- `Height` = 88;

請記住 X 和 Y 座標您傳遞給`DrawText`方法指定的基準文字左邊。 `Top`值會指出文字會擴充該基準和 （減去 68 it 從 88） 上方的 68 像素低於基準 20 像素為單位。 `Left` 6 的值會指出文字開始右邊的 X 值的 6 個像素`DrawText`呼叫。 這可讓一般字元的間距。 如果您想要顯示的左上角 snugly 顯示文字，傳遞這些否定`Left`和`Top`值的 X 和 Y 座標的`DrawText`，在此範例中， &ndash;6 和 68。

`SKRect`結構會定義數個實用的屬性和方法，其中一部分中的其餘部分使用`PaintSurface`處理常式。 `MidX`和`MidY`值表示中心之矩形的座標。 (在 iPhone 7 範例中，這些值是 338.4107 和&ndash;24。)下列程式碼會在顯示的文字置中座標的最簡單的計算中使用這些值：

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

`PaintSurface`含有兩個呼叫的處理常式結束`DrawRoundRect`，兩者都需要引數的`SKRect`。 這`SKRect`值就會確實類似`SKRect`值取自`MeasureText`方法，但它不能相同。 首先，它必須變大，而上邊緣的文字，繪製圓角的矩形，不需要移位空間中的第二，以便`Left`和`Top`值會對應至矩形即將成為左上角定位。 這些兩個工作透過`Offset`和`Inflate`所定義的方法`SKRect`:

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

接下來，此方法的其餘部分是直接的。 它會建立另一個`SKPaint`框線和呼叫物件`DrawRoundRect`兩次。 第二個呼叫會使用另一個 10 個像素膨脹的矩形。 第一次呼叫指定圓角半徑 20 像素。第二個具有圓角半徑 30 像素，讓它們看起來是平行：

 [![](text-images/framedtext-small.png "已框架處理文字頁面的三個螢幕擷取畫面")](text-images/framedtext-large.png#lightbox "框架的文字頁面的三個螢幕擷取畫面")

您可以開啟您的電話或模擬器側邊，若要查看文字和框架大小增加。

如果您只需要在螢幕上的部分文字置中，您也不該大約測量的文字，藉由設定`TextAlign`屬性`SKPaint`至`SKTextAlign.Center`。 您在中指定的 X 座標`DrawText`方法，則表示文字的水平中央位置。 如果您將畫面的中點`DrawText`方法中，文字會水平置中和*幾乎*垂直置中對齊，因為基準將垂直置中。

文字本身可以被視為更圖形化選項。 若要顯示的文字字元，而不是一般的填滿的顯示外框是一個簡單的選項：

[![](text-images/outlinedtext-small.png "螢幕擷取畫面所述的文字頁面的三倍")](text-images/outlinedtext-large.png#lightbox "三倍所述的文字頁面的螢幕擷取畫面")

這是由變更一般`Style`屬性`SKPaint`物件從其預設值為`SKPaintStyle.Fill`至`SKPaintStyle.Stroke`並指定筆劃寬度。 `PaintSurface`處理常式的**概述文字**頁面會顯示如何進行：

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
    SKRect textBounds;
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

 在過去幾篇文章，您有看到如何使用 SkiaSharp 繪製文字，現在圓形，省略符號，以及圓角的矩形。 下一個步驟是[SkiaSharp 線條和路徑](~/xamarin-forms/user-interface/graphics/skiasharp/paths/paths.md)中您將學習如何繪製圖形之路徑中的連接的直線。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
