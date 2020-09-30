---
title: SkiaSharp 中的矩陣轉換
description: 本文使用多功能的轉換矩陣更深入地探討 SkiaSharp 的轉換，並以範例程式碼示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: davidbritch
ms.author: dabritch
ms.date: 04/12/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 911365b6293fecd3bf309f3e61d9b232d90b7a13
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556551"
---
# <a name="matrix-transforms-in-skiasharp"></a>SkiaSharp 中的矩陣轉換

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用多功能轉換矩陣深入探索 SkiaSharp 轉換_

套用至物件的所有轉換 `SKCanvas` 都會合並到結構的單一實例中 [`SKMatrix`](xref:SkiaSharp.SKMatrix) 。 這是標準的 3 x 3 轉換矩陣，類似于所有新式2D 圖形系統中的矩陣。

如您所見，您可以在 SkiaSharp 中使用轉換，而不需要知道轉換矩陣，但轉換矩陣很重要，因為轉換矩陣很重要，而是使用轉換來修改路徑或處理複雜的觸控輸入時，這兩種方式都是在本文和下一節中示範。

![與仿射轉換的點陣圖](matrix-images/matrixtransformexample.png)

目前套用至的轉換矩陣 `SKCanvas` 可透過存取唯讀屬性隨時取得 [`TotalMatrix`](xref:SkiaSharp.SKCanvas.TotalMatrix) 。 您可以使用方法來設定新的轉換矩陣 [`SetMatrix`](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) ，也可以藉由呼叫，將該轉換矩陣還原為預設值 [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) 。

`SKCanvas`直接與畫布的矩陣轉換搭配使用的另一個成員是將 [`Concat`](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) 兩個矩陣串連在一起，以串連兩個矩陣。

預設的轉換矩陣是身分識別矩陣，由1的對角線儲存格和0的其他地方所組成：

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

您可以使用靜態方法來建立身分識別矩陣  [`SKMatrix.MakeIdentity`](xref:SkiaSharp.SKMatrix.MakeIdentity) ：

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

預設的函 `SKMatrix` 式 *不* 會傳回識別矩陣。 它會傳回所有資料格都設為零的矩陣。 `SKMatrix`除非您打算手動設定這些儲存格，否則請勿使用此函數。

當 SkiaSharp 轉譯繪圖物件時， (x，y) 的每個點實際上都會轉換成第三個數據行中有1個的 1 x 3 個矩陣：

<pre>
| x  y  1 |
</pre>

這個 1 x 3 矩陣代表 Z 座標設定為1的三維點。  (稍後討論的數學原因，) 二維矩陣轉換需要三個維度的運作方式。 您可以將這個 1 x 3 的矩陣想像為代表3D 座標系統中的點，但一律在2D 平面上，Z 等於1。

然後再乘以轉換矩陣，結果就會是在畫布上轉譯的點：

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

使用標準矩陣乘法時，轉換的點如下所示：

`x' = x`

`y' = y`

`z' = 1`

這是預設轉換。

在 `Translate` 物件上呼叫方法時 `SKCanvas` ， `tx` 方法的和 `ty` 引數 `Translate` 會變成轉換矩陣第三個數據列中的前兩個數據格：

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

相乘現在如下所示：

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

以下是轉換公式：

`x' = x + tx`

`y' = y + ty`

調整因數的預設值為1。 當您 `Scale` 在新的物件上呼叫方法時 `SKCanvas` ，結果轉換矩陣會包含 `sx` `sy` 對角線儲存格中的和引數：

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

轉換公式如下所示：

`x' = sx · x`

`y' = sy · y`

在呼叫後的轉換矩陣 `Skew` 包含矩陣資料格中的兩個引數（與縮放比例相鄰）：

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

轉換公式如下：

`x' = x + xSkew · y`

`y' = ySkew · x + y`

針對 `RotateDegrees` 或 `RotateRadians` α角度的呼叫，轉換矩陣如下所示：

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

以下是轉換公式：

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

當α為0度時，即為識別矩陣。 當α為180度時，轉換矩陣如下所示：

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

180度旋轉相當於水準和垂直翻轉物件，也可以藉由設定-1 的縮放因數來達成。

