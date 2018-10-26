---
title: 非仿射轉換
description: 本文說明如何使用轉換矩陣的第三個資料行建立檢視方塊和錐形的效果，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: da5306ed9c301a7229d2fc5e913a4217e844bbba
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116143"
---
# <a name="non-affine-transforms"></a>非仿射轉換

_使用轉換矩陣的第三個資料行建立檢視方塊和錐形效果_

平移、 縮放、 旋轉、 和扭曲全都歸類為*仿射*轉換。 仿射轉換保留平行的行。 如果兩行平行轉換之前，它們都是平行後轉換。 Parallelograms 一律被轉換矩形。

不過，也是支援的非仿射轉換，有能力將矩形轉換成任何凸方形 SkiaSharp:

![](non-affine-images/nonaffinetransformexample.png "點陣圖，轉換成凸方形")

凸方形是四邊內部角度永遠大於或等於 180 度和沒有彼此相交的側邊。

非仿射轉換的轉換矩陣的第三個資料列設為 0、 0 和 1 以外的值時的結果。 完整`SKMatrix`乘法則是：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

結果的轉換公式如下：

x' = ScaleX·x + SkewX·y + TransX

y' = SkewY·x + ScaleY·y + TransY

z' = Persp0·x + Persp1·y + Persp2

使用 3-3 矩陣之二維轉換的基本規則是，所有項目仍會在平面上其中 Z 等於 1。 除非`Persp0`並`Persp1`為 0，及`Persp2`等於 1，轉換已移關閉該平面的 Z 座標。

若要還原這二維的轉換，座標必須移回至該平面。 另一個步驟是必要的。 X'，y'，與 z '值必須除以 z':

x" = x' / z'

y"= y' / z'

z"= z' / z' = 1

這些值稱為*homogeneous coordinate*和開發時的數學家年 8 月斐迪南 Möbius，更好已知為他的拓撲很奇怪，Möbius 帶狀。

如果 z' 為 0，分區會導致無限的座標。 事實上，開發 homogeneous coordinate Möbius 的動機之一是能夠代表有限的數字的無限值。

當顯示 [圖形]，不過，您想要避免轉譯項目轉換為無限值的座標。 不會轉譯這些座標。 Pe-15 這些座標的所有項目將會非常大，可能不會以視覺化方式一致。

這個方程式中，您不想 z 值，而 ' 變成零：

z' = Persp0·x + Persp1·y + Persp2

因此，這些值會有一些實用的限制： 

`Persp2`資料格可以是零或不為零。 如果`Persp2`是零，則 z' 為零 （0，0） 的點，而這通常就不需要因為該點是在二維圖形中很常見。 如果`Persp2`不等於零，則不會遺失的一般性如果`Persp2`固定為 1。 例如，如果您判斷`Persp2`應該是 5，則您可以只是除以矩陣中的所有資料格 5，使得`Persp2`等於 1，而且結果會相同。

基於這些理由，`Persp2`通常固定為 1，這是身分識別矩陣中的相同值。

一般而言，`Persp0`和`Persp1`是小型的數字。 例如，假設您開始使用的身分識別矩陣但集`Persp0`為 0.01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

轉換公式如下：

x' = x / （0.01·x + 1）

y' = y / （0.01·x + 1）

現在使用這項轉換來轉譯 100 像素的方形方塊，位於原點。 以下是四角轉換的方式：

（0，0） → （0，0）

（0，100） → （0，100）

（100，0） → （50，0）

（100，100） → （50，50）

當 x 是 100，則 z' 分母為 2，因此 x 和 y 座標會有效減半。 方塊的右邊會變成短於左側項目：

![](non-affine-images/nonaffinetransform.png "非仿射轉換會受限於方塊")

`Persp`因為縮短建議方塊現在傾斜的右側，檢視器更，「 檢視方塊 」 指的這些資料格名稱的組件。

**測試透視圖**頁面可讓您試驗值`Persp0`和`Pers1`來瞭解其運作方式。 這些矩陣資料格的合理的值為很小，`Slider`在通用 Windows 平台無法正確處理它們。 若要容納 UWP 問題，這兩個`Slider`中的項目[ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml)必須初始化為範圍從-1，1:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.TestPerspectivePage"
             Title="Test Perpsective">
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
                    <Setter Property="Minimum" Value="-1" />
                    <Setter Property="Maximum" Value="1" />
                    <Setter Property="Margin" Value="20, 0" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="persp0Slider"
                Grid.Row="0"
                ValueChanged="OnPersp0SliderValueChanged" />

        <Label x:Name="persp0Label"
               Text="Persp0 = 0.0000"
               Grid.Row="1" />

        <Slider x:Name="persp1Slider"
                Grid.Row="2"
                ValueChanged="OnPersp1SliderValueChanged" />

        <Label x:Name="persp1Label"
               Text="Persp1 = 0.0000"
               Grid.Row="3" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="4"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

