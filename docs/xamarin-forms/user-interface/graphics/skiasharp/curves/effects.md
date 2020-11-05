---
title: SkiaSharp 中的路徑效果
description: 本文說明各種 SkiaSharp 路徑效果，這些效果可讓路徑用於筆劃和填滿，並以範例程式碼示範。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 21e06560bd67683496b10c8e8c9c3fff520fc36a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368163"
---
# <a name="path-effects-in-skiasharp"></a>SkiaSharp 中的路徑效果

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索允許路徑用於筆劃和填滿的各種路徑效果_

*路徑效果* 是 [`SKPathEffect`](xref:SkiaSharp.SKPathEffect) 類別的實例，該類別是使用類別所定義的八種靜態建立方法之一所建立。 `SKPathEffect`物件接著會設定為物件的 [`PathEffect`](xref:SkiaSharp.SKPaint.PathEffect) 屬性 [`SKPaint`](xref:SkiaSharp.SKPaint) ，以取得各種有趣的效果，例如，以小型的複寫路徑對一行進行筆劃：

![連結鏈範例](effects-images/patheffectsample.png)

路徑效果可讓您：

- 使用點和虛線來筆觸線
- 使用任何填滿的路徑來筆觸線
- 以影線線填滿區域
- 以並排顯示的路徑填滿區域
- 將尖角四捨五入
- 將隨機的「抖動」新增至線條和曲線

此外，您可以結合兩個以上的路徑效果。

本文也會示範如何使用的 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) 方法，藉 `SKPaint` 由套用的屬性 `SKPaint` （包括和）將一個路徑轉換成另一個路徑 `StrokeWidth` `PathEffect` 。 這會產生一些有趣的技巧，例如取得以另一個路徑為大綱的路徑。 `GetFillPath` 對於路徑效果的連接也很有説明。

## <a name="dots-and-dashes"></a>點和虛線

方法的使用 [`PathEffect.CreateDash`](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single)) 方式如文章中的 [**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)所述。 方法的第一個引數是一個陣列，其中包含兩個或多個值的偶數，以及虛線之間的虛線長度和間隙長度：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

這些值 *不* 會相對於筆觸寬度。 例如，如果筆劃寬度為10，而您想要以正方形連字號和正方形間距組成的線條，請將 `intervals` 陣列設定為 {10，10}。 `phase`引數指出行在虛線模式內開始的位置。 在此範例中，如果您想要以正方形間距開頭的那一行，請將設定 `phase` 為10。

虛線的結尾會受到的 `StrokeCap` 屬性影響 `SKPaint` 。 如果是寬筆劃寬度，將這個屬性設定為，以將 `SKStrokeCap.Round` 虛線的末端四捨五入是很常見的。 在此情況下，陣列中的值 `intervals` *不* 包含舍入所產生的額外長度。 這表示圓形點需要將寬度指定為零。 如果筆劃寬度為10，若要建立具有圓形點的線條和相同直徑點之間的間隙，請使用 `intervals` {0，20} 的陣列。

**動畫的虛線文字** 頁面類似于在 [ [**整合文字] 和 [圖形**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)] 中所述的 **概述文字** 頁面，因為它會將物件的屬性設為，以顯示外框文字字元 `Style` `SKPaint` `SKPaintStyle.Stroke` 。 此外， **動畫的虛線文字** 會用 `SKPathEffect.CreateDash` 來為此輪廓提供點狀外觀，而程式也會以動畫顯示方法的 `phase` 引數， `SKPathEffect.CreateDash` 讓點看起來好像是在文字字元的周圍移動。 以下是橫向模式的頁面：

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

`PaintSurface`處理常式一開始會先建立 `SKPaint` 物件以顯示文字。 `TextSize`屬性會根據畫面的寬度進行調整：

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

在方法的結尾， `SKPathEffect.CreateDash` 會使用 `dashArray` 定義為欄位的，以及動畫的值來呼叫方法 `phase` 。 `SKPathEffect`實例會設定為物件的 `PathEffect` 屬性 `SKPaint` ，以顯示文字。

