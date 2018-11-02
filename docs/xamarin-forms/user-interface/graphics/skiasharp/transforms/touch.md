---
title: 觸控操作
description: 本文說明如何使用實作拖曳觸控、 捏合、 和旋轉的矩陣轉換，並示範此範例程式碼。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
ms.openlocfilehash: 6f7236a3650c04098edbef92f3d6ed620be501c3
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2018
ms.locfileid: "39615388"
---
# <a name="touch-manipulations"></a>觸控操作

_使用矩陣轉換來實作拖曳觸控、 捏合、 和旋轉_

在多點觸控環境，例如行動裝置上，使用者通常使用他們的手指來操作螢幕上的物件。 常用的筆勢，例如一個手指拖曳和雙指捏合可以移動和縮放物件，或甚至將它們旋轉。 這些筆勢通常使用轉換矩陣來實作，這篇文章會示範如何執行該動作。

![](touch-images/touchmanipulationsexample.png "受到平移、 縮放和旋轉點陣圖")

如下所示的所有範例都使用 Xamarin.Forms 觸控追蹤效果一文所述[**叫用事件影響**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)。

## <a name="dragging-and-translation"></a>拖曳和轉譯

其中一個最重要的應用程式的矩陣轉換是觸控處理。 單一[ `SKMatrix` ](xref:SkiaSharp.SKMatrix)值可以彙總一系列的觸控式作業。 

單指拖曳，`SKMatrix`值執行轉譯。 這示範於**點陣圖拖曳**頁面。 XAML 檔案會具現化`SKCanvasView`Xamarin.Forms 中`Grid`。 A`TouchEffect`已新增至物件`Effects`集合， `Grid`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.BitmapDraggingPage"
             Title="Bitmap Dragging">
    
    <Grid BackgroundColor="White">
        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction" />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

理論上，`TouchEffect`物件無法直接加入`Effects`的集合`SKCanvasView`，但無法在所有平台上運作。 因為`SKCanvasView`是相同的大小`Grid`在此組態中，將它附加`Grid`一樣好。

