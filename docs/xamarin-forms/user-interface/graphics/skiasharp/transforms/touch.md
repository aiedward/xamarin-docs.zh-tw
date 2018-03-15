---
title: "觸控操作"
description: "使用矩陣轉換實作拖曳觸控、 就，和旋轉"
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: charlespetzold
ms.author: chape
ms.date: 04/12/2017
ms.openlocfilehash: 90be80d42c20ca7509037b5f59b34cc1bddde6de
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="touch-manipulations"></a>觸控操作

_使用矩陣轉換實作拖曳觸控、 就，和旋轉_

在多點觸控環境，例如行動裝置上，使用者通常使用其手指來操作螢幕上的物件。 一般一個單指拖曳等兩個單指捏合手勢可以移動和縮放物件，或甚至旋轉它們。 這些筆勢通常會實作使用的轉換矩陣，本文將說明如何執行這些作業。

![](touch-images/touchmanipulationsexample.png "進行轉譯、 縮放和旋轉點陣圖")

## <a name="manipulating-one-bitmap"></a>操作一個點陣圖

**觸控操作**頁面示範單一點陣圖的觸控操作。
這個範例會使用觸控追蹤效果呈現文件中的[叫用事件效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

數個其他檔案提供的支援**觸控操作**頁面。 第一個是[ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs)列舉，指出將會看到您的程式碼所實作的觸控操作不同類型：

```csharp
enum TouchManipulationMode
{
    None,
    PanOnly,
    IsotropicScale,     // includes panning
    AnisotropicScale,   // includes panning
    ScaleRotate,        // implies isotropic scaling
    ScaleDualRotate     // adds one-finger rotation
}
```

`PanOnly` 是與轉譯實作一個單指拖曳。 所有後續的選項也包含移動瀏覽，但牽涉到兩隻手指放：`IsotropicScale`是捏合作業所產生之水平和垂直方向中同樣的縮放比例物件中。 `AnisotropicScale` 可讓不相等的縮放比例。

`ScaleRotate`選項適用於兩個單指縮放和旋轉。 調整是等方向性。 實作兩個單指旋轉與非等向性調整會有問題，因為手指移動基本上都相同。

`ScaleDualRotate`選項會增加一個單指的旋轉。 當為單指指拖曳物件時，拖曳的物件第一次旋轉中心，讓物件的中央對齊上拖曳的向量。

[ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)檔案包含`Picker`與成員`TouchManipulationMode`列舉型別：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.TouchManipulationPage"
             Title="Touch Manipulation">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <Picker Title="Touch Mode"
                Grid.Row="0"
                SelectedIndexChanged="OnTouchModePickerSelectedIndexChanged">
            <Picker.Items>
                <x:String>None</x:String>
                <x:String>PanOnly</x:String>
                <x:String>IsotropicScale</x:String>
                <x:String>AnisotropicScale</x:String>
                <x:String>ScaleRotate</x:String>
                <x:String>ScaleDualRotate</x:String>
            </Picker.Items>
            <Picker.SelectedIndex>
                4
            </Picker.SelectedIndex>
        </Picker>

        <Grid BackgroundColor="White"
              Grid.Row="1">

            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>
    </Grid>
</ContentPage>
```

底端是`SKCanvasView`和`TouchEffect`附加至單一儲存格`Grid`圍住它。

[ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)程式碼後置檔案具有`bitmap`欄位，但它不是類型`SKBitmap`。 型別是`TouchManipulationBitmap`（稍後就會看到的類別）：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    TouchManipulationBitmap bitmap;
    ...

    public TouchManipulationPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.MountainClimbers.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        {
            SKBitmap bitmap = SKBitmap.Decode(skStream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

建構函式具現化`TouchManipulationBitmap`物件，傳遞到建構函式`SKBitmap`取自內嵌資源。 建構函式結束時，會設定`Mode`屬性`TouchManager`屬性`TouchManipulationBitmap`物件成員的`TouchManipulationMode`列舉型別。

`SelectedIndexChanged`處理常式`Picker`也會將此`Mode`屬性：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            TouchManipulationMode mode;
            Enum.TryParse(picker.Items[picker.SelectedIndex], out mode);
            bitmap.TouchManager.Mode = mode;
        }
    }
    ...
}
```

`TouchAction`處理常式的`TouchEffect`具現化的呼叫中 XAML 檔案中的兩個方法`TouchManipulationBitmap`名為`HitTest`和`ProcessTouchEvent`:

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    List<long> touchIds = new List<long>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (bitmap.HitTest(point))
                {
                    touchIds.Add(args.Id);
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    break;
                }
                break;

            case TouchActionType.Moved:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchIds.Contains(args.Id))
                {
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    touchIds.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

如果`HitTest`方法會傳回`true`&mdash;表示手指有接觸到的螢幕點陣圖所佔用的區域內&mdash;則 touch ID 加入至`TouchIds`集合。 此 ID 代表該手指觸控事件的順序，直到從螢幕上手指，拿起。 如果多個手指觸控的點陣圖，然後在`touchIds`集合包含每個手指觸控識別碼。

`TouchAction`處理常式也會呼叫`ProcessTouchEvent`類別`TouchManipulationBitmap`。 這是 where 某些 （但並非全部） 的真實觸控進行處理。

[ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)類別是包裝函式類別`SKBitmap`，其中包含用於轉譯點陣圖並處理觸控事件的程式碼。 它可搭配多個通用的程式碼中的`TouchManipulationManager`類別 （您會看到）。

`TouchManipulationBitmap`建構函式儲存`SKBitmap`兩個屬性，再執行個體化`TouchManager`型別的屬性`TouchManipulationManager`和`Matrix`型別的屬性`SKMatrix`:

```csharp
class TouchManipulationBitmap
{
    SKBitmap bitmap;
    ...

    public TouchManipulationBitmap(SKBitmap bitmap)
    {
        this.bitmap = bitmap;
        Matrix = SKMatrix.MakeIdentity();

        TouchManager = new TouchManipulationManager
        {
            Mode = TouchManipulationMode.ScaleRotate
        };
    }

    public TouchManipulationManager TouchManager { set; get; }

    public SKMatrix Matrix { set; get; }
    ...
}
```

這`Matrix`屬性是所有觸控活動所都導致的累積的轉換。 如您所見，每個觸控事件會解析成矩陣，然後就會與串連`SKMatrix`所儲存值`Matrix`屬性。

`TouchManipulationBitmap`物件本身中繪製其`Paint`方法。 引數是`SKCanvas`物件。 這`SKCanvas`可能已經有轉換套用至其中，所以`Paint`方法串連`Matrix`屬性至現有的轉換，點陣圖相關聯，並完成時，還原畫布：

```csharp
class TouchManipulationBitmap
{
    ...
    public void Paint(SKCanvas canvas)
    {
        canvas.Save();
        SKMatrix matrix = Matrix;
        canvas.Concat(ref matrix);
        canvas.DrawBitmap(bitmap, 0, 0);
        canvas.Restore();
    }
    ...
}
```

`HitTest`方法會傳回`true`如果使用者觸螢幕點陣圖界限內的點。 使用者管理點陣圖，點陣圖可能被輪替，或甚至 （透過非等向性縮放和旋轉的組合） 中的平行四邊形的圖形。 您可能會擔心，`HitTest`方法必須在此情況下實作複雜的分析幾何。

不過，捷徑，可以：

判斷是否點所在的轉換的矩形界限內時判斷是否會反轉的轉換的點位於未轉換的矩形界限內相同。 更容易計算且可以使用方便`Contains`方法所定義`SKRect`:

```csharp
class TouchManipulationBitmap
{
    ...
    public bool HitTest(SKPoint location)
    {
        // Invert the matrix
        SKMatrix inverseMatrix;

        if (Matrix.TryInvert(out inverseMatrix))
        {
            // Transform the point using the inverted matrix
            SKPoint transformedPoint = inverseMatrix.MapPoint(location);

            // Check if it's in the untransformed bitmap rectangle
            SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
            return rect.Contains(transformedPoint);
        }
        return false;
    }
    ...
}
```

中的第二個公用方法`TouchManipulationBitmap`是`ProcessTouchEvent`。 呼叫這個方法時，它已經建立觸控事件屬於這個特定的點陣圖。 方法會維護的字典[ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs)是只是先前的點，每個手指新點的物件：

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

以下是字典和`ProcessTouchEvent`方法本身：

```csharp
class TouchManipulationBitmap
{
    ...
    Dictionary<long, TouchManipulationInfo> touchDictionary =
        new Dictionary<long, TouchManipulationInfo>();
    ...
    public void ProcessTouchEvent(long id, TouchActionType type, SKPoint location)
    {
        switch (type)
        {
            case TouchActionType.Pressed:
                touchDictionary.Add(id, new TouchManipulationInfo
                {
                    PreviousPoint = location,
                    NewPoint = location
                });
                break;

            case TouchActionType.Moved:
                TouchManipulationInfo info = touchDictionary[id];
                info.NewPoint = location;
                Manipulate();
                info.PreviousPoint = info.NewPoint;
                break;

            case TouchActionType.Released:
                touchDictionary[id].NewPoint = location;
                Manipulate();
                touchDictionary.Remove(id);
                break;

            case TouchActionType.Cancelled:
                touchDictionary.Remove(id);
                break;
        }
    }
    ...
}
```

在`Moved`和`Released`事件，方法會呼叫`Manipulate`。 在這段時間，`touchDictionary`包含一或多個`TouchManipulationInfo`物件。 如果`touchDictionary`包含一個項目，很可能會`PreviousPoint`和`NewPoint`值不相等，且代表手指移動。 如果多個手指接觸點陣圖，字典中包含一個以上的項目，但其中一個項目有不同`PreviousPoint`和`NewPoint`值。 請將其餘所有具有相等`PreviousPoint`和`NewPoint`值。

這是很重要：`Manipulate`方法可以假設它正在處理的只有一個手指移動。 在這個呼叫時不其他指會移動，而且如果它們真正要移動 （如有），這些動作將會處理在未來的呼叫`Manipulate`。

`Manipulate`方法首先會字典複製到陣列為了方便起見。 它會忽略前兩個項目以外的任何項目。 如果將嘗試以上兩隻手指放操作點陣圖，其他則會忽略。 `Manipulate` 是的最後一個成員`TouchManipulationBitmap`:

```csharp
class TouchManipulationBitmap
{
    ...
    void Manipulate()
    {
        TouchManipulationInfo[] infos = new TouchManipulationInfo[touchDictionary.Count];
        touchDictionary.Values.CopyTo(infos, 0);
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();

        if (infos.Length == 1)
        {
            SKPoint prevPoint = infos[0].PreviousPoint;
            SKPoint newPoint = infos[0].NewPoint;
            SKPoint pivotPoint = Matrix.MapPoint(bitmap.Width / 2, bitmap.Height / 2);

            touchMatrix = TouchManager.OneFingerManipulate(prevPoint, newPoint, pivotPoint);
        }
        else if (infos.Length >= 2)
        {
            int pivotIndex = infos[0].NewPoint == infos[0].PreviousPoint ? 0 : 1;
            SKPoint pivotPoint = infos[pivotIndex].NewPoint;
            SKPoint newPoint = infos[1 - pivotIndex].NewPoint;
            SKPoint prevPoint = infos[1 - pivotIndex].PreviousPoint;

            touchMatrix = TouchManager.TwoFingerManipulate(prevPoint, newPoint, pivotPoint);
        }

        SKMatrix matrix = Matrix;
        SKMatrix.PostConcat(ref matrix, touchMatrix);
        Matrix = matrix;
    }
}
```

如果單指在操作點陣圖，`Manipulate`呼叫`OneFingerManipulate`方法`TouchManipulationManager`物件。 兩隻手指放，它會呼叫`TwoFingerManipulate`。 這些方法的引數都相同：`prevPoint`和`newPoint`引數表示移動手指。 但`pivotPoint`引數是不同的兩個呼叫：

一個單指操作`pivotPoint`是點陣圖的中心。 這是為了允許一個單指的旋轉。 兩個單指管理事件表示只有一個的手指移動以便`pivotPoint`是未移動手指。

在這兩種情況下，`TouchManipulationManager`傳回`SKMatrix`方法串連與目前的值`Matrix`屬性，`TouchManipulationPage`用來呈現點陣圖。

`TouchManipulationManager` 一般化，並不使用以外的任何其他檔案`TouchManipulationMode`。 您可以在自己的應用程式中使用這個類別不會變更。 它會定義單一屬性的型別`TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


不過，您可能會想要避免`AnisotropicScale`選項。 它是很容易操作點陣圖的縮放比例因素會變成零這個選項。 它會返回永遠不會看不到消失的點陣圖。 如果您真正需要非等向性縮放比例，您要增強的邏輯，以避免非預期的結果。

`TouchManipulationManager` 使用的向量，但因為沒有任何`SKVector`結構 SkiaSharp，`SKPoint`改為使用。 `SKPoint` 減法運算子，而且結果可以視為向量的支援。 要加入所需的只有向量特定邏輯`Magnitude`計算：

```csharp
class TouchManipulationManager
{
    ...
    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
}
```

每當您已選取旋轉，一個手指和兩個單指操作方法先行處理旋轉。 如果偵測到任何旋轉時，會有效地移除的旋轉元件。 剩下會解譯為移動瀏覽和縮放比例。

以下是`OneFingerManipulate`方法。 如果尚未啟用一個單指旋轉，則其邏輯相當簡單&mdash;它會直接使用新的點與前一個點來建構名為向量`delta`對應的精確地轉譯。 具有一個單指旋轉啟用，此方法會使用角度從樞紐分析點 （點陣圖的中心） 至新的點與前一個點來建構一個旋轉矩陣：

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }

    public SKMatrix OneFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        if (Mode == TouchManipulationMode.None)
        {
            return SKMatrix.MakeIdentity();
        }

        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint delta = newPoint - prevPoint;

        if (Mode == TouchManipulationMode.ScaleDualRotate)  // One-finger rotation
        {
            SKPoint oldVector = prevPoint - pivotPoint;
            SKPoint newVector = newPoint - pivotPoint;

            // Avoid rotation if fingers are too close to center
            if (Magnitude(newVector) > 25 && Magnitude(oldVector) > 25)
            {
                float prevAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                // Calculate rotation matrix
                float angle = newAngle - prevAngle;
                touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                // Effectively rotate the old vector
                float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                oldVector.X = magnitudeRatio * newVector.X;
                oldVector.Y = magnitudeRatio * newVector.Y;

                // Recalculate delta
                delta = newVector - oldVector;
            }
        }

        // Multiply the rotation matrix by a translation matrix
        SKMatrix.PostConcat(ref touchMatrix, SKMatrix.MakeTranslation(delta.X, delta.Y));

        return touchMatrix;
    }
    ...
}
```

在`TwoFingerManipulate`方法中，樞紐分析點是不在此事件中特定的觸控式移動手指的位置。 輪替是一個單指旋轉，非常類似，並接著向量是具名`oldVector`（根據前一個點） 調整的旋轉。 其餘的移動會解譯為調整：

```csharp
class TouchManipulationManager
{
    ...
    public SKMatrix TwoFingerManipulate(SKPoint prevPoint, SKPoint newPoint, SKPoint pivotPoint)
    {
        SKMatrix touchMatrix = SKMatrix.MakeIdentity();
        SKPoint oldVector = prevPoint - pivotPoint;
        SKPoint newVector = newPoint - pivotPoint;

        if (Mode == TouchManipulationMode.ScaleRotate ||
            Mode == TouchManipulationMode.ScaleDualRotate)
        {
            // Find angles from pivot point to touch points
            float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
            float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

            // Calculate rotation matrix
            float angle = newAngle - oldAngle;
            touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

            // Effectively rotate the old vector
            float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
            oldVector.X = magnitudeRatio * newVector.X;
            oldVector.Y = magnitudeRatio * newVector.Y;
        }

        float scaleX = 1;
        float scaleY = 1;

        if (Mode == TouchManipulationMode.AnisotropicScale)
        {
            scaleX = newVector.X / oldVector.X;
            scaleY = newVector.Y / oldVector.Y;

        }
        else if (Mode == TouchManipulationMode.IsotropicScale ||
                 Mode == TouchManipulationMode.ScaleRotate ||
                 Mode == TouchManipulationMode.ScaleDualRotate)
        {
            scaleX = scaleY = Magnitude(newVector) / Magnitude(oldVector);
        }

        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
        {
            SKMatrix.PostConcat(ref touchMatrix,
                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y));
        }

        return touchMatrix;
    }
    ...
}
```

您會發現沒有在這個方法沒有明確的轉譯。 不過，這兩個`MakeRotation`和`MakeScale`方法為基礎的樞紐分析點，以及包含隱含的轉譯。 如果您使用兩隻手指放在點陣圖，並將它們拖曳相同的方向，`TouchManipulation`會取得一系列的觸控事件的兩指之間交替。 每個手指移動相對於其他、 縮放或旋轉結果，但是否定由其他手指移動，而且結果會轉譯。

唯一的其餘部分的**觸控操作**頁面是`PaintSurface`中的處理常式`TouchManipulationPage`程式碼後置檔案。 這會呼叫`Paint`方法`TouchManipulationBitmap`，適用於代表累積的觸控式活動的矩陣：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    MatrixDisplay matrixDisplay = new MatrixDisplay();
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        bitmap.Paint(canvas);

        // Display the matrix in the lower-right corner
        SKSize matrixSize = matrixDisplay.Measure(bitmap.Matrix);

        matrixDisplay.Paint(canvas, bitmap.Matrix,
            new SKPoint(info.Width - matrixSize.Width,
                        info.Height - matrixSize.Height));
    }
}
```

`PaintSurface`處理常式結束時，會顯示`MatrixDisplay`物件顯示累積的觸控矩陣：

[![](touch-images/touchmanipulation-small.png "觸控操作頁面的三個螢幕擷取畫面")](touch-images/touchmanipulation-large.png#lightbox "接觸的管理頁面的三個螢幕擷取畫面")

## <a name="manipulating-multiple-bitmaps"></a>操作多個點陣圖

這類隔離觸控處理類別中的程式碼的優點之一`TouchManipulationBitmap`和`TouchManipulationManager`是重複使用這些類別可讓使用者管理多個點陣圖的程式中的功能。

**點陣圖散佈檢視**頁面會示範如何完成這。 而不是定義類型的欄位`TouchManipulationBitmap`、 [ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/SkiaSharpFormsDemos/SkiaSharpFormsDemos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs)類別會定義`List`的點陣圖物件：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    List<TouchManipulationBitmap> bitmapCollection =
        new List<TouchManipulationBitmap>();
    ...
    public BitmapScatterViewPage()
    {
        InitializeComponent();

        // Load in all the available bitmaps
        Assembly assembly = GetType().GetTypeInfo().Assembly;
        string[] resourceIDs = assembly.GetManifestResourceNames();
        SKPoint position = new SKPoint();

        foreach (string resourceID in resourceIDs)
        {
            if (resourceID.EndsWith(".png") ||
                resourceID.EndsWith(".jpg"))
            {
                using (Stream stream = assembly.GetManifestResourceStream(resourceID))
                using (SKManagedStream skStream = new SKManagedStream(stream))
                {
                    SKBitmap bitmap = SKBitmap.Decode(skStream);
                    bitmapCollection.Add(new TouchManipulationBitmap(bitmap)
                    {
                        Matrix = SKMatrix.MakeTranslation(position.X, position.Y),
                    });
                    position.X += 100;
                    position.Y += 100;
                }
            }
        }
    }
    ...
}
```

