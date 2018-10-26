---
title: 裁剪 SkiaSharp 點陣圖
description: 了解如何使用 SkiaSharp 設計以互動方式 desribing 裁剪矩形的使用者介面。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 653904da37354db52ef6bbd303355e98ddc1582f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122630"
---
# <a name="cropping-skiasharp-bitmaps"></a>裁剪 SkiaSharp 點陣圖

[**建立和繪製 SkiaSharp 點陣圖**](drawing.md)一文所述方式`SKBitmap`物件可以傳遞至`SKCanvas`建構函式。 要呈現在點陣圖上該畫布原因圖形上呼叫任何繪製方法。 這些繪製方法包括`DrawBitmap`，這表示，這項技術可讓傳送部分或全部的一個點陣圖到另一個點陣圖，可能與套用的轉換。

您可以使用該技術，藉由呼叫裁剪點陣圖[ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint))與來源和目的地矩形的方法：

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

不過，實作通常裁剪的應用程式提供使用者以互動方式選取裁剪矩形的介面：

![裁剪範例](cropping-images/CroppingSample.png "裁剪範例")

本文著重於該介面。

## <a name="encapsulating-the-cropping-rectangle"></a>封裝的裁剪矩形

最好先找出一些類別，名為中的裁剪邏輯`CroppingRectangle`。 建構函式參數會包含一個最大的矩形，這通常是點陣圖兩邊被裁切的大小和選擇性的長寬比。 建構函式會先定義初始的裁剪矩形，它可在公用`Rect`型別的屬性`SKRect`。 這個初始的裁剪矩形是 80%的寬度和高度的點陣圖矩形中，但再調整如果指定的外觀比例：

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

一個有用的資訊片段，`CroppingRectangle`就會提供也是陣列`SKPoint`值對應至四個角落的裁剪矩形左上角、 右上方、 右下方和左下方的順序：

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

這個陣列用在下列的方法中，稱為`HitTest`。 `SKPoint`參數是對應到手指觸控點，或按一下滑鼠。 此方法會傳回索引 （0、 1、 2 或 3） 對應至手指或滑鼠指標接觸到，在指定距離內的角`radius`參數： 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

如果觸控或滑鼠點沒有內`radius`的任何一角的單位，則方法會傳回&ndash;1。

中的最後一個方法`CroppingRectangle`稱為`MoveCorner`，稱為以回應觸控動作，或將滑鼠移動。 兩個參數指出要移動的角落和該角落的新位置的索引。 方法的第一個部分會調整裁剪矩形的邊角中，但一定範圍內的新位置為基礎`maxRect`，這是點陣圖的大小。 此邏輯也會考慮`MINIMUM`以避免摺疊成 nothing 的裁剪矩形的欄位：

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

方法的第二個部分會調整為選擇性的長寬比。

請記住，這個類別中的所有項目是以像素為單位。

## <a name="a-canvas-view-just-for-cropping"></a>僅供裁剪畫布檢視

`CroppingRectangle`會使用您剛看到的類別`PhotoCropperCanvasView`類別，衍生自`SKCanvasView`。 這個類別是負責顯示點陣圖和裁剪矩形中，以及處理變更的裁剪矩形的觸控或滑鼠事件。

`PhotoCropperCanvasView`建構函式需要點陣圖。 外觀比例是選擇性的。 建構函式具現化型別的物件`CroppingRectangle`根據此點陣圖及長寬比，並將它儲存為欄位：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

因為這個類別衍生自`SKCanvasView`，它並不需要安裝的處理常式`PaintSurface`事件。 它可以改為覆寫其`OnPaintSurface`方法。 方法會顯示點陣圖，並採用了幾種`SKPaint`儲存為欄位，以繪製目前的裁剪矩形的物件：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

中的程式碼`CroppingRectangle`類別為基礎的裁剪矩形點陣圖的像素大小。 不過，顯示的點陣圖`PhotoCropperCanvasView`類別上的顯示區域的大小調整依據。 `bitmapScaleMatrix`計算中`OnPaintSurface`覆寫中的點陣圖像素的點陣圖位置和大小的對應，因為它會顯示。 這個矩陣接著用於轉換的裁剪矩形，使它可以顯示相對於點陣圖。

最後一行`OnPaintSurface`覆寫會接受的反向`bitmapScaleMatrix`並將它儲存為`inverseBitmapMatrix`欄位。 這用於觸控處理。

A`TouchEffect`物件具現化做為欄位，並建構函式會將附加的處理常式`TouchAction`事件，但`TouchEffect`必須要加入至`Effects`集合_父_的`SKCanvasView`衍生，以便完成`OnParentSet`覆寫：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

觸控事件處理`TouchAction`處理常式是以與裝置無關單位。 這些必須先轉換成使用像素`ConvertToPixel`方法底部的類別，然後轉換為`CroppingRectangle`使用的單位`inverseBitmapMatrix`。

針對`Pressed`事件，`TouchAction`處理常式會呼叫`HitTest`方法`CroppingRectangle`。 如果此命令傳回索引以外&ndash;1，然後裁剪矩形的邊角的其中一個受管理。 從角實際的觸控點的位移和索引會儲存在`TouchPoint`物件，並新增至`touchPoints`字典。

針對`Moved`事件，`MoveCorner`方法`CroppingRectangle`呼叫以移動的角，並進行的外觀比例的可能調整。

在任何時候程式，使用`PhotoCropperCanvasView`可以存取`CroppedBitmap`屬性。 這個屬性會使用`Rect`屬性`CroppingRectangle`來建立新的點陣圖的裁剪後的大小。 版本`DrawBitmap`目的地與來源矩形再擷取原始點陣圖的子集：

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>裝載相片 cropper 畫布檢視

