---
title: SkiaSharp SVG 路徑資料
description: 本文說明如何定義 SkiaSharp 路徑使用可擴充的向量圖形格式的文字字串，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
ms.openlocfilehash: 467863dba2f5757e0590ccf64927ae2af292f285
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770564"
---
# <a name="svg-path-data-in-skiasharp"></a>SkiaSharp SVG 路徑資料

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_定義使用可擴充的向量圖形格式的文字字串的路徑_

[
          `SKPath`
          ](xref:SkiaSharp.SKPath)類別支援可縮放向量圖形 (SVG) 規格所建立的格式中的從文字字串的完整路徑物件的定義。 您會在本文稍後看到可以代表文字字串中的這類的整個路徑的方式：

![](path-data-images/pathdatasample.png "SVG 路徑資料所定義的範例路徑")

SVG 是以 XML 為基礎的圖形程式設計語言的網頁。 SVG 必須允許標記，而不是一系列的函式呼叫中定義的路徑，因為標準 SVG 包含極精簡方式，指定為文字字串的整個圖形路徑。

SkiaSharp，在這種格式被指 「 SVG 路徑資料。 」 在 Windows XAML 型程式設計環境，包括 Windows Presentation Foundation 和通用 Windows 平台，它即為也支援的格式[路徑標記語法](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax)或[移動繪製命令語法和](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它也可以做為向量圖形的映像，特別是在以文字為基礎的檔案，例如 XML 交換格式。

[ `SKPath` ](xref:SkiaSharp.SKPath)類別會定義上面顯示的兩種方法`SvgPathData`名稱中：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

靜態[ `ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String))方法將字串轉換成`SKPath`物件，而[ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData)轉換`SKPath`為字串的物件。

以下是五角星形以點為中心 （0，0） 半徑為 100 的 SVG 字串：

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

字母都是建置的指令`SKPath`物件：`M`指出`MoveTo`呼叫，`L`會`LineTo`，和`Z`是`Close`關閉分佈。 每一個數字組會提供資料點 X 和 Y 座標。 請注意，`L`命令後面是以逗號分隔的多個點。 在一系列的座標和點、 逗號和空格會視為相同。 某些程式設計人員想要放置逗點之間的 X 和 Y 座標，而不是點，之間，但若要避免模稜兩可只需要逗號或空格。 這是完全合法的：

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

在正式記載的 SVG 路徑資料語法[SVG 規格的章節 8.3](http://www.w3.org/TR/SVG11/paths.html#PathData)。 摘要如下：

## <a name="moveto"></a>**MoveTo**

```
M x y
```

這樣就會開始藉由設定的目前位置的路徑中的新分佈。 路徑資料應該一律會以開頭`M`命令。

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

此命令會將一條直線 （或線條） 加入至路徑，並將新的目前位置設定為最後一行的結尾。 您可以依照`L`命令搭配多個組*x*並*y*座標。

## <a name="horizontal-lineto"></a>**水平 LineTo**

```
H x ...
```

此命令會將水平列加入至路徑，並設定新的目前位置到行尾。 您可以依照`H`具有多個命令*x*座標，但它不太合理。

## <a name="vertical-line"></a>**垂直線**

```
V y ...
```

此命令會將一條垂直線加入路徑，並設定新的目前位置到行尾。

## <a name="close"></a>**關閉**

```
Z
```

`C`命令會關閉藉由從目前位置的線段的開頭加入一條直線線段。

## <a name="arcto"></a>**ArcTo**

將橢圓弧形至分佈的命令是目前為止最複雜的命令，在整個 SVG 路徑資料的規格。 它是唯一的命令數字可以代表座標值以外的項目：

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx*並*之後*參數都是橢圓形的水平和垂直半徑。 *旋轉角度*是順時針旋轉，以度為單位。

設定*大型弧線-旗標*大型弧線的 1 或 0 小型弧線。

設定*掃掠旗標*為 1，進行順時針旋轉，並為 0 表示以逆時針方向。

繪製弧形的點 (*x*， *y*)，此 guid 將成為新的目前位置。

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

此命令會從目前的位置，以新增三次方貝茲曲線 (*x3*， *y3*)，此 guid 將成為新的目前位置。 點 (*x1*， *y1*) 和 (*x2*， *y2*) 是控制點。

可以指定多個貝茲曲線，由單一`C`命令。 點的數目必須是 3 的倍數。

另外還有 「 順利 」 的貝茲曲線命令：

```
S x2 y2 x3 y3 ...
```

此命令應該遵循一般的貝茲命令 （雖然不完全是需要）。 平滑的貝茲命令計算的第一個控制點，使其先前的貝茲其相互點周圍的第二個控制點的反映。 下列三個點會因此 colinear，而且 smooth 兩條貝茲曲線之間的連線。

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

二次方貝茲曲線的點數目必須是 2 的倍數。 控制項控點是 (*x1*， *y1*) 和結束點 （以及新的目前位置） (*x2*， *y2*)

另外還有平滑二次方曲線命令：

```
T x2 y2 ...
```

控制項控點是根據先前的二次方曲線的控制點計算。

所有這些命令中也會有 「 相對 」 版本中，相對於目前的位置座標點的所在。 這些相對命令開頭為小寫字母，例如`c`而非`C`三次方貝茲命令的相對版本。

這是 SVG 路徑資料定義的範圍。 沒有任何重複的命令群組，或執行任何類型的計算的設備。 適用於命令`ConicTo`或其他類型的弧形規格無法使用。

靜態[ `SKPath.ParseSvgPathData` ](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String))方法預期的 SVG 命令有效的字串。 如果偵測到任何語法錯誤，則方法會傳回`null`。 這是唯一的錯誤指示。

[ `ToSvgPathData` ](xref:SkiaSharp.SKPath.ToSvgPathData)方法很方便取得 SVG 路徑資料從現有`SKPath`將轉移到另一個程式，或是儲存在以文字為基礎的檔案格式，例如 XML 中的物件。 (`ToSvgPathData`方法並不會在這篇文章中的範例程式碼示範。)請勿*未*預期`ToSvgPathData`傳回完全對應建立路徑的方法呼叫的字串。 特別是，您會發現弧線會轉換成多個`QuadTo`命令，這是出現在傳回的路徑資料的方式和`ToSvgPathData`。

**路徑資料 Hello**頁面魔法出文字"HELLO"使用 SVG 路徑資料。 同時`SKPath`並`SKPaint`物件會定義為欄位[ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs)類別：

```csharp
public class PathDataHelloPage : ContentPage
{
    SKPath helloPath = SKPath.ParseSvgPathData(
        "M 0 0 L 0 100 M 0 50 L 50 50 M 50 0 L 50 100" +                // H
        "M 125 0 C 60 -10, 60 60, 125 50, 60 40, 60 110, 125 100" +     // E
        "M 150 0 L 150 100, 200 100" +                                  // L
        "M 225 0 L 225 100, 275 100" +                                  // L
        "M 300 50 A 25 50 0 1 0 300 49.9 Z");                           // O

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ...
}
```

定義文字字串的路徑會在左上角，在點 （0，0） 開始。 每個字母是 50 個單位寬及 100 個單位的高度，並字母以分隔其他的 25 個單位，這表示的完整路徑是 350 單位的寬度。

'Hello"'H' 被組成三行的分佈，而 'E' 是兩個連接的三次方貝茲曲線。 請注意，`C`命令後面六個點，還有兩個控制點的 Y 座標 – 10 以及 110，將它們放在其他字母的 Y 座標的範圍之外。 'L' 是兩個連接的直線，而 'o' 是以呈現的橢圓形`A`命令。

請注意，`M`開始的最後一個分佈的命令會將位置設為點 （350，50），也就是垂直中心的左側這邊的 ' o '。 第一個數字下列所示`A`命令、 橢圓形具有水平半徑為 25，50 垂直半徑。 結束點由數字中的最後一個配對`A`命令，它代表點 （300，49.9）。 這是刻意只要稍有不同的起始點。 如果端點設定為等於起始點，將無法呈現弧形。 若要繪製完成的省略符號，您必須將端點設定接近 （但不是等於） 起始點，或者您必須使用兩個或多個`A`命令，每個完整的橢圓形的一部分。

您可能想要將下列陳述式加入至網頁的建構函式，並接著設定中斷點來檢查結果的字串：

```csharp
string str = helloPath.ToSvgPathData();
```

您會發現一長串的已取代弧線`Q`命令使用二次方貝茲曲線的弧線的分次近似值。

`PaintSurface`處理常式會取得緊密的路徑，不包含 'E' 的控點的界限和 'o' 曲線。 三個轉換移動路徑的中心點 （0，0） 和調整其規模的路徑的大小在畫布上 （但也納入考量的筆觸粗細），然後將路徑的中心移至畫布的正中央：

```csharp
public class PathDataHelloPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect bounds;
        helloPath.GetTightBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(info.Width / (bounds.Width + paint.StrokeWidth),
                     info.Height / (bounds.Height + paint.StrokeWidth));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(helloPath, paint);
    }
}
```

路徑填滿畫布，看起來更合理以橫向模式檢視時：

[![](path-data-images/pathdatahello-small.png "路徑資料 Hello 頁面三個螢幕擷取畫面")](path-data-images/pathdatahello-large.png#lightbox "路徑資料 Hello 頁面三個螢幕擷取畫面")

**路徑資料 Cat**頁面很類似。 路徑和繪製物件兩者都定義為中的欄位[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)類別：

```csharp
public class PathDataCatPage : ContentPage
{
    SKPath catPath = SKPath.ParseSvgPathData(
        "M 160 140 L 150 50 220 103" +              // Left ear
        "M 320 140 L 330 50 260 103" +              // Right ear
        "M 215 230 L 40 200" +                      // Left whiskers
        "M 215 240 L 40 240" +
        "M 215 250 L 40 280" +
        "M 265 230 L 440 200" +                     // Right whiskers
        "M 265 240 L 440 240" +
        "M 265 250 L 440 280" +
        "M 240 100" +                               // Head
        "A 100 100 0 0 1 240 300" +
        "A 100 100 0 0 1 240 100 Z" +
        "M 180 170" +                               // Left eye
        "A 40 40 0 0 1 220 170" +
        "A 40 40 0 0 1 180 170 Z" +
        "M 300 170" +                               // Right eye
        "A 40 40 0 0 1 260 170" +
        "A 40 40 0 0 1 300 170 Z");

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Orange,
        StrokeWidth = 5
    };
    ...
}
```

一隻貓的標頭是圓形，並呈現具有兩個以下`A`命令，其中每個繪製半圓形。 兩者`A`標頭會定義為 100 的水平和垂直半徑的命令。 第一個弧形開始 （240，100） 且結束於 240 (300），而成為上一步結尾的第二個弧形的開始點 （240，100）。

具有兩個也會呈現兩個眼睛`A`命令，並如同貓咪標頭，第二個`A`命令結尾的第一個開頭為相同的點`A`命令。 不過，這些成對的`A`命令不會定義橢圓形。 使用的每個弧形為 40 的單位且 radius 同時 40 的單位，這表示這些弧形不完整的 semicircles。

`PaintSurface`處理常式為先前的範例中，執行類似的轉換，但設定一個`Scale`維持外觀比例，並提供一些邊界，因此貓咪鬚線不會碰觸螢幕側邊的因素：

```csharp
public class PathDataCatPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        SKRect bounds;
        catPath.GetBounds(out bounds);

        canvas.Translate(info.Width / 2, info.Height / 2);

        canvas.Scale(0.9f * Math.Min(info.Width / bounds.Width,
                                     info.Height / bounds.Height));

        canvas.Translate(-bounds.MidX, -bounds.MidY);

        canvas.DrawPath(catPath, paint);
    }
}
```

以下是執行的程式：

[![](path-data-images/pathdatacat-small.png "路徑資料 Cat 頁面的三個螢幕擷取畫面")](path-data-images/pathdatacat-large.png#lightbox "路徑資料 Cat 頁面的三個螢幕擷取畫面")

通常，當`SKPath`物件會定義為欄位，必須在建構函式或另一種方法中定義路徑的輪廓。 當使用 SVG 路徑資料，不過，您已了解可以完全在欄位定義中指定的路徑。

稍早**醜陋的類比時鐘**範例中[**旋轉轉換**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)文章為簡單的線條顯示時鐘指針。 **很類比時鐘**下方的程式會取代使用這幾行`SKPath`定義為欄位中的物件[ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs)類別連同`SKPaint`物件：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    // Clock hands pointing straight up
    SKPath hourHandPath = SKPath.ParseSvgPathData(
        "M 0 -60 C   0 -30 20 -30  5 -20 L  5   0" +
                "C   5 7.5 -5 7.5 -5   0 L -5 -20" +
                "C -20 -30  0 -30  0 -60 Z");

    SKPath minuteHandPath = SKPath.ParseSvgPathData(
        "M 0 -80 C   0 -75  0 -70  2.5 -60 L  2.5   0" +
                "C   2.5 5 -2.5 5 -2.5   0 L -2.5 -60" +
                "C 0 -70  0 -75  0 -80 Z");

    SKPath secondHandPath = SKPath.ParseSvgPathData(
        "M 0 10 L 0 -80");

    // SKPaint objects
    SKPaint handStrokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 2,
        StrokeCap = SKStrokeCap.Round
    };

    SKPaint handFillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Gray
    };
    ...
}
```

