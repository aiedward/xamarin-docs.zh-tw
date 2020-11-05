---
title: 非仿射轉換
description: 本文說明如何使用轉換矩陣的第三個數據行來建立透視圖和錐度效果，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 785F4D13-7430-492E-B24E-3B45C560E9F1
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 16085f8bb3af57fdd0c00c7c111a91df1dd273f7
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374546"
---
# <a name="non-affine-transforms"></a>非仿射轉換

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用轉換矩陣的第三個數據行來建立透視圖和錐度效果_

轉譯、縮放、旋轉和扭曲全都分類為 *仿射* 轉換。 仿射轉換會保留平行線。 如果兩行在轉換之前是平行的，它們會在轉換後保持平行。 矩形一律會轉換成 parallelograms。

不過，SkiaSharp 也能夠使用非仿射轉換，其功能可以將矩形轉換成任何凸四邊形：

![轉換成凸四邊形的點陣圖](non-affine-images/nonaffinetransformexample.png)

凸四邊形是四邊的圖，其內部角度一律小於180度且不會彼此相交。

當轉換矩陣的第三個數據列設定為0、0和1以外的值時，就會產生非仿射轉換。 完整的 `SKMatrix` 乘法是：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z' |
              │ TransX  TransY  Persp2 │
</pre>

結果轉換公式如下：

x ' = ScaleX · x + SkewX · y + TransX

y ' = SkewY · x + ScaleY · y + TransY

z ' = Persp0 · x + Persp1 · y + Persp2

針對二維轉換使用 3 x 3 矩陣的基本規則是，所有專案都會保留在 Z 等於1的平面上。 除非 `Persp0` 和 `Persp1` 為0，而 `Persp2` 等於1，否則轉換已將 Z 座標從該平面移出。

若要將此還原為二維轉換，必須將座標移回該平面。 需要另一個步驟。 X '，y ' 和 z ' 值必須除以 z '：

x "= x '/z '

y "= y '/z '

z "= z '/z ' = 1

這些都稱為 *同質座標* ，而且是由 Mathematician 8 月 Ferdinand Möbius 所開發，其拓撲件怪事為 Möbius 區域。

如果 z ' 是0，則除法會產生無限座標。 事實上，開發同質座標的 Möbius's 動機之一，就是能夠以有限的數位來表示無限值。

不過，在顯示圖形時，您會想要避免以轉換成無限值的座標呈現。 這些座標不會呈現。 這些座標鄰近範圍中的所有專案都很大，而且可能無法以視覺化方式呈現。

在此方程式中，您不希望 z ' 的值變成零：

z ' = Persp0 · x + Persp1 · y + Persp2

因此，這些值有一些實際的限制： 

資料 `Persp2` 格可以是零或不是零。 如果 `Persp2` 是零，則 z ' 的點為零， (0，0) ，而且通常不需要，因為該點在二維圖形中很常見。 如果不 `Persp2` 等於零，則如果固定為1，則不會遺失任何程式 `Persp2` 。 例如，如果您判斷 `Persp2` 應該是5，則可以只將矩陣中的所有資料格除以5（ `Persp2` 等於1），而結果會相同。

基於這些原因， `Persp2` 通常會固定為1，也就是身分識別矩陣中的相同值。

一般來說， `Persp0` 和 `Persp1` 是小數位數。 例如，假設您開始使用身分識別矩陣，但設定 `Persp0` 為0.01：

<pre>
| 1  0   0.01 |
| 0  1    0   |
| 0  0    1   |
</pre>

轉換公式如下：

x ' = x/ (0.01 · x + 1) 

y ' = y/ (0.01 · x + 1) 

現在使用這種轉換來呈現位於原點的 100-圖元方塊。 以下是轉換四個角落的方式：

 (0、0) → (0、0) 

 (0、100) → (0、100) 

 (100、0) → (50、0) 

 (100、100) → (50、50) 

當 x 為100時，z 的分母為2，因此 x 和 y 座標實際上是減半。 方塊的右邊會比左邊更短：

![非仿射轉換的箱](non-affine-images/nonaffinetransform.png)

`Persp`這些儲存格名稱的一部分是指「透視圖」，因為透視量會建議方塊現在與檢視器的右側傾斜。

[ **測試透視圖** ] 頁面可讓您使用和的值進行實驗， `Persp0` `Pers1` 以瞭解其運作方式。 這些矩陣資料格的合理值很小，因為 `Slider` 通用 Windows 平臺無法正確地處理它們。 為了容納 UWP 問題，TestPerspective 中的兩個 `Slider` 元素 [**TestPerspective.xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml)必須初始化為範圍從–1到1：

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

