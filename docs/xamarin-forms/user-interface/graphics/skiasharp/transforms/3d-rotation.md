---
title: SkiaSharp 中的3D 旋轉
description: 本文說明如何使用非仿射轉換來旋轉3D 空間中的2D 物件，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dfc7ec9b029cedf5584590e17565069bbf83ed2a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373168"
---
# <a name="3d-rotations-in-skiasharp"></a>SkiaSharp 中的3D 旋轉

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用非仿射轉換來旋轉3D 空間中的2D 物件。_

非仿射轉換的一個常見應用，是在3D 空間中模擬2D 物件的旋轉：

![在3D 空間中旋轉的文字字串](3d-rotation-images/3drotationsexample.png)

這項作業牽涉到使用三維度的旋轉，然後衍生 `SKMatrix` 執行這些3d 旋轉的非仿射轉換。

開發這項轉換很難 `SKMatrix` 在兩個維度內運作。 當這個 3 x 3 的矩陣衍生自3D 圖形中所使用的 4 x 4 矩陣時，作業變得更容易。 SkiaSharp 包含 [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) 這個用途的類別，但需要3d 圖形中的部分背景，才能瞭解3d 旋轉和4四轉換矩陣。

三維座標系統會新增第三個軸，稱為 Z。在概念上，Z 軸是在螢幕的右角。 3D 空間中的座標點會以三個數字表示： (x、y、z) 。 在本文所使用的3D 座標系統中，將 X 的值增加到右邊，並增加 Y 的值（就像兩個維度一樣）。 從螢幕中增加正 Z 值。 原點是左上角，就像在2D 圖形中一樣。 您可以將螢幕視為 XY 平面，將 Z 軸的角度與此平面對齊。

這稱為左方座標系統。 如果您將左邊的食指指向右邊的 X 座標 (朝右) ，而您的中間手指 (向下) ，則您的拇指會指向遞增的 Z 座標方向，並從螢幕向上擴充。

在3D 圖形中，轉換是以4到4矩陣為基礎。 以下是4對4的識別矩陣：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

在使用4四矩陣時，使用資料列和資料行編號來識別資料格是很方便的：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

不過，SkiaSharp `Matrix44` 類別有點不同。 在中設定或取得個別資料格值的唯一方法 `SKMatrix44` 是使用 [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) 索引子。 資料列和資料行索引是以零為基底，而不是以1為基底，而且資料列和資料行會交換。 上圖中的儲存格 M14 是使用物件中的索引子來存取 `[3, 0]` `SKMatrix44` 。

在3D 圖形系統中， (x，y，z) 的3D 點會轉換成1×4矩陣，以乘以4乘4的轉換矩陣：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

類似于在三個維度中進行的2D 轉換，會假設3D 轉換會以四個維度來進行。 第四個維度稱為 W，而3D 空間則假設存在於4座標等於1的4D 空間內。 轉換公式如下所示：

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

這顯然是因為資料格的轉換公式、 `M11` `M22` `M33` x、y 和 z 方向的縮放比例，以及 `M41` `M42` `M43` x、y 和 z 方向的平移因數。

為了將這些座標轉換回3D 空間，其中 W 等於1，x '，y ' 和 z ' 座標全都除以 W '：

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

該除以 w ' 可提供3D 空間的觀點。 如果 w ' 等於1，則不會發生任何觀點。

3D 空間中的旋轉可能相當複雜，但最簡單的旋轉是圍繞 X、Y 和 Z 軸的旋轉。 圍繞 X 軸的角度α旋轉是此矩陣：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

當受制于這項轉換時，X 的值會維持不變。 Y 軸的旋轉會讓 Y 的值保持不變：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

圍繞 Z 軸的旋轉與2D 圖形中的旋轉相同：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

