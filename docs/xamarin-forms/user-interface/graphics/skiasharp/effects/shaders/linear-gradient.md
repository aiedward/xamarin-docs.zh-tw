---
title: SkiaSharp 線性漸層
description: 了解如何繪製行，或使用兩種色彩逐漸 blend 所組成的漸層填滿區域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 20A2A8C4-FEB7-478D-BF57-C92E26117B6A
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 9551a3b8e093dbb49a55a3761543602c40e81023
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158397"
---
# <a name="the-skiasharp-linear-gradient"></a>SkiaSharp 線性漸層

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

[ `SKPaint` ](xref:SkiaSharp.SKPaint)類別會定義[ `Color` ](xref:SkiaSharp.SKPaint.Color)用來繪製線條，或使用純色填滿區域的屬性。 或者繪製線條或填滿的區域_漸層_，這是漸進式混合色彩：

![線性漸層停駐範例](linear-gradient-images/LinearGradientSample.png "線性漸層停駐的範例")

最基本的漸層的類型是_線性_漸層停駐。 色彩的混合，就會發生在行 (稱為_漸層線_) 從另一個點。 與 axis 成漸層線的線條會具有相同的色彩。 建立線形漸層使用其中兩個靜態[ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient*)方法。 其中包括矩陣的轉換，和其他沒有兩個多載之間的差異。 

這些方法會傳回類型的物件[ `SKShader` ](xref:SkiaSharp.SKShader)設為[ `Shader` ](xref:SkiaSharp.SKPaint.Shader)屬性`SKPaint`。 如果`Shader`屬性為非 null，它會覆寫`Color`屬性。 任何描邊的一行或任何使用此填滿的區域`SKPaint`物件為基礎的漸層，而不是色彩的純色。

> [!NOTE]
> `Shader`屬性會被忽略，當您將包含`SKPaint`物件中`DrawBitmap`呼叫。 您可以使用`Color`屬性`SKPaint`若要設定顯示點陣圖的透明度層級 (文件中所述[顯示 SkiaSharp 點陣圖](../../bitmaps/displaying.md#displaying-in-pixel-dimensions))，但是您無法使用`Shader`顯示的屬性漸層的透明度與點陣圖。 其他技術可供顯示使用漸層停駐的投影片的點陣圖：這些文章中所述[SkiaSharp 循環的漸層](circular-gradients.md#radial-gradients-for-masking)並[SkiaSharp 複合 （compositing） 和 blend 模式](../blend-modes/porter-duff.md#gradient-transparency-and-transitions)。

## <a name="corner-to-corner-gradients"></a>角-漸層

通常線形漸層從一個矩形角延伸到另一個。 如果矩形左上角的起始點，可以擴充之漸層：

- 垂直至左下角
- 水平擴展至右上角
- 右下方的對角線，

對角線性漸層所示的第一頁**SkiaSharp 著色器和其他效果**一節[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例。 **角-漸層** 頁面建立`SKCanvasView`其建構函式中。 `PaintSurface`處理常式會建立`SKPaint`物件中`using`陳述式，然後定義置於畫布的 300 像素正方形的矩形：

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    ···
    public CornerToCornerGradientPage ()
    {
        Title = "Corner-to-Corner Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ···
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create 300-pixel square centered rectangle
            float x = (info.Width - 300) / 2;
            float y = (info.Height - 300) / 2;
            SKRect rect = new SKRect(x, y, x + 300, y + 300);

            // Create linear gradient from upper-left to lower-right
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { SKColors.Red, SKColors.Blue },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Repeat);

            // Draw the gradient on the rectangle
            canvas.DrawRect(rect, paint);
            ···
        }
    }
}
```

`Shader`的屬性`SKPaint`指派`SKShader`傳回的值從靜態`SKShader.CreateLinearGradient`方法。 五個引數如下所示：

- 矩形左上角這裡設漸層的起始點
- 矩形右下角這裡設漸層的結束點
- 兩個或多個參與的漸層的色彩的陣列
- 陣列`float`值，表示的色彩在漸層線的相對位置
- 成員[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)列舉類型，表示漸層線結束部分超出漸層的運作方式

建立漸層停駐的物件之後，`DrawRect`方法可讓您繪製 300 像素的正方形矩形使用`SKPaint`物件，其中包含著色器。 這裡執行 iOS、 Android 和通用 Windows 平台 (UWP) 上：

[![角-漸層](linear-gradient-images/CornerToCornerGradient.png "角-漸層")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

指定為前兩個引數的兩個點定義漸層線。 請注意，這些點的相對_畫布_並_不_圖形顯示使用之漸層的物件。 沿著漸層的線條、 色彩，逐漸轉換從左上方為藍色右下方的紅色。 垂直漸層停駐列於任何一行有常數的色彩。

陣列`float`指定為第四個引數的值有一對一的對應色彩的陣列。 值，表示這些色彩的發生位置的相對位置沿著漸層線。 在這裡，0 表示`Red`就會發生在漸層停駐列，開頭和 1 則表示`Blue`結尾的行，就會發生。 數字必須遞增排序，並應在 0 到 1 的範圍內。 如果沒有在該範圍內，它們將會調整為在該範圍內。

陣列中的兩個值可以設定為，0 和 1 以外。 試試這個：

```csharp
new float[] { 0.25f, 0.75f }
```

現在整個第一季的漸層線純紅色，而最後一季純藍色。 漸層線的中央部分只有紅色和藍色的組合。

一般而言，您會想空間同樣是介於 0 到 1 這些位置值。 如果是這樣，您只可以提供`null`做為第四個引數`CreateLinearGradient`。

雖然這個漸層的 300 像素的正方形矩形的兩個邊角之間定義，但不限於填滿該矩形。 **角-漸層**頁面包含一些額外的程式碼，以回應點選或按一下網頁上的滑鼠。 `drawBackground`欄位就會切換為之間`true`和`false`與每次點選。 如果值為`true`，則`PaintSurface`處理常式會使用相同`SKPaint`物件以填滿整個畫布，然後繪製以黑色矩形表示較小的矩形： 

```csharp
public class CornerToCornerGradientPage : ContentPage
{
    bool drawBackground;

