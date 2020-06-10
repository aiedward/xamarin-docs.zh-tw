---
title： "SkiaSharp 中的路徑效果" 描述： "本文說明各種不同的 SkiaSharp 路徑效果，可讓您將路徑用於筆劃和填滿，並以範例程式碼示範。
skiasharp 的 assetid： 95167D1F-A718-405A-AFCC-90E596D422F3 author： davidbritch ms-chap： dabritch ms. date： 07/29/2017 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="path-effects-in-skiasharp"></a>SkiaSharp 中的路徑效果

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索可讓路徑用於筆劃和填滿的各種路徑效果_

*路徑效果*是類別的實例 [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) ，它是由類別所定義的八個靜態建立方法之一所建立。 `SKPathEffect`物件接著會設定為物件的 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) 屬性，以 [`SKPaint`](xref:SkiaSharp.SKPaint) 取得各種有趣的效果，例如，使用小型複寫的路徑對行執行筆劃：

![連結的連鎖範例](effects-images/patheffectsample.png)

路徑效果可讓您：

- 以點和虛線繪製線條
- 以任何填滿的路徑繪製線條
- 以影線線條填滿區域
- 使用並排路徑填滿區域
- 將尖角進位
- 將隨機的「抖動」新增至線條和曲線

此外，您可以結合兩個或多個路徑效果。

本文也會示範如何使用的 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) 方法，藉 `SKPaint` 由套用的屬性 `SKPaint` （包括和），將一個路徑轉換成另一個路徑 `StrokeWidth` `PathEffect` 。 這會產生一些有趣的技術，例如取得另一個路徑外框的路徑。 `GetFillPath`也有助於連接路徑效果。

## <a name="dots-and-dashes"></a>點和虛線

此方法的用法 [`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) 已于文章[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)中說明。 方法的第一個引數是包含偶數的兩個或多個值的陣列，會在破折號的長度和虛線之間的間距長度之間交替：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

這些值*不*是筆劃寬度的相對路徑。 例如，如果筆觸寬度為10，而您想要由正方形破折號和正方形間距組成的線條，請將 `intervals` 陣列設定為 {10，10}。 `phase`引數會指出行開始的虛線模式內的位置。 在此範例中，如果您想要讓線條以正方形間距開頭，請將設定 `phase` 為10。

虛線的結尾會受到的 `StrokeCap` 屬性影響 `SKPaint` 。 對於寬筆劃寬度而言，將此屬性設定為以四捨五入虛線的結尾是非常常見的 `SKStrokeCap.Round` 。 在此情況下，陣列中的值 `intervals` *不會*包含舍入所產生的額外長度。 這種事實表示圓形點需要將寬度指定為零。 若筆觸寬度為10，若要在相同直徑的點之間建立具有迴圈點和間距的線條，請使用 `intervals` {0，20} 的陣列。

**動畫虛線文字**頁面類似于[**整合文字和圖形**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)中所述的外框**文字頁面，** 其會將物件的屬性設定為，以顯示外框文字字元 `Style` `SKPaint` `SKPaintStyle.Stroke` 。 此外，**動畫的虛線文字**會使用 `SKPathEffect.CreateDash` 來提供此外框的外觀，而程式也會以動畫顯示 `phase` 方法的引數， `SKPathEffect.CreateDash` 讓這些點看起來就像是在文字字元的前後移動。 以下是橫向模式的頁面：

[![動畫虛線文字頁面的三重螢幕擷取畫面](effects-images/animateddottedtext-small.png)](effects-images/animateddottedtext-large.png#lightbox)

[`AnimatedDottedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別一開始會定義一些常數，也會覆寫 `OnAppearing` 動畫的和 `OnDisappearing` 方法：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    const string text = "DOTTED";
    const float strokeWidth = 10;
    static readonly float[] dashArray = { 0, 2 * strokeWidth };

    SKCanvasView canvasView;
    bool pageIsActive;

    public AnimatedDottedTextPage()
    {
        Title = "Animated Dotted Text";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    protected override void OnAppearing()
    {
        base.OnAppearing();
        pageIsActive = true;

        Device.StartTimer(TimeSpan.FromSeconds(1f / 60), () =>
        {
            canvasView.InvalidateSurface();
            return pageIsActive;
        });
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        pageIsActive = false;
    }
    ...
}
```

`PaintSurface`處理常式一開始會先建立 `SKPaint` 物件來顯示文字。 `TextSize`屬性會根據螢幕的寬度進行調整：

```csharp
public class AnimatedDottedTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create an SKPaint object to display the text
        using (SKPaint textPaint = new SKPaint
            {
                Style = SKPaintStyle.Stroke,
                StrokeWidth = strokeWidth,
                StrokeCap = SKStrokeCap.Round,
                Color = SKColors.Blue,
            })
        {
            // Adjust TextSize property so text is 95% of screen width
            float textWidth = textPaint.MeasureText(text);
            textPaint.TextSize *= 0.95f * info.Width / textWidth;

            // Find the text bounds
            SKRect textBounds = new SKRect();
            textPaint.MeasureText(text, ref textBounds);

            // Calculate offsets to center the text on the screen
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            // Animate the phase; t is 0 to 1 every second
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 1 / 1);
            float phase = -t * 2 * strokeWidth;

            // Create dotted line effect based on dash array and phase
            using (SKPathEffect dashEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                // Set it to the paint object
                textPaint.PathEffect = dashEffect;

                // And draw the text
                canvas.DrawText(text, xText, yText, textPaint);
            }
        }
    }
}
```

在方法的結尾處， `SKPathEffect.CreateDash` 會使用 `dashArray` 定義為欄位的來呼叫方法，並使用動畫 `phase` 值。 `SKPathEffect`實例會設定為 `PathEffect` `SKPaint` 要顯示文字之物件的屬性。

或者，您可以先將 `SKPathEffect` 物件設定為 `SKPaint` 物件，再測量文字並將它置中在頁面上。 不過，在這種情況下，動畫的點和虛線會造成轉譯文字大小的變化，而文字通常會震動一點。 （試試看！）

您也會注意到，隨著文字字元的動畫點圓，在每個封閉的曲線中都有一個點，其中的點似乎會彈出並不存在。 這是定義字元外框的路徑開始和結束的位置。 如果路徑長度不是破折號模式長度的整數倍數（在此案例中為20圖元），則只有該模式的一部分可以放在路徑的結尾。

您可以調整虛線模式的長度以符合路徑的長度，但必須判斷路徑的長度，也就是檔[**路徑資訊和列舉**](information.md)一文所涵蓋的技術。

**點/虛線變形**程式會以動畫的方式繪製虛線模式本身，讓虛線看起來像是虛線，這會再次結合為破折號：

[![點劃線變形頁面的三重螢幕擷取畫面](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

[`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別會覆寫 `OnAppearing` 和 `OnDisappearing` 方法，就像先前的程式一樣，但類別會將 `SKPaint` 物件定義為欄位：