程式碼後端檔案中滑杆的事件處理常式 [`TestPerspectivePage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TestPerspectivePage.xaml.cs) 會將值除以100，使其範圍介於-0.01 與0.01 之間。 此外，此函式會以點陣圖載入：

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

此 `PaintSurface` 處理常式會 `SKMatrix` `perspectiveMatrix` 根據這兩個滑杆的值除以100來計算名為的值。 這會結合兩個轉譯轉換，將這項轉換的中心置中為點陣圖的中心：

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

以下是一些範例映射：

[![[測試透視圖] 頁面的三重螢幕擷取畫面](non-affine-images/testperspective-small.png)](non-affine-images/testperspective-large.png#lightbox "[測試透視圖] 頁面的三重螢幕擷取畫面")

當您試驗滑杆時，您會發現值超過0.0066 或低於-0.0066 會導致映射突然被破壞並一致。 要轉換的點陣圖是 300-圖元的正方形。 它會相對於其中心進行轉換，因此點陣圖的座標範圍從–150到150。 回想一下 z ' 的值是：

z ' = Persp0 · x + Persp1 · y + 1

如果 `Persp0` 或 `Persp1` 大於0.0066 或低於-0.0066，則會永遠有一些點陣圖的座標，其會產生 z 的值為零。 這會導致除數為零，而轉譯會變得混亂。 使用非仿射轉換時，您會想要避免以零除的座標呈現任何的。

一般而言，您不會設定 `Persp0` `Persp1` 隔離。 通常也需要設定矩陣中的其他資料格，以達成某些類型的非仿射轉換。

其中一個這類非仿射轉換是 *錐度轉換* 。 這種類型的非仿射轉換會保留矩形的整體維度，但 tapers 一側：

![錐度轉換的箱](non-affine-images/tapertransform.png)

[`TaperTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransform.cs)類別會根據這些參數執行非仿射轉換的一般化計算：

- 要轉換之影像的矩形大小，
- 列舉，指出 tapers 之矩形的側邊。
- 另一個列舉，指出它的 tapers 方式，以及
- tapering 的範圍。

此程式碼如下：

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

這個類別是在 [ **錐度轉換** ] 頁面中使用。 XAML 檔案會具現化兩個專案 `Picker` 來選取列舉值，並具現化以選擇 `Slider` 錐度分數。 [`PaintSurface`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TaperTransformPage.xaml.cs#L55)處理常式會結合兩個轉換轉換與兩個轉譯轉換，使轉換相對於點陣圖左上角：

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

[![[錐度轉換] 頁面的三重螢幕擷取畫面](non-affine-images/tapertransform-small.png)](non-affine-images/tapertransform-large.png#lightbox "[錐度轉換] 頁面的三重螢幕擷取畫面")

另一種類型的一般化非仿射轉換是3D 旋轉，在下一篇文章（ [**3D 旋轉**](3d-rotation.md)）中示範。

非仿射轉換可以將矩形轉換成任何凸四邊形。 這是由 [ **顯示非仿射矩陣** ] 頁面所示範。 它非常類似 [**矩陣轉換**](matrix.md)發行項中的 **顯示仿射矩陣** 頁面，不同之處在于它有第四個 `TouchPoint` 物件可操作點陣圖的第四個角落：

[![顯示非仿射矩陣頁面的三重螢幕擷取畫面](non-affine-images/shownonaffinematrix-small.png)](non-affine-images/shownonaffinematrix-large.png#lightbox "顯示非仿射矩陣頁面的三重螢幕擷取畫面")

只要您不想要讓點陣圖的其中一個角落成為大於180度的邊角，或讓兩個邊彼此相交，程式就會使用此方法從類別成功計算轉換 [`ShowNonAffineMatrixPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowNonAffineMatrixPage.xaml.cs) ：

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

為了方便計算，這個方法會以三個不同轉換的乘積取得總轉換，這些轉換會以箭號來日後代表，顯示這些轉換如何修改點陣圖的四個角落：

 (0、0) → (0、0) → (0、0) → (x0、y0)  (左上) 

 (0、H) → (0、1) → (0、1) → (x1、y1)  (左下) 

 (W，0) → (1，0) → (1，0) → (x2，y2)  (右上方) 

 (W，H) → (1，1) → (a，b) → (x3，y3)  (右下角) 

右邊的最後一個座標是與四個觸控點相關聯的四個點。 這些是點陣圖角落的最終座標。

W 和 H 代表點陣圖的寬度和高度。 第一個轉換 `S` 只會將點陣圖調整為1圖元的正方形。 第二個轉換是非仿射轉換 `N` ，第三個則是仿射轉換 `A` 。 該仿射轉換是以三個點為基礎，因此它與先前的仿射方法相同， [`ComputeMatrix`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs#L68) 而且不牽涉到第四個數據列（ (a，b) 點）。

`a` `b` 系統會計算和值，讓第三個轉換成為仿射。 程式碼會取得仿射轉換的反向，然後使用它來對應右下角。 這就是 (a，b) 的點。

非仿射轉換的另一種用法是模仿三維圖形。 在下一篇文章中， [**3D 旋轉**](3d-rotation.md) 您會看到如何在3d 空間中旋轉二維圖形。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)