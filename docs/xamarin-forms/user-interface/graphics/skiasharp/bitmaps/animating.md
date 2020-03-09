---
title: 製作 SkiaSharp 點陣圖的動畫
description: 瞭解如何藉由依序顯示一系列的點陣圖和呈現動畫 GIF 檔案，來執行點陣圖動畫。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 33e17a01d8a13fcdaee27e5857c554a4a232c534
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/07/2020
ms.locfileid: "78917916"
---
# <a name="animating-skiasharp-bitmaps"></a>製作 SkiaSharp 點陣圖的動畫

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

以動畫顯示 SkiaSharp 圖形的應用程式通常會以固定的速率呼叫 `SKCanvasView` 上的 `InvalidateSurface`，通常是每16毫秒一次。 使表面失效會觸發呼叫 `PaintSurface` 處理常式來重新繪製顯示。 當視覺效果每秒重新繪製60次時，它們看起來會順暢地動畫。

不過，如果圖形太複雜而無法在16毫秒內轉譯，動畫可能會變得抖動。 程式設計人員可能會選擇將重新整理頻率減少為30倍或15次，但有時甚至還不夠。 有時候圖形很複雜，因此無法即時轉譯。

其中一個解決方案是在一系列的點陣圖上轉譯動畫的個別畫面格，以便事先準備動畫。 若要顯示動畫，只需要以每秒60次的順序顯示這些點陣圖。

當然，這可能是很多的點陣圖，但這就是如何製作大預算的3D 動畫電影。 3D 圖形太複雜而無法即時轉譯。 轉譯每個畫面格需要許多處理時間。 當您觀看影片時，您看到的內容基本上是一系列的點陣圖。

您可以在 SkiaSharp 中執行類似的動作。 本文將示範兩種點陣圖動畫類型。 第一個範例是 Mandelbrot 集的動畫：

![動畫範例](animating-images/AnimatingSample.png "動畫範例")

第二個範例示範如何使用 SkiaSharp 呈現動畫 GIF 檔案。

## <a name="bitmap-animation"></a>點陣圖動畫

Mandelbrot 集的視覺效果驚人，但 computionally 冗長。 （如需 Mandelbrot 集和此處所用數學的討論，請參閱使用從頁面666開始[_建立 Mobile Apps 與 Xamarin_的第20章](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)。 下列描述假設背景知識。）

[**Mandelbrot 動畫**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)範例會使用點陣圖動畫來模擬 Mandelbrot 集中固定點的連續縮放。 放大會接著縮小，而迴圈會永遠重複，或直到您結束程式為止。

程式會藉由建立最多50個位圖並儲存在應用程式本機儲存體中，來準備此動畫。 每個點陣圖的寬度和高度，都與上一個點陣圖一樣複雜。 （在程式中，這些點陣圖稱為代表整數_縮放層級_）。接著會依序顯示點陣圖。 每個點陣圖的縮放比例都會以動畫的形式，從一個點陣圖到另一個點陣圖提供順暢的進展。

如同使用 Mandelbrot 建立_Mobile Apps_一章所述的最後一個程式， **Mandelbrot 動畫**中的集計算是具有八個參數的非同步方法。 這些參數包括複雜的中心點，以及圍繞該中心點之複雜平面的寬度和高度。 接下來的三個參數是要建立之點陣圖的圖元寬度和高度，以及遞迴計算的反覆運算次數上限。 `progress` 參數是用來顯示此計算的進度。 此程式中不會使用 `cancelToken` 參數：