```csharp
public class DotDashMorphPage : ContentPage
{
    const float strokeWidth = 30;
    static readonly float[] dashArray = new float[4];

    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint ellipsePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = strokeWidth,
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Create elliptical path
        using (SKPath ellipsePath = new SKPath())
        {
            ellipsePath.AddOval(new SKRect(50, 50, info.Width - 50, info.Height - 50));

            // Create animated path effect
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 3 / 3);
            float phase = 0;

            if (t < 0.25f)  // 1, 0, 1, 2 --> 0, 2, 0, 2
            {
                float tsub = 4 * t;
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2 * tsub;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2;
            }
            else if (t < 0.5f)  // 0, 2, 0, 2 --> 1, 2, 1, 0
            {
                float tsub = 4 * (t - 0.25f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2 * (1 - tsub);
                phase = strokeWidth * tsub;
            }
            else if (t < 0.75f) // 1, 2, 1, 0 --> 0, 2, 0, 2
            {
                float tsub = 4 * (t - 0.5f);
                dashArray[0] = strokeWidth * (1 - tsub);
                dashArray[1] = strokeWidth * 2;
                dashArray[2] = strokeWidth * (1 - tsub);
                dashArray[3] = strokeWidth * 2 * tsub;
                phase = strokeWidth * (1 - tsub);
            }
            else               // 0, 2, 0, 2 --> 1, 0, 1, 2
            {
                float tsub = 4 * (t - 0.75f);
                dashArray[0] = strokeWidth * tsub;
                dashArray[1] = strokeWidth * 2 * (1 - tsub);
                dashArray[2] = strokeWidth * tsub;
                dashArray[3] = strokeWidth * 2;
            }

            using (SKPathEffect pathEffect = SKPathEffect.CreateDash(dashArray, phase))
            {
                ellipsePaint.PathEffect = pathEffect;
                canvas.DrawPath(ellipsePath, ellipsePaint);
            }
        }
    }
}
```

`PaintSurface`處理常式會根據頁面大小建立橢圓形路徑，並執行設定和變數的長區段程式碼 `dashArray` `phase` 。 當動畫變數的 `t` 範圍介於0到1之間 `if` 時，區塊會將該時間分解成四季，而在每個季中，其 `tsub` 範圍也會從0到1。 最後，程式會建立， `SKPathEffect` 並將它設定為要繪製的 `SKPaint` 物件。

## <a name="from-path-to-path"></a>從路徑到路徑

的 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) 方法會 `SKPaint` 根據物件中的設定，將一個路徑轉換成另一個路徑 `SKPaint` 。 若要查看其運作方式，請 `canvas.DrawPath` 將上一個程式中的呼叫取代為下列程式碼：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在這個新的程式碼中， `GetFillPath` 呼叫 `ellipsePath` 會將（這只是一個橢圓形）轉換成 `newPath` ，然後顯示 `newPaint` 。 `newPaint`物件是使用所有的預設屬性設定所建立，但 `Style` 屬性是根據中的布林值傳回值所設定 `GetFillPath` 。

除了色彩以外，視覺效果完全相同，但不會設定 `ellipsePaint` `newPaint` 。 不是中所定義的簡單橢圓形，而是 `ellipsePath` `newPath` 包含許多定義一系列點和虛線的路徑分佈。 這是將的各種屬性 `ellipsePaint` （具體而言，、 `StrokeWidth` `StrokeCap` 和 `PathEffect` ）套用至 `ellipsePath` ，並將結果路徑放入 `newPath` 的結果。 方法會傳回 `GetFillPath` 布林值，指出是否要填入目的地路徑; 在此範例中，傳回值是用 `true` 來填滿路徑。

嘗試將 `Style` 中的設定變更 `newPaint` 為 `SKPaintStyle.Stroke` ，您將會看到個別的路徑輪廓，以一個圖元寬度的線條加以概述。

## <a name="stroking-with-a-path"></a>具有路徑的筆劃

[`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle))方法在概念上類似于， `SKPathEffect.CreateDash` 不同之處在于您指定路徑，而不是虛線和間距的模式。 這個路徑會多次複寫，以繪製線條或曲線。

語法為：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

一般來說，您傳遞給的路徑將會很 `Create1DPath` 小，並以點（0，0）為中心。 `advance`參數會指出路徑中心與路徑在行上複寫的距離。 您通常會將此引數設定為路徑的大約寬度。 `phase`引數在這裡扮演的角色與在方法中的相同 `CreateDash` 。

[`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle)有三個成員：

- `Translate`
- `Rotate`
- `Morph`

`Translate`成員會使路徑保持與沿著線條或曲線複寫的方向相同。 `Rotate`若為，則會根據曲線的正切函數旋轉路徑。 路徑的水平線具有正常方向。 `Morph`類似于， `Rotate` 不同之處在于路徑本身也會彎曲，以符合所繪製線條的曲率。

[ **1D 路徑] 效果**頁面會示範這三個選項。 [**OneDimensionalPathEffectPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)會定義一個選擇器，其中包含三個對應至列舉型別成員的專案：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.OneDimensionalPathEffectPage"
             Title="1D Path Effect">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker x:Name="effectStylePicker"
                Title="Effect Style"
                Grid.Row="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type x:String}">
                    <x:String>Translate</x:String>
                    <x:String>Rotate</x:String>
                    <x:String>Morph</x:String>
                </x:Array>
            </Picker.ItemsSource>
            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           Grid.Row="1" />
    </Grid>
