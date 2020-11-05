---
title: 觸控操作
description: 本文說明如何使用矩陣轉換來執行觸控式拖曳、捏合和旋轉，並使用範例程式碼來示範這點。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: A0B8DD2D-7392-4EC5-BFB0-6209407AD650
author: davidbritch
ms.author: dabritch
ms.date: 09/14/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ee69ca1e95f7dcffa60387579e89c3a2d3e985da
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374286"
---
# <a name="touch-manipulations"></a>觸控操作

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_使用矩陣轉換來執行觸控拖曳、捏合和旋轉_

在多點觸控的環境中（例如行動裝置上的環境），使用者通常會使用其手指操作螢幕上的物件。 一般手勢（例如單向拖曳和雙手指縮小）可以移動和縮放物件，甚至可以旋轉它們。 這些手勢通常是使用轉換矩陣來實行，本文將說明如何執行這項操作。

![用來平移、縮放和旋轉的點陣圖](touch-images/touchmanipulationsexample.png)

此處顯示的所有範例都會使用 Xamarin.Forms [**從效果**](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)叫用事件的文章中顯示的觸控追蹤效果。

## <a name="dragging-and-translation"></a>拖曳和轉譯

矩陣轉換最重要的其中一個應用程式是觸控處理。 單一 [`SKMatrix`](xref:SkiaSharp.SKMatrix) 值可以合併一系列觸控作業。 

若為單一手指拖曳，此 `SKMatrix` 值會執行轉譯。 這會在 **點陣圖拖曳** 頁面中示範。 XAML 檔案會在中具現化 `SKCanvasView` Xamarin.Forms `Grid` 。 `TouchEffect`物件已加入至的集合中 `Effects` `Grid` ：

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

理論 `TouchEffect` 上，可以將物件直接新增至的 `Effects` 集合 `SKCanvasView` ，但無法在所有平臺上運作。 因為與 `SKCanvasView` 此設定中的大小相同 `Grid` ，所以將它附加至也可以 `Grid` 運作。

程式碼後端檔案會在其函式的點陣圖資源中載入，並將其顯示在 `PaintSurface` 處理常式中：

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

如果沒有任何進一步的程式碼，此 `SKMatrix` 值一律為識別矩陣，而不會影響點陣圖的顯示。 `OnTouchEffectAction`XAML 檔案中設定之處理常式的目標是要變更矩陣值以反映觸控操作。

`OnTouchEffectAction`處理常式一開始會將 Xamarin.Forms `Point` 值轉換成 SkiaSharp `SKPoint` 值。 這是根據 `Width` (的和屬性進行調整的簡單 `Height` 方式， `SKCanvasView` 其為裝置獨立單位) 和 `CanvasSize` 屬性（以圖元為單位）：

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

當手指先觸及畫面時，就會引發型別的事件 `TouchActionType.Pressed` 。 第一個工作是判斷手指是否觸及點陣圖。 這類工作通常稱為 _點擊測試_ 。 在此情況下，您可以藉由建立 `SKRect` 對應到點陣圖的值、套用矩陣轉換給它，然後 `MapRect` 判斷觸控點是否在轉換的矩形內，來完成點擊測試。

如果是這種情況，則會將 `touchId` 欄位設定為觸控識別碼，並儲存手指位置。

針對 `TouchActionType.Moved` 事件， `SKMatrix` 系統會根據手指的目前位置和手指的新位置來調整值的轉譯因數。 這個新位置會在下次執行時儲存，而且 `SKCanvasView` 會失效。

當您試驗此程式時，請注意，您只能在手指觸及點陣圖顯示區域時拖曳點陣圖。 雖然這項限制對此程式而言並不重要，但在操作多個點陣圖時，是很重要的。

## <a name="pinching-and-scaling"></a>捏合和調整

當兩個手指接觸點陣圖時，您想要發生什麼事？ 如果兩個手指平行移動，您可能會想要讓點陣圖沿著手指移動。 如果雙手指執行縮小或延展作業，您可能會想要將點陣圖旋轉 (，以) 或調整下一節中討論。 縮放點陣圖時，兩個手指最適合維持在相對於點陣圖的相同位置，並據以調整點陣圖。

一次處理兩個手指看似複雜，但請記住， `TouchAction` 處理常式一次只會接收一個手指的相關資訊。 如果有兩個手指正在操作點陣圖，則針對每個事件，一個手指已變更位置，但另一個手指未變更。 在下方的 **點陣圖縮放** 頁面程式碼中，未變更位置的手指稱為「 _資料透視_ 點」，因為轉換是相對於該點。