    public CornerToCornerGradientPage ()
    {
        ···
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            drawBackground ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPaint paint = new SKPaint())
        {
            ···
            if (drawBackground)
            {
                // Draw the gradient on the whole canvas
                canvas.DrawRect(info.Rect, paint);

                // Outline the smaller rectangle
                paint.Shader = null;
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                canvas.DrawRect(rect, paint);
            }
        }
    }
}
```

以下是您會看到點選螢幕之後：

[![角右上角的漸層停駐全文](linear-gradient-images/CornerToCornerGradientFull.png "角右上角的漸層停駐全文")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

請注意，漸層會自行重複超過定義漸層線的點相同的模式中。 因為，就會發生此重複的最後一個引數`CreateLinearGradient`是`SKShaderTileMode.Repeat`。 （您很快其他選項。）

也請注意您用來指定漸層線的點不是唯一的。 與 axis 成漸層線的線條會有相同的色彩，因此會有無限個漸層的線條，您可以指定相同的效果。 例如，當水平漸層填滿矩形，您可以指定左上角和右上角的左下角和右下角或甚至使用和並行的這幾行的任何兩個點。

## <a name="interactively-experiment"></a>以互動方式進行實驗

您可以以互動方式試驗使用線形漸層**互動式線性漸層停駐**頁面。 此頁面會使用`InteractivePage`一文中導入的類別[**繪製弧形的三種方式**](../../curves/arcs.md)。`InteractivePage`控制代碼[ `TouchEffect` ](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)維護的集合的事件`TouchPoint`您可以使用手指或滑鼠移動的物件。

XAML 檔案會附加`TouchEffect`到上層`SKCanvasView`還包含`Picker`，可讓您選取其中一個的三個成員[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)列舉型別：

```xaml
<local:InteractivePage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       xmlns:local="clr-namespace:SkiaSharpFormsDemos"
                       xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
                       xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
                       xmlns:tt="clr-namespace:TouchTracking"
                       x:Class="SkiaSharpFormsDemos.Effects.InteractiveLinearGradientPage"
                       Title="Interactive Linear Gradient">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid BackgroundColor="White"
              Grid.Row="0">
            <skiaforms:SKCanvasView x:Name="canvasView"
                                    PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Picker x:Name="tileModePicker" 
                Grid.Row="1"
                Title="Shader Tile Mode" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKShaderTileMode}">
                    <x:Static Member="skia:SKShaderTileMode.Clamp" />
                    <x:Static Member="skia:SKShaderTileMode.Repeat" />
                    <x:Static Member="skia:SKShaderTileMode.Mirror" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</local:InteractivePage>