</ContentPage>
```

[**OneDimensionalPathEffectPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)程式碼後置檔案會將三個 `SKPathEffect` 物件定義為欄位。 這些全都是使用搭配 `SKPathEffect.Create1DPath` `SKPath` 使用建立的物件來建立 `SKPath.ParseSvgPathData` 。 第一個是簡單的方塊，第二個是菱形圖形，而第三個是矩形。 這些是用來示範三種效果樣式：

```csharp
public partial class OneDimensionalPathEffectPage : ContentPage
{
    SKPathEffect translatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 -10 L 10 -10, 10 10, -10 10 Z"),
                                  24, 0, SKPath1DPathEffectStyle.Translate);

    SKPathEffect rotatePathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -10 0 L 0 -10, 10 0, 0 10 Z"),
                                  20, 0, SKPath1DPathEffectStyle.Rotate);

    SKPathEffect morphPathEffect =
        SKPathEffect.Create1DPath(SKPath.ParseSvgPathData("M -25 -10 L 25 -10, 25 10, -25 10 Z"),
                                  55, 0, SKPath1DPathEffectStyle.Morph);

    SKPaint pathPaint = new SKPaint
    {
        Color = SKColors.Blue
    };

    public OneDimensionalPathEffectPage()
    {
        InitializeComponent();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath path = new SKPath())
        {
            path.MoveTo(new SKPoint(0, 0));
            path.CubicTo(new SKPoint(2 * info.Width, info.Height),
                         new SKPoint(-info.Width, info.Height),
                         new SKPoint(info.Width, 0));

            switch ((string)effectStylePicker.SelectedItem))
            {
                case "Translate":
                    pathPaint.PathEffect = translatePathEffect;
                    break;

                case "Rotate":
                    pathPaint.PathEffect = rotatePathEffect;
                    break;

                case "Morph":
                    pathPaint.PathEffect = morphPathEffect;
                    break;
            }

            canvas.DrawPath(path, pathPaint);
        }
    }
}
```

`PaintSurface`處理常式會建立迴圈的貝茲曲線，並存取選擇器來判斷 `PathEffect` 應該使用哪一個來進行筆觸。 這三個選項（ `Translate` 、 `Rotate` 和 `Morph` ）會從左至右顯示：

[![1D 路徑效果頁面的三重螢幕擷取畫面](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

方法中指定的路徑 `SKPathEffect.Create1DPath` 一律會填入。 `DrawPath`如果 `SKPaint` 物件的 `PathEffect` 屬性設定為 1d path 效果，則在方法中指定的路徑一律會進行繪製。 請注意， `pathPaint` 物件沒有任何 `Style` 設定，其通常預設為 `Fill` ，但此路徑會進行繪製，而不管。

範例中使用的 `Translate` 方塊是20圖元正方形，而 `advance` 引數則設定為24。 這項差異會線上條大約是水準或垂直時，讓方塊之間出現間距，但當線條為對角線時，方塊會重迭一點，因為方塊的對角線為28.3 圖元。

範例中的菱形圖形 `Rotate` 也是20圖元寬。 `advance`會設為20，讓點繼續觸控，而菱形會與線條的曲率一起旋轉。

範例中的矩形圖形 `Morph` 是50圖元寬，而 `advance` 設定為55，會在矩形之間形成輕微間隙，因為它們在貝茲曲線周圍彎曲。

如果 `advance` 引數小於路徑的大小，則複寫的路徑可能會重迭。 這可能會產生一些有趣的效果。 [**連結的連鎖**] 頁面會顯示一連串的重迭圓形，看起來像是連結的鏈，這會在 catenary 的獨特圖形中停止回應：

[![連結鏈頁面的三重螢幕擷取畫面](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

看起來非常接近，而且您會看到這些不是真正的圓形。 鏈中的每個連結都是兩個弧形，大小和位置，因此它們似乎會與連續的連結連接。

統一權數散發的連鎖或纜線會以 catenary 的形式停止回應。 以反轉 catenary 形式建立的外型，從一間外型的權數分散程度的壓力來獲益。 Catenary 有一個看似簡單的數學描述：

`y = a · cosh(x / a)`

*Cosh*是雙曲余弦函數。 若*x*等於0，則*cosh*為零，而*y*等於*a*。 這就是 catenary 的中心。 就像*余弦*函數一樣， *cosh*會被視為*偶數*，這表示*cosh （– x）* 等於*cosh （x）*，而值則會增加，以提高正或負引數。 這些值會描述形成 catenary 邊的曲線。

尋找*適當的值以符合*手機頁面尺寸的 catenary，並不是直接計算。 如果*w*和*h*是矩形的寬度和高度，*則的最佳值會滿足下列*方程式：

`cosh(w / 2 / a) = 1 + h / a`

類別中的下列方法 [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) 會藉由參考等號左邊和右邊的兩個運算式來併入該相等 `left` `right` 。 對於*的*小數值而言， `left` 大於; 如果的 `right` 大數值為， *a* `left` 則小於 `right` 。 迴圈會在的 `while` 最佳值上縮小： *a*

```csharp
float FindOptimumA(float width, float height)
{
    Func<float, float> left = (float a) => (float)Math.Cosh(width / 2 / a);
    Func<float, float> right = (float a) => 1 + height / a;

    float gtA = 1;         // starting value for left > right
    float ltA = 10000;     // starting value for left < right

    while (Math.Abs(gtA - ltA) > 0.1f)
    {
        float avgA = (gtA + ltA) / 2;

        if (left(avgA) < right(avgA))
        {
            ltA = avgA;
        }
        else
        {
            gtA = avgA;
        }
    }

    return (gtA + ltA) / 2;
}
```

`SKPath`連結的物件是在類別的函式中建立，然後將結果 `SKPathEffect` 物件設定為 `PathEffect` `SKPaint` 儲存為欄位之物件的屬性：

```csharp
public class LinkedChainPage : ContentPage
{
    const float linkRadius = 30;
    const float linkThickness = 5;

    Func<float, float, float> catenary = (float a, float x) => (float)(a * Math.Cosh(x / a));

    SKPaint linksPaint = new SKPaint
    {
        Color = SKColors.Silver
    };

