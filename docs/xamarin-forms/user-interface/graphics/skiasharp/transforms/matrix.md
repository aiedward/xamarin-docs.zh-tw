---
title： "SkiaSharp 中的矩陣轉換" 描述： "這篇文章探討更深入探討具有多功能轉換矩陣的 SkiaSharp 轉換，並使用範例程式碼示範這項功能。"
skiasharp assetid： 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4 author： davidbritch ms. 作者： dabritch ms。日期：04/12/2017 不是-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="matrix-transforms-in-skiasharp"></a>SkiaSharp 中的矩陣轉換

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用多功能轉換矩陣深入探索 SkiaSharp 轉換_

所有套用至物件的轉換 `SKCanvas` 都會合並在結構的單一實例中 [`SKMatrix`](xref:SkiaSharp.SKMatrix) 。 這是標準的3到3轉換矩陣，與所有新式2D 圖形系統中的類似。

如您所見，您可以在 SkiaSharp 中使用轉換，而不需要知道轉換矩陣，但轉換矩陣對理論的觀點很重要，而且當使用轉換來修改路徑或處理複雜的觸控輸入時，這兩個專案都是在本文和下一個中示範。

![](matrix-images/matrixtransformexample.png "A bitmap subjected to an affine transform")

套用至的目前轉換矩陣隨時 `SKCanvas` 都可存取唯讀 [`TotalMatrix`](xref:SkiaSharp.SKCanvas.TotalMatrix) 屬性。 您可以使用方法來設定新的轉換矩陣 [`SetMatrix`](xref:SkiaSharp.SKCanvas.SetMatrix(SkiaSharp.SKMatrix)) ，而且可以藉由呼叫，將該轉換矩陣還原為預設值 [`ResetMatrix`](xref:SkiaSharp.SKCanvas.ResetMatrix) 。

只有 `SKCanvas` 直接與畫布的矩陣轉換搭配使用的另一個成員，就是將 [`Concat`](xref:SkiaSharp.SKCanvas.Concat(SkiaSharp.SKMatrix@)) 兩個矩陣結合在一起。

預設的轉換矩陣是身分識別矩陣，由1的在對角線資料格中，而0則是其他位置：

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

您可以使用靜態方法來建立身分識別矩陣 [`SKMatrix.MakeIdentity`](xref:SkiaSharp.SKMatrix.MakeIdentity) ：

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

`SKMatrix`預設的構造函式不會傳回識別矩陣。 *not* 它會傳回將所有資料格設定為零的矩陣。 `SKMatrix`除非您打算手動設定這些儲存格，否則請不要使用此函數。

當 SkiaSharp 轉譯繪圖物件時，每個點（x，y）會有效地轉換成第三個數據行中具有1的 1 x 3 矩陣：

<pre>
| x  y  1 |
</pre>

這個1到3的矩陣代表3d 座標設定為1的三維點。 有一些數學原因（稍後會討論）二維矩陣轉換需要在三個維度中運作。 您可以將這個1到3的矩陣視為3D 座標系統中的某個點，但一定會在2D 平面上，Z 等於1。

這個1到3的矩陣接著會乘以轉換矩陣，而結果會是在畫布上呈現的點：

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

使用標準矩陣乘法，轉換的點如下所示：

`x' = x`

`y' = y`

`z' = 1`

這是預設轉換。

在 `Translate` 物件上呼叫方法時 `SKCanvas` ， `tx` 方法的和 `ty` 引數 `Translate` 會成為轉換矩陣第三個數據列中的前兩個數據格：

<pre>
|  1   0   0 |
|  0   1   0 |
| tx  ty   1 |
</pre>

乘法現在如下所示：

<pre>
              |  1   0   0 |
| x  y  1 | × |  0   1   0 | = | x'  y'  z' |
              | tx  ty   1 |
</pre>

以下是轉換公式：

`x' = x + tx`

`y' = y + ty`

