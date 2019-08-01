---
title: SkiaSharp 中的 3D 旋轉
description: 本文說明如何以旋轉 2D 物件在 3D 空間中，使用非仿射轉換，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: davidbritch
ms.author: dabritch
ms.date: 04/14/2017
ms.openlocfilehash: c6a600b7ae05df626d4315796c7a5e425639e2f8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652789"
---
# <a name="3d-rotations-in-skiasharp"></a>SkiaSharp 中的 3D 旋轉

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_您可以使用非仿射轉換，旋轉 3D 空間中的 2D 物件。_

非仿射轉換的常見用法模擬 3D 空間中的 2D 物件的旋轉：

![](3d-rotation-images/3drotationsexample.png "在 3D 空間中旋轉的文字字串")

這項作業牽涉到使用三維旋轉，以及接著衍生非仿射`SKMatrix`執行這些 3D 旋轉轉換。

很難開發這`SKMatrix`轉換只會在兩個維度內正常運作。 當這個 3-3 矩陣衍生自 3D 圖形中所使用的 4 乘 4 矩陣，作業就容易多了。 包含 SkiaSharp [ `SKMatrix44` ](xref:SkiaSharp.SKMatrix44)此目的，但在 3D 圖形中的一些背景的類別是需要了解 3D 旋轉和 4 x 4 轉換矩陣。

3d 座標系統中加入 z 在概念上呼叫第三個軸、 Z 軸為直角至畫面。 在 3D 空間中的座標點會以三個數字來表示: （x，y，z）。 3D 中用於本文中，增加的 X 值的座標系統是權限，以及兩個維度中一樣，所發生故障，增加值的 Y。 增加正 Z 值都是由螢幕。 原點是左上角的 2D 圖形一樣。 您可以想像螢幕的 Z 軸直角此平面與 XY 平面。

這稱為左側的座標系統。 如果點的 X 座標 （向右），正方向的左側的食指，並將中指的方向增加 Y 座標 （下），然後拇指指向遞增的 Z 座標的方向，從擴充出[] 畫面。

在 3D 圖形中，轉換為基礎的 4 乘 4 矩陣。 以下是 4 x 4 單位矩陣：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

在使用 4 x 4 矩陣，是很方便識別資料格，其資料列和資料行的數字：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

不過，SkiaSharp`Matrix44`類別是有點不同。 設定或取得個別資料格的值的唯一辦法`SKMatrix44`是使用[ `Item` ](xref:SkiaSharp.SKMatrix44.Item(System.Int32,System.Int32))索引子。 資料列和資料行索引是以零為起始，而非一個為基礎，並會進行交換的資料列和資料行。 使用索引子來存取在上圖中的儲存格 M14`[3, 0]`在`SKMatrix44`物件。

在 3D 圖形系統中，至 1 x 4 矩陣中的 4 乘 4 轉換矩陣乘以轉換的 3D 點 （x，y，z）：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

類似於 2D 轉換在三個維度中的所發生 3D 轉換會假設在四個維度。 第四個維度指 W、 和 3D 空間會假設為存在於其中 W 座標會等於 1 的 4 維空間內。 轉換公式如下所示：

`x' = M11·x + M21·y + M31·z + M41`

`y' = M12·x + M22·y + M32·z + M42`

`z' = M13·x + M23·y + M33·z + M43`

`w' = M14·x + M24·y + M34·z + M44`

很明顯地，從轉換公式的儲存格`M11`， `M22`，`M33`縮放係數中的 X、 Y 和 Z 的指示，與`M41`， `M42`，和`M43`是 X、 Y 和 Z 轉譯因素指示進行。

若要將這些座標轉換回其中 W 等於 1，x 的 3D 空間 '，y'，與 z '座標所有分為 「 w':

`x" = x' / w'`

`y" = y' / w'`

`z" = z' / w'`

`w" = w' / w' = 1`

該除數 w' 提供在 3D 空間中的檢視方塊。 如果 w' 等於 1，則沒有檢視方塊，就會發生。

在 3D 空間中的旋轉可能相當複雜，但最簡單的旋轉是 X、 Y 和 Z 軸周圍。 角度 α 繞著 X 軸的旋轉是此矩陣：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

