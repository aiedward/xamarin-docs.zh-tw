---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3706139a2c15d01af67203c2bd09b281de80ed52
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140201"
---
# <a name="3d-rotations-in-skiasharp"></a>SkiaSharp 中的3D 旋轉

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用非仿射轉換來旋轉3D 空間中的2D 物件。_

非仿射轉換的其中一個常見應用，是在3D 空間中模擬2D 物件的旋轉：

![](3d-rotation-images/3drotationsexample.png "A text string rotated in 3D space")

此作業牽涉到使用三維旋轉，然後衍生 `SKMatrix` 執行這些3d 旋轉的非仿射轉換。

開發這種轉換時，很難 `SKMatrix` 只在兩個維度內運作。 當這個3到3的矩陣衍生自3D 圖形中所使用的4到4矩陣時，作業就會變得更容易。 SkiaSharp 包含 [`SKMatrix44`](xref:SkiaSharp.SKMatrix44) 此用途的類別，但3d 圖形中的某些背景是瞭解3d 旋轉和4個轉換矩陣的必要部分。

三維座標系統會加入稱為 Z 的第三個軸。在概念上，Z 軸是在螢幕的直角。 3D 空間中的座標點會以三個數字表示：（x，y，z）。 在本文所使用的3D 座標系統中，增加 X 的值是向右及增加 Y 的值，就像兩個維度一樣。 增加正 Z 值會出現在螢幕上。 原點是左上角，就像2D 圖形一樣。 您可以將螢幕視為具有 Z 軸的 XY 平面到這個平面。

這稱為「左手座標系統」。 如果您將左側的食指指向正 X 座標的方向（向右），而中間手指的方向是增加 Y 座標（向下），則您的 thumb 會在增加 Z 座標的方向上（從螢幕向外擴充）。

在3D 圖形中，轉換是以4到4的矩陣為基礎。 以下是4乘以4的身分識別矩陣：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

在使用4對4矩陣時，使用資料列和資料行編號來識別儲存格會很方便：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

不過，SkiaSharp `Matrix44` 類別有點不同。 在中設定或取得個別資料格值的唯一方式 `SKMatrix44` 是使用 [`Item`](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32)) 索引子。 資料列和資料行索引是以零為起始，而不是以一為基礎，而且資料列和資料行已交換。 上圖中的資料格 M14 是使用物件中的索引子來存取 `[3, 0]` `SKMatrix44` 。

在3D 圖形系統中，3D 點（x，y，z）會轉換為1到4的矩陣，乘以4到4的轉換矩陣：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

類似于在三個維度中進行的2D 轉換，會假設3D 轉換會在四個維度中進行。 第四個維度稱為 W，而3D 空間則假設在4D 空間中，W 座標等於1。 轉換公式如下所示：

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

這很明顯地來自轉換公式，其中的資料格 `M11` `M22` `M33` 是 x、y 和 z 方向的縮放比例，而 `M41` 、 `M42` 和 `M43` 則是 x、y 和 z 方向的轉譯因素。

若要將這些座標轉換回3D 空間，其中 W 等於1，x '、y ' 和 z ' 座標全部除以 W '：

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

以 w ' 分割的會提供3D 空間中的觀點。 如果 w ' 等於1，則不會發生任何觀點。

3D 空間中的旋轉可能非常複雜，但最簡單的旋轉是圍繞 X、Y 和 Z 軸。 圍繞 X 軸旋轉的角度α是此矩陣：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

對這項轉換進行時，X 的值保持不變。 繞 Y 軸旋轉會使 Y 的值保持不變：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

Z 軸的旋轉等同于2D 圖形中的：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

旋轉的方向是由座標系統的慣用手所隱含。 這是一種左手邊的系統，因此，如果您將左側的捲軸向右旋轉以增加特定軸的值，請在 X 軸繞著旋轉的右方，沿著 Y 軸繞著旋轉，然後朝向 Z 軸旋轉，而另一個手指的曲線則表示正角度的旋轉方向。

`SKMatrix44`具有一般化 [`CreateRotation`](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single)) 的靜態和 [`CreateRotationDegrees`](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single)) 方法，可讓您指定旋轉發生的軸：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

若要繞著 X 軸旋轉，請將前三個引數設為1、0、0。 針對 Y 軸繞著旋轉，將其設定為0、1、0，並繞著 Z 軸旋轉，將其設定為0，0，1。

4到4的第四個數據行是用於觀點。 沒有 `SKMatrix44` 建立透視圖轉換的方法，但您可以使用下列程式碼自行建立一個：

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

引數名稱的原因 `depth` 很快就會很明顯。 該程式碼會建立矩陣：

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

轉換公式會產生 w ' 的下列計算：

`w' = –z / depth + 1`

這可用來減少 X 和 Y 座標（當 Z 的值小於零時（從概念上在 XY 平面後面），以及增加 Z 的正數值 X 和 Y 座標。當 Z 座標等於時 `depth` ，w ' 為零，而座標會變成無限。 3d 圖形系統是以攝影機比喻為基礎，而 `depth` 這裡的值代表相機與座標系統原點之間的距離。 如果繪圖物件的 Z 座標是 `depth` 來自原點的單位，則會在概念上觸及相機的鏡頭，並且變得無限大。

