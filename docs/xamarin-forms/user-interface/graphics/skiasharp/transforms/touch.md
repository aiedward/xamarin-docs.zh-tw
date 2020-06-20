---
title: 觸控操作
description: 本文說明如何使用矩陣轉換來執行觸控拖曳、捏合和旋轉，並使用範例程式碼示範這點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f5738f376e2a814ef1af455178c2e68135184402
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140162"
---
# <a name="touch-manipulations"></a>觸控操作

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用矩陣轉換來執行觸控拖曳、捏合和旋轉_

在像是行動裝置上的多點觸控環境中，使用者通常會使用手指來操作螢幕上的物件。 一般手勢（例如單向拖曳和雙手指延展）可以移動和縮放物件，甚至是旋轉它們。 這些手勢通常會使用轉換矩陣來實行，而本文將說明如何執行這項操作。

![](touch-images/touchmanipulationsexample.png "A bitmap subjected to translation, scaling, and rotation")

此處顯示的所有範例都會使用 Xamarin.Forms [**從效果**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)叫用事件一文所呈現的觸控追蹤效果。

## <a name="dragging-and-translation"></a>拖曳和轉譯

矩陣轉換的其中一個最重要的應用程式就是觸控處理。 單一 [`SKMatrix`](xref:SkiaSharp.SKMatrix) 值可以合併一系列的觸控作業。 

若為單一手指拖曳，此 `SKMatrix` 值會執行轉譯。 這會在**點陣圖拖曳**頁面中示範。 XAML 檔案會在中具現化 `SKCanvasView` Xamarin.Forms `Grid` 。 `TouchEffect`物件已加入至的集合中 `Effects` `Grid` ：

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

理論上， `TouchEffect` 物件可以直接加入至的 `Effects` 集合 `SKCanvasView` ，但在所有平臺上都不適用。 因為與 `SKCanvasView` 此設定中的大小相同 `Grid` ，所以將它附加至也會 `Grid` 運作。

程式碼後置檔案會在其函式中載入點陣圖資源，並將它顯示在 `PaintSurface` 處理常式中：

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

如果沒有任何進一步的程式碼，此 `SKMatrix` 值一律是識別矩陣，而不會影響點陣圖的顯示。 在 `OnTouchEffectAction` XAML 檔案中設定處理常式的目標是要改變矩陣值，以反映觸控操作。

`OnTouchEffectAction`處理常式一開始會將 Xamarin.Forms `Point` 值轉換成 SkiaSharp `SKPoint` 值。 這是根據的 `Width` 和 `Height` 屬性 `SKCanvasView` （也就是裝置獨立單位）和屬性（以圖元為單位）進行調整的簡單 `CanvasSize` 方式：

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

當手指第一次接觸畫面時， `TouchActionType.Pressed` 會引發類型為的事件。 第一個工作是判斷手指是否觸及點陣圖。 這類工作通常稱為_點擊測試_。 在此情況下，可以藉由建立 `SKRect` 對應到點陣圖的值，將矩陣轉換套用至它，然後 `MapRect` 判斷觸控點是否在轉換後的矩形內，來完成點擊測試。

如果是這種情況，則 `touchId` 會將欄位設定為 TOUCH ID，並儲存手指位置。

若為 `TouchActionType.Moved` 事件， `SKMatrix` 會根據手指的目前位置和手指的新位置來調整值的轉譯因素。 新位置會在下一次儲存，且 `SKCanvasView` 會失效。

當您嘗試使用此程式時，請注意，當手指觸及顯示點陣圖的區域時，您只能拖曳點陣圖。 雖然這項限制對此程式來說並不重要，但在操作多個點陣圖時，這一點就變得很重要。

## <a name="pinching-and-scaling"></a>捏合和調整

當兩個手指接觸點陣圖時，會發生什麼事？ 如果兩個手指以平行方式移動，您可能會想要讓點陣圖與手指一起移動。 如果這兩個手指執行縮小或延展作業，您可能會想要旋轉點陣圖（將在下一節中討論）或縮放。 調整點陣圖時，最適合讓兩個手指保持與點陣圖相對的相同位置，並據此調整點陣圖。

一次處理兩個手指似乎很複雜，但請記住， `TouchAction` 處理常式一次只會接收一個手指的相關資訊。 如果兩個手指正在操作點陣圖，則在每個事件中，一個手指已變更位置，但另一個則未變更。 在下面的**點陣圖調整**頁面程式碼中，未變更位置的手指稱為「 _pivot_點」，因為轉換是相對於該點。