與處理裁剪的邏輯，這兩個類別**裁剪相片**頁面 **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式有很少的工作来執行。 XAML 檔案會具現化`Grid`主機`PhotoCropperCanvasView`並**完成**按鈕：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

`PhotoCropperCanvasView`無法執行個體化，XAML 檔案中因為它需要的型別參數`SKBitmap`。

相反地，`PhotoCropperCanvasView`具現化中使用其中一種資源點陣圖的程式碼後置檔案的建構函式：

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

然後，使用者可以操作的裁剪矩形：

[![相片 Cropper 1](cropping-images/PhotoCropping1.png "相片 Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

已定義好的裁剪矩形，按一下**完成** 按鈕。 `Clicked`處理常式會取得從裁剪後的點陣圖`CroppedBitmap`屬性`PhotoCropperCanvasView`，並取代所有頁面的內容與新`SKCanvasView`物件，會顯示此裁剪後的點陣圖：

[![相片 Cropper 2](cropping-images/PhotoCropping2.png "相片 Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

嘗試設定的第二個引數`PhotoCropperCanvasView`1.78f （例如） 來：

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

您會看到限制為 16-9 外觀比例特性高畫質電視的裁剪矩形。

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>將點陣圖分割成圖格

知名的 Xamarin.Forms 版本 14 或 15 謎題出現在本書第 22 章[_使用 Xamarin.Forms 建立行動應用程式_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)而且可以下載作為[ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)。 不過，拼圖會變得更多樂趣 （且通常更具挑戰性） 當它根據您自己的相片媒體櫃中的影像。

這個版本的 14-15 個謎題是一部分 **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式中，組成一連串的頁面標題為**相片拼圖**。

**PhotoPuzzlePage1.xaml**檔案的組成`Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

程式碼後置檔案會實作`Clicked`使用的處理常式`IPhotoLibrary`，讓使用者從相片庫挑選相片的相依性服務：

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

方法接著會巡覽到`PhotoPuzzlePage2`，傳遞至建構函式所選的點陣圖。

您可從程式庫中選取的照片不導向，因為它出現在相片媒體櫃中，但會旋轉或上下。 （這是特別的 iOS 裝置的問題）。基於這個理由，`PhotoPuzzlePage2`可讓您將影像旋轉成所需的方向。 XAML 檔案中包含三個按鈕，分別為**90&#x00B0;權限**（依順時針方向表示）， **90&#x00B0;左側**（逆時針算起），以及**完成**。

程式碼後置檔案會實作一文中顯示的點陣圖旋轉邏輯 **[建立和上 SkiaSharp 點陣圖繪製](drawing.md#rotating-bitmaps)** 。 使用者可以將影像旋轉 90 度順時針或逆時針旋轉任意數目的時間： 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

當使用者按一下**完成** 按鈕，`Clicked`處理常式會瀏覽至`PhotoPuzzlePage3`，傳入網頁的建構函式中的最後一個旋轉的點陣圖。

`PhotoPuzzlePage3` 允許將影片裁剪相片。 程式需要將分成 4 x 4 方格的 tile 的方形點陣圖。

**PhotoPuzzlePage3.xaml**檔案包含`Label`，則`Grid`主機`PhotoCropperCanvasView`，和另一個**完成**按鈕：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

程式碼後置檔案會具現化`PhotoCropperCanvasView`與傳遞至其建構函式的點陣圖。 請注意，傳遞做為第二個引數 1 `PhotoCropperCanvasView`。 這個的外觀比例為 1 會強制正方形的裁剪矩形：

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

**完成**按鈕處理常式取得裁剪 （這兩個值應該是相同） 的點陣圖的高度與寬度，並再將它分成 15 的個別點陣圖，每個都是 1/4 之原始的高度與寬度。 （可能 16 點陣圖的最後一個不會建立。）`DrawBitmap`來源和目的地矩形的方法可讓點陣圖，以根據較大的點陣圖的子集來建立。

## <a name="converting-to-xamarinforms-bitmaps"></a>Xamarin.Forms 的點陣圖轉換

在 `OnDoneButtonClicked`方法，為 15 的點陣圖建立陣列屬於類型[ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` 是封裝點陣圖 Xamarin.Forms 基底類型。 幸運的是，可讓 SkiaSharp，從 SkiaSharp 點陣圖轉換成 Xamarin.Forms 點陣圖。 **SkiaSharp.Views.Forms**組件定義[ `SKBitmapImageSource` ](xref:SkiaSharp.Views.Forms.SKBitmapImageSource)類別衍生自`ImageSource`但可以建立根據 SkiaSharp`SKBitmap`物件。 `SKBitmapImageSource` 甚至定義之間的轉換`SKBitmapImageSource`和`SKBitmap`，以及這如何`SKBitmap`物件會儲存在陣列作為 Xamarin.Forms 點陣圖：

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

點陣圖這個陣列會傳遞的建構函式為`PhotoPuzzlePage4`。 該頁面完全是透過 Xamarin.Forms，但未使用任何 SkiaSharp。 它是非常類似[ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)，因此它不此處所述，但它會顯示您所選的相片，分成 15 的正方形圖格：

[![相片謎題 1](cropping-images/PhotoPuzzle1.png "相片謎題 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

按下**Randomize**按鈕混合組成的所有圖格：

[![相片謎題 2](cropping-images/PhotoPuzzle2.png "相片謎題 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

現在您可以將它們放回正確的順序。 將其移到空正方形，可以點選相同的資料列或以空白的方形的形式的資料行中的任何圖格。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