建構函式在所有可用做為內嵌資源，點陣圖中載入，並將其加入`bitmapCollection`。 請注意，`Matrix`屬性會初始化每個`TouchManipulationBitmap`物件，因此每個點陣圖左上角位移 x 100 像素。

`BitmapScatterView`頁面也需要處理多個點陣圖觸控事件。 而不是定義`List`的 touch Id 的目前操作`TouchManipulationBitmap`物件，此程式需要字典：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    Dictionary<long, TouchManipulationBitmap> bitmapDictionary =
       new Dictionary<long, TouchManipulationBitmap>();
    ...
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        // Convert Xamarin.Forms point to pixels
        Point pt = args.Location;
        SKPoint point =
            new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                        (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                for (int i = bitmapCollection.Count - 1; i >= 0; i--)
                {
                    TouchManipulationBitmap bitmap = bitmapCollection[i];

                    if (bitmap.HitTest(point))
                    {
                        // Move bitmap to end of collection
                        bitmapCollection.Remove(bitmap);
                        bitmapCollection.Add(bitmap);

                        // Do the touch processing
                        bitmapDictionary.Add(args.Id, bitmap);
                        bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                        canvasView.InvalidateSurface();
                        break;
                    }
                }
                break;

            case TouchActionType.Moved:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (bitmapDictionary.ContainsKey(args.Id))
                {
                    TouchManipulationBitmap bitmap = bitmapDictionary[args.Id];
                    bitmap.ProcessTouchEvent(args.Id, args.Type, point);
                    bitmapDictionary.Remove(args.Id);
                    canvasView.InvalidateSurface();
                }
                break;
        }
    }
    ...
}
```

請注意如何`Pressed`邏輯迴圈`bitmapCollection`反向。 點陣圖通常彼此重疊。 稍後在集合中的點陣圖以視覺化方式之間稍早在集合中的點陣圖之上。 如果有多個點陣圖下按下螢幕手指，最上層的其中一個必須由該手指操作的一個。

也請注意，`Pressed`邏輯將移該點陣圖至集合結尾，讓它以視覺化方式移到其他點陣圖堆頂端。

在`Moved`和`Released`事件，`TouchAction`處理常式呼叫`ProcessingTouchEvent`方法中的`TouchManipulationBitmap`如同舊版的程式。

最後，`PaintSurface`處理常式呼叫`Paint`方法的每個`TouchManipulationBitmap`物件：

```csharp
public partial class BitmapScatterViewPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKCanvas canvas = args.Surface.Canvas;
        canvas.Clear();

        foreach (TouchManipulationBitmap bitmap in bitmapCollection)
        {
            bitmap.Paint(canvas);
        }
    }
}
```

程式碼集合執行迴圈，並顯示一堆集合的開頭到結尾的點陣圖：

[![](touch-images/bitmapscatterview-small.png "點陣圖 [散佈圖檢視] 頁面的三個螢幕擷取畫面")](touch-images/bitmapscatterview-large.png#lightbox "點陣圖散佈檢視頁面的三個螢幕擷取畫面")


## <a name="related-links"></a>相關連結

- [SkiaSharp 應用程式開發介面](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos (sample)](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/SkiaSharpFormsDemos/)
- [叫用事件的效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
