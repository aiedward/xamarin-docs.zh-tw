---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 680c924280f8f3a6080b2fcc1968ecaf308f33a0
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138056"
---
# <a name="svg-path-data-in-skiasharp"></a>SkiaSharp 中的 SVG 路徑資料

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用可調整向量圖形格式的文字字串來定義路徑_

[`SKPath`](xref:SkiaSharp.SKPath)類別支援以可擴充向量圖形（SVG）規格所建立的格式，從文字字串定義整個路徑物件。 您稍後會在本文中看到如何在文字字串中表示整個路徑，例如：

![](path-data-images/pathdatasample.png "A sample path defined with SVG path data")

SVG 是以 XML 為基礎的 web 程式設計語言，適用于網頁。 因為 SVG 必須允許在標記中定義路徑，而不是一系列的函式呼叫，所以 SVG 標準包含非常簡潔的方法，可將整個圖形路徑指定為文字字串。

在 SkiaSharp 中，此格式稱為「SVG 路徑-資料」。 Windows XAML 架構程式設計環境中也支援格式，包括 Windows Presentation Foundation 和通用 Windows 平臺，其中稱為[路徑標記語法](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax)或[Move 和 draw 命令語法](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它也可以做為向量圖形影像的交換格式，特別是在以文字為基礎的檔案（例如 XML）。

[`SKPath`](xref:SkiaSharp.SKPath)類別會定義兩個方法，其中包含 `SvgPathData` 名稱中的單字：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

靜態 [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) 方法會將字串轉換成 `SKPath` 物件，並 [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) 將物件轉換 `SKPath` 成字串。

以下是以點（0，0）為中心的五顆星的 SVG 字串，半徑為100：

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

字母是建立物件的命令 `SKPath` ： `M` 表示 `MoveTo` 呼叫、 `L` 是 `LineTo` ，而 `Z` 是 `Close` 關閉輪廓。 每個數位配對都會提供某個點的 X 和 Y 座標。 請注意，此 `L` 命令後面會接著以逗號分隔的多個點。 在一系列的座標和點中，逗號和空白字元的處理方式相同。 有些程式設計人員偏好在 X 和 Y 座標之間加上逗號，而不是在點之間，但只需要逗號或空格來避免不明確。 這是完全合法的：

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

SVG 路徑資料的語法在[svg 規格的8.3 節](https://www.w3.org/TR/SVG11/paths.html#PathData)中正式記載。 摘要如下：

## <a name="moveto"></a>**MoveTo**

```
M x y
```

這會藉由設定目前的位置，在路徑中開始新的輪廓。 路徑資料的開頭應該一律是 `M` 命令。

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

此命令會將直線（或線條）新增至路徑，並將新的目前位置設定為最後一行的結尾。 您可以在 `L` 命令後面加上多對*x*和*y*座標。

## <a name="horizontal-lineto"></a>**水準 LineTo**

```
H x ...
```

此命令會將水平線加入至路徑，並將新的目前位置設為行尾。 您可以 `H` 使用多個*x*座標來執行命令，但它並沒有太大意義。

## <a name="vertical-line"></a>**垂直線**

```
V y ...
```

此命令會將垂直線新增至路徑，並將新的目前位置設定為行尾。

## <a name="close"></a>**關閉**

```
Z
```

此 `C` 命令會從目前位置到輪廓的開頭新增直線，以關閉輪廓。

## <a name="arcto"></a>**ArcTo**

在整個 SVG 路徑-資料規格中，將橢圓形弧線新增至輪廓的命令是目前最複雜的命令。 這是唯一的命令，其中的數位可以代表座標值以外的專案：

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx*和 *& r*參數是橢圓形的水準和垂直半徑。 *旋轉角度*的順時針是以度為單位。

將大弧線的*大弧線旗*標設定為1，或將小弧線設為0。

以順時針的方向將清除*旗*標設定為1，將順時針設為0。

弧線會繪製到點（*x*， *y*），這會變成新的目前位置。

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

此命令會將從目前位置到（*x3*， *y3*）的三次方貝茲曲線加入至新的目前位置。 點（*x1*、 *y1*）和（*x2*， *y2*）是控制點。

單一命令可以指定多個貝茲曲線 `C` 。 點數目必須是3的倍數。

另外還有一個「平滑」的貝茲曲線命令：

```
S x2 y2 x3 y3 ...
```

此命令應遵循一般的 Bezier 命令（雖然這不是絕對必要的）。 平滑的 Bezier 命令會計算第一個控制點，讓它反映出上一個貝塞爾點的第二個控制點。 因此，這三個點會 colinear，而兩個貝茲曲線之間的連接會平滑。

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

若為二次方貝茲曲線，點數目必須是2的倍數。 控制點為（*x1*， *y1*），而結束點（和新的目前位置）是（*x2*， *y2*）

另外還有一個流暢的二次方曲線命令：

```
T x2 y2 ...
```

控制點是根據前二次曲線的控制點來計算。

所有這些命令也會在「相對」版本中提供，其中的座標點會相對於目前的位置。 這些相對命令是以小寫字母開頭，例如， `c` 而不是 `C` 以協力廠商 bezier 命令的相對版本來表示。

這是 SVG 路徑資料定義的範圍。 沒有任何設備可用於重複的命令群組，或用於執行任何類型的計算。 `ConicTo`或其他類型的弧線規格的命令無法使用。

靜態 [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) 方法預期 SVG 命令的有效字串。 如果偵測到任何語法錯誤，則方法會傳回 `null` 。 這是唯一的錯誤指示。

[`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData)方法很方便從現有的物件取得 SVG 路徑資料 `SKPath` ，以傳送至另一個程式，或以文字檔案格式（例如 XML）儲存。 （ `ToSvgPathData` 此方法不會在本文的範例程式碼中示範）。*不預期會*傳回與 `ToSvgPathData` 建立路徑之方法呼叫完全對應的字串。 特別是，您會發現弧形會轉換成多個 `QuadTo` 命令，而這就是它們在所傳回之路徑資料中的顯示方式 `ToSvgPathData` 。

**Path Data Hello**頁面會使用 SVG 路徑資料來將 "hello" 這個字拼寫出來。 `SKPath`和 `SKPaint` 物件都定義為類別中的欄位 [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) ：

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

定義文字字串的路徑是從點（0，0）的左上角開始。 每個字母都是50單位寬和100單位高，而字母以另25個單位分隔，這表示整個路徑是350個單位。

"Hello" 的 ' H ' 是由 3 1 行輪廓組成，而 ' E ' 則是兩個連接的三次方貝茲曲線。 請注意， `C` 命令後面會加上六個點，而其中兩個控制點具有–10和110的 y 座標，這會將它們放在其他字母的 y 座標範圍外。 ' L ' 是兩條連接的線條，而 ' O ' 則是以命令呈現的橢圓形 `A` 。

請注意， `M` 開始最後一個等高線的命令會將位置設定為點（350，50），這是 ' O ' 左側的垂直中心。 如命令後面的第一個數位所指示 `A` ，橢圓形的水準半徑為25，而垂直半徑為50。 結束點是由命令中的最後一組數位所 `A` 表示，這代表點（300，49.9）。 這只是與起點稍有不同的地方。 如果端點設定為等於起點，將不會轉譯弧線。 若要繪製完整的橢圓形，您必須將端點設定為接近（但不等於）起點，或者您必須使用兩個或多個 `A` 命令，每個都是完整橢圓形的一部分。

您可能想要將下列語句加入至頁面的程式集，然後設定中斷點來檢查結果字串：

```csharp
string str = helloPath.ToSvgPathData();
```

您會發現 arc 已被 `Q` 使用二次方貝茲曲線的一段長串命令所取代。

`PaintSurface`處理常式會取得路徑的緊密界限，而不包含 ' E ' 和 ' O ' 曲線的控制點。 這三個轉換會將路徑的中心移至點（0，0），將路徑縮放至畫布大小（但同時將筆觸寬度納入考慮），然後將路徑的中心移至畫布的中央：

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

路徑會填滿畫布，在橫向模式中觀看時看起來更合理：

[![](path-data-images/pathdatahello-small.png "Triple screenshot of the Path Data Hello page")](path-data-images/pathdatahello-large.png#lightbox "Triple screenshot of the Path Data Hello page")

[**路徑資料 Cat** ] 頁面類似。 Path 和 paint 物件都定義為類別中的欄位 [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) ：

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

貓的標頭是圓形，在此會以兩個 `A` 命令轉譯，其中每一個都繪製一個半圓。 標頭的兩個 `A` 命令都會定義100的水準和垂直半徑。 第一個弧線從（240，100）開始，並于（240，300）結束，這會變成第二個 arc 的起點（以（240，100）為結尾）。

這兩個眼睛也會以兩個 `A` 命令轉譯，如同 cat 的標頭，第二個 `A` 命令會在第一個命令開頭的相同點結束 `A` 。 不過，這組 `A` 命令不會定義橢圓形。 具有每個弧線的是40單位，而半徑也是40單位，這表示這些弧形不是完整 semicircles。

`PaintSurface`處理常式會執行與上一個範例類似的轉換，但會設定一個 `Scale` 因素來維持外觀比例並提供一些邊界，讓貓的須線不會觸及螢幕的側邊：

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

以下是程式執行情況：

[![](path-data-images/pathdatacat-small.png "Triple screenshot of the Path Data Cat page")](path-data-images/pathdatacat-large.png#lightbox "Triple screenshot of the Path Data Cat page")

一般來說，將 `SKPath` 物件定義為欄位時，必須在此函式或其他方法中定義路徑的輪廓。 不過，使用 SVG 路徑資料時，您已看到路徑可以完全在欄位定義中指定。

[[**旋轉轉換**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)] 文章中較早的不美觀**類比時鐘**範例，會以簡單的線條顯示時鐘的手。 下面的「**類比時鐘**」程式會以 `SKPath` 類別中的欄位和物件來取代這些行 [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) `SKPaint` ：

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

小時和分鐘的手中現在有封閉的區域。 為了讓這些人彼此不同，會使用和物件繪製黑色外框和灰色填滿 `handStrokePaint` `handFillPaint` 。

在較舊的不美觀的**類比時鐘**範例中，標記為小時和分鐘的小圓圈會在迴圈中繪製。 在這個**非常類比的時鐘**範例中，會使用完全不同的方法：小時和分鐘標記是以和物件繪製的虛線 `minuteMarkPaint` `hourMarkPaint` ：

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

[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)一文會討論如何使用 [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) 方法來建立虛線。 第一個引數是 `float` 通常有兩個元素的陣列：第一個元素是虛線的長度，而第二個元素是虛線之間的間距。 當 `StrokeCap` 屬性設定為時 `SKStrokeCap.Round` ，虛線的圓角端點實際上會以虛線兩側的筆劃寬度來延長虛線長度。 因此，將第一個陣列元素設定為0會建立虛線。

這些點之間的距離是由第二個陣列元素所控制。 您很快就會看到這兩個 `SKPaint` 物件用來繪製半徑為90單位的圓形。 此圓形的圓周是180π，這表示60分鐘標記必須出現在每個3π單位，也就是陣列中的第二個值 `float` `minuteMarkPaint` 。 12小時標記必須出現在每個15π單位，也就是第二個數組中的值 `float` 。

`PrettyAnalogClockPage`類別會將計時器設定為每16毫秒使介面失效，並以 `PaintSurface` 該速率呼叫處理常式。 舊版和物件的定義 `SKPath` `SKPaint` 允許非常清楚的繪製程式碼：

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

不過，第二個東西是特別的。 由於時鐘會每隔16毫秒更新一次，因此 `Millisecond` 值的屬性 `DateTime` 可能會用來以動畫顯示清除的第二個手勢，而不是在第二個和第二個的離散跳躍中移動的。 但此程式碼不允許移動順暢。 相反地，它會使用 Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 和 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 動畫緩動函式來進行不同類型的移動。 這些緩動函式會導致第二個手以 jerkier 的方式移動，然後再進行 &mdash; 移動，然後稍微過度轉移其目的地，這種效果在下列靜態螢幕擷取畫面中很難重現：

[![](path-data-images/prettyanalogclock-small.png "Triple screenshot of the Pretty Analog Clock page")](path-data-images/prettyanalogclock-large.png#lightbox "Triple screenshot of the Pretty Analog Clock page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