事件處理常式中的滑桿[ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs)程式碼後置檔案會將 100 的值，讓它們 –0.01 和 0.01 之間的範圍。 此外，建構函式會載入點陣圖：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    SKBitmap bitmap;

    public TestPerspectivePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnPersp0SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp0Label.Text = String.Format("Persp0 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }

    void OnPersp1SliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        Slider slider = (Slider)sender;
        persp1Label.Text = String.Format("Persp1 = {0:F4}", slider.Value / 100);
        canvasView.InvalidateSurface();
    }
    ...
}
```

`PaintSurface`處理常式會計算`SKMatrix`值並命名為`perspectiveMatrix`除以 100 這些兩個滑桿的值為基礎。 這會結合兩個轉譯置於中央的點陣圖中的這項轉換的中心的轉換：

```csharp
public partial class TestPerspectivePage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Calculate perspective matrix
        SKMatrix perspectiveMatrix = SKMatrix.MakeIdentity();
        perspectiveMatrix.Persp0 = (float)persp0Slider.Value / 100;
        perspectiveMatrix.Persp1 = (float)persp1Slider.Value / 100;

        // Center of screen
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);
        SKMatrix.PostConcat(ref matrix, perspectiveMatrix);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(xCenter, yCenter));

        // Coordinates to center bitmap on canvas
        float x = xCenter - bitmap.Width / 2;
        float y = yCenter - bitmap.Height / 2;

        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

以下是一些範例映像：