此程式與上一個程式之間的差異在於，必須儲存多個 touch Id。 字典是用於此用途，其中 touch ID 是字典索引鍵，而 dictionary 值是該手指的目前位置：

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
                            // If something bad hasn't happened, calculate a scale and translation matrix
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

動作的處理 `Pressed` 幾乎與上一個程式相同，不同之處在于識別碼和觸控點會加入字典中。 `Released`和 `Cancelled` 動作會移除字典專案。

`Moved`不過，此動作的處理會更複雜。 如果只涉及一個手指，則處理常式會與前一個程式非常類似。 針對兩個以上的手指，此程式也必須從包含未移動手指的字典中取得資訊。 其運作方式是將字典索引鍵複製到陣列中，然後比較第一個索引鍵與要移動之手指的識別碼。 這可讓程式取得對應于不移動之手指的軸點。

接下來，程式會計算相對於軸點的新手指位置的兩個向量，以及相對於資料軸點的舊手指位置。 這些向量的比例是縮放因素。 因為零除是可能的，所以必須檢查是否有無限值或 NaN （不是數位）值。 如果一切順利，縮放轉換會與 `SKMatrix` 儲存為欄位的值串連。

當您嘗試使用此頁面時，您會注意到，您可以使用一或兩個手指拖曳點陣圖，或使用兩個手指來調整它的大小。 縮放_是「_ 非高度」，這表示水準和垂直方向的縮放比例可能不同。 這會扭曲外觀比例，但也可讓您翻轉點陣圖來建立鏡像影像。 您也可能會發現，您可以將點陣圖縮小為零個維度，而且它會消失。 在實際執行的程式碼中，您會想要防範這種情況。

## <a name="two-finger-rotation"></a>雙手指旋轉

[**點陣圖旋轉**] 頁面可讓您使用兩個手指旋轉或 isotropic 縮放。 點陣圖一律會保持正確的外觀比例。 同時使用兩個手指來進行旋轉和非雙向調整，並不會有太大的作用，因為這兩個工作的手指移動非常類似。

此程式的第一個主要差異是點擊測試邏輯。 先前的程式會使用的 `Contains` 方法 `SKRect` 來判斷觸控點是否在對應到點陣圖的轉換後矩形中。 但是當使用者操作點陣圖時，點陣圖可能會旋轉，而且 `SKRect` 無法正確呈現旋轉的矩形。 您可能會擔心點擊測試邏輯必須在這種情況下執行，而不是複雜的分析幾何。

不過，有快捷方式可用：判斷某個點是否位於轉換後矩形的界限內，是否與判斷反向轉換的點是否位於未轉換矩形的界限內相同。 這是比較容易的計算，而邏輯可以繼續使用便利的 `Contains` 方法：

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

事件的邏輯 `Moved` 開始就像上一個程式。 名為和的兩個向量 `oldVector` `newVector` 是根據移動手指的目前點和 unmoving 手指的軸點來計算。 但是會決定這些向量的角度，而差異就是旋轉角度。

可能也會涉及調整，因此舊的向量會根據旋轉角度旋轉。 這兩個向量的相對大小現在是縮放比例。 請注意，相同的 `scale` 值會用於水準和垂直縮放比例，以 isotropic 縮放比例。 `matrix`旋轉矩陣和尺規矩陣都會調整此欄位。

如果您的應用程式需要針對單一點陣圖（或其他物件）執行觸控處理，您可以針對自己的應用程式，調整這三個範例中的程式碼。 但是，如果您需要針對多個點陣圖執行觸控處理，您可能會想要在其他類別中封裝這些觸控作業。

## <a name="encapsulating-the-touch-operations"></a>封裝觸控作業

**觸控操作**頁面會示範單一點陣圖的觸控操作，但使用數個其他封裝上述邏輯的其他檔案。 這些檔案中的第一個是 [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) 列舉，這表示您會看到的程式碼所執行的不同類型的觸控操作：

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

`PanOnly`是以轉譯方式執行的單向拖曳。 所有後續的選項也包括 [移動]，但牽涉到兩個手指： `IsotropicScale` 是一種縮小作業，會導致水準和垂直方向的物件等距縮放。 `AnisotropicScale`允許不相等的縮放比例。

