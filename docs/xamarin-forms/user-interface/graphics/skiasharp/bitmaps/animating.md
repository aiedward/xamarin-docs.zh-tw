---
title: 製作 SkiaSharp 點陣圖的動畫
description: 瞭解如何依序顯示一連串點陣圖和轉譯動畫 GIF 檔案，以執行點陣圖動畫。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: eeaf62c684100ff9fd5bb9ffd15162bdb4c6afd6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366512"
---
# <a name="animating-skiasharp-bitmaps"></a>製作 SkiaSharp 點陣圖的動畫

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

製作 SkiaSharp 圖形動畫的應用程式通常會 `InvalidateSurface` `SKCanvasView` 以固定的速率呼叫，通常是每16毫秒一次。 使介面失效會觸發對 `PaintSurface` 處理常式的呼叫，以重繪顯示。 當視覺效果每秒重新繪製60次時，它們看起來就像是流暢的動畫。

但是，如果圖形太複雜而無法在16毫秒內轉譯，則動畫可能會變得更抖動。 程式設計師可能會選擇將重新整理頻率縮減為30次或15倍，但有時甚至還不夠。 有時候圖形很複雜，因為它們只是無法即時轉譯。

其中一個解決方法是將動畫的個別畫面格轉譯成一系列的點陣圖，以預先準備動畫。 若要顯示動畫，只需要每秒將這些點陣圖顯示為60次。

當然，這可能是很多的點陣圖，但這就是大型預算的3D 動畫製作影片的方式。 3D 圖形太複雜而無法即時轉譯。 需要大量的處理時間才能轉譯每個畫面格。 觀賞電影時所看到的內容基本上是一系列的點陣圖。

您可以在 SkiaSharp 中做一些類似的動作。 本文示範兩種點陣圖動畫類型。 第一個範例是 Mandelbrot 集的動畫：

![製作範例動畫](animating-images/AnimatingSample.png "製作範例動畫")

第二個範例示範如何使用 SkiaSharp 來呈現動畫 GIF 檔案。

## <a name="bitmap-animation"></a>點陣圖動畫

Mandelbrot 集以視覺化方式驚人，但 computionally 冗長。  (如需 Mandelbrot 集和此處所用數學的討論，請參閱從666頁面開始 [_建立 Mobile Apps Xamarin.Forms_ 的第20章](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf)。 下列描述假設背景知識。 ) 

[**Mandelbrot 動畫**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)範例會使用點陣圖動畫來模擬 Mandelbrot 集中固定點的連續縮放。 放大會接著縮小，然後迴圈會永久重複，或直到您結束程式為止。

程式會藉由建立最多50點陣圖（其儲存在應用程式本機儲存體中）來準備此動畫。 每個點陣圖都會包含複雜平面的一半寬度和高度，作為前一個點陣圖。  (在程式中，這些點陣圖表示為代表整數 _縮放層級_ 。 ) 接著會依序顯示點陣圖。 調整每個點陣圖的動畫，以提供從某個點陣圖到另一個點陣圖的流暢進展。

就像使用 _建立 Mobile Apps Xamarin.Forms_ 的第20章中所述的最終程式一樣， **Mandelbrot 動畫** 中的 Mandelbrot 集計算是具有8個參數的非同步方法。 這些參數包括複雜的中心點，以及圍繞該中心點之複雜平面的寬度和高度。 接下來的三個參數是要建立之點陣圖的圖元寬度和高度，以及遞迴計算的反覆運算次數上限。 `progress`參數是用來顯示此計算的進度。 `cancelToken`此程式未使用此參數：

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

方法會傳回型別的物件 `BitmapInfo` ，提供建立點陣圖的資訊：

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

**Mandelbrot 動畫** XAML 檔案包含兩個 `Label` 視圖：、 `ProgressBar` 和，以及 `Button` `SKCanvasView` ：

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

程式碼後端檔案一開始先定義三個重要的常數和一組點陣圖：

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

在某個時間點，您可能會想要將 `COUNT` 值變更為50，以查看動畫的完整範圍。 50以上的值不實用。 在48或更高的縮放層級周圍，雙精確度浮點數的解析度會變得不足以進行 Mandelbrot 集計算。 此問題會在 _建立 Mobile Apps 的 Xamarin.Forms_ 頁面684中討論。

