---
title: 在 SkiaSharp SVG 路徑資料
description: 本文說明如何定義 SkiaSharp 路徑可縮放的向量圖形格式使用文字字串，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 1D53067B-3502-4D74-B89D-7EC496901AE2
author: charlespetzold
ms.author: chape
ms.date: 05/24/2017
ms.openlocfilehash: 0453374c59c3b12842b7fb1524cc150329d84b7f
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35243970"
---
# <a name="svg-path-data-in-skiasharp"></a>在 SkiaSharp SVG 路徑資料

_定義可縮放的向量圖形格式使用文字字串的路徑_

`SKPath`類別支援向量圖形 (SVG) 規格所建立的格式從文字字串的整個路徑物件的定義。 您會看到本文中稍後可以代表這種文字字串中的整個路徑的方式：

![](path-data-images/pathdatasample.png "定義與 SVG 路徑資料的範例路徑")

SVG 是以 XML 為基礎的圖形程式設計語言的 web 網頁。 SVG 必須允許標記，而不是一系列的函式呼叫中定義的路徑，因為標準 SVG 包含非常簡潔的方式來指定為文字字串的整個圖形路徑。

內 SkiaSharp，這種格式被指 「 SVG 路徑的資料。 」 格式也支援 Windows XAML 型程式設計環境中，包括 Windows Presentation Foundation 和通用 Windows 平台，又稱為[路徑標記語法](https://msdn.microsoft.com/library/ms752293%28v=vs.110%29.aspx)或[移動繪製命令語法和](/windows/uwp/xaml-platform/move-draw-commands-syntax/)。 它也可做為向量圖形映像，特別是在以文字為基礎的檔案，例如 XML 交換格式。

SkiaSharp 定義字的兩個方法`SvgPathData`名稱中：

```csharp
public static SKPath ParseSvgPathData(string svgPath)

public string ToSvgPathData()
```

靜態[ `ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/)方法將字串轉換成`SKPath`物件，而[ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/)轉換`SKPath`物件的字串。

以下是五角星形點為中心在 （0，0） 半徑為 100 的 SVG 字串：

```csharp
"M 0 -100 L 58.8 90.9, -95.1 -30.9, 95.1 -30.9, -58.8 80.9 Z"
```

字母都是建置的命令`SKPath`物件。 `M` 指出`MoveTo`呼叫，`L`是`LineTo`，和`Z`是`Close`關閉分佈。 每一個數字的配對可提供一個點的 X 和 Y 座標。 請注意，`L`命令後面以逗號分隔的多個點。 以一系列的座標和點、 逗號和空格會視為完全相同。 某些程式設計人員想要將 X 和 Y 座標之間，而不是點之間的逗號，但是逗號或空格只需要以避免模稜兩可。 這是中完全合法的：

```csharp
"M0-100L58.8 90.9-95.1-30.9 95.1-30.9-58.8 80.9Z"
```

語法 SVG 路徑資料的正式記載於[SVG 規格的區段 8.3](http://www.w3.org/TR/SVG11/paths.html#PathData)。 摘要如下：

## <a name="moveto"></a>**MoveTo**

```csharp
M x y
```

這會開始在路徑中的新分佈設定目前的位置。 路徑資料開頭應該是一律`M`命令。

## <a name="lineto"></a>**LineTo**

```csharp
L x y ...
```

此命令會將一條直線 （或線條） 加入路徑，並設定新的目前位置的最後一行的結尾。 您可以依照`L`命令與多對*x*和*y*座標。

## <a name="horizontal-lineto"></a>**水平 LineTo**

```csharp
H x ...
```

此命令會將水平列加入路徑，並設定新的目前位置為一行的結尾。 您可以依照`H`命令與多個*x*座標，但它不太合理。

## <a name="vertical-line"></a>**垂直線條**

```csharp
V y ...
```

此命令會將一條垂直線加入路徑，並設定新的目前位置為一行的結尾。

## <a name="close"></a>**關閉**

```csharp
Z
```

`C`命令關閉分佈，方法是從目前位置的直線加入分佈的開頭。

## <a name="arcto"></a>**ArcTo**

要加入分佈的橢圓形弧線的命令是最整個 SVG 路徑資料規格中最複雜的命令。 它是唯一的命令數字可以代表座標值以外的項目：

```csharp
A rx ry rotation-angle large-arc-flag sweep-flag x y ...
```

*Rx*和*之後*參數都是水平及垂直橢圓形的半徑。 *旋轉角度*是順時針方向，以度為單位。

設定*大型弧線標幟*大型弧線 1 或 0 小弧線。

設定*掃掠旗標*為 1，進行順時針旋轉和為 0 以逆時針方向。

繪製弧形的點 (*x*， *y*)，會變成新的目前位置。

## <a name="cubicto"></a>**CubicTo**

```csharp
C x1 y1 x2 y2 x3 y3 ...
```

此命令會從目前的位置，以便新增三次方貝茲曲線 (*x3*， *y3*)，會變成新的目前位置。 點 (*x1*， *y1*) 和 (*x2*， *y2*) 是控制點。

可以指定多個貝茲曲線，由單一`C`命令。 點的數目必須是 3 的倍數。

也是 「 smooth"的貝茲曲線命令：

```csharp
S x2 y2 x3 y3 ...
```

（不過，有不是十分必要），此命令應該遵循一般的貝茲命令。 平滑的貝茲命令計算的第一個控制點，使其反映其相互點周圍先前的貝茲的第二個控制點。 這些三個點可以因此 colinear，且兩個貝茲曲線之間的連線是平滑。

## <a name="quadto"></a>**QuadTo**

```csharp
Q x1 y1 x2 y2 ...
```

二次方貝茲曲線的點數必須是 2 的倍數。 程式控制點 (*x1*， *y1*) 和結束點 （以及新的目前位置） (*x2*， *y2*)

也是 smooth 二次方曲線命令：

```csharp
T x2 y2 ...
```

控制項控點是根據前一個二次方曲線控制點計算。

所有這些命令也會提供在 「 相對 」 版本中，相對於目前的位置座標點的所在。 這些相對命令開頭是小寫字母，例如`c`而不是`C`三次方貝茲命令的相對版本。

這是 SVG 路徑資料定義的範圍。 沒有任何設備重複群組的命令或執行任何類型的計算。 命令`ConicTo`或無法使用其他類型的圓弧規格。

靜態[ `SKPath.ParseSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ParseSvgPathData/p/System.String/)方法預期的 SVG 命令的有效字串。 如果偵測到任何語法錯誤，則方法會傳回`null`。 這是唯一的錯誤指示。

[ `ToSvgPathData` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.ToSvgPathData()/)方法是很方便用來取得從現有的 SVG 路徑資料`SKPath`物件傳送至另一個程式，或將儲存在以文字為基礎的檔案格式，例如 XML。 (`ToSvgPathData`方法不會在本文中的範例程式碼中示範。)請勿*不*預期`ToSvgPathData`傳回完全對應的方法呼叫，建立路徑的字串。 特別是，您會發現弧形會轉換成多個`QuadTo`命令，而這就是它們是如何出現在所傳回的資料路徑`ToSvgPathData`。

**路徑資料 Hello**網頁魔法出文字"HELLO"的 SVG 路徑資料。 同時`SKPath`和`SKPaint`為中的欄位會定義物件[ `PathDataHelloPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataHelloPage.cs)類別：

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

定義文字字串的路徑的時間點 （0，0） 的左上角開始。 每個字母都是 50 單位的寬度和高度，100 單位與字母隔開另一個的 25 個單位，這表示的完整路徑是 350 單位的寬度。

'Hello"'H' 被組成三個行分佈，而 'E' 是兩個連接的三次方貝茲曲線。 請注意，`C`命令後面有六個點，而且兩個控制點擁有 – 10 和 110，將它們放在其他字母的 Y 座標的範圍以外的 Y 座標。 'L' 是兩個連接的直線，而 'o' 是以呈現的橢圓形`A`命令。

請注意，`M`開始的最後一個分佈的命令將位置設為點 350 （50），也就是左邊的垂直中央的那一邊 ' o '。 第一個數字下列所示`A`命令時，省略符號有水平半徑為 25 和 50 垂直半徑。 結束點由數字中的最後一個配對`A`命令，其表示的點 （300，49.9）。 這是刻意只要稍有不同的開始點。 如果端點設定為起始點，將不會轉譯弧線。 若要繪製完整橢圓形，您必須設定端點關閉 （但不是等於） 起始點，或者您必須使用兩個或多個`A`命令，每個完整的橢圓形的一部分。

您可能想要在頁面的建構函式，新增下列陳述式，然後設定 中斷點檢查結果的字串：

```csharp
string str = helloPath.ToSvgPathData();
```

您會發現弧線已被取代一長串的`Q`命令使用二次方貝茲曲線的弧線的分次近似值。

`PaintSurface`處理常式會取得緊密界限的路徑，不包括控制點 'E' 和 'o' 曲線。 三個轉換移動路徑的中心點 （0，0） 和調整其規模的路徑大小，在畫布上 （但也列入考量的筆劃寬度），然後將路徑的中心移到畫布的正中央：

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

路徑填滿畫布，看起來更合理地以橫向模式檢視時：

[![](path-data-images/pathdatahello-small.png "路徑資料 Hello 頁面的三個螢幕擷取畫面")](path-data-images/pathdatahello-large.png#lightbox "路徑資料 Hello 頁面的三個螢幕擷取畫面")

**路徑資料 Cat**頁面是類似。 路徑和小畫家物件兩者都定義為中的欄位[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)類別：

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

標頭的 cat 是圓形，以及這裡呈現具有兩個`A`命令，其中每個繪製半圓形。 同時`A`命令標頭會定義為 100 的水平和垂直半徑。 第一個弧形開始於 （240，100），並於結束 240 (300），會變成結束回於第二個弧線的起點 （240，100）。

兩個眼睛也會呈現具有兩個`A`命令，並如同 cat head，第二個`A`命令結束與開始的第一個相同點`A`命令。 不過，這些成對的`A`命令不會定義橢圓形。 每個弧形的為 40 的單位，而 radius 也是 40 單位，這表示，這些都不是完整 semicircles。

`PaintSurface`處理常式執行類似轉換為先前的範例中，但設定一個`Scale`維持外觀比例，並提供一些邊界，讓 cat 鬚不觸控螢幕的側邊的因素：

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

以下是所有三個平台上執行的程式：

[![](path-data-images/pathdatacat-small.png "路徑資料 Cat 頁面的三個螢幕擷取畫面")](path-data-images/pathdatacat-large.png#lightbox "路徑資料 Cat 頁面的三個螢幕擷取畫面")

通常，當`SKPath`物件會定義為欄位，必須定義路徑的輪廓，建構函式或另一個方法中。 當使用 SVG 路徑的資料，不過，您就已經看到可以完全在欄位定義中指定的路徑。

舊版**醜陋類比時鐘**範例[**旋轉轉換**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/rotate.md)文章為簡單的線條顯示時鐘指針。 **相當類比時鐘**下列的程式會取代這些與行`SKPath`物件定義中的欄位為[ `PrettyAnalogClockPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PrettyAnalogClockPage.cs)類別連同`SKPaint`物件：

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

小時和分鐘手現在有括區域，因此讓出手彼此相異，則繪與黑色的外框灰色的填滿使用`handStrokePaint`和`handFillPaint`物件。

在先前**醜陋類比時鐘**範例中，幾乎圓形標示的小時和分鐘的時間已繪製在迴圈中。 在這個**相當類比時鐘**範例，使用完全不同的方法： 小時和分鐘標記會以繪製虛線`minuteMarkPaint`和`hourMarkPaint`物件：

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

[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)指南討論您可以使用`SKPathEffect.CreateDash`方法來建立一條虛線。 第一個引數是`float`通常有兩個元素的陣列： 第一個項目長度的連字號，而第二個元素是連字號之間的差距。 當`StrokeCap`屬性設定為`SKStrokeCap.Round`，接著虛線的圓角的端點有效地延長虛線長度虛線的兩端的筆劃寬度。 因此，將第一個陣列元素設定為 0，會建立虛線。

這些點之間的距離係由第二個陣列元素。 您會發現在短時間內，這兩個`SKPaint`物件用來繪製圓形半徑為 90 的單位。 此圓形的圓周所以 180π，這表示 60 分鐘標記必須出現每隔 3 π 單位，這是中的第二個值`float`陣列中`minuteMarkPaint`。 12 個小時標記必須顯示每個 15π 單位，它在第二個是值`float`陣列。

`PrettyAnalogClockPage`類別設定要使其失效之介面的計時器每隔 16 毫秒，而`PaintSurface`會呼叫處理常式的速率。 先前定義的`SKPath`和`SKPaint`物件允許非常清楚的繪圖程式碼：

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

特殊項目是第二個手中，不過。 因為時鐘會更新每隔 16 毫秒，`Millisecond`屬性`DateTime`值可能用第二個手動動畫掃掠，而不是一個不連續的跳躍點移動至第二個第二個。 但是，這段程式碼不允許順暢移動。 相反地，它會使用 Xamarin.Forms [ `SpringIn` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringIn/)和[ `SpringOut` ](https://developer.xamarin.com/api/field/Xamarin.Forms.Easing.SpringOut/)動畫的 easing 函式的不同種類的移動。 這些 easing 函式會導致 jerkier 方式移動秒針&mdash;提取回很小之前它會移動，然後稍微過度疑難排解不幸的，其目的地，效果無法重現這些靜態螢幕擷取畫面中：

[![](path-data-images/prettyanalogclock-small.png "很類比時鐘頁面的三個螢幕擷取畫面")](path-data-images/prettyanalogclock-large.png#lightbox "的非常類比時鐘頁面 Triple 螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