    public LinkedChainPage()
    {
        Title = "Linked Chain";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the individual links
        SKRect outer = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
        SKRect inner = outer;
        inner.Inflate(-linkThickness, -linkThickness);

        using (SKPath linkPath = new SKPath())
        {
            linkPath.AddArc(outer, 55, 160);
            linkPath.ArcTo(inner, 215, -160, false);
            linkPath.Close();

            linkPath.AddArc(outer, 235, 160);
            linkPath.ArcTo(inner, 395, -160, false);
            linkPath.Close();

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(linkPath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);
        }
    }
    ...
}
```

處理常式的主要工作 `PaintSurface` 是建立 catenary 本身的路徑。 判斷最佳*a*並將它儲存在變數中之後 `optA` ，它也需要計算來自視窗頂端的位移。 然後，它可以累積 `SKPoint` catenary 的值集合、將它轉換成路徑，然後使用先前建立的物件繪製路徑 `SKPaint` ：

```csharp
public class LinkedChainPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Width and height of catenary
        int width = info.Width;
        float height = info.Height - linkRadius;

        // Find the optimum 'a' for this width and height
        float optA = FindOptimumA(width, height);

        // Calculate the vertical offset for that value of 'a'
        float yOffset = catenary(optA, -width / 2);

        // Create a path for the catenary
        SKPoint[] points = new SKPoint[width];

        for (int x = 0; x < width; x++)
        {
            points[x] = new SKPoint(x, yOffset - catenary(optA, x - width / 2));
        }

        using (SKPath path = new SKPath())
        {
            path.AddPoly(points, false);

            // And render that path with the linksPaint object
            canvas.DrawPath(path, linksPaint);
        }
    }
    ...
}
```

此程式會定義在中用 `Create1DPath` 來將其（0，0）點置中的路徑。 這似乎是合理的，因為路徑的（0，0）點會與它所裝飾的線條或曲線對齊。 不過，您可以使用非置中（0，0）點來表示某些特殊效果。

[**輸送機皮帶**] 頁面會建立一個類似于 oblong 輸送機皮帶的路徑，其大小為上而下為視窗的尺寸。 該路徑會以一個簡單的 `SKPaint` 物件（寬20圖元）和彩色灰色繪製，然後再以另一個物件進行繪製，而 `SKPaint` `SKPathEffect` 物件參考的路徑類似于小值區：

[![[輸送機皮帶] 頁面的三重螢幕擷取畫面](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

值區路徑的（0，0）點是控制碼，因此當引數以動畫顯示時， `phase` 值區似乎會繞著輸送機皮帶旋轉，可能會在底部 scooping，並將其傾印到頂端。

[`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)類別會使用和方法的覆寫來執行動畫 `OnAppearing` `OnDisappearing` 。 值區的路徑定義于頁面的函式中：

```csharp
public class ConveyorBeltPage : ContentPage
{
    SKCanvasView canvasView;
    bool pageIsActive = false;

    SKPaint conveyerPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 20,
        Color = SKColors.DarkGray
    };

    SKPath bucketPath = new SKPath();

    SKPaint bucketsPaint = new SKPaint
    {
        Color = SKColors.BurlyWood,
    };

    public ConveyorBeltPage()
    {
        Title = "Conveyor Belt";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Create the path for the bucket starting with the handle
        bucketPath.AddRect(new SKRect(-5, -3, 25, 3));

        // Sides
        bucketPath.AddRoundedRect(new SKRect(25, -19, 27, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);
        bucketPath.AddRoundedRect(new SKRect(63, -19, 65, 18), 10, 10,
                                  SKPathDirection.CounterClockwise);

        // Five slats
        for (int i = 0; i < 5; i++)
        {
            bucketPath.MoveTo(25, -19 + 8 * i);
            bucketPath.LineTo(25, -13 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.CounterClockwise, 65, -13 + 8 * i);
            bucketPath.LineTo(65, -19 + 8 * i);
            bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                             SKPathDirection.Clockwise, 25, -19 + 8 * i);
            bucketPath.Close();
        }

        // Arc to suggest the hidden side
        bucketPath.MoveTo(25, -17);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.Clockwise, 65, -17);
        bucketPath.LineTo(65, -19);
        bucketPath.ArcTo(50, 50, 0, SKPathArcSize.Small,
                         SKPathDirection.CounterClockwise, 25, -19);
        bucketPath.Close();

        // Make it a little bigger and correct the orientation
        bucketPath.Transform(SKMatrix.MakeScale(-2, 2));
        bucketPath.Transform(SKMatrix.MakeRotationDegrees(90));
    }
    ...
```

值區建立程式碼完成時，會有兩個轉換，讓值區變得更大，並將其翻轉。 套用這些轉換比調整先前程式碼中的所有座標更容易。

`PaintSurface`處理常式一開始會定義傳送帶的路徑。 這只是一對線條，而一對以20圖元寬的暗灰色線條繪製的半圈：

```csharp
public class ConveyorBeltPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float width = info.Width / 3;
        float verticalMargin = width / 2 + 150;

        using (SKPath conveyerPath = new SKPath())
        {
            // Straight verticals capped by semicircles on top and bottom
            conveyerPath.MoveTo(width, verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, 2 * width, verticalMargin);
            conveyerPath.LineTo(2 * width, info.Height - verticalMargin);
            conveyerPath.ArcTo(width / 2, width / 2, 0, SKPathArcSize.Large,
                               SKPathDirection.Clockwise, width, info.Height - verticalMargin);
            conveyerPath.Close();

            // Draw the conveyor belt itself
            canvas.DrawPath(conveyerPath, conveyerPaint);

            // Calculate spacing based on length of conveyer path
            float length = 2 * (info.Height - 2 * verticalMargin) +
                           2 * ((float)Math.PI * width / 2);

            // Value will be somewhere around 200
            float spacing = length / (float)Math.Round(length / 200);

            // Now animate the phase; t is 0 to 1 every 2 seconds
            TimeSpan timeSpan = new TimeSpan(DateTime.Now.Ticks);
            float t = (float)(timeSpan.TotalSeconds % 2 / 2);
            float phase = -t * spacing;

            // Create the buckets PathEffect
            using (SKPathEffect bucketsPathEffect =
                        SKPathEffect.Create1DPath(bucketPath, spacing, phase,
                                                  SKPath1DPathEffectStyle.Rotate))
            {
                // Set it to the Paint object and draw the path again
                bucketsPaint.PathEffect = bucketsPathEffect;
                canvas.DrawPath(conveyerPath, bucketsPaint);
            }
        }
    }
}
```

繪製傳送帶的邏輯無法以橫向模式運作。

「值區」的距離應介於「輸送機皮帶」上的大約200圖元。 不過，輸送機皮帶可能不是200圖元長度的倍數，這表示當的 `phase` 引數為 `SKPathEffect.Create1DPath` 動畫時，值區會彈出並不存在。

