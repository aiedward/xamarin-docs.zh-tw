---
title: "3D 旋轉"
description: "使用非仿射轉換旋轉 2D 在 3D 空間中的物件。"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: B5894EA0-C415-41F9-93A4-BBF6EC72AFB9
author: charlespetzold
ms.author: chape
ms.date: 04/14/2017
ms.openlocfilehash: a959278b5de72792b23e46372b1333362bed91c8
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="3d-rotations"></a>3D 旋轉

_使用非仿射轉換旋轉 2D 在 3D 空間中的物件。_

一個常見的應用程式的非仿射轉換為模擬旋轉的 3D 空間中的 2D 物件：

![](3d-rotation-images/3drotationsexample.png "旋轉的 3D 空間中的文字字串")

這項作業包括使用 3d 旋轉，以及衍生 非仿射`SKMatrix`執行這些 3D 旋轉的轉換。

很難開發這`SKMatrix`轉換只在兩個維度內正常運作。 這個 3-3 的矩陣衍生自 4-4 矩陣，用於 3D 圖形時，作業就會更容易。 包含 SkiaSharp [ `SKMatrix44` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.PreConcat/p/SkiaSharp.SKMatrix44/)達到此目的，但某些背景的 3D 圖形的類別是需要了解 3D 旋轉和 4-4 轉換矩陣。

三維的座標系統新增隨後在概念上呼叫第三個軸，Z 軸是直角至螢幕。 在 3D 空間中的座標點會以三個數字來表示: （x，y，z）。 以 3D 本文中，增加的 X 值中使用的座標系統是權限，以及增加 Y 的值會跟著中斷，如同兩個維度。 增加正數的 Z 值送出螢幕。 原點是左上角，就像 2D 圖形一樣。 您可以將螢幕的視為直角到此平面在 Z 軸 XY 平面。

這稱為左邊的座標系統。 如果您指向左手朝其方向的 X 座標 （至右邊），正如食指中間手指方向增加 Y 座標 （下），然後您捲動方塊中的點增加 Z 座標的方向，從出延伸畫面。

在 3D 圖形中，轉換取決於 4-4 矩陣。 以下是 4-4 身分識別矩陣：

<pre>
|  1  0  0  0  |
|  0  1  0  0  |
|  0  0  1  0  |
|  0  0  0  1  |
</pre>

在處理 4-4 矩陣，並方便其資料列和資料行的數字指定的資料格：

<pre>
|  M11  M12  M13  M14  |
|  M21  M22  M23  M24  |
|  M31  M32  M33  M34  |
|  M41  M42  M43  M44  |
</pre>

不過，SkiaSharp`Matrix44`類別是稍有不同。 若要設定或取得個別資料格的值中的唯一方式`SKMatrix44`使用[ `Item` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Item/p/System.Int32/System.Int32/)索引子。 資料列和資料行的索引以零為起始而不是一個為基礎，而且交換的資料列和資料行。 使用索引子存取上述圖表中的資料格 M14`[3, 0]`中`SKMatrix44`物件。

在 3D 圖形系統中，3D 的點 （x，y，z） 會轉換成 4-4 轉換矩陣乘以的 1-4 矩陣：

<pre>
                 |  M11  M12  M13  M14  |
| x  y  z  1 | × |  M21  M22  M23  M24  | = | x'  y'  z'  w' |
                 |  M31  M32  M33  M34  |
                 |  M41  M42  M43  M44  |
</pre>

類似於 2D 轉換在三個維度中的所發生 3D 轉換會假設採用四個維度中的位置。 第四個維度指 W，並會假設 3D 空間 W 座標會等於 1，4 D 空間內。 轉換公式如下所示：

x' = M11·x + M21·y + M31·z + M41

y' = M12·x + M22·y + M32·z + M42

z' = M13·x + M23·y + M33·z + M43

w' = M14·x + M24·y + M34·z + M44

很明顯的轉換公式的資料格`M11`， `M22`，`M33`以 X、 Y 和 Z 方向中，調整係數和`M41`， `M42`，和`M43`是轉譯因素 X、 Y 和 Z指示。

若要將這些座標轉換回 W 其中等於 1，x 的 3D 空間 '，y'、 z' 座標是所有除以 w':

x" = x' / w'

y"= y' /w '

z"= z' /w '

w" = w' / w' = 1

該除以 w' 提供在 3D 空間中的檢視方塊。 如果 w' 等於 1，則沒有檢視方塊，就會發生。

在 3D 空間中的旋轉可能相當複雜，但最簡單的旋轉那些在 X、 Y 和 Z 軸。 將角度 α 繞 X 軸的旋轉是此矩陣：