旋轉方向是由座標系統的 handedness 所隱含。 這是左手系統的系統，因此，如果您將左側的 thumb 指向針對特定軸增加值的方向，則會向右旋轉 X 軸、向下旋轉 Y 軸，以及朝您旋轉 Z 軸的方向，而另一個手指的曲線表示旋轉方向的角度。

`SKMatrix44` 具有一般化靜態 [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) 和 [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) 方法，可讓您指定旋轉發生的軸：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

針對 X 軸旋轉，將前三個引數設定為1，0，0。 針對 Y 軸旋轉，將其設定為0、1、0，並針對 Z 軸旋轉，將其設定為0、0、1。

4到4的第四個數據行適用于透視圖。 沒有 `SKMatrix44` 建立觀點轉換的方法，但是您可以使用下列程式碼自行建立它：

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

引數名稱的原因 `depth` 很快就會出現。 該程式碼會建立矩陣：

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

轉換公式會產生下列 w ' 的計算：

`w' = –z / depth + 1`

這可減少 X 和 Y 座標，當 Z 的值小於零 (在 XY 平面的背後) ，以及增加 Z 值的 X 和 Y 座標。當 Z 座標等於時 `depth` ，w ' 為零，而座標會變成無限。 三維圖形系統是以攝影機比喻為基礎，而 `depth` 此處的值代表相機與座標系統原點之間的距離。 如果繪圖物件的 Z 座標是 `depth` 來自原點的單位，則在概念上會觸及相機的鏡頭，並變得無限大。

請記住，您可能會將此 `perspectiveMatrix` 值與旋轉矩陣一起使用。 如果要旋轉的繪圖物件有 X 或 Y 座標大於 `depth` ，則這個物件在3d 空間中的旋轉可能會涉及 Z 座標大於 `depth` 。 必須避免這個情況！ 當您建立時 `perspectiveMatrix` ，無論繪圖物件的旋轉方式為何，您都想要將其設定 `depth` 為夠大的值。 這可確保永遠不會有零除。

結合3D 旋轉和透視圖需要將4個矩陣組合在一起。 基於這個目的，會 `SKMatrix44` 定義串連方法。 如果 `A` 和 `B` 是 `SKMatrix44` 物件，則下列程式碼會將 A 設為等於 a × B：

```csharp
A.PostConcat(B);
```

當2D 圖形系統中使用4個轉換矩陣時，會將它套用至2D 物件。 這些物件是平面的，且假設其 Z 座標為零。 轉換乘法比稍早所示的轉換簡單一點：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Z 的0值會導致轉換公式，而不涉及矩陣第三列中的任何資料格：

x ' = M11 · x + M21 · y + M41

y ' = M12 · x + M22 · y + M42

z ' = M13 · x + M23 · y + M43

w ' = M14 · x + M24 · y + M44

此外，z 的座標也與此處無關。 當2D 圖形系統中顯示3D 物件時，會忽略 Z 座標值，以折迭為二維物件。 轉換公式其實只是這兩個：

`x" = x' / w'`

`y" = y' / w'`

這表示可以忽略 4 x 4 矩陣的第三個數據列 *和* 第三個數據行。

但是如果這樣做，那麼第一次需要 4 x 4 矩陣的原因為何？

雖然四維的第三個數據列和第三個數據行與二維轉換無關，但是第三個數據列和資料行在將不同的值相乘時，會 *先扮演角色* `SKMatrix44` 。 例如，假設您將 Y 軸的旋轉與觀點轉換相乘：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在產品中，資料格 `M14` 現在包含一個觀點值。 如果您想要將該矩陣套用至2D 物件，則會排除第三個數據列和資料行，以將其轉換成 3 x 3 的矩陣：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

您現在可以使用它來轉換2D 點：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

轉換公式如下：

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

現在將所有專案除以 z」：

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

當2D 物件在 Y 軸周圍以正角度旋轉時，會將正 X 值 recede 至背景，而負 X 值會指向前景。 X 值看起來會更接近 Y 軸， (由余弦值所控管) 當 Y 軸的座標從檢視器更接近或更接近檢視器時，會變得較小或更大。

