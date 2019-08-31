---
title: 以動畫顯示 SkiaSharp 點陣圖
description: 了解如何執行點陣圖的動畫，循序顯示一系列的點陣圖，並呈現動畫的 GIF 檔案。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 97142ADC-E2FD-418C-8A09-9C561AEE5BFD
author: davidbritch
ms.author: dabritch
ms.date: 07/12/2018
ms.openlocfilehash: 74fb5bdc13facd52c06e8b7c2138d1d5db9d4565
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199504"
---
# <a name="animating-skiasharp-bitmaps"></a>以動畫顯示 SkiaSharp 點陣圖

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

通常建立動畫 SkiaSharp 圖形的應用程式呼叫`InvalidateSurface`上`SKCanvasView`以固定費率，通常每隔 16 毫秒。 讓介面失效，觸發程序呼叫`PaintSurface`重繪其顯示的處理常式。 當視覺效果會重新繪製 60 次第二個，看起來就像順利建立動畫。

不過，如果圖形是太複雜，無法呈現在 16 毫秒，動畫可能會變得抖動。 程式設計人員可能會選擇將重新整理速率降低 30 倍或 15 次第二個，但有時甚至是不足夠。 有時候圖形是過於複雜，它們只是無法呈現即時的。

其中一個解決方案是事先準備動畫所呈現的動畫點陣圖的一連串個別的畫面格。 若要顯示動畫，才需要顯示這些點陣圖循序 60 次秒。

當然，這可能是點陣圖，很多，但這如何巨量預算 3D 動畫的電影進行。 3D 圖形是許多太複雜，無法呈現即時的。 大量處理時間，才能呈現每個畫面格。 觀看影片時所看到的是點陣圖的基本上是點陣圖的一系列。

您可以執行類似 SkiaSharp。 這篇文章會示範兩種類型的點陣圖的動畫。 第一個範例是 Mandelbrot 集合的動畫：

![建立動畫範例](animating-images/AnimatingSample.png "動畫範例")

第二個範例示範如何使用 SkiaSharp 呈現的動畫的 GIF 檔案。

## <a name="bitmap-animation"></a>點陣圖的動畫