```csharp
static class Mandelbrot
{
    public static Task<BitmapInfo> CalculateAsync(Complex center,
                                                  double width, double height,
                                                  int pixelWidth, int pixelHeight,
                                                  int iterations,
                                                  IProgress<double> progress,
                                                  CancellationToken cancelToken)
    {
        return Task.Run(() =>
        {
            int[] iterationCounts = new int[pixelWidth * pixelHeight];
            int index = 0;

            for (int row = 0; row < pixelHeight; row++)
            {
                progress.Report((double)row / pixelHeight);
                cancelToken.ThrowIfCancellationRequested();

                double y = center.Imaginary + height / 2 - row * height / pixelHeight;

                for (int col = 0; col < pixelWidth; col++)
                {
                    double x = center.Real - width / 2 + col * width / pixelWidth;
                    Complex c = new Complex(x, y);

                    if ((c - new Complex(-1, 0)).Magnitude < 1.0 / 4)
                    {
                        iterationCounts[index++] = -1;
                    }
                    // http://www.reenigne.org/blog/algorithm-for-mandelbrot-cardioid/
                    else if (c.Magnitude * c.Magnitude * (8 * c.Magnitude * c.Magnitude - 3) < 3.0 / 32 - c.Real)
                    {
                        iterationCounts[index++] = -1;
                    }
                    else
                    {
                        Complex z = 0;
                        int iteration = 0;

                        do
                        {
                            z = z * z + c;
                            iteration++;
                        }
                        while (iteration < iterations && z.Magnitude < 2);

                        if (iteration == iterations)
                        {
                            iterationCounts[index++] = -1;
                        }
                        else
                        {
                            iterationCounts[index++] = iteration;
                        }
                    }
                }
            }
            return new BitmapInfo(pixelWidth, pixelHeight, iterationCounts);
        }, cancelToken);
    }
}
```

方法會傳回 `BitmapInfo` 類型的物件，以提供建立點陣圖的資訊：

```csharp
class BitmapInfo
{
    public BitmapInfo(int pixelWidth, int pixelHeight, int[] iterationCounts)
    {
        PixelWidth = pixelWidth;
        PixelHeight = pixelHeight;
        IterationCounts = iterationCounts;
    }

    public int PixelWidth { private set; get; }

    public int PixelHeight { private set; get; }

    public int[] IterationCounts { private set; get; }
}
```

**Mandelbrot 動畫**XAML 檔案包含兩個 `Label` 視圖、`ProgressBar`和 `Button`，以及 `SKCanvasView`：

```csharp
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="MandelAnima.MainPage"
             Title="Mandelbrot Animation">

    <StackLayout>
        <Label x:Name="statusLabel"
               HorizontalTextAlignment="Center" />
        <ProgressBar x:Name="progressBar" />

        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <StackLayout Orientation="Horizontal"
                     Padding="5">
            <Label x:Name="storageLabel"
                   VerticalOptions="Center" />

            <Button x:Name="deleteButton"
                    Text="Delete All"
                    HorizontalOptions="EndAndExpand"
                    Clicked="OnDeleteButtonClicked" />
        </StackLayout>
    </StackLayout>
</ContentPage>
```

程式碼後置檔案一開始會定義三個重要常數和一組點陣圖：

```csharp
public partial class MainPage : ContentPage
{
    const int COUNT = 10;           // The number of bitmaps in the animation.
                                    // This can go up to 50!

    const int BITMAP_SIZE = 1000;   // Program uses square bitmaps exclusively

    // Uncomment just one of these, or define your own
    static readonly Complex center = new Complex(-1.17651152924355, 0.298520986549558);
    //   static readonly Complex center = new Complex(-0.774693089457127, 0.124226621261617);
    //   static readonly Complex center = new Complex(-0.556624880053304, 0.634696788141351);

    SKBitmap[] bitmaps = new SKBitmap[COUNT];   // array of bitmaps
    ···
}
```

在某些時候，您可能會想要將 `COUNT` 值變更為50，以查看動畫的完整範圍。 50以上的值並不實用。 在縮放層級48或左右，雙精確度浮點數的解析度會變得不足以進行 Mandelbrot 集計算。 此問題會在_使用 Xamarin 建立 Mobile Apps_的第684頁中討論。

`center` 值非常重要。 這是動畫縮放的焦點。 檔案中的三個值是在第20章_建立 Mobile Apps 和_在684頁面上使用 Xamarin 的最後一個螢幕擷取畫面，但您可以試驗該章節中的程式，以使用您自己的其中一個值。

**Mandelbrot 動畫**範例會將這些 `COUNT` 點陣圖儲存在本機應用程式儲存區中。 50點陣圖在您的裝置上需要超過 20 mb 的儲存空間，因此您可能會想要知道這些點陣圖佔用多少儲存空間，而且您可能會想要將它們全部刪除。 這是在 `MainPage` 類別底部的這兩個方法的用途：

```csharp
public partial class MainPage : ContentPage
{
    ···
    void TallyBitmapSizes()
    {
        long fileSize = 0;

        foreach (string filename in Directory.EnumerateFiles(FolderPath()))
        {
            fileSize += new FileInfo(filename).Length;
        }

        storageLabel.Text = $"Total storage: {fileSize:N0} bytes";
    }

    void OnDeleteButtonClicked(object sender, EventArgs args)
    {
        foreach (string filepath in Directory.EnumerateFiles(FolderPath()))
        {
            File.Delete(filepath);
        }

        TallyBitmapSizes();
    }
}
```