[![](non-affine-images/testperspective-small.png "[測試] 檢視方塊頁面上的三個螢幕擷取畫面")](non-affine-images/testperspective-large.png#lightbox "的 [測試] 檢視方塊頁面上的三個螢幕擷取畫面")

實驗使用滑桿時，您會發現超過 0.0066 或 –0.0066 如下的值會導致要突然 fractured 和不一致的映像。 要進行轉換的點陣圖是 300 像素的正方形。 它會轉換相對於其中心，因此點陣圖的座標範圍從 –150 150。 請記得，z 值，而 ' 是：

z' = Persp0·x + Persp1·y + 1

如果`Persp0`或`Persp1`大於 0.0066 或以下 –0.0066，就一律點陣圖 z 所導致的部分座標 ' 值為零。 導致除數為零，並呈現變得混亂。 當使用非仿射轉換，您會想要避免轉譯任何項目使用這些座標會導致除數為零。

一般而言，您將不會設定`Persp0`和`Persp1`在隔離狀態。 您還有通常需要設定其他資料格，以達到特定類型的非仿射轉換矩陣中。

是一個這類非仿射轉換*錐形轉換*。 這種類型的非仿射轉換會保留一個矩形的整體維度，但 tapers 一端：

![](non-affine-images/tapertransform.png "錐形轉換會受限於方塊")

[ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs)類別來執行非仿射轉換，以根據這些參數的一般化的計算：

- 要進行轉換之影像的矩形的大小
- 列舉，指出 tapers，矩形的側邊
- 另一個的列舉，指出 tapers 如何和
- 錐度的範圍。

程式碼如下：

```csharp
enum TaperSide { Left, Top, Right, Bottom }

enum TaperCorner { LeftOrTop, RightOrBottom, Both }

static class TaperTransform
{
    public static SKMatrix Make(SKSize size, TaperSide taperSide, TaperCorner taperCorner, float taperFraction)
    {
        SKMatrix matrix = SKMatrix.MakeIdentity();

        switch (taperSide)
        {
            case TaperSide.Left:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp0 = (taperFraction - 1) / size.Width;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        matrix.TransY = size.Height * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Top:
                matrix.ScaleX = taperFraction;
                matrix.ScaleY = taperFraction;
                matrix.Persp1 = (taperFraction - 1) / size.Height;

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction);
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        matrix.TransX = size.Width * (1 - taperFraction) / 2;
                        break;
                }
                break;

            case TaperSide.Right:
                matrix.ScaleX = 1 / taperFraction;
                matrix.Persp0 = (1 - taperFraction) / (size.Width * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewY = size.Height * matrix.Persp0;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewY = (size.Height / 2) * matrix.Persp0;
                        break;
                }
                break;

            case TaperSide.Bottom:
                matrix.ScaleY = 1 / taperFraction;
                matrix.Persp1 = (1 - taperFraction) / (size.Height * taperFraction);

                switch (taperCorner)
                {
                    case TaperCorner.RightOrBottom:
                        break;

                    case TaperCorner.LeftOrTop:
                        matrix.SkewX = size.Width * matrix.Persp1;
                        break;

                    case TaperCorner.Both:
                        matrix.SkewX = (size.Width / 2) * matrix.Persp1;
                        break;
                }
                break;
        }
        return matrix;
    }
}
```

這個類別用於**錐形轉換**頁面。 XAML 檔案具現化兩個`Picker`項目選取列舉值，和`Slider`選擇錐形分數。 [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55)處理常式結合錐形轉換具有兩個轉譯進行相對於點陣圖左上角的轉換的轉換：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedItem;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedItem;
    float taperFraction = (float)taperFractionSlider.Value;

    SKMatrix taperMatrix =
        TaperTransform.Make(new SKSize(bitmap.Width, bitmap.Height),
                            taperSide, taperCorner, taperFraction);

    // Display the matrix in the lower-right corner
    SKSize matrixSize = matrixDisplay.Measure(taperMatrix);

    matrixDisplay.Paint(canvas, taperMatrix,
        new SKPoint(info.Width - matrixSize.Width,
                    info.Height - matrixSize.Height));

    // Center bitmap on canvas
    float x = (info.Width - bitmap.Width) / 2;
    float y = (info.Height - bitmap.Height) / 2;

    SKMatrix matrix = SKMatrix.MakeTranslation(-x, -y);
    SKMatrix.PostConcat(ref matrix, taperMatrix);
    SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(x, y));

    canvas.SetMatrix(matrix);
    canvas.DrawBitmap(bitmap, x, y);
}
```

以下是一些範例：

[![](non-affine-images/tapertransform-small.png "錐形轉換 頁面的三個螢幕擷取畫面")](non-affine-images/tapertransform-large.png#lightbox "錐形轉換 頁面的三個螢幕擷取畫面")

一般化的非仿射轉換的另一種是會示範在下一篇文章中的 3D 旋轉[ **3D 旋轉**](3d-rotation.md)。

非仿射轉換可以將矩形轉換成任何凸面的方形。 最好的證明**顯示非仿射矩陣**頁面。 它是非常類似**顯示仿射矩陣**頁面上，從[**矩陣轉換**](matrix.md)發行項之處在於它有第四個`TouchPoint`物件來管理第四個討論的點陣圖：

[![](non-affine-images/shownonaffinematrix-small.png "顯示非仿射矩陣頁面的三個螢幕擷取畫面")](non-affine-images/shownonaffinematrix-large.png#lightbox "的 [顯示非仿射矩陣] 頁面上的三個螢幕擷取畫面")

只要您不要嘗試進行的其中一個角落的點陣圖內部角度大於 180 度，或是對兩邊彼此相交，程式成功地計算轉換使用這個方法從[ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs)類別：

```csharp
static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL, SKPoint ptLR)
{
    // Scale transform
    SKMatrix S = SKMatrix.MakeScale(1 / size.Width, 1 / size.Height);

    // Affine transform
    SKMatrix A = new SKMatrix
    {
        ScaleX = ptUR.X - ptUL.X,
        SkewY = ptUR.Y - ptUL.Y,
        SkewX = ptLL.X - ptUL.X,
        ScaleY = ptLL.Y - ptUL.Y,
        TransX = ptUL.X,
        TransY = ptUL.Y,
        Persp2 = 1
    };

    // Non-Affine transform
    SKMatrix inverseA;
    A.TryInvert(out inverseA);
    SKPoint abPoint = inverseA.MapPoint(ptLR);
    float a = abPoint.X;
    float b = abPoint.Y;

    float scaleX = a / (a + b - 1);
    float scaleY = b / (a + b - 1);

    SKMatrix N = new SKMatrix
    {
        ScaleX = scaleX,
        ScaleY = scaleY,
        Persp0 = scaleX - 1,
        Persp1 = scaleY - 1,
        Persp2 = 1
    };

    // Multiply S * N * A
    SKMatrix result = SKMatrix.MakeIdentity();
    SKMatrix.PostConcat(ref result, S);
    SKMatrix.PostConcat(ref result, N);
    SKMatrix.PostConcat(ref result, A);

    return result;
}
```

為了方便計算，這個方法會當做三個不同的轉換，來表示這裡，以顯示這些轉換如何修改點陣圖的四個邊角的箭號產品取得總轉換：

（0，0） （0，0） 的 → → （0，0） → (x，0，y0) （左上方）

(0、 H) → （0，1） （0，1） 的 → → (x1，y1) （左下方）

(W，0) （1，0） 的 → → （1，0） → (x2 y2) （右上方）

（W、 H） （1，1） 的 → → (a、 b) → (x 3，y3) （右下方）

右邊的最後一個座標是四個觸控點相關聯的四個點。 這些是點陣圖的邊角的最後一個座標。

W 和 H 表示點陣圖的高度與寬度。 第一個轉換`S`只會調整的點陣圖，以 1 像素的正方形。 第二個轉換為非仿射轉換`N`，和第三個是仿射轉換`A`。 該仿射轉換基於三個點，因此它只是像先前仿射[ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68)方法並不需要用到的第四個資料列 (a、 b） 點。

`a`和`b`值的計算方式，以便第三個轉換為仿射。 程式碼取得仿射轉換的反向，並接著使用該值來對應右下角。 這就是重點 (a、 b)。

非仿射轉換的另一個用法是模擬 3d 圖形。 在下一步 文章中， [ **3D 旋轉**](3d-rotation.md)您了解如何旋轉 3D 空間中的二維圖形。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
