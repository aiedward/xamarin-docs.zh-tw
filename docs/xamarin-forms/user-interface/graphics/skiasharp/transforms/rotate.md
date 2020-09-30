---
title: 旋轉轉換
description: 本文將探討 SkiaSharp 旋轉轉換的效果和動畫效果，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CBB3CD72-4377-4EA3-A768-0C4228229FC2
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2a6e19d25cefb4b8fcd870a9b590f129b2d9d85b
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557877"
---
# <a name="the-rotate-transform"></a>旋轉轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索 SkiaSharp 旋轉轉換可以使用的效果和動畫_

使用旋轉轉換，SkiaSharp 繪圖物件會中斷與水準和垂直軸對齊的條件約束：

![圍繞中央旋轉的文字](rotate-images/rotateexample.png)

若要圍繞點 (0、0) 來旋轉繪圖物件，SkiaSharp 支援 [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single)) 方法和 [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single)) 方法：

```csharp
public void RotateDegrees (Single degrees)

public Void RotateRadians (Single radians)
```

360度的圓形與 twoπ弧度相同，因此可以輕鬆地在兩個單位之間轉換。 請使用任何便利的。 .NET 類別中的所有三角函數都 [`Math`](xref:System.Math) 使用弧度單位。

以順時針方向旋轉以增加角度。  (雖然在笛卡兒座標系統上的旋轉是依慣例依順時針方向進行的，但在 SkiaSharp 中，Y 座標會隨著 Y 座標而增加。 ) 負角度和大於360度的角度。

用於旋轉的轉換公式比轉譯和縮放的公式更為複雜。 針對α的角度，轉換公式如下：

x ' = x • cos (α) – y • sin (α)    

y ' = x • sin (α) + y • cos (α) 

**基本的旋轉**頁面會示範 `RotateDegrees` 方法。 [**BasicRotate.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicRotatePage.xaml.cs)檔案會顯示部分文字，其基準位於頁面上，並根據 `Slider` 範圍為–360到360的進行旋轉。 以下是處理常式的相關部分 `PaintSurface` ：

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

因為旋轉是以畫布左上角為中心，所以針對此程式中所設定的大部分角度而言，文字會在螢幕上旋轉：

[![基本旋轉頁面的三重螢幕擷取畫面](rotate-images/basicrotate-small.png)](rotate-images/basicrotate-large.png#lightbox "基本旋轉頁面的三重螢幕擷取畫面")

通常您會想要使用這些版本的和方法，來旋轉以指定的資料點為中心的某個東西 [`RotateDegrees`](xref:SkiaSharp.SKCanvas.RotateDegrees(System.Single,System.Single,System.Single)) [`RotateRadians`](xref:SkiaSharp.SKCanvas.RotateRadians(System.Single,System.Single,System.Single)) ：

```csharp
public void RotateDegrees (Single degrees, Single px, Single py)

public void RotateRadians (Single radians, Single px, Single py)
```

**中央旋轉**頁面就像**基本旋轉**一樣，不同之處在于擴充的版本 `RotateDegrees` 是用來將旋轉中心設定成用來放置文字的相同點：

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

現在文字會在用來放置文字的點周圍旋轉，也就是文字基準的水準中心：

[![中央旋轉頁面的三重螢幕擷取畫面](rotate-images/centeredrotate-small.png)](rotate-images/centeredrotate-large.png#lightbox "中央旋轉頁面的三重螢幕擷取畫面")

如同方法的中央版本 `Scale` ，呼叫的中央版本 `RotateDegrees` 是快捷方式。 方法如下：

```csharp
RotateDegrees (degrees, px, py);
```

該呼叫等同于下列專案：

```csharp
canvas.Translate(px, py);
canvas.RotateDegrees(degrees);
canvas.Translate(-px, -py);
```

您會發現您有時可以將 `Translate` 呼叫與 `Rotate` 呼叫合併。 例如，以下是 `RotateDegrees` `DrawText` **中央旋轉** 頁面中的和呼叫;

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

在 `DrawText` 特定位置顯示文字的呼叫相當於 `Translate` 該位置的呼叫，後面接著 `DrawText` 點 (0，0) ：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.Translate(-info.Width / 2, -info.Height / 2);
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.DrawText(Title, 0, 0, textPaint);
```

這兩個連續的 `Translate` 呼叫會取消彼此：

```csharp
canvas.Translate(info.Width / 2, info.Height / 2);
canvas.RotateDegrees((float)rotateSlider.Value);
canvas.DrawText(Title, 0, 0, textPaint);
```

就概念而言，這兩個轉換會依照其在程式碼中出現的順序來套用。 `DrawText`呼叫會顯示畫布左上角的文字。 此 `RotateDegrees` 呼叫會旋轉相對於左上角的文字。 然後，此 `Translate` 呼叫會將文字移至畫布的中心。

通常有數種方式可以結合旋轉和轉譯。 **旋轉的文字**頁面會建立下列顯示：

[![旋轉文字頁面的三重螢幕擷取畫面](rotate-images/rotatedtext-small.png)](rotate-images/rotatedtext-large.png#lightbox "旋轉文字頁面的三重螢幕擷取畫面")

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

`xCenter`和 `yCenter` 值表示畫布的中心。 此 `yText` 值與此值之間有稍微的位移。 此值是放置文字所需的 Y 座標，因此它會在頁面上以垂直方式置中。 `for`然後迴圈會根據畫布的中心設定旋轉。 旋轉以30度為單位遞增。 使用值來繪製文字 `yText` 。 在值中「旋轉」一字之前的空格數目 `text` 已決定廣為人知且實證，讓這12個文字字串之間的連接看似 dodecagon。

簡化此程式碼的其中一種方式是在呼叫之後，每次透過迴圈將旋轉角度遞增30度 `DrawText` 。 這樣就不需要呼叫 `Save` 和 `Restore` 。 請注意，此 `degrees` 變數不再用於區塊主體內 `for` ：

```csharp
for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, xCenter, yText, textPaint);
    canvas.RotateDegrees(30, xCenter, yCenter);
}