此 `ScaleRotate` 選項適用于雙手指縮放和旋轉。 調整規模為 isotropic。 如先前所述，使用非型式調整來執行雙手指旋轉會有問題，因為手指移動基本上是相同的。

`ScaleDualRotate`選項會新增一方旋轉。 當單一手指拖曳物件時，拖曳的物件會先沿著其中心旋轉，使物件的中心以拖曳向量向上移動。

[**TouchManipulationPage**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)包含 `Picker` 具有列舉成員的 `TouchManipulationMode` ：

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

向下的是 `SKCanvasView` ，而 `TouchEffect` 附加至括住它的單一資料格 `Grid` 。

[**TouchManipulationPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)程式碼後置檔案有 `bitmap` 欄位，但其類型不是 `SKBitmap` 。 類型為 `TouchManipulationBitmap` （您很快就會看到一個類別）：

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

此程式會將物件具現化 `TouchManipulationBitmap` ，並傳遞至 `SKBitmap` 從內嵌資源取得的函式。 此函式會將 `Mode` 物件之屬性的屬性設定 `TouchManager` `TouchManipulationBitmap` 為列舉的成員來結束 `TouchManipulationMode` 。

的 `SelectedIndexChanged` 處理常式 `Picker` 也會設定此 `Mode` 屬性：

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

在 `TouchAction` XAML 檔案中具現化之的處理常式會 `TouchEffect` 呼叫中 `TouchManipulationBitmap` 名為和的兩個方法 `HitTest` `ProcessTouchEvent` ：

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

如果 `HitTest` 方法傳回 `true` &mdash; 表示手指已觸及點陣圖所佔用區域內的螢幕，則 &mdash; touch ID 會加入至 `TouchIds` 集合。 此識別碼代表該手指的觸控事件順序，直到手指從螢幕提起為止。 如果多個手指接觸點陣圖，則 `touchIds` 集合會包含每個手指的觸控識別碼。

`TouchAction`處理常式也會呼叫 `ProcessTouchEvent` 中的類別 `TouchManipulationBitmap` 。 這是實際觸控處理的部分（但不是全部）發生的地方。