或者，您可以先將 `SKPathEffect` 物件設定為 `SKPaint` 物件，然後再測量文字，並將它放在頁面上。 不過，在這種情況下，動畫的點和連字號會導致轉譯文字大小的變化，而文字通常很震動。  (試試看！ ) 

您也會注意到，當動畫的點圍繞文字字元時，每個封閉曲線中都有一個特定的點，其中的點似乎是彈出和不存在的。 這是定義字元外框開始與結束的路徑。 如果路徑長度不是虛線模式長度的整數倍數 (在此案例中為20圖元) ，則只有該模式的一部分可符合路徑的結尾。

您可以調整虛線模式的長度以符合路徑的長度，但需要判斷路徑的長度，也就是文章 [**路徑資訊和列舉**](information.md)所涵蓋的技巧。

**點/虛線的變形** 程式會將虛線模式本身繪製成動畫，讓虛線看起來像是一個點，也就是一次形成虛線：

[![點虛線變形頁面的三重螢幕擷取畫面](effects-images/dotdashmorph-small.png)](effects-images/dotdashmorph-large.png#lightbox)

[`DotDashMorphPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別會覆寫 `OnAppearing` 和 `OnDisappearing` 方法（如同先前的程式），但類別會將 `SKPaint` 物件定義為欄位：

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

此 `PaintSurface` 處理常式會根據頁面大小建立橢圓路徑，並執行可設定和變數的長段程式碼 `dashArray` `phase` 。 當動畫變數的 `t` 範圍從0到1時， `if` 區塊會將該時間分成四個季，而在每個季度中， `tsub` 也會從0到1的範圍內。 程式最後會建立， `SKPathEffect` 並將它設定為要 `SKPaint` 繪製的物件。

## <a name="from-path-to-path"></a>從路徑到路徑

的 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single)) 方法會 `SKPaint` 根據物件中的設定，將一個路徑轉換成另一個路徑 `SKPaint` 。 若要查看其運作方式，請以 `canvas.DrawPath` 下列程式碼取代上一個程式中的呼叫：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在這個新的程式碼中， `GetFillPath` 呼叫 `ellipsePath` 會將只是一個 oval) 的 (轉換成 `newPath` ，然後以顯示 `newPaint` 。 `newPaint`物件是以所有預設屬性設定建立的，但是 `Style` 屬性是根據的布林值傳回值所設定 `GetFillPath` 。

除了色彩之外，視覺效果完全相同，但不會設定為 `ellipsePaint` `newPaint` 。 不是中定義的簡單橢圓形 `ellipsePath` ，而是 `newPath` 包含許多定義一連串點和虛線的路徑輪廓。 這是套用 (的各種屬性的結果 `ellipsePaint` ，具體來說，、 `StrokeWidth` `StrokeCap` 和 `PathEffect`) ， `ellipsePath` 並將結果路徑放在中 `newPath` 。 方法會傳回 `GetFillPath` 布林值，指出是否要填入目的地路徑; 在此範例中，傳回值是用 `true` 來填滿路徑。

請嘗試將 `Style` 中的設定變更 `newPaint` 為 `SKPaintStyle.Stroke` ，然後您會看到個別的路徑輪廓（以一圖元寬度的線條框住）。

## <a name="stroking-with-a-path"></a>使用路徑進行筆劃

[`SKPathEffect.Create1DPath`](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle))方法在概念上類似于， `SKPathEffect.CreateDash` 不同之處在于您指定路徑，而不是連字號和間隙的模式。 這個路徑會複寫多次，以筆劃線條或曲線。

語法為：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

一般而言，您傳遞給的路徑 `Create1DPath` 會很小，並以點為中心， (0，0) 。 `advance`參數會指出路徑的中心與在該行上複寫路徑的距離。 您通常會將此引數設定為路徑的大約寬度。 `phase`引數在這裡扮演的角色與方法中的角色相同 `CreateDash` 。

[`SKPath1DPathEffectStyle`](xref:SkiaSharp.SKPath1DPathEffectStyle)有三個成員：

- `Translate`
- `Rotate`
- `Morph`

`Translate`成員會使路徑維持在相同的方向，因為它是沿著線條或曲線進行複寫。 `Rotate`若為，則會根據曲線的正切來旋轉路徑。 路徑的水平線條具有正常的方向。 `Morph` 類似于， `Rotate` 除了路徑本身也會彎曲，以符合正在繪製線條的曲率。

[ **1D 路徑效果** ] 頁面會示範這三個選項。 [**OneDimensionalPathEffectPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)檔案會定義一個選擇器，其中包含三個對應至列舉的三個成員的專案：

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

[**OneDimensionalPathEffectPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)程式碼後端檔案會將三個 `SKPathEffect` 物件定義為欄位。 這些都是透過使用 `SKPathEffect.Create1DPath` 建立的物件所建立 `SKPath` `SKPath.ParseSvgPathData` 。 第一個是簡單的方塊，第二個是鑽石圖形，第三個則是矩形。 這些是用來示範三種效果樣式：

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

此 `PaintSurface` 處理常式會建立迴圈的貝茲曲線，並存取選擇器來判斷應該使用哪一種 `PathEffect` 來將其繪製。 這三個選項（ `Translate` 、 `Rotate` 和 `Morph` ）會從左至右顯示：

[![1D 路徑效果頁面的三重螢幕擷取畫面](effects-images/1dpatheffect-small.png)](effects-images/1dpatheffect-large.png#lightbox)

方法中指定的路徑 `SKPathEffect.Create1DPath` 一律會填滿。 `DrawPath`如果 `SKPaint` 物件將其 `PathEffect` 屬性設定為1d 路徑效果，則方法中指定的路徑一律會進行筆劃。 請注意， `pathPaint` 物件沒有 `Style` 設定，其通常會預設為 `Fill` ，但會將路徑設定為筆劃。

範例中使用的 `Translate` 方塊是20圖元的正方形，而 `advance` 引數則設定為24。 這項差異會導致當線條大約水準或垂直時，方塊之間的間距，但當線條是對角線時，方塊會重迭一點，因為方塊的對角線是28.3 圖元。

範例中的菱形形狀 `Rotate` 也是20圖元寬。 `advance`會設定為20，讓點繼續觸控，因為鑽石會與線條的曲率一起旋轉。

此範例中的矩形圖形 `Morph` 為50圖元寬，而 `advance` 設定為55，在矩形之間形成輕微間距，因為它們在貝茲曲線周圍彎曲。

如果 `advance` 引數小於路徑的大小，則複寫的路徑可能會重迭。 這可能會導致一些有趣的效果。 [ **連結的鏈** ] 頁面會顯示一連串重迭的圓形，看起來類似于 catenary 的獨特圖形中的連結鏈：

[![連結鏈頁面的三重螢幕擷取畫面](effects-images/linkedchain-small.png)](effects-images/linkedchain-large.png#lightbox)

看起來非常接近，您將會看到這些不是真正的圓形。 鏈中的每個連結都有兩個弧形、大小和位置，因此似乎是以連續的連結連接。

統一權數分佈的鏈或纜線會以 catenary 的形式暫停。 以反轉 catenary 形式建立的架構，可從一組與某個外型加權相等的壓力分佈獲益。 Catenary 具有看似簡單的數學描述：

`y = a · cosh(x / a)`

*Cosh* 是雙曲余弦函數。 若 *x* 等於0， *cosh* 為零， *y* 等於 *a* 。 這就是 catenary 的中心。 就像 *余弦* 函數一樣， *cosh* 也就是所謂的，這表示 *cosh ( – x)* 等於 *cosh (x)* ，而增加正值或負引數的 *值會增加* 。 這些值描述形成 catenary 邊的曲線。

尋找 *適當的值以符合* catenary 至電話頁面的尺寸，並不是直接計算。 如果 *w* 和 *h* 是矩形的寬度和高度， *則的最佳值符合下列* 方程式：

`cosh(w / 2 / a) = 1 + h / a`

類別中的下列方法 [`LinkedChainPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs) 會藉由參考等號（和）左右兩個運算式，來併入相等的 `left` `right` 。 針對 *的小值，* 大於，如果的 `left` `right` 大型值小於， *a* `left` 則小於 `right` 。 迴圈會將的 `while` 最佳值縮小： *a*

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

`SKPath`連結的物件會在類別的函式中建立，然後結果 `SKPathEffect` 物件會設定為 `PathEffect` `SKPaint` 儲存為欄位之物件的屬性：

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

處理常式的主要工作 `PaintSurface` 是建立 catenary 本身的路徑。 在決定最佳 *a* 並將它儲存在變數中之後 `optA` ，也需要計算從視窗頂端算出的位移。 然後，它可以累積 `SKPoint` catenary 值的集合，將它轉換成路徑，並使用先前建立的物件來繪製路徑 `SKPaint` ：

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

此程式會定義中使用的路徑， `Create1DPath` 以將其 (0、0) 點置中。 這似乎很合理，因為路徑的 (0、0) 點與裝飾的線條或曲線對齊。 不過，您可以使用非中央的 (0，0) 點來取得一些特殊效果。

[ **傳送帶** ] 頁面會建立類似于 oblong 傳送帶的路徑，其大小為頂端和底部，大小為視窗的維度。 該路徑會以簡單物件（ `SKPaint` 寬度為20圖元）和彩色灰色來繪製，然後以另一個 `SKPaint` 物件與 `SKPathEffect` 參考類似小值區之路徑的物件來繪製：

[![傳送帶頁面的三重螢幕擷取畫面](effects-images/conveyorbelt-small.png)](effects-images/conveyorbelt-large.png#lightbox)

值區路徑的 (0、0) 點是控制碼，因此當 `phase` 引數是以動畫顯示時，值區似乎是在傳送帶周圍旋轉，或許是在底部 scooping 水，然後傾印在最上方。

[`ConveyorBeltPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)類別會使用和方法的覆寫來實行動畫 `OnAppearing` `OnDisappearing` 。 值區的路徑定義于頁面的函式中：

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

值區建立程式碼完成時，會有兩個轉換，可讓值區稍微放大，並將其側邊。 套用這些轉換比調整先前程式碼中的所有座標更容易。

`PaintSurface`處理常式一開始會定義傳送帶本身的路徑。 這只是一對線條，以及一組以20圖元寬的暗灰色線條繪製的半圓圈：

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

繪圖傳送帶的邏輯無法在橫向模式中運作。

值區上的值區間距大約為200圖元。 不過，在200圖元長的情況下，可能不會有一倍的搭搭，這表示由於的 `phase` 引數 `SKPathEffect.Create1DPath` 是以動畫顯示，因此值區將會彈出並不存在。

基於這個理由，程式會先計算名為 `length` 的值，這是傳送帶的長度。 因為傳送帶是由直線和半圓圈所組成，所以這是簡單的計算。 接下來，值區數目的計算方式是除以 `length` 200。 這會舍入為最接近的整數，然後將該數位分割為 `length` 。 結果是值區整數數目的間距。 `phase`引數只是其中一小部分。

## <a name="from-path-to-path-again"></a>再次從路徑到路徑

在 `DrawSurface` **傳送帶** 的處理常式底部，將通話標記為批註， `canvas.DrawPath` 並將其取代為下列程式碼：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

如同先前的範例 `GetFillPath` ，您會看到結果與色彩不同，但色彩除外。 執行之後 `GetFillPath` ， `newPath` 物件會包含值區路徑的多個複本，每個複本都會放置在呼叫時，動畫所在的相同位置。

## <a name="hatching-an-area"></a>影線區域

[`SKPathEffect.Create2DLines`](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix))方法會使用平行線來填滿區域，通常稱為 *影線線* 。 方法具有下列語法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`引數會指定影線線的筆觸寬度。 `matrix`參數是縮放和選擇性旋轉的組合。 縮放比例表示 Skia 用來空間影線線的圖元增量。 線條之間的區隔是調整因數減去 `width` 引數。 如果縮放比例小於或等於 `width` 值，影線線之間將沒有空格，且區域將會顯示為已填滿。 針對水準和垂直縮放比例指定相同的值。

依預設，影線線條是水準的。 如果 `matrix` 參數包含旋轉，則會順時針旋轉影線線。

[ **影線填滿** ] 頁面會示範此路徑效果。 此 [`HatchFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs) 類別會將三個路徑效果定義為欄位，第一個適用于寬度為3圖元的水準影線線條，其縮放比例表示其間距為6圖元。 行之間的分隔是三個圖元。 第二個路徑效果是針對寬度為6圖元的水準影線線，並在 (，因此分隔為18圖元) ，而第三個則是以對角線線線條12圖元寬間距36圖元為單位。

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

請注意矩陣 `Multiply` 方法。 由於水準和垂直縮放比例相同，因此調整和旋轉矩陣相乘的順序並不重要。

此 `PaintSurface` 處理常式會使用三種不同色彩的三種路徑效果，以配合 `fillPaint` 頁面填滿四捨五入的矩形大小。 `Style`會忽略在上設定的屬性 `fillPaint` ; 當 `SKPaint` 物件包含從建立的路徑效果時 `SKPathEffect.Create2DLine` ，就會填滿區域，不論：

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

如果您仔細查看結果，您會看到紅色和藍色影線線條未精確地限定于圓角矩形。  (這顯然是基礎 Skia 程式碼的特性。 ) 如果這不滿意，則會以綠色顯示對角線線線的替代方法：圓角矩形會用來做為裁剪路徑，並在整個頁面上繪製影線線。

`PaintSurface`處理常式結束時，只會對圓角矩形進行筆劃的呼叫，因此您可以看到與紅色和藍色影線線的差異：

[![影線填滿頁面的三重螢幕擷取畫面](effects-images/hatchfill-small.png)](effects-images/hatchfill-large.png#lightbox)

Android 畫面看起來並不像這樣：螢幕擷取畫面的縮放會導致精簡的紅線和細空格合併到看似更寬的紅線和更寬的空間中。

## <a name="filling-with-a-path"></a>填滿路徑

可 [`SKPathEffect.Create2DPath`](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath)) 讓您以水準和垂直方式複寫的路徑來填滿區域：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`縮放因數表示複寫路徑的水準和垂直間距。 但是，您無法使用這個引數來旋轉路徑 `matrix` ; 如果您想要旋轉路徑，請使用所定義的方法來旋轉路徑本身 `Transform` `SKPath` 。

複寫的路徑通常會與畫面的左邊和上邊緣對齊，而不是與所填滿的區域對齊。 您可以覆寫此行為，方法是提供介於0和縮放因數之間的平移因數，以指定左右側的水準和垂直位移。

**路徑磚填滿** 頁面會示範此路徑效果。 用於並排平區的路徑會定義為類別中的欄位 [`PathTileFillPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs) 。 水準和垂直座標範圍從–40到40，這表示此路徑為80圖元正方形：

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

在 `PaintSurface` 處理常式中， `SKPathEffect.Create2DPath` 呼叫會將水準和垂直間距設為64，使80圖元的正方形磚重迭。 幸運的是，此路徑類似拼圖片段，meshing 適當地搭配連續的磚：

[![路徑磚填滿頁面的三重螢幕擷取畫面](effects-images/pathtilefill-small.png)](effects-images/pathtilefill-large.png#lightbox)

從原始螢幕擷取畫面進行調整會導致某些失真，尤其是在 Android 畫面上。

請注意，這些圖格一律會顯示為整個，而且永遠不會被截斷。 在前兩個螢幕擷取畫面上，要填滿的區域是圓角的矩形。 如果您想要將這些磚截斷到特定區域，請使用裁剪路徑。

嘗試將 `Style` 物件的屬性設 `SKPaint` 為 `Stroke` ，您將會看到個別的圖格，而不是填滿。

您也可以使用並排顯示的點陣圖來填滿區域，如 [**SkiaSharp 點陣圖**](../effects/shaders/bitmap-tiling.md)並排顯示的文章所示。

## <a name="rounding-sharp-corners"></a>進位尖角

**舍入的 Heptagon** 計畫以 [**三種方式呈現弧形**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章，使用正切曲線來繪製七邊圖的點曲線。 **另一個舍入的 Heptagon** 頁面會顯示更簡單的方法，使用從方法建立的路徑效果 [`SKPathEffect.CreateCorner`](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single)) ：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

雖然單一引數是命名的 `radius` ，但您必須將它設定為所需的邊角半徑的一半。  (這是基礎 Skia 程式碼的特性。 ) 

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

您可以使用此效果搭配筆劃，或根據物件的屬性填滿 `Style` `SKPaint` 。 它正在執行：

[![另一個圓角 Heptagon 頁面的三重螢幕擷取畫面](effects-images/anotherroundedheptagon-small.png)](effects-images/anotherroundedheptagon-large.png#lightbox)

您會看到這個圓角的 heptagon 與先前的程式相同。 如果您需要更多的說服，邊角半徑實際上是100，而不是在呼叫中指定的 50 `SKPathEffect.CreateCorner` ，您可以取消批註程式中的最後一個語句，並查看在角落重迭的100半徑圓圈。

## <a name="random-jitter"></a>隨機抖動

有時候電腦圖形的完美直線並不是您想要的，而且需要一些隨機性。 在這種情況下，您會想要嘗試 [`SKPathEffect.CreateDiscrete`](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32)) 方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

您可以將此路徑效果用於筆劃或填滿。 線條會分隔為連接的區段（由指定的大約長度 `segLength` ），並以不同的方向延伸。 原始程式列的偏差範圍是由指定 `deviation` 。

最後一個引數是用來產生效果所用之虛擬隨機順序的種子。 不同種子的抖動效果看起來會有些不同。 引數的預設值為零，表示每當您執行程式時，效果都會相同。 如果您想要在每次重繪螢幕時使用不同的抖動，可以將種子設定為 `Millisecond` 值的屬性 `DataTime.Now` (例如) 。

[ **抖動實驗** ] 頁面可讓您使用不同的值來試驗矩形的筆劃：

[![JitterExperiment 頁面的三重螢幕擷取畫面](effects-images/jitterexperiment-small.png)](effects-images/jitterexperiment-large.png#lightbox)

程式很簡單。 [**JitterExperimentPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)檔案具現化兩個 `Slider` 元素和 `SKCanvasView` ：

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

`PaintSurface`每當值變更時，就會呼叫 [**JitterExperimentPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)程式碼後端檔案中的處理常式 `Slider` 。 它會 `SKPathEffect.CreateDiscrete` 使用兩個 `Slider` 值來呼叫，並使用該值來繪製矩形：

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

您也可以將此效果用於填滿，在這種情況下，填滿區域的外框會受限於這些隨機偏差。 [ **抖動文字** ] 頁面會示範如何使用此路徑效果來顯示文字。 類別處理常式中的大部分 `PaintSurface` 程式碼 [`JitterTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs) 都是專門用來調整文字的大小和中心：

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

在這裡以橫向模式執行：

[![JitterText 頁面的三重螢幕擷取畫面](effects-images/jittertext-small.png)](effects-images/jittertext-large.png#lightbox)

## <a name="path-outlining"></a>路徑大綱

您已經看過兩個方法的範例 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath*) `SKPaint` ，它有兩個版本：

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

只有前兩個引數是必要的。 方法會存取引數所參考的路徑 `src` 、根據物件中的筆觸屬性修改路徑資料 `SKPaint` (包括 `PathEffect`) 的屬性，然後將結果寫入至 `dst` 路徑。 `resScale`參數允許降低精確度來建立較小的目的地路徑，而 `cullRect` 引數可以消除矩形外的輪廓。

這種方法的其中一個基本用途，完全不牽涉到路徑效果：如果 `SKPaint` 物件的 `Style` 屬性設定為 `SKPaintStyle.Stroke` ，而且沒有 *not* `PathEffect` 設定，則會 `GetFillPath` 建立一個路徑來表示來源路徑的 *外框* ，就如同繪製屬性已將其繪製。

例如，如果 `src` 路徑是 radius 500 的簡單圓圈，而且 `SKPaint` 物件指定筆劃寬度100，則 `dst` 路徑會變成兩個同心圓，一個半徑為450，另一個半徑為550。 `GetFillPath`因為填滿此 `dst` 路徑與為路徑進行筆劃的方式相同，所以會呼叫此方法 `src` 。 但是您也可以 `dst` 對路徑進行筆劃，以查看路徑大綱。

**點擊以概述路徑** 會示範這點。 `SKCanvasView`和 `TapGestureRecognizer` 會在 [**TapToOutlineThePathPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)檔案中具現化。 [**TapToOutlineThePathPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)程式碼後端檔案會將三個 `SKPaint` 物件定義為欄位，兩個用於筆劃寬度為100和20的筆劃，第三個則用於填滿：

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

如果未點擊螢幕， `PaintSurface` 處理常式會使用 `blueFill` 和 `redThickStroke` 繪製物件來呈現迴圈路徑：

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

圓形會填滿並以您預期的方式繪製：

[![一般點擊以概述路徑頁面的三重螢幕擷取畫面](effects-images/taptooutlinethepathnormal-small.png)](effects-images/taptooutlinethepathnormal-large.png#lightbox)

當您按一下畫面時， `outlineThePath` 會設定為 `true` ，而 `PaintSurface` 處理常式會建立新 `SKPath` 的物件，並在 `GetFillPath` 繪製物件的呼叫中使用該物件做為目的路徑 `redThickStroke` 。 接著會填滿該目的地路徑並將其加 `redThinStroke` 上筆劃，以產生下列結果：

[![外框的三重螢幕擷取畫面，顯示路徑頁面的輪廓](effects-images/taptooutlinethepathoutlined-small.png)](effects-images/taptooutlinethepathoutlined-large.png#lightbox)

這兩個紅色圓圈清楚指出原始的圓形路徑已轉換成兩個圓形輪廓。

在開發用於方法的路徑時，這個方法會非常有用 `SKPathEffect.Create1DPath` 。 在這些方法中指定的路徑，一律會在複寫路徑時填滿。 如果您不想要填滿整個路徑，您必須仔細定義大綱。

例如，在 **連結鏈** 範例中，連結是以四個弧形的數列定義，每一組都是以兩個半徑為基礎，以勾勒出要填滿的路徑區域。 您可以取代類別中的程式碼 `LinkedChainPage` ，以稍微不同的方式來進行。

首先，您會想要重新定義 `linkRadius` 常數：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`現在只有兩個以該單一半徑為依據的弧形，具有所需的起始角度和掃除角度：

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

`outlinePath` `linkPath` 當物件以中指定的屬性進行筆劃時，該物件即為大綱的收件者 `strokePaint` 。

使用這項技術的另一個範例是在方法中使用的路徑下一個。

## <a name="combining-path-effects"></a>合併路徑效果

的最後兩個靜態建立方法 `SKPathEffect` 是 [`SKPathEffect.CreateSum`](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) 和 [`SKPathEffect.CreateCompose`](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)) ：

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

這兩種方法都會結合兩個路徑效果，以建立複合路徑效果。 `CreateSum`方法會建立類似于個別套用的兩個路徑效果的路徑效果， `CreateCompose` 並將一個路徑效果 (`inner`) ，然後將套用 `outer` 至該路徑。

您已經看過如何將 `GetFillPath` `SKPaint` 一個路徑轉換成另一個路徑， `SKPaint` (包括 `PathEffect`) ，因此不應該 *太* 神秘 `SKPaint` 物件如何使用或方法中所指定的兩個路徑效果來執行該作業兩次 `CreateSum` `CreateCompose` 。

的其中一項明顯用途 `CreateSum` 是定義一個 `SKPaint` 物件，該物件會以一個路徑效果填滿路徑，並以另一個路徑效果來筆觸路徑。 這會示範在 [ **框架中的貓** ] 範例中，它會在畫面格內顯示具有 scalloped 邊緣的貓陣列：

[![框架頁面中貓的三重螢幕擷取畫面](effects-images/catsinframe-small.png)](effects-images/catsinframe-large.png#lightbox)

[`CatsInFramePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)類別一開始會定義數個欄位。 您可以 [`PathDataCatPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs) 從 [**SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md) 文章中辨識類別的第一個欄位。 第二個路徑是根據框架 scallop 模式的線條和弧線：

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

`catPath` `SKPathEffect.Create2DPath` 如果 `SKPaint` 物件 `Style` 屬性設定為，就可以在方法中使用 `Stroke` 。 但是，如果 `catPath` 直接在此程式中使用，則會填滿貓的整個標頭，而且須線甚至看不見。  (試試看！ ) 需要取得該路徑的外框，並在方法中使用該大綱 `SKPathEffect.Create2DPath` 。

此函式會執行此作業。 它會先將兩個轉換套用至， `catPath` 以將 (0、0) 指向中央，並以大小向下調整。 `GetFillPath` 取得中的輪廓的所有外框 `outlinedCatPath` ，並在呼叫中使用該物件 `SKPathEffect.Create2DPath` 。 值中的縮放比例 `SKMatrix` 會稍微大於貓的水準和垂直大小，以提供圖格之間的小小緩衝區，而轉譯因數則衍生有點廣為人知且實證，以便在框架的左上角顯示完整的貓：

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

然後，此函式會呼叫 `SKPathEffect.Create1DPath` scalloped 框架。 請注意，路徑的寬度是100圖元，但前進是75圖元，如此一來，複寫的路徑就會在框架周圍重迭。 函式的最後一個語句會呼叫 `SKPathEffect.CreateSum` 以合併這兩個路徑效果，並將結果設定為 `SKPaint` 物件。

這些工作都可讓 `PaintSurface` 處理常式相當簡單。 它只需要定義一個矩形，然後使用下列內容來繪製它 `framePaint` ：

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

路徑效果背後的演算法一律會造成用來顯示筆劃或填滿的整個路徑，這可能會導致某些視覺效果出現在矩形之外。 `ClipRect`呼叫之前的呼叫可 `DrawRect` 讓視覺效果大幅簡潔。  (不需要裁剪就試試看！ ) 

通常會使用將 `SKPathEffect.CreateCompose` 某些抖動新增至另一個路徑效果。 您當然可以自行實驗，但以下是一個有點不同的範例：

**虛線影線線條** 會以虛線填滿橢圓形。 類別中的大部分工作 [`DashedHatchLinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs) 都是直接在欄位定義中執行。 這些欄位會定義虛線效果和影線效果。 它們定義如下 `static` ：它們接著會在定義的呼叫中參考 `SKPathEffect.CreateCompose` `SKPaint` ：

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

`PaintSurface`處理常式只需要包含標準的額外負荷，再加上一個呼叫 `DrawOval` ：

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

如您所探索到的，影線線不會精確地限制在區域內部，而在此範例中，它們一律會從左邊開始，且具有全虛線：

[![虛線影線線頁的三重螢幕擷取畫面](effects-images/dashedhatchlines-small.png)](effects-images/dashedhatchlines-large.png#lightbox)

現在您已看過從簡單的點和連字號到奇怪組合的路徑效果，請使用您的想像並查看您可以建立的內容。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)