```

您也可以使用的簡單形式，方法是在 `RotateDegrees` 迴圈前面加上呼叫，將 `Translate` 所有專案移至畫布中央：

```csharp
float yText = -textBounds.Height / 2 - textBounds.Top;

canvas.Translate(xCenter, yCenter);

for (int degrees = 0; degrees < 360; degrees += 30)
{
    canvas.DrawText(text, 0, yText, textPaint);
    canvas.RotateDegrees(30);
}
```

修改過的 `yText` 計算不再合併 `yCenter` 。 現在呼叫會將文字置中在 `DrawText` 畫布頂端垂直。

因為轉換在概念上與它們出現在程式碼中的方式相反，所以通常可能會以更多的全域轉換開始，後面接著更多本機轉換。 這通常是結合旋轉和轉譯的最簡單方式。

例如，假設您想要繪製圍繞其中心旋轉的繪圖物件，就像是在其軸上旋轉的地球。 但您也想要讓這個物件繞著畫面的中央旋轉，就像是在太陽周圍的世界旋轉一樣。

若要這麼做，您可以將物件放在畫布左上角，然後使用動畫在該角落周圍旋轉。 接下來，像 orbital 半徑一樣，以水準方式轉譯物件。 現在套用第二個動畫旋轉，也就是原點周圍。 這會讓物件繞著角落。 現在轉譯為畫布的中心。

以下是 `PaintSurface` 以反向順序包含這些轉換呼叫的處理常式：

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

`revolveDegrees`和 `rotateDegrees` 欄位會以動畫顯示。 此程式使用以類別為基礎的不同動畫技術 Xamarin.Forms [`Animation`](xref:Xamarin.Forms.Animation) 。  (此類別會在[*建立) Mobile Apps Xamarin.Forms *的第22章](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch22-Apr2016.pdf)中說明，而覆寫會使用 `OnAppearing` 回呼方法建立兩個 `Animation` 物件，然後 `Commit` 在其上呼叫以取得動畫持續時間：

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

第一個 `Animation` 物件會 `revolveDegrees` 從0度到360度的10秒動畫。 第二個則是 `rotateDegrees` 每1秒從0度到360度的動畫，也會使介面失效，以產生另一個對 `PaintSurface` 處理常式的呼叫。 覆 `OnDisappearing` 寫會取消這兩個動畫：

```csharp
protected override void OnDisappearing()
{
    base.OnDisappearing();
    this.AbortAnimation("revolveAnimation");
    this.AbortAnimation("rotateAnimation");
}
```

不美觀的 **類比時鐘** 程式 (因此呼叫，因為稍後的文章中將會說明更吸引人的類比時鐘，) 使用旋轉來繪製時鐘的分鐘和小時標記，以及旋轉手。 程式會使用以圓為中心的任意座標系統（以半徑為100的點 (0，0) ）來繪製時鐘。 它會使用平移和縮放比例，在頁面上展開該圓形並置中。

`Translate`和 `Scale` 呼叫會全域套用至時鐘，因此這些是在物件初始化之後要呼叫的第一個 `SKPaint` ：

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

有兩個不同大小的60標記，必須在時鐘周圍以圓圈繪製。 此 `DrawCircle` 呼叫會在 (0，– 90) 的點繪製該圓圈，相對於時鐘的中心會對應到12:00。 呼叫會在 `RotateDegrees` 每個刻度之後，將旋轉角度遞增6度。 `angle`變數僅用來判斷是否繪製大型圓形或小型圓形：

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

最後， `PaintSurface` 處理常式會取得目前的時間，並計算小時、分鐘和第二次的旋轉度數。 每個手都會繪製在12:00 的位置，使旋轉角度相對於：

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

時鐘當然可以正常運作，雖然手中的手相當粗糙：

[![不美觀的類比時鐘文字頁面的三重螢幕擷取畫面](rotate-images/uglyanalogclock-small.png)](rotate-images/uglyanalogclock-large.png#lightbox "不美觀類比頁面的三重螢幕擷取畫面")

如需更吸引人的時鐘，請參閱 [**SkiaSharp 中的 SVG 路徑資料**](../curves/path-data.md)文章。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)