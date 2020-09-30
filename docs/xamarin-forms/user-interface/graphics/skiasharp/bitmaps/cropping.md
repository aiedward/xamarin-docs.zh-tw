---
title: 裁剪 SkiaSharp 點陣圖
description: 瞭解如何使用 SkiaSharp 來設計以互動方式 desribing 裁剪矩形的使用者介面。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 45634695050dc6f74a9b1617a2180481e3788d11
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557318"
---
# <a name="cropping-skiasharp-bitmaps"></a>裁剪 SkiaSharp 點陣圖

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[**建立和繪製 SkiaSharp 點陣圖**](drawing.md)文章會說明如何 `SKBitmap` 將物件傳遞至函式 `SKCanvas` 。 在該畫布上呼叫的任何繪圖方法都會使圖形呈現在點陣圖上。 這些繪圖方法包括 `DrawBitmap` ，這表示這項技術允許將部分或全部點陣圖傳送到另一個點陣圖，也許是套用了轉換。

您可以使用該技術來裁剪點陣圖，方法是呼叫 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 具有來源和目的地矩形的方法：

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

不過，執行裁剪的應用程式通常會提供介面讓使用者以互動方式選取裁剪矩形：

![裁剪範例](cropping-images/CroppingSample.png "裁剪範例")

本文著重于該介面。

## <a name="encapsulating-the-cropping-rectangle"></a>封裝裁剪矩形

在名為的類別中隔離一些裁剪邏輯是很有説明的 `CroppingRectangle` 。 這些函式參數包括最大矩形，通常是要裁剪的點陣圖大小，以及選擇性的外觀比例。 此函式會先定義初始裁剪矩形，它會在類型的 `Rect` 屬性中公開 `SKRect` 。 這個初始裁剪矩形是點陣圖矩形寬度和高度的80%，但是如果指定了外觀比例，則會進行調整：

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

也可以使用的一項實用資訊， `CroppingRectangle` 是 `SKPoint` 對應至左上角、右上角、右下角和左下角之裁剪矩形四個角落的值陣列：

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

此陣列會在呼叫的下列方法中使用 `HitTest` 。 `SKPoint`參數是對應至手指觸控或滑鼠點擊的點。 方法會傳回索引 (0、1、2或 3) 對應至手指或滑鼠指標觸及的角落，在參數所指定的距離內 `radius` ：

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

如果觸控或滑鼠點不在 `radius` 任何角落的單位內，則此方法會傳回 &ndash; 1。

中的最後一個方法 `CroppingRectangle` 稱為 `MoveCorner` ，會呼叫以回應觸控或滑鼠移動。 這兩個參數表示要移動之邊角的索引，以及該角落的新位置。 方法的前半部會根據角落的新位置來調整裁剪矩形，但一律會在的界限內 `maxRect` （也就是點陣圖的大小）。 此邏輯也會考慮 `MINIMUM` 欄位，以避免將裁剪矩形折迭為 nothing：

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

方法的第二個部分會針對選擇性的外觀比例進行調整。

請記住，這個類別中的所有專案都是以圖元為單位。

## <a name="a-canvas-view-just-for-cropping"></a>只用于裁剪的畫布視圖

`CroppingRectangle`您剛才看到的類別是由 `PhotoCropperCanvasView` 衍生自的類別使用 `SKCanvasView` 。 這個類別負責顯示點陣圖和裁剪矩形，以及處理觸控或滑鼠事件來變更裁剪矩形。

此 `PhotoCropperCanvasView` 函數需要點陣圖。 外觀比例是選擇性的。 此函式 `CroppingRectangle` 會根據此點陣圖和外觀比例來具現化類型的物件，並將其儲存為欄位：

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

因為這個類別是衍生自 `SKCanvasView` ，所以不需要安裝事件的處理常式 `PaintSurface` 。 它可以改為覆寫它的 `OnPaintSurface` 方法。 方法會顯示點陣圖，並使用一些 `SKPaint` 儲存為欄位的物件來繪製目前的裁剪矩形：

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

