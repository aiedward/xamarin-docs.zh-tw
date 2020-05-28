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
ms.openlocfilehash: 520c4c3b61049bf17c2c964523714db196da6839
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132180"
---
# <a name="the-rotate-transform"></a>旋轉轉換

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 旋轉轉換的可能效果和動畫_

有了旋轉轉換，SkiaSharp 繪圖物件就不會有與水準和垂直軸對齊的條件約束：

![](rotate-images/rotateexample.png "Text rotated around a center")

針對圍繞點（0，0）的繪圖物件旋轉，SkiaSharp 同時支援 [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) 方法和 [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) 方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

360度的圓形與 twoπ弧度相同，因此在兩個單位之間進行轉換很容易。 使用任何方便的。 .NET 類別中的所有三角函數函式都 [`Math`](xref:System.Math) 使用弧度單位。

旋轉會順時針以增加角度。 （雖然笛卡兒座標系統上的旋轉是依慣例順時針的，但順時針旋轉與 Y 座標一致，如 SkiaSharp 中所增加）。允許負角和大於360度的角度。

用於旋轉的轉換公式比轉譯和縮放時更複雜。 針對α的角度，轉換公式為：

x ' = x • cos （α）– y • sin （α）   

y ' = x • sin （α） + y • cos （α）

[**基本旋轉**] 頁面會示範 `RotateDegrees` 方法。 [**BasicRotate.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)檔案會顯示一些文字，其基準會置中在頁面上，並根據 `Slider` 範圍為–360到360的來進行旋轉。 以下是處理常式的相關部分 `PaintSurface` ：

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

由於旋轉是以畫布左上角為中心，因此，針對此程式中設定的大部分角度，文字會從螢幕旋轉：