```

在程式碼後置檔案中的建構函式會建立兩個`TouchPoint`起點和終點的線性漸層的物件。 `PaintSurface`處理常式定義 （用於漸層從紅色到綠色和藍色） 的三種色彩的陣列，並取得目前`SKShaderTileMode`從`Picker`:

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    public InteractiveLinearGradientPage ()
    {
        InitializeComponent ();

        touchPoints = new TouchPoint[2];

        for (int i = 0; i < 2; i++)
        { 
            touchPoints[i] = new TouchPoint
            {
                Center = new SKPoint(100 + i * 200, 100 + i * 200)
            };
        }

        InitializeComponent();
        baseCanvasView = canvasView;
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKColor[] colors = { SKColors.Red, SKColors.Green, SKColors.Blue };
        SKShaderTileMode tileMode =
            (SKShaderTileMode)(tileModePicker.SelectedIndex == -1 ?
                                        0 : tileModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(touchPoints[0].Center,
                                                         touchPoints[1].Center,
                                                         colors,
                                                         null,
                                                         tileMode);
            canvas.DrawRect(info.Rect, paint);
        }
        ···
    }
}
```

`PaintSurface`處理常式會建立`SKShader`物件，該資訊，並使用它來色彩整個畫布。 陣列`float`值設為`null`。 否則為要同樣空間三種色彩，您應該設定該參數為 0，0.5 和 1 的值陣列。

大量`PaintSurface`處理常式用來顯示數個物件： 觸控點，圓形外框、 漸層停駐列，以及垂直觸控點的漸層線條的線條：

```csharp
public partial class InteractiveLinearGradientPage : InteractivePage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Display the touch points here rather than by TouchPoint
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;

            foreach (TouchPoint touchPoint in touchPoints)
            {
                canvas.DrawCircle(touchPoint.Center, touchPoint.Radius, paint);
            }

            // Draw gradient line connecting touchpoints
            canvas.DrawLine(touchPoints[0].Center, touchPoints[1].Center, paint);

            // Draw lines perpendicular to the gradient line
            SKPoint vector = touchPoints[1].Center - touchPoints[0].Center;
            float length = (float)Math.Sqrt(Math.Pow(vector.X, 2) +
                                            Math.Pow(vector.Y, 2));
            vector.X /= length;
            vector.Y /= length;
            SKPoint rotate90 = new SKPoint(-vector.Y, vector.X);
            rotate90.X *= 200;
            rotate90.Y *= 200;

            canvas.DrawLine(touchPoints[0].Center, 
                            touchPoints[0].Center + rotate90, 
                            paint);

            canvas.DrawLine(touchPoints[0].Center,
                            touchPoints[0].Center - rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center + rotate90,
                            paint);

            canvas.DrawLine(touchPoints[1].Center,
                            touchPoints[1].Center - rotate90,
                            paint);
        }
    }
}
```

漸層線連接兩個接觸點很容易繪製，但垂直線需要一些更多的工作。 漸層線會轉換為向量，正規化為長度為一個單位，並再旋轉 90 度。 該向量則是提供長度為 200 像素。 它用來繪製為垂直漸層線的觸控點從擴充的四行程式碼。

垂直線符合開始和結束點。 除了這幾行的情況取決於設定`SKShaderTileMode`列舉型別：