所有這些類型的轉換都分類為 *仿射* 轉換。 仿射轉換絕不會涉及矩陣的第三個數據行，此資料行會維持在預設值0、0和1。 [**非仿射轉換**](non-affine.md)文章討論非仿射轉換。

## <a name="matrix-multiplication"></a>矩陣乘法

使用轉換矩陣的優點之一，就是可以透過矩陣乘法取得複合轉換，這在 SkiaSharp 檔中通常稱為「 *串連*」。 中許多轉換相關的方法都是 `SKCanvas` 指「預先串連」或「預先連接」。 這指的是乘法的順序，這一點很重要，因為矩陣乘法不是可交換的。

例如，方法的檔顯示「 [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) 使用指定的翻譯預先 concats 目前的矩陣」，而該方法的檔 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) 指出它「預先 concats 目前的矩陣並具有指定的小數位數」。

這表示，方法呼叫所指定的轉換是左邊運算元 (乘數) 而且目前的轉換矩陣是右邊運算元) 的被乘數 (。

假設 `Translate` 呼叫後接著 `Scale` ：

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

`Scale`轉換會乘以 `Translate` 複合轉換矩陣的轉換：

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` 您可以先呼叫，就 `Translate` 像這樣：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

在這種情況下，會反轉乘法的順序，而且調整因數會有效地套用至轉譯因素：

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

以下是 `Scale` 具有 pivot 點的方法：

```csharp
canvas.Scale(sx, sy, px, py);
```

這相當於下列轉譯和小數位數的呼叫：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

三個轉換矩陣會以反向順序乘以方法在程式碼中的顯示方式：

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>SKMatrix 結構

`SKMatrix`結構會定義類型的九個讀取/寫入屬性， `float` 對應至轉換矩陣的九個數據格：

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` 也會定義名為的屬性，其 [`Values`](xref:SkiaSharp.SKMatrix.Values) 類型為 `float[]` 。 您可以使用這個屬性來設定或取得其中九個值，順序為、、、、、、、 `ScaleX` `SkewX` `TransX` `SkewY` `ScaleY` `TransY` `Persp0` `Persp1` 和 `Persp2` 。

`Persp0`、 `Persp1` 和資料 `Persp2` 格會在[**非仿射轉換**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)文章中討論。 如果這些資料格的預設值為0、0和1，則轉換會乘以如下的座標點：

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

這是完整的二維仿射轉換。 仿射轉換會保留平行線，這表示矩形絕不會轉換成平行四邊形以外的任何類型。

`SKMatrix`結構會定義用來建立值的數個靜態方法 `SKMatrix` 。 這些會傳回 `SKMatrix` 值：

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single)) 使用 pivot 點
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single)) 以弧度為單位的角度
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single)) 以弧度為單位的角度（含 pivot 點）
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single)) 使用 pivot 點
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix` 也會定義數個串連兩個矩陣的靜態方法，這表示要將它們相乘。 這些方法會命名為 [`Concat`](xref:SkiaSharp.SKMatrix.Concat*) 、 [`PostConcat`](xref:SkiaSharp.SKMatrix.PostConcat*) 和 [`PreConcat`](xref:SkiaSharp.SKMatrix.PreConcat*) ，且每個都有兩個版本。 這些方法沒有傳回值;相反地，它們會 `SKMatrix` 透過 `ref` 引數參考現有的值。 在下列範例中， `A` `B` `R` "result" ) 的、和 (全都是 `SKMatrix` 值。

這兩個 `Concat` 方法的呼叫方式如下：

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

這些會執行下列乘法：

`R = B × A`

其他方法只有兩個參數。 第一個參數會被修改，而且在從方法呼叫傳回時，會包含兩個矩陣的乘積。 這兩個 `PostConcat` 方法的呼叫方式如下：

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

這些呼叫會執行下列作業：

`A = A × B`

這兩種 `PreConcat` 方法很類似：

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

這些呼叫會執行下列作業：

`A = B × A`

使用所有引數的這些方法的版本 `ref` 在呼叫基礎執行時稍微更有效率，但在讀取您的程式碼時，可能會對其他人造成混淆，並假設 `ref` 該方法已修改具有引數的任何程式碼。 此外，傳遞做為其中一個方法結果的引數通常很方便 `Make` ，例如：

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

這會建立下列矩陣：

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

這是縮放轉換乘以「轉譯」轉換。 在這個特殊情況下， `SKMatrix` 結構會提供一個快捷方式，其中包含名為的方法 [`SetScaleTranslate`](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)) ：

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

這是可安全使用此函式的幾次 `SKMatrix` 。 `SetScaleTranslate`方法會設定矩陣的所有九個數據格。 使用函式搭配 `SKMatrix` 靜態和方法也是安全的 `Rotate` `RotateDegrees` ：

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

這些方法 *不會* 將旋轉轉換串連至現有的轉換。 方法會設定矩陣的所有儲存格。 它們的功能與 `MakeRotation` 和方法相同， `MakeRotationDegrees` 不同之處在于它們不會具現化 `SKMatrix` 值。

假設您有一個 `SKPath` 您想要顯示的物件，但是您希望它有稍微不同的方向或不同的中心點。 您可以 [`Transform`](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) `SKPath` 使用引數呼叫的方法，以修改該路徑的所有座標 `SKMatrix` 。 [ **路徑轉換** ] 頁面會示範如何這樣做。 [`PathTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs)類別會參考 `HendecagramPath` 欄位中的物件，但會使用它的函式將轉換套用至該路徑：