縮放因數的預設值為1。 當您 `Scale` 在新的物件上呼叫方法時 `SKCanvas` ，結果轉換矩陣會包含 `sx` 對角線資料格中的和 `sy` 引數：

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

轉換公式如下所示：

`x' = sx · x`

`y' = sy · y`

呼叫之後的轉換矩陣會 `Skew` 包含矩陣資料格中的兩個引數（與縮放因數相鄰）：

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

轉換公式為：

`x' = x + xSkew · y`

`y' = ySkew · x + y`

對於 `RotateDegrees` `RotateRadians` α角度的或的呼叫，轉換矩陣如下所示：

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

以下是轉換公式：

`x' = cos(α) · x - sin(α) · y`

`y' = sin(α) · x - cos(α) · y`

當α為0度時，即為身分識別矩陣。 當α為180度時，轉換矩陣如下所示：

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

180度旋轉相當於水準和垂直翻轉物件，也就是設定-1 的縮放因數來完成。

所有這些類型的轉換都會分類為*仿射*轉換。 仿射轉換絕不會牽涉到矩陣的第三個數據行，它會保留在預設值0、0和1。 [**非仿射轉換**](non-affine.md)一文會討論非仿射轉換。

## <a name="matrix-multiplication"></a>矩陣乘法

使用轉換矩陣的其中一個重要優點是，可以透過矩陣乘法來取得複合轉換，這在 SkiaSharp 檔中通常稱為「*串連*」。 中的許多轉換相關方法都 `SKCanvas` 參考「預先串連」或「預先連接」。 這是指乘法的順序，這一點很重要，因為矩陣乘法不是可交換的。

例如，方法的檔 [`Translate`](xref:SkiaSharp.SKCanvas.Translate(System.Single,System.Single)) 指出它「預先 concats 具有指定翻譯的目前矩陣」，而方法的檔則指出「已 [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) 預先 concats 具有指定之刻度的目前矩陣」。

這表示方法呼叫所指定的轉換是乘數（左運算元），而目前的轉換矩陣則是被乘數（右運算元）。

假設 `Translate` 會呼叫，後面接著 `Scale` ：

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

`Scale`在此之前，您可以呼叫， `Translate` 如下所示：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

在此情況下，乘法的順序會反轉，而縮放比例會有效地套用到轉譯因素：

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

以下是 `Scale` 具有 pivot 點的方法：

```csharp
canvas.Scale(sx, sy, px, py);
```

這相當於下列的轉譯和調整呼叫：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

這三個轉換矩陣會以反向順序乘以方法在程式碼中的顯示方式：

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

## <a name="the-skmatrix-structure"></a>SKMatrix 結構

`SKMatrix`結構會定義類型的九個讀取/寫入屬性，其 `float` 對應于轉換矩陣的九個數據格：

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix`也會定義名為且 [`Values`](xref:SkiaSharp.SKMatrix.Values) 類型為的屬性 `float[]` 。 這個屬性可以用來以下列順序設定或取得一次中的九個值：、、、、、、 `ScaleX` `SkewX` `TransX` `SkewY` `ScaleY` `TransY` `Persp0` `Persp1` 和 `Persp2` 。

`Persp0`、 `Persp1` 和資料 `Persp2` 格會在[**非仿射轉換一**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)文中討論。 如果這些儲存格的預設值為0、0和1，則轉換會乘以如下的座標點：

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

`x' = ScaleX · x + SkewX · y + TransX`

`y' = SkewX · x + ScaleY · y + TransY`

`z' = 1`

這是完整的二維仿射轉換。 「仿射」轉換會保留平行行，這表示矩形絕不會轉換成平行四邊形以外的任何專案。

`SKMatrix`結構會定義數個用來建立值的靜態方法 `SKMatrix` 。 這些全都傳回的 `SKMatrix` 值：