使用時 `SKMatrix44` ，請將不同的值相乘來執行所有3d 旋轉和透視圖作業 `SKMatrix44` 。 然後，您可以使用類別的屬性，從4到4的矩陣解壓縮二維 3 x 3 的矩陣 [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) `SKMatrix44` 。 這個屬性會傳回熟悉的 `SKMatrix` 值。

[ **旋轉 3d** ] 頁面可讓您使用3d 旋轉進行實驗。 [**Rotation3DPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)檔案具現化四個滑杆，以設定 X、Y 和 Z 軸的旋轉，並設定深度值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Transforms.Rotation3DPage"
             Title="Rotation 3D">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
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
                    <Setter Property="Maximum" Value="360" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Slider x:Name="xRotateSlider"
                Grid.Row="0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference xRotateSlider},
                              Path=Value,
                              StringFormat='X-Axis Rotation = {0:F0}'}"
               Grid.Row="1" />

        <Slider x:Name="yRotateSlider"
                Grid.Row="2"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference yRotateSlider},
                              Path=Value,
                              StringFormat='Y-Axis Rotation = {0:F0}'}"
               Grid.Row="3" />

        <Slider x:Name="zRotateSlider"
                Grid.Row="4"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zRotateSlider},
                              Path=Value,
                              StringFormat='Z-Axis Rotation = {0:F0}'}"
               Grid.Row="5" />

        <Slider x:Name="depthSlider"
                Grid.Row="6"
                Maximum="2500"
                Minimum="250"
                ValueChanged="OnSliderValueChanged" />

        <Label Grid.Row="7"
               Text="{Binding Source={x:Reference depthSlider},
                              Path=Value,
                              StringFormat='Depth = {0:F0}'}" />

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="8"
                           PaintSurface="OnCanvasViewPaintSurface" />
    </Grid>
</ContentPage>
```

請注意， `depthSlider` 會使用250的 `Minimum` 值進行初始化。 這表示在此處旋轉的2D 物件，其 X 和 Y 座標受限於原點周圍250圖元半徑所定義的圓形。 此物件在3D 空間中的任何旋轉一律會產生小於250的座標值。

[**Rotation3DPage.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)程式碼後端檔案會以300圖元正方形載入點陣圖：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        if (canvasView != null)
        {
            canvasView.InvalidateSurface();
        }
    }
    ...
}
```

如果3D 轉換在此點陣圖上置中，則 X 和 Y 座標的範圍介於-150 到150之間，而角落則是從中央到212圖元，因此所有專案都在250圖元半徑內。

`PaintSurface`處理常式會 `SKMatrix44` 根據滑杆建立物件，並使用來將它們相乘 `PostConcat` 。 `SKMatrix`從最終物件中解壓縮的值 `SKMatrix44` 會以轉譯轉換來括住螢幕中央的旋轉中心：

```csharp
public partial class Rotation3DPage : ContentPage
{
    SKBitmap bitmap;

    public Rotation3DPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
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

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Use 3D matrix for 3D rotations and perspective
        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, (float)xRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, (float)yRotateSlider.Value));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, (float)zRotateSlider.Value));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / (float)depthSlider.Value;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the bitmap
        canvas.SetMatrix(matrix);
        float xBitmap = xCenter - bitmap.Width / 2;
        float yBitmap = yCenter - bitmap.Height / 2;
        canvas.DrawBitmap(bitmap, xBitmap, yBitmap);
    }
}
```

當您試驗第四個滑杆時，您會發現不同的深度設定不會將物件從檢視器移到更遠的範圍，而是改為改變觀點效果的範圍：