類別中的程式碼會以 `CroppingRectangle` 點陣圖圖元大小的裁剪矩形作為基礎。 不過， `PhotoCropperCanvasView` 會根據顯示區域的大小來調整依類別顯示的點陣圖。 在覆 `bitmapScaleMatrix` 寫中計算的 `OnPaintSurface` 會從點陣圖圖元對應到點陣圖顯示時的大小和位置。 接著會使用這個矩陣來轉換裁剪矩形，使其可以相對於點陣圖顯示。

覆寫的最後一行 `OnPaintSurface` 會採用的反向， `bitmapScaleMatrix` 並將其儲存為 `inverseBitmapMatrix` 欄位。 這可用於觸控處理。

`TouchEffect`物件會具現化為欄位，而且此函式會將處理常式附加至 `TouchAction` 事件，但是必須將處理常式加入至衍生的 `TouchEffect` `Effects` _父系_集合 `SKCanvasView` ，以便在覆寫中完成 `OnParentSet` ：

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

處理常式所處理的觸控事件 `TouchAction` 是與裝置無關的單位。 這些必須先使用類別底部的方法轉換成圖元 `ConvertToPixel` ，然後使用來轉換成 `CroppingRectangle` 單位 `inverseBitmapMatrix` 。

若為 `Pressed` 事件， `TouchAction` 處理常式會呼叫的 `HitTest` 方法 `CroppingRectangle` 。 如果這會傳回1以外的索引 &ndash; ，則會操作裁剪矩形的其中一個角落。 該索引和角落實際觸控點的位移會儲存在 `TouchPoint` 物件中，並加入至 `touchPoints` 字典。

針對 `Moved` 事件， `MoveCorner` 會呼叫的方法 `CroppingRectangle` 來移動角落，以及可能的外觀比例調整。

在任何時候，使用的程式 `PhotoCropperCanvasView` 都可以存取 `CroppedBitmap` 屬性。 這個屬性會使用的 `Rect` 屬性 `CroppingRectangle` ，建立裁剪大小的新點陣圖。 `DrawBitmap`具有目的地和來源矩形的版本接著會解壓縮原始點陣圖的子集：

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

## <a name="hosting-the-photo-cropper-canvas-view"></a>裝載相片 cropper 畫布視圖

由於這兩個類別會處理裁剪邏輯，因此**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中的 [**相片裁剪**] 頁面會有很多工作。 XAML 檔案會具現化， `Grid` 以裝載 `PhotoCropperCanvasView` 和 [ **完成** ] 按鈕：

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

`PhotoCropperCanvasView`無法在 XAML 檔案中具現化，因為它需要類型的參數 `SKBitmap` 。

相反地， `PhotoCropperCanvasView` 會使用其中一個資源點陣圖，在程式碼後端檔案的函式中具現化：

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

然後，使用者可以操作裁剪矩形：

