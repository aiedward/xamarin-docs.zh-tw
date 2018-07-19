---
title: SkiaSharp 中的矩陣轉換
description: 本文章探討到靈活的轉換矩陣中，使用 SkiaSharp 轉換，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: fafa883d013701b9e72e544aff03739a7ff9230c
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130864"
---
# <a name="matrix-transforms-in-skiasharp"></a>SkiaSharp 中的矩陣轉換

_更深入了解 SkiaSharp 轉換具有多樣化的轉換矩陣_

若要套用的所有轉換`SKCanvas`物件會合併成單一執行個體[ `SKMatrix` ](https://developer.xamarin.com/api/type/SkiaSharp.SKMatrix/)結構。 這是類似於所有新式的 2D 圖形系統中的標準 3-3 轉換矩陣。

如您所見，您可以使用轉換 SkiaSharp 中而不需要知道有關轉換矩陣，但轉換矩陣，請務必從理論性的觀點來看，並使用轉換來修改路徑時相當重要的或處理複雜的觸控輸入，這兩個這會示範這篇文章和下一步。

![](matrix-images/matrixtransformexample.png "點陣圖會受限於仿射轉換")

套用至目前的轉換矩陣`SKCanvas`使用，請隨時存取唯讀[ `TotalMatrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.TotalMatrix/)屬性。 您可以設定新的轉換矩陣 using [ `SetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.SetMatrix/p/SkiaSharp.SKMatrix/)方法，而且您可以還原該轉換矩陣為預設值呼叫[ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix/)。

只有其他`SKCanvas`直接搭配畫布的矩陣轉換的成員是[ `Concat` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Concat/p/SkiaSharp.SKMatrix@/)其中乘以它們一起串連兩個矩陣。

預設轉換矩陣是單位矩陣，而且包含 1 的對角線的儲存格和 0 其他位置中：

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

您可以建立身分識別矩陣使用靜態[ `SKMatrix.MakeIdentity` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeIdentity()/)方法：

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

`SKMatrix`預設建構函式未*不*傳回身分識別矩陣。 它會傳回所有設為零的資料格的矩陣。 請勿使用`SKMatrix`建構函式除非您打算以手動方式將這些資料格的設定。

當 SkiaSharp 呈現圖形的物件時，每個點 （x，y） 有效地轉換成 1-3 矩陣中第三個資料行 1:

<pre>
| x  y  1 |
</pre>

這個 1-3 矩陣代表三維點設為 1 的 Z 座標。 二維矩陣轉換需要三個維度中使用的原因，則需要有數學的原因 （稍後討論）。 您可以將為代表在 3D 座標系統中，但一定是 2D 平面上點這個 1-3 矩陣其中 Z 等於 1。

這個 1-3 矩陣會乘以轉換矩陣中，而且結果是呈現在畫布上的點：

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

使用標準的矩陣相乘，已轉換的點如下所示：

x' = x

y' = y

z' = 1

這是預設轉換。

當`Translate`上呼叫方法`SKCanvas`物件，`tx`並`ty`引數`Translate`方法變成中轉換矩陣的第三個資料列的前兩個資料格：

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

x' = x + tx

y' = y + ty

縮放比例會有預設值為 1。 當您呼叫`Scale`上的新方法`SKCanvas`物件，包含結果的轉換矩陣`sx`和`sy`對角線的儲存格內的引數：

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

轉換公式如下所示：

x' = sx ·x

y' = sy ·y

轉換矩陣之後呼叫`Skew`包含矩陣資料格相鄰的縮放因數中的兩個引數：

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

轉換公式如下：

x' = x + xSkew ·y

y' = ySkew ·x + y

若要呼叫`RotateDegrees`或`RotateRadians`α 的角度，位於轉換矩陣如下所示：

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

以下是轉換公式：

x' = cos(α) ·x 軸 sin(α) ·y

y' = sin(α) ·x 軸 cos(α) ·y

0 度 α 時，它會是身分識別矩陣。 180 度 α 時，位於轉換矩陣如下所示：

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

180 度旋轉，相當於水平翻轉物件和垂直，這也可以設定為-1 的縮放比例。

所有這些類型的轉換可分為*仿射*轉換。 仿射轉換永遠不會牽涉到矩陣中，會保持為 0、 0 和 1 的預設值的第三個資料行。 發行項[非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)討論非仿射轉換。

## <a name="matrix-multiplication"></a>矩陣乘法

使用轉換矩陣的一大優點是矩陣相乘，通常稱為 SkiaSharp 文件中可以取得複合轉換*串連*。 轉換相關的方法中的許多`SKCanvas`指的 「 前串連"或"pre-concat。 」 這是指順序很重要，因為無法交替矩陣相乘的乘。

比方說，針對文件[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/)方法會指出它"Pre-concats 目前與指定的轉譯矩陣 」 文件時為[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/)方法會指出它"Pre-concats 目前與指定的縮放矩陣。 」

這表示在方法呼叫所指定的轉換是乘數 （左方運算元），而且目前的轉換矩陣是被乘數 （右方運算元）。

假設`Translate`呼叫後面`Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

`Scale`轉換會乘以`Translate`複合的轉換矩陣的轉換：

<pre>
| sx   0   0 |   |  1   0   0 |   | sx   0   0 |
|  0  sy   0 | × |  0   1   0 | = |  0  sy   0 |
|  0   0   1 |   | tx  ty   1 |   | tx  ty   1 |
</pre>

`Scale` 無法在之前呼叫`Translate`如下所示：

```csharp
canvas.Scale(sx, sy);
canvas.Translate(tx, ty);
```

在此情況下，乘法運算的順序相反，，和縮放比例會有效地套用至轉譯因素：

<pre>
|  1   0   0 |   | sx   0   0 |   |  sx      0    0 |
|  0   1   0 | × |  0  sy   0 | = |   0     sy    0 |
| tx  ty   1 |   |  0   0   1 |   | tx·sx  ty·sy  1 |
</pre>

以下是`Scale`與樞紐分析點的方法：

```csharp
canvas.Scale(sx, sy, px, py);
```

這是相當於下列的翻譯和小數位數呼叫：

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

三個的轉換矩陣相乘以反向順序從方法出現在程式碼的方式：

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

### <a name="the-skmatrix-structure"></a>SKMatrix 結構

`SKMatrix`結構會定義九個讀取/寫入屬性的型別`float`對應的轉換矩陣的九個資料格：

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` 也會定義名為的屬性[ `Values` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix.Values/)型別的`float[]`。 這個屬性可用來設定或取得九個值的順序一次`ScaleX`， `SkewX`， `TransX`， `SkewY`， `ScaleY`， `TransY`， `Persp0`， `Persp1`，和`Persp2`。

`Persp0`， `Persp1`，並`Persp2`一文中討論資料格[非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。 如果這些資料格都有預設值 0、 0 和 1，轉換會乘以座標的點，就像這樣：

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

x' = ScaleX ·x + SkewX ·y + TransX

y' = SkewX ·x + ScaleY ·y + TransY

z' = 1

這是完整的二維仿射轉換。 仿射轉換會保留平行線，這表示，矩形會永遠不會轉換成的平行四邊形以外的任何項目。

`SKMatrix`結構會定義數種靜態方法，來建立`SKMatrix`值。 這些全都是傳回`SKMatrix`值：

- [`MakeTranslation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeTranslation/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/System.Single/System.Single/) 使用樞紐分析點
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/) 以弧度為單位的角度
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/System.Single/System.Single/) 以弧度為單位，與樞紐分析點的角度
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/)
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/System.Single/System.Single/) 使用樞紐分析點
- [`MakeSkew`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeSkew/p/System.Single/System.Single/)

`SKMatrix` 也會定義數個靜態方法，串連兩個矩陣，這表示若要將它們相乘。 這些方法的命名`Concat`， `PostConcat`，和`PreConcat`，而且有兩個版本的每個。 這些方法沒有任何傳回的值;相反地，它們會參考現有`SKMatrix`透過值`ref`引數。 在下列範例中， `A`， `B`，並`R`（適用於 「 結果 」） 都`SKMatrix`值。

這兩個`Concat`會呼叫方法，就像這樣：

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

這些物件可執行下列的乘法運算：

R = B × A

其他的方法有只有兩個參數。 第一個參數修改，以及從方法呼叫傳回時，包含兩個矩陣的乘積。 這兩個`PostConcat`會呼叫方法，就像這樣：

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

這些呼叫會執行下列作業：

A = B ×

這兩個`PreConcat`方法如下：

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

這些呼叫會執行下列作業：

A = B × A

所有這些方法呼叫的版本`ref`引數會稍微更有效地進行呼叫的基礎實作，但它可能會造成混淆給其他人讀取您的程式碼，並假設凡是`ref`引數修改方法。 此外，它通常很方便傳遞的引數是其中的結果`Make`方法，例如：

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

這是擴展轉換乘以平移轉換。 在此案例中，`SKMatrix`結構會提供名為的方法中的快顯[ `SetScaleTranslate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.SetScaleTranslate/p/System.Single/System.Single/System.Single/System.Single/):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

這是其中一個時安全地使用幾次`SKMatrix`建構函式。 `SetScaleTranslate`方法會設定所有的九個儲存格的矩陣。 它也會安全地使用`SKMatrix`建構函式以靜態`Rotate`和`RotateDegrees`方法：

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

這些方法進行*不*串連至現有的轉換的旋轉轉換。 方法會設定所有儲存格的矩陣。 它們是在功能上等同`MakeRotation`並`MakeRotationDegrees`方法不同之處在於它們不具現化`SKMatrix`值。

假設您有`SKPath`您想要顯示，但您希望它有稍有不同的方向或不同的中心點的物件。 您可以藉由呼叫來修改該路徑的所有座標[ `Transform` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Transform/p/SkiaSharp.SKMatrix/)方法`SKPath`使用`SKMatrix`引數。 **路徑轉換**頁面會示範如何執行這項操作。 [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs)類別的參考`HendecagramPath`但若要將轉換套用到該路徑使用其建構函式的欄位中的物件：

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

`HendecagramPath`物件具有的中心點在 （0，0） 和星號的十一個點向外擴充從該中心往所有方向的 100 個單位。 這表示路徑具有正數和負數的座標。 **路徑轉換**頁面搭配三次一樣大，星號與所有的正座標會優先。 此外，它不想要星號，點直接往上移一個的點。 它要改為一個點星形直視推點。 （星號有 11 個點，因為它不能兩者。）這需要 22 360 度旋轉星號除以。

建構函式會建置`SKMatrix`從三個不同的轉換使用的物件`PostConcat`方法，以下列模式，A、 B 和 C 是的執行個體`SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

這會是一系列的連續的乘法運算，因此結果如下所示：

× B × C

連續的乘法運算有助於了解每個轉換的功能。 縮放轉換的 3 倍增加路徑座標的大小，因此座標範圍從 –300 300。 旋轉轉換旋轉其來源周圍的星號。 平移轉換然後則會將其 300 像素右和向下，讓所有成為正數的座標。

有其他順序，產生相同的矩陣。 以下是另一個：

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

這第一次，旋轉中心的路徑，然後將它轉譯 100 像素為單位向右和向下所有座標都為正數。 星號會再增加相對於其新的左上角，為點 （0，0） 的大小。

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

[![](matrix-images/pathtransform-small.png "轉換路徑 頁面的三個螢幕擷取畫面")](matrix-images/pathtransform-large.png#lightbox "路徑轉換 頁面的三個螢幕擷取畫面")

此程式的建構函式適用於矩陣的路徑，使用下列呼叫：

```csharp
transformedPath.Transform(matrix);
```

路徑並未*不*保留此矩陣做為屬性。 相反地，它將轉換套用至所有路徑的座標。 如果`Transform`呼叫一次，同樣地，套用轉換，而您可以移回的唯一方法是藉由套用另一個復原轉換的矩陣。 幸好`SKMatrix`結構會定義[ `TryInverse` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.TryInvert/p/SkiaSharp.SKMatrix/)取得矩陣的方法會回復指定的矩陣：

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

方法會呼叫`TryInverse`因為並非所有的矩陣可反轉的但不可反轉的矩陣不太可能用於圖形轉換。

您也可以套用至矩陣轉換`SKPoint`值、 點陣列`SKRect`，或甚至只是單一數字內您的程式。 `SKMatrix`結構支援與一字開頭的方法的集合，這些作業`Map`，這類：

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

如果您使用的最後一個方法，請記住，`SKRect`結構不是可代表旋轉的矩形。 此方法才有意義`SKMatrix`值表示轉譯和調整。

### <a name="interactive-experimentation"></a>互動式的測試

若要瞭解仿射轉換的方法之一是藉由以互動方式移動在畫面的點陣圖的三個角及檢視哪些轉換結果。 這是背後的構想**顯示仿射矩陣**頁面。 此頁面需要兩個其他示範中也會使用其他類別：

[ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchPoint.cs)類別會顯示您可以拖曳畫面的半透明圓形。 `TouchPoint` 要求`SKCanvasView`或元素的父系`SKCanvasView`有[ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/TouchEffect.cs)附加。 將 `Capture` 屬性設定為 `true`。 在 `TouchAction`事件處理常式，程式必須呼叫`ProcessTouchEvent`方法中的`TouchPoint`每個`TouchPoint`執行個體。 此方法會傳回`true`如果觸控事件導致移動觸控點。 此外，`PaintSurface`處理常式必須呼叫`Paint`方法，在每個`TouchPoint`執行個體，傳遞給它`SKCanvas`物件。

`TouchPoint` 示範共通方式 SkiaSharp 視覺效果，可以封裝在個別的類別。 類別可以定義用於指定的視覺效果中，特性的屬性和方法命名為`Paint`與`SKCanvas`引數可以轉譯它。

`Center`屬性`TouchPoint`表示物件的位置。 這個屬性可以設定為初始化的位置;當使用者拖曳到畫布四周的圓圈，就會變更屬性。

**顯示網頁的仿射矩陣**也需要[ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/MatrixDisplay.cs)類別。 此類別會顯示的儲存格`SKMatrix`物件。 它有兩個公用方法：`Measure`若要取得的轉譯矩陣中，維度和`Paint`來顯示它。 此類別包含`MatrixPaint`型別的屬性`SKPaint`，可以取代為不同的字型大小或色彩。

[ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml)檔案會具現化`SKCanvasView`並附加`TouchEffect`。 [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs)程式碼後置檔案會建立三個`TouchPoint`物件，然後將它們設定為對應的點陣圖，它會載入從內嵌的三個邊角的位置資源：

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

非仿射矩陣是唯一定義由三個點。 三個`TouchPoint`物件對應至點陣圖的左上角、 右上方和左下角邊角。 由於仿射矩陣只是能夠轉換平行四邊形的矩形，其他三個被隱含的第四個點。 藉由呼叫的建構函式結束`ComputeMatrix`，計算的資料格`SKMatrix`從這些三個點的物件。

`TouchAction`處理常式會呼叫`ProcessTouchEvent`方法，每個`TouchPoint`。 `scale`值將從 Xamarin.Forms 座標轉換為像素：

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

如果有的話`TouchPoint`已移動，則此方法會呼叫`ComputeMatrix`再次和介面失效。

`ComputeMatrix`方法會判斷這些三個點所隱含的矩陣。 矩陣名`A`轉換為一個像素正方形的矩形，變成平行四邊形根據三個點，而呼叫的縮放轉換`S`調整要為一個像素的正方形矩形的點陣圖。 複合矩陣`S`× `A`:

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

最後，`PaintSurface`方法，可呈現該矩陣為基礎的點陣圖，螢幕的底部顯示矩陣並在呈現觸控點的三個角落的點陣圖：

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

當第一次載入頁面，而其他兩個畫面所顯示的是它在某些操作之後，下方的 [iOS] 畫面會顯示點陣圖：

[![](matrix-images/showaffinematrix-small.png "顯示仿射矩陣頁面的三個螢幕擷取畫面")](matrix-images/showaffinematrix-large.png#lightbox "顯示仿射矩陣頁面的三個螢幕擷取畫面")

雖然它看起來好像觸控點拖曳點陣圖的邊角，這只是假象。 從觸控點計算的矩陣轉換點陣圖，以便與觸控點一致的邊角。

這是更自然的使用者來移動、 調整大小，和旋轉點陣圖不是藉由拖曳邊角中，但使用一或兩根手指直接物件上拖曳，縮小，且旋轉。 這在下一篇文章中涵蓋[觸控的操作](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)。

### <a name="the-reason-for-the-3-by-3-matrix"></a>原因 3-3 矩陣

可能會預期二維圖形系統會要求僅 2-2 的轉換矩陣：

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

這適用於調整、 旋轉和甚至扭曲，但不支援的最基本的轉換，也就是轉譯。

問題是 2-2 矩陣表示*線性*轉換兩個維度。 線性轉換會保留一些基本的算術運算，但其中一個影響是線性的轉換永遠不會改變的點 （0，0）。 線性轉換可讓您無法轉譯。

在三維空間中，線性轉換矩陣，看起來像這樣：

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

標示的資料格`SkewXY`值會扭曲 Y 的值為基礎的 X 座標的方式; 資料格中`SkewXZ`表示值會扭曲的 X 座標，Z; 的值為基礎，而且其他同樣的值會扭曲`Skew`資料格。

很可能限於二維平面中的這個 3D 轉換矩陣，藉由設定`SkewZX`並`SkewZY`為 0，及`ScaleZ`為 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

如果二維圖形繪製完全在其中 Z 等於 1 的 3D 空間中的平面上，轉換乘法看起來像這樣：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

一切保持在二維平面上其中 Z 等於 1，但`SkewXZ`和`SkewYZ`資料格實際改成二維轉譯因素。

這是三維的線性轉換做為二維的非線性變形的方式。 （比方說，3D 圖形中的轉換所根據的 4 乘 4 矩陣。）

`SKMatrix` SkiaSharp 中的結構會定義該第三個資料列的屬性：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

非零值的`Persp0`和`Persp1`導致移動關閉二維平面物件，其中 Z 等於 1 的轉換。 這些物件會移回該平面時，會發生什麼事上一文中涵蓋[非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
