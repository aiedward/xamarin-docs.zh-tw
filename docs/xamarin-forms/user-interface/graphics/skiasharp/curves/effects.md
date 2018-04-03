---
title: 路徑的效果
description: 探索各種路徑效果可讓用於繪製和填滿的路徑
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 95167D1F-A718-405A-AFCC-90E596D422F3
author: charlespetzold
ms.author: chape
ms.date: 07/29/2017
ms.openlocfilehash: 140e5768db797f36299bef358e0296ae57398535
ms.sourcegitcommit: 4f1b508caa8e7b6ccf85d167ea700a5d28b0347e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="path-effects"></a>路徑的效果

_探索各種路徑效果可讓用於繪製和填滿的路徑_

A*路徑效果*的執行個體[ `SKPathEffect` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPathEffect/)類別用來建立一個八個靜態`Create`方法。 `SKPathEffect`物件然後將設定為[ `PathEffect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKPaint.PathEffect/)屬性`SKPaint`繪製小的複寫路徑的線條各種有趣的效果，比方說，物件：

![](effects-images/patheffectsample.png "連結的鏈結範例")

路徑效果可讓您：

- 繪製點和虛線的線條
- 繪製任何填滿的路徑的線條
- 規劃線與填滿區域
- 填滿的區域，以並排顯示的路徑
- 請尖銳圓角
- 將隨機選取"抖動"線條和曲線

此外，您可以結合兩個或多個路徑的效果。

本文也會示範如何使用`GetFillPath`方法`SKPaint`套用的屬性，將一個路徑轉換成另一個路徑`SKPaint`，包括`StrokeWidth`和`PathEffect`。 這會導致一些有趣的技術，例如取得另一個路徑的外框的路徑。 `GetFillPath` 也很有用之路徑的效果。

## <a name="dots-and-dashes"></a>句點和連字號

使用[ `PathEffect.CreateDash` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDash/p/System.Single[]/System.Single/)發行項中所說明的方法[**點和虛線**](~/xamarin-forms/user-interface/graphics/skiasharp/paths/dots.md)。 方法的第一個引數是包含兩個或多個值，交替的虛線的長度，以及連字號間之間距的長度為偶數的陣列：

```csharp
public static SKPathEffect CreateDash (Single[] intervals, Single phase)
```

這些值是*不*相對於筆劃寬度。 例如，如果筆劃寬度為 10，而您會想一條線方形的虛線和間隙方形所組成，設定`intervals`陣列 {10，10}。 `phase`引數指出虛線圖樣線條開始位置。 在此範例中，如果您想以方形的間隔開始的行，設定`phase`為 10。

受影響的連字號結尾`StrokeCap`屬性`SKPaint`。 寬筆劃寬度的是很常見，無法將此屬性設定為`SKStrokeCap.Round`要捨入的連字號結尾。 在此情況下中的值`intervals`陣列執行*不*包括額外長度所產生的捨入，這表示一個循環的點，必須指定為零的寬度。 如筆劃寬度為 10，若要建立一條線循環的點與相同的直徑的點之間的間距，使用`intervals`陣列 {0，20}。

**動畫以點分隔的文字**頁面是類似於**概述文字**頁面文件中所述[**整合文字和圖形**](~/xamarin-forms/user-interface/graphics/skiasharp/basics/text.md)中它會顯示概述文字字元，藉由設定`Style`屬性`SKPaint`物件`SKPaintStyle.Stroke`。 此外，**動畫以點分隔的文字**使用`SKPathEffect.CreateDash`來提供這外框虛線的外觀，並將程式也動畫`phase`引數的`SKPathEffect.CreateDash`方法以進行似乎周遊文字周圍的點字元。 以下是頁面上以橫向模式：

[![](effects-images/animateddottedtext-small.png "三個螢幕擷取畫面顯示動畫以點分隔的文字頁面的")](effects-images/animateddottedtext-large.png#lightbox "動畫以點分隔的文字頁面的三個螢幕擷取畫面")

[ `AnimatedDottedTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別一開始會定義一些常數，也會覆寫`OnAppearing`和`OnDisappearing`動畫的方法：

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

`PaintSurface`處理常式一開始會建立`SKPaint`來顯示文字的物件。 `TextSize`根據螢幕的寬度調整屬性：

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
            SKRect textBounds;
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

結束時的方法，`SKPathEffect.CreateDash`使用呼叫方法`dashArray`，會定義為欄位，以及動畫`phase`值。 `SKPathEffect`設執行個體`PathEffect`屬性`SKPaint`來顯示文字的物件。

或者，您可以設定`SKPathEffect`物件`SKPaint`之前測量的文字，然後將它置中頁面上的物件。 在此情況下，不過，動畫的句點和連字號會有些不同導致所呈現的文字大小，文字會有點震動。 （試試看吧 ！）

您也會發現為動畫的點的圓形周圍的文字字元，沒有每個封閉曲線中的某一點點似乎存在進出快顯。 這是定義字元外框的路徑開始和結束的位置。 如果路徑長度不是整數類資料的虛線圖樣 （在此情況下 20 像素為單位） 的長度只屬於此模式中可以容納在路徑結尾。

您可以調整以符合路徑的長度虛線圖樣的長度，但需要決定長度的路徑，一種技術，可在未來的文章所涵蓋。

**點 / 虛線 Morph**程式繪製虛線圖樣本身，以便連字號似乎分成數個點，該表單連字號來結合，一次：

[![](effects-images/dotdashmorph-small.png "三個螢幕擷取畫面的點虛線 Morph 頁面")](effects-images/dotdashmorph-large.png#lightbox "點虛線 Morph 頁面的三個螢幕擷取畫面")

[ `DotDashMorphPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DotDashMorphPage.cs)類別覆寫`OnAppearing`和`OnDisappearing`方法就像前一個程式，但類別會定義`SKPaint`物件做為欄位：

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

`PaintSurface`處理常式建立橢圓形的路徑 頁面上，大小為基礎，並執行很長的一段程式碼，以設定`dashArray`和`phase`變數。 為動畫變數`t`範圍從 0 到 1，`if`區塊分解成四個 [季]，並在每個這些季度的該時間`tsub`也範圍從 0 到 1。 在極結束時，程式會建立`SKPathEffect`並將其設`SKPaint`繪圖的物件。

## <a name="from-path-to-path"></a>從路徑與路徑

[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/)方法`SKPaint`變成根據中設定另一個路徑`SKPaint`物件。 若要查看其運作方式，來取代`canvas.DrawPath`先前的程式以下列程式碼中呼叫：

```csharp
SKPath newPath = new SKPath();
bool fill = ellipsePaint.GetFillPath(ellipsePath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);

```

在這個新的程式碼，`GetFillPath`呼叫轉換`ellipsePath`（此為只橢圓形） 到`newPath`，這時會顯示與`newPaint`。 `newPaint`物件建立使用所有預設的屬性設定不同之處在於`Style`根據設定屬性的布林值會傳回值，從`GetFillPath`。

視覺效果除了之外完全相同的色彩中所設定`ellipsePaint`但不是`newPaint`。 而不是簡單中定義的橢圓形`ellipsePath`，`newPath`包含許多定義點和虛線的一系列的路徑分佈。 這是套用的各種屬性的結果`ellipsePaint`— `StrokeWidth`， `StrokeCap`，和`PathEffect`— 以`ellipsePath`置於結果的路徑和`newPath`。 `GetFillPath`方法會傳回布林值，指出目的地路徑是否要填滿，則傳回值是在此範例中，`true`以便填滿的路徑。

請嘗試變更`Style`中設定`newPaint`至`SKPaintStyle.Stroke`您將會看到所述的其中一個像素寬度第一行的個別路徑分佈。

## <a name="stroking-with-a-path"></a>繪製路徑

[ `SKPathEffect.Create1DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create1DPath/p/SkiaSharp.SKPath/System.Single/System.Single/SkiaSharp.SKPath1DPathEffectStyle/)方法在概念上類似`SKPathEffect.CreateDash`不同之處在於您指定的路徑，而非虛線和間隙的圖樣。 此路徑筆觸線條或曲線複寫許多次。

其語法為：

```csharp
public static SKPathEffect Create1DPath (SKPath path, Single advance,
                                         Single phase, SKPath1DPathEffectStyle style)
```

> [!IMPORTANT]
> 注意： 的多載`Create1DPath`定義類型的列舉型別引數與`SkPath1DPathEffect`包含小寫的 'k'。 此名稱的錯誤，並因此列舉型別和方法已被取代，但是很簡單，已被取代的方法，才能成為您的程式碼的一部分而且很難確切地查看哪些是錯誤。

一般而言，您將傳遞至的路徑`Create1DPath`將小型和中心點 （0，0）。 `advance`參數指出路徑會列在複製路徑中心之間的距離。 您通常會設定為大約路徑寬度的這個引數。 `phase`中沒有相同的角色這裡為它的引數所扮演`CreateDash`方法。

[ `SKPath1DPathEffectStyle` ](https://developer.xamarin.com/api/type/SkiaSharp.SKPath1DPathEffectStyle/)有三個成員：

- [`Translate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Translate/)
- [`Rotate`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Rotate/)
- [`Morph`](https://developer.xamarin.com/api/field/SkiaSharp.SKPath1DPathEffectStyle.Morph/)

`Translate`成員會造成能夠保持在相同的方向，因為它複寫沿著線條或曲線的路徑。 如`Rotate`，根據曲線一側正切函數旋轉路徑。 路徑有其正常方向的水平線。 `Morph` 類似於`Rotate`不同之處在於路徑本身也曲線要比對正在圖案線條的曲度。

**1 D 路徑效果**頁面示範這三個選項。 [ **OneDimensionalPathEffectPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml)檔案會定義包含三個項目對應至列舉的三個成員的選擇器：

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
            <Picker.Items>
                <x:String>Translate</x:String>
                <x:String>Rotate</x:String>
                <x:String>Morph</x:String>
            </Picker.Items>
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

[ **OneDimensionalPathEffectPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/OneDimensionalPathEffectPage.xaml.cs)程式碼後置檔案會定義三個`SKPathEffect`做為欄位的物件。 這些所有建立使用`SKPathEffect.Create1DPath`與`SKPath`使用建立的物件`SKPath.ParseSvgPathData`。 第一個是簡單的方塊、 第二個是一個菱形圖案，和第三個是一個矩形。 這些用來示範三個效果樣式：

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

            switch (effectStylePicker.Items[effectStylePicker.SelectedIndex])
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

`PaintSurface`處理常式會建立迴圈循環本身，並存取選擇器，以判斷哪一個貝茲曲線`PathEffect`應該用來繪製它。 三個選項- `Translate`， `Rotate`，和`Morph`— 從左到右顯示：

[![](effects-images/1dpatheffect-small.png "三個螢幕擷取畫面的 1d 路徑效果頁面")](effects-images/1dpatheffect-large.png#lightbox "1d 路徑效果頁面的三個螢幕擷取畫面")

中指定的路徑`SKPathEffect.Create1DPath`方法一律會填滿。 中指定的路徑`DrawPath`方法永遠繪製如果`SKPaint`物件都有其`PathEffect`屬性設定為 1 D 路徑效果。 請注意，`pathPaint`物件之中未包含任何`Style`設定，通常會預設為`Fill`，但無論繪製路徑。

方塊用`Translate`範例是 20 像素方形，而`advance`引數設定為 24。 這項差異會導致行大致上是水平或垂直，但線條是對角線，因為方塊的對角線為 28.3 像素時，方塊的方式稍有重疊時，在方塊之間的間距。 

中的菱形圖案`Rotate`範例也是 20 像素寬。 `advance`以便在持續點觸及菱形旋轉線條的曲度以及設定為 20。

中的矩形圖形`Morph`範例為 50 像素寬`advance`55 的設定，讓矩形間小的缺口，因為它們彎曲周圍貝茲曲線。

如果`advance`引數小於路徑的大小，則複寫的路徑可以重疊。 這會導致一些有趣的效果。 **連結的鏈結**頁面會顯示一系列的重疊圓形似乎類似連結的鏈結，catenary 特殊圖形中停止回應：

[![](effects-images/linkedchain-small.png "連結的鏈結頁面的三個螢幕擷取畫面")](effects-images/linkedchain-large.png#lightbox "連結的鏈結頁面的三個螢幕擷取畫面")

看起來非常接近，而且您會看到這些並不是實際的圓形。 鏈結中的每個連結為兩個弧形的大小和位置，讓它們似乎連接與相鄰的連結。

鏈結或纜線統一的權數分配的停止回應的 catenary 形式。 內建的反向 catenary 表單 arch 受益的壓力，從 a c h 的加權分散。 Catenary 具有表面上簡單數學描述：

y = a · cosh(x / a)

*Cosh*是雙曲線餘弦函數。 如*x*等於 0， *cosh*為零和*y*等於。 這是 catenary 的中心。 像*餘弦函數*函式， *cosh*即為*甚至*，這表示*cosh(–x)*等於*cosh(x)*，並增加增加正數或負數的引數的值。 這些值描述形成 catenary 方曲線。

尋找適當的值以符合的手機上的頁面尺寸 catenary 不是直接的計算。 如果*w*和*h*是最佳的值是矩形的高度與寬度滿足以下方程式：

cosh(w / 2 / a) = 1 + h / a

中的下列方法[ `LinkedChainPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/LinkedChainPage.cs)類別可藉由參考左側和右側等號做為兩個運算式會併入該相等`left`和`right`。 值較小，`left`大於`right`; 中的大型值，`left`是小於`right`。 `while`迴圈中的最佳值縮減:

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

`SKPath`物件類別的建構函式和結果中建立的連結`SKPathEffect`物件然後將設定為`PathEffect`屬性`SKPaint`儲存為欄位的物件：

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

主要工作`PaintSurface`處理常式會建立本身 catenary 的路徑。 決定最佳之後並儲存在`optA`變數，也必須計算從視窗頂端的位移。 然後，它可以累積的集合`SKPoint`catenary 的值為一個路徑，此功能，以及繪製的路徑與先前建立`SKPaint`物件：

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

此程式定義中使用的路徑`Create1DPath`有其 （0，0） 中的中心點。 這看起來合理因為 （0，0） 與線條或曲線，它在裝飾對齊路徑的點。 不過，您可以使用非集中 （0，0） 點的某些特殊效果。

**輸送帶**頁面會建立與下列類似尾巴的輸送帶與曲線上下這會調整成視窗大小的路徑。 該路徑會使用簡單圖案`SKPaint`物件 20 像素寬、 彩色的灰色，並接著圖案與另一個`SKPaint`物件`SKPathEffect`參考路徑，類似一個框住小的值區的物件：

[![](effects-images/conveyorbelt-small.png "輸送帶頁面的三個螢幕擷取畫面")](effects-images/conveyorbelt-large.png#lightbox "輸送帶頁面的三個螢幕擷取畫面")

（0，0） 的時間點的值區路徑是控制代碼，因此當`phase`引數會動畫、 心力都圍繞的輸送帶，或許 scooping 向上底部水和傾印出頂端似乎貯體。

[ `ConveyorBeltPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/ConveyorBeltPage.cs)類別會實作覆寫的動畫`OnAppearing`和`OnDisappearing`方法。 頁面的建構函式中定義的路徑，值區：

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

值區建立程式碼會完成的兩個轉換，讓變大一點的值區，並開啟它側邊。 套用這些轉換是更為調整在先前的程式碼中的所有座標。 

`PaintSurface`處理常式開始藉由定義本身的輸送帶的路徑。 這是只是一組線條和分號圓形繪製以 20 像素寬暗灰色列的一組：

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

值區應該間距約 200 像素上輸送帶距離。 不過，輸送帶可能不是 200 像素長，這表示它作為倍數`phase`引數的`SKPathEffect.Create1DPath`是動畫，值區會快顯進出是否存在。 

基於這個理由，程式會先計算名為的值`length`也就是輸送帶的長度。 輸送帶包含直線和分號的圓形，因為這是簡單的計算。 接下來，bucket 的數目會計算除以`length`由 200。 這會四捨五入為最接近的整數，而數字就分成`length`。 結果是一個整數的值區的間距。 `phase`引數是直接的分數。

## <a name="from-path-to-path-again"></a>從路徑的路徑

在底部`DrawSurface`中的處理常式**輸送帶**，標記為註解`canvas.DrawPath`呼叫，並取代為下列程式碼：

```csharp
SKPath newPath = new SKPath();
bool fill = bucketsPaint.GetFillPath(conveyerPath, newPath);
SKPaint newPaint = new SKPaint
{
    Style = fill ? SKPaintStyle.Fill : SKPaintStyle.Stroke
};
canvas.DrawPath(newPath, newPaint);
```

如同上述範例的`GetFillPath`，您會看到結果會相同，但是色彩。 在執行之後`GetFillPath`、`newPath`物件包含的值區路徑的多個複本，每位在同一個特別的動畫就會將它們放置在呼叫時。

## <a name="hatching-an-area"></a>影線區域

[ `SKPathEffect.Create2DLines` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DLine/p/System.Single/SkiaSharp.SKMatrix/)方法填滿區域平行線，通常稱為*影線行*。 方法具有下列語法：

```csharp
public static SKPathEffect Create2DLine (Single width, SKMatrix matrix)
```

`width`引數會指定筆觸線條的寬度影線。 `matrix`參數是調整和選擇性旋轉的組合。 縮放比例表示 Skia 使用空間規劃線的像素遞增。 行之間的分隔是負的縮放比例`width`引數。 如果縮放比例小於或等於`width`值影線線條之間會有任何空間和區域會顯示要填滿。 指定水平和垂直縮放比例的相同值。 

根據預設，規劃線是水平的。 如果`matrix`參數包含旋轉、 規劃線會朝順時針方向旋轉。

**填滿影線**頁面示範這個路徑的影響。 [ `HatchFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/HatchFillPage.cs)類別做為欄位會定義三種路徑效果，它們是水平影線線條寬度為 3 的像素，並提供縮放因數，指出第一個間距相距 6 個像素。 行之間的分離，因此為 3 的像素。 第二個路徑效果是垂直規劃線寬度為 6 個像素間距 24 像素的距離 （因此分隔為 18 個像素），和第三個是斜對角影線行 12 個像素寬間距 36 佹萺慒距離。 

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
        SKMatrix target;
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

請注意矩陣`Multiply`方法。 水平和垂直縮放比例是相同的因為乘上的縮放和旋轉的矩陣的順序並不重要。

`PaintSurface`處理常式具有三個不同的色彩，搭配使用下列三個路徑效果`fillPaint`填滿圓角的矩形的大小調整成符合頁面。 `Style`屬性上設定`fillPaint`會被忽略; 當`SKPaint`物件包含從建立路徑效果`SKPathEffect.Create2DLine`，無論填滿區域：

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

如果您仔細查看結果，您會看到紅色和藍色影線行不精確限制圓角矩形。 （這是很明顯在極基礎 Skia 程式碼的特性）。如果這是令人滿意，斜對角影線格線以綠色顯示的替代方式： 圓角的矩形當做裁剪路徑，並規劃線條會繪製在整個頁面。

`PaintSurface`處理常式結尾的呼叫只繪製圓角的矩形，讓您可以查看具有紅色和藍色影線行的差異：

[![](effects-images/hatchfill-small.png "規劃圖樣填滿頁面的三個螢幕擷取畫面")](effects-images/hatchfill-large.png#lightbox "規劃圖樣填滿頁面的三個螢幕擷取畫面")

Android 螢幕真的看起來像這樣： 的螢幕擷取畫面縮放比例導致精簡紅線，以便將合併成看似廣紅線精簡空間和更多的空間。

## <a name="filling-with-a-path"></a>填滿的路徑

[ `SKPathEffect.Create2DPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.Create2DPath/p/SkiaSharp.SKMatrix/SkiaSharp.SKPath/)作用中可讓您以水平和垂直，複寫路徑填滿區域並排區域：

```csharp
public static SKPathEffect Create2DPath (SKMatrix matrix, SKPath path)
```

`SKMatrix`縮放比例表示複寫路徑的水平和垂直間距。 但您不能旋轉使用此路徑`matrix`引數; 如果您想路徑旋轉，旋轉路徑本身使用`Transform`方法所定義`SKPath`。 

複寫的路徑通常對齊的左端和頂端邊緣螢幕，而不是被填滿的區域。 您可以藉由提供介於 0 到指定的左端和頂端旁邊的水平和垂直位移的縮放比例因素轉譯因素來覆寫這個行為。

**路徑並排顯示填滿**頁面示範這個路徑的影響。 用來並排顯示區域路徑定義中的欄位為[ `PathFileFillPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathTileFillPage.cs)類別。 水平和垂直座標介於 –40 到 40，這表示這個路徑是 80 個像素正方形： 

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

在`PaintSurface`處理常式，`SKPathEffect.Create2DPath`呼叫至 64 導致重疊的 80 個像素正方形磚設定水平和垂直間距。 幸運的是，路徑看起來像拼圖棋子，正確地切割 meshing 與相鄰的並排顯示：

[![](effects-images/pathtilefill-small.png "路徑並排顯示填滿頁面的三個螢幕擷取畫面")](effects-images/pathtilefill-large.png#lightbox "路徑並排顯示填滿頁面的三個螢幕擷取畫面")

從原始的螢幕擷取畫面縮放比例會導致某些扭曲程度，特別是 Android 螢幕。

請注意，這些磚一律會顯示整個永遠不會截斷。 除了在 Windows 10 行動裝置版 畫面中，它不是更明顯被填滿的區域是圓角的矩形。 如果您想要截斷這些磚的特定區域，使用裁剪路徑。

請嘗試設定`Style`屬性`SKPaint`物件`Stroke`，您將會看到個別的圖格所述，而不會填滿。

## <a name="rounding-sharp-corners"></a>捨入尖角

**四捨五入 Heptagon**程式中呈現[**三個方法可以繪製弧形**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/arcs.md)文章用於正切弧線曲線的七個雙側 」 圖形的點。 **另一個四捨五入 Heptagon**頁面會顯示更簡便的方法會從建立路徑效果[ `SKPathEffect.CreateCorner` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCorner/p/System.Single/)方法：

```csharp
public static SKPathEffect CreateCorner (Single radius)
```

雖然單一引數名為`radius`您必須將它設定為一半的所需的圓角半徑。 （這是基礎 Skia 程式碼的特性）。

以下是`PaintSurface`中的處理常式[ `AnotherRoundedHeptagonPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/AnotherRoundedHeptagonPage.cs)類別：

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

您可以使用這種效果繪製或根據填滿`Style`屬性`SKPaint`物件。 以下是所有三個平台上：

[![](effects-images/anotherroundedheptagon-small.png "三個螢幕擷取畫面的另一個四捨五入 Heptagon 頁面")](effects-images/anotherroundedheptagon-large.png#lightbox "的另一個四捨五入 Heptagon 頁面的三個螢幕擷取畫面")

您會看到此圓角的 heptagon 等同於先前的程式。 如果您需要更多說服圓角半徑是真正 100，而不要中指定 50`SKPathEffect.CreateCorner`呼叫時，您可以取消註解最終的陳述式中的程式，請參閱 100 radius 圓形重疊角落上。

## <a name="random-jitter"></a>隨機抖動

有時電腦圖形中的掌控的直線很想，和不想要使用極小的隨機性。 在此情況下，您會想要再試一次[ `SKPathEffect.CreateDiscrete` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateDiscrete/p/System.Single/System.Single/System.UInt32/)方法：

```csharp
public static SKPathEffect CreateDiscrete (Single segLength, Single deviation, UInt32 seedAssist)
```

您可以使用此路徑效果繪製或填滿。 行分成連接區段，其近似長度所指定`segLength`— 及擴充不同的方向。 所指定的範圍從原始列差`deviation`。 

最後一個引數是用來產生虛擬亂數序列的效果所使用的種子。 抖動效果看起來稍有不同的不同的種子。 引數的預設值是零，表示結果是相同每次執行程式。 如果您希望不同抖動重繪螢幕，您可以設定種子為`Millisecond`屬性`DataTime.Now`值 （例如）。


**抖動實驗**頁面可讓您試驗不同的值中繪製的矩形：

[![](effects-images/jitterexperiment-small.png "三倍抖動實驗頁面的螢幕擷取畫面")](effects-images/jitterexperiment-large.png#lightbox "Triple screenshot of the JitterExperiment page")

此程式會警告並不如直接。 [ **JitterExperimentPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml)檔案會呈現兩個`Slider`項目和`SKCanvasView`:

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

`PaintSurface`中的處理常式[ **JitterExperimentPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterExperimentPage.xaml.cs)程式碼後置檔案便稱為每當`Slider`值變更。 它會呼叫`SKPathEffect.CreateDiscrete`使用兩個`Slider`值，並使用該值來繪製矩形：

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

您可以使用這個效果，以便填滿，在此情況下填滿區域的外框限於這些隨機的偏差。 **抖動文字**頁面將示範如何使用此路徑效果來顯示文字。 中的程式碼的大部分`PaintSurface`處理常式的[ `JitterTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/JitterTextPage.cs)類別專用於縮放及置中的文字：

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
        SKRect textBounds;
        textPaint.MeasureText(text, ref textBounds);

        // Calculate offsets to center the text on the screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此處以橫向模式上執行所有三個平台：

[![](effects-images/jittertext-small.png "三倍抖動的文字頁面的螢幕擷取畫面")](effects-images/jittertext-large.png#lightbox "Triple screenshot of the JitterText page")

## <a name="path-outlining"></a>路徑大綱

您已經看到的兩個小範例[ `GetFillPath` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/System.Single/)方法`SKPaint`，這也存在於[多載](https://developer.xamarin.com/api/member/SkiaSharp.SKPaint.GetFillPath/p/SkiaSharp.SKPath/SkiaSharp.SKPath/SkiaSharp.SKRect/System.Single/):

```csharp
public Boolean GetFillPath (SKPath src, SKPath dst, Single resScale)

public Boolean GetFillPath (SKPath src, SKPath dst, SKRect cullRect, Single resScale)
```

只需要前兩個引數。 方法會存取所參考的路徑`src`引數，會修改路徑資料中的筆觸屬性為基礎`SKPaint`物件 (包括`PathEffect`屬性)，然後將 將結果`dst`路徑。 `resScale`參數可讓以建立較小的目的地路徑，有效位數減少和`cullRect`引數可以排除外部矩形分佈。

一個基本使用這個方法不會不包含路徑的效果。 如果`SKPaint`物件都有其`Style`屬性設定為`SKPaintStyle.Stroke`，並且不*不*有其`PathEffect`設定，則`GetFillPath`建立路徑，表示*大綱*的來源路徑如同它已被圖案 [小畫家] 屬性。

例如，如果`src`路徑是簡單的圓形的半徑 500，和`SKPaint`物件指定筆劃寬度為 100，則`dst`路徑會變成兩個重要同心圓，半徑為 550 半徑為 450，另一個。 此方法會呼叫`GetFillPath`因為填滿此`dst`路徑等同於繪製`src`路徑。 但您也可以繪製`dst`查看路徑外框的路徑。

**點選外框以路徑**示範這項處理。 `SKCanvasView`和`TapGestureRecognizer`中執行個體化[ **TapToOutlineThePathPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml)檔案。 [ **TapToOutlineThePathPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/TapToOutlineThePathPage.xaml.cs)程式碼後置檔案會定義三個`SKPaint`物件做為欄位，兩個進行繪製的筆觸寬度 100 和 20，並填滿的第三個：

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

圓形填滿，圖案如您所預期的：

[![](effects-images/taptooutlinethepathnormal-small.png "一般點選要大綱 Path 分頁的三個螢幕擷取畫面")](effects-images/taptooutlinethepathnormal-large.png#lightbox "一般點選要大綱 Path 分頁的三個螢幕擷取畫面")

當您點選 螢幕`outlineThePath`設`true`，和`PaintSurface`處理常式會建立全新`SKPath`物件，並使用該值做為目的地路徑的呼叫中`GetFillPath`上`redThickStroke`小畫家 物件。 然後填入並圖案與該目的地路徑`redThinStroke`，並產生下列：

[![](effects-images/taptooutlinethepathoutlined-small.png "三個螢幕擷取畫面的外框的點選要大綱 Path 頁面")](effects-images/taptooutlinethepathoutlined-large.png#lightbox "外框的點選要大綱 Path 頁面的三個螢幕擷取畫面")

兩個紅色圓形清楚指出循環的原始路徑都已轉換成兩個循環的分佈。

此方法就會很有用的開發要用於路徑`SKPathEffect.Create1DPath`方法。 複寫路徑時，一律會填入您在這些方法中指定的路徑。 如果您不想要填入的完整路徑，您必須仔細定義外框。

例如，在**連結的鏈結**範例，以一系列的四個弧形，每一對根據所概述的路徑，要填滿區域的兩個 radii 定義的連結。 可取代中的程式碼`LinkedChainPage`要稍有不同的類別。

首先，您會想要重新定義`linkRadius`常數：

```csharp
const float linkRadius = 27.5f;
const float linkThickness = 5;
```

`linkPath`現在根據該單一 radius，與所需的兩個弧形開始角度和掃掠角度是：

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

`outlinePath`物件就的外框的收件者`linkPath`時繪製在指定的屬性`strokePaint`。 

使用這項技術的另一個範例中使用的路徑用於接下來`SKPathEffect.Create2DPath`方法。 

## <a name="combining-path-effects"></a>合併路徑效果

兩種最終靜態建立方法的`SKPathEffect`是[ `SKPathEffect.CreateSum` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateSum/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/)和[ `SKPathEffect.CreateCompose` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPathEffect.CreateCompose/p/SkiaSharp.SKPathEffect/SkiaSharp.SKPathEffect/):

```csharp
public static SKPathEffect CreateSum (SKPathEffect first, SKPathEffect second)

public static SKPathEffect CreateCompose (SKPathEffect outer, SKPathEffect inner)
```

這兩種方法結合兩個路徑效果，來建立複合路徑效果。 `CreateSum`方法會建立套用個別的兩個路徑效果類似路徑效果時`CreateCompose`適用於其中一個路徑的影響 ( `inner`)，然後將套用`outer`的。

您已經看到如何`GetFillPath`方法`SKPaint`可以將一個路徑轉換成另一個路徑根據`SKPaint`屬性 (包括`PathEffect`) 讓它不應該是*太*神秘如何`SKPaint`物件可以執行這項操作，兩次發生指定的兩個路徑結果`CreateSum`或`CreateCompose`方法。

明顯的用途之一`CreateSum`是定義`SKPaint`填滿的路徑，並且將一個路徑的影響，並且 strokes 另一個路徑效果路徑物件。 這示範於**框架中的 Cats**範例中，會顯示以扇形邊緣 cats 範圍內的陣列：

[![](effects-images/catsinframe-small.png "在框架 Cats 頁面的三個螢幕擷取畫面")](effects-images/catsinframe-large.png#lightbox "Cats 中框架頁面的三個螢幕擷取畫面")

[ `CatsInFramePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/CatsInFramePage.cs)類別開始會定義數個欄位。 您可能會辨識的第一個欄位從[ `PathDataCatPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/PathDataCatPage.cs)類別從[ **SVG 路徑資料**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/path-data.md)發行項。 第二個路徑根據一條線和弧線 scallop 模式的框架：

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

`catPath`無法用於`SKPathEffect.Create2DPath`方法如果`SKPaint`物件`Style`屬性設定為`Stroke`。 不過，如果`catPath`可直接在程式中，然後 cat 的整個標頭會填滿，而且鬚甚至不會是可見的。 （試試看吧 ！）它是為了取得該路徑的大綱、 使用中的大綱`SKPathEffect.Create2DPath`方法。

建構函式會執行這項作業。 它會先套用至兩個轉換`catPath`移動 （0，0） 到中心點和向下調整的大小。 `GetFillPath` 取得在分佈的所有大綱`outlinedCatPath`，而且該物件會用於`SKPathEffect.Create2DPath`呼叫。 縮放因數`SKMatrix`值會稍微大於水平和數據機用作提供小緩衝區時轉譯因素的磚之間的垂直大小衍生稍微實證，如此才能完整 cat 看到中畫面格的左上角：

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

然後呼叫建構函式`SKPathEffect.Create1DPath`扇形框架。 請注意，路徑的寬度是 100 像素，但前進是 75 像素為單位，以便複寫的路徑有重疊周圍框架的狀況。 最終的陳述式的建構函式呼叫`SKPathEffect.CreateSum`結合兩個路徑效果，以及將結果設`SKPaint`物件。

這項工作可讓`PaintSurface`是相當簡單的處理常式。 它只需要定義矩形以及它使用繪製`framePaint`:

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

路徑效果背後的演算法一定會造成整個路徑用於繪製或填滿顯示，這可能會出現在矩形的外面的某些視覺效果。 `ClipRect`之前呼叫`DrawRect`呼叫可讓要大幅清除器造成的視覺效果。 （試試而不裁剪 ！）

它會使用`SKPathEffect.CreateCompose`某些抖動加入另一個路徑的效果。 您可以直接實驗您自己，但以下是稍有不同的範例：

**虛線影線**虛線的規劃線與填滿橢圓形。 中的工作，大部分[ `DashedHatchLinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Curves/DashedHatchLinesPage.cs)類別執行中的欄位定義的權限。 這些欄位會定義虛線效果及串聯效果。 定義為`static`因為再中參考`SKPathEffect.CreateCompose`呼叫中`SKPaint`定義：

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
        SKMatrix target;
        SKMatrix.Concat(ref target, first, second);
        return target;
    }
}
```

`PaintSurface`包含只有一般的額外負荷再加上一個呼叫的處理常式需要`DrawOval`:

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

您已經發現，規劃線不是精確限制為內部的區域，以及在此範例中，它們一定會開始於整個虛線左側：

[![](effects-images/dashedhatchlines-small.png "三個螢幕擷取畫面的虛線影線頁面")](effects-images/dashedhatchlines-large.png#lightbox "虛線影線頁面的三個螢幕擷取畫面")

現在您已看過路徑效果，範圍從簡單的點及連字號到奇怪的組合，使用您自己的想像並查看您可以建立。



## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