此程式與上一個程式之間的一項差異在於，必須儲存多個觸控識別碼。 字典是用於此用途，其中觸控識別碼為字典索引鍵，而字典值為該手指的目前位置：

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

動作的處理 `Pressed` 幾乎與上一個程式相同，不同之處在于識別碼和觸控點會新增至字典。 `Released`和 `Cancelled` 動作會移除字典專案。

`Moved`但是，動作的處理會更複雜。 如果只涉及一個手指，則處理與先前的程式非常類似。 針對兩個或更多手指，程式也必須從包含未移動之手指的字典中取得資訊。 其運作方式是將字典索引鍵複製到陣列，然後將第一個索引鍵與要移動的手指的識別碼進行比較。 這可讓程式取得對應到未移動之手指的軸點。

接下來，程式會計算相對於 pivot 點的新手指位置的兩個向量，以及相對於 pivot 點的舊指標位置。 這些向量的比例是調整因素。 由於零除是有可能的，因此必須檢查這些值是否為無限值或 NaN (不是) 值的數位。 如果一切正常，縮放轉換會與 `SKMatrix` 儲存為欄位的值串連。

當您試驗此頁面時，您會注意到您可以使用一或兩個手指來拖曳點陣圖，或使用兩個手指進行縮放。 調整 _是非_ 等的，這表示水準和垂直方向的縮放比例可能不同。 這會扭曲外觀比例，但也可讓您翻轉點陣圖以建立鏡像影像。 您也可能會發現，您可以將點陣圖壓縮成零的維度，而它就會消失。 在實際執行的程式碼中，您會想要防範這種情況。

## <a name="two-finger-rotation"></a>雙手指旋轉

**點陣圖旋轉** 頁面可讓您使用兩個手指來旋轉或 isotropic 縮放。 點陣圖一律會保留其正確的外觀比例。 針對這兩個工作，使用兩個手指來旋轉和非雙向調整的效果並不佳，因為手指的移動非常類似。

此計畫中的第一個重大差異在於點擊測試邏輯。 先前的程式使用的 `Contains` 方法 `SKRect` 來判斷觸控點是否在對應到點陣圖的已轉換矩形內。 但是當使用者操作點陣圖時，點陣圖可能會旋轉，而且 `SKRect` 無法正確地表示旋轉的矩形。 您可能會擔心點擊測試邏輯必須在該情況下實作為複雜的分析幾何。

不過，您可以使用快捷方式：判斷某個點是否位於已轉換矩形的界限內，是否與判斷反向轉換點是否位於未轉換矩形的界限內。 這是較容易的計算，而邏輯可以繼續使用方便的 `Contains` 方法：

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

事件的邏輯 `Moved` 就像前一個程式一樣開始。 名為和的兩個向量 `oldVector` `newVector` 是根據移動手指的上一個和目前的點以及 unmoving 手指的旋轉點來計算。 但是，系統會決定這些向量的角度，而差異在於旋轉角度。

您也可以進行縮放，因此舊的向量會根據旋轉角度旋轉。 這兩個向量的相對大小現在是調整比例。 請注意，相同的 `scale` 值會用於水準和垂直縮放比例，以便 isotropic 調整。 此 `matrix` 欄位是由旋轉矩陣和尺規矩陣所調整。

如果您的應用程式需要針對單一點陣圖 (或其他物件) 來執行觸控處理，您可以為自己的應用程式調整這三個範例中的程式碼。 但是，如果您需要針對多個點陣圖執行觸控處理，您可能會想要將這些觸控作業封裝在其他類別中。

## <a name="encapsulating-the-touch-operations"></a>封裝觸控作業

[ **觸控操作** ] 頁面會示範單一點陣圖的觸控操作，但會使用多個封裝以上所示邏輯的其他檔案。 這些檔案中的第一個是 [`TouchManipulationMode`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationMode.cs) 列舉，指出您將看到的程式碼所執行的不同類型觸控操作：

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

`PanOnly` 是使用轉譯所執行的單向拖曳。 所有後續的選項也包含移動，但牽涉到兩個手指： `IsotropicScale` 是一種縮小作業，會在水準和垂直方向中平均地調整物件。 `AnisotropicScale` 允許不相等的調整。