您可以刪除本機儲存區中的點陣圖，而程式會以動畫顯示相同的點陣圖，因為程式會將它們保留在記憶體中。 但下一次執行程式時，必須重新建立點陣圖。

儲存在本機應用程式儲存區中的點陣圖會將 `center` 值併入其檔案名中，因此，如果您變更 `center` 設定，則不會在儲存體中取代現有的點陣圖，而且會繼續佔用空間。

以下是 `MainPage` 用來建立檔案名的方法，以及用來根據色彩元件定義圖元值的 `MakePixel` 方法：

```csharp
public partial class MainPage : ContentPage
{
    ···
    // File path for storing each bitmap in local storage
    string FolderPath() =>
        Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData);

    string FilePath(int zoomLevel) =>
        Path.Combine(FolderPath(),
                     String.Format("R{0}I{1}Z{2:D2}.png", center.Real, center.Imaginary, zoomLevel));

    // Form bitmap pixel for Rgba8888 format
    uint MakePixel(byte alpha, byte red, byte green, byte blue) =>
        (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

`FilePath` 的 `zoomLevel` 參數範圍從0到 `COUNT` 常數減1。

`MainPage` 的函式會呼叫 `LoadAndStartAnimation` 方法：

```csharp
public partial class MainPage : ContentPage
{
    ···
    public MainPage()
    {
        InitializeComponent();

        LoadAndStartAnimation();
    }
    ···
}
```

`LoadAndStartAnimation` 方法會負責存取應用程式本機儲存體，以載入先前執行程式時可能已建立的任何點陣圖。 它會迴圈處理 `zoomLevel` 值，從0到 `COUNT`。 如果檔案存在，它就會將它載入 `bitmaps` 陣列中。 否則，它必須藉由呼叫 `Mandelbrot.CalculateAsync`來建立特定 `center` 的點陣圖，並 `zoomLevel` 值。 該方法會取得每個圖元的反復專案計數，這個方法會將它轉換成色彩：

```csharp
public partial class MainPage : ContentPage
{
    ···
    async void LoadAndStartAnimation()
    {
        // Show total bitmap storage
        TallyBitmapSizes();

        // Create progressReporter for async operation
        Progress<double> progressReporter =
            new Progress<double>((double progress) => progressBar.Progress = progress);

        // Create (unused) CancellationTokenSource for async operation
        CancellationTokenSource cancelTokenSource = new CancellationTokenSource();

        // Loop through all the zoom levels
        for (int zoomLevel = 0; zoomLevel < COUNT; zoomLevel++)
        {
            // If the file exists, load it
            if (File.Exists(FilePath(zoomLevel)))
            {
                statusLabel.Text = $"Loading bitmap for zoom level {zoomLevel}";

                using (Stream stream = File.OpenRead(FilePath(zoomLevel)))
                {
                    bitmaps[zoomLevel] = SKBitmap.Decode(stream);
                }
            }
            // Otherwise, create a new bitmap
            else
            {
                statusLabel.Text = $"Creating bitmap for zoom level {zoomLevel}";

                CancellationToken cancelToken = cancelTokenSource.Token;

                // Do the (generally lengthy) Mandelbrot calculation
                BitmapInfo bitmapInfo =
                    await Mandelbrot.CalculateAsync(center,
                                                    4 / Math.Pow(2, zoomLevel),
                                                    4 / Math.Pow(2, zoomLevel),
                                                    BITMAP_SIZE, BITMAP_SIZE,
                                                    (int)Math.Pow(2, 10), progressReporter, cancelToken);

                // Create bitmap & get pointer to the pixel bits
                SKBitmap bitmap = new SKBitmap(BITMAP_SIZE, BITMAP_SIZE, SKColorType.Rgba8888, SKAlphaType.Opaque);
                IntPtr basePtr = bitmap.GetPixels();

                // Set pixel bits to color based on iteration count
                for (int row = 0; row < bitmap.Width; row++)
                    for (int col = 0; col < bitmap.Height; col++)
                    {
                        int iterationCount = bitmapInfo.IterationCounts[row * bitmap.Width + col];
                        uint pixel = 0xFF000000;            // black

                        if (iterationCount != -1)
                        {
                            double proportion = (iterationCount / 32.0) % 1;
                            byte red = 0, green = 0, blue = 0;

                            if (proportion < 0.5)
                            {
                                red = (byte)(255 * (1 - 2 * proportion));
                                blue = (byte)(255 * 2 * proportion);
                            }
                            else
                            {
                                proportion = 2 * (proportion - 0.5);
                                green = (byte)(255 * proportion);
                                blue = (byte)(255 * (1 - proportion));
                            }

                            pixel = MakePixel(0xFF, red, green, blue);
                        }

                        // Calculate pointer to pixel
                        IntPtr pixelPtr = basePtr + 4 * (row * bitmap.Width + col);

                        unsafe     // requires compiling with unsafe flag
                        {
                            *(uint*)pixelPtr.ToPointer() = pixel;
                        }
                    }

                // Save as PNG file
                SKData data = SKImage.FromBitmap(bitmap).Encode();

                try
                {
                    File.WriteAllBytes(FilePath(zoomLevel), data.ToArray());
                }
                catch
                {
                    // Probably out of space, but just ignore
                }

                // Store in array
                bitmaps[zoomLevel] = bitmap;

                // Show new bitmap sizes
                TallyBitmapSizes();
            }

            // Display the bitmap
            bitmapIndex = zoomLevel;
            canvasView.InvalidateSurface();
        }

        // Now start the animation
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }
    ···
}
```

請注意，此程式會將這些點陣圖儲存在本機應用程式儲存區中，而不是裝置的相片媒體櫃中。 .NET Standard 2.0 程式庫可讓您使用熟悉的 `File.OpenRead` 和 `File.WriteAllBytes` 方法來進行這項工作。

在建立所有點陣圖或將其載入記憶體之後，方法會啟動 `Stopwatch` 物件，並呼叫 `Device.StartTimer`。 每16毫秒會呼叫一次 `OnTimerTick` 方法。

`OnTimerTick` 會計算介於0到6000倍 `COUNT`之間的 `time` 值（以毫秒為單位），這會 apportions 每個點陣圖的顯示六秒。 `progress` 值會使用 `Math.Sin` 值來建立 sinusoidal 動畫，在迴圈開始時將會變慢，而在結束時則會以相反方向的速度變慢。

`progress` 值的範圍是從0到 `COUNT`。 這表示 `progress` 的整數部分是 `bitmaps` 陣列中的索引，而 `progress` 的小數部分則表示該特定點陣圖的縮放層級。 這些值會儲存在 [`bitmapIndex`] 和 [`bitmapProgress`] 欄位中，而且會由 XAML 檔案中的 `Label` 和 `Slider` 顯示。 `SKCanvasView` 無效，而無法更新點陣圖顯示：

```csharp
public partial class MainPage : ContentPage
{
    ···
    Stopwatch stopwatch = new Stopwatch();      // for the animation
    int bitmapIndex;
    double bitmapProgress = 0;
    ···
    bool OnTimerTick()
    {
        int cycle = 6000 * COUNT;       // total cycle length in milliseconds

        // Time in milliseconds from 0 to cycle
        int time = (int)(stopwatch.ElapsedMilliseconds % cycle);

        // Make it sinusoidal, including bitmap index and gradation between bitmaps
        double progress = COUNT * 0.5 * (1 + Math.Sin(2 * Math.PI * time / cycle - Math.PI / 2));

        // These are the field values that the PaintSurface handler uses
        bitmapIndex = (int)progress;
        bitmapProgress = progress - bitmapIndex;

        // It doesn't often happen that we get up to COUNT, but an exception would be raised
        if (bitmapIndex < COUNT)
        {
            // Show progress in UI
            statusLabel.Text = $"Displaying bitmap for zoom level {bitmapIndex}";
            progressBar.Progress = bitmapProgress;

            // Update the canvas
            canvasView.InvalidateSurface();
        }

        return true;
    }
    ···
}
```

最後，`SKCanvasView` 的 `PaintSurface` 處理常式會計算目的地矩形，盡可能地顯示點陣圖，同時維持外觀比例。 來源矩形是以 `bitmapProgress` 值為基礎。 在此處計算的 `fraction` 值是從0開始，當 `bitmapProgress` 為0時，若要顯示整個點陣圖，則範圍為0.25，而 `bitmapProgress` 為1時，則會顯示點陣圖的一半寬度和高度，有效地縮放：

```csharp
public partial class MainPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        if (bitmaps[bitmapIndex] != null)
        {
            // Determine destination rect as square in canvas
            int dimension = Math.Min(info.Width, info.Height);
            float x = (info.Width - dimension) / 2;
            float y = (info.Height - dimension) / 2;
            SKRect destRect = new SKRect(x, y, x + dimension, y + dimension);

            // Calculate source rectangle based on fraction:
            //  bitmapProgress == 0: full bitmap
            //  bitmapProgress == 1: half of length and width of bitmap
            float fraction = 0.5f * (1 - (float)Math.Pow(2, -bitmapProgress));
            SKBitmap bitmap = bitmaps[bitmapIndex];
            int width = bitmap.Width;
            int height = bitmap.Height;
            SKRect sourceRect = new SKRect(fraction * width, fraction * height,
                                           (1 - fraction) * width, (1 - fraction) * height);

            // Display the bitmap
            canvas.DrawBitmap(bitmap, sourceRect, destRect);
        }
    }
    ···
}
```

以下是程式執行情況：

[![Mandelbrot 動畫](animating-images/MandelbrotAnimation.png "Mandelbrot 動畫")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>GIF 動畫

圖形交換格式（GIF）規格包含一項功能，可讓單一 GIF 檔案包含一個場景的多個連續畫面格，這種情況通常會在迴圈中以連續方式顯示。 這些檔案就是所謂的_動畫 gif_。 網頁瀏覽器可以播放動畫 Gif，而 SkiaSharp 可讓應用程式從動畫 GIF 檔案中解壓縮框架，並依序顯示它們。

[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例包含名為**NEWTONS_CRADLE_ANIMATION_BOOK_2**的動畫 Gif 資源，由 DemonDeLuxe 建立並從維琪百科中的[牛頓底座](https://en.wikipedia.org/wiki/Newton%27s_cradle)頁面下載。 **動畫 GIF**頁面包含 XAML 檔案，可提供該資訊並具現化 `SKCanvasView`：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.AnimatedGifPage"
             Title="Animated GIF">

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="GIF file by DemonDeLuxe from Wikipedia Newton's Cradle page"
               Grid.Row="1"
               Margin="0, 5"
               HorizontalTextAlignment="Center" />
    </Grid>
</ContentPage>
```

