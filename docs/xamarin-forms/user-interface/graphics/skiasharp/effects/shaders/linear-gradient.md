---
標題：「SkiaSharp 線性漸層」描述：「探索如何以兩種色彩的漸進混合所組成的漸層來繪製線條或填滿區域。」
skiasharp 的 assetid： 20A2A8C4-FEB7-478D-BF57-C92E26117B6A author： davidbritch ms-chap： dabritch ms. date： 08/23/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="the-skiasharp-linear-gradient"></a>SkiaSharp 線性漸層

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[`SKPaint`](xref:SkiaSharp.SKPaint)類別 [`Color`](xref:SkiaSharp.SKPaint.Color) 會定義用來以純色繪製線條或填滿區域的屬性。 或者 _，您也可以使用漸_層色彩來筆劃線條或填滿區域，這是色彩的漸進混合：

![線性漸層範例](linear-gradient-images/LinearGradientSample.png "線性漸層範例")

最基本的漸層類型是_線性_漸層。 色彩的 blend 會在一條線（稱為漸層_線_）的某個點發生。 垂直于漸層線條的線條具有相同的色彩。 您可以使用這兩個靜態方法的其中一個來建立線性漸層 [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient*) 。 這兩個多載之間的差異在於其中一個會包含矩陣轉換，另一個則不會。 

這些方法 [`SKShader`](xref:SkiaSharp.SKShader) 會傳回您設定為之屬性的型別物件 [`Shader`](xref:SkiaSharp.SKPaint.Shader) `SKPaint` 。 如果 `Shader` 屬性為非 null，則會覆寫 `Color` 屬性。 任何已繪製的線條，或任何使用這個物件填滿的區域， `SKPaint` 都是根據漸層，而不是純色。