程式碼後置檔案載入點陣圖資源在其建構函式中，並顯示在`PaintSurface`處理常式：

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    // Bitmap and matrix for display
    SKBitmap bitmap;
    SKMatrix matrix = SKMatrix.MakeIdentity();
    ···

    public BitmapDraggingPage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Display the bitmap
        canvas.SetMatrix(matrix);
        canvas.DrawBitmap(bitmap, new SKPoint());
    }
}
```

不需要任何進一步的程式碼，`SKMatrix`值一律是身分識別矩陣，就會不會影響顯示的點陣圖。 目標`OnTouchEffectAction`改變以反映觸控操作矩陣值，是在 XAML 檔案中設定的處理常式。

`OnTouchEffectAction`處理常式開始轉換 Xamarin.Forms `Point` SkiaSharp 值`SKPoint`值。 這是簡單的調整依據`Width`並`Height`的屬性`SKCanvasView`（這些是裝置獨立單位） 和`CanvasSize`屬性，這是以像素為單位：

```csharp
public partial class BitmapDraggingPage : ContentPage
{
    ···
    // Touch information
    long touchId = -1;
    SKPoint previousPoint;
    ···
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
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point))
                {
                    touchId = args.Id;
                    previousPoint = point;
                }
                break;

            case TouchActionType.Moved:
                if (touchId == args.Id)
                {
                    // Adjust the matrix for the new position
                    matrix.TransX += point.X - previousPoint.X;
                    matrix.TransY += point.Y - previousPoint.Y;
                    previousPoint = point;
                    canvasView.InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = -1;
                break;
        }
    }
    ···
}
```

在手指先觸控螢幕，類型的事件`TouchActionType.Pressed`引發。 第一項工作是判斷手指會觸碰點陣圖。 這類工作通常稱為_點擊測試_。 在此情況下，叫用測試將可藉由建立`SKRect`對應至點陣圖，將矩陣轉換套用至它與值`MapRect`，接著再判斷 觸控點是否在轉換後的矩形內。

如果這種情況，則`touchId`欄位設為 touch ID，並儲存手指的位置。

針對`TouchActionType.Moved`事件、 的翻譯因素`SKMatrix`會調整值目前位置的手指，和手指的新位置。 新的位置下一次，透過儲存和`SKCanvasView`失效。

實驗與此程式時，記下，您可以只拖曳點陣圖時您的手指接觸到的區域會顯示點陣圖。 雖然這項限制不是非常重要，此程式，它會變成重要管理多個點陣圖時。

## <a name="pinching-and-scaling"></a>進行捏合和縮放比例

兩隻手指觸控的點陣圖時，就會發生什麼？ 如果兩指移動以平行方式，然後您可能想要移動以及手指的點陣圖。 如果兩指執行縮小或延伸作業，您可能想要 （若要在下一節中討論） 旋轉或縮放點陣圖。 當縮放點陣圖，它最有意義的兩指保留在相同的位置，相對於點陣圖，以及要據以調整的點陣圖。

一次處理兩指看起來複雜，但請記住，`TouchAction`處理常式只會接收一次一隻手指的相關資訊。 如果兩隻指頭正在操作點陣圖，每個事件，一隻手指已變更位置，但其他尚未變更。 在 **點陣圖縮放**以下頁面程式碼，呼叫尚未變更位置的手指_樞紐分析_點，因為轉換是相對於該點。

這項計畫和前一個程式的其中一個差異是識別碼，必須先儲存該多個觸控。 字典用於此目的，其中 touch ID 是字典索引鍵，而字典值是該手指的目前位置而定：

```csharp
public partial class BitmapScalingPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
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
                // Find transformed bitmap rectangle
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = matrix.MapRect(rect);

                // Determine if the touch was within that rectangle
                if (rect.Contains(point) && !touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Add(args.Id, point);
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger scale and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index of non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points involved in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Scaling factors are ratios of those
                        float scaleX = newVector.X / oldVector.X;
                        float scaleY = newVector.Y / oldVector.Y;

                        if (!float.IsNaN(scaleX) && !float.IsInfinity(scaleX) &&
                            !float.IsNaN(scaleY) && !float.IsInfinity(scaleY))
                        {
                            // If smething bad hasn't happened, calculate a scale and translation matrix
                            SKMatrix scaleMatrix = 
                                SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);

                            SKMatrix.PostConcat(ref matrix, scaleMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }
    ···
}
```

處理`Pressed`動作幾乎是相同先前的程式不同之處在於識別碼與接觸點會加入至字典。 `Released`和`Cancelled`動作移除字典項目。

處理長達`Moved`動作將會更複雜，不過。 如果沒有涉及的只有一隻手指，然後處理是前一個程式幾乎完全相同。 針對兩個或多根手指，程式必須也涉及不移動手指字典中取得資訊。 它會將字典索引鍵複製到陣列，然後再比較第一個索引鍵，識別碼為正在移動手指。 這可讓程式，以取得對應到不移動手指的樞紐分析點。

接下來，程式會計算兩個向量的新手指的位置相對於軸點和舊的手指相對於位置的樞紐分析點。 這些向量的比例縮放係數。 因為可能被除數為零，這些必須檢查無限值或 NaN （不是數字） 的值。 如果一切正常，就會與串連縮放轉換`SKMatrix`儲存為欄位的值。

嘗試使用此頁面，您會注意到，您可以拖曳以一或兩個手指點陣圖或調整使用兩根手指。 調整大小_非等向性_，這表示縮放可以水平和垂直方向不同。 這會扭曲外觀比例，但也可讓您翻轉的點陣圖，以進行鏡像映像。 您也可能會發現，您可以壓縮點陣圖的零個維度，它就會消失。 在實際程式碼，您會想要防範這種。

## <a name="two-finger-rotation"></a>雙指的旋轉

**旋轉點陣圖** 頁面可讓您使用兩指的旋轉或 dbi100 調整。 點陣圖一律會保留其正確的外觀比例。 運用兩指的旋轉和非等向性調整無法運作得非常順利因為移動指的是這兩項工作非常類似。

此程式中第一個主要的差別在於的點擊測試的邏輯。 使用上一個程式`Contains`方法的`SKRect`判斷觸控點是否在轉換對應至點陣圖的矩形。 使用者管理的點陣圖，可能是點陣圖，但旋轉，和`SKRect`無法正確表示旋轉的矩形。 您可能會擔心的點擊測試的邏輯必須在此情況下實作相當複雜的分析幾何圖形。

不過，捷徑，可以： 判斷點所在的已轉換的矩形界限內是否等同於判斷是否會反向已轉換的點位於未轉換的矩形界限內。 更簡單的計算，且邏輯可以繼續使用方便`Contains`方法：

```csharp
public partial class BitmapRotationPage : ContentPage
{
    ···
    // Touch information
    Dictionary<long, SKPoint> touchDictionary = new Dictionary<long, SKPoint>();
    ···
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
                if (!touchDictionary.ContainsKey(args.Id))
                {
                    // Invert the matrix
                    if (matrix.TryInvert(out SKMatrix inverseMatrix))
                    {
                        // Transform the point using the inverted matrix
                        SKPoint transformedPoint = inverseMatrix.MapPoint(point);

                        // Check if it's in the untransformed bitmap rectangle
                        SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);

                        if (rect.Contains(transformedPoint))
                        {
                            touchDictionary.Add(args.Id, point);
                        }
                    }
                }
                break;

            case TouchActionType.Moved:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    // Single-finger drag
                    if (touchDictionary.Count == 1)
                    {
                        SKPoint prevPoint = touchDictionary[args.Id];

                        // Adjust the matrix for the new position
                        matrix.TransX += point.X - prevPoint.X;
                        matrix.TransY += point.Y - prevPoint.Y;
                        canvasView.InvalidateSurface();
                    }
                    // Double-finger rotate, scale, and drag
                    else if (touchDictionary.Count >= 2)
                    {
                        // Copy two dictionary keys into array
                        long[] keys = new long[touchDictionary.Count];
                        touchDictionary.Keys.CopyTo(keys, 0);

                        // Find index non-moving (pivot) finger
                        int pivotIndex = (keys[0] == args.Id) ? 1 : 0;

                        // Get the three points in the transform
                        SKPoint pivotPoint = touchDictionary[keys[pivotIndex]];
                        SKPoint prevPoint = touchDictionary[args.Id];
                        SKPoint newPoint = point;

                        // Calculate two vectors
                        SKPoint oldVector = prevPoint - pivotPoint;
                        SKPoint newVector = newPoint - pivotPoint;

                        // Find angles from pivot point to touch points
                        float oldAngle = (float)Math.Atan2(oldVector.Y, oldVector.X);
                        float newAngle = (float)Math.Atan2(newVector.Y, newVector.X);

                        // Calculate rotation matrix
                        float angle = newAngle - oldAngle;
                        SKMatrix touchMatrix = SKMatrix.MakeRotation(angle, pivotPoint.X, pivotPoint.Y);

                        // Effectively rotate the old vector
                        float magnitudeRatio = Magnitude(oldVector) / Magnitude(newVector);
                        oldVector.X = magnitudeRatio * newVector.X;
                        oldVector.Y = magnitudeRatio * newVector.Y;

                        // Isotropic scaling!
                        float scale = Magnitude(newVector) / Magnitude(oldVector);

                        if (!float.IsNaN(scale) && !float.IsInfinity(scale))
                        {
                            SKMatrix.PostConcat(ref touchMatrix,
                                SKMatrix.MakeScale(scale, scale, pivotPoint.X, pivotPoint.Y));

                            SKMatrix.PostConcat(ref matrix, touchMatrix);
                            canvasView.InvalidateSurface();
                        }
                    }

                    // Store the new point in the dictionary
                    touchDictionary[args.Id] = point;
                }

                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchDictionary.ContainsKey(args.Id))
                {
                    touchDictionary.Remove(args.Id);
                }
                break;
        }
    }

    float Magnitude(SKPoint point)
    {
        return (float)Math.Sqrt(Math.Pow(point.X, 2) + Math.Pow(point.Y, 2));
    }
    ···
}
```

邏輯`Moved`事件開頭就像先前的程式。 名為兩個向量`oldVector`和`newVector`會根據計算先前和目前的點移動手指與 unmoving 手指的樞紐分析點。 然後這些向量角度決定權，但差別的旋轉角度。

調整可能會也涉及，讓舊的向量上的旋轉角度旋轉基礎。 縮放比例的現在兩個向量的相對大小。 請注意，相同`scale`值會用於水平及垂直調整，以便調整為 dbi100。 `matrix`欄位會調整旋轉矩陣並調整矩陣。

如果您的應用程式必須實作觸控處理單一的點陣圖 （或其他物件），您可以調整這些三個範例的程式碼為您自己的應用程式。 但如果您需要實作觸控處理多個點陣圖，您可能需要將這些封裝 touch 的其他類別中的作業。

## <a name="encapsulating-the-touch-operations"></a>封裝的觸控操作

**觸控的操作**頁面會示範觸控操作單一點陣圖，但使用數個其他封裝許多邏輯，如上所示的檔案。 這些檔案的第一個是[ `TouchManipulationMode` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs)列舉型別，這表示您會看到程式碼實作的觸控操作的不同類型：

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

`PanOnly` 是一個手指拖曳實作進行轉譯。 所有後續的選項也會包含移動瀏覽，但牽涉到兩隻手指：`IsotropicScale`是縮小作業會導致同樣調整水平和垂直方向的物件。 `AnisotropicScale` 可讓不相等的縮放比例。

`ScaleRotate`選項是用於雙指縮放和旋轉。 調整為 dbi100 就行了。 如先前所述，實作雙指循環與非等向性調整是有問題因為手指移動基本上都相同。

`ScaleDualRotate`選項會新增一個指的旋轉。 當一根手指拖曳物件時，被拖曳的物件第一次旋轉中心，讓物件的中心對齊拖曳的向量。

[ **TouchManipulationPage.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)檔案包含`Picker`的成員`TouchManipulationMode`列舉型別：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos.Transforms"
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
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:TouchManipulationMode}">
                    <x:Static Member="local:TouchManipulationMode.None" />
                    <x:Static Member="local:TouchManipulationMode.PanOnly" />
                    <x:Static Member="local:TouchManipulationMode.IsotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.AnisotropicScale" />
                    <x:Static Member="local:TouchManipulationMode.ScaleRotate" />
                    <x:Static Member="local:TouchManipulationMode.ScaleDualRotate" />
                </x:Array>
            </Picker.ItemsSource>
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

底端會`SKCanvasView`並`TouchEffect`附加至單一儲存格`Grid`圍住它。

[ **TouchManipulationPage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)程式碼後置檔案具有`bitmap`欄位，但它不是類型`SKBitmap`。 型別是`TouchManipulationBitmap`（您很快會看到的類別）：

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
        {
            SKBitmap bitmap = SKBitmap.Decode(stream);
            this.bitmap = new TouchManipulationBitmap(bitmap);
            this.bitmap.TouchManager.Mode = TouchManipulationMode.ScaleRotate;
        }
    }
    ...
}
```