[`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)類別是的包裝函式類別 `SKBitmap` ，其中包含用來呈現點陣圖和處理觸控事件的程式碼。 它會與類別中更一般化的程式碼搭配運作 `TouchManipulationManager` （您很快就會看到）。

此函式會儲存並具現 `TouchManipulationBitmap` `SKBitmap` 化兩個屬性，也就是 `TouchManager` 類型的屬性 `TouchManipulationManager` 和 `Matrix` 類型的屬性 `SKMatrix` ：

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

此 `Matrix` 屬性是所有觸控活動所產生的累積轉換。 如您所見，每個觸控事件都會解析成矩陣，然後再與 `SKMatrix` 屬性所儲存的值串連 `Matrix` 。

`TouchManipulationBitmap`物件會在其方法中繪製本身 `Paint` 。 引數是 `SKCanvas` 物件。 這 `SKCanvas` 可能已經套用轉換，因此方法會將 `Paint` `Matrix` 與點陣圖相關聯的屬性串連至現有的轉換，並在畫布完成時將其還原：

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

`HitTest` `true` 如果使用者在點陣圖界限內的某個點觸及螢幕，則方法會傳回。 這會使用稍早在 [**點陣圖旋轉**] 頁面中顯示的邏輯：

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

中的第二個公用方法 `TouchManipulationBitmap` 是 `ProcessTouchEvent` 。 呼叫這個方法時，已經建立觸控事件屬於這個特定點陣圖。 方法會維護物件的字典 [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) ，這只是先前的點和每個手指的新點：

```csharp
class TouchManipulationInfo
{
    public SKPoint PreviousPoint { set; get; }

    public SKPoint NewPoint { set; get; }
}
```

以下是字典和 `ProcessTouchEvent` 方法本身：

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

在 `Moved` 和 `Released` 事件中，方法會呼叫 `Manipulate` 。 在這些時間，會 `touchDictionary` 包含一或多個 `TouchManipulationInfo` 物件。 如果 `touchDictionary` 包含一個專案，可能是 `PreviousPoint` 和 `NewPoint` 值不相等，而且代表手指的移動。 如果有多個手指觸及點陣圖，則字典包含一個以上的專案，但只有其中一個專案具有不同的 `PreviousPoint` 和 `NewPoint` 值。 所有其餘的都有相等的 `PreviousPoint` 和 `NewPoint` 值。

這很重要： `Manipulate` 方法可以假設它只處理一個手指的移動。 在這個呼叫時，其他手指都不會移動，而且如果它們真的在移動中（可能的話），這些移動將會在未來的呼叫中處理 `Manipulate` 。

`Manipulate`方法會先將字典複製到陣列以方便使用。 它會忽略前兩個專案以外的任何內容。 如果有兩個以上的手指嘗試操作點陣圖，則會略過其他人。 `Manipulate`是的最終成員 `TouchManipulationBitmap` ：

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

如果其中一個手指正在操作點陣圖，會 `Manipulate` 呼叫 `OneFingerManipulate` 物件的方法 `TouchManipulationManager` 。 針對兩個手指，它會呼叫 `TwoFingerManipulate` 。 這些方法的引數相同： `prevPoint` 和 `newPoint` 引數代表正在移動的手指。 但 `pivotPoint` 這兩個呼叫的引數不同：

若為單向操作，則 `pivotPoint` 為點陣圖的中心。 這是為了允許單向旋轉。 若為雙向操作，事件表示只移動一個手指，使 `pivotPoint` 成為不移動的手指。

在這兩種情況下，都會傳回 `TouchManipulationManager` `SKMatrix` 值，方法 `Matrix` 會與用來轉譯點陣圖的目前屬性進行串連 `TouchManipulationPage` 。

`TouchManipulationManager`已一般化，而且不會使用任何其他檔案（除外） `TouchManipulationMode` 。 您可以在自己的應用程式中使用這個類別，而不需要變更。 它會定義一個 `TouchManipulationMode` 類型的單一屬性：

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

不過，您可能會想要避免這個 `AnisotropicScale` 選項。 使用此選項來操作點陣圖非常簡單，因為其中一個縮放因數會變成零。 這會讓點陣圖消失，而不會傳回。 如果您真的需要進行非快速調整，您會想要增強邏輯，以避免產生非預期的結果。

`TouchManipulationManager`會使用向量，但由於 `SKVector` SkiaSharp 中沒有結構，因此 `SKPoint` 會改用。 `SKPoint`支援減法運算子，而且可以將結果視為向量。 唯一需要新增的向量特定邏輯是 `Magnitude` 計算：

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

只要選取了旋轉，一方和兩指的操作方法就會先處理旋轉。 如果偵測到任何旋轉，則會有效地移除旋轉元件。 剩餘的內容會被視為移動流覽和縮放。

以下是 `OneFingerManipulate` 方法。 如果尚未啟用一方旋轉，則邏輯很簡單， &mdash; 只是使用先前的點和新點來建立一個名為的向量，其對應的方式 `delta` 相當於轉譯。 啟用單向旋轉之後，方法會使用從資料透視點（點陣圖中心）到上一個點的角度，以及用來建立旋轉矩陣的新點：

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

在 `TwoFingerManipulate` 方法中，資料透視點是指未在此特定觸控事件中移動的手指位置。 旋轉非常類似于一方旋轉，接著會針對旋轉調整名為的向量 `oldVector` （根據上一個點）。 其餘的移動會被視為調整：

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

您會注意到這個方法沒有明確的轉譯。 不過， `MakeRotation` 和 `MakeScale` 方法都是以資料軸點為基礎，其中包含隱含轉譯。 如果您在點陣圖上使用兩個手指，並以相同的方向拖曳它們，則 `TouchManipulation` 會在兩個手指之間取得一系列的觸控事件。 當每個手指相對於另一個、縮放或旋轉結果，但它已否定另一個手指的移動，且結果為「轉譯」時。

**觸控操作**頁面唯一剩餘的部分是 `PaintSurface` 程式碼後置檔案中的處理常式 `TouchManipulationPage` 。 這會呼叫 `Paint` 的方法 `TouchManipulationBitmap` ，它會套用代表累積觸控活動的矩陣：

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

`PaintSurface`處理常式會藉由顯示 `MatrixDisplay` 物件顯示累積的觸控矩陣來結束：

[![](touch-images/touchmanipulation-small.png "Triple screenshot of the Touch Manipulation page")](touch-images/touchmanipulation-large.png#lightbox "Triple screenshot of the Touch Manipulation page")

## <a name="manipulating-multiple-bitmaps"></a>操作多個點陣圖

在類別（例如和）中隔離觸控處理常式代碼的優點之一， `TouchManipulationBitmap` `TouchManipulationManager` 是能夠在允許使用者操作多個點陣圖的程式中重複使用這些類別。

[**點陣圖散佈圖**] 頁面會示範如何完成此作業。 類別不會定義類型的欄位，而是會 `TouchManipulationBitmap` [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) 定義 `List` 點陣圖物件的：

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

此函式會在所有可做為內嵌資源的點陣圖中載入，並將其新增至 `bitmapCollection` 。 請注意， `Matrix` 屬性會在每個 `TouchManipulationBitmap` 物件上初始化，因此每個點陣圖的左上角會位移100圖元。

此 `BitmapScatterView` 頁面也需要處理多個點陣圖的觸控事件。 此程式不會定義 `List` 目前操作物件的觸控識別碼，而是 `TouchManipulationBitmap` 需要字典：

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

請注意 `Pressed` 邏輯如何 `bitmapCollection` 反向迴圈。 點陣圖通常會彼此重迭。 集合中稍後的點陣圖會以視覺方式位於集合中稍早的點陣圖上方。 如果在螢幕上按下手指中有多個點陣圖，則最上層的一個就必須是該手指所操作的一個。

另請注意， `Pressed` 邏輯會將該點陣圖移至集合結尾，使其能以視覺化方式移至其他點陣圖的最上方。

在 `Moved` 和 `Released` 事件中， `TouchAction` 處理常式會 `ProcessingTouchEvent` 在中呼叫方法， `TouchManipulationBitmap` 就像先前的程式一樣。

最後， `PaintSurface` 處理常式會呼叫 `Paint` 每個物件的方法 `TouchManipulationBitmap` ：

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

程式碼會在集合中迴圈，並顯示從集合開頭到結尾的一堆點陣圖：

[![](touch-images/bitmapscatterview-small.png "Triple screenshot of the Bitmap Scatter View page")](touch-images/bitmapscatterview-large.png#lightbox "Triple screenshot of the Bitmap Scatter View page")

## <a name="single-finger-scaling"></a>單一手指調整

調整作業通常需要使用兩個手指的縮小手勢。 不過，您可以讓手指移動點陣圖的角落，以單一手指來執行調整。

這會在 [**單一手指角落尺規**] 頁面中示範。 因為此範例所使用的縮放類型與類別中所實的不同 `TouchManipulationManager` ，所以不會使用該類別或 `TouchManipulationBitmap` 類別。 相反地，所有觸控邏輯都會在程式碼後置檔案中。 這是比平常更簡單的邏輯，因為它一次只會追蹤一個手指，而只會忽略任何可能觸及螢幕的次要手指。

[**SingleFingerCornerScale**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml)會將類別具現化 `SKCanvasView` ，並建立 `TouchEffect` 物件來追蹤觸控事件：

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

[**SingleFingerCornerScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs)檔案會從**媒體**目錄載入點陣圖資源，並使用 `SKMatrix` 定義為欄位的物件來顯示它：

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

這個 `SKMatrix` 物件是由觸控邏輯修改，如下所示。

程式碼後置檔案的其餘部分是 `TouchEffect` 事件處理常式。 一開始會將手指的目前位置轉換為 `SKPoint` 值。 對於 `Pressed` 動作類型，處理常式會檢查沒有其他手指觸及螢幕，而且手指在點陣圖的界限內。

程式碼的重要部分是 `if` 涉及兩個方法呼叫的語句 `Math.Pow` 。 此數學運算會檢查手指位置是否在填滿點陣圖的橢圓形外。 若是如此，這就是調整作業。 手指位於點陣圖的其中一個角落附近，而資料中心點則是由另一個角落決定。 如果手指在此橢圓形內，這就是一般的移動操作：

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

`Moved`動作類型會計算對應于從手指按下螢幕到這段時間的觸控活動的矩陣。 它會在手指第一次按下點陣圖時，將該矩陣與作用中的矩陣串連。 縮放作業一律相對於手指接觸的邊角。

針對小型或 oblong 點陣圖，內部橢圓形可能會佔用大部分的點陣圖，並將社區域留在角落以縮放點陣圖。 您可能會偏好某種不同的方法，在此情況下，您可以 `if` 使用下列程式碼來取代設定為的整個區塊 `isScaling` `true` ：

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

這段程式碼會將點陣圖的區域有效地分成內部菱形圖形，並將四個三角形放在角落。 這可讓角落的更大區域抓取和縮放點陣圖。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [從效果叫用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
