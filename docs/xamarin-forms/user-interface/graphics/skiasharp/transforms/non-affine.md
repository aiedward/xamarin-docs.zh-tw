---
title: "非仿射轉換"
description: "轉換矩陣的第三個資料行建立檢視方塊和錐形效果"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: 2e2e83404bc93bd07885008b868c51eba2ff7140
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="non-affine-transforms"></a>非仿射轉換

_轉換矩陣的第三個資料行建立檢視方塊和錐形效果_

平移、 縮放、 旋轉和扭曲全都歸類為*仿射*轉換。 仿射轉換保留平行的行。 如果兩個線條平行轉換之前，它們之後會維持平行轉換。 Parallelograms 一律被轉換的矩形。

不過，也是支援的非仿射轉換，有能力將矩形轉換成任何凸四邊形 SkiaSharp:

![](non-affine-images/nonaffinetransformexample.png "轉換成凸四邊形點陣圖")

凸四邊形是四邊內部角度一律小於 180 度以及沒有彼此相交的側邊。

非仿射轉換的轉換矩陣的第三個資料列設為 0，0 和 1 以外的值時的結果。 完整`SKMatrix`乘法是：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

結果轉換公式是：

x' = ScaleX·x + SkewX·y + TransX

y' = SkewY·x + ScaleY·y + TransY

z' = Persp0·x + Persp1·y + Persp2

使用 3-3 的矩陣轉換二維的基本規則是，所有項目仍會在平面上 Z 等於 1。 除非`Persp0`和`Persp1`是 0，和`Persp2`等於 1，轉換已經關閉該平面的 Z 座標。

若要還原這二維轉換，座標必須移回至該平面。 需要其他步驟。 X'，y'、 z' 值必須是除以 z':

x" = x' / z'

y" = y' / z'

z" = z' / z' = 1

這些值稱為*同質座標*和開發時由數學家年 8 月斐迪南 Möbius，更佳已知他拓樸件怪事，如 Möbius 區域。

如果 z' 為 0，分區會導致無限的座標。 事實上，其中一個開發同質座標 Möbius 的動機是代表有限數目的無限值的能力。

當顯示圖形，不過，您想要避免轉譯這些座標會轉換成無限值的項目。 將不會轉譯這些座標。 這些座標來的所有項目將會非常大並且可能不是以視覺化方式一致。

公式中，您不想 z 的值 ' 變成零：

z' = Persp0·x + Persp1·y + Persp2

`Persp2`資料格可以是零或不是零。 如果`Persp2`是零，則 z' 為零 （0，0） 的點，且通常不需要這樣做因為該點是在二維圖形中很常見。 如果`Persp2`不是等於零，則不會有遺失的一般性如果`Persp2`固定為 1。 例如，如果您判定`Persp2`應為 5，則您可以只是除以矩陣中的所有資料格 5，讓`Persp2`等於 1，而且結果會相同。

基於這些理由，`Persp2`通常固定為 1，這是身分識別矩陣中的相同值。

一般而言，`Persp0`和`Persp1`是小型的數字。 例如，假設您開始使用矩陣但集`Persp0`為 0.01:

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

轉換公式是：

x` = x / (0.01·x + 1)

y' = y / （0.01·x + 1）

現在使用這項轉換來轉譯 100 像素的方塊位於原始位置。 以下是四個角落的轉換方式：

(0, 0) → (0, 0)

(0, 100) → (0, 100)

(100, 0) → (50, 0)

(100, 100) → (50, 50)

當 x 是 100，則 z' 分母為 2，，x 和 y 座標，所以會有效減半。 方塊右邊會變成短於左邊算起的：

![](non-affine-images/nonaffinetransform.png "方塊，必須為非仿射轉換")

`Persp`因為 foreshortening 建議方塊現在傾斜右側檢視器之間的距離越遠，「 檢視方塊 」 指這些資料格名稱的一部分。

**測試透視圖**頁面可讓您試驗值`Persp0`和`Pers1`來初步的運作方式。 這些矩陣資料格的合理的值為很小，`Slider`在通用 Windows 平台無法正確處理它們。 若要容納 UWP 問題，這兩個`Slider`中的項目[ **TestPerspective.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml)必須初始化為範圍為 1-1:

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

中的滑桿的事件處理常式[ `TestPerspectivePage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs)程式碼後置檔案除以值 100，讓它們 –0.01 和 0.01 之間的範圍。 此外，建構函式會載入點陣圖：

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

`PaintSurface`處理常式會計算`SKMatrix`名為值`perspectiveMatrix`根據除以 100 這些兩個滑桿的值。 這會結合兩個轉譯這個轉換的中心放在點陣圖的中心的轉換：

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