建構函式具現化`TouchManipulationBitmap`物件，傳遞至建構函式`SKBitmap`取得從內嵌資源。 建構函式結束時，會設定`Mode`的屬性`TouchManager`屬性`TouchManipulationBitmap`物件的成員`TouchManipulationMode`列舉型別。

`SelectedIndexChanged`處理常式`Picker`也會設定這個`Mode`屬性：

```csharp
public partial class TouchManipulationPage : ContentPage
{
    ...
    void OnTouchModePickerSelectedIndexChanged(object sender, EventArgs args)
    {
        if (bitmap != null)
        {
            Picker picker = (Picker)sender;
            bitmap.TouchManager.Mode = (TouchManipulationMode)picker.SelectedItem;
        }
    }
    ...
}
```

`TouchAction`處理常式`TouchEffect`具現化的呼叫中 XAML 檔案中的兩個方法`TouchManipulationBitmap`名為`HitTest`和`ProcessTouchEvent`:

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

如果`HitTest`方法會傳回`true`&mdash;表示手指已觸及的螢幕內的點陣圖所佔用的區域&mdash;則 touch ID 加入至`TouchIds`集合。 此識別碼代表該手指觸控事件的順序，直到從螢幕上手指，拿起。 如果多個根手指觸控的點陣圖，然後在`touchIds`集合包含每根手指觸控識別碼。