[![旋轉3D 頁面的三重螢幕擷取畫面](3d-rotation-images/rotation3d-small.png)](3d-rotation-images/rotation3d-large.png#lightbox "旋轉3D 頁面的三重螢幕擷取畫面")

**動畫旋轉 3d** 也會 `SKMatrix44` 在3d 空間中用來建立文字字串的動畫。 `textPaint`設定為欄位的物件會在函式中用來判斷文字的界限：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    SKCanvasView canvasView;
    float xRotationDegrees, yRotationDegrees, zRotationDegrees;
    string text = "SkiaSharp";
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black,
        TextSize = 100,
        StrokeWidth = 3,
    };
    SKRect textBounds;

    public AnimatedRotation3DPage()
    {
        Title = "Animated Rotation 3D";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Measure the text
        textPaint.MeasureText(text, ref textBounds);
    }
    ...
}
```

覆 `OnAppearing` 寫會定義三個 Xamarin.Forms `Animation` 物件 `xRotationDegrees` ，以不同的速率建立、 `yRotationDegrees` 和欄位的動畫 `zRotationDegrees` 。 請注意，這些動畫的期間會設定為質數 (5 秒、7秒和11秒) 因此，整體組合只會每隔385秒重複一次，或超過10分鐘：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    protected override void OnAppearing()
    {
        base.OnAppearing();

        new Animation((value) => xRotationDegrees = 360 * (float)value).
            Commit(this, "xRotationAnimation", length: 5000, repeat: () => true);

        new Animation((value) => yRotationDegrees = 360 * (float)value).
            Commit(this, "yRotationAnimation", length: 7000, repeat: () => true);

        new Animation((value) =>
        {
            zRotationDegrees = 360 * (float)value;
            canvasView.InvalidateSurface();
        }).Commit(this, "zRotationAnimation", length: 11000, repeat: () => true);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();
        this.AbortAnimation("xRotationAnimation");
        this.AbortAnimation("yRotationAnimation");
        this.AbortAnimation("zRotationAnimation");
    }
    ...
}
```

如同先前的程式， `PaintCanvas` 處理常式會建立 `SKMatrix44` 旋轉和觀點的值，並將它們相乘：

```csharp
public class AnimatedRotation3DPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find center of canvas
        float xCenter = info.Width / 2;
        float yCenter = info.Height / 2;

        // Translate center to origin
        SKMatrix matrix = SKMatrix.MakeTranslation(-xCenter, -yCenter);

        // Scale so text fits
        float scale = Math.Min(info.Width / textBounds.Width,
                               info.Height / textBounds.Height);
        SKMatrix.PostConcat(ref matrix, SKMatrix.MakeScale(scale, scale));

        // Calculate composite 3D transforms
        float depth = 0.75f * scale * textBounds.Width;

        SKMatrix44 matrix44 = SKMatrix44.CreateIdentity();
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(1, 0, 0, xRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 1, 0, yRotationDegrees));
        matrix44.PostConcat(SKMatrix44.CreateRotationDegrees(0, 0, 1, zRotationDegrees));

        SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
        perspectiveMatrix[3, 2] = -1 / depth;
        matrix44.PostConcat(perspectiveMatrix);

        // Concatenate with 2D matrix
        SKMatrix.PostConcat(ref matrix, matrix44.Matrix);

        // Translate back to center
        SKMatrix.PostConcat(ref matrix,
            SKMatrix.MakeTranslation(xCenter, yCenter));

        // Set the matrix and display the text
        canvas.SetMatrix(matrix);
        float xText = xCenter - textBounds.MidX;
        float yText = yCenter - textBounds.MidY;
        canvas.DrawText(text, xText, yText, textPaint);
    }
}
```

此3D 旋轉會以數個2D 轉換括住，以將旋轉中心移至螢幕中央，並調整文字字串的大小，使其寬度與螢幕相同：

[![動畫旋轉3D 頁面的三重螢幕擷取畫面](3d-rotation-images/animatedrotation3d-small.png)](3d-rotation-images/animatedrotation3d-large.png#lightbox "動畫旋轉3D 頁面的三重螢幕擷取畫面")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)