[![](non-affine-images/testperspective-small.png "[測試] 檢視方塊 頁面上的三個螢幕擷取畫面")](non-affine-images/testperspective-large.png#lightbox "的 [測試] 檢視方塊 頁面上的三個螢幕擷取畫面")

當您嘗試將滑桿，您會發現 0.0066 超過或低於 –0.0066 的值會導致突然變得 fractured 和一致的映像。 要轉換點陣圖為 300 像素正方形。 它會轉換相對於其中心，讓點陣圖的座標的範圍從 –150 到 150。 請記得，z 的值 ' 是：

z' = Persp0·x + Persp1·y + 1

如果`Persp0`或`Persp1`大於 0.0066 或下方 –0.0066，就一律導致 z 點陣圖的部分座標 ' 值為零。 進而導致除數為零，並呈現變成混亂。 當使用非仿射轉換時，您會想要避免轉譯任何項目以零而造成除法的座標。

一般而言，您將不會設定`Persp0`和`Persp1`隔離。 它也是通常需要設定其他資料格來達成特定類型的非仿射轉換矩陣中。

是一個這類非仿射轉換*錐形轉換*。 這種類型的非仿射轉換會保留整個矩形的維度，但 tapers 一端：

![](non-affine-images/tapertransform.png "遭受錐形轉換方塊")

[ `TaperTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs)類別來執行非仿射轉換這些參數為基礎的通用的計算：

- 要轉換的映像的矩形的大小
- 列舉，指出 tapers，矩形的邊
- 另一個列舉，指出 tapers 如何和
- 錐度的範圍。

下列程式碼：

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

這個類別用於**錐形轉換**頁面。 XAML 檔案會呈現兩個`Picker`選取列舉值的項目和`Slider`選擇錐形分數。 [ `PaintSurface` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55)處理常式結合錐形轉換具有兩個轉譯進行相對於點陣圖左上角轉換的轉換：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    TaperSide taperSide = (TaperSide)taperSidePicker.SelectedIndex;
    TaperCorner taperCorner = (TaperCorner)taperCornerPicker.SelectedIndex;
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

[![](non-affine-images/tapertransform-small.png "[錐形轉換] 頁面上的三個螢幕擷取畫面")](non-affine-images/tapertransform-large.png#lightbox "錐形轉換頁面的三個螢幕擷取畫面")

另一種廣義的非仿射轉換是下一個文件中所示範的 3D 旋轉[3D 旋轉](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)。

非仿射轉換可以將矩形轉換成任何凸四邊形。 這示範**顯示非仿射矩陣**頁面。 非常類似於**顯示仿射矩陣**頁面[矩陣轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/matrix.md)文件不同之處在於它有第四個`TouchPoint`物件來操作第四個角落的點陣圖：

[![](non-affine-images/shownonaffinematrix-small.png "顯示非仿射矩陣頁面的三個螢幕擷取畫面")](non-affine-images/shownonaffinematrix-large.png#lightbox "的 [顯示非仿射矩陣] 頁面上的三個螢幕擷取畫面")

只要您不要嘗試建立點陣圖的四個端點的其中一個內部角度大於 180 度，或建立彼此相交的兩個邊，程式成功地計算轉換使用這個方法從[ `ShowNonAffineMatrixPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs)類別：

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

為了方便計算的這個方法會以三個不同的轉換，來表示這裡，以顯示這些轉換如何修改點陣圖的四個邊角的箭號的產品取得總轉換：

（0，0） （0，0） 的 → → （0，0） → (x，0，y0) （左上方）

(0、 H) → （0，1） （0，1） → → (x1 y1) （左下方）

(W，0) （1，0） 的 → → （1，0） → (x2 y2) （右上方）

（W、 H） （1，1） → → (a、 b) → (x 3 y3) （右下方）

在右邊最後座標是四個觸控點相關聯的四個點。 這些是最終點陣圖的邊角的座標。

W 和 H 代表點陣圖的高度與寬度。 第一個轉換 (`S`) 只是可調整為 1 像素矩形的點陣圖。 第二個轉換為非仿射轉換`N`，和第三個是仿射轉換`A`。 該仿射轉換根據三個點，因此有就像先前仿射[ `ComputeMatrix` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68)方法，而且不包含的第四個資料列 (a、 b） 點。

`a`和`b`，讓仿射轉換第三個計算值。 程式碼取得仿射轉換的反向，並且會使用將該對應右下角。 這點 (a、 b)。

非仿射轉換的另一個用途是模擬 3d 圖形。 在下一個發行項， [3D 旋轉](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/3d-rotation.md)您了解如何旋轉二維圖形在 3D 空間中的。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