基於這個理由，程式會先計算名為 `length` 的值，這是傳送帶的長度。 因為傳送帶包含直線和半圓，所以這是簡單的計算。 接下來，值區的數目是以200除以來計算 `length` 。 這會四捨五入為最接近的整數，然後該數位會分割成 `length` 。 結果是值區整數的間距。 `phase`引數只是其中的一小部分。

## <a name="from-path-to-path-again"></a>再次從路徑到路徑

在 [ `DrawSurface` **輸送機皮帶**] 的處理常式底部，將呼叫標記為批註， `canvas.DrawPath` 並將其取代為下列程式碼：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

如同先前的範例 `GetFillPath` ，您會看到除了色彩以外的結果都相同。 在執行之後 `GetFillPath` ， `newPath` 物件會包含多個值區路徑的複本，每一個都位於呼叫時位於動畫所在的相同位置。

## <a name="hatching-an-area"></a>將區域影線

[`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix))方法會以平行線填滿區域，通常稱為*影線線*。 此方法具有下列語法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`引數會指定影線線條的筆劃寬度。 `matrix`參數是縮放和選擇性旋轉的組合。 縮放比例表示 Skia 用來空間影線線條的圖元增量。 線條之間的分隔是縮放比例減去 `width` 引數。 如果縮放比例小於或等於 `width` 值，影線線條之間就不會有空格，而且區域會顯示為已填滿。 針對水準和垂直縮放指定相同的值。

根據預設，影線線條是水準的。 如果 `matrix` 參數包含旋轉，則影線線條會順時針旋轉。

[**影線填滿**] 頁面會示範此路徑效果。 [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs)類別會將三個路徑效果定義為欄位，第一個是水準影線線條，寬度為3圖元，縮放因數表示它們的間距為6個圖元。 因此，線條間的分隔就是三個圖元。 第二個路徑效果適用于寬度為六圖元的垂直影線線條，並以24圖元分隔（因此，分隔為18圖元），而第三個則適用于對角影線線條12圖元寬36圖元的間距。

```csharp
public class HatchFillPage : ContentPage
{
    SKPaint fillPaint = new SKPaint();

    SKPathEffect horzLinesPath = SKPathEffect.Create2DLine(3, SKMatrix.MakeScale(6, 6));

    SKPathEffect vertLinesPath = SKPathEffect.Create2DLine(6,
        Multiply(SKMatrix.MakeRotationDegrees(90), SKMatrix.MakeScale(24, 24)));

    SKPathEffect diagLinesPath = SKPathEffect.Create2DLine(12,
        Multiply(SKMatrix.MakeScale(36, 36), SKMatrix.MakeRotationDegrees(45)));

    SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 3,
        Color = SKColors.Black
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

請注意矩陣 `Multiply` 方法。 因為水準和垂直縮放比例是相同的，所以縮放和旋轉矩陣的相乘順序並不重要。

此 `PaintSurface` 處理常式會使用三種不同色彩結合的三種路徑效果， `fillPaint` 以填滿已調整大小的圓角矩形以符合頁面。 `Style`會忽略上設定的屬性 `fillPaint` ; 當 `SKPaint` 物件包含從建立的路徑效果時 `SKPathEffect.Create2DLine` ，就會填入區域，不論：

```csharp
public class HatchFillPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath roundRectPath = new SKPath())
        {
            // Create a path
            roundRectPath.AddRoundedRect(
                new SKRect(50, 50, info.Width - 50, info.Height - 50), 100, 100);

            // Horizontal hatch marks
            fillPaint.PathEffect = horzLinesPath;
            fillPaint.Color = SKColors.Red;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Vertical hatch marks
            fillPaint.PathEffect = vertLinesPath;
            fillPaint.Color = SKColors.Blue;
            canvas.DrawPath(roundRectPath, fillPaint);

            // Diagonal hatch marks -- use clipping
            fillPaint.PathEffect = diagLinesPath;
            fillPaint.Color = SKColors.Green;

            canvas.Save();
            canvas.ClipPath(roundRectPath);
            canvas.DrawRect(new SKRect(0, 0, info.Width, info.Height), fillPaint);
            canvas.Restore();

            // Outline the path
            canvas.DrawPath(roundRectPath, strokePaint);
        }
    }
    ...
}
```

如果您仔細查看結果，您會看到紅色和藍色影線線條不會精確地限制為圓角矩形。 （這顯然是基礎 Skia 程式碼的特性）。如果不滿意，則會顯示綠色對角線影線線的替代方法：圓角矩形會當做裁剪路徑使用，而影線線條則會在整個頁面上繪製。

`PaintSurface`處理常式會以呼叫來結束，只需為圓角矩形加上筆劃，讓您可以看到具有紅色和藍色影線線的差異：

[![影線填滿頁的三向螢幕擷取畫面](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

Android 畫面看起來不像這樣：螢幕擷取畫面的縮放會導致精簡的紅線和細空格合併成看似較寬的紅線和更寬的空間。

## <a name="filling-with-a-path"></a>以路徑填滿

可 [`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) 讓您使用水準和垂直複製的路徑來填滿區域，實際上會並排區域：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`縮放比例表示複寫路徑的水準和垂直間距。 但是，您無法使用這個引數來旋轉路徑 `matrix` ; 如果您想要旋轉路徑，請使用所定義的方法來旋轉路徑本身 `Transform` `SKPath` 。

複寫的路徑通常會與螢幕的左邊和上邊緣對齊，而不是已填滿的區域。 您可以藉由提供介於0和縮放因數之間的轉譯因數來覆寫此行為，以指定從左邊和右上方的水準和垂直位移。

[**路徑磚填滿**] 頁面會示範此路徑效果。 用於並排分區的路徑會定義為類別中的欄位 [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) 。 水準和垂直座標的範圍從–40到40，這表示此路徑是80圖元正方形：

```csharp
public class PathTileFillPage : ContentPage
{
    SKPath tilePath = SKPath.ParseSvgPathData(
        "M -20 -20 L 2 -20, 2 -40, 18 -40, 18 -20, 40 -20, " +
        "40 -12, 20 -12, 20 12, 40 12, 40 40, 22 40, 22 20, " +
        "-2 20, -2 40, -20 40, -20 8, -40 8, -40 -8, -20 -8 Z");
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Red;