請記住，您可能會將此 `perspectiveMatrix` 值與旋轉矩陣搭配使用。 如果要旋轉的繪圖物件具有大於的 X 或 Y 座標 `depth` ，則3d 空間中這個物件的旋轉可能會牽涉到 Z 座標大於 `depth` 。 這必須避免！ 當您建立時 `perspectiveMatrix` ，無論 `depth` 繪圖物件中的座標如何旋轉，您都想要將它的值設為夠大。 這可確保永遠不會有零除。

結合3D 旋轉和透視圖需要將4個矩陣結合在一起。 基於此目的，會 `SKMatrix44` 定義串連方法。 如果 `A` 和 `B` 是 `SKMatrix44` 物件，則下列程式碼會將設為等於 a × B：

```csharp
A.PostConcat(B);
```

當2D 圖形系統中使用4至4的轉換矩陣時，它會套用至2D 物件。 這些物件是平面的，假設其 Z 座標為零。 轉換乘法比先前所示的轉換簡單一些：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Z 的0值會導致轉換公式，而不涉及矩陣第三列中的任何資料格：

x ' = M11 · x + M21 · y + M41

y ' = M12 · x + M22 · y + M42

z ' = M13 · x + M23 · y + Imb-m43

w ' = M14 · x + M24 · y + M44

此外，z ' 座標也在這裡無關。 3D 物件在2D 圖形系統中顯示時，會略過 Z 座標值以折迭為二維物件。 轉換公式其實只是這兩個：

`x" = x' / w'`

`y" = y' / w'`

這表示可以忽略4到4矩陣的第三個數據列*和*第三個數據行。

但若是如此，為什麼第一次需要4到4個矩陣？

雖然4乘以4的第三個數據列和第三個數據行與二維轉換無關，但第三個數據列和資料行在將不同的值相乘時，會*先扮演角色* `SKMatrix44` 。 例如，假設您使用「透視」轉換來乘以 Y 軸的旋轉：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在產品中，資料格 `M14` 現在包含一個透視值。 如果您想要將該矩陣套用至2D 物件，則會排除第三個數據列和資料行，將它轉換成 3 x 3 的矩陣：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

現在可以用來轉換2D 點：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

轉換公式為：

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

現在將所有專案除以 z '：

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

當2D 物件繞著 Y 軸的正角度旋轉時，會將正 X 值 recede 至背景，而負值 X 值則會到達前景。 X 值似乎會移到接近 Y 軸（由余弦值所控管），因為當 Y 軸的座標從檢視器或更接近檢視器進一步移動時，其距離會變小或更大。

使用時 `SKMatrix44` ，藉由將各種不同的值相乘來執行所有3d 旋轉和透視圖作業 `SKMatrix44` 。 然後，您可以使用類別的屬性，從4到4的矩陣中，將二維的3到3的矩陣解壓縮 [`Matrix`](xref:SkiaSharp.SKMatrix44.Matrix) `SKMatrix44` 。 這個屬性會傳回熟悉的 `SKMatrix` 值。

[**旋轉 3d** ] 頁面可讓您使用3d 旋轉進行實驗。 [**Rotation3DPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)會具現化四個滑杆，以設定 X、Y 和 Z 軸的旋轉，以及設定深度值：

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

請注意， `depthSlider` 已初始化，其 `Minimum` 值為250。 這表示在這裡旋轉的2D 物件，其 X 和 Y 座標限制為在原點周圍250圖元半徑所定義的圓形。 此物件在3D 空間中的任何旋轉，一律會產生小於250的座標值。

[**Rotation3DPage.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)程式碼後置檔案會在300圖元正方形的點陣圖中載入：

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

如果3D 轉換是以這個點陣圖為中心，則 X 和 Y 座標的範圍介於–150和150之間，而角落則是從中央開始的212圖元，因此所有專案都在「250圖元半徑」內。

`PaintSurface`處理常式會 `SKMatrix44` 根據滑杆建立物件，並使用將它們與一起相乘 `PostConcat` 。 `SKMatrix`從最終物件中解壓縮的值 `SKMatrix44` 會以轉譯轉換來括住，並在螢幕中央置中旋轉：

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

當您試驗第四個滑杆時，您會注意到不同的深度設定並不會從檢視器進一步移動物件，而是改變觀點效果的範圍：

[![](3d-rotation-images/rotation3d-small.png "Triple screenshot of the Rotation 3D page")](3d-rotation-images/rotation3d-large.png#lightbox "Triple screenshot of the Rotation 3D page")

**動畫旋轉 3d**也會使用 `SKMatrix44` ，以動畫顯示3d 空間中的文字字串。 `textPaint`設定為欄位的物件會在函式中用來決定文字的界限：

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

覆 `OnAppearing` 寫會定義三個 Xamarin.Forms `Animation` 物件 `xRotationDegrees` ，以不同的速率建立、 `yRotationDegrees` 和欄位的動畫 `zRotationDegrees` 。 請注意，這些動畫的期間會設定為質數（5秒、7秒和11秒），因此整體組合只會每385秒重複一次，或超過10分鐘：

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

如同上一個程式， `PaintCanvas` 處理常式會建立 `SKMatrix44` 旋轉和觀點的值，並將它們相乘：

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

此3D 旋轉會以數個2D 轉換來括住，以將旋轉中心移至螢幕的中央，並調整文字字串的大小，使其與螢幕的寬度相同：

[![](3d-rotation-images/animatedrotation3d-small.png "Triple screenshot of the Animated Rotation 3D page")](3d-rotation-images/animatedrotation3d-large.png#lightbox "Triple screenshot of the Animated Rotation 3D page")

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