`TouchAction`處理常式也會呼叫`ProcessTouchEvent`類別中`TouchManipulationBitmap`。 這正是某些 （但並非全部） 的真實的觸控式進行處理。

[ `TouchManipulationBitmap` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)類別是包裝函式類別`SKBitmap`包含用於轉譯點陣圖，並處理觸控事件的程式碼。 它可搭配多個通用的程式碼中的`TouchManipulationManager`類別 （此您會看到）。

`TouchManipulationBitmap`建構函式儲存`SKBitmap`並具現化兩個屬性，`TouchManager`型別的屬性`TouchManipulationManager`而`Matrix`型別的屬性`SKMatrix`:

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

這`Matrix`屬性是累積的轉換所產生的所有觸控活動。 如您所見，每個觸控事件會解析成矩陣，然後就會與串連`SKMatrix`所儲存的值`Matrix`屬性。

`TouchManipulationBitmap`物件中繪製本身及其`Paint`方法。 引數是`SKCanvas`物件。 這`SKCanvas`可能已經有轉換套用至其中，因此`Paint`方法會串連`Matrix`屬性與現有的轉換，點陣圖相關聯，並完成時，還原畫布：

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

`HitTest`方法會傳回`true`如果使用者接觸到螢幕的某一點界限內的點陣圖。 這會使用先前顯示於邏輯**點陣圖旋轉**頁面：

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