- [`MakeTranslation`](xref:SkiaSharp.SKMatrix.MakeTranslation(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single))
- [`MakeScale`](xref:SkiaSharp.SKMatrix.MakeScale(System.Single,System.Single,System.Single,System.Single))使用資料透視點
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single))以弧度為單位的角度
- [`MakeRotation`](xref:SkiaSharp.SKMatrix.MakeRotation(System.Single,System.Single,System.Single))以弧度為單位的角度與 pivot 點
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single))
- [`MakeRotationDegrees`](xref:SkiaSharp.SKMatrix.MakeRotationDegrees(System.Single,System.Single,System.Single))使用資料透視點
- [`MakeSkew`](xref:SkiaSharp.SKMatrix.MakeSkew(System.Single,System.Single))

`SKMatrix`也會定義數個用來串連兩個矩陣的靜態方法，這表示會將它們相乘。 這些方法的名稱為 [`Concat`](xref:SkiaSharp.SKMatrix.Concat*) 、 [`PostConcat`](xref:SkiaSharp.SKMatrix.PostConcat*) 和 [`PreConcat`](xref:SkiaSharp.SKMatrix.PreConcat*) ，而且每個都有兩個版本。 這些方法沒有傳回值;相反地，它們會 `SKMatrix` 透過引數來參考現有 `ref` 的值。 在下列範例中， `A` 、 `B` 和 `R` （適用于 "result"）都是所有 `SKMatrix` 值。

這兩種 `Concat` 方法的呼叫方式如下所示：

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

這些會執行下列乘法運算：

`R = B × A`

其他方法只有兩個參數。 第一個參數已修改，且從方法呼叫傳回時，會包含兩個矩陣的乘積。 這兩種 `PostConcat` 方法的呼叫方式如下所示：

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

使用所有引數的這些方法的版本 `ref` 在呼叫基礎的執行方面稍有效率，但可能會使讀取您程式碼的人感到困惑，並假設 `ref` 方法修改了任何具有引數的專案。 此外，傳遞做為其中一個方法結果的引數通常很方便，例如 `Make` ：

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

這是尺規轉換乘以轉譯轉換。 在此特殊情況下， `SKMatrix` 結構會提供具有名為之方法的快捷方式 [`SetScaleTranslate`](xref:SkiaSharp.SKMatrix.SetScaleTranslate(System.Single,System.Single,System.Single,System.Single)) ：

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

這是使用此程式安全的幾次 `SKMatrix` 。 `SetScaleTranslate`方法會設定矩陣的所有九個數據格。 您也可以安全地使用 `SKMatrix` 具有靜態和方法的函式 `Rotate` `RotateDegrees` ：

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

這些方法*不會*將旋轉轉換串連到現有的轉換。 方法會設定矩陣的所有儲存格。 它們的功能與 `MakeRotation` 和方法相同， `MakeRotationDegrees` 不同之處在于它們不會具現化 `SKMatrix` 值。

假設您有 `SKPath` 想要顯示的物件，但您希望它的方向不同，或是不同的中心點。 您可以藉由呼叫 [`Transform`](xref:SkiaSharp.SKPath.Transform(SkiaSharp.SKMatrix)) `SKPath` 具有引數的方法，修改該路徑的所有座標 `SKMatrix` 。 [**路徑轉換**] 頁面會示範如何執行此動作。 [`PathTransform`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs)類別會參考 `HendecagramPath` 欄位中的物件，但會使用其函式，將轉換套用至該路徑：

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

`HendecagramPath`物件的中心為（0，0），而星形的11個點從該中心向外延展到所有方向的100個單位。 這表示路徑具有正和負座標。 [**路徑轉換**] 頁面偏好使用星號三次，並使用所有正座標。 此外，它也不希望星形的某個點往上單點。 它想要改為將星形的某個點指向正向下。 （因為星形有11個點，所以不能同時有這兩者）。這需要將星星旋轉360度除以22。

此函式 `SKMatrix` 會使用方法搭配下列模式，從三個不同的轉換建立物件 `PostConcat` ，其中 A、B 和 C 都是的實例 `SKMatrix` ：

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