此 `ScaleRotate` 選項適用于雙手指縮放和旋轉。 調整規模為 isotropic。 如先前所述，使用非等向調整來執行雙手指旋轉會有問題，因為手指移動本質上是相同的。

`ScaleDualRotate`選項會新增單向旋轉。 當單一手指拖曳物件時，拖曳的物件會先圍繞其中心旋轉，如此一來，物件的中心就會與拖曳向量對齊。

[**TouchManipulationPage .xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml)檔案包含 `Picker` 具有列舉成員的 `TouchManipulationMode` ：

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

到底部是 `SKCanvasView` ，並 `TouchEffect` 附加至括住它的單一資料格 `Grid` 。

[**TouchManipulationPage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationPage.xaml.cs)程式碼後端檔案具有 `bitmap` 欄位，但其類型不是 `SKBitmap` 。 此類型 `TouchManipulationBitmap` (您很快就會看到) 的類別：

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

此函式會將物件具現化 `TouchManipulationBitmap` ，並傳遞至 `SKBitmap` 從內嵌資源取得的函式。 此函式會將 `Mode` `TouchManager` 物件之屬性的屬性設定 `TouchManipulationBitmap` 為列舉的成員，以結束 `TouchManipulationMode` 。

的 `SelectedIndexChanged` 處理常式 `Picker` 也會設定這個 `Mode` 屬性：

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

在 `TouchAction` XAML 檔案中具現化的處理常式會 `TouchEffect` 呼叫 `TouchManipulationBitmap` 名為和的兩個方法 `HitTest` `ProcessTouchEvent` ：

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

如果 `HitTest` 方法傳回 `true` &mdash; 表示手指觸及點陣圖所佔用區域內的螢幕，則 &mdash; 會將觸控識別碼新增至 `TouchIds` 集合。 此識別碼代表該手指的觸控事件順序，直到手指從螢幕中提起為止。 如果有多個手指接觸點陣圖，則 `touchIds` 集合會包含每個手指的 TOUCH ID。

`TouchAction`處理常式也會呼叫 `ProcessTouchEvent` 中的類別 `TouchManipulationBitmap` 。 這是某些 (但不是所有實際觸控處理的) 。

[`TouchManipulationBitmap`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationBitmap.cs)類別是的包裝函式類別 `SKBitmap` ，其包含用來呈現點陣圖和處理觸控事件的程式碼。 它可與類別中的更一般化程式碼搭配使用 `TouchManipulationManager` (您很快就會看到) 。

此函式會儲存並具現 `TouchManipulationBitmap` `SKBitmap` 化兩個屬性 `TouchManager` ：型別的屬性 `TouchManipulationManager` 和 `Matrix` 型別的屬性 `SKMatrix` ：

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

這個 `Matrix` 屬性是從所有觸控活動產生的累積轉換。 如您所見，每個觸控事件都會解析成矩陣，然後與 `SKMatrix` 屬性儲存的值串連 `Matrix` 。

`TouchManipulationBitmap`物件會在其方法中自行繪製 `Paint` 。 引數是 `SKCanvas` 物件。 這 `SKCanvas` 可能已經套用轉換，所以方法會將 `Paint` `Matrix` 與點陣圖相關聯的屬性串連至現有的轉換，並在完成時還原畫布：

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

`HitTest` `true` 如果使用者在點陣圖界限內的某個點觸及螢幕，方法會傳回。 這會使用稍早在 **點陣圖旋轉** 頁面中顯示的邏輯：

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

中的第二個公用方法 `TouchManipulationBitmap` 是 `ProcessTouchEvent` 。 當呼叫這個方法時，就已經建立觸控事件屬於這個特定的點陣圖。 方法會維護物件的字典 [`TouchManipulationInfo`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/TouchManipulationInfo.cs) ，而這只是前一個點和每個手指的新點：

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

在 `Moved` 和 `Released` 事件中，方法會呼叫 `Manipulate` 。 在這些情況下，會 `touchDictionary` 包含一或多個 `TouchManipulationInfo` 物件。 如果 `touchDictionary` 包含一個專案，則 `PreviousPoint` 和值可能不相等， `NewPoint` 且代表手指的移動。 如果有多個手指觸及點陣圖，則字典會包含一個以上的專案，但只有其中一個專案具有不同 `PreviousPoint` 和 `NewPoint` 值。 所有其餘的都有相等 `PreviousPoint` 和 `NewPoint` 值。

