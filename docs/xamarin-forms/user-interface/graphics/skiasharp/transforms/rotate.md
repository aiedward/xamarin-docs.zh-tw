---
title: 旋轉轉換
description: 本文將探討的效果和動畫設有 SkiaSharp 旋轉轉換，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: cbb34fb4887fc3fa086fa9912d25addebd9b13f2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994996"
---
# <a name="the-rotate-transform"></a>旋轉轉換

_瀏覽的效果和動畫設有 SkiaSharp 旋轉轉換_

旋轉轉換中，使用 SkiaSharp 圖形物件具有水平和垂直軸則會中斷免費的對齊方式的條件約束：

![](rotate-images/rotateexample.png "旋轉中心周圍的文字")

輪替 SkiaSharp 同時支援點 （0，0），周圍的圖形物件[ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/)方法和[ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/)方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

360 度的圓形等同於 2 π 弧度為單位，因此很容易的兩個單位之間轉換。 使用兩者中較方便。 在靜態所有三角函數[ `Math` ](xref:System.Math)類別都使用弧度為單位的單位。

輪替是增加角度的順時針旋轉。 （雖然在笛卡兒座標系統是依照慣例以逆時針方向，順時針旋轉，是配合增加移往下的 Y 座標）。負的角度及大於允許 360 度的角度。

旋轉轉換公式是更複雜，比翻譯和小數位數。 Α 的角度，轉換公式是：

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

**基本旋轉**頁面會示範`RotateDegrees`方法。 [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)檔案會置於頁面與其基準線顯示一些文字，並且將它根據旋轉`Slider`–360 到 360 的範圍。 以下是相關的部分`PaintSurface`處理常式：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

因為的畫布，在本電腦程式中設定的大部分角度的左上角為中心旋轉文字的旋轉螢幕：

[![](rotate-images/basicrotate-small.png "基本旋轉頁面的三個螢幕擷取畫面")](rotate-images/basicrotate-large.png#lightbox "基本旋轉頁面的三個螢幕擷取畫面")

通常您會想要旋轉項目集中使用這些版本的指定的樞紐分析點周圍[ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/)並[ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/)方法：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**置中對齊旋轉**頁面是如同**基本旋轉**不同之處在於的展開的版本`RotateDegrees`用來設定旋轉中心的相同的點，用來將文字定位：

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Blue,
    TextAlign = SKTextAlign.Center,
    TextSize = 100
})
{
    canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
    canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
}
```

現在文字就會用來將文字，也就是水平置中文字的基準的定位點沿著旋轉：

[![](rotate-images/centeredrotate-small.png "置中對齊旋轉頁面的三個螢幕擷取畫面")](rotate-images/centeredrotate-large.png#lightbox "置中對齊旋轉頁面的三個螢幕擷取畫面")

如同的置中新版`Scale`方法、 置中對齊的`RotateDegrees`呼叫是捷徑：

```csharp
RotateDegrees (degrees, px, py);
```

這個運算式就相當於下列運算式：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

您會發現，您有時可以結合`Translate`呼叫`Rotate`呼叫。 例如，以下是`RotateDegrees`並`DrawText`呼叫中**置中對齊旋轉**頁面;

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`RotateDegrees`呼叫就相當於兩個`Translate`呼叫和非集中`RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`DrawText`呼叫顯示在特定位置的文字相當於`Translate`後面加上該位置的呼叫`DrawText`在點 （0，0）：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

兩個連續`Translate`呼叫相互抵銷：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

就概念而言，兩種轉換會套用的順序，相對於容量出現在程式碼的方式。 `DrawText`呼叫畫布左上角顯示的文字。 `RotateDegrees`呼叫旋轉文字的左上角相對位置。 則`Translate`呼叫文字移到畫布的正中央。

通常，有數種方式可合併旋轉和平移。 **旋轉文字**頁面會建立下列畫面：