Hour 和 minute 手現在有括住的區域。 若要讓這些實際操作彼此相異，繪製同時具有黑色的外框和使用灰色填滿`handStrokePaint`和`handFillPaint`物件。

在舊版**醜陋的類比時鐘**範例中的小圓圈標記的小時和分鐘的時間會繪製在迴圈中。 在此**很類比時鐘**範例中，會使用完全不同的方法： 小時和分鐘標記是以繪製的虛線`minuteMarkPaint`和`hourMarkPaint`物件：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    SKPaint minuteMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 3,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 3 * 3.14159f }, 0)
    };

    SKPaint hourMarkPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        StrokeWidth = 6,
        StrokeCap = SKStrokeCap.Round,
        PathEffect = SKPathEffect.CreateDash(new float[] { 0, 15 * 3.14159f }, 0)
    };
    ...
}
```

[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)篇文章討論如何使用[ `SKPathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash*)方法用來建立一條虛線。 第一個引數是`float`陣列，通常有兩個元素：第一個元素是虛線的長度，而第二個元素是虛線之間的間距。 當`StrokeCap`屬性設定為`SKStrokeCap.Round`，接著虛線的兩端筆劃寬度，由條虛線的圓角的端點有效延長的虛線長度。 因此，將第一個陣列元素設定為 0，會建立一條虛線。