X 值維持不變時遇到這項轉換。 繞著 Y 軸旋轉會保留不變的 Y 值：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

繞著 Z 軸旋轉為 2D 圖形相同：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

法線慣用手的座標系統被隱含的方向旋轉。 這是慣用左手的系統，因此，如果您指向您朝向提高特定的座標軸值的左邊的縮圖，向右旋轉繞著 X 軸，繞著 Z 軸旋轉的旋轉繞著 Y 軸，而是使用您的向下 — 然後的曲線 yo其他手指表示旋轉的正角度的方向。

`SKMatrix44` 已一般化靜態[ `CreateRotation` ](xref:SkiaSharp.SKMatrix44.CreateRotation(System.Single,System.Single,System.Single,System.Single))並[ `CreateRotationDegrees` ](xref:SkiaSharp.SKMatrix44.CreateRotationDegrees(System.Single,System.Single,System.Single,System.Single))方法，可讓您指定著它旋轉的軸：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

繞著 X 軸的旋轉，如設定前三個引數為 1，0，0。 繞著 Y 軸的旋轉，將它們設定為 0、 1、 0，以及用於繞著 Z 軸旋轉，將它們設定為 0，0，1。

第四個資料行 x 4 4 是檢視方塊。 `SKMatrix44`不有任何方法來建立檢視方塊的轉換，但您可以自行建立使用下列程式碼：

```csharp
SKMatrix44 perspectiveMatrix = SKMatrix44.CreateIdentity();
perspectiveMatrix[3, 2] = -1 / depth;
```

引數名稱的原因`depth`很快就會明顯。 該程式碼會建立矩陣：

<pre>
|  1  0  0      0     |
|  0  1  0      0     |
|  0  0  1  -1/depth  |
|  0  0  0      1     |
</pre>

轉換公式會導致下列計算的 w':

`w' = –z / depth + 1`

這可降低時的 Z 值會小於零 （在概念上 XY 平面） 後面的 X 和 Y 座標，以及增加的正值 Z 的 X 和 Y 座標。Z 座標等於時`depth`，然後 w' 是零，而且座標成為無限。 3d 圖形系統是以數位相機的比喻，和`depth`的值，表示相機的距離，座標系統的原點。 如果的圖形化的物件具有 Z，也就是協調`depth`單位從原點方法，它可以在概念上會觸碰觀景窗的情況下的，並變得可無限大。

請記住，您可能要使用這`perspectiveMatrix`旋轉矩陣結合的值。 如果要旋轉圖形物件具有 X 或 Y 座標大於`depth`，此物件在 3D 空間中的旋轉很可能牽涉到 Z 座標大於`depth`。 必須避免這樣 ！ 建立時`perspectiveMatrix`您想要設定`depth`夠大，不論如何，它會旋轉圖形物件中的所有座標的值。 這可確保絕對沒有任何除數為零。

結合 3D 旋轉和觀點來看，需要 4 x 4 矩陣一起相乘。 基於此目的，`SKMatrix44`定義串連的方法。 如果`A`並`B`是`SKMatrix44`物件，則下列程式碼會將一個設 × b:

```csharp
A.PostConcat(B);
```

2D 圖形系統中使用 4 x 4 轉換矩陣時，它會套用到 2D 的物件。 這些物件是一般，而且會假設為零的 Z 座標。 轉換乘法運算是比較簡單比轉換稍早所示：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  0  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

Z 結果不包含矩陣的第三個資料列中的任何資料格的轉換公式中的 0 值：

x' = M11·x + M21·y + M41

y' = M12·x + M22·y + M42

z' = M13·x + M23·y + M43

w' = M14·x + M24·y + M44

此外，z' 座標無關這裡也是。 2D 圖形系統中顯示為 3D 物件時，它會摺疊的二維物件來忽略 Z 座標值。 其實這兩個轉換公式︰

`x" = x' / w'`

`y" = y' / w'`

這表示第三個資料列*和*可以忽略的 4 乘 4 矩陣的第三個資料行。

但如果是這樣，為什麼一開始是 4 x 4 矩陣真的必要？