程式碼後置檔案不會一般化來播放任何動畫 GIF 檔案。 它會忽略一些可用的資訊，特別是重複計數，而且只會在迴圈中播放動畫 GIF。

使用 SkisSharp 來解壓縮動畫 GIF 檔案的框架並不會在任何地方記載，因此後面的程式碼描述會比平常更詳細：

動畫 GIF 檔案的解碼會出現在頁面的函式中，而且必須使用參考點陣圖的 `Stream` 物件來建立 `SKManagedStream` 物件，然後是[`SKCodec`](xref:SkiaSharp.SKCodec)物件。 [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount)屬性會指出構成動畫的畫面格數目。

這些框架最後會儲存為個別的點陣圖，因此，此函式會使用 `FrameCount` 來配置 `SKBitmap` 類型的陣列，以及在每個畫面格的持續期間和（用來簡化動畫邏輯）累積的持續時間的兩個 `int` 陣列。

`SKCodec` 類別的[`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo)屬性是[`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo)值的陣列，每個框架都有一個，但此程式從該結構取得的唯一專案是以毫秒為單位的框架[`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) 。

`SKCodec` 會定義名為[`Info`](xref:SkiaSharp.SKCodec.Info)類型為[`SKImageInfo`](xref:SkiaSharp.SKImageInfo)的屬性，但該 `SKImageInfo` 值表示（至少為此影像）顏色類型為 `SKColorType.Index8`，這表示每個圖元都是色彩類型的索引。 為了避免中斷使用色彩表，程式會使用該結構中的[`Width`](xref:SkiaSharp.SKImageInfo.Width)和[`Height`](xref:SkiaSharp.SKImageInfo.Height)資訊，來建立它自己的全彩 `ImageInfo` 值。 每個 `SKBitmap` 都會從該建立。

`SKBitmap` 的 `GetPixels` 方法會傳回參考該點陣圖圖元位的 `IntPtr`。 尚未設定這些圖元位。 該 `IntPtr` 會傳遞至 `SKCodec`的其中一個[`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions))方法。 該方法會將框架從 GIF 檔案複製到 `IntPtr`所參考的記憶體空間中。 [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions)的函式會指出畫面格編號：

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;
    ···

    public AnimatedGifPage ()
    {
        InitializeComponent ();

        string resourceID = "SkiaSharpFormsDemos.Media.Newtons_cradle_animation_book_2.gif";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        using (SKManagedStream skStream = new SKManagedStream(stream))
        using (SKCodec codec = SKCodec.Create(skStream))
        {
            // Get frame count and allocate bitmaps
            int frameCount = codec.FrameCount;
            bitmaps = new SKBitmap[frameCount];
            durations = new int[frameCount];
            accumulatedDurations = new int[frameCount];

            // Note: There's also a RepetitionCount property of SKCodec not used here

            // Loop through the frames
            for (int frame = 0; frame < frameCount; frame++)
            {
                // From the FrameInfo collection, get the duration of each frame
                durations[frame] = codec.FrameInfo[frame].Duration;

                // Create a full-color bitmap for each frame
                SKImageInfo imageInfo = code.new SKImageInfo(codec.Info.Width, codec.Info.Height);
                bitmaps[frame] = new SKBitmap(imageInfo);

                // Get the address of the pixels in that bitmap
                IntPtr pointer = bitmaps[frame].GetPixels();

                // Create an SKCodecOptions value to specify the frame
                SKCodecOptions codecOptions = new SKCodecOptions(frame, false);

                // Copy pixels from the frame into the bitmap
                codec.GetPixels(imageInfo, pointer, codecOptions);
            }

            // Sum up the total duration
            for (int frame = 0; frame < durations.Length; frame++)
            {
                totalDuration += durations[frame];
            }

            // Calculate the accumulated durations
            for (int frame = 0; frame < durations.Length; frame++)
            {
                accumulatedDurations[frame] = durations[frame] +
                    (frame == 0 ? 0 : accumulatedDurations[frame - 1]);
            }
        }
    }
    ···
}
```

雖然 `IntPtr` 值，但不需要 `unsafe` 程式碼，因為 `IntPtr` 絕對不會轉換成C#指標值。

在解壓縮每個框架之後，此函式會匯總所有框架的持續時間，然後初始化具有累積持續時間的另一個陣列。

程式碼後置檔案的其餘部分專供動畫之用。 `Device.StartTimer` 方法是用來啟動計時器，而 `OnTimerTick` 回呼會使用 `Stopwatch` 物件來判斷經過的時間（以毫秒為單位）。 迴圈執行累積的持續時間陣列，足以找出目前的框架：

```csharp
public partial class AnimatedGifPage : ContentPage
{
    SKBitmap[] bitmaps;
    int[] durations;
    int[] accumulatedDurations;
    int totalDuration;