這些點之間的距離係由第二個陣列項目。 如您所見，這些兩個`SKPaint`物件用來繪製圓形半徑為 90 的單位。 此圓形的圓周是因此 180π，這表示 60 分鐘標記必須出現每 3 π 單位，這是中的第二個值`float`陣列中`minuteMarkPaint`。 12 小時標記必須顯示每個 15π 單位，它在第二個是值`float`陣列。

`PrettyAnalogClockPage`類別會設定要使其失效之介面的計時器每隔 16 毫秒，而`PaintSurface`處理常式會呼叫的速率。 先前的定義`SKPath`和`SKPaint`物件允許非常清楚的繪圖程式碼：

```csharp
public class PrettyAnalogClockPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Transform for 100-radius circle in center
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(Math.Min(info.Width / 200, info.Height / 200));

        // Draw circles for hour and minute marks
        SKRect rect = new SKRect(-90, -90, 90, 90);
        canvas.DrawOval(rect, minuteMarkPaint);
        canvas.DrawOval(rect, hourMarkPaint);

        // Get time
        DateTime dateTime = DateTime.Now;

        // Draw hour hand
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawPath(hourHandPath, handStrokePaint);
        canvas.DrawPath(hourHandPath, handFillPaint);
        canvas.Restore();

        // Draw minute hand
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawPath(minuteHandPath, handStrokePaint);
        canvas.DrawPath(minuteHandPath, handFillPaint);
        canvas.Restore();

        // Draw second hand
        double t = dateTime.Millisecond / 1000.0;

        if (t < 0.5)
        {
            t = 0.5 * Easing.SpringIn.Ease(t / 0.5);
        }
        else
        {
            t = 0.5 * (1 + Easing.SpringOut.Ease((t - 0.5) / 0.5));
        }

        canvas.Save();
        canvas.RotateDegrees(6 * (dateTime.Second + (float)t));
        canvas.DrawPath(secondHandPath, handStrokePaint);
        canvas.Restore();
    }
}
```

特殊項目是第二個手中，不過。 因為在更新時鐘每隔 16 毫秒，`Millisecond`屬性`DateTime`值可能可以使用第二個手動動畫而不是一個移動不連續跳動的掃掠到第二個的第二個。 但這段程式碼不允許的移動更為順暢。 相反地，它會使用 Xamarin.Forms [ `SpringIn` ](xref:Xamarin.Forms.Easing.SpringIn)並[ `SpringOut` ](xref:Xamarin.Forms.Easing.SpringOut)動畫的 easing 函式，針對不同種類的移動。 這些 easing 函式會導致 jerkier 方式移動秒針&mdash;提取回稍微移動，並再稍微過度著眼於其目的地，效果，不幸的無法重現這些靜態的螢幕擷取畫面中：

[![](path-data-images/prettyanalogclock-small.png "很類比時鐘頁面的三個螢幕擷取畫面")](path-data-images/prettyanalogclock-large.png#lightbox "很類比時鐘頁面的三個螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