這是很重要的： `Manipulate` 方法可以假設它只處理一個手指的移動。 這次呼叫時，其他手指都不會移動，而且如果它們真的移動 (可能) ，這些移動將在未來的呼叫中處理 `Manipulate` 。

`Manipulate`為了方便起見，方法會先將字典複製到陣列。 它會忽略前兩個專案以外的任何專案。 如果有兩個以上的手指試圖操作點陣圖，則會忽略其他手指。 `Manipulate` 是的最終成員 `TouchManipulationBitmap` ：

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

如果一個手指正在操作點陣圖，則會 `Manipulate` 呼叫 `OneFingerManipulate` 物件的方法 `TouchManipulationManager` 。 雙手指會呼叫 `TwoFingerManipulate` 。 這些方法的引數相同： `prevPoint` 和 `newPoint` 引數代表正在移動的手指。 但是 `pivotPoint` 這兩個呼叫的引數不同：

若是單向操作，則 `pivotPoint` 為點陣圖的中心。 這是為了允許單向旋轉。 針對雙向操作，事件表示只移動一個手指，因此 `pivotPoint` 是不移動的手指。

在這兩種情況下， `TouchManipulationManager` `SKMatrix` 會傳回值，此方法會與 `Matrix` 用來轉譯點陣圖的目前屬性進行串連 `TouchManipulationPage` 。

`TouchManipulationManager` 是一般化的，且不使用其他檔案，但除外 `TouchManipulationMode` 。 您可以使用這個類別，而不需要在自己的應用程式中變更。 它會定義一個 `TouchManipulationMode` 類型的單一屬性：

```csharp
class TouchManipulationManager
{
    public TouchManipulationMode Mode { set; get; }
    ...
}
```

不過，您可能會想要避免這個 `AnisotropicScale` 選項。 使用這個選項來管理點陣圖非常簡單，讓其中一個縮放比例變成零。 這會讓點陣圖消失，而且永遠不會傳回。 如果您真正需要進行的調整，您會想要增強邏輯以避免不想要的結果。

`TouchManipulationManager` 利用向量，但由於 `SKVector` SkiaSharp 中沒有結構，因此 `SKPoint` 會改用。 `SKPoint` 支援減法運算子，而且可以將結果視為向量。 唯一需要新增的向量特定邏輯是 `Magnitude` 計算：

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

每次選取旋轉時，即會先處理手指和雙手指操作方法。 如果偵測到任何旋轉，則會有效地移除旋轉元件。 剩下的內容會被解釋為移動和縮放。

以下是 `OneFingerManipulate` 方法。 如果未啟用單向旋轉，則邏輯很簡單， &mdash; 只是使用上一個點和新點來建立名為 `delta` 的向量，以精確對應至轉譯。 在啟用單向旋轉的情況下，方法會使用距離軸的角度， (點陣圖) 的中心，再到上一個點，並使用新點來建立旋轉矩陣：

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

在 `TwoFingerManipulate` 方法中，資料透視點是指不會在這個特定觸控事件中移動的手指位置。 旋轉與單向旋轉非常類似，接著 `oldVector` 會針對旋轉調整以先前的點為基礎的向量 () 。 其餘的移動會被視為調整：

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

您會注意到這個方法沒有明確的翻譯。 不過， `MakeRotation` 和 `MakeScale` 方法都是以 pivot 點為基礎，而且包含隱含轉譯。 如果您在點陣圖上使用兩個手指，並以相同方向拖曳它們， `TouchManipulation` 將會取得一連串觸控事件，在兩個手指之間交替。 由於每個手指會與另一個手指移動、縮放或旋轉結果相反，但會由另一個手指的移動否定，且結果為轉譯。

「 **觸控操作** 」頁面的其餘部分是 `PaintSurface` 程式碼後端檔案中的處理常式 `TouchManipulationPage` 。 這會呼叫的 `Paint` 方法，此方法會 `TouchManipulationBitmap` 套用代表累積觸控活動的矩陣：

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

`PaintSurface`處理常式會藉由顯示 `MatrixDisplay` 顯示累積觸控矩陣的物件來結束：