```csharp
public class PathTransformPage : ContentPage
{
    SKPath transformedPath = HendecagramArrayPage.HendecagramPath;

    public PathTransformPage()
    {
        Title = "Path Transform";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        SKMatrix matrix = SKMatrix.MakeScale(3, 3);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(360f / 22));
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(300, 300));

        transformedPath.Transform(matrix);
    }
    ...
}
```

`HendecagramPath`物件的中心位於 (0、0) ，而星形的11點是以所有方向的100單位向外擴充。 這表示路徑具有正面和負座標。 [ **路徑轉換** ] 頁面偏好使用星號三次，並使用所有正面座標。 此外，它也不會想要有一個星狀的點指出。 它想要改為在星星的某個點上向下指向。  (，因為星形有11點，所以不能同時有這兩者。 ) 這需要將星號乘以360度除以22。

此函式 `SKMatrix` 會使用具有下列模式的方法，從三個不同的轉換建立物件 `PostConcat` ，其中 A、B 和 C 是實例 `SKMatrix` ：

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

這是一系列連續的乘法運算，因此結果如下所示：

`A × B × C`

連續的乘法運算有助於瞭解每個轉換的用途。 調整規模轉換會將路徑座標的大小增加為3，因此座標範圍從–300到300。 旋轉轉換會圍繞其原點旋轉星形。 然後轉譯轉換會向右和向下移動300圖元，讓所有座標變成正面。

還有其他順序會產生相同的矩陣。 以下是另一個：

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

這會先旋轉其中心的路徑，然後將其向右和向下轉譯為100圖元，如此一來，所有座標都是正面的。 然後星號的大小會相對於其左上角，也就是 (0，0) 的點。

`PaintSurface`處理常式可以直接轉譯此路徑：

```csharp
public class PathTransformPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Magenta;
            paint.StrokeWidth = 5;

            canvas.DrawPath(transformedPath, paint);
        }
    }
}

```

它會顯示在畫布的左上角：