[![相片 Cropper 1](cropping-images/PhotoCropping1.png "相片 Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

定義好的裁剪矩形之後，請按一下 [ **完成** ] 按鈕。 `Clicked`處理常式會從的屬性取得裁剪點陣圖 `CroppedBitmap` `PhotoCropperCanvasView` ，並以 `SKCanvasView` 顯示此裁剪點陣圖的新物件取代頁面的所有內容：

[![相片 Cropper 2](cropping-images/PhotoCropping2.png "相片 Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

嘗試將的第二個引數設定 `PhotoCropperCanvasView` 為 1.78 f (例如) ：

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

您會看到裁剪矩形受限於高定義電視的16到9外觀比例特性。

## <a name="dividing-a-bitmap-into-tiles"></a>將點陣圖分割為磚

Xamarin.Forms[_建立 Mobile Apps Xamarin.Forms _](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)的書籍第22章會出現一版著名的14-15 謎題，可下載為[**>xamagonxuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)。 不過，謎題變得更有趣 (而且當它是以您自己的相片媒體櫃中的影像為基礎時，通常更具挑戰性的) 。

這一版的14-15 謎題是 **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式的一部分，並且是由一系列標題為 **相片謎題**的頁面所組成。

**PhotoPuzzlePage1 .xaml**檔案包含 `Button` ：

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

程式碼後端檔案會執行一個 `Clicked` 處理常式，該處理常式會使用相依性 `IPhotoLibrary` 服務，讓使用者從照片媒體櫃中挑選相片：

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

然後，方法會導覽至 `PhotoPuzzlePage2` ，並將選取的點陣圖傳遞至建構。

從文件庫中選取的相片可能不會在相片圖庫中顯示，而是會旋轉或倒置。  (這是 iOS 裝置的問題。基於這個原因，) 可 `PhotoPuzzlePage2` 讓您將影像旋轉至所需的方向。 XAML 檔案包含三個標示為 **90 的按鈕&#x00B0; 右** (表示順時針) 、 **90&#x00B0; 左方** (逆時針) 和 **完成**。

程式碼後端檔案會執行在 **[SkiaSharp 點陣圖上建立和繪製](drawing.md#rotating-bitmaps)** 文章中所顯示的點陣圖旋轉邏輯。 使用者可以順時針或逆時針旋轉90影像的次數：

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

當使用者按一下 [ **完成** ] 按鈕時， `Clicked` 處理常式會流覽至 `PhotoPuzzlePage3` ，並在頁面的函式中傳遞最終的旋轉點陣圖。

`PhotoPuzzlePage3` 允許裁剪相片。 程式需要正方形點陣圖，以分割成四個磚的四個方格。

**PhotoPuzzlePage3 .xaml**檔案包含 `Label` 、 `Grid` 可裝載的、 `PhotoCropperCanvasView` 以及另一個**完成**的按鈕：

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

程式碼後端檔案會具現化， `PhotoCropperCanvasView` 並將點陣圖傳遞給它的函式。 請注意，1會作為的第二個引數傳遞給 `PhotoCropperCanvasView` 。 這個外觀比例1會強制裁剪矩形成為正方形：

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

**完成**按鈕處理常式會取得裁剪點陣圖的寬度和高度 (這兩個值應該是相同的) 然後將它分割成15個不同的點陣圖，每個都是1/4 的原始寬度和高度。  (不會建立最後一個可能的16個位圖。 ) `DrawBitmap` 具有來源和目的地矩形的方法，可讓您根據較大點陣圖的子集來建立點陣圖。

## <a name="converting-to-no-locxamarinforms-bitmaps"></a>轉換成 Xamarin.Forms 點陣圖

在 `OnDoneButtonClicked` 方法中，為15個位圖建立的陣列類型為 [`ImageSource`](xref:Xamarin.Forms.ImageSource) ：

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` 是 Xamarin.Forms 封裝點陣圖的基底類型。 幸運的是，SkiaSharp 允許從 SkiaSharp 點陣圖轉換成 Xamarin.Forms 點陣圖。 **SkiaSharp**元件 [`SKBitmapImageSource`](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) 會定義衍生自的類別， `ImageSource` 但可以根據 SkiaSharp 物件來建立 `SKBitmap` 。 `SKBitmapImageSource` 甚至定義和之間的轉換 `SKBitmapImageSource` `SKBitmap` ，這就是 `SKBitmap` 物件以點陣圖形式儲存在陣列中的方式 Xamarin.Forms ：

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

這個點陣圖陣列是以函式的形式傳遞至 `PhotoPuzzlePage4` 。 該頁面完全不會 Xamarin.Forms 使用任何 SkiaSharp。 這與 [**>xamagonxuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle)非常類似，因此不會在此說明，但會顯示您選取的相片分成15個正方形圖格：

[![相片謎題1](cropping-images/PhotoPuzzle1.png "相片謎題1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

按 [ **隨機化** ] 按鈕會混合所有磚：

[![相片謎題2](cropping-images/PhotoPuzzle2.png "相片謎題2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

現在您可以將它們放回正確的順序。 相同資料列或資料行中與空白方形相同的任何磚，都可以用來將它們移至空白方形。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)