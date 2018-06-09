---
title: 旋轉的轉換
description: 這篇文章探索效果和動畫的 SkiaSharp 旋轉轉換，可能和範例程式碼為其示範。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: charlespetzold
ms.author: chape
ms.date: 03/23/2017
ms.openlocfilehash: 514ecd16fedd7d3fda39fe20641cf0ee9ecb119e
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35244616"
---
# <a name="the-rotate-transform"></a>旋轉的轉換

_瀏覽的效果和 SkiaSharp 旋轉轉換的可能動畫_

和旋轉的轉換，SkiaSharp 圖形物件具有水平和垂直軸則會中斷免費提供的對齊方式的條件約束：

![](rotate-images/rotateexample.png "旋轉中心周圍的文字")

輪替繞著 SkiaSharp 同時支援點 （0，0） 的圖形物件[ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/)方法和[ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/)方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

為 360 度的圓形等同於 2 π 弧度為單位，因此很容易就能將兩個單元之間進行轉換。 使用方便的。 所有三角函數程式靜態[ `Math` ](https://developer.xamarin.com/api/type/System.Math/)類別都使用弧度為單位的單位。

輪替是增加角度的順時針旋轉。 （雖然依照慣例以逆時針方向旋轉笛卡兒座標系統上的，順時針旋轉是一致的向下增加持續的 Y 座標。）負角度和大於允許 360 度的角度。

旋轉轉換的公式是翻譯和小數位數的還要複雜。 代表角度 α 轉換公式是：

x' = x•cos(α) – y•sin(α)   

y` = x•sin(α) + y•cos(α)

**基本旋轉**頁面示範`RotateDegrees`方法。 [ `BasicRotate.xaml.cs` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)檔案某些文字顯示在頁面上置中對齊其基準線，並根據旋轉`Slider`–360 到 360 的範圍。 以下是相關的部分`PaintSurface`處理常式：

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

因為輪替為主的畫布上，設定此程式的大部分角度的左上角文字的旋轉關閉螢幕：

[![](rotate-images/basicrotate-small.png "基本旋轉頁面的三個螢幕擷取畫面")](rotate-images/basicrotate-large.png#lightbox "基本旋轉頁面的三個螢幕擷取畫面")

通常您會想要旋轉項目集中在使用這些版本的指定之樞紐點[ `RotateDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateDegrees/p/System.Single/System.Single/System.Single/)和[ `RotateRadians` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.RotateRadians/p/System.Single/System.Single/System.Single/)方法：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**置中對齊旋轉**頁面一樣是**基本旋轉**不同之處在於的展開的版本`RotateDegrees`可用來設定用來放置文字的相同點旋轉的中心點：

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

現在，文字旋轉繞著用來將位置的文字，是水平文字的基準的中心點：