[![互動式的線性漸層](linear-gradient-images/InteractiveLinearGradient.png "互動式的線性漸層")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

三個螢幕擷取畫面顯示三個不同值的結果[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)。 IOS 螢幕擷取畫面顯示`SKShaderTileMode.Clamp`，這只會延長上之漸層框線的色彩。 `SKShaderTileMode.Repeat` Android 的螢幕擷取畫面中的選項會顯示如何重複漸層停駐的模式。 `SKShaderTileMode.Mirror` UWP 螢幕擷取畫面中的選項也會重複模式，但模式相反，每次導致沒有色彩的不連續。

## <a name="gradients-on-gradients"></a>在漸層的漸層

`SKShader`類別會定義任何公用屬性或方法，除了`Dispose`。 `SKShader`物件建立它的靜態方法，因此是固定不變。 即使您使用相同的漸層的兩個不同的物件時，很可能您會想要稍有差異之漸層。 若要這麼做，您將需要建立新`SKShader`物件。

**漸層停駐文字**頁面會顯示文字和兩者著色使用類似的漸層背景：

[![漸層停駐文字](linear-gradient-images/GradientText.png "漸層停駐文字")](linear-gradient-images/GradientText-Large.png#lightbox)

漸層的唯一差異是開始和結束點。 用來顯示文字的漸層為基礎的文字的週框矩形邊角的兩個點。 兩個點的背景，依據整個畫布。 程式碼如下：

```csharp
public class GradientTextPage : ContentPage
{
    const string TEXT = "GRADIENT";

    public GradientTextPage ()
    {
        Title = "Gradient Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Create gradient for background
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                new SKPoint(info.Width, info.Height),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw background
            canvas.DrawRect(info.Rect, paint);

            // Set TextSize to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Shift textBounds by that amount
            textBounds.Offset(xText, yText);

            // Create gradient for text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(textBounds.Left, textBounds.Top),
                                new SKPoint(textBounds.Right, textBounds.Bottom),
                                new SKColor[] { new SKColor(0x40, 0x40, 0x40),
                                                new SKColor(0xC0, 0xC0, 0xC0) },
                                null,
                                SKShaderTileMode.Clamp);

            // Draw text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

`Shader`屬性`SKPaint`物件第一次設定為顯示到覆蓋背景的漸層。 漸層停駐點會設定為畫布的左上角和右下角邊角。

程式碼集`TextSize`屬性`SKPaint`物件，讓文字會顯示在畫布的寬度的 90%。 文字範圍用來計算`xText`並`yText`值来傳遞至`DrawText`方法將文字置中。

第二個漸層的點但`CreateLinearGradient`呼叫必須參考的文字相對於在畫布的左上角和右下角顯示。 這透過移位`textBounds`由相同矩形`xText`和`yText`值：

```csharp
textBounds.Offset(xText, yText);
```

現在，矩形的左上角和右下角邊角可用來設定開始和結束點的漸層。

## <a name="animating-a-gradient"></a>以動畫顯示漸層

有數種方式來建立漸層的動畫。 其中一個方法是以動畫顯示開始和結束點。 **漸層停駐的動畫**頁面移動兩個點置中對齊是圓形中畫布上。 此圓形的半徑一半的寬度或高度的畫布，取其較小。 開始和結束點位於相反彼此此圓形，而漸層會從白色與黑色`Mirror`並排顯示模式：

[![漸層停駐的動畫](linear-gradient-images/GradientAnimation.png "漸層停駐的動畫")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

建構函式建立`SKCanvasView`。 `OnAppearing`和`OnDisappearing`方法會處理動畫邏輯：

```csharp
public class GradientAnimationPage : ContentPage
{
    SKCanvasView canvasView;
    bool isAnimating;
    double angle;
    Stopwatch stopwatch = new Stopwatch();

    public GradientAnimationPage()
    {
        Title = "Gradient Animation";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 3000;
        angle = 2 * Math.PI * (stopwatch.ElapsedMilliseconds % duration) / duration;
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

`OnTimerTick`方法會計算`angle`以動畫顯示從 0 到 2 π 每隔 3 秒的值。 

以下是一個來計算兩個漸層停駐點的方式。 `SKPoint`值並命名為`vector`計算圓形的半徑點延伸從畫布的正中央。 這個向量的方向根據角度的正弦和餘弦值。 然後會計算兩個相反的漸層停駐點：一個點的計算方式是減去的向量從中心點，並加入到中心點的向量來計算其他點：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKPoint center = new SKPoint(info.Rect.MidX, info.Rect.MidY);
            int radius = Math.Min(info.Width, info.Height) / 2;
            SKPoint vector = new SKPoint((float)(radius * Math.Cos(angle)),
                                         (float)(radius * Math.Sin(angle)));

            paint.Shader = SKShader.CreateLinearGradient(
                                center - vector,
                                center + vector,
                                new SKColor[] { SKColors.White, SKColors.Black },
                                null,
                                SKShaderTileMode.Mirror);

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

以稍有不同的方法需要較少的程式碼。 這個方法會利用[ `SKShader.CreateLinearGradient` ](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))矩陣轉換，最後一個引數的方法多載。 這種方法是中的版本[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例：

```csharp
public class GradientAnimationPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, 0),
                                info.Width < info.Height ? new SKPoint(info.Width, 0) : 
                                                           new SKPoint(0, info.Height),
                                new SKColor[] { SKColors.White, SKColors.Black },
                                new float[] { 0, 1 },
                                SKShaderTileMode.Mirror,
                                SKMatrix.MakeRotation((float)angle, info.Rect.MidX, info.Rect.MidY));

            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

如果畫布的寬度小於高度，則兩個漸層停駐點設定為 （0，0） 和 (`info.Width`，0)。 傳遞做為最後一個引數的旋轉轉換`CreateLinearGradient`有效地旋轉中心周圍螢幕的這兩個點。

請注意，如果角度為 0，沒有任何循環，而且兩個的漸層停駐點是畫布的左上角和右上角。 這些點不是相同的計算方式為先前所示的漸層停駐點`CreateLinearGradient`呼叫。 但這些點_平行_以水平漸層線的 bisects 畫布的中心與它們產生相同的漸層。

**Rainbow 漸層**

**彩虹漸層停駐**頁面將 rainbow 繪製從畫布的左上角至右下角。 但不像真正的彩虹的這個 rainbow 漸層。 直線，而不是曲線，但它根據取決於一一巡視色調值從 0 到 360 的八個 HSL （色相-彩度-亮度） 色彩：

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

程式碼是一部分`PaintSurface`如下所示的處理常式。 處理常式一開始會建立可定義六個邊的多邊形從畫布的左上角延伸至右下角的路徑：

```csharp
public class RainbowGradientPage : ContentPage
{
    public RainbowGradientPage ()
    {
        Title = "Rainbow Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            float rainbowWidth = Math.Min(info.Width, info.Height) / 2f;

            // Create path from upper-left to lower-right corner
            path.MoveTo(0, 0);
            path.LineTo(rainbowWidth / 2, 0);
            path.LineTo(info.Width, info.Height - rainbowWidth / 2);
            path.LineTo(info.Width, info.Height);
            path.LineTo(info.Width - rainbowWidth / 2, info.Height);
            path.LineTo(0, rainbowWidth / 2);
            path.Close();

            using (SKPaint paint = new SKPaint())
            {
                SKColor[] colors = new SKColor[8];

                for (int i = 0; i < colors.Length; i++)
                {
                    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
                }

                paint.Shader = SKShader.CreateLinearGradient(
                                    new SKPoint(0, rainbowWidth / 2), 
                                    new SKPoint(rainbowWidth / 2, 0),
                                    colors,
                                    null,
                                    SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

中的兩個漸層停駐點`CreateLinearGradient`方法會根據兩個定義這個路徑的點：這兩個點都接近左上角。 第一個是在畫布的邊緣和第二個是在畫布左邊緣。 結果如下：

[![Rainbow 漸層故障](linear-gradient-images/RainbowGradientFaulty.png "故障的 Rainbow 漸層")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

這是一個有趣的影像，但不是很意圖。 問題是在建立線形漸層時，常數色彩的線條垂直漸層線。 漸層線為基礎的點圖碰觸的上方和左邊的側邊，而那一行通常不是垂直延伸至右下角圖的邊緣。 這種方法可行，只有當畫布已在方形。

若要建立適當的 rainbow 漸層，必須是彩虹的邊緣的垂直漸層線。 這是更複雜的計算。 向量必須定義平行圖的長邊。 讓您可於該側的垂直向量會是旋轉 90 度。 然後要乘以的圖的寬度加長`rainbowWidth`。 兩個漸層停駐點是根據上圖中，旁邊的點以及計算該點再加上向量。 以下是會出現在程式碼**彩虹漸層停駐**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例：

```csharp
public class RainbowGradientPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        using (SKPath path = new SKPath())
        {
            ···
            using (SKPaint paint = new SKPaint())
            {
                ···
                // Vector on lower-left edge, from top to bottom 
                SKPoint edgeVector = new SKPoint(info.Width - rainbowWidth / 2, info.Height) - 
                                     new SKPoint(0, rainbowWidth / 2);

                // Rotate 90 degrees counter-clockwise:
                SKPoint gradientVector = new SKPoint(edgeVector.Y, -edgeVector.X);

                // Normalize
                float length = (float)Math.Sqrt(Math.Pow(gradientVector.X, 2) +
                                                Math.Pow(gradientVector.Y, 2));
                gradientVector.X /= length;
                gradientVector.Y /= length;

                // Make it the width of the rainbow
                gradientVector.X *= rainbowWidth;
                gradientVector.Y *= rainbowWidth;

                // Calculate the two points
                SKPoint point1 = new SKPoint(0, rainbowWidth / 2);
                SKPoint point2 = point1 + gradientVector;

                paint.Shader = SKShader.CreateLinearGradient(point1,
                                                             point2,
                                                             colors,
                                                             null,
                                                             SKShaderTileMode.Repeat);

                canvas.DrawPath(path, paint);
            }
        }
    }
}
```

現在的 rainbow 色彩符合圖：

[![Rainbow 漸層](linear-gradient-images/RainbowGradient.png "彩虹漸層")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**無限大色彩**

Rainbow 漸層也會在**無限大色彩**頁面。 此頁面會繪製使用本文所述的路徑物件無限大登[**三種類型的貝茲曲線**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs)。 然後使用持續掃掠整個映像的動畫的 rainbow 漸層著色映像。

建構函式建立`SKPath`描述無限大符號的物件。 建立路徑之後，建構函式也可以取得路徑的矩形界限。 接著再計算一個值，稱為`gradientCycleLength`。 如果漸層為基礎的左上角和右下角邊角`pathBounds`矩形中，這`gradientCycleLength`值是漸層停駐模式的總水平寬度：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    SKCanvasView canvasView;

    // Path information 
    SKPath infinityPath;
    SKRect pathBounds;
    float gradientCycleLength;

    // Gradient information
    SKColor[] colors = new SKColor[8];
    ···

    public InfinityColorsPage ()
    {
        Title = "Infinity Colors";

        // Create path for infinity sign
        infinityPath = new SKPath();
        infinityPath.MoveTo(0, 0);                                  // Center
        infinityPath.CubicTo(  50,  -50,   95, -100,  150, -100);   // To top of right loop
        infinityPath.CubicTo( 205, -100,  250,  -55,  250,    0);   // To far right of right loop
        infinityPath.CubicTo( 250,   55,  205,  100,  150,  100);   // To bottom of right loop
        infinityPath.CubicTo(  95,  100,   50,   50,    0,    0);   // Back to center  
        infinityPath.CubicTo( -50,  -50,  -95, -100, -150, -100);   // To top of left loop
        infinityPath.CubicTo(-205, -100, -250,  -55, -250,    0);   // To far left of left loop
        infinityPath.CubicTo(-250,   55, -205,  100, -150,  100);   // To bottom of left loop
        infinityPath.CubicTo( -95,  100, - 50,   50,    0,    0);   // Back to center
        infinityPath.Close();

        // Calculate path information 
        pathBounds = infinityPath.Bounds;
        gradientCycleLength = pathBounds.Width +
            pathBounds.Height * pathBounds.Height / pathBounds.Width;

        // Create SKColor array for gradient
        for (int i = 0; i < colors.Length; i++)
        {
            colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
        }

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

建構函式也會建立`colors`彩虹，陣列和`SKCanvasView`物件。

覆寫`OnAppearing`和`OnDisappearing`方法動畫會執行額外負荷。 `OnTimerTick`方法以動畫展示`offset`欄位從 0 到`gradientCycleLength`每隔兩秒鐘：

```csharp
public class InfinityColorsPage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    float offset;
    Stopwatch stopwatch = new Stopwatch();
    ···

    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        const int duration = 2;     // seconds
        double progress = stopwatch.Elapsed.TotalSeconds % duration / duration;
        offset = (float)(gradientCycleLength * progress);
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

最後，`PaintSurface`處理常式可以呈現的無限值正負號。 因為路徑包含負數和正數圍繞的中心點的座標 （0，0），`Translate`畫布上的轉換用來移位至中心。 平移轉換後面`Scale`適用於縮放的比例，同時仍然合乎 95%的寬度和高度的畫布，讓無限大登一樣大的轉換。 

請注意，`STROKE_WIDTH`常數會新增至路徑的週框矩形的高度與寬度。 將一行則為這個寬度，圖案的路徑，讓呈現的無限值大小的大小會增加四個邊的一半，寬度：

```csharp
public class InfinityColorsPage : ContentPage
{
    const int STROKE_WIDTH = 50;
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set transforms to shift path to center and scale to canvas size
        canvas.Translate(info.Width / 2, info.Height / 2);
        canvas.Scale(0.95f * 
            Math.Min(info.Width / (pathBounds.Width + STROKE_WIDTH),
                     info.Height / (pathBounds.Height + STROKE_WIDTH)));

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = STROKE_WIDTH;
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(pathBounds.Left, pathBounds.Top),
                                new SKPoint(pathBounds.Right, pathBounds.Bottom),
                                colors,
                                null,
                                SKShaderTileMode.Repeat,
                                SKMatrix.MakeTranslation(offset, 0));

            canvas.DrawPath(infinityPath, paint);
        }
    }
}
```

看看前兩個引數形式傳遞的點`SKShader.CreateLinearGradient`。 這些點為基礎的週框矩形的原始路徑。 第一個點 (&ndash;250， &ndash;100) 的第二個是 （250，100）。 內部 SkiaSharp，這些點受制於目前的畫布轉換使它們顯示的無限大號正確對齊。

而最後一個引數不`CreateLinearGradient`，您會看到從左上無限大符號的低權限延伸的 rainbow 漸層。 （事實上，漸層從延伸左上角到指定的週框右下角。 轉譯的無限大登大於一半的周框`STROKE_WIDTH`所有側邊的值。 因為漸層是紅色開頭和結尾，且漸層會透過`SKShaderTileMode.Repeat`，差異並不明顯。)

最後一個引數與`CreateLinearGradient`，漸層停駐模式持續掃掠整個映像：

[![無限大色彩](linear-gradient-images/InfinityColors.png "無限大色彩")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>透明及漸層

用於漸層的色彩可以納入透明度。 而不是從一種色彩之間淡入淡漸層、 漸層可以從淡出色彩為透明。 

您可以使用這項技術的一些有趣效果。 其中一個典型的範例顯示具有其反映的圖形化的物件：

[![反映漸層](linear-gradient-images/ReflectionGradient.png "反映漸層")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

使用 50%透明頂端到底部完全透明的漸層著色顛倒的文字。 以下層級是透明度的 alpha 值 0x80 和 0 相關聯。

`PaintSurface`中的處理常式**反映漸層停駐**頁面縮放的 90%的畫布的寬度的文字大小。 接著再計算`xText`和`yText`往水平置中對齊文字的值，但基準，對應至垂直置中頁面的位置：

```csharp
public class ReflectionGradientPage : ContentPage
{
    const string TEXT = "Reflection";

    public ReflectionGradientPage ()
    {
        Title = "Reflection Gradient";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            // Set text color to blue
            paint.Color = SKColors.Blue;

            // Set text size to fill 90% of width
            paint.TextSize = 100;
            float width = paint.MeasureText(TEXT);
            float scale = 0.9f * info.Width / width;
            paint.TextSize *= scale;

            // Get text bounds
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Calculate offsets to position text above center
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2;

            // Draw unreflected text
            canvas.DrawText(TEXT, xText, yText, paint);

            // Shift textBounds to match displayed text
            textBounds.Offset(xText, yText);

            // Use those offsets to create a gradient for the reflected text
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(0, textBounds.Top),
                                new SKPoint(0, textBounds.Bottom),
                                new SKColor[] { paint.Color.WithAlpha(0),
                                                paint.Color.WithAlpha(0x80) },
                                null,
                                SKShaderTileMode.Clamp);

            // Scale the canvas to flip upside-down around the vertical center
            canvas.Scale(1, -1, 0, yText);

            // Draw reflected text
            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

那些`xText`及`yText`的值為相同的值，用來顯示中的反映的文字`DrawText`底部的呼叫`PaintSurface`處理常式。 之前該程式碼，不過，您會看到呼叫`Scale`方法的`SKCanvas`。 這`Scale`方法會水平調整 （這不執行任何動作） 1 但垂直的&ndash;1，有效地翻轉的所有項目為上下顛倒。 旋轉的中心點設定為點 (0， `yText`)，其中`yText`是垂直中心的畫布，原先做為計算`info.Height`除以 2。

請記住，Skia 會使用之漸層色彩的畫布轉換之前的圖形物件。 繪製 unreflected 的文字之後，`textBounds`矩形都會轉移，因此它會對應至顯示的文字：

```csharp
textBounds.Offset(xText, yText);
```

`CreateLinearGradient`呼叫會定義從該矩形的頂端到底部的漸層。 漸層是從完全透明的藍色 (`paint.Color.WithAlpha(0)`) 為 50%透明的藍色 (`paint.Color.WithAlpha(0x80)`)。 50%透明藍色開始基準，因此會變成透明頂端的文字，畫布轉換就會翻轉為上下顛倒的文字。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
