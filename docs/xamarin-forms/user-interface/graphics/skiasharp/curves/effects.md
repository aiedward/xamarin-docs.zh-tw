---
title: SkiaSharp 路徑效果
description: 這篇文章說明可供路徑，用於繪製，並填入，以及範例程式碼會示範這個 SkiaSharp 各種路徑效果。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2017
ms.openlocfilehash: 8354539288613353dcb7a792ace335daa5dc8f27
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2018
ms.locfileid: "52171140"
---
# <a name="path-effects-in-skiasharp"></a>SkiaSharp 路徑效果

_探索各種路徑效果可讓用於繪製，並填入的路徑_

A*路徑效果*的執行個體[ `SKPathEffect` ](xref:SkiaSharp.SKPathEffect)會透過其中一種類別所定義的八個靜態建立方法的類別。 `SKPathEffect`物件會設為[ `PathEffect` ](xref:SkiaSharp.SKPaint.PathEffect)屬性[ `SKPaint` ](xref:SkiaSharp.SKPaint)有趣的效果，例如，有許多物件繪製含有小型的複寫路徑的線條:

![](effects-images/patheffectsample.png "連結的鏈結範例")

路徑效果可讓您：

- 繪製線條與點和虛線
- 使用任何填滿之路徑的線條描邊
- 填滿的區域與規劃線條
- 填滿的區域，以並排顯示的路徑
- 進行明確的圓角邊角
- 新增隨機選取 「 抖動"線條和曲線

此外，您可以結合兩個或多個路徑的效果。

這篇文章也會示範如何使用[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*)方法`SKPaint`套用的屬性，將一個路徑轉換成另一個路徑`SKPaint`，其中包括`StrokeWidth`並`PathEffect`。 這會導致一些有趣的技術，例如取得另一個路徑的外框的路徑。 `GetFillPath` 也很有用的相關路徑效果。

## <a name="dots-and-dashes"></a>點和虛線

善用[ `PathEffect.CreateDash` ](xref:SkiaSharp.SKPathEffect.CreateDash(System.Single[],System.Single))文章中所述的方法[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)。 方法的第一個引數是包含偶數數目的兩個或多個值，交替的虛線長度，以及連字號之間的間距的長度的陣列：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

這些值是*不*相對於筆觸粗細。 例如，如果筆觸寬度為 10，而您會想一條線組成方形的虛線和正方形的間距，設定`intervals`陣列 {10，10}。 `phase`引數表示虛線模式內的行開始的位置。 在此範例中，如果您想的一行開始的方形的差距，設定`phase`為 10。

連字號結尾會受到`StrokeCap`屬性`SKPaint`。 各種筆劃寬度是很常見，無法將此屬性設定為`SKStrokeCap.Round`要捨入的連字號結尾。 在本例中的值`intervals`陣列，進行*不*包括額外長度所產生的捨入。 這表示將會以循環的點，需要指定寬度為零。 筆觸寬度為 10，若要循環的點與相同的直徑的點之間的間隔建立一條線，請使用`intervals`陣列 {0，20}。

**動畫點線文字**頁面大致**外框文字**一文所述的頁面[**整合文字與圖形**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)中它會顯示藉由設定說明文字字元`Style`的屬性`SKPaint`物件到`SKPaintStyle.Stroke`。 颾魤 ㄛ**動畫點線文字**會使用`SKPathEffect.CreateDash`提供給這外框虛線的外觀，並計劃也以動畫顯示`phase`引數`SKPathEffect.CreateDash`方法，讓似乎到處移動文字的點字元。 以下是在橫向模式中的頁面：