[![[路徑轉換] 頁面的三重螢幕擷取畫面](matrix-images/pathtransform-small.png)](matrix-images/pathtransform-large.png#lightbox "[路徑轉換] 頁面的三重螢幕擷取畫面")

此程式的函式會使用下列呼叫，將矩陣套用至路徑：

```csharp
transformedPath.Transform(matrix);
```

路徑不會將此 *矩陣保留為* 屬性。 相反地，它會將轉換套用至路徑的所有座標。 如果 `Transform` 再次呼叫，就會再次套用轉換，而您可以返回的唯一方法是套用另一個可復原轉換的矩陣。 幸運的 `SKMatrix` 是，結構會定義一個 [`TryInvert`](xref:SkiaSharp.SKMatrix.TryInvert*) 方法，以取得可反轉指定矩陣的矩陣：

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

呼叫方法是 `TryInverse` 因為並非所有矩陣都能反轉，但是非反轉矩陣不太可能用於圖形轉換。

您也可以將矩陣轉換套用至 `SKPoint` 值、點陣列、 `SKRect` ，甚至是程式內的單一數位。 `SKMatrix`結構使用以單字開頭的方法集合來支援這些作業 `Map` ，如下所示：

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

如果您使用最後一個方法，請記住 `SKRect` 結構無法表示旋轉的矩形。 方法只對 `SKMatrix` 表示轉譯和縮放的值有意義。

## <a name="interactive-experimentation"></a>互動式實驗

有一種方式可讓您輕鬆地在螢幕上移動點陣圖的三個角落，並查看轉換的結果。 這是 [ **顯示仿射矩陣** ] 頁面背後的概念。 此頁面需要其他兩個也用於其他示範的類別：

[`TouchPoint`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs)類別會顯示可以在螢幕上拖曳的半透明圓形。 `TouchPoint` 要求的 `SKCanvasView` 或的父系 `SKCanvasView` 必須附加的或元素 [`TouchEffect`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) 。 將 `Capture` 屬性設定為 `true`。 在 `TouchAction` 事件處理常式中，程式必須 `ProcessTouchEvent` `TouchPoint` 針對每個實例呼叫中的方法 `TouchPoint` 。 `true`如果觸控事件造成觸控點移動，方法會傳回。 此外， `PaintSurface` 處理常式必須 `Paint` 在每個實例中呼叫方法，並將 `TouchPoint` 它傳遞給 `SKCanvas` 物件。

`TouchPoint` 示範 SkiaSharp 視覺效果可以封裝在不同類別中的常見方式。 類別可以定義屬性來指定視覺效果的特性，而以引數命名的方法則 `Paint` `SKCanvas` 可以呈現。

的 `Center` 屬性會 `TouchPoint` 指出物件的位置。 這個屬性可以設定為初始化位置;當使用者在畫布周圍拖曳圓圈時，屬性就會變更。

**顯示仿射矩陣頁面**也需要 [`MatrixDisplay`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) 類別。 這個類別會顯示物件的儲存格 `SKMatrix` 。 它有兩個公用方法： `Measure` 取得轉譯矩陣的維度，以及 `Paint` 顯示它。 類別包含 `MatrixPaint` `SKPaint` 可取代為不同字型大小或色彩的型別屬性（property）。

[**ShowAffineMatrixPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml)檔案具現化， `SKCanvasView` 並附加 `TouchEffect` 。 [**ShowAffineMatrixPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs)程式碼後置檔案會建立三個 `TouchPoint` 物件，然後將它們設定為對應至從內嵌資源載入之點陣圖三個角落的位置：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    SKMatrix matrix;
    SKBitmap bitmap;
    SKSize bitmapSize;

    TouchPoint[] touchPoints = new TouchPoint[3];

    MatrixDisplay matrixDisplay = new MatrixDisplay();

    public ShowAffineMatrixPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        touchPoints[0] = new TouchPoint(100, 100);                  // upper-left corner
        touchPoints[1] = new TouchPoint(bitmap.Width + 100, 100);   // upper-right corner
        touchPoints[2] = new TouchPoint(100, bitmap.Height + 100);  // lower-left corner

        bitmapSize = new SKSize(bitmap.Width, bitmap.Height);
        matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                           touchPoints[1].Center,
                                           touchPoints[2].Center);
    }
    ...
}
```

仿射矩陣是由三個點唯一定義。 這三個 `TouchPoint` 物件對應至點陣圖左上角、右上角和左下角。 因為仿射矩陣只能夠將矩形轉換成平行四邊形，所以第四個點是由其他三個點所隱含。 此函式會結束，並呼叫 `ComputeMatrix` ，其會 `SKMatrix` 從這三個點計算物件的儲存格。

`TouchAction`處理常式會呼叫 `ProcessTouchEvent` 每個的方法 `TouchPoint` 。 `scale`值會從 Xamarin.Forms 座標轉換成圖元：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        bool touchPointMoved = false;

        foreach (TouchPoint touchPoint in touchPoints)
        {
            float scale = canvasView.CanvasSize.Width / (float)canvasView.Width;
            SKPoint point = new SKPoint(scale * (float)args.Location.X,
                                        scale * (float)args.Location.Y);
            touchPointMoved |= touchPoint.ProcessTouchEvent(args.Id, args.Type, point);
        }

        if (touchPointMoved)
        {
            matrix = ComputeMatrix(bitmapSize, touchPoints[0].Center,
                                               touchPoints[1].Center,
                                               touchPoints[2].Center);
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果有任何 `TouchPoint` 已移動的，則方法會 `ComputeMatrix` 再次呼叫並使介面失效。

`ComputeMatrix`方法會決定這三個點所隱含的矩陣。 所謂的矩陣會 `A` 根據三個點將一個圖元的正方形轉換成平行四邊形，而縮放轉換則會將 `S` 點陣圖縮放為一個圖元的正方形。 複合矩陣是 `S` × `A` ：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    static SKMatrix ComputeMatrix(SKSize size, SKPoint ptUL, SKPoint ptUR, SKPoint ptLL)
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

        SKMatrix result = SKMatrix.MakeIdentity();
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

最後，此 `PaintSurface` 方法會根據該矩陣轉譯點陣圖、在畫面底部顯示矩陣，並在點陣圖的三個角落呈現觸控點：

```csharp
public partial class ShowAffineMatrixPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap using the matrix
        canvas.Save();
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(matrix);

        matrixDisplay.Paint(canvas, matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));

        // Display the touchpoints
        foreach (TouchPoint touchPoint in touchPoints)
        {
            touchPoint.Paint(canvas);
        }
    }
  }