[![](rotate-images/basicrotate-small.png "Triple screenshot of the Basic Rotate page")](rotate-images/basicrotate-large.png#lightbox "Triple screenshot of the Basic Rotate page")

通常，您會想要使用這些版本的和方法，在指定的資料透視點周圍旋轉某個專案 [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) ：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**中央旋轉**頁面與**基本旋轉**相似，不同之處在于的擴充版本 `RotateDegrees` 是用來將旋轉中心設定為用來放置文字的相同點：

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

現在文字會繞著用來放置文字的點旋轉，這是文字基線的水準中心：

[![](rotate-images/centeredrotate-small.png "Triple screenshot of the Centered Rotate page")](rotate-images/centeredrotate-large.png#lightbox "Triple screenshot of the Centered Rotate page")

如同方法的中央版本 `Scale` ，呼叫的中央版本 `RotateDegrees` 是快捷方式。 方法如下：

```csharp
RotateDegrees (degrees, px, py);
```

該呼叫相當於下列內容：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

您會發現，有時可以 `Translate` 使用呼叫來結合呼叫 `Rotate` 。 例如，以下是 `RotateDegrees` 和 `DrawText` 呼叫**中央旋轉**頁面中的：

```csharp
canvas.RotateDegrees((float)rotateSlider.Value, info.Width / 2, info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

`RotateDegrees`呼叫相當於兩個 `Translate` 呼叫，而非中心 `RotateDegrees` ：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.DrawText(Title, info.Width / 2, info.Height / 2, textPaint);
```

在 `DrawText` 特定位置顯示文字的呼叫相當於對 `Translate` 該位置的呼叫，後面接著 `DrawText` 點（0，0）：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

兩個連續的呼叫會彼此 `Translate` 取消：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

在概念上，這兩項轉換會依照其在程式碼中出現的順序來套用。 `DrawText`呼叫會顯示畫布左上角的文字。 此 `RotateDegrees` 呼叫會旋轉相對於左上角的文字。 然後 `Translate` 通話會將文字移到畫布的中央。

通常有數種方式可以結合旋轉和轉譯。 [**旋轉的文字**] 頁面會建立下列顯示：

[![](rotate-images/rotatedtext-small.png "Triple screenshot of the Rotated Text page")](rotate-images/rotatedtext-large.png#lightbox "Triple screenshot of the Rotated Text page")

以下是 `PaintSurface` 類別的處理常式 [`RotatedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/RotatedTextPage.cs) ：

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

`xCenter`和 `yCenter` 值表示畫布的中心。 `yText`值是來自的小位移。 這個值是放置文字所需的 Y 座標，因此它真正垂直地置中在頁面上。 `for`然後，迴圈會根據畫布的中心來設定旋轉。 旋轉以30度為增量。 使用值繪製文字 `yText` 。 值中「旋轉」文字之前的空白數目 `text` 已決定廣為人知且實證，讓這12個文字字串之間的連接看起來就像是 dodecagon。

簡化此程式碼的方法之一，就是在呼叫之後，每次透過迴圈來遞增旋轉角度30度 `DrawText` 。 這樣就不需要呼叫 `Save` 和 `Restore` 。 請注意，此 `degrees` 變數不再用於區塊的主體內 `for` ：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

您也可以使用的簡單形式， `RotateDegrees` 方法是在迴圈前面加上呼叫， `Translate` 將所有專案移至畫布中央：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

修改過的 `yText` 計算不再納入 `yCenter` 。 現在， `DrawText` 呼叫會在畫布頂端垂直置中文字。

由於轉換在概念上與它們在程式碼中的顯示方式相反，因此通常可以從更多的全域轉換開始，然後再進行更多的本機轉換。 這通常是結合旋轉和轉譯的最簡單方式。

例如，假設您想要繪製圍繞其中心的繪圖物件，就像是在其軸上旋轉的地球。 但是您也想要讓這個物件繞著螢幕的中央旋轉，就像是在 sun 周圍的地球。

若要這麼做，您可以將物件放在畫布的左上角，然後使用動畫來圍繞該角落旋轉。 接下來，以水準方式轉譯物件，就像 orbital 的半徑一樣。 現在套用第二個動畫旋轉，同時也在原點周圍。 這會讓物件繞著角落旋轉。 現在轉譯成畫布的中心。

以下是 `PaintSurface` 以相反順序包含這些轉換呼叫的處理常式：

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

`revolveDegrees`和 `rotateDegrees` 欄位會以動畫顯示。 此程式會根據類別使用不同的動畫技術 Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) 。 （在[*使用 Xamarin.Forms 建立 Mobile Apps*的第22章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)中會描述此類別） `OnAppearing` 。覆寫會建立兩個 `Animation` 具有回呼方法的物件，然後 `Commit` 在其上呼叫以取得動畫持續時間：

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

第一個 `Animation` 物件在 `revolveDegrees` 10 秒內從0度到360度的動畫。 第二個是 `rotateDegrees` 每1秒從0度到360度的動畫，也會使表面失效，以產生另一個呼叫 `PaintSurface` 處理常式。 覆 `OnDisappearing` 寫會取消這兩個動畫：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

不太好的**類比時鐘**程式（因為後續的文章將會說明更吸引人的類比時鐘），會使用旋轉來繪製時鐘的分鐘和小時標記，並旋轉手。 此程式會使用任意座標系統繪製時鐘，此角度是以半徑為100的點（0，0）為中心的圓角。 它會使用平移和縮放比例，在頁面上展開並置中該圓形。

`Translate`和 `Scale` 呼叫會全域套用至時鐘，因此這些是在物件初始化之後所呼叫的第一個 `SKPaint` ：

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
```

有兩個不同大小的60標記必須在時鐘周圍以圓形繪製。 此 `DrawCircle` 呼叫會在點（0，–90）繪製該圓形，相對於時鐘中心，其對應于12:00。 `RotateDegrees`呼叫會以每個刻度後的6度遞增旋轉角度。 `angle`變數僅用於判斷是否繪製大型圓形或小圓形：

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

最後， `PaintSurface` 處理常式會取得目前的時間，並計算小時、分鐘和第二個手的旋轉度數。 每個手勢都會在12:00 位置繪製，讓旋轉角度相對於：

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

雖然實際操作相當粗糙，但時鐘仍可正常運作：

[![](rotate-images/uglyanalogclock-small.png "Triple screenshot of the Ugly Analog Clock Text page")](rotate-images/uglyanalogclock-large.png#lightbox "Triple screenshot of the Ugly Analog page")

如需更具吸引力的時鐘，請參閱[**SkiaSharp 中的 SVG 路徑資料**](../curves/path-data.md)一文。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