            using (SKPathEffect pathEffect =
                   SKPathEffect.Create2DPath(SKMatrix.MakeScale(64, 64), tilePath))
            {
                paint.PathEffect = pathEffect;

                canvas.DrawRoundRect(
                    new SKRect(50, 50, info.Width - 50, info.Height - 50),
                    100, 100, paint);
            }
        }
    }
}
```

在 `PaintSurface` 處理常式中， `SKPathEffect.Create2DPath` 呼叫會將水準和垂直間距設定為64，使80圖元的正方形磚重迭。 幸好，此路徑類似于謎題，meshing 與連續的磚完美搭配：

[![路徑磚填滿頁面的三向螢幕擷取畫面](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

從原始螢幕擷取畫面進行調整會造成一些失真，特別是在 Android 畫面上。

請注意，這些磚一律會整體出現，而且永遠不會被截斷。 在前兩個螢幕擷取畫面中，所填滿的區域是圓角矩形，甚至不明顯。 如果您想要將這些磚截斷到特定區域，請使用裁剪路徑。

嘗試將 `Style` 物件的屬性設定 `SKPaint` 為 `Stroke` ，您會看到個別的磚，而不是已填滿。

您也可以使用並排顯示點陣圖來填滿區域，如[**SkiaSharp 點陣圖**](../effects/shaders/bitmap-tiling.md)磚一文所示。

## <a name="rounding-sharp-corners"></a>進位尖角

以[**三種方式繪製弧線**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)發行項的**圓角 Heptagon**程式，是使用正切弧線來曲線七邊的圖表點。 **另一個 [四捨五入的 Heptagon** ] 頁面會顯示更簡單的方法，使用從方法建立的路徑效果 [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) ：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

雖然單一引數命名為 `radius` ，但您必須將它設定為所需的圓角半徑一半。 （這是基礎 Skia 程式碼的特性）。

以下是 `PaintSurface` 類別中的處理常式 [`AnotherRoundedHeptagonPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs) ：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    int numVertices = 7;
    float radius = 0.45f * Math.Min(info.Width, info.Height);
    SKPoint[] vertices = new SKPoint[numVertices];
    double vertexAngle = -0.5f * Math.PI;       // straight up

    // Coordinates of the vertices of the polygon
    for (int vertex = 0; vertex < numVertices; vertex++)
    {
        vertices[vertex] = new SKPoint(radius * (float)Math.Cos(vertexAngle),
                                       radius * (float)Math.Sin(vertexAngle));
        vertexAngle += 2 * Math.PI / numVertices;
    }

    float cornerRadius = 100;

    // Create the path
    using (SKPath path = new SKPath())
    {
        path.AddPoly(vertices, true);

        // Render the path in the center of the screen
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = 10;

            // Set argument to half the desired corner radius!
            paint.PathEffect = SKPathEffect.CreateCorner(cornerRadius / 2);

            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.DrawPath(path, paint);

            // Uncomment DrawCircle call to verify corner radius
            float offset = cornerRadius / (float)Math.Sin(Math.PI * (numVertices - 2) / numVertices / 2);
            paint.Color = SKColors.Green;
            // canvas.DrawCircle(vertices[0].X, vertices[0].Y + offset, cornerRadius, paint);
        }
    }
}
```

您可以將此效果用於筆劃，或根據物件的屬性填滿 `Style` `SKPaint` 。 它正在執行：

[![另一個圓角 Heptagon 頁面的三向螢幕擷取畫面](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

您會看到這個舍入的 heptagon 與先前的程式相同。 如果您需要更多的方式來判斷圓角半徑是真正的100，而不是呼叫中指定的 50 `SKPathEffect.CreateCorner` ，您可以取消批註程式中的最後一個語句，並查看在角落上重迭的100半徑圓圈。

## <a name="random-jitter"></a>隨機抖動

有時候電腦圖形的完美直線就不是您想要的，而且需要一些隨機性。 在此情況下，您會想要嘗試 [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) 方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

您可以將此路徑效果用於筆劃或填滿。 線條會分成已連接的區段（指定的大約長度 `segLength` ），並以不同的方向擴充。 原始行的偏差範圍是由所指定 `deviation` 。

最後一個引數是用來產生用於效果之虛擬隨機序列的種子。 不同種子的抖動效果看起來會有點不同。 引數的預設值為零，表示當您執行程式時，效果會相同。 如果您想要在螢幕重新繪製時使用不同的抖動，可以將種子設定為 `Millisecond` 值的屬性 `DataTime.Now` （例如）。

[**抖動實驗**] 頁面可讓您以矩形的筆劃來試驗不同的值：

[![[JitterExperiment] 頁面的三重螢幕擷取畫面](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

程式很簡單。 [**JitterExperimentPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)會具現化兩個 `Slider` 元素和 `SKCanvasView` ：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Curves.JitterExperimentPage"
             Title="Jitter Experiment">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Label">
                    <Setter Property="HorizontalTextAlignment" Value="Center" />
                </Style>

                <Style TargetType="Slider">
                    <Setter Property="Margin" Value="20, 0" />
                    <Setter Property="Minimum" Value="0" />
                    <Setter Property="Maximum" Value="100" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="segLengthSlider"
                Grid.Row="0"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference segLengthSlider},
                              Path=Value,
                              StringFormat='Segment Length = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="deviationSlider"
                Grid.Row="2"
                ValueChanged="sliderValueChanged" />

        <Label Text="{Binding Source={x:Reference deviationSlider},
                              Path=Value,
                              StringFormat='Deviation = {0:F0}'}"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

`PaintSurface`每當值變更時，就會呼叫[**JitterExperimentPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)程式碼後置檔案中的處理常式 `Slider` 。 它會 `SKPathEffect.CreateDiscrete` 使用兩個 `Slider` 值來呼叫，並使用它來繪製矩形：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    float segLength = (float)segLengthSlider.Value;
    float deviation = (float)deviationSlider.Value;

    using (SKPaint paint = new SKPaint())
    {
        paint.Style = SKPaintStyle.Stroke;
        paint.StrokeWidth = 5;
        paint.Color = SKColors.Blue;

        using (SKPathEffect pathEffect = SKPathEffect.CreateDiscrete(segLength, deviation))
        {
            paint.PathEffect = pathEffect;

            SKRect rect = new SKRect(100, 100, info.Width - 100, info.Height - 100);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

您也可以使用此效果進行填滿，在此情況下，填滿區域的外框會受到這些隨機偏差的影響。 [**抖動文字**] 頁面會示範如何使用此路徑效果來顯示文字。 類別處理常式中的大部分 `PaintSurface` 程式碼 [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) 都是專門用來調整大小和置中文字：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "FUZZY";

    using (SKPaint textPaint = new SKPaint())
    {
        textPaint.Color = SKColors.Purple;
        textPaint.PathEffect = SKPathEffect.CreateDiscrete(3f, 10f);

        // Adjust TextSize property so text is 95% of screen width
        float textWidth = textPaint.MeasureText(text);
        textPaint.TextSize *= 0.95f * info.Width / textWidth;

        // Find the text bounds
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

在這裡，它是在橫向模式中執行：

[![[JitterText] 頁面的三重螢幕擷取畫面](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>路徑大綱

您已經看過的方法有兩個範例 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) `SKPaint` ，其中有兩個版本：

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

只有前兩個引數是必要的。 方法會存取引數所參考的路徑 `src` ，並根據物件中的筆觸屬性 `SKPaint` （包括屬性）修改路徑資料 `PathEffect` ，然後將結果寫入 `dst` 路徑。 `resScale`參數可減少精確度以建立較小的目的地路徑，而 `cullRect` 引數可以去除矩形外的輪廓。

此方法的其中一項基本用法並不牽涉到路徑效果：如果 `SKPaint` 物件的 `Style` 屬性設定為 `SKPaintStyle.Stroke` ，而且沒有其*not* `PathEffect` 設定，則會 `GetFillPath` 建立代表來源路徑*外框*的路徑，如同繪製屬性已繪製。

例如，如果 `src` 路徑是 radius 500 的簡單圓，而且 `SKPaint` 物件指定的筆劃寬度為100，則 `dst` 路徑會變成兩個同心圓，一個半徑為450，另一個半徑為550。 會呼叫方法， `GetFillPath` 因為填滿此 `dst` 路徑與將路徑進行筆劃的方式相同 `src` 。 但是，您也可以將 `dst` 路徑筆劃，以查看路徑外框。

**按一下以大綱顯示路徑**會示範這點。 `SKCanvasView`和 `TapGestureRecognizer` 會在[**TapToOutlineThePathPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)中具現化。 [**TapToOutlineThePathPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)程式碼後置檔案會將三個 `SKPaint` 物件定義為欄位，兩個用於筆觸寬度為100和20的筆劃，而第三個用於填滿：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    bool outlineThePath = false;

    SKPaint redThickStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 100
    };

    SKPaint redThinStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 20
    };

    SKPaint blueFill = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue
    };

    public TapToOutlineThePathPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewTapped(object sender, EventArgs args)
    {
        outlineThePath ^= true;
        (sender as SKCanvasView).InvalidateSurface();
    }
    ...
}
```