[![](effects-images/animateddottedtext-small.png "以動畫顯示點線的文字頁面的三個螢幕擷取畫面")](effects-images/animateddottedtext-large.png#lightbox "動畫點線的文字頁面的三個螢幕擷取畫面")

[ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別一開始會定義一些常數，且也會覆寫`OnAppearing`和`OnDisappearing`動畫的方法：

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

`PaintSurface`處理常式一開始先建立`SKPaint`來顯示文字的物件。 `TextSize`根據螢幕的寬度調整屬性：

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

方法的結尾`SKPathEffect.CreateDash`方法使用稱為`dashArray`定義為欄位，以及動畫`phase`值。 `SKPathEffect`執行個體設定為`PathEffect`屬性`SKPaint`來顯示文字的物件。

或者，您可以設定`SKPathEffect`物件至`SKPaint`之前測量的文字，並將它置中頁面上的物件。 在此情況下，不過，動畫的點和虛線會造成一些不同大小的轉譯的文字，而文字傾向於有點震動。 （試試看 ！）

您也會發現為動畫的點周圍的圓圈文字字元，已關閉的每條曲線中的某一點點似乎顯示流入和流出存在。 這是定義字元外框路徑的開始和結束位置。 如果路徑長度不是整數類資料的多個虛線圖樣 （在此情況下 20 像素為單位） 的長度，該模式的唯一部分可以容納在路徑結尾。

可調整的長度，虛線圖樣，使其符合路徑的長度，但需要決定長度的路徑，也就是本文所述的技巧[**路徑資訊與列舉**](information.md).

**點 / Dash Morph**程式繪製虛線圖樣本身，以便連字號似乎分成數個點，再結合表單連字號：

[![](effects-images/dotdashmorph-small.png "三個點虛線 Morph 頁面螢幕擷取畫面")](effects-images/dotdashmorph-large.png#lightbox "點虛線 Morph 頁面的三個螢幕擷取畫面")

[ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別會覆寫`OnAppearing`並`OnDisappearing`方法就像前一個程式，但此類別會定義`SKPaint`做為欄位的物件：

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

`PaintSurface`處理常式建立橢圓形路徑的頁面上，大小為基礎，並執行很長的一段設定的程式碼`dashArray`和`phase`變數。 為動畫的變數`t`範圍從 0 到 1，`if`區塊分解成四個季度，和每個這些季中該時間`tsub`也範圍從 0 到 1。 在結尾處，程式會建立`SKPathEffect`並將它設定為`SKPaint`繪圖的物件。

## <a name="from-path-to-path"></a>從路徑的路徑

[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,System.Single))方法`SKPaint`取決於設定中的另一個會變成一個路徑`SKPaint`物件。 若要查看其運作方式，來取代`canvas.DrawPath`前一個程式，以下列程式碼中呼叫：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在這個新的程式碼`GetFillPath`呼叫轉換`ellipsePath`（不只一個橢圓） 到`newPath`，接著會顯示與`newPaint`。 `newPaint`物件會建立使用所有的預設屬性設定不同之處在於`Style`屬性根據設定的布林值會傳回值`GetFillPath`。

視覺效果都是相同的色彩設定中除了`ellipsePaint`而非`newPaint`。 而不是簡單中定義的橢圓形`ellipsePath`，`newPath`包含多個路徑分佈定義的點和虛線序列。 這是套用的各種屬性的結果`ellipsePaint`(具體而言， `StrokeWidth`， `StrokeCap`，以及`PathEffect`) 來`ellipsePath`，並將結果路徑放`newPath`。 `GetFillPath`方法會傳回布林值，指出目的地路徑是否填滿; 在此範例中，傳回的值是`true`來填滿的路徑。

請嘗試變更`Style`中設定`newPaint`到`SKPaintStyle.Stroke`，您會看到有一個像素寬度線條所述的個別路徑輪廓。

## <a name="stroking-with-a-path"></a>繪製路徑

[ `SKPathEffect.Create1DPath` ](xref:SkiaSharp.SKPathEffect.Create1DPath(SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPath1DPathEffectStyle))方法在概念上類似`SKPathEffect.CreateDash`不同之處在於您指定的路徑，而不是虛線和間隙的模式。 此路徑筆觸線條或曲線複寫許多次。

其語法為：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

一般而言，您將傳遞至的路徑`Create1DPath`會太大，而且點 （0，0） 為中心。 `advance`參數表示路徑的中心之間的距離，因為複寫列上的路徑。 您通常會設定為大約路徑寬度的這個引數。 `phase`引數所扮演的角色相同這裡因為它會以`CreateDash`方法。

[ `SKPath1DPathEffectStyle` ](xref:SkiaSharp.SKPath1DPathEffectStyle)有三個成員：

- `Translate`
- `Rotate`
- `Morph`

`Translate`成員會導致保留在相同的方向，因為它複寫沿著線條或曲線的路徑。 針對`Rotate`，根據曲線的正切函數旋轉的路徑。 路徑中有水平線其正常方向。 `Morph` 類似於`Rotate`不同之處在於路徑本身也弧形以符合正在將描邊的一行的曲率。

**1 D 路徑效果**頁面會示範這三個選項。 [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)檔案會定義包含三個項目對應至三個成員的列舉型別選擇器：

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

[ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)程式碼後置檔案會定義三個`SKPathEffect`做為欄位的物件。 這些所有使用建立`SKPathEffect.Create1DPath`具有`SKPath`使用所建立的物件`SKPath.ParseSvgPathData`。 第一個是簡單的方塊，第二個是菱形圖案，，第三個是一個矩形。 這些用來示範三種效果樣式：

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

`PaintSurface`處理常式會建立迴圈循環，並存取來判斷哪一個選擇器的貝茲曲線`PathEffect`應該用來繪製它。 三個選項 — `Translate`， `Rotate`，和`Morph`— 從左到右顯示：

[![](effects-images/1dpatheffect-small.png "1d 路徑效果頁面的三個螢幕擷取畫面")](effects-images/1dpatheffect-large.png#lightbox "1d 路徑效果頁面的三個螢幕擷取畫面")

中指定的路徑`SKPathEffect.Create1DPath`方法一律會填滿。 中指定的路徑`DrawPath`如果方法一律會將描邊`SKPaint`物件都有其`PathEffect`屬性設定為 1d 路徑效果。 請注意，`pathPaint`物件沒有`Style`設定，而這通常會預設為`Fill`，但不論描邊的路徑。

使用中的方塊`Translate`範例是 20 像素的正方形，和`advance`引數設定為 24。 這項差異會造成有差距的一行大致上是水平或垂直的但方塊有點重疊時線條是對角線，因為方塊的對角線 28.3 像素為單位的方塊。

中的菱形圖案`Rotate`範例也是 20 像素寬。 `advance`設定為 20，以便在持續點接觸，菱形旋轉以及線條的曲率。

中的矩形圖案`Morph`範例中是 50 個像素與寬`advance`55 設定它們周圍的貝茲曲線的插腳做矩形之間有小型的間隙。

如果`advance`引數是否小於一個大小的路徑，則複寫的路徑可以重疊。 這會導致一些有趣效果。 **連結的鏈結**頁面會顯示一系列的重疊的圓似乎類似於連結的鏈結，在特殊的圖案 catenary 的停止回應：

[![](effects-images/linkedchain-small.png "連結的鏈結頁面的三個螢幕擷取畫面")](effects-images/linkedchain-large.png#lightbox "連結的鏈結頁面的三個螢幕擷取畫面")

看起來非常接近，您會看到這些並不是實際的圓形。 鏈結中的每個連結是大小和位置，讓它們似乎具有相鄰的連結所連接的兩個弧形。

鏈結或纜線，統一的權數分佈的停止回應的 catenary 形式。 內建的反向 catenary 形式 arch 受惠於壓力 arch 的加權平均分散。 Catenary 有看似簡單的數學描述：

y = ·cosh(x / a)

*Cosh*是雙曲線餘弦函數。 針對*x*等於 0， *cosh*為零並*y*等於。 這是 catenary 的中心。 像是*餘弦函數*函式*cosh*要*甚至*，這表示*cosh(–x)* 等於*cosh(x)*，並增加正數或負數的引數的值增加。 這些值描述構成 catenary 側邊的曲線。

尋找適當的值成維度的手機上的頁面 catenary 不是直接的計算。 如果*w*並*h*是最佳的值是矩形的高度與寬度滿足下列方程式：

cosh (w/2/a) = 1 + h / a

中的下列方法[ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs)類別會併入該等號比較，藉由參考兩個運算式的左側和右側為等號`left`和`right`。 值較小，`left`大於`right`; 對於大型值，`left`是小於`right`。 `while`迴圈中的最佳值縮減:

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

`SKPath`物件類別的建構函式和結果中建立的連結`SKPathEffect`物件會設為`PathEffect`屬性`SKPaint`儲存為欄位的物件：

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

主要工作`PaintSurface`處理常式是建立 catenary 本身的路徑。 如果之後決定最佳並將它儲存在`optA`變數時，它也需要計算從視窗頂端的位移。 然後，它可以累積一堆`SKPoint`catenary，值為一個路徑，此功能，以及繪製的路徑，以先前建立`SKPaint`物件：

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

此程式定義中使用的路徑`Create1DPath`有其 （0，0） 的中心點。 這似乎很適當因為 （0，0） 的路徑的點會與線條或曲線，就裝飾對齊。 不過，您可以使用非中心 （0，0） 的一些特殊效果點。

**輸送帶**頁面會建立類似尾巴輸送帶，使用曲線的頂端和底部，會調整成視窗大小的路徑。 該路徑繪製簡單`SKPaint`物件 20 像素寬和彩色的灰色顯示，並接著圖案與另一個`SKPaint`物件`SKPathEffect`參考路徑，類似於小小的值區的物件：

[![](effects-images/conveyorbelt-small.png "輸送帶頁面的三個螢幕擷取畫面")](effects-images/conveyorbelt-large.png#lightbox "輸送帶頁面的三個螢幕擷取畫面")

（0，0） 的貯體路徑的點是控制代碼，因此當`phase`引數以動畫顯示，不外乎的輸送帶，或許 scooping 向上底部的上限和傾印出頂端似乎貯體。

[ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)類別會實作覆寫的動畫`OnAppearing`和`OnDisappearing`方法。 貯體的路徑被定義在網頁的建構函式：

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

貯體建立程式碼完成，使得稍微大一點的貯體，並可以將它的兩個轉換。 套用這些轉換是容易調整先前的程式碼中的所有座標。

`PaintSurface`處理常式一開始會定義輸送帶本身的路徑。 這是只需一組線條，以及一組的分號繪製有 20 像素寬的深灰色線條的圓圈：

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

繪製輸送帶的邏輯在橫向模式中無法運作。

貯體的間距應約 200 像素上輸送帶分開。 不過，輸送帶可能不是 200 像素長，這表示它作為倍數`phase`引數`SKPathEffect.Create1DPath`是動畫，值區會顯示傳入和傳出是否存在。

基於這個理由，程式會先計算名為的值`length`也就是輸送帶的長度。 輸送帶所組成的直線，線條和分號的圓形，因為這是簡單的計算。 接下來，計算貯體數目除以`length`的 200。 這會四捨五入為最接近的整數，和數字接著會分割成`length`。 結果為整數的值區的間距。 `phase`引數是只需一小部分的。

## <a name="from-path-to-path-again"></a>從一次路徑的路徑

在底部`DrawSurface`中的處理常式**輸送帶**，標記為註解`canvas.DrawPath`呼叫，並將它取代為下列程式碼：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

如同上述範例的`GetFillPath`，您會看到結果會相同，但是色彩。 在執行後`GetFillPath`，則`newPath`物件包含的貯體路徑的多個複本，每個位於相同的點，動畫就會將它們放置在呼叫的時間。

## <a name="hatching-an-area"></a>影線區域

[ `SKPathEffect.Create2DLines` ](xref:SkiaSharp.SKPathEffect.Create2DLine(System.Single,SkiaSharp.SKMatrix))方法會填滿的平行線，通常稱為區域*影線行*。 方法具有下列語法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`引數會指定筆劃的規劃線條寬度。 `matrix`參數是調整和選擇性的循環的組合。 縮放比例會指出空間的規劃線條用以 Skia 像素遞增值。 線條之間的分隔是減去的縮放比例`width`引數。 如果縮放比例小於或等於`width`值之間的規劃線條，會有任何空間和區域會顯示要填滿。 指定水平和垂直縮放的相同值。

根據預設，規劃線條會水平的。 如果`matrix`參數包含循環，規劃線條會依順時針方向旋轉。

**規劃圖樣填滿**頁面會示範這個路徑的效果。 [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs)類別做為欄位定義三種路徑效果，它們是水平的規劃線條寬度的縮放係數指出 3 個像素為單位的第一個空格分開的 6 個像素。 線條之間的分隔，因此是三個像素。 第二個路徑效果是垂直的規劃線條的六個像素寬度間距 24 個像素的位置 （因此有所區隔為 18 個像素），和第三個是斜對角影線行 12 個像素寬等間距 36 佹萺慒分開。

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

請注意矩陣`Multiply`方法。 水平和垂直縮放比例都相同，因為乘上縮放和旋轉的矩陣的順序不重要。

`PaintSurface`處理常式以三個不同的色彩結合使用這些三個路徑效果`fillPaint`來填滿圓角的矩形調整成頁面大小。 `Style`上設定屬性`fillPaint`會被忽略; 當`SKPaint`物件包含從建立路徑效果`SKPathEffect.Create2DLine`，無論填滿的區域：

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

如果您仔細看一下結果，您會看到紅色和藍色的規劃線條不精確地限制圓角矩形。 （這是很顯然是基礎 Skia 程式碼的特性）。如果這是令人滿意，斜對角影線格線，以綠色顯示的替代方法： 圓角的矩形當做裁剪路徑，並規劃線條會繪製整個頁面上。

`PaintSurface`處理常式的呼叫，以便您可以看到紅色和藍色的規劃線條與差異，只是繪製圓角的矩形，結束：

[![](effects-images/hatchfill-small.png "規劃圖樣填滿頁面的三個螢幕擷取畫面")](effects-images/hatchfill-large.png#lightbox "規劃圖樣填滿頁面的三個螢幕擷取畫面")

Android 畫面不真的看起來像這樣： 的螢幕擷取畫面縮放比例導致精簡的紅線和細的空間，將彙總成更寬看似紅線和更多的空間。

## <a name="filling-with-a-path"></a>路徑填滿

[ `SKPathEffect.Create2DPath` ](xref:SkiaSharp.SKPathEffect.Create2DPath(SkiaSharp.SKMatrix,SkiaSharp.SKPath))實際上可讓您以水平和垂直，複寫路徑填滿的區域並排在區域：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`縮放比例表示複寫路徑的水平和垂直間距。 您不能旋轉使用此路徑，但是`matrix`引數; 如果您想要旋轉的路徑替換路徑本身使用`Transform`所定義的方法`SKPath`。

複寫的路徑通常與畫面，而不是要填滿的區域左邊和頂端邊緣對齊。 若要覆寫這個行為，可以提供翻譯的因素，介於 0 和縮放比例，以指定從左上側邊的水平和垂直位移。

**路徑並排顯示填滿**頁面會示範這個路徑的效果。 並排在區域所使用的路徑定義中的欄位[ `PathTileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs)類別。 水平和垂直座標範圍從 –40 到 40，這表示這個路徑是 80 像素的正方形：

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

在 `PaintSurface`處理常式，`SKPathEffect.Create2DPath`呼叫設定的水平和垂直間距設為 64，讓 80 個像素正方形圖格會重疊。 幸運的是，路徑類似拼圖片段，網格妥善使用圖格相鄰 （adjoining）：

[![](effects-images/pathtilefill-small.png "路徑並排顯示填滿頁面的三個螢幕擷取畫面")](effects-images/pathtilefill-large.png#lightbox "路徑並排顯示填滿頁面的三個螢幕擷取畫面")

從原始的螢幕擷取畫面縮放比例會導致某些扭曲，特別是 Android 的畫面。

請注意這些圖格一律會顯示整個，而且永遠不會被截斷。 上的前兩個螢幕擷取畫面中，並不被填滿的區域是圓角的矩形更明顯。 如果您想要截斷一個磚，以特定的區域，請使用 裁剪路徑。

請嘗試設定`Style`屬性`SKPaint`物件`Stroke`，您會看到個別的圖格所述，而不是填滿。

它也可填滿的區域與並排顯示的點陣圖，文件中所示[ **SkiaSharp 點陣圖並排**](../effects/shaders/bitmap-tiling.md)。

## <a name="rounding-sharp-corners"></a>明確設為圓

**捨入 Heptagon**程式中呈現[**繪製弧形的三種方式**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文件用的正切函數的弧線來曲線的七個側 」 圖形的點。 **另一個的捨入 Heptagon**頁面會顯示更簡便的方法會建立從路徑效果[ `SKPathEffect.CreateCorner` ](xref:SkiaSharp.SKPathEffect.CreateCorner(System.Single))方法：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

雖然單一引數名為`radius`，您必須將它設定為一半的所需的圓角半徑。 （這是基礎 Skia 程式碼的特性）。

以下是`PaintSurface`中的處理常式[ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs)類別：

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

您可以使用這種效果繪製或根據填滿`Style`屬性`SKPaint`物件。 以下執行：

[![](effects-images/anotherroundedheptagon-small.png "另一個的捨入 Heptagon 頁面的三個螢幕擷取畫面")](effects-images/anotherroundedheptagon-large.png#lightbox "的另一個的捨入 Heptagon 頁面的三個螢幕擷取畫面")

您會看到這個圓角的 heptagon 等同於先前的程式。 如果您需要更多說服圓角半徑可說是 100 而非指定 50`SKPathEffect.CreateCorner`呼叫時，您可以取消註解最終的陳述式中的程式並查看 100 radius 圓形重疊，邊角。

## <a name="random-jitter"></a>隨機抖動

有時候真是完美的直線，線條的電腦圖形中並不很想，而且想要使用一些隨機性。 在此情況下，您會想要嘗試[ `SKPathEffect.CreateDiscrete` ](xref:SkiaSharp.SKPathEffect.CreateDiscrete(System.Single,System.Single,System.UInt32))方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

您可以使用此路徑效果繪製或填滿。 行分成連接區段-其中的約略長度由`segLength`— 和擴充在不同的方向。 從原始的列差的範圍由`deviation`。

最後一個引數是用來產生用於影響的虛擬隨機序列的種子。 抖動效果看起來稍有不同的不同的種子。 引數的預設值是零，表示結果是相同每當您執行程式。 如果您想不同抖動，畫面會重新繪製時，您可以設定種子`Millisecond`屬性`DataTime.Now`（舉例來說） 值。


**抖動實驗**頁面可讓您試驗不同的值，在繪製矩形：

[![](effects-images/jitterexperiment-small.png "三倍抖動實驗 頁面上的螢幕擷取畫面")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

程式很簡單。 [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)檔案具現化兩個`Slider`項目和`SKCanvasView`:

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

`PaintSurface`中的處理常式[ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)程式碼後置檔案便稱為每當`Slider`值變更。 它會呼叫`SKPathEffect.CreateDiscrete`使用兩個`Slider`值，並使用它來繪製矩形：

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

您可以使用這種效果，以便填滿，在此情況下的區域分布區域的外框限於這些隨機的偏差。 **抖動文字**頁面將示範如何使用此路徑效果來顯示文字。 中的程式碼的大部分`PaintSurface`處理常式[ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs)類別用來調整，並將文字置中：

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

這裡執行以橫向模式：

[![](effects-images/jittertext-small.png "三倍抖動文字頁面的螢幕擷取畫面")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>路徑大綱

您已經看到兩個小例子[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath*)方法`SKPaint`，存在於兩個版本：

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale = 1)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale = 1)
```

只需要前兩個引數。 方法會存取所參考的路徑`src`引數，修改路徑資料中的筆劃屬性為基礎`SKPaint`物件 (包括`PathEffect`屬性)，然後將結果載入`dst`路徑。 `resScale`參數可讓您可建立較小的目的地路徑的有效位數減少和`cullRect`引數可以排除外部矩形的輪廓。

一個基本的用法，此方法並不完全包含路徑的影響： 如果`SKPaint`物件都有其`Style`屬性設定為`SKPaintStyle.Stroke`，並*不*有其`PathEffect`設定，然後`GetFillPath`建立表示路徑*大綱*的來源路徑，如果有 [小畫家] 屬性將描邊。

例如，如果`src`路徑是簡單的圓形的半徑 500，而`SKPaint`物件會指定筆劃寬度為 100，則`dst`路徑會變成兩個的同心圓，其中一個半徑為 450，而另一個半徑為 550。 方法會呼叫`GetFillPath`因為填入此`dst`路徑等同於繪製`src`路徑。 但您也可以繪製`dst`查看路徑外框輪廓的路徑。

**點選以外框路徑**示範這項功能。 `SKCanvasView`並`TapGestureRecognizer`會在具現化[ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)檔案。 [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)程式碼後置檔案會定義三個`SKPaint`物件做為欄位，用於繪製具有兩個描邊寬度為 100 和 20 和填滿的第三個：

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

如果畫面未被點選，`PaintSurface`處理常式會使用`blueFill`和`redThickStroke`繪製到轉譯的循環路徑的物件：

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

圓形是填滿，而且如您所預期的圖案：

[![](effects-images/taptooutlinethepathnormal-small.png "一般點選至外框路徑 頁面的三個螢幕擷取畫面")](effects-images/taptooutlinethepathnormal-large.png#lightbox "正常的點選來外框路徑頁面三個螢幕擷取畫面")

當您點選 螢幕`outlineThePath`設為`true`，而`PaintSurface`處理常式會建立全新`SKPath`物件，並會使用該值作為呼叫中的目的地路徑`GetFillPath`上`redThickStroke`繪製物件。 然後填入並圖案與該目的地路徑`redThinStroke`，產生下列：

[![](effects-images/taptooutlinethepathoutlined-small.png "外框的 [點選以外框路徑] 頁面的三個螢幕擷取畫面")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "的外框的 [點選以外框路徑] 頁面的三個螢幕擷取畫面")

兩個紅色圓形清楚指出，原始的循環路徑轉換成兩個循環的輪廓。

這個方法可以是非常有幫助開發用於路徑`SKPathEffect.Create1DPath`方法。 複寫路徑時，永遠是填滿您在這些方法中指定的路徑。 如果您不想要填入的完整路徑，您必須仔細定義外框輪廓。

例如，在**連結的鏈結**範例中，以一系列的四個弧形，概述在路徑填滿區域的兩個半徑以每一對定義的連結。 您可在程式碼取代`LinkedChainPage`要稍有不同的類別。

首先，您會想要重新定義`linkRadius`常數：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`現在根據該單一 radius，與需要的只是兩個弧形開始角度和掃掠角度是：

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

`outlinePath`物件則是外框的收件者`linkPath`當它經過繪製，在指定的屬性`strokePaint`。

使用這項技術的另一個範例在隨後的方法中使用的路徑的下一步。

## <a name="combining-path-effects"></a>合併路徑效果

兩個的最後一個靜態建立方法`SKPathEffect`都[ `SKPathEffect.CreateSum` ](xref:SkiaSharp.SKPathEffect.CreateSum(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect))並[ `SKPathEffect.CreateCompose` ](xref:SkiaSharp.SKPathEffect.CreateCompose(SkiaSharp.SKPathEffect,SkiaSharp.SKPathEffect)):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

這兩種方法結合兩個路徑效果，以建立複合路徑效果。 `CreateSum`方法會建立兩個路徑效果分別套用類似的路徑效果雖然`CreateCompose`適用於其中一個路徑的影響 ( `inner`)，然後套用`outer`的。

您已了解如何`GetFillPath`方法`SKPaint`可以將一個路徑轉換成另一個路徑，根據`SKPaint`屬性 (包括`PathEffect`) 讓它不應*太*神秘方式`SKPaint`物件可以執行該作業兩次使用中指定這兩個路徑效果`CreateSum`或`CreateCompose`方法。

明顯的用途之一`CreateSum`是定義`SKPaint`物件，一個路徑的效果，以填滿的路徑和 strokes 另一個路徑效果的路徑。 這示範於**貓 」、 「 在框架中的**範例，它會顯示與扇形邊緣的貓 」、 「 在範圍內的陣列：

[![](effects-images/catsinframe-small.png "三重的螢幕擷取畫面的畫面格的貓 」、 「 頁面")](effects-images/catsinframe-large.png#lightbox "的畫面格的貓 」、 「 頁面的三個螢幕擷取畫面")

[ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)類別一開始會定義數個欄位。 您可能會發現從第一個欄位[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)類別[ **SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)文章。 第二個路徑根據一條線和弧形的框架 scallop 模式而定：

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

`catPath`可供`SKPathEffect.Create2DPath`方法如果`SKPaint`物件`Style`屬性設定為`Stroke`。 不過，如果`catPath`可直接在此程式中，然後貓的整個標頭會填滿，和鬚線甚至不會顯示。 （試試看 ！）就必須取得該路徑的外框，並使用該的外框`SKPathEffect.Create2DPath`方法。

建構函式會執行此作業。 它會先套用至兩個轉換`catPath`移動 （0，0） 到中心點和縮小的大小。 `GetFillPath` 取得所有外框輪廓中的`outlinedCatPath`，而且該物件用於`SKPathEffect.Create2DPath`呼叫。 的縮放因數`SKMatrix`值會稍微大於水平及垂直大小的數據機用作提供一些緩衝之間的磚，在轉譯因素時衍生稍微廣為人知以便完整 cat 會顯示在畫面格的左上角：

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

建構函式接著會呼叫`SKPathEffect.Create1DPath`扇形的框架。 請注意，路徑的寬度為 100 的像素，但前進是 75 像素為單位，以便複寫的路徑框有重疊的狀況。 建構函式呼叫的最後一個陳述式`SKPathEffect.CreateSum`結合這兩個路徑的效果，並將結果設為`SKPaint`物件。

所有這些工作可讓`PaintSurface`是相當簡單的處理常式。 它只需要定義矩形，並繪製它使用`framePaint`:

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

路徑效果背後的演算法一定會造成整個路徑用來繪製或填滿顯示，這可能會導致矩形外顯示某些視覺效果。 `ClipRect`呼叫之前`DrawRect`呼叫可讓視覺效果，是很簡潔。 （試用而不裁剪 ！）

它會使用`SKPathEffect.CreateCompose`一些抖動加入另一個路徑的效果。 您當然可以實驗您自己的圖片，但稍有不同的範例如下：

**虛線的規劃線條**虛線的規劃線條以填滿橢圓形。 大部分的工作[ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs)類別會直接在欄位定義中的。 這些欄位定義 dash 效果和影線的效果。 係指`static`因為接著會在參考`SKPathEffect.CreateCompose`呼叫中`SKPaint`定義：

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

`PaintSurface`處理常式需要包含僅標準的額外負荷再加上一個呼叫`DrawOval`:

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

因為您已經發現，規劃線條不精確地限制為內部的區域中，並在此範例中，它們一定會開始於整個虛線左側：

[![](effects-images/dashedhatchlines-small.png "虛線規劃頁面的三個螢幕擷取畫面")](effects-images/dashedhatchlines-large.png#lightbox "虛線規劃頁面的三個螢幕擷取畫面")

既然您已了解範圍從簡單的路徑效果點和虛線奇怪的組合，以使用您的想像力，並看看您可以建立。



## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