<pre>
|  1     0       0     0  |
|  0   cos(α)  sin(α)  0  |
|  0  –sin(α)  cos(α)  0  |
|  0     0       0     1  |
</pre>

X 的值保持不變時進行這項轉換。 繞 Y 軸旋轉會保留不變的 Y 值：

<pre>
|  cos(α)  0  –sin(α)  0  |
|    0     1     0     0  |
|  sin(α)  0   cos(α)  0  |
|    0     0     0     1  |
</pre>

繞 Z 軸旋轉是與 2D 圖形中的相同：

<pre>
|  cos(α)  sin(α)  0  0  |
| –sin(α)  cos(α)  0  0  |
|    0       0     1  0  |
|    0       0     0  1  |
</pre>

座標系統的慣用右手或左手所隱含的方向旋轉。 這是慣用左手系統，因此，如果您指向的左手朝向增加特定的座標軸值軸之捲動方塊 — 繞 X 軸旋轉右邊繞 Z 軸旋轉旋轉繞著 Y 軸，並朝向您的向下 — 的曲線 yo其他手指表示旋轉正面角度的方向。

`SKMatrix44` 已經一般化靜態[ `CreateRotation` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotation/p/System.Single/System.Single/System.Single/System.Single/)和[ `CreateRotationDegrees` ](https://developer.xamarin.com/api/member/SkiaSharp.SKMatrix44.CreateRotationDegrees/p/System.Single/System.Single/System.Single/System.Single/)方法可讓您指定的軸的旋轉角度，就會發生：

```csharp
public static SKMatrix44 CreateRotationDegrees (Single x, Single y, Single z, Single degrees)
```

繞 X 軸旋轉，為設定的前三個引數為 1，0，0。 繞 Y 軸旋轉，將它們設定為 0，1，0，以及針對繞著 Z 軸旋轉、 將它們設定為 0，0，1。

4-4 的第四個資料行是檢視方塊。 `SKMatrix44`沒有方法建立檢視方塊轉換，但是您可以建立一個自行使用下列程式碼：

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

轉換公式會導致下列計算 w':

w' = – z / 深度 + 1

這樣有助於減少 X 和 Y 座標的 Z 值為小於零 （概念上 XY 平面） 後面時，以增加 X 和 Y 座標的 Z 的正數值。Z 座標等於時`depth`，然後 w' 為零，並成為無限的座標。 3d 圖形系統根據相機的字眼，來建立和`depth`的值，表示從座標系統的原點相機的距離。 如果在圖形化的物件具有 Z，也就是協調`depth`單位從原點，它會在概念上接觸相機的觀點，並會變成無限大。

請記住，您可能要使用這`perspectiveMatrix`搭配旋轉的矩陣中的值。 如果正在被輪替的圖形物件具有 X 或 Y 座標大於`depth`，旋轉的 3D 空間中的此物件是有可能涉及 Z 座標大於`depth`。 必須避免 ！ 建立時`perspectiveMatrix`您想要設定`depth`圖形物件，不論旋轉方式中的所有座標夠大的值。 這可確保絕對沒有任何除數為零。

結合 3D 旋轉和檢視方塊需要 4-4 矩陣一起相乘。 基於此目的，`SKMatrix44`定義串連方法。 如果`A`和`B`是`SKMatrix44`物件時，下列程式碼會將等設 × b:

```csharp
A.PostConcat(B);
```

4-4 轉換矩陣 2D 圖形系統中使用時，它會套用至 2D 物件。 這些物件是一般，而且會假設具有零的 Z 座標。 轉換乘法會稍微比轉換稍早所示：

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

此外，z' 座標也會是不相關這裡。 2D 圖形系統中顯示為 3D 物件時，它會摺疊二維物件以忽略 Z 座標值。 轉換公式是其實只是這兩個：

x" = x' / w'

y"= y' /w '

這表示第三個資料列*和*可以忽略 4-4 矩陣的第三個資料行。

但如果這是，原因是 4-4 矩陣甚至不必在第一次？

雖然第三個資料列和第三個資料行 x 4 4 是二維轉換、 第三個資料列和資料行無關*不要*扮演的角色之前，當各種`SKMatrix44`值相乘。 例如，假設您乘以繞 Y 軸和檢視方塊的轉換：

<pre>
|  cos(α)  0  –sin(α)  0  |   |  1  0  0      0     |   |  cos(α)  0  –sin(α)   sin(α)/depth  |
|    0     1     0     0  | × |  0  1  0      0     | = |    0     1     0           0        |
|  sin(α)  0   cos(α)  0  |   |  0  0  1  -1/depth  |   |  sin(α)  0   cos(α)  -cos(α)/depth  |  
|    0     0     0     1  |   |  0  0  0      1     |   |    0     0     0           1        |
</pre>

在產品中，資料格`M14`現在包含檢視方塊的值。 如果您想要將該矩陣套用至 2D 物件，第三個資料列和資料行中刪除，將轉換的 3-3 的矩陣：

<pre>
|  cos(α)  0  sin(α)/depth  |
|    0     1       0        |
|    0     0       1        |
</pre>

現在可以用來轉換 2D 點：

<pre>
                |  cos(α)  0  sin(α)/depth  |
|  x  y  1  | × |    0     1       0        | = |  x'  y'  z'  |
                |    0     0       1        |
</pre>

轉換公式是：

x' = cos(α)·x

y' = y

z' = (sin （α） / 深度) ·x + 1

現在將所有項目除以 z':

x" = cos(α)·x / ((sin(α)/depth)·x + 1)

y"= y / ((sin （α） / 深度) ·x + 1)

X 值時繞著 Y 軸，然後正數正面角度的 2D 物件會循環 recede 負數時背景的 X 值會回到前景。 X 值看起來與 furthest from Y 軸座標接近移動 Y 軸 （其中係由的餘弦值） 會變成較小或移動的檢視器之間的距離越遠較大或較接近檢視器。

當使用`SKMatrix44`，乘以各種執行所有的 3D 旋轉和檢視方塊作業`SKMatrix44`值。 然後您可以從 x 4 4 擷取的二維 3-3 矩陣矩陣使用[ `Matrix` ](https://developer.xamarin.com/api/property/SkiaSharp.SKMatrix44.Matrix/)屬性`SKMatrix44`類別。 這個屬性會傳回熟悉`SKMatrix`值。

**旋轉 3D**頁面可讓您試驗 3D 旋轉。 [ **Rotation3DPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml)檔案具現化的四個滑桿，以設定繞 X、 Y 和 Z 軸，並設定深度值：

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

請注意，`depthSlider`初始化`Minimum`值為 250。 這表示這裡旋轉的 2D 物件具有限制為 250 像素半徑的原點所定義的圓形的 X 和 Y 座標。 此物件在 3D 空間中的任何輪替永遠會導致小於 250 座標值。

[ **Rotation3DPage.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/Rotation3DPage.xaml.cs)為 300 像素正方形點陣圖中載入的程式碼後置檔案：

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

如果 3D 轉換為此點陣圖上置中，然後 X 和 Y 座標 –150 和 150 之間的範圍時角落 212 像素為單位，從中心，讓一切皆維持在 250 像素的範圍內。

`PaintSurface`處理常式會建立`SKMatrix44`物件根據滑桿，並將它們一起使用`PostConcat`。 `SKMatrix`從最後擷取的值`SKMatrix44`住物件是由將轉譯為置中的螢幕旋轉中心的轉換：

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
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            bitmap = SKBitmap.Decode(skStream);
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

當您試驗第四個滑桿時，您會注意到不同的深度設定不將物件移進一步遠離檢視器中，但改為 alter 的觀點來看效果範圍：

[![](3d-rotation-images/rotation3d-small.png "旋轉的 3D 頁面的三個螢幕擷取畫面")](3d-rotation-images/rotation3d-large.png#lightbox "旋轉的 3D 頁面的三個螢幕擷取畫面")

**3D 動畫的旋轉**也會使用`SKMatrix44`以動畫方式顯示在 3D 空間中的文字字串。 `textPaint`物件設定為建構函式中使用欄位來決定文字的界限：

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

`OnAppearing`覆寫定義三個 Xamarin.Forms`Animation`以動畫方式顯示物件`xRotationDegrees`， `yRotationDegrees`，和`zRotationDegrees`欄位不同的速率。 請注意這些動畫的週期設為主要數字-5 秒，7 秒和 11 秒，讓每個 385 秒或超過 10 分鐘，只會重複整體組合：

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

如同先前的程式，`PaintCanvas`處理常式會建立`SKMatrix44`值旋轉的角度來看，並將它們一起相乘：

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

這個 3D 旋轉是括住數個 2D 轉換將旋轉的中心點移至螢幕的中央，並調整文字字串的大小，使其與螢幕的寬度相同：

[![](3d-rotation-images/animatedrotation3d-small.png "三個螢幕擷取畫面的動畫旋轉的 3D 頁面")](3d-rotation-images/animatedrotation3d-large.png#lightbox "的動畫旋轉的 3D 頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