如果尚未按下該畫面，則 `PaintSurface` 處理常式會使用 `blueFill` 和 `redThickStroke` 繪製物件來轉譯迴圈路徑：

```csharp
public partial class TapToOutlineThePathPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circlePath = new SKPath())
        {
            circlePath.AddCircle(info.Width / 2, info.Height / 2,
                                 Math.Min(info.Width / 2, info.Height / 2) -
                                 redThickStroke.StrokeWidth);

            if (!outlineThePath)
            {
                canvas.DrawPath(circlePath, blueFill);
                canvas.DrawPath(circlePath, redThickStroke);
            }
            else
            {
                using (SKPath outlinePath = new SKPath())
                {
                    redThickStroke.GetFillPath(circlePath, outlinePath);

                    canvas.DrawPath(outlinePath, blueFill);
                    canvas.DrawPath(outlinePath, redThinStroke);
                }
            }
        }
    }
}
```

圓形會填滿並依照您預期的方式繪製：

[![以一般點框顯示路徑頁面的三個螢幕擷取畫面](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

當您點一下畫面時， `outlineThePath` 會設為 `true` ，而且 `PaintSurface` 處理常式會建立新的物件， `SKPath` 並在 `GetFillPath` 繪製物件的呼叫中使用該物件做為目的地路徑 `redThickStroke` 。 接著，該目的地路徑會填滿並以進行繪製 `redThinStroke` ，產生下列結果：

[![概述 [路徑] 頁面的三向外框的螢幕擷取畫面](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

這兩個紅色圓圈會清楚指出原始迴圈路徑已轉換成兩個圓形輪廓。

在開發用於方法的路徑時，這個方法很有用 `SKPathEffect.Create1DPath` 。 當複寫路徑時，一律會填入您在這些方法中指定的路徑。 如果您不想要填滿整個路徑，您必須仔細定義外框。

例如，在連結的**連鎖**範例中，連結是以四個弧形的數列定義，而每一組都是以兩個半徑為基礎，其中的每一條線會在要填滿的路徑區域中加上外框。 您可以取代類別中的程式碼 `LinkedChainPage` ，以稍微不同的方式來執行。

首先，您會想要重新定義 `linkRadius` 常數：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`現在只有兩個以該單一半徑為基礎的弧線，具有所需的開始角度和掃描角度：

```csharp
using (SKPath linkPath = new SKPath())
{
    SKRect rect = new SKRect(-linkRadius, -linkRadius, linkRadius, linkRadius);
    linkPath.AddArc(rect, 55, 160);
    linkPath.AddArc(rect, 235, 160);

    using (SKPaint strokePaint = new SKPaint())
    {
        strokePaint.Style = SKPaintStyle.Stroke;
        strokePaint.StrokeWidth = linkThickness;

        using (SKPath outlinePath = new SKPath())
        {
            strokePaint.GetFillPath(linkPath, outlinePath);

            // Set that path as the 1D path effect for linksPaint
            linksPaint.PathEffect =
                SKPathEffect.Create1DPath(outlinePath, 1.3f * linkRadius, 0,
                                          SKPath1DPathEffectStyle.Rotate);

        }

    }
}
```

`outlinePath` `linkPath` 當物件是使用中指定的屬性進行繪製時，其外框的收件者就會是 `strokePaint` 。

使用這項技術的另一個範例，是在方法中使用的路徑下一步。

## <a name="combining-path-effects"></a>結合路徑效果

的兩個最終靜態建立方法 `SKPathEffect` 是 [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) 和 [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) ：

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

這兩種方法都會結合兩個路徑效果，以建立複合路徑效果。 `CreateSum`方法會建立類似于分別套用的兩個路徑效果的路徑效果，而 `CreateCompose` 則會套用一個路徑效果（ `inner` ），然後將套用 `outer` 至該。

您已經瞭解的 `GetFillPath` 方法 `SKPaint` 可以如何根據屬性（包括）將一個路徑轉換成另一個路徑，如此一來， `SKPaint` `PathEffect` *too* `SKPaint` 使用或方法中所指定的兩個路徑效果，就不應該太神秘物件可以執行該作業兩次 `CreateSum` `CreateCompose` 。

有一個很明顯的用法 `CreateSum` ，就是定義一個 `SKPaint` 物件，它會填滿一個路徑效果的路徑，並以另一個路徑效果來進行路徑的筆劃。 這會在 [**框架中的貓**] 範例中示範，它會在具有 scalloped 邊緣的框架內顯示貓陣列：

[![框架頁面中貓的三向螢幕擷取畫面](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

[`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)類別一開始會定義數個欄位。 您可以從 [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) [**SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)一文中辨識出類別的第一個欄位。 第二個路徑是以框架的 scallop 模式的線條和弧線為基礎：

```csharp
public class CatsInFramePage : ContentPage
{
    // From PathDataCatPage.cs
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

    SKPaint catStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5
    };

    SKPath scallopPath =
        SKPath.ParseSvgPathData("M 0 0 L 50 0 A 60 60 0 0 1 -50 0 Z");

    SKPaint framePaint = new SKPaint
    {
        Color = SKColors.Black
    };
    ...
}
```

`catPath` `SKPathEffect.Create2DPath` 如果 `SKPaint` 物件 `Style` 屬性設定為，則可以在方法中使用 `Stroke` 。 不過，如果 `catPath` 直接在此程式中使用，則會填滿貓的整個標頭，而須線甚至不會顯示。 （試試看！）您必須取得該路徑的外框，並在方法中使用該外框 `SKPathEffect.Create2DPath` 。

此函式會執行此作業。 它會先將兩個轉換套用至，將 `catPath` （0，0）點移到中央，並以大小向下調整。 `GetFillPath`取得中分佈的所有外框 `outlinedCatPath` ，並在呼叫中使用該物件 `SKPathEffect.Create2DPath` 。 值中的縮放比例 `SKMatrix` 會稍微大於貓的水準和垂直大小，以提供磚之間的小緩衝區，而轉譯因數則衍生成有點廣為人知且實證，以便在框架的左上角顯示完整的貓：

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    public CatsInFramePage()
    {
        Title = "Cats in Frame";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Move (0, 0) point to center of cat path
        catPath.Transform(SKMatrix.MakeTranslation(-240, -175));

        // Now catPath is 400 by 250
        // Scale it down to 160 by 100
        catPath.Transform(SKMatrix.MakeScale(0.40f, 0.40f));

        // Get the outlines of the contours of the cat path
        SKPath outlinedCatPath = new SKPath();
        catStroke.GetFillPath(catPath, outlinedCatPath);

        // Create a 2D path effect from those outlines
        SKPathEffect fillEffect = SKPathEffect.Create2DPath(
            new SKMatrix { ScaleX = 170, ScaleY = 110,
                           TransX = 75, TransY = 80,
                           Persp2 = 1 },
            outlinedCatPath);

        // Create a 1D path effect from the scallop path
        SKPathEffect strokeEffect =
            SKPathEffect.Create1DPath(scallopPath, 75, 0, SKPath1DPathEffectStyle.Rotate);

        // Set the sum the effects to frame paint
        framePaint.PathEffect = SKPathEffect.CreateSum(fillEffect, strokeEffect);
    }
    ...
}
```

然後，此函數會呼叫 `SKPathEffect.Create1DPath` scalloped 框架。 請注意，路徑的寬度是100圖元，但前進是75圖元，因此複寫的路徑會在框架周圍重迭。 此函式的最後一個語句會呼叫 `SKPathEffect.CreateSum` 來結合這兩個路徑效果，並將結果設定為 `SKPaint` 物件。

所有此工作都可讓 `PaintSurface` 處理常式相當簡單。 它只需要定義一個矩形，然後使用來繪製它 `framePaint` ：

```csharp
public class CatsInFramePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect rect = new SKRect(50, 50, info.Width - 50, info.Height - 50);
        canvas.ClipRect(rect);
        canvas.DrawRect(rect, framePaint);
    }
}
```

Path 效果背後的演算法一律會使用於筆劃或填滿的整個路徑顯示，這可能會導致某些視覺效果出現在矩形外。 呼叫 `ClipRect` 之前的呼叫可 `DrawRect` 讓視覺效果變得更清楚。 （不用裁剪就試試看！）

通常會使用將 `SKPathEffect.CreateCompose` 一些抖動新增至另一個路徑效果。 您當然可以自行試驗，但以下是一個有點不同的範例：

**虛線影線線條**會以虛線填補具有影線線條的橢圓形。 類別中的大部分工作 [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) 都是直接在欄位定義中執行。 這些欄位會定義虛線效果和影線效果。 它們會定義為， `static` 因為它們會在定義的呼叫中被參考 `SKPathEffect.CreateCompose` `SKPaint` ：

```csharp
public class DashedHatchLinesPage : ContentPage
{
    static SKPathEffect dashEffect =
        SKPathEffect.CreateDash(new float[] { 30, 30 }, 0);

    static SKPathEffect hatchEffect = SKPathEffect.Create2DLine(20,
        Multiply(SKMatrix.MakeScale(60, 60),
                 SKMatrix.MakeRotationDegrees(45)));

    SKPaint paint = new SKPaint()
    {
        PathEffect = SKPathEffect.CreateCompose(dashEffect, hatchEffect),
        StrokeCap = SKStrokeCap.Round,
        Color = SKColors.Blue
    };
    ...
    static SKMatrix Multiply(SKMatrix first, SKMatrix second)
    {
        SKMatrix target = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

`PaintSurface`處理常式只需要包含標準額外負荷，再呼叫一次 `DrawOval` ：

```csharp
public class DashedHatchLinesPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawOval(info.Width / 2, info.Height / 2,
                        0.45f * info.Width, 0.45f * info.Height,
                        paint);
    }
    ...
}
```

就像您已經發現的，影線線不會精確地限制為區域的內部，在此範例中，它們一律從左邊開始，並包含完整的虛線：

[![虛線影線線頁的三向螢幕擷取畫面](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

現在您已經看過從簡單的點和虛線到奇怪組合的路徑效果，請使用您的想像，並查看您可以建立的內容。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