Mandelbrot 集合是以視覺化方式酷炫但 computionally 冗長。 (如 Mandelbrot 集合以及此處所使用的數學運算的討論，請參閱 <<c0> [ 的第 20 章_使用 Xamarin.Forms 建立行動應用程式_](https://xamarin.azureedge.net/developer/xamarin-forms-book/XamarinFormsBook-Ch20-Apr2016.pdf) 666 頁面上啟動。 下列說明假設該背景知識。）

[ **Mandelbrot 動畫**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)範例使用點陣圖動畫來模擬連續的顯示比例 Mandelbrot 集合中的固定元素。 放大後面縮小，並再循環重複下去，或直到您結束程式。

這個動畫的程式是藉由建立最多 50 的點陣圖，它會儲存在應用程式的本機儲存體中準備。 每個點陣圖會包含一半的寬度和高度複數平面為先前的點陣圖。 (在程式中，這些點陣圖 non-deterministic 來代表整數_縮放層級_。)序列中隨即顯示點陣圖。 每個點陣圖的縮放比例是以提供從一個點陣圖的 smooth 進展到另一個動畫顯示。

例如最終程式中的第 20 章所述_使用 Xamarin.Forms 建立行動應用程式_，Mandelbrot 集合中的計算**Mandelbrot 動畫**具有八個非同步方法參數。 參數包含複雜的中心點，以及寬度和高度圍繞的中心點的複數平面。 接下來三個參數為像素寬度和要建立點陣圖的高度和遞迴計算的反覆項目數目上限。 `progress`參數用來顯示這項計算的進度。 `cancelToken`參數不會用於此程式：

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

方法會傳回型別的物件`BitmapInfo`，提供建立點陣圖的資訊：

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

**Mandelbrot 動畫**XAML 檔案包含兩個`Label`檢視`ProgressBar`，和`Button`以及`SKCanvasView`:

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

程式碼後置檔案一開始會定義三個重要的常數和點陣圖的陣列：

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

在某些時候，您可能需要變更`COUNT`值為 50，若要查看完整的動畫。 50 以上的值不是很有用的。 48 左右的縮放層級，周圍的雙精確度浮點數的解析度會變得不足 Mandelbrot 集合計算。 頁面 684 上討論此問題_使用 Xamarin.Forms 建立行動應用程式_。

`center`值是非常重要。 這是動畫縮放的焦點。 在檔案中的三個值所使用的三個的最終螢幕擷取畫面中的第 20 章該些_使用 Xamarin.Forms 建立行動應用程式_頁面上的 684，但您可以試驗來擬定您自己的值的其中一個該章節中的程式。

**Mandelbrot 動畫**範例會儲存這些`COUNT`本機應用程式儲存體中的點陣圖。 五十個點陣圖需要超過 20 mb 的儲存體，您在裝置上，因此您可能想要知道這些點陣圖會佔用，多少儲存體，並在某個時間點您可能想要刪除它們全部。 這是在底部這兩種方法的目的`MainPage`類別：

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

雖然程式為動畫形式這些相同的點陣圖，因為程式會將它們保留在記憶體中，您可以刪除本機儲存體中的點陣圖。 但是下, 一次執行程式，它必須重新建立的點陣圖。

儲存在本機的應用程式存放區中的點陣圖併入`center`值在其檔名，因此，如果您變更`center`設定時，現有的點陣圖不會取代在儲存體，而且將會繼續佔用的空間。

以下是方法的`MainPage`會使用建構檔名，以及`MakePixel`定義像素值的方法會依據色彩元件：

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

`zoomLevel`參數來`FilePath`範圍從 0 到`COUNT`常數減 1。

`MainPage`建構函式呼叫`LoadAndStartAnimation`方法：

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

`LoadAndStartAnimation`方法會負責存取應用程式載入任何可能已建立先前已執行此程式的點陣圖的本機儲存體。 會循環`zoomLevel`值從 0 到`COUNT`。 如果檔案存在，它會載入到`bitmaps`陣列。 否則，它需要建立點陣圖的特定`center`並`zoomLevel`值，藉由呼叫`Mandelbrot.CalculateAsync`。 該方法會取得每個像素，這個方法會將色彩轉換的反覆項目計數：

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

請注意程式會儲存在本機的應用程式存放區中，而不是裝置的相片媒體櫃中這些點陣圖。 .NET Standard 2.0 程式庫可讓您可使用熟悉`File.OpenRead`和`File.WriteAllBytes`這項工作的方法。

已建立或載入記憶體中的所有點陣圖之後，此方法便會啟動`Stopwatch`物件並呼叫`Device.StartTimer`。 `OnTimerTick`方法會呼叫每個 16 毫秒。

`OnTimerTick` 計算`time`值以毫秒為單位，範圍從 0 到 6000 時間`COUNT`，其中 apportions 六秒的每個點陣圖顯示。 `progress`值使用`Math.Sin`值來建立將會變慢的循環，開頭的正弦曲線動畫並速度較慢，因為它的結尾會反轉方向。

`progress`值範圍從 0 到`COUNT`。 這表示的整數部分`progress`到索引`bitmaps`陣列中的小數部分時`progress`指出該特定的點陣圖的縮放層級。 這些值會儲存在`bitmapIndex`並`bitmapProgress`欄位，並且由顯示`Label`和`Slider`XAML 檔案中。 `SKCanvasView`失效來更新點陣圖顯示：

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

最後，`PaintSurface`處理常式`SKCanvasView`計算目的地矩形，同時維持外觀比例顯示一樣大的點陣圖。 來源矩形根據`bitmapProgress`值。 `fraction`值以下計算範圍是 0 時，從`bitmapProgress`為 0 時，顯示整個點陣圖，0.25 時`bitmapProgress`為 1 到一半的寬度和高度的點陣圖，有效地放大顯示：

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

以下是執行的程式：

[![Mandelbrot 動畫](animating-images/MandelbrotAnimation.png "Mandelbrot 動畫")](animating-images/MandelbrotAnimation-Large.png#lightbox)

## <a name="gif-animation"></a>動畫 GIF

圖形交換格式 (GIF) 規格包含可以使用單一的 GIF 檔案包含多個循序的框架場景連續，通常在迴圈中可顯示的一項功能。 這些檔案也稱為_動畫 Gif_。 網頁瀏覽器可以播放動畫的 Gif，和 SkiaSharp 可讓應用程式擷取的畫面格動畫 GIF 檔案，並循序顯示它們。

[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例包含名為的動畫的 GIF 資源**Newtons_cradle_animation_book_2.gif** DemonDeLuxe 所建立，並從下載[牛頓的底座](https://en.wikipedia.org/wiki/Newton%27s_cradle)維基百科中的頁面。 **動畫 GIF**頁面包含 XAML 檔案，提供該資訊，並具現化`SKCanvasView`:

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

若要播放任何動畫的 GIF 檔案從未一般化的程式碼後置檔案。 它會忽略的部分資訊可供使用，特別是，重複計數，並只播放在迴圈中的動畫 GIF。

SkisSharp 擷取的畫面格動畫 GIF 檔案的使用似乎並沒有任何位置，記錄比平常更詳細的描述後面的程式碼很：

在頁面的建構函式，就會發生的動畫 GIF 檔案解碼，並要求`Stream`參考點陣圖物件用來建立`SKManagedStream`物件，然後[ `SKCodec` ](xref:SkiaSharp.SKCodec)物件。 [ `FrameCount` ](xref:SkiaSharp.SKCodec.FrameCount)屬性會指出組成動畫畫面格數目。

因此建構函式會使用最後這些框架儲存為個別的點陣圖`FrameCount`配置的型別陣列`SKBitmap`以及兩個`int`陣列的每個畫面格並 （簡化動畫邏輯） 的持續時間累積持續時間。

[ `FrameInfo` ](xref:SkiaSharp.SKCodec.FrameInfo)屬性`SKCodec`類別是陣列[ `SKCodecFrameInfo` ](xref:SkiaSharp.SKCodecFrameInfo)值，一個用於每個畫面格，但此程式會從該結構的作法就是一種[ `Duration` ](xref:SkiaSharp.SKCodecFrameInfo.Duration)的框架，以毫秒為單位。

`SKCodec` 定義屬性，名為[ `Info` ](xref:SkiaSharp.SKCodec.Info)型別的[ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo)，但該`SKImageInfo`值表示 (至少這個映像) 色彩類型是`SKColorType.Index8`，這表示，每個像素都是色彩類型中的索引。 若要避免麻煩色彩表，程式會使用[ `Width` ](xref:SkiaSharp.SKImageInfo.Width)並[ `Height` ](xref:SkiaSharp.SKImageInfo.Height)從該結構來建構它的資訊是擁有全彩`ImageInfo`值。 每個`SKBitmap`從所建立。

`GetPixels`方法`SKBitmap`傳回`IntPtr`參考該點陣圖的像素位元。 有尚未設定這些像素位元。 該`IntPtr`傳遞至其中一個[ `GetPixels` ](xref:SkiaSharp.SKCodec.GetPixels(SkiaSharp.SKImageInfo,System.IntPtr,SkiaSharp.SKCodecOptions))方法`SKCodec`。 該方法將框架從 GIF 檔案複製到所參考的記憶體空間`IntPtr`。 [ `SKCodecOptions` ](xref:SkiaSharp.SKCodecOptions)建構函式會指出畫面格數目：

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

儘管`IntPtr`值，否`unsafe`程式碼是必要的因為`IntPtr`永遠不會轉換成 C# 指標值。

已擷取每個畫面格之後，建構函式註冊的所有框架，持續時間總計，並再初始化另一個陣列累積的持續時間。

動畫被專用的程式碼後置檔案的其餘部分。 `Device.StartTimer`方法用來啟動計時器，而`OnTimerTick`回呼使用`Stopwatch`物件來判斷經過的時間，以毫秒為單位。 循環使用累積的持續時間陣列便可找到目前的框架：

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

每次`currentframe`變數的變更，`SKCanvasView`失效，並顯示新的框架：

[![動畫 GIF](animating-images/AnimatedGif.png "動畫 GIF")](animating-images/AnimatedGif-Large.png#lightbox)

當然，您會想要執行程式，看看動畫。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Mandelbrot 動畫 （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-mandelanima)