> [!NOTE]
> `Shader`當您 `SKPaint` 在呼叫中包含物件時，會忽略屬性 `DrawBitmap` 。 您可以使用的 `Color` 屬性 `SKPaint` 來設定顯示點陣圖的透明度層級（如[顯示 SkiaSharp 點陣圖](../../bitmaps/displaying.md#displaying-in-pixel-dimensions)一文所述），但您無法使用 `Shader` 屬性來顯示具有漸層透明度的點陣圖。 其他技術可用於顯示具有漸層透明度的點陣圖：這些會在[SkiaSharp 圓形](circular-gradients.md#radial-gradients-for-masking)漸層和[SkiaSharp 複合和 blend 模式](../blend-modes/porter-duff.md#gradient-transparency-and-transitions)的文章中加以說明。

## <a name="corner-to-corner-gradients"></a>邊角漸層

線性漸層通常會從矩形的一個角落延伸至另一個。 如果起始點是矩形的左上角，則漸層可以擴充：

- 垂直到左下角
- 水準到右上角
- 對角線向右下角

對角線線性漸層會在[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的**SkiaSharp 著色器和其他效果**區段的第一頁中示範。 **角落**的漸層頁面會 `SKCanvasView` 在其程式中建立。 `PaintSurface`處理常式會 `SKPaint` 在語句中建立物件 `using` ，然後定義以畫布為中心的300圖元方形矩形：

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

的 `Shader` 屬性 `SKPaint` 會指派 `SKShader` 靜態方法的傳回值 `SKShader.CreateLinearGradient` 。 五個引數如下所示：

- 漸層的起點，設定在此矩形的左上角
- 漸層的結束點，在這裡設定為矩形的右下角
- 組成漸層的兩個或多個色彩的陣列
- 值的陣列 `float` ，表示漸層中色彩的相對位置。
- 列舉的成員，表示漸層的 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 行為超出漸層線的結尾

建立漸層物件之後，方法會 `DrawRect` 使用包含著色器的物件繪製300圖元的方形矩形 `SKPaint` 。 在這裡，它是在 iOS、Android 和通用 Windows 平臺（UWP）上執行：

[![邊角漸層](linear-gradient-images/CornerToCornerGradient.png "邊角漸層")](linear-gradient-images/CornerToCornerGradient-Large.png#lightbox)

漸層線條是由指定為前兩個引數的兩個點所定義。 請注意，這些點是相對於_畫布_，而_不_是與漸層一起顯示的繪圖物件。 沿著漸層線條，色彩會逐漸從右下方的左上到藍色從紅色轉換。 任何垂直漸層線條的線條都有常數色彩。

`float`指定為第四個引數的值陣列與色彩的陣列具有一對一的對應關係。 這些值表示沿著漸層線條上發生這些色彩的相對位置。 在這裡，0表示 `Red` 出現在漸層行首，而1表示 `Blue` 發生在行尾。 數位必須是遞增的，且應介於0到1的範圍內。 如果它們不在該範圍內，則會調整為在該範圍內。

陣列中的兩個值可以設定為0和1以外的值。 試試看：

```csharp
new float[] { 0.25f, 0.75f }
```

現在漸層的整個第一季是純紅色，而最後一季是純藍色。 紅色和藍色的混合會限制為漸層的中半部。

一般來說，您會想要將這些位置值的空間平均從0到1。 如果是這種情況，您可以直接 `null` 將當做的第四個引數提供給 `CreateLinearGradient` 。

雖然此漸層是在300圖元方形矩形的兩個角落之間定義的，但並不限於填滿該矩形。 **邊**角漸層頁面包含一些額外的程式碼，可回應頁面上的點擊或滑鼠點擊。 `drawBackground` `true` 每次使用時，會在和之間切換此欄位 `false` 。 如果值為 `true` ，則 `PaintSurface` 處理常式會使用相同的 `SKPaint` 物件來填滿整個畫布，然後繪製黑色矩形來表示較小的矩形： 

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

以下是您在點擊畫面後會看到的內容：

[![邊角漸層已滿](linear-gradient-images/CornerToCornerGradientFull.png "邊角漸層已滿")](linear-gradient-images/CornerToCornerGradientFull-Large.png#lightbox)

請注意，漸層會在定義漸層線條的點以外的相同模式中重複。 發生這個重複的原因是，的最後一個引數 `CreateLinearGradient` 是 `SKShaderTileMode.Repeat` 。 （您很快就會看到其他選項）。

另請注意，您用來指定漸層線條的點不是唯一的。 垂直于漸層線條的線條具有相同的色彩，因此，您可以為相同效果指定無限多的漸層。 例如，當填滿具有水準漸層的矩形時，您可以指定左上角和右上角，或是左下方和右下角，或任何兩個甚至是與這些線條平行的點。

## <a name="interactively-experiment"></a>以互動方式實驗

您可以使用**互動式線性**漸層頁面，以互動方式試驗線性漸層。 此頁面會使用 `InteractivePage` 文章中引進的類別，[**以三種方式繪製弧線**](../../curves/arcs.md)。 `InteractivePage` [`TouchEffect`](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md) 會處理事件，以維護 `TouchPoint` 可使用手指或滑鼠移動的物件集合。

XAML 檔案會將附加 `TouchEffect` 至的父系 `SKCanvasView` ，而且也包含，可 `Picker` 讓您選取列舉中三個成員的其中一個 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ：

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

程式碼後置檔案中的函式會 `TouchPoint` 針對線性漸層的起點和終點建立兩個物件。 `PaintSurface`處理常式會定義三種色彩的陣列（針對從紅色到綠色到藍色的漸層），並從取得目前的 `SKShaderTileMode` `Picker` ：

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

`PaintSurface`處理常式 `SKShader` 會從所有該資訊建立物件，並使用它來為整個畫布上色。 值的陣列 `float` 設定為 `null` 。 否則，若要將該參數設定為值為0、0.5 和1的陣列，

大量 `PaintSurface` 處理常式專門用來顯示數個物件：觸控點為外框、漸層線條，以及與觸控點上的漸層垂直線垂直的線條：

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

連接兩個接觸點的漸層線條很容易繪製，但是垂直線需要一些工作。 漸層線條會轉換成向量，並正規化為長度為一個單位，然後旋轉90度。 接著，該向量會得到200圖元的長度。 它是用來繪製四條線，從觸控點擴充以垂直到漸層線條。

垂直線會與漸層的開始和結束點一致。 這幾行以外的作業會視列舉的設定而定 `SKShaderTileMode` ：

[![互動式線性漸層](linear-gradient-images/InteractiveLinearGradient.png "互動式線性漸層")](linear-gradient-images/InteractiveLinearGradient-Large.png#lightbox)

這三個螢幕擷取畫面顯示三個不同值的結果 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) 。 IOS 螢幕擷取畫面顯示 `SKShaderTileMode.Clamp` ，它只會擴充漸層框線的色彩。 `SKShaderTileMode.Repeat`Android 螢幕擷取畫面中的選項會顯示如何重複漸層模式。 `SKShaderTileMode.Mirror`UWP 螢幕擷取畫面中的選項也會重複模式，但是每次都會反轉模式，因此不會不連續色彩。

## <a name="gradients-on-gradients"></a>梯度的漸層

`SKShader`類別不會定義任何公用屬性或方法（除外） `Dispose` 。 `SKShader`因此，其靜態方法所建立的物件是不可變的。 即使針對兩個不同的物件使用相同的漸層，您可能會想要稍微改變漸層。 若要這麼做，您必須建立新的 `SKShader` 物件。

[漸層**文字**] 頁面會顯示以類似漸層著色的文字和背景：

[![漸層文字](linear-gradient-images/GradientText.png "漸層文字")](linear-gradient-images/GradientText-Large.png#lightbox)

漸層的唯一差異在於起點和終點。 用於顯示文字的漸層是以文字周框角落的兩個點為基礎。 就背景而言，這兩個點是以整個畫布為基礎。 此程式碼如下：

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

`Shader`物件的屬性 `SKPaint` 會先設定成顯示漸層來涵蓋背景。 漸層點會設定為畫布的左上方和右下角。

程式碼會設定 `TextSize` 物件的屬性， `SKPaint` 使文字顯示在畫布寬度的90%。 文字界限用來計算要 `xText` `yText` 傳遞至方法以將文字置中的值 `DrawText` 。

不過，第二個呼叫的漸 `CreateLinearGradient` 層點必須參考顯示時相對於畫布的文字左上角和右下角。 這是藉由 `textBounds` 將矩形轉換成相同的 `xText` 和 `yText` 值來完成：

```csharp
textBounds.Offset(xText, yText);
```

現在可以使用矩形的左上角和右下角來設定漸層的起點和終點。

## <a name="animating-a-gradient"></a>建立漸層動畫

有數種方式可建立漸層的動畫。 其中一種方法是以動畫顯示起點和終點。 [漸層**動畫**] 頁面會在以畫布為中心的圓形上移動兩個點。 此圓形的半徑為畫布寬度或高度的一半，以較小者為准。 起點和終點在此圓形上彼此相反，而漸層則以磚模式從白色到黑色 `Mirror` ：

[![漸層動畫](linear-gradient-images/GradientAnimation.png "漸層動畫")](linear-gradient-images/GradientAnimation-Large.png#lightbox)

此構造函式會建立 `SKCanvasView` 。 `OnAppearing`和 `OnDisappearing` 方法會處理動畫邏輯：

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

`OnTimerTick`方法 `angle` 會計算每3秒從0到2π的動畫值。 

以下是計算兩個漸層點的一種方式。 `SKPoint`系統會計算名為的值， `vector` 以便從畫布中央延伸到圓形半徑上的點。 這個向量的方向是以角度的正弦和余弦值為基礎。 接著會計算兩個相反的漸層點：一個點的計算方式是從中心點減去該向量，而其他點則是藉由將向量新增至中心點來計算：

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

有些不同的方法需要較少的程式碼。 這個方法會使用多載 [`SKShader.CreateLinearGradient`](xref:SkiaSharp.SKShader.CreateLinearGradient(SkiaSharp.SKPoint,SkiaSharp.SKPoint,SkiaSharp.SKColor[],System.Single[],SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 方法搭配矩陣轉換做為最後一個引數。 此方法是[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的版本：

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

如果畫布的寬度小於高度，則兩個漸層點會設定為（0，0）和（ `info.Width` ，0）。 當做最後一個引數傳遞的旋轉轉換，可 `CreateLinearGradient` 有效地將這兩個點旋轉到螢幕中央。

請注意，如果角度為0，則不會旋轉，而且兩個漸層點是畫布的左上角和右上角。 這些點不是以先前呼叫中所示計算的相同漸層點 `CreateLinearGradient` 。 但這些點會_平行_處理 bisects 畫布中心的水準漸層線條，而且會產生相同的漸層。

**彩虹漸層**

**彩虹**漸層頁面會將畫布左上角的彩虹繪製至右下角。 但這個彩虹漸層不像是一個真實的彩虹。 它是直接而不是彎曲的，但它是以8個 HSL （色調-飽和度-亮度）色彩為基礎，而這是透過從0到360的色調值來決定的：

```csharp
SKColor[] colors = new SKColor[8];

for (int i = 0; i < colors.Length; i++)
{
    colors[i] = SKColor.FromHsl(i * 360f / (colors.Length - 1), 100, 50);
}
```

該程式碼是如下所 `PaintSurface` 示之處理常式的一部分。 處理常式一開始會先建立一個路徑，以定義從畫布左上角延伸至右下角的六側多邊形：

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

方法中的兩個漸層點 `CreateLinearGradient` 是以定義此路徑的兩個點為基礎：兩個點都靠近左上角。 第一個是位於畫布的上邊緣，而第二個位于畫布的左邊緣。 結果如下︰

[![彩虹梯度錯誤](linear-gradient-images/RainbowGradientFaulty.png "彩虹梯度錯誤")](linear-gradient-images/RainbowGradientFaulty-Large.png#lightbox)

這是一個有趣的映射，但它並不是真正的目的。 問題在於，建立線性漸層時，常數色彩的線條會垂直到漸層線條。 漸層線條是以圖表觸及頂端和左邊的點為基礎，而該線條通常不會垂直于延伸至右下角的圖形邊緣。 只有當畫布為正方形時，此方法才會生效。

若要建立適當的彩虹漸層，漸層線條必須垂直于彩虹的邊緣。 這是比較牽涉的計算。 向量必須定義為與圖表的長邊平行。 向量會旋轉90度，使其與該邊垂直。 然後藉由乘以來將它加長，以作為圖表的寬度 `rainbowWidth` 。 這兩個漸層點是根據圖表側邊的點來計算，而該點加上向量。 以下是[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的**彩虹**漸層頁面中顯示的程式碼：

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

現在，彩虹色彩會與圖對齊：

[![彩虹漸層](linear-gradient-images/RainbowGradient.png "彩虹漸層")](linear-gradient-images/RainbowGradient-Large.png#lightbox)

**無限大色彩**

[**無限大色彩**] 頁面也會使用彩虹漸層。 此頁面會使用一文中所述的路徑物件來繪製無限大符號，這是[**第三種類型的貝茲曲線**](../../curves/beziers.md#bezier-curve-approximation-to-circular-arcs)。 然後，影像會以動畫的彩虹漸層著色，持續在影像之間進行掃描。

此函式會建立 `SKPath` 描述無限大符號的物件。 建立路徑之後，此構造函式也可以取得路徑的矩形界限。 然後，它會計算名為的值 `gradientCycleLength` 。 如果漸層是以矩形的左上方和右下角為基礎 `pathBounds` ，則此 `gradientCycleLength` 值為漸層模式的總水準寬度：

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

此函式也會建立 `colors` 彩虹和物件的陣列 `SKCanvasView` 。

和方法的覆寫會對 `OnAppearing` `OnDisappearing` 動畫執行額外負荷。 方法會將 `OnTimerTick` `offset` 欄位從0動畫繪製到 `gradientCycleLength` 每兩秒：

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

最後， `PaintSurface` 處理常式會呈現無限大符號。 因為路徑包含圍繞著中心點（0，0）的負和正座標，所以 `Translate` 畫布上的轉換會用來將它移到中央。 「轉譯」轉換後面接著一個 `Scale` 轉換，它會套用調整因數，讓無限大的正負號變大，同時仍保持在畫布的寬度和高度的95% 內。 

請注意， `STROKE_WIDTH` 常數會加入至路徑周框的寬度和高度。 此路徑會以此寬度的線條進行繪製，因此，呈現的無限大大小大小會增加到所有四側的一半寬度：

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

查看傳遞為前兩個引數的點 `SKShader.CreateLinearGradient` 。 這些點是以原始的路徑周框為基礎。 第一個重點是（ &ndash; 250， &ndash; 100），而第二個是（250，100）。 在 SkiaSharp 內部，這些點會受到目前畫布的轉換，使其能以顯示的無限大符號正確對齊。

如果沒有的最後一個引數 `CreateLinearGradient` ，您會看到彩虹漸層，從無限大符號的左上角擴充到右下方。 （實際上，漸層會從左上角延伸到周框的右下角。 呈現的無限大符號大於周框的 `STROKE_WIDTH` 所有周數值一半。 因為漸層在開頭和結尾都是紅色，而漸層是以建立 `SKShaderTileMode.Repeat` ，所以差異並不明顯。）

使用的最後一個引數時，漸層 `CreateLinearGradient` 模式會持續在影像上進行掃描：

[![無限大色彩](linear-gradient-images/InfinityColors.png "無限大色彩")](linear-gradient-images/InfinityColors-Large.png#lightbox)

## <a name="transparency-and-gradients"></a>透明度和漸層

參與漸層的色彩可以納入透明度。 漸層不會從某種色彩漸淡成另一種顏色，而是從色彩淡出到透明。 

您可以使用這項技術來進行一些有趣的效果。 其中一個傳統範例顯示繪圖物件及其反映：

[![反映梯度](linear-gradient-images/ReflectionGradient.png "反映梯度")](linear-gradient-images/ReflectionGradient-Large.png#lightbox)

反轉的文字會以漸層的色彩呈現，其頂端為50% 透明，而在底部是完全透明。 這些透明度層級會與0x80 和0的 Alpha 值相關聯。

`PaintSurface`[**反映**漸層] 頁面中的處理常式會將文字大小調整為畫布寬度的90%。 然後，它會計算 `xText` 和值，以將文字置中 `yText` 水準置中，但在相對於頁面垂直中心的基準上放置：

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

這些 `xText` 和 `yText` 值都是相同的值，可用來在 `DrawText` 處理常式底部的呼叫中顯示反映的文字 `PaintSurface` 。 不過，在該程式碼之前，您會看到的方法呼叫 `Scale` `SKCanvas` 。 這個 `Scale` 方法會以1為水準調整（不會執行任何操作），但會以1垂直縮放 &ndash; ，這可有效地將所有專案反轉。 旋轉的中心是設定為點（0， `yText` ），其中 `yText` 是畫布的垂直中心，原先是以 `info.Height` 2 來計算。

請記住，在畫布轉換之前，Skia 會使用漸層來著色繪圖物件。 繪製 unreflected 文字之後， `textBounds` 會對矩形進行移位，使其對應至顯示的文字：

```csharp
textBounds.Offset(xText, yText);
```

`CreateLinearGradient`呼叫會定義從該矩形頂端到底部的漸層。 漸層是從完全透明的藍色（ `paint.Color.WithAlpha(0)` ）到50% 透明藍色（ `paint.Color.WithAlpha(0x80)` ）。 畫布轉換會反轉文字的倒置，因此50% 透明藍色會從基準開始，並在文字頂端變成透明。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