雖然第三個資料列和第三個資料行 x 4 4 是二維轉換、 第三個資料列和資料行無關*請勿*扮演角色之前，當各種`SKMatrix44`值相乘。 例如，假設您將繞著 Y 軸角度轉換的旋轉：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在產品中，儲存格`M14`現在包含 檢視方塊的值。 如果您想要將該矩陣套用至 2D 物件時，會將它轉換成 3-3 矩陣刪除第三個資料列和資料行：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

現在它可以用來轉換 2D 點：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

轉換公式如下：

`x' = cos(α)·x`

`y' = y`

`z' = (sin(α)/depth)·x + 1`

現在將所有項目除以 z':

`x" = cos(α)·x / ((sin(α)/depth)·x + 1)`

`y" = y / ((sin(α)/depth)·x + 1)`

以背景工作，而負數時 2D 物件會具有正數的角度繞著 Y 軸，則正旋轉 recede X 值的 X 值會造成前景。 X 值看起來可以更接近的 Y 軸 （受到所餘弦值） 為 furthest from Y 軸座標變得較小或較大，因為它們將檢視器更仔細的檢視器。

使用時`SKMatrix44`，執行所有的 3D 旋轉和觀點來看作業乘以各種`SKMatrix44`值。 然後您可以從 x 4 4 擷取的二維 3-3 矩陣使用矩陣[ `Matrix` ](xref:SkiaSharp.SKMatrix44.Matrix)屬性`SKMatrix44`類別。 這個屬性會傳回熟悉`SKMatrix`值。

**旋轉 3D**頁面可讓您試驗 3D 旋轉。 [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)檔案具現化的四個滑桿來設定繞著 X、 Y 和 Z 軸旋轉，並設定深度值：

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

請注意，`depthSlider`會使用初始化`Minimum`值為 250。 這表示這裡旋轉的 2D 物件已限制為 250 像素半徑原點為中心所定義的圓形的 X 和 Y 座標。 此物件在 3D 空間中的任何旋轉永遠會導致座標值小於 250。

[ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)載入為 300 像素的正方形的點陣圖中的程式碼後置檔案：

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

如果 3D 轉換會在這個點陣圖上置中，然後 X 和 Y 座標 –150 和 150 之間的範圍時邊角 212 的像素的中心，所以內容都 250 像素半徑內。

`PaintSurface`處理常式會建立`SKMatrix44`物件根據滑桿和乘以它們一起使用`PostConcat`。 `SKMatrix`擷取從最後的值`SKMatrix44`物件必須括住的轉譯轉換至螢幕的中心旋轉的中心：

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

當您試驗第四個滑桿時，您會發現不同的深度設定不會移動遠離檢視器，進一步的物件，但而改變的觀點來看作用範圍：

[![](3d-rotation-images/rotation3d-small.png "旋轉 3D 頁面的三個螢幕擷取畫面")](3d-rotation-images/rotation3d-large.png#lightbox "旋轉 3D 頁面的三個螢幕擷取畫面")

**動畫的旋轉 3D**也會使用`SKMatrix44`以動畫顯示 3D 空間中的文字字串。 `textPaint`依照欄位來判斷文字的界限時，會在建構函式，設定物件：

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

`OnAppearing`覆寫定義三個 Xamarin.Forms`Animation`物件來以動畫顯示`xRotationDegrees`， `yRotationDegrees`，和`zRotationDegrees`欄位不同的速率。 請注意，這些動畫的期間會設定為質數數字 （5 秒、 7、 11 秒數以及），因此每個 385 的秒數或超過 10 分鐘，只會重複整體的組合：

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

前一個程式，如同`PaintCanvas`處理常式會建立`SKMatrix44`值旋轉的角度來看，並將它們一起相乘︰

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

這個 3D 旋轉被用來移至螢幕的中心的旋轉中心的並相應的文字字串的大小，使它與螢幕的寬度相同的數個 2D 轉換：

[![](3d-rotation-images/animatedrotation3d-small.png "動畫的旋轉 3D 頁面的三個螢幕擷取畫面")](3d-rotation-images/animatedrotation3d-large.png#lightbox "動畫的旋轉 3D 頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