這是一系列連續的乘法運算，因此結果如下所示：

`A × B × C`

連續的乘法運算有助於瞭解每個轉換的功能。 尺規轉換會將路徑座標的大小增加為3的因數，因此座標的範圍是從–300到300。 旋轉轉換會在其原點周圍旋轉星形。 然後，轉譯轉換會將它向右或向下移動300圖元，讓所有座標變成正面。

還有其他序列會產生相同的矩陣。 以下是另一個：

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

這會先旋轉其中心的路徑，然後將它轉譯為向右和向下100圖元，讓所有座標都是正數。 然後，星號會以相對於其左上角的大小增加，也就是點（0，0）。

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

它會出現在畫布的左上角：

[![](matrix-images/pathtransform-small.png "Triple screenshot of the Path Transform page")](matrix-images/pathtransform-large.png#lightbox "Triple screenshot of the Path Transform page")

此程式的函式會將矩陣套用至具有下列呼叫的路徑：

```csharp
transformedPath.Transform(matrix);
```

此*路徑不會保留此*矩陣做為屬性。 相反地，它會將轉換套用到路徑的所有座標。 如果 `Transform` 再次呼叫，則會再次套用轉換，而您可以返回的唯一方式是套用另一個會復原轉換的矩陣。 幸運的是， `SKMatrix` 結構 [`TryInvert`](xref:SkiaSharp.SKMatrix.TryInvert*) 會定義方法，以取得反轉指定矩陣的矩陣：

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

系統會呼叫方法 `TryInverse` ，因為並非所有的矩陣都可反轉，但無法反轉的矩陣不太可能用於圖形轉換。

您也可以將矩陣轉換套用至 `SKPoint` 值、點陣列、 `SKRect` ，甚至是程式內的單一數位。 `SKMatrix`結構支援這些作業，其中包含以一字開頭的方法集合 `Map` ，如下所示：

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

如果您使用最後一個方法，請記住， `SKRect` 結構不能代表旋轉的矩形。 方法僅適用于 `SKMatrix` 代表轉譯和縮放比例的值。

## <a name="interactive-experimentation"></a>互動式實驗

有一種方式可以讓仿射轉型轉換，方法是以互動方式在螢幕上移動點陣圖的三個角落，並查看轉換結果。 這是 [**顯示仿射矩陣**] 頁面背後的概念。 此頁面需要其他兩個也用於其他示範的類別：

[`TouchPoint`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs)類別會顯示可以拖曳至螢幕周圍的半透明圓形。 `TouchPoint`需要 `SKCanvasView` 具有附加之父系的或元素 `SKCanvasView` [`TouchEffect`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs) 。 將 `Capture` 屬性設為 `true`。 在 `TouchAction` 事件處理常式中，程式必須 `ProcessTouchEvent` 在中 `TouchPoint` 針對每個實例呼叫方法 `TouchPoint` 。 `true`如果觸控事件導致觸控點移動，此方法會傳回。 此外， `PaintSurface` 處理常式必須 `Paint` 在每個實例中呼叫方法 `TouchPoint` ，並將它傳遞至 `SKCanvas` 物件。

`TouchPoint`示範 SkiaSharp 視覺效果可以封裝在不同類別中的常見方式。 類別可以定義用來指定視覺效果特性的屬性，而且 `Paint` 以引數命名的方法 `SKCanvas` 可以呈現它。

的 `Center` 屬性會 `TouchPoint` 指出物件的位置。 此屬性可以設定為初始化位置;當使用者拖曳畫布周圍的圓形時，屬性會變更。

[**顯示仿射矩陣] 頁面**也需要 [`MatrixDisplay`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs) 類別。 這個類別會顯示物件的儲存格 `SKMatrix` 。 它有兩個公用方法： `Measure` 取得轉譯矩陣的維度，並 `Paint` 顯示它。 類別包含類型的 `MatrixPaint` 屬性 `SKPaint` ，可以取代為不同的字型大小或色彩。

[**ShowAffineMatrixPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml)會具現化 `SKCanvasView` ，並附加 `TouchEffect` 。 [**ShowAffineMatrixPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs)程式碼後置檔案會建立三個 `TouchPoint` 物件，然後將它們設定為對應到從內嵌資源載入之點陣圖的三個角落的位置：

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

仿射矩陣是由三個點唯一定義。 這三個 `TouchPoint` 物件會對應到點陣圖的左上角、右上方和左下角。 因為仿射矩陣只能夠將矩形轉換成平行四邊形，所以第四個點是由其他三個所隱含。 此函式會使用呼叫來結束 `ComputeMatrix` ，其會 `SKMatrix` 從這三個點計算物件的儲存格。

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

如果 `TouchPoint` 有任何移動，則方法會 `ComputeMatrix` 再次呼叫並使介面失效。

`ComputeMatrix`方法會決定這三個點所隱含的矩陣。 矩陣稱為會 `A` 根據三個點，將一個圖元的方形矩形轉換成平行四邊形，而縮放轉換會將 `S` 點陣圖調整為一個圖元的方形矩形。 複合矩陣是 `S` × `A` ：

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

最後， `PaintSurface` 方法會根據該矩陣呈現點陣圖、在畫面底部顯示矩陣，然後將觸控點呈現在點陣圖的三個角落：

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

下列的 iOS 畫面會顯示第一次載入頁面時的點陣圖，而其他兩個畫面則會在某些操作之後顯示它：

[![](matrix-images/showaffinematrix-small.png "Triple screenshot of the Show Affine Matrix page")](matrix-images/showaffinematrix-large.png#lightbox "Triple screenshot of the Show Affine Matrix page")

雖然觸控點只會拖曳點陣圖的角落，但這只是一種假像。 從觸控點計算的矩陣會轉換點陣圖，讓角落與觸控點一致。

使用者可以更自然地移動、調整大小和旋轉點陣圖，而不是拖曳角落，而是直接在物件上使用一或兩個手指來拖曳、縮小和旋轉。 這會在下一篇文章[**觸控操作**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)中討論。

## <a name="the-reason-for-the-3-by-3-matrix"></a>3到3個矩陣的原因

可能預期二維圖形系統只需要2到2個轉換矩陣：

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

這適用于縮放、旋轉，甚至是扭曲，但它無法支援最基本的轉換，也就是轉譯。

問題在於 2 x 2 矩陣代表兩個維度中的*線性*轉換。 線性轉換會保留一些基本的算數運算，但其中一項含意是線性轉換永遠不會改變點（0，0）。 線性轉換不可能進行轉譯。

在三個維度中，線性轉換矩陣看起來像這樣：

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

標示的 `SkewXY` 資料格表示值會根據 Y 的值來扭曲 x 座標; 資料格 `SkewXZ` 表示值會根據 Z 值來扭曲 x 座標，而值則會與其他儲存格的偏差類似 `Skew` 。

將和設定為 `SkewZX` 0，並將設為1，可以將此3d 轉換矩陣限制為二維平面 `SkewZY` `ScaleZ` ：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

如果二維圖形完全在3D 空間的平面上繪製，其中 Z 等於1，則轉換乘法看起來會像這樣：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

所有專案都會停留在 Z 等於1的二維平面上，但 `SkewXZ` 和資料 `SkewYZ` 格實際上會變成二維轉譯因數。

這是三維線性轉換如何做為二維非線性轉換。 （透過比喻，3D 圖形中的轉換是以4到4的矩陣為基礎）。

`SKMatrix`SkiaSharp 中的結構會定義第三個數據列的屬性：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

和的非零值 `Persp0` `Persp1` 會導致轉換，將物件從 Z 等於1的二維平面移出。 當這些物件移回該平面時，會在[**非仿射轉換的相關**](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)文章中加以討論。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