[![](rotate-images/rotatedtext-small.png "旋轉的文字頁面的三個螢幕擷取畫面")](rotate-images/rotatedtext-large.png#lightbox "旋轉的文字頁面的三個螢幕擷取畫面")

以下是`PaintSurface`處理常式[ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs)類別：

```csharp
static readonly string text = "    ROTATE";
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Black,
        TextSize = 72
    })
    {
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);
        float yText = yCenter - textBounds.Height / 2 - textBounds.Top;

        for (int degrees = 0; degrees < 360; degrees += 30)
        {
            canvas.Save();
            canvas.RotateDegrees(degrees, xCenter, yCenter);
            canvas.DrawText(text, xCenter, yText, textPaint);
            canvas.Restore();
        }
    }
}

```

`xCenter`和`yCenter`值，表示畫布的正中央。 `yText`值是有點自的位移。 這表示，因此它是真正垂直置中對齊頁面上定位文字時所需的 Y 座標。 `for`迴圈，然後將置於畫布中央的旋轉。 輪替是以 30 度的遞增量。 使用來繪製文字`yText`值。 在詞彙之前的空格數目"旋轉"`text`值已確定廣為人知之間建立連線這些 12 的文字字串看起來似乎 dodecagon。

若要簡化此程式碼之一是將執行迴圈之後, 每次增加 30 度的旋轉角度`DrawText`呼叫。 這就不需要呼叫`Save`和`Restore`。 請注意，`degrees`主體內不再使用變數`for`區塊：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

它也可使用的簡單形式`RotateDegrees`前面引用藉由呼叫迴圈`Translate`將一切移至畫布的正中央：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

已修改`yText`計算不會再包含`yCenter`。 現在`DrawText`呼叫置中以垂直方式在畫布頂端的文字。

相對於容量出現在程式碼的方式在概念上套用轉換，因為通常很可能一開始更通用的轉換，後面接著更多的本機轉換。 這通常是最簡單的方式結合旋轉和平移。

例如，假設您想要如同憑藉其軸上旋轉中心周圍繪製旋轉圖形物件。 但您也想這個物件，以如同全球各地項 sun 上方螢幕的中心為中心。

定位畫布左上角的物件，然後再使用旋轉該角的 動畫，您可以執行這項操作。 接下來，將轉譯的物件，以水平方式像是軌道的半徑。 現在適用於第二個動畫的旋轉，則也原點為中心。 這可讓角為中心的物件。 現在會轉譯為畫布的正中央。

以下是`PaintSurface`包含這些的處理常式以相反順序轉換呼叫：

```csharp
float revolveDegrees, rotateDegrees;
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint fillPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Red
    })
    {
        // Translate to center of canvas
        canvas.Translate(info.Width / 2, info.Height / 2);

        // Rotate around center of canvas
        canvas.RotateDegrees(revolveDegrees);

        // Translate horizontally
        float radius = Math.Min(info.Width, info.Height) / 3;
        canvas.Translate(radius, 0);

        // Rotate around center of object
        canvas.RotateDegrees(rotateDegrees);

        // Draw a square
        canvas.DrawRect(new SKRect(-50, -50, 50, 50), fillPaint);
    }
}
```

`revolveDegrees`和`rotateDegrees`欄位有動畫效果。 此程式會使用以 Xamarin.Forms 為基礎的不同動畫技術`Animation`類別。 (這個類別所述[第 22 章*使用 Xamarin.Forms 建立行動應用程式*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf))`OnAppearing`覆寫會建立兩個`Animation`物件使用回呼方法，然後再呼叫`Commit`對其動畫持續時間：

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    new Animation((value) => revolveDegrees = 360 * (float)value).
        Commit(this, "revolveAnimation", length: 10000, repeat: () => true);

    new Animation((value) =>
    {
        rotateDegrees = 360 * (float)value;
        canvasView.InvalidateSurface();
    }).Commit(this, "rotateAnimation", length: 1000, repeat: () => true);
}
```

第一個`Animation`物件以動畫顯示`revolveDegrees`從 0 到 360 度超過 10 秒。 第二個動畫`rotateDegrees`從 0 到 360 度每隔 1 秒，也介面失效產生另一個呼叫`PaintSurface`處理常式。 `OnDisappearing`覆寫會取消這些兩個動畫：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

**醜陋的類比時鐘**（因此稱為因為更吸引人的類比時鐘會更新版本的文章中所述） 的程式會使用循環，來繪製時鐘的小時和分鐘標記，以及旋轉指針。 程式繪製使用任意的座標系統，根據圓形的中心點 （0，0） 半徑為 100 的時鐘。 它會使用平移和縮放，以展開和置中該頁面上的圓形。

`Translate`並`Scale`呼叫全域套用的時鐘，因此這些會初始化之後呼叫的第一個項目`SKPaint`物件：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint strokePaint = new SKPaint())
    using (SKPaint fillPaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.Color = SKColors.Black;
        strokePaint.StrokeCap = SKStrokeCap.Round;

        fillPaint.Style = SKPaintStyle.Fill;
        fillPaint.Color = SKColors.Gray;

        // Transform for 100-radius circle centered at origin
        canvas.Translate(info.Width / 2f, info.Height / 2f);
        canvas.Scale(Math.Min(info.Width / 200f, info.Height / 200f));
        ...
    }
}

```csharp
There are 60 marks of two different sizes that must be drawn in a circle around the clock. The `DrawCircle` call draws that circle at the point (0, –90), which relative to the center of the clock corresponds to 12:00. The `RotateDegrees` call increments the rotation angle by 6 degrees after every tick mark. The `angle` variable is used solely to determine if a large circle or a small circle is drawn:

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        // Hour and minute marks
        for (int angle = 0; angle < 360; angle += 6)
        {
            canvas.DrawCircle(0, -90, angle % 30 == 0 ? 4 : 2, fillPaint);
            canvas.RotateDegrees(6);
        }
    ...
    }
}
```

最後，`PaintSurface`處理常式會取得目前的時間，並計算小時、 分鐘和第二個實際操作的旋轉度數。 如此的旋轉角度是相對於，每一個指針繪製 12:00 位置：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
        DateTime dateTime = DateTime.Now;

        // Hour hand
        strokePaint.StrokeWidth = 20;
        canvas.Save();
        canvas.RotateDegrees(30 * dateTime.Hour + dateTime.Minute / 2f);
        canvas.DrawLine(0, 0, 0, -50, strokePaint);
        canvas.Restore();

        // Minute hand
        strokePaint.StrokeWidth = 10;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Minute + dateTime.Second / 10f);
        canvas.DrawLine(0, 0, 0, -70, strokePaint);
        canvas.Restore();

        // Second hand
        strokePaint.StrokeWidth = 2;
        canvas.Save();
        canvas.RotateDegrees(6 * dateTime.Second);
        canvas.DrawLine(0, 10, 0, -80, strokePaint);
        canvas.Restore();
    }
}
```

雖然手粗糙，時鐘是肯定的功能：

[![](rotate-images/uglyanalogclock-small.png "三倍不美觀的類比時鐘文字頁面的螢幕擷取畫面")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