```

以下的 iOS 畫面會在第一次載入頁面時顯示點陣圖，而其他兩個畫面則在進行某些操作之後顯示該點陣圖：

[![顯示仿射矩陣頁面的三重螢幕擷取畫面](matrix-images/showaffinematrix-small.png)](matrix-images/showaffinematrix-large.png#lightbox "顯示仿射矩陣頁面的三重螢幕擷取畫面")

雖然觸控點也是拖曳點陣圖角落的，但這只是一種假像。 從觸控點計算的矩陣會轉換點陣圖，使角落與觸控點一致。

當使用者移動、調整大小和旋轉點陣圖時，不需要拖曳角落，而是直接在物件上使用一或兩個手指來拖曳、縮小和旋轉，是更自然的方式。 這涵蓋于下一篇文章 [**觸控操作**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)中。

## <a name="the-reason-for-the-3-by-3-matrix"></a>3 x 3 矩陣的原因

二維圖形系統可能只需要一個2對2的轉換矩陣：

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

這適用于調整大小、旋轉甚至扭曲，但不能是最基本的轉換，也就是轉譯。

問題在於，2 x 2 矩陣代表兩個維度中的 *線性* 轉換。 線性轉換會保留一些基本的算數運算，但其中一個含意是線性轉換絕不會改變 (0，0) 的點。 線性轉換不可能進行轉譯。

在三個維度中，線性轉換矩陣看起來像這樣：

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

標示的 `SkewXY` 資料格表示值會根據 Y 的值扭曲 x 座標; 儲存格 `SkewXZ` 表示值會根據 Z 的值扭曲 x 座標，而值會與其他資料 `Skew` 格類似。

您可以藉由將和設定為0，將此3D 轉換矩陣限制為二維平面 `SkewZX` `SkewZY` ，並將設定 `ScaleZ` 為1：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

如果二維圖形完全繪製在3D 空間中，Z 等於1的平面上，則轉換乘法如下所示：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

所有專案都會保持在二維平面上，其中 Z 等於1，但是 `SkewXZ` 和 `SkewYZ` 儲存格實際上會變成二維轉譯因素。

這是三維線性轉換做為二維非線性轉換的方式。  (，則3D 圖形中的轉換是以4到4矩陣為基礎。 ) 

`SKMatrix`SkiaSharp 中的結構會定義第三個數據列的屬性：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

的非零值 `Persp0` 和 `Persp1` 會導致轉換，將物件從 Z 等於1的二維平面移動。 當這些物件移回該平面時，會在 [**非仿射轉換**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)的文章中討論。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)