    Stopwatch stopwatch = new Stopwatch();
    bool isAnimating;

    int currentFrame;
    ···
    protected override void OnAppearing()
    {
        base.OnAppearing();

        isAnimating = true;
        stopwatch.Start();
        Device.StartTimer(TimeSpan.FromMilliseconds(16), OnTimerTick);
    }

    protected override void OnDisappearing()
    {
        base.OnDisappearing();

        stopwatch.Stop();
        isAnimating = false;
    }

    bool OnTimerTick()
    {
        int msec = (int)(stopwatch.ElapsedMilliseconds % totalDuration);
        int frame = 0;

        // Find the frame based on the elapsed time
        for (frame = 0; frame < accumulatedDurations.Length; frame++)
        {
            if (msec < accumulatedDurations[frame])
            {
                break;
            }
        }

        // Save in a field and invalidate the SKCanvasView.
        if (currentFrame != frame)
        {
            currentFrame = frame;
            canvasView.InvalidateSurface();
        }

        return isAnimating;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Black);

        // Get the bitmap and center it
        SKBitmap bitmap = bitmaps[currentFrame];
        canvas.DrawBitmap(bitmap,info.Rect, BitmapStretch.Uniform);
    }
}
```

每次 `currentframe` 變數變更時，`SKCanvasView` 就會失效，並顯示新的畫面格：

[![動畫 GIF](animating-images/AnimatedGif.png "動畫 GIF")](animating-images/AnimatedGif-Large.png#lightbox)

當然，您會想要自行執行程式以查看動畫。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Mandelbrot 動畫（範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