`center`值相當重要。 這是動畫縮放的焦點。 檔案中的三個值是在第20章的三個最後一個螢幕擷取畫面中所使用的值，在684第20頁的第20章中 _建立 Mobile Apps Xamarin.Forms_ ，但您可以在該章節中試驗程式，以使用您自己的其中一個值。

**Mandelbrot 動畫** 範例會將這些 `COUNT` 點陣圖儲存在本機應用程式儲存區中。 50點陣圖在您的裝置上需要超過 20 mb 的儲存空間，因此您可能會想要知道這些點陣圖所佔用的儲存體數量，而且在某個時間點，您可能會想要全部刪除。 這就是類別底部的這兩個方法的用途 `MainPage` ：

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

當程式製作相同點陣圖的動畫時，您可以刪除本機儲存區中的點陣圖，因為程式會將它們保留在記憶體中。 但是，當您下一次執行程式時，將需要重新建立點陣圖。

儲存在本機應用程式儲存體中的點陣圖 `center` 會將值併入其檔案名中，因此，如果您變更此 `center` 設定，將不會取代儲存區中的現有點陣圖，而且會繼續佔用空間。

以下是 `MainPage` 用來建立檔案名的方法，以及 `MakePixel` 根據色彩元件定義圖元值的方法：

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

`zoomLevel`參數的 `FilePath` 範圍從0到 `COUNT` 常數減1。

此 `MainPage` 函數會呼叫 `LoadAndStartAnimation` 方法：

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

`LoadAndStartAnimation`方法負責存取應用程式本機儲存體，以載入在先前執行程式時可能已建立的任何點陣圖。 它會透過 `zoomLevel` 0 到的值進行迴圈 `COUNT` 。 如果檔案存在，則會將它載入 `bitmaps` 陣列中。 否則，它必須藉由呼叫來建立特定 `center` 和值的點陣圖 `zoomLevel` `Mandelbrot.CalculateAsync` 。 該方法會取得每個圖元的反復專案計數，這個方法會轉換成色彩：

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

請注意，此程式會將這些點陣圖儲存在本機應用程式儲存區，而不是儲存在裝置的相片媒體櫃中。 .NET Standard 2.0 程式庫可讓您使用熟悉的 `File.OpenRead` 和 `File.WriteAllBytes` 方法來進行這項工作。

在將所有點陣圖建立或載入至記憶體後，方法會啟動 `Stopwatch` 物件並呼叫 `Device.StartTimer` 。 `OnTimerTick`方法會每16毫秒呼叫一次。

`OnTimerTick` 計算以 `time` 毫秒為單位的值，範圍從0到6000次 `COUNT` ，apportions 每個點陣圖的顯示為6秒。 `progress`值會使用此 `Math.Sin` 值來建立正弦曲線動畫，在迴圈開始時會變慢，並在其反轉方向時以較慢的方式結束。

`progress`值的範圍是從 0 `COUNT` 到。 這表示的整數部分 `progress` 是陣列中的索引 `bitmaps` ，而的小數部分則 `progress` 表示該特定點陣圖的縮放層級。 這些值會儲存在 `bitmapIndex` 和欄位中，而且會在 XAML 檔案中 `bitmapProgress` 顯示 `Label` `Slider` 。 `SKCanvasView`會失效以更新點陣圖顯示：

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

最後，的 `PaintSurface` 處理常式會 `SKCanvasView` 計算目的地矩形，盡可能盡可能地顯示點陣圖，同時維持外觀比例。 來源矩形是以值為基礎 `bitmapProgress` 。 `fraction`此處所計算的值範圍從0，如果 `bitmapProgress` 是0，則會顯示整個點陣圖，而0.25 若為 `bitmapProgress` 1 則顯示點陣圖寬度和高度的一半，則會有效地縮放：

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

圖形交換格式 (GIF) 規格包含的功能，可讓單一 GIF 檔案包含多個可連續顯示的場景（通常在迴圈中）的連續框架。 這些檔案稱為 _動畫 gif_ 。 Web 瀏覽器可以播放動畫 gif，而 SkiaSharp 可讓應用程式從動畫 GIF 檔案中解壓縮畫面格，並依序顯示它們。

[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例包含名為 **Newtons_cradle_animation_book_2.gif** 的動畫 GIF 資源，由 DemonDeLuxe 所建立，並從維琪百科的 [牛頓底座](https://en.wikipedia.org/wiki/Newton%27s_cradle)頁面下載。 **動畫 gif** 頁面包含提供該資訊的 XAML 檔案，並具現化 `SKCanvasView` ：

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

程式碼後端檔案不會被一般化來播放任何動畫 GIF 檔案。 它會忽略一些可用的資訊，特別是重複計數，而且只會在迴圈中播放動畫 GIF。

使用 SkisSharp 來解壓縮動畫 GIF 檔案的框架似乎不會記錄在任何位置，因此下列程式碼的描述比平常更詳細：

動畫 GIF 檔案的解碼會在頁面的函式中進行，而且需要 `Stream` 使用參考點陣圖的物件來建立 `SKManagedStream` 物件和 [`SKCodec`](xref:SkiaSharp.SKCodec) 物件。 [`FrameCount`](xref:SkiaSharp.SKCodec.FrameCount)屬性指出組成動畫的框架數目。

這些框架最後會儲存為個別點陣圖，因此，此函 `FrameCount` 式會在每個畫面格的持續時間內使用配置類型的陣列以及 `SKBitmap` 兩個 `int` 陣列，並 (以簡化動畫邏輯) 累積的持續時間。

[`FrameInfo`](xref:SkiaSharp.SKCodec.FrameInfo)類別的屬性 `SKCodec` 是值的陣列 [`SKCodecFrameInfo`](xref:SkiaSharp.SKCodecFrameInfo) ，每個框架各一個，但是這個程式從該結構所採用的唯一專案是 [`Duration`](xref:SkiaSharp.SKCodecFrameInfo.Duration) 以毫秒為單位的框架。

`SKCodec` 定義名為 [`Info`](xref:SkiaSharp.SKCodec.Info) 的屬性 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) ，但是該值 `SKImageInfo` 表示至少 (此影像) 色彩類型為 `SKColorType.Index8` ，這表示每個圖元都是色彩類型的索引。 為了避免竟然想使用色彩表，程式會使用 [`Width`](xref:SkiaSharp.SKImageInfo.Width) 和 [`Height`](xref:SkiaSharp.SKImageInfo.Height) 資訊來自該結構，以建立其本身的全顏色 `ImageInfo` 值。 每個 `SKBitmap` 都是從該建立的。

的 `GetPixels` 方法 `SKBitmap` `IntPtr` 會傳回參考該點陣圖圖元位的。 尚未設定這些圖元位。 `IntPtr`傳遞給的其中一個 [`GetPixels`](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions)) 方法 `SKCodec` 。 該方法會將框架從 GIF 檔案複製到所參考的記憶體空間中 `IntPtr` 。 此函式會 [`SKCodecOptions`](xref:SkiaSharp.SKCodecOptions) 指出畫面格編號：

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

即使 `IntPtr` 是值，也不需要任何程式 `unsafe` 代碼，因為永遠不會 `IntPtr` 轉換成 c # 指標值。

在解壓縮每個框架之後，此函式會計算所有框架的持續時間，然後以累積的期間初始化另一個陣列。

程式碼後端檔案的其餘部分則專供動畫之用。 `Device.StartTimer`方法是用來啟動計時器，而 `OnTimerTick` 回呼會使用 `Stopwatch` 物件來判斷經過的時間（以毫秒為單位）。 迴圈執行累積的持續時間陣列，足以找出目前的畫面格：

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

每次 `currentframe` 變數變更時， `SKCanvasView` 就會失效，並顯示新的框架：

[![動畫 GIF](animating-images/AnimatedGif.png "動畫 GIF")](animating-images/AnimatedGif-Large.png#lightbox)

當然，您會想要自行執行程式來查看動畫。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Mandelbrot 動畫 (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)