中的第二個公用方法`TouchManipulationBitmap`是`ProcessTouchEvent`。 呼叫這個方法時，它已建立的觸控事件都屬於此特定的點陣圖。 方法會維護的字典[ `TouchManipulationInfo` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs)物件，也就是只要先前的點和新的點，每根手指的：

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

在 `Moved`並`Released`事件，方法會呼叫`Manipulate`。 在這些時間，`touchDictionary`包含一或多個`TouchManipulationInfo`物件。 如果`touchDictionary`包含一個項目，很可能會`PreviousPoint`和`NewPoint`值不相等，且代表的手指移動。 如果多個根手指接觸點陣圖，字典會包含一個以上的項目，但只有其中一個項目有不同`PreviousPoint`和`NewPoint`值。 所有其餘部分具有相等`PreviousPoint`和`NewPoint`值。

這點很重要：`Manipulate`方法可以假設它正在處理的只有一隻手指移動。 在這個呼叫時沒有任何其他指的是移動和這些移動未來呼叫中，如果他們真的要移動的 （如有可能），將會處理`Manipulate`。

`Manipulate`方法第一次將字典複製到陣列，為了方便起見。 它會忽略前兩個項目以外的任何項目。 如果兩個以上的手指來操作點陣圖，有些則會忽略它。 `Manipulate` 是最後一個成員的`TouchManipulationBitmap`:

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

如果操作點陣圖，一隻手指`Manipulate`呼叫`OneFingerManipulate`方法`TouchManipulationManager`物件。 為兩指，它會呼叫`TwoFingerManipulate`。 這些方法的引數都相同：`prevPoint`和`newPoint`引數表示移動手指。 但`pivotPoint`引數是不同的兩個呼叫：

一個指操作`pivotPoint`是點陣圖的中心。 這是為了允許一個指的旋轉。 若是雙指操作，此事件會指示只能有一個根手指移動以便`pivotPoint`是不移動手指。

在這兩種情況下，`TouchManipulationManager`會傳回`SKMatrix`值，它與目前的方法會採用串聯`Matrix`屬性，`TouchManipulationPage`用來呈現點陣圖。

`TouchManipulationManager` 已一般化，並不使用以外的任何其他檔案`TouchManipulationMode`。 您可以在自己的應用程式中使用這個類別不會變更。 它會定義單一屬性的型別`TouchManipulationMode`:

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```


不過，您可能希望避免`AnisotropicScale`選項。 它是很容易操作點陣圖，使其中一個縮放比例會變成零，此選項。 這可讓從深入解析，永遠不再消失的點陣圖。 如果您真正需要非等向性調整，您會想要加強的邏輯，以避免非預期的結果。

`TouchManipulationManager` 使用的向量，但因為沒有任何`SKVector`SkiaSharp，結構`SKPoint`改為使用。 `SKPoint` 減法運算子和結果可以視為向量的支援。 要加入所需的只會將向量特有邏輯是`Magnitude`計算：

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

每當選取了循環，這兩個一個指和雙指操作方法會先處理循環。 如果偵測到任何旋轉時，會有效地移除的旋轉元件。 仍會解譯為移動瀏覽和縮放比例。

以下是`OneFingerManipulate`方法。 如果尚未啟用一個手指旋轉，則邏輯很&mdash;它會直接使用新的點與前一個點來建構向量，名為`delta`精確地轉譯為對應。 啟用一個手指旋轉，方法會使用角度從樞紐分析點 （點陣圖的中心） 的前一個點和新的點來建構一個旋轉矩陣：

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

在 `TwoFingerManipulate`方法中，樞紐分析點是不在本項目特定的觸控式移動手指的位置。 輪替是非常類似於一個手指輪替和向量的具名然後`oldVector`（根據前一個點） 會進行調整的旋轉。 其餘的移動會解譯為調整：

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

您會發現沒有在這個方法沒有明確的轉譯。 不過，這兩個`MakeRotation`和`MakeScale`方法為基礎的樞紐分析點，並包含隱含的轉譯。 如果在點陣圖，並將它們拖曳相同的方向，將兩隻手指`TouchManipulation`會取得一系列的交替兩隻手指觸控事件。 為每根手指移動相對於其他、 縮放或旋轉結果，否定藉由其他手指的移動，但結果是轉譯。

唯一的其餘部分**觸控的操作**頁`PaintSurface`中的處理常式`TouchManipulationPage`程式碼後置檔案。 這會呼叫`Paint`方法的`TouchManipulationBitmap`，適用於矩陣，表示累積的觸控式活動：

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

`PaintSurface`處理常式結束時，會顯示`MatrixDisplay`顯示累積的觸控矩陣的物件：

[![](touch-images/touchmanipulation-small.png "觸控操作頁面的三個螢幕擷取畫面")](touch-images/touchmanipulation-large.png#lightbox "觸控操作頁面的三個螢幕擷取畫面")

## <a name="manipulating-multiple-bitmaps"></a>操作多個點陣圖

其中一個這類隔離類別中的觸控處理程式碼的優點`TouchManipulationBitmap`和`TouchManipulationManager`是能夠重複使用這些類別可讓使用者管理多個點陣圖的程式中。

**點陣圖散佈圖檢視**頁面會示範如何做到這點。 而不是定義類型的欄位`TouchManipulationBitmap`，則[ `BitmapScatterPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs)類別會定義`List`的點陣圖物件：

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
                {
                    SKBitmap bitmap = SKBitmap.Decode(stream);
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

建構函式中所有可用做為內嵌資源，點陣圖會載入，並將其加入`bitmapCollection`。 請注意，`Matrix`屬性會初始化每個`TouchManipulationBitmap`物件，因此每個點陣圖的左上角邊角位移 x 100 像素。

`BitmapScatterView`頁面也需要處理多個點陣圖的觸控事件。 而不是定義`List`觸控的識別碼的目前操作`TouchManipulationBitmap`物件，此程式需要字典：

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

請注意如何`Pressed`邏輯迴圈`bitmapCollection`反向。 點陣圖通常彼此重疊。 在稍早在集合中的點陣圖上以視覺化方式位於稍後在集合中的點陣圖。 如果有多個畫面按下的手指底下的點陣圖，最上層的其中一個必須是由該手指操作。

也請注意，`Pressed`邏輯會移至集合結尾的點陣圖，讓它以視覺方式移至其他點陣圖堆頂端。

在 `Moved`和`Released`事件，`TouchAction`處理常式會呼叫`ProcessingTouchEvent`方法中的`TouchManipulationBitmap`如同舊版的程式。

最後，`PaintSurface`處理常式會呼叫`Paint`方法，每個`TouchManipulationBitmap`物件：

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

[![](touch-images/bitmapscatterview-small.png "點陣圖散佈圖檢視頁面的三個螢幕擷取畫面")](touch-images/bitmapscatterview-large.png#lightbox "點陣圖散佈圖檢視頁面的三個螢幕擷取畫面")

## <a name="single-finger-scaling"></a>單指縮放比例

調整作業通常需要運用兩指捏合手勢。 不過，就可以實作單指使用自動調整的手指移動點陣圖的邊角。

這示範於**單一手指角擴展**頁面。 因為這個範例會使用稍有不同類型的縮放比例，實作`TouchManipulationManager`類別，它不會使用該類別或`TouchManipulationBitmap`類別。 相反地，所有的觸控式邏輯，就會在程式碼後置檔案中。 這會是比較簡單的邏輯比平常，因為它會只有一隻手指追蹤一次，並只會忽略任何可能會觸碰螢幕的次要手指。

[ **SingleFingerCornerScale.xaml** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml)頁面會具現化`SKCanvasView`類別，並建立`TouchEffect`追蹤觸控事件的物件：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Transforms.SingleFingerCornerScalePage"
             Title="Single Finger Corner Scale">

    <Grid BackgroundColor="White"
          Grid.Row="1">

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface" />
        <Grid.Effects>
            <tt:TouchEffect Capture="True"
                            TouchAction="OnTouchEffectAction"   />
        </Grid.Effects>
    </Grid>
</ContentPage>
```

[ **SingleFingerCornerScalePage.xaml.cs** ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs)檔案載入點陣圖資源中的**媒體**目錄，並顯示使用`SKMatrix`物件定義為欄位：

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();
    ···

    public SingleFingerCornerScalePage()
    {
        InitializeComponent();

        string resourceID = "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.SetMatrix(currentMatrix);
        canvas.DrawBitmap(bitmap, 0, 0);
    }
    ···
}
```

這`SKMatrix`物件遭到修改，如下所示的觸控式邏輯。

程式碼後置檔案的其餘部分`TouchEffect`事件處理常式。 它會開始轉換到手指的目前位置`SKPoint`值。 針對`Pressed`動作類型，處理常式會檢查任何其他手指會觸碰 [] 畫面和點陣圖的範圍內的手指。

程式碼的重要部分是`if`牽涉到兩個呼叫的陳述式`Math.Pow`方法。 這個數學檢查手指的位置是否外點陣圖會填滿橢圓形。 如果是的話，就是調整作業。 手指是其中一個的點陣圖，邊角附近，樞紐分析點決定，則是相反的角。 如果手指在這個橢圓形內，則它是一般的移動作業：

```csharp
public partial class SingleFingerCornerScalePage : ContentPage
{
    SKBitmap bitmap;
    SKMatrix currentMatrix = SKMatrix.MakeIdentity();

    // Information for translating and scaling
    long? touchId = null;
    SKPoint pressedLocation;
    SKMatrix pressedMatrix;

    // Information for scaling
    bool isScaling;
    SKPoint pivotPoint;
    ···

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
                // Track only one finger
                if (touchId.HasValue)
                    return;

                // Check if the finger is within the boundaries of the bitmap
                SKRect rect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
                rect = currentMatrix.MapRect(rect);
                if (!rect.Contains(point))
                    return;

                // First assume there will be no scaling
                isScaling = false;

                // If touch is outside interior ellipse, make this a scaling operation
                if (Math.Pow((point.X - rect.MidX) / (rect.Width / 2), 2) +
                    Math.Pow((point.Y - rect.MidY) / (rect.Height / 2), 2) > 1)
                {
                    isScaling = true;
                    float xPivot = point.X < rect.MidX ? rect.Right : rect.Left;
                    float yPivot = point.Y < rect.MidY ? rect.Bottom : rect.Top;
                    pivotPoint = new SKPoint(xPivot, yPivot);
                }

                // Common for either pan or scale
                touchId = args.Id;
                pressedLocation = point;
                pressedMatrix = currentMatrix;
                break;

            case TouchActionType.Moved:
                if (!touchId.HasValue || args.Id != touchId.Value)
                    return;

                SKMatrix matrix = SKMatrix.MakeIdentity();

                // Translating
                if (!isScaling)
                {
                    SKPoint delta = point - pressedLocation;
                    matrix = SKMatrix.MakeTranslation(delta.X, delta.Y);
                }
                // Scaling
                else
                {
                    float scaleX = (point.X - pivotPoint.X) / (pressedLocation.X - pivotPoint.X);
                    float scaleY = (point.Y - pivotPoint.Y) / (pressedLocation.Y - pivotPoint.Y);
                    matrix = SKMatrix.MakeScale(scaleX, scaleY, pivotPoint.X, pivotPoint.Y);
                }

                // Concatenate the matrices
                SKMatrix.PreConcat(ref matrix, pressedMatrix);
                currentMatrix = matrix;
                canvasView.InvalidateSurface();
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                touchId = null;
                break;
        }
    }
}
```

`Moved`動作類型會計算對應於觸控活動手指按下此次螢幕的時間的矩陣。 它實際上在手指第一次按下點陣圖會該矩陣與矩陣。 在調整作業一律是相對於手指接觸到的一個相角。

小型或尾巴的點陣圖，可能會佔用大部分的點陣圖內部的省略符號，並將其保留在縮放點陣圖的角落的小區域中。 您可能會想稍微不同的方法，在此情況下，您可以取代該整個`if`設定的區塊`isScaling`到`true`以下列程式碼：

```csharp
float halfHeight = rect.Height / 2;
float halfWidth = rect.Width / 2;

// Top half of bitmap
if (point.Y < rect.MidY)
{
    float yRelative = (point.Y - rect.Top) / halfHeight;

    // Upper-left corner
    if (point.X < rect.MidX - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Bottom);
    }
    // Upper-right corner
    else if (point.X > rect.MidX + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Bottom);
    }
}
// Bottom half of bitmap
else
{
    float yRelative = (point.Y - rect.MidY) / halfHeight;

    // Lower-left corner
    if (point.X < rect.Left + yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Right, rect.Top);
    }
    // Lower-right corner
    else if (point.X > rect.Right - yRelative * halfWidth)
    {
        isScaling = true;
        pivotPoint = new SKPoint(rect.Left, rect.Top);
    }
}
```

此程式碼有效地將內部的菱形圖案到點陣圖的區域並在角落的四個三角形。 這可讓多更大的區域，來擷取及縮放點陣圖的角落。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [叫用事件的效果](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