[![[觸控操作] 頁面的三重螢幕擷取畫面](touch-images/touchmanipulation-small.png)](touch-images/touchmanipulation-large.png#lightbox "[觸控操作] 頁面的三重螢幕擷取畫面")

## <a name="manipulating-multiple-bitmaps"></a>操作多個點陣圖

隔離觸控處理常式代碼（例如和）的優點之一， `TouchManipulationBitmap` `TouchManipulationManager` 就是在允許使用者操作多個點陣圖的程式中重複使用這些類別的能力。

**點陣圖散佈圖** 頁面會示範如何完成這項操作。 類別不會定義類型的欄位，而是會 `TouchManipulationBitmap` [`BitmapScatterPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BitmapScatterViewPage.xaml.cs) 定義 `List` 點陣圖物件的：

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

此函式會在所有可做為內嵌資源的點陣圖中載入，並將它們新增至 `bitmapCollection` 。 請注意， `Matrix` 屬性會在每個 `TouchManipulationBitmap` 物件上初始化，因此每個點陣圖的左上角會以100圖元位移。

`BitmapScatterView`頁面也需要處理多個點陣圖的觸控事件。 `List` `TouchManipulationBitmap` 此程式需要字典，而不是定義目前操作之物件的觸控識別碼：

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

請注意 `Pressed` 邏輯如何 `bitmapCollection` 反向迴圈。 點陣圖通常會互相重迭。 集合中稍後的點陣圖會以視覺化方式位於集合中稍早的點陣圖上方。 如果在螢幕上按下的手指有多個點陣圖，則最上方的點陣圖必須是該手指操作的點陣圖。

另外也請注意， `Pressed` 邏輯會將該點陣圖移至集合的結尾，使其以視覺化方式移至其他點陣圖的堆積上方。

在 `Moved` 和 `Released` 事件中， `TouchAction` 處理常式會呼叫 `ProcessingTouchEvent` 中的方法， `TouchManipulationBitmap` 就像先前的程式一樣。

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

程式碼會在集合中執行迴圈，並顯示從集合開頭到結尾的點陣圖堆積：

[![點陣圖散佈視圖頁面的三重螢幕擷取畫面](touch-images/bitmapscatterview-small.png)](touch-images/bitmapscatterview-large.png#lightbox "點陣圖散佈視圖頁面的三重螢幕擷取畫面")

## <a name="single-finger-scaling"></a>Single-Finger 調整

調整作業通常需要使用雙手指的縮小手勢。 不過，您可以藉由讓手指移動點陣圖的角落，以單一手指進行調整。

這會在 **單一手指縮放** 頁面中示範。 因為這個範例使用的縮放比例與類別中所執行的不同 `TouchManipulationManager` ，所以不會使用該類別或 `TouchManipulationBitmap` 類別。 相反地，所有觸控邏輯都是在程式碼後端檔案中。 這是比平常更簡單的邏輯，因為它一次只會追蹤一個手指，而只會忽略可能觸及螢幕的任何次要手指。

[**SingleFingerCornerScale**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml)會將類別具現化 `SKCanvasView` ，並建立用 `TouchEffect` 來追蹤觸控事件的物件：

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

[**SingleFingerCornerScalePage.xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/SingleFingerCornerScalePage.xaml.cs)檔案會從 **媒體** 目錄載入點陣圖資源，並使用 `SKMatrix` 定義為欄位的物件來顯示它：

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

此 `SKMatrix` 物件是由如下所示的觸控邏輯修改。

程式碼後端檔案的其餘部分是 `TouchEffect` 事件處理常式。 首先，將手指的目前位置轉換成 `SKPoint` 值。 針對 `Pressed` 動作類型，處理常式會檢查沒有其他手指觸及螢幕，且手指位於點陣圖的界限內。

程式碼的重要部分是 `if` 涉及兩個方法呼叫的語句 `Math.Pow` 。 此數學運算會檢查手指位置是否位於填滿點陣圖的省略號之外。 如果是，就是調整規模作業。 手指接近點陣圖的其中一個角落，而資料透視點則判定為相反的角落。 如果手指在這個橢圓形內，它就是一般的移動操作：

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

`Moved`動作類型會計算對應至觸控活動的矩陣（從手指按下畫面到目前為止）。 它會將該矩陣串連在手指首次按下點陣圖時生效的矩陣。 調整作業一律相對於與手指接觸的邊角相反。

若為小型或 oblong 點陣圖，內部橢圓形可能會佔用大部分的點陣圖，並在角落留下微社區域以縮放點陣圖。 您可能會偏好稍有不同的方法，在此情況下，您可以使用下列程式碼來取代 `if` 將設定為的整個區塊 `isScaling` `true` ：

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

這段程式碼可有效地將點陣圖區域分成內部鑽石形，以及四個三角形。 這可讓角落的更大區域抓取和縮放點陣圖。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [從效果叫用事件](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)