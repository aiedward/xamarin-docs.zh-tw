---
title: SkiaSharp 中的 SVG 路徑資料
description: 本文說明如何使用可縮放向量圖形格式的文字字串來定義 SkiaSharp 路徑，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12af5d9be025fb2113d70a93a364619aff75598d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370061"
---
# <a name="svg-path-data-in-skiasharp"></a>SkiaSharp 中的 SVG 路徑資料

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用可調整向量圖形格式的文字字串來定義路徑_

[`SKPath`](xref:SkiaSharp.SKPath)類別支援以可擴充向量圖形所建立的格式，從文字字串定義整個路徑物件 (SVG) 規格。 您稍後會在本文中看到如何以文字字串表示整個路徑，例如：

![以 SVG 路徑資料定義的範例路徑](path-data-images/pathdatasample.png)

SVG 是適用于網頁的 XML 架構圖形程式設計語言。 由於 SVG 必須允許在標記中定義路徑，而不是一系列的函式呼叫，因此 SVG 標準包含了一種非常簡潔的方法，將整個圖形路徑指定為文字字串。

在 SkiaSharp 中，此格式稱為「SVG 路徑資料」。 Windows XAML 型程式設計環境中也支援此格式，包括 Windows Presentation Foundation 和通用 Windows 平臺，其中稱為 [路徑標記語法](/dotnet/framework/wpf/graphics-multimedia/path-markup-syntax) 或 [Move 和 draw 命令語法](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它也可以做為向量圖形影像的交換格式，特別是在以文字為基礎的檔案（例如 XML）中。

類別會以 [`SKPath`](xref:SkiaSharp.SKPath) 名稱中的單字定義兩個方法 `SvgPathData` ：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

靜態 [`ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) 方法會將字串轉換成 `SKPath` 物件，並 [`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData) 將物件轉換 `SKPath` 成字串。

以下是以100為中心的五指向星形的 SVG 字串， (0，0) ，半徑為：

```
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

這些字母是建立物件的命令 `SKPath` ： `M` 表示 `MoveTo` 呼叫、 `L` 為 `LineTo` ，以及 `Z` `Close` 關閉輪廓。 每個數位配對都會提供點的 X 和 Y 座標。 請注意， `L` 命令後面會加上以逗號分隔的多個點。 在一系列的座標和點中，逗號和空白字元的處理方式相同。 某些程式設計人員偏好在 X 和 Y 座標（而不是點之間）之間放置逗號，但只需要逗號或空格來避免混淆。 這是完全合法的：

```
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

SVG 路徑資料的語法正式記載于 [svg 規格的8.3 節](https://www.w3.org/TR/SVG11/paths.html#PathData)中。 摘要如下：

## <a name="moveto"></a>**MoveTo**

```
M x y
```

這會藉由設定目前的位置，在路徑中開始新的輪廓。 路徑資料的開頭應該一律是 `M` 命令。

## <a name="lineto"></a>**LineTo**

```
L x y ...
```

此命令會將直線 (或行) 至路徑，並將新的目前位置設定為最後一行的結尾。 您可以在 `L` 命令後面加上多對 *x* 和 *y* 座標。

## <a name="horizontal-lineto"></a>**水準 LineTo**

```
H x ...
```

此命令會將水平線新增至路徑，並將新的目前位置設定為該行的結尾。 您可以在 `H` 命令後面加上多個 *x* 座標，但它並沒有太大的意義。

## <a name="vertical-line"></a>**垂直線**

```
V y ...
```

此命令會將垂直線新增至路徑，並將新的目前位置設定為該行的結尾。

## <a name="close"></a>**關閉**

```
Z
```

此 `C` 命令會將從目前位置開始到輪廓開頭的直線，以關閉輪廓。

## <a name="arcto"></a>**ArcTo**

在整個 SVG 路徑資料規格中，將橢圓弧線新增到輪廓的命令是目前為止最複雜的命令。 它是唯一的命令，其中的數位可以代表座標值以外的值：

```
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx* 和 *ry* 參數是橢圓形的水準和垂直半徑。 *旋轉角度* 以度為單位，以度為單位。

將大型弧線的 *大弧線旗* 標設為1，或針對小弧線設定為0。

將順時針設定的清除 *旗* 標設為1，並以逆時針的方向設定為0。

弧線會繪製到 ( *x* ， *y* ) 的點，而這會成為新的目前位置。

## <a name="cubicto"></a>**CubicTo**

```
C x1 y1 x2 y2 x3 y3 ...
```

此命令會將目前位置的三次貝茲曲線新增至 ( *x3* ， *Y3* ) ，這會成為新的目前位置。  ( *x1* 、 *y1* ) 和 ( *x2* 的點， *y2* ) 是控制點。

單一命令可以指定多個貝茲曲線 `C` 。 點數數目必須是3的倍數。

另外還有一個「平滑」貝茲曲線命令：

```
S x2 y2 x3 y3 ...
```

此命令應遵循一般的 Bezier 命令 (，但這並不是絕對必要的) 。 平滑式貝塞爾命令會計算第一個控制點，讓它反映出前一個貝塞爾點圍繞其相互點的第二個控制點。 因此，這三個點會 colinear，而兩個貝茲曲線之間的連接會平滑。

## <a name="quadto"></a>**QuadTo**

```
Q x1 y1 x2 y2 ...
```

針對二次方貝茲曲線，點數目必須是2的倍數。 控制點是 ( *x1* 、 *y1* ) 和結束點 (，而新的目前位置)  ( *x2* ， *y2* ) 

另外還有一個流暢的二曲線命令：

```
T x2 y2 ...
```

控制點是根據上一個二次曲線的控制點來計算。

所有這些命令也可在「相對」版本中使用，其中座標點相對於目前的位置。 這些相對的命令是以小寫字母開頭，例如， `c` 而不是 `C` 針對三次方貝塞爾命令的相對版本。

這是 SVG 路徑資料定義的範圍。 不需要重複的命令群組或執行任何類型的計算。 `ConicTo`或其他類型的弧線規格的命令都無法使用。

靜態 [`SKPath.ParseSvgPathData`](xref:SkiaSharp.SKPath.ParseSvgPathData(System.String)) 方法需要 SVG 命令的有效字串。 如果偵測到任何語法錯誤，則方法會傳回 `null` 。 這是唯一的錯誤指示。

[`ToSvgPathData`](xref:SkiaSharp.SKPath.ToSvgPathData)方法可讓您從現有的物件取得 SVG 路徑資料 `SKPath` ，以傳送至另一個程式，或儲存為以文字為基礎的檔案格式（例如 XML）。  (`ToSvgPathData` 不會在本文的範例程式碼中示範方法。 ) *不會* 預期傳回與 `ToSvgPathData` 建立路徑之方法呼叫完全對應的字串。 尤其是，您會發現弧線轉換成多個 `QuadTo` 命令，而這就是它們在傳回的路徑資料中出現的方式 `ToSvgPathData` 。

**Path Data Hello** 頁面會使用 SVG 路徑資料來將 "Hello" 這個字拼寫出來。 `SKPath`和 `SKPaint` 物件都會定義為類別中的欄位 [`PathDataHelloPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs) ：

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

定義文字字串的路徑是從左上角的位置開始， (0，0) 。 每個字母都是50個單位寬和100個單位的高度，而字母則會以另一個25個單位隔開，這表示整個路徑為350單位寬。

"Hello" 的 ' H ' 是由 3 1 行輪廓所組成，而 ' E ' 是兩個連接的三次方貝茲曲線。 請注意，此 `C` 命令後面會加上六個點，而兩個控制點的 y 座標為-10 和110，這會將它們放在其他字母的 y 座標範圍之外。 ' L ' 是兩個連接的行，而 ' O ' 是使用命令轉譯的橢圓形 `A` 。

請注意， `M` 開始最後一個輪廓的命令會將位置設定為點 (350，50) ，也就是 ' O ' 左邊的垂直中心。 如命令後面的第一個數位所指示 `A` ，橢圓形的水準半徑為25，而垂直半徑為50。 結束點是由命令中的最後一組數位所表示 `A` ，表示 (300，49.9) 的點。 這其實與起點稍微不同。 如果端點設定等於起始點，則不會轉譯弧線。 若要繪製完整的橢圓形，您必須將端點設定為接近 (但不等於) 起點，或是您必須使用兩個以上的 `A` 命令，每個命令都是完整橢圓形的一部分。

您可能會想要將下列語句加入至頁面的函式，然後設定中斷點來檢查結果字串：

```csharp
string str = helloPath.ToSvgPathData();
```

您將會發現， `Q` 使用二次方貝茲曲線，以長串的一連串命令取代弧線。

`PaintSurface`處理常式會取得路徑的緊密界限，而不包含 ' E ' 和 ' O ' 曲線的控制點。 這三個轉換會將路徑的中心移至 (0，0) 的點，並將路徑縮放至 (畫布的大小，但也將筆劃寬度改為帳戶) ，然後將路徑的中心移至畫布的中心：

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

[![Path Data Hello 頁面的三重螢幕擷取畫面](path-data-images/pathdatahello-small.png)](path-data-images/pathdatahello-large.png#lightbox "Path Data Hello 頁面的三重螢幕擷取畫面")

**路徑資料貓** 頁面很類似。 路徑和繪圖物件都定義為類別中的欄位 [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) ：

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

貓的標頭是一個圓形，在這裡會使用兩個 `A` 命令來轉譯，每個命令都會繪製一個半圓。 標頭的兩個 `A` 命令都會定義100的水準和垂直半徑。 第一個弧線開始于 (240，100) ，結束于 (240，300) ，它會變成第二個弧線的起點，而第二個弧線的起點是 (240，100) 。

這兩個眼睛也會使用兩個命令來轉譯 `A` ，如同貓的標頭一樣，第二個 `A` 命令會在與第一個命令開始的相同位置結束 `A` 。 不過，這兩組 `A` 命令不會定義橢圓形。 每個弧線的會是40單位，半徑也是40單位，這表示這些弧線並非完整 semicircles。

此 `PaintSurface` 處理常式會執行與上一個範例類似的轉換，但會設定單一 `Scale` 因數來維持外觀比例，並提供微小的邊界，讓貓的須線不會接觸畫面的側邊：

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

[![路徑資料貓頁面的三重螢幕擷取畫面](path-data-images/pathdatacat-small.png)](path-data-images/pathdatacat-large.png#lightbox "路徑資料貓頁面的三重螢幕擷取畫面")

一般來說，當 `SKPath` 物件定義為欄位時，必須在函式或另一個方法中定義路徑的輪廓。 但是，當您使用 SVG 路徑資料時，您已看到可以完全在欄位定義中指定路徑。

「 [**旋轉轉換**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)」一文中較早不美觀的 **類比時鐘** 範例，會將時鐘的手以簡單的線條顯示。 下列 **相當的類比時鐘** 程式會使用 `SKPath` 定義為類別中欄位的物件和物件來取代這些行 [`PrettyAnalogClockPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs) `SKPaint` ：

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

小時與分鐘的手現在都有封閉的區域。 若要讓這些指標彼此相異，則會使用和物件繪製黑色外框和灰色填滿 `handStrokePaint` `handFillPaint` 。

在較早的 **類比時鐘** 範例中，標示為小時和分鐘的小圓形會在迴圈中繪製。 在這個 **相當的類比時鐘** 範例中，會使用完全不同的方法：小時和分鐘標記是以和物件繪製的點線 `minuteMarkPaint` `hourMarkPaint` ：

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

[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)文章討論了如何使用 [`SKPathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash*) 方法來建立虛線。 第一個引數是 `float` 通常有兩個元素的陣列：第一個元素是虛線的長度，而第二個元素是虛線之間的間距。 當 `StrokeCap` 屬性設定為時 `SKStrokeCap.Round` ，虛線的圓角端會有效地將虛線長度延長為虛線兩側的筆觸寬度。 因此，將第一個陣列元素設定為0會建立虛線。

這些點之間的距離是由第二個陣列元素所控管。 您很快就會發現，這兩個 `SKPaint` 物件是用來繪製半徑為90單位的圓形。 因此，這個圓形的圓周是180π，這表示60分鐘標記必須出現在每個3π單位，也就是陣列中的第二個值 `float` `minuteMarkPaint` 。 12小時標記必須出現在每個15π單位，也就是第二個數組中的值 `float` 。

`PrettyAnalogClockPage`類別會設定計時器，使介面每16毫秒失效一次，並 `PaintSurface` 以該速率呼叫處理常式。 和物件的較早 `SKPath` 定義 `SKPaint` 可以提供非常乾淨的繪圖程式碼：

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

但是，第二個部分就是特別的事情。 由於時鐘會每隔16毫秒更新一次，因此 `Millisecond` 值的屬性 `DateTime` 可能會用來將清理的第二個手勢（而不是從第二個移至第二個）移至另一個。 但這段程式碼不允許移動變得更順暢。 相反地，它會使用 Xamarin.Forms [`SpringIn`](xref:Xamarin.Forms.Easing.SpringIn) 和 [`SpringOut`](xref:Xamarin.Forms.Easing.SpringOut) 動畫簡化函式以進行不同類型的移動。 這些簡化函式會導致第二個手以 jerkier 的方式移動，然後在 &mdash; 移動之前先拉出一小部分，然後稍微過度移出它的目的地，這在這些靜態螢幕擷取畫面中很可惜無法重現的效果：

[![相當於類比時鐘頁面的三重螢幕擷取畫面](path-data-images/prettyanalogclock-small.png)](path-data-images/prettyanalogclock-large.png#lightbox "相當於類比時鐘頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)