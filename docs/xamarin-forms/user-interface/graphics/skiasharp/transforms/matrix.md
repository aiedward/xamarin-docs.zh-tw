---
title: "矩陣轉換"
description: "深入 SkiaSharp 多用途的轉換矩陣的轉換"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 9EDED6A0-F0BF-4471-A9EF-E0D6C5954AE4
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 9d5e65abe675ded48e9239f2cd10ceed4a7c3a52
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="matrix-transforms"></a>矩陣轉換

_深入 SkiaSharp 多用途的轉換矩陣的轉換_

套用至所有轉換`SKCanvas`物件中的單一執行個體將會合併[ `SKMatrix` ](https://developer.xamarin.com/api/type/SkiaSharp.SKMatrix/)結構。 這是類似於所有現代化 2D 圖形系統的標準 3-3 轉換矩陣。

如您所見，您可以使用轉換 SkiaSharp 中而不需要知道有關轉換矩陣，但轉換矩陣，請務必理論的觀點而言，以及修改路徑使用轉型時，它會非常重要，或處理複雜的觸控輸入，這兩個這將示範本文和下一步。

![](matrix-images/matrixtransformexample.png "要以仿射轉換點陣圖")

套用至目前的轉換矩陣`SKCanvas`可隨時藉由存取唯讀[ `TotalMatrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKCanvas.TotalMatrix/)屬性。 您可以設定新的轉換矩陣使用[ `SetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.SetMatrix/p/SkiaSharp.SKMatrix/)方法，而且您可以還原該轉換矩陣為預設值呼叫[ `ResetMatrix` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.ResetMatrix/)。

只有其他`SKCanvas`直接搭配畫布的矩陣轉換的成員是[ `Concat` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Concat/p/SkiaSharp.SKMatrix@/)的串連兩個矩陣一起相乘它們。

預設轉換矩陣是單位矩陣，而且包含 1 的對角線儲存格和 0 其他位置中：

<pre>
| 1  0  0 |
| 0  1  0 |
| 0  0  1 |
</pre>

您可以建立使用靜態身分識別矩陣[ `SKMatrix.MakeIdentity` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeIdentity()/)方法：

```csharp
SKMatrix matrix = SKMatrix.MakeIdentity();
```

`SKMatrix`預設建構函式會*不*傳回身分識別矩陣。 它會傳回所有設為零的資料格的矩陣。 請勿使用`SKMatrix`建構函式除非您計劃手動設定這些資料格。

當 SkiaSharp 轉譯圖形化的物件時，每個點 （x，y） 有效地轉換至 1-3 矩陣中的第三個資料行 1:

<pre>
| x  y  1 |
</pre>

這個 1-3 矩陣代表三維點設為 1 的 Z 座標。 二維矩陣轉換需要使用三個維度的原因有數學考量 （稍後再討論）。 您可以將做為表示在 3D 座標系統中，但一定 2D 平面上點這個 1-3 矩陣 Z 等於 1。

這個 1-3 的矩陣會乘以轉換矩陣，其結果呈現在畫布上的點：

<pre>
              | 1  0  0 |
| x  y  1 | × | 0  1  0 | = | x'  y'  z' |
              | 0  0  1 |
</pre>

使用標準的矩陣乘法，已轉換的點如下：

x' = x

y' = y

z' = 1

這是預設轉換。

當`Translate`上呼叫方法`SKCanvas`物件`tx`和`ty`引數`Translate`方法成為轉換矩陣的第三個資料列中的前兩個資料格：

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

縮放比例會有預設值是 1。 當您呼叫`Scale`新方法`SKCanvas`物件時，結果的轉換矩陣包含`sx`和`sy`對角線儲存格中的引數：

<pre>
              | sx   0   0 |
| x  y  1 | × |  0  sy   0 | = | x'  y'  z' |
              |  0   0   1 |
</pre>

轉換公式如下所示：

x' = sx ·x

y' = sy ·y

轉換矩陣之後呼叫`Skew`包含矩陣資料格中縮放比例相鄰的兩個引數：

<pre>
              │   1   ySkew   0 │
| x  y  1 | × │ xSkew   1     0 │ = | x'  y'  z' |
              │   0     0     1 │
</pre>

轉換公式是：

x' = x + xSkew · y

y' = ySkew ·x + y

若要呼叫的`RotateDegrees`或`RotateRadians`α 的角度，轉換矩陣如下所示：

<pre>
              │  cos(α)  sin(α)  0 │
| x  y  1 | × │ –sin(α)  cos(α)  0 │ = | x'  y'  z' |
              │    0       0     1 │
</pre>

以下是轉換公式：

x' = cos(α) · x - sin(α) · y

y' = sin(α) · x - cos(α) · y

0 度 α 時，它會為識別矩陣。 Α 是 180 度，轉換矩陣時，如下所示：

<pre>
| –1   0   0 |
|  0  –1   0 |
|  0   0   1 |
</pre>

180 度旋轉，相當於水平翻轉物件平和垂直方式，這也可以設定為 – 1 的縮放比例。

所有這些類型的轉換就會歸類為*仿射*轉換。 仿射轉換永遠不會牽涉到矩陣中，會保持為 0，0 和 1 的預設值的第三個資料行。 發行項[非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)討論非仿射轉換。

## <a name="matrix-multiplication"></a>矩陣乘法

使用轉換的矩陣一個很大的好處是可以取得是矩陣乘法，通常指為 SkiaSharp 文件中的複合轉換*串連*。 中的轉換相關方法的多個`SKCanvas`參考 」 前串連"或"pre concat"。 這是指順序很重要，因為矩陣乘法不可以交替的乘法。

例如，文件[ `Translate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Translate/p/System.Single/System.Single/)方法指出它"Pre-concats 目前與指定的轉譯矩陣 」 文件時的[ `Scale` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.Scale/p/System.Single/System.Single/)方法會指出它"Pre-concats 目前與指定的縮放矩陣。 」

這表示在方法呼叫所指定的轉換是乘數 （左方運算元），而且目前的轉換矩陣是被乘數 （右方運算元）。

假設`Translate`稱為後面`Scale`:

```csharp
canvas.Translate(tx, ty);
canvas.Scale(sx, sy);
```

`Scale`轉換乘以`Translate`複合的轉換矩陣的轉換：

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

在此情況下，相乘的順序相反的因此縮放比例會有效地套用至轉譯因素：

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

三個的轉換矩陣會以反向順序相乘，從方法是如何出現在程式碼：

<pre>
|  1    0   0 |   | sx   0   0 |   |  1   0  0 |   |    sx         0     0 |
|  0    1   0 | × |  0  sy   0 | × |  0   1  0 | = |     0        sy     0 |
| –px  –py  1 |   |  0   0   1 |   | px  py  1 |   | px–px·sx  py–py·sy  1 |
</pre>

### <a name="the-skmatrix-structure"></a>SKMatrix 結構

`SKMatrix`結構會定義九個讀取/寫入屬性的型別`float`轉換矩陣的九個儲存格對應：

<pre>
│ ScaleX  SkewY   Persp0 │
│ SkewX   ScaleY  Persp1 │
│ TransX  TransY  Persp2 │
</pre>

`SKMatrix` 也會定義名為的屬性[ `Values` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix.Values/)型別的`float[]`。 這個屬性可以用來設定或取得的九個值的順序一次完成`ScaleX`， `SkewX`， `TransX`， `SkewY`， `ScaleY`， `TransY`， `Persp0`， `Persp1`，和`Persp2`。

`Persp0`， `Persp1`，和`Persp2`文章中討論的資料格[非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。 如果這些資料格都有預設值為 0，0，且 1，轉換會乘以座標的點，就像這樣：

<pre>
              │ ScaleX  SkewY   0 │
| x  y  1 | × │ SkewX   ScaleY  0 │ = | x'  y'  z' |
              │ TransX  TransY  1 │
</pre>

x' = ScaleX ·x + SkewX ·y + TransX

y' = SkewX ·x + ScaleY ·y + TransY

z' = 1

這是完整的二維仿射轉換。 仿射轉換會保留平行線，這表示，矩形會永遠不會轉換成平行四邊形以外的任何項目。

`SKMatrix`結構會定義數個靜態方法，以建立`SKMatrix`值。 這些全都是傳回`SKMatrix`值：

- [`MakeTranslation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeTranslation/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/)
- [`MakeScale`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeScale/p/System.Single/System.Single/System.Single/System.Single/) 使用樞紐分析點
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/) 以弧度為單位的角度
- [`MakeRotation`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotation/p/System.Single/System.Single/System.Single/) 以弧度為單位的樞紐分析點與角度
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/)
- [`MakeRotationDegrees`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeRotationDegrees/p/System.Single/System.Single/System.Single/) 使用樞紐分析點
- [`MakeSkew`](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.MakeSkew/p/System.Single/System.Single/)

`SKMatrix` 也會定義數種靜態方法，串連兩個矩陣，也就是將它們相乘。 這些方法的命名`Concat`， `PostConcat`，和`PreConcat`，並有兩種版本。 這些方法會有任何傳回的值;相反地，它們參考現有`SKMatrix`值透過`ref`引數。 在下列範例中， `A`， `B`，和`R`（適用於 「 結果 」） 都`SKMatrix`值。

這兩個`Concat`呼叫的方法如下：

```csharp
SKMatrix.Concat(ref R, A, B);

SKMatrix.Concat(ref R, ref A, ref B);
```

這些執行下列乘法：

R = B × A

其他方法有兩個參數。 第一個參數修改，以及從方法呼叫傳回，內含兩個矩陣的乘積。 這兩個`PostConcat`呼叫的方法如下：

```csharp
SKMatrix.PostConcat(ref A, B);

SKMatrix.PostConcat(ref A, ref B);
```

這些呼叫會執行下列作業：

A = A × B

這兩個`PreConcat`方法如下：

```csharp
SKMatrix.PreConcat(ref A, B);

SKMatrix.PreConcat(ref A, ref B);
```

這些呼叫會執行下列作業：

A = B × A

所有這些方法呼叫的版本`ref`引數會稍微更有效地進行呼叫的基礎實作，但它可能會造成混淆給其他人讀取您的程式碼，並假設任何項目與`ref`引數修改方法。 此外，它通常很方便傳遞的引數，其中的結果`Make`方法，例如：

```csharp
SKMatrix result;
SKMatrix.Concat(result, SKMatrix.MakeTranslation(100, 100),
                        SKMatrix.MakeScale(3, 3));
```

這會建立下列的矩陣：

<pre>
│   3    0  0 │
│   0    3  0 │
│ 100  100  1 │
</pre>

這是標尺轉換乘以轉換轉換。 在本個案`SKMatrix`結構提供方法，名為捷徑[ `SetScaleTranslate` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.SetScaleTranslate/p/System.Single/System.Single/System.Single/System.Single/):

```csharp
SKMatrix R = new SKMatrix();
R.SetScaleTranslate(3, 3, 100, 100);
```

這是其中幾次何時可安全使用`SKMatrix`建構函式。 `SetScaleTranslate`方法會設定所有的九個儲存格的矩陣。 它也是安全使用`SKMatrix`具有靜態建構函式`Rotate`和`RotateDegrees`方法：

```csharp
SKMatrix R = new SKMatrix();

SKMatrix.Rotate(ref R, radians);

SKMatrix.Rotate(ref R, radians, px, py);

SKMatrix.RotateDegrees(ref R, degrees);

SKMatrix.RotateDegrees(ref R, degrees, px, py);
```

這些方法都會執行*不*串連旋轉轉換至現有的轉換。 方法會將矩陣的所有資料格。 它們是在功能上等於`MakeRotation`和`MakeRotationDegrees`方法不同之處在於它們不具現化`SKMatrix`值。

假設您有`SKPath`您想要顯示，但您想使用它有稍有不同的方向或不同的中心點的物件。 您可以藉由呼叫修改該路徑的所有座標[ `Transform` ](https://developer.xamarin.com/api/member/SkiaSharp.SKPath.Transform/p/SkiaSharp.SKMatrix/)方法`SKPath`與`SKMatrix`引數。 **路徑轉換**頁面會示範如何執行這項操作。 [ `PathTransform` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/PathTransformPage.cs)類別參考`HendecagramPath`但套用至該路徑的轉換使用其建構函式的欄位中的物件：

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

`HendecagramPath`物件已經在中心 （0，0） 和星號的十一個點，向外擴充從 100 單位所有方向的中心。 這表示路徑的正面和負面的座標。 **路徑轉換**頁面慣用工作與星狀三次一樣大，以及所有座標為正數。 此外，它不想星形可直接註冊點某的一點。 它要改為一個點的星狀往點。 （星號有 11 個點，因為它不能有兩者。）這需要 360 度旋轉星形除以 22。

建構函式建置`SKMatrix`物件從三個不同的轉換使用`PostConcat`具有下列模式中，A、 B 和 C 是的執行個體方法`SKMatrix`:

```csharp
SKMatrix matrix = A;
SKMatrix.PostConcat(ref A, B);
SKMatrix.PostConcat(ref A, C);
```

這是一系列的連續的乘積，所以結果為，如下所示：

A × B × C

連續乘法有助於了解每個轉換的功能。 小數位數轉換 3 倍增加路徑座標的大小，讓座標的範圍從 –300 到 300。 旋轉轉換旋轉周圍其原來的星號。 轉換轉換然後則會將其 300 像素為單位右和向下鍵，讓所有變成正數的座標。

有其他會產生相同的矩陣的順序。 以下是另一項：

```csharp
SKMatrix matrix = SKMatrix.MakeRotationDegrees(360f / 22);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeTranslation(100, 100));
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(3, 3));
```

這第一次，旋轉中心的路徑，然後將它轉譯 100 像素為單位，向右和向下，因此所有座標都為正數。 星狀然後會在相對於其新的左上角，為點 （0，0） 的大小增加。

`PaintSurface`處理常式可以直接轉譯這個路徑：

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

它會出現在畫布左上角：

[![](matrix-images/pathtransform-small.png "[路徑轉換] 頁面上的三個螢幕擷取畫面")](matrix-images/pathtransform-large.png#lightbox "路徑轉換頁面的三個螢幕擷取畫面")

此程式的建構函式適用於矩陣的路徑與下列呼叫：

```csharp
transformedPath.Transform(matrix);
```

路徑並未*不*保留此矩陣做為屬性。 相反地，它將轉換套用至所有路徑的座標。 如果`Transform`呼叫一次，同樣地，套用轉換，您可以移回的唯一方式是藉由套用另一個復原轉換的矩陣。 幸運的是，`SKMatrix`結構會定義[ `TryInverse` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix.TryInvert/p/SkiaSharp.SKMatrix@/)取得矩陣的方法會回復指定的矩陣：

```csharp
SKMatrix inverse;
bool success = matrix.TryInverse(out inverse);
```

此方法會呼叫`TryInverse`因為並非所有的矩陣是可反轉的但非可反轉的矩陣可能不會用於圖形轉換。

您也可以套用至矩陣轉換`SKPoint`值、 點，陣列`SKRect`，或甚至只是單一數字程式內。 `SKMatrix`結構支援具有字開頭的方法的集合，這些作業`Map`，這類：

```csharp
SKPoint transformedPoint = matrix.MapPoint(point);

SKPoint transformedPoint = matrix.MapPoint(x, y);

SKPoint[] transformedPoints = matrix.MapPoints(pointArray);

float transformedValue = matrix.MapRadius(floatValue);

SKRect transformedRect = matrix.MapRect(rect);
```

如果您使用的最後一個方法，請注意，`SKRect`結構不能代表旋轉的矩形。 這個方法才有意義`SKMatrix`代表轉譯和縮放比例的值。

### <a name="interactive-experimentation"></a>互動式試驗

仿射轉換的操作有初步的一種方式是以互動方式移動螢幕上的點陣圖的三個角，並查看哪些轉換結果。 這是背後的概念**顯示仿射矩陣**頁面。 此頁面需要兩個其他的示範中也會使用其他類別：

[ `TouchPoint` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/TouchPoint.cs)類別會顯示可以拖曳出來，放在螢幕上的半透明圓形。 `TouchPoint` 要求`SKCanvasView`或為其父系的項目`SKCanvasView`有[ `TouchEffect` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/TouchEffect.cs)附加。 將 `Capture` 屬性設定為 `true`。 在`TouchAction`事件處理常式，程式必須呼叫`ProcessTouchEvent`方法中的`TouchPoint`每個`TouchPoint`執行個體。 方法會傳回`true`如果觸控事件導致移動觸控點。 此外，`PaintSurface`處理常式必須呼叫`Paint`中每個方法`TouchPoint`執行個體，傳遞給它來`SKCanvas`物件。

`TouchPoint` 示範一般方式 SkiaSharp 視覺效果，可以封裝在個別的類別。 類別可以定義來指定特性的視覺效果上，屬性和方法命名為`Paint`與`SKCanvas`引數可以轉譯它。

`Center`屬性`TouchPoint`表示物件的位置。 這個屬性可以設定為初始化的位置。當使用者拖曳到畫布四周的圓圈屬性變更。

**仿射矩陣網頁顯示**也需要[ `MatrixDisplay` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/MatrixDisplay.cs)類別。 這個類別會顯示的資料格`SKMatrix`物件。 它有兩個公用方法：`Measure`取得的轉譯矩陣中，維度和`Paint`加以顯示。 這個類別包含`MatrixPaint`型別的屬性`SKPaint`，可以在不同的字型的大小或色彩取代。

[ **ShowAffineMatrixPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml)檔案會具現化`SKCanvasView`並附加`TouchEffect`。 [ **ShowAffineMatrixPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/ShowAffineMatrixPage.xaml.cs)程式碼後置檔案會建立三個`TouchPoint`物件，然後將它們設定為對應至載入從內嵌點陣圖的三個角的位置資源：

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

仿射矩陣唯一是由三個點定義。 三個`TouchPoint`物件對應至點陣圖的左上角、 右上角和左下角。 因為只能夠將矩形轉換成平行四邊形仿射矩陣，其他三個被隱含的第四個點。 建構函式結束時，呼叫`ComputeMatrix`，它會計算的資料格`SKMatrix`從這些三個點的物件。

`TouchAction`處理常式呼叫`ProcessTouchEvent`方法的每個`TouchPoint`。 `scale`值將從 Xamarin.Forms 座標轉換為像素為單位：

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

如果有任何`TouchPoint`已移動，則方法會呼叫`ComputeMatrix`再次和介面失效。

`ComputeMatrix`方法會判斷這些三個點所隱含的矩陣。 矩陣呼叫`A`轉換為一個像素正方形矩形變成平行四邊形根據三個點，而呼叫的小數位數轉換`S`縮放至一個像素正方形矩形的點陣圖。 複合矩陣是`S`× `A`:

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

        SKMatrix result;
        SKMatrix.Concat(ref result, A, S);
        return result;
    }
    ...
}
```

最後，`PaintSurface`方法呈現該矩陣為依據的點陣圖，在螢幕的底部顯示矩陣並呈現點陣圖的三個角落觸控點：

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

第一次載入的頁面，而其他兩個螢幕所顯示的是它後一些操作時，下方的 [iOS] 畫面會顯示點陣圖：

[![](matrix-images/showaffinematrix-small.png "三個螢幕擷取畫面顯示仿射矩陣頁面的")](matrix-images/showaffinematrix-large.png#lightbox "顯示仿射矩陣頁面的三個螢幕擷取畫面")

雖然似乎觸控點如同拖曳邊角的點陣圖，是只視覺效果。 從觸控點計算出來的矩陣轉換點陣圖，以便與觸控點一致的邊角。

更自然的使用者來移動、 調整大小，和旋轉點陣圖不是藉由拖曳邊角中，但使用一個或兩個指直接拖曳時，在物件上縮小，和旋轉。 這在下一個發行項中說明[觸控操作](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/touch.md)。

### <a name="the-reason-for-the-3-by-3-matrix"></a>原因 3-3 矩陣

可能預期二維圖形系統需要 2-2 的轉換矩陣：

<pre>
           │ ScaleX  SkewY  │
| x  y | × │                │ = | x'  y' |
           │ SkewX   ScaleY │
</pre>

這適用於縮放、 旋轉和甚至扭曲，但不支援的最基本的轉換，也就是轉譯。

問題是 2-2 矩陣代表*線性*轉換中兩個維度。 線性轉換會保留一些基本的算術運算，但有一個的含意線性轉換永遠不會改變點 （0，0）。 線性轉換可讓您無法轉譯。

三個維度，在線性轉換矩陣看起來像這樣：

<pre>
              │ ScaleX  SkewYX  SkewZX │
| x  y  z | × │ SkewXY  ScaleY  SkewZY │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ  ScaleZ │
</pre>

標示的資料格`SkewXY`值誤差的 X 座標，根據的 Y 值的方式; 資料格`SkewXZ`表示值扭曲 Z; 的值為基礎的 X 座標，以及其他同樣的值會扭曲`Skew`資料格。

您可設定以限制此 3D 轉換矩陣到二維平面`SkewZX`和`SkewZY`為 0，和`ScaleZ`設為 1:

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  z | × │ SkewXY  ScaleY   0 │ = | x'  y'  z' |
              │ SkewXZ  SkewYZ   1 │
</pre>

如果完全在 Z 等於 1 的 3D 空間中平面上繪製的二維圖形、 轉換乘法看起來像這樣：

<pre>
              │ ScaleX  SkewYX   0 │
| x  y  1 | × │ SkewXY  ScaleY   0 │ = | x'  y'  1 |
              │ SkewXZ  SkewYZ   1 │
</pre>

一切保持二維平面上 Z 等於 1，但`SkewXZ`和`SkewYZ`資料格實際改成二維轉譯因素。

這是三維的線性轉換做為二維的非線性轉換的方式。 （比喻，3D 圖形中的轉換會依據 4-4 矩陣）。

`SKMatrix` SkiaSharp 中的結構會定義該第三個資料列的屬性：

<pre>
              │ ScaleX  SkewY   Persp0 │
| x  y  1 | × │ SkewX   ScaleY  Persp1 │ = | x'  y'  z` |
              │ TransX  TransY  Persp2 │
</pre>

非零值`Persp0`和`Persp1`導致移動物件超出二維平面 Z 等於 1 的轉換。 這些物件會移回該平面時會發生什麼事在文件中涵蓋[非仿射轉換](~/xamarin-forms/user-interface/graphics/skiasharp/transforms/non-affine.md)。


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