[![](rotate-images/centeredrotate-small.png "旋轉置中頁面的三個螢幕擷取畫面")](rotate-images/centeredrotate-large.png#lightbox "旋轉置中頁面的三個螢幕擷取畫面")

如同的置中對齊新版`Scale`方法、 置中對齊的`RotateDegrees`呼叫是捷徑：

```csharp
RotateDegrees (degrees, px, py);
```

這個運算式就相當於下列運算式：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

您會發現，您有時可以組合`Translate`呼叫`Rotate`呼叫。 例如，以下是`RotateDegrees`和`DrawText`中呼叫**置中對齊旋轉**頁面上。

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`RotateDegrees`呼叫相當於兩個`Translate`呼叫和非集中`RotateDegrees`:

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`DrawText`來顯示文字的特定位置的呼叫就相當於`Translate`後面加上該位置呼叫`DrawText`在點 （0，0）：

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

就概念而言，兩個轉換會套用與它們是如何出現在程式碼中的順序。 `DrawText`呼叫畫布左上角顯示的文字。 `RotateDegrees`呼叫旋轉文字的左上角相對位置。 然後在`Translate`呼叫的文字移到畫布的正中央。

通常，有幾種方式可以結合旋轉和轉譯。 **旋轉文字**頁面會建立下列畫面：

[![](rotate-images/rotatedtext-small.png "旋轉的文字頁面的三個螢幕擷取畫面")](rotate-images/rotatedtext-large.png#lightbox "旋轉的文字頁面的三個螢幕擷取畫面")

以下是`PaintSurface`處理常式的[ `RotatedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs)類別：

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

`xCenter`和`yCenter`值表示畫布的正中央。 `yText`值的方式稍有不同的位移。 這表示，使得真正垂直置中頁面上定位文字時所需的 Y 座標。 `for`迴圈，則將置於畫布的正中央旋轉。 輪替是以 30 度的遞增量。 使用來繪製文字`yText`值。 空白個數字之前 「 旋轉 」`text`值來進行連接，這些 12 文字字串之間 dodecagon 似乎已實證判斷。

若要簡化此程式碼的一種方式為迴圈之後每次增加 30 度的旋轉角度`DrawText`呼叫。 這就不需要呼叫`Save`和`Restore`。 請注意，`degrees`主體內不會再使用變數`for`區塊：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

它也可使用的簡單形式`RotateDegrees`引用呼叫迴圈`Translate`將所有項目移到畫布的正中央：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

修改`yText`計算不再納入`yCenter`。 現在`DrawText`呼叫置中以垂直方式在畫布的頂端的文字。

在概念上與它們出現在程式碼的方式套用轉型，因為通常很可能一開始會更通用的轉換，後面接著本機的多個轉換。 這通常是最簡單的方式結合旋轉和轉譯。

例如，假設您想要繪製圖形物件旋轉中心憑藉旋轉的軸上非常類似。 但是，您也想要心力都圍繞如同上方周圍 sun 地球螢幕的中央此物件。

您可以經由畫布左上角位置物件，然後使用來繞該角的 動畫。 接下來，將轉譯的水平像軌道 radius 的物件。 立即套用第二個動畫的旋轉，則也周圍原點。 這可讓心力都圍繞邊角的物件。 現在將轉譯為畫布的正中央。

以下是`PaintSurface`包含這些處理常式以相反順序轉換呼叫：

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

`revolveDegrees`和`rotateDegrees`欄位有動畫效果。 此程式會使用不同的動畫技術，根據 Xamarin.Forms`Animation`類別。 (這個類別所述[章 22 的*建立行動應用程式使用 Xamarin.Forms*](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf))`OnAppearing`覆寫會建立兩個`Animation`物件與回呼方法，然後再呼叫`Commit`這些動畫持續時間：

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

第一個`Animation`物件繪製`revolveDegrees`從 0 到 360 度超過 10 秒。 第二個繪製`rotateDegrees`從 0 到 360 度每隔 1 和第二個介面失效產生另一個呼叫`PaintSurface`處理常式。 `OnDisappearing`覆寫會取消這些兩個動畫：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

**醜陋類比時鐘**（緣由更能吸引類比時鐘會更新發行項中說明） 的程式會使用循環來繪製時鐘的小時和分鐘標記並旋轉指針。 程式繪製使用任意座標系統為基礎的圓形中心的點 （0，0） 半徑為 100 的時鐘。 它會使用轉譯和調整即可展開並置中該頁面上的圓形。

`Translate`和`Scale`呼叫全域套用至時鐘，因此這些是初始化之後呼叫的第一個`SKPaint`物件：

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

最後，`PaintSurface`處理常式會取得目前的時間，並計算小時、 分鐘和第二個指針的旋轉角度。 使的旋轉角度，相對於每一個指針會繪製為 12:00 位置：

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

雖然指針粗糙，時鐘已確實功能：

[![](rotate-images/uglyanalogclock-small.png "三倍醜陋類比時鐘的文字頁面的螢幕擷取畫面")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
