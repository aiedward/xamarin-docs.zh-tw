---
title： "存取 SkiaSharp 點陣圖圖元位" 描述： "探索用來存取和修改 SkiaSharp 點陣圖圖元位的各種技術。"
skiasharp 的 assetid： DBB58522-F816-4A8C-96A5-E0236F16A5C6 author： davidbritch ms-chap： dabritch ms. date： 07/11/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>存取 SkiaSharp 點陣圖圖元位

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如在將[**SkiaSharp 點陣圖儲存至**](saving.md)檔案一文中所見，點陣圖通常會以壓縮格式（例如 JPEG 或 PNG）儲存在檔案中。 在 constrast 中，儲存在記憶體中的 SkiaSharp 點陣圖並不會壓縮。 它會儲存為一連串的圖元。 這種未壓縮的格式有助於將點陣圖傳送到顯示介面。

SkiaSharp 點陣圖所佔用的記憶體區塊是以非常直接的方式組織的：它是以圖元的第一個資料列開頭，由左至右，然後繼續第二個數據列。 若是全彩色點陣圖，每個圖元都包含四個位元組，這表示點陣圖所需的總記憶體空間是其寬度和高度的四倍乘積。

本文說明應用程式如何透過直接存取點陣圖的圖元記憶體區塊或間接存取這些圖元。 在某些情況下，程式可能會想要分析影像的圖元，並建立某種類型的長條圖。 更常見的情況是，應用程式可以藉由以演算法方式建立組成點陣圖的圖元來建立唯一的影像：

![圖元位範例](pixel-bits-images/PixelBitsSample.png "圖元位範例")

## <a name="the-techniques"></a>技術

SkiaSharp 提供數種存取點陣圖圖元位的技巧。 您選擇哪一個通常會在程式碼撰寫便利性（與維護和輕鬆的偵錯工具相關）和效能之間受到危害。 在大部分的情況下，您將使用下列其中一個方法和屬性 `SKBitmap` 來存取點陣圖的圖元：

- `GetPixel`和 `SetPixel` 方法可讓您取得或設定單一圖元的色彩。
- `Pixels`屬性會取得整個點陣圖的圖元色彩陣列，或設定色彩的陣列。
- `GetPixels`傳回點陣圖所使用之圖元記憶體的位址。
- `SetPixels`取代點陣圖所使用之圖元記憶體的位址。

您可以將前兩個技術視為「高階」，而第二個是「低層級」。 還有一些其他方法和屬性可供您使用，但這些都是最重要的。

為了讓您能夠查看這些技術之間的效能差異， [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式包含一個名為「漸層**點陣圖**」的頁面，它會建立一個具有圖元的點陣圖來結合紅色和藍色網底來建立漸層。 程式會建立此點陣圖的八個不同複本，全都使用不同的技術來設定點陣圖圖元。 這八個位圖中的每一個都是以個別的方法來建立，也會設定這項技術的簡短文字描述，並計算設定所有圖元所需的時間。 每個方法都會迴圈執行圖元設定邏輯100次，以取得更佳的效能估計。

### <a name="the-setpixel-method"></a>Bitmap.setpixel 方法

如果您只需要設定或取得數個個別的圖元， [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) 和 [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) 方法就很理想。 針對這兩個方法中的每一個，您可以指定整數資料行和資料列。 不論像素格式為何，這兩種方法都可讓您取得或設定圖元作為 `SKColor` 值：

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

自 `col` 變數的範圍必須介於0到一個小於 `Width` 點陣圖的屬性，且 `row` 範圍從0到一個小於 `Height` 屬性。

以下是漸層**點陣圖**中的方法，會使用方法來設定點陣圖的內容 `SetPixel` 。 點陣圖是256乘以256圖元，而迴圈則 `for` 是硬式編碼的值範圍：

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

針對每個圖元所設定的色彩，其紅色元件會等於點陣圖資料行，而藍色元件則等於資料列。 產生的點陣圖是左上方的黑色、右下方的紅色、位於左下方的藍色，以及位於右下方的洋紅，並有其他地方的漸層。

此 `SetPixel` 方法稱為65536次，不論此方法有多有效率，通常不建議您在有替代方案時，進行許多 API 呼叫。 幸運的是，有數個替代方案。

### <a name="the-pixels-property"></a>圖元屬性

`SKBitmap`定義 [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) 會傳回 `SKColor` 整個點陣圖值陣列的屬性。 您也可以使用 `Pixels` 來設定點陣圖的色彩值陣列：

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

圖元會在陣列中排列，從第一個資料列開始、由左至右、第二個數據列，依此類推。 陣列中的總色彩數等於點陣圖寬度和高度的乘積。

雖然這個屬性看起來很有效率，但請記住，圖元會從點陣圖複製到陣列中，再從陣列複製回點陣圖，而圖元會從和轉換成 `SKColor` 值。

以下是類別中的方法 `GradientBitmapPage` ，可使用屬性來設定點陣圖 `Pixels` 。 方法 `SKColor` 會配置所需大小的陣列，但它可能已使用 `Pixels` 屬性來建立該陣列：

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

請注意，陣列的索引 `pixels` 必須從 `row` 和變數計算出來 `col` 。 資料列會乘以每個資料列中的圖元數（在此案例中為256），然後再加入資料行。

`SKBitmap`也會定義類似的 `Bytes` 屬性，它會傳回整個點陣圖的位元組陣列，但對於全彩色點陣圖而言則比較麻煩。

### <a name="the-getpixels-pointer"></a>GetPixels 指標

存取點陣圖圖元可能是最強大的技術，而 [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels) 不是與 `GetPixel` 方法或 `Pixels` 屬性混淆。 您會立即注意到的差異在於 `GetPixels` ，它會傳回在 c # 程式設計中不常發生的問題：

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [`IntPtr`](xref:System.IntPtr) 類型代表指標。 它會被呼叫 `IntPtr` ，因為它是執行程式之電腦原生處理器上的整數長度，通常是32位或64位的長度。 傳回 `IntPtr` 的 `GetPixels` 是點陣圖物件用來儲存其圖元之實際記憶體區塊的位址。

您可以使用方法，將轉換 `IntPtr` 成 c # 指標類型 [`ToPointer`](xref:System.IntPtr.ToPointer) 。 C # 指標語法與 C 和 c + + 相同：

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr`變數的類型為_byte 指標_。 這個 `ptr` 變數可讓您存取用來儲存點陣圖圖元的個別記憶體位元組。 您可以使用類似下面的程式碼，從這個記憶體讀取一個位元組，或將一個位元組寫入記憶體：

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

在此內容中，星號是 c #_間接運算子_，用來參考所指向的記憶體內容 `ptr` 。 一開始 `ptr` 會指向點陣圖第一個資料列第一個圖元的第一個位元組，但是您可以在變數上執行算術，將 `ptr` 它移至點陣圖內的其他位置。

其中一個缺點是，您只能 `ptr` 在以關鍵字標記的程式碼區塊中使用這個變數 `unsafe` 。 此外，元件必須標示為允許 unsafe 區塊。 這會在專案的屬性中完成。

在 c # 中使用指標的功能非常強大，但也非常危險。 您必須小心，不要存取超出指標應參考的記憶體。 這就是為什麼指標使用與「unsafe」這個字相關聯的原因。

以下是使用方法的類別中的方法 `GradientBitmapPage` `GetPixels` 。 請注意 `unsafe` 包含所有使用位元組指標之程式碼的區塊：

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

`ptr`第一次從方法取得變數時 `ToPointer` ，會指向點陣圖第一個資料列最左邊圖元的第一個位元組。 `for`和的迴圈 `row` `col` 已設定，因此可以在 `ptr` `++` 設定每個圖元的每個位元組之後，以運算子遞增。 若是透過圖元的其他99迴圈，必須將 `ptr` 設定回點陣圖的開頭。

每個圖元都是四個位元組的記憶體，因此必須分別設定每個位元組。 此處的程式碼假設位元組的順序是紅色、綠色、藍色和 Alpha，與 `SKColorType.Rgba8888` 色彩類型一致。 您可能還記得這是 iOS 和 Android 的預設色彩類型，但不適用於通用 Windows 平臺。 根據預設，UWP 會建立 `SKColorType.Bgra8888` 色彩類型的點陣圖。 基於這個理由，預期會在該平臺上看到一些不同的結果！

可以將傳回的值轉換 `ToPointer` 成 `uint` 指標，而不是 `byte` 指標。 這可讓您在一個語句中存取整個圖元。 將 `++` 運算子套用至該指標，會以四個位元組遞增，以指向下一個圖元：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

圖元是使用方法設定的 `MakePixel` ，它會從紅色、綠色、藍色和 Alpha 元件中，建立整數圖元。 請記住， `SKColorType.Rgba8888` 格式具有如下所示的圖元位元組順序：

RR GG BB AA

但對應至這些位元組的整數是：

AABBGGRR

此整數的最小有效位元組會先根據極小的架構來儲存。 `MakePixel`對於色彩類型的點陣圖，這個方法將無法正確運作 `Bgra8888` 。

`MakePixel`方法會加上 [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) 選項，以鼓勵編譯器避免將此方法設為不同的方法，但改為編譯呼叫方法的程式碼。 這應該會改善效能。

有趣的 `SKColor` 是，結構定義從 `SKColor` 到不帶正負號整數的明確轉換，這表示 `SKColor` 可以建立值，而可以使用的轉換， `uint` 而不是 `MakePixel` ：

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

唯一的問題是：這是 `SKColor` 以色彩類型的順序或色彩類型的值的整數格式， `SKColorType.Rgba8888` `SKColorType.Bgra8888` 還是完全是其他東西？ 該問題的答案應該很快就會顯示出來。

### <a name="the-setpixels-method"></a>SetPixels 方法

`SKBitmap`也會定義名為的方法 [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)) ，您可以在此呼叫，如下所示：

```csharp
bitmap.SetPixels(intPtr);
```

回想一下，會取得 `GetPixels` `IntPtr` 參考點陣圖用來儲存其圖元的記憶體區塊。 `SetPixels`呼叫會將該記憶體區塊_取代_為指定之所參考的記憶體區塊 `IntPtr` 做為 `SetPixels` 引數。 然後，點陣圖會釋出先前使用的記憶體區塊。 下一次 `GetPixels` 呼叫時，它會取得以設定的記憶體區塊 `SetPixels` 。

一開始，似乎 `SetPixels` 不會讓您的電源和效能變得更 `GetPixels` 方便。 當 `GetPixels` 您取得點陣圖記憶體區塊並加以存取時。 `SetPixels`當您配置和存取一些記憶體，然後將其設定為點陣圖記憶體區塊。

但使用 `SetPixels` 提供了不同的語法優勢：它可讓您使用陣列來存取點陣圖圖元位。 以下是中 `GradientBitmapPage` 示範這項技術的方法。 方法會先定義一個與點陣圖圖元的位元組對應的多維度位元組陣列。 第一個維度是資料列，第二個維度是資料行，而第三個維度對應為每個圖元的四個元件：

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

然後，在以圖元填滿陣列之後， `unsafe` `fixed` 就會使用區塊和語句來取得指向這個陣列的位元組指標。 然後，該位元組指標就可以轉換成 `IntPtr` ，以便傳遞給 `SetPixels` 。

您所建立的陣列不一定要是位元組陣列。 它可以是只有兩個數據列和資料行之維度的整數陣列：

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

`MakePixel`方法再次用來將色彩元件結合成32位圖元。

就完整性而言，以下是相同的程式碼，但具有 `SKColor` 轉換成不帶正負號整數的值：

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>比較技巧

[漸層**色彩**] 頁面的 [函式] 會呼叫以上所示的所有八個方法，並儲存結果：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

此函式會藉由建立 `SKCanvasView` 來顯示結果點陣圖來結束。 此 `PaintSurface` 處理常式會將它的表面分成八個矩形，並呼叫 `Display` 來顯示每一項：

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

為了讓編譯器將程式碼優化，此頁面是在**發行**模式中執行。 以下是在 MacBook Pro、第5部 Android 手機和 Surface Pro 3 （執行 Windows 10）上的 iPhone 8 模擬器上執行的頁面。 由於硬體的差異，請避免比較裝置之間的效能時間，而是查看每個裝置上的相對時間：

[![漸層點陣圖](pixel-bits-images/GradientBitmap.png "漸層點陣圖")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

以下是合併執行時間（以毫秒為單位）的資料表：

| API       | 資料類型 | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| Bitmap.setpixel  |           | 3.17 |   10.77 | 3.49 |
| 阻擋的    |           | 0.32 |    1.23 | 0.07 |
| GetPixels | byte      | 0.09 |    0.24 | 0.10 |
|           | uint      | 0.06 |    0.26 | 0.05 |
|           | SKColor   | 0.29 |    0.99 | 0.07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0.14 |    0.69 | 0.06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

如預期，呼叫 `SetPixel` 65536 次是設定點陣圖圖元的最 effeicient 方式。 填入 `SKColor` 陣列和設定 `Pixels` 屬性會比較好，而且甚至比一些 `GetPixels` 和技巧更有利 `SetPixels` 。 使用 `uint` 圖元值通常比設定不同 `byte` 的元件更快，而將 `SKColor` 值轉換成不帶正負號的整數會增加處理常式的額外負荷。

比較各種漸層也很有趣：每個平臺的前幾個資料列都相同，並依預期顯示漸層。 這表示 `SetPixel` 方法和 `Pixels` 屬性會正確地從色彩建立圖元，而不論基礎像素格式為何。

IOS 和 Android 螢幕擷取畫面的後續兩個數據列也相同，這會確認 `MakePixel` 已針對這些平臺的預設像素格式正確定義了小方法 `Rgba8888` 。

IOS 和 Android 螢幕擷取畫面的底部資料列是回溯的，這表示轉換值所取得的不帶正負號整數 `SKColor` 格式為：

AARRGGBB

這些位元組的順序如下：

BB GG RR AA

這是 `Bgra8888` 順序，而不是 `Rgba8888` 排序。 `Brga8888`格式是通用 Windows 平臺的預設值，這就是為什麼該螢幕擷取畫面的最後一個資料列的漸層與第一個資料列相同的原因。 但中間的兩個數據列不正確，因為建立這些點陣圖的程式碼會假設 `Rgba8888` 排序。

如果您想要使用相同的程式碼來存取每個平臺上的圖元位，可以 `SKBitmap` 使用 `Rgba8888` 或格式明確建立 `Bgra8888` 。 如果您想要將 `SKColor` 值轉換成點陣圖圖元，請使用 `Bgra8888` 。

## <a name="random-access-of-pixels"></a>隨機存取圖元

[漸層 `FillBitmapBytePtr` `FillBitmapUintPtr` **點陣圖**] 頁面中的和方法會從 `for` 設計來依序填滿點陣圖的迴圈（從頂端資料列到底端資料列），以及從左至右的每個資料列中獲益。 您可以使用遞增指標的相同語句來設定圖元。

有時候，必須隨機存取圖元，而不是順序。 如果您使用 `GetPixels` 方法，則必須根據資料列和資料行來計算指標。 這會在**彩虹正弦**頁面中示範，它會建立一個點陣圖，以顯示一段正弦曲線迴圈形式的彩虹。

彩虹的色彩最容易使用 HSL （色調、飽和度、亮度）色彩模型來建立。 `SKColor.FromHsl`方法 `SKColor` 會使用從0到360（例如圓形的角度，但從紅色到綠色、藍色，再回到紅色）和飽和度和亮度值（範圍從0到100）來建立值。 對於彩虹的色彩，飽和度應設定為最大值100，而亮度則設為50的中點。

**彩虹的正弦**會藉由迴圈流覽點陣圖的資料列來建立此影像，然後再迴圈到360色調值。 根據每個色調值，它會計算同時以正弦值為基礎的點陣圖資料行：

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
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
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

請注意，此函式會根據 `SKColorType.Bgra8888` 下列格式建立點陣圖：

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

這可讓程式使用 `SKColor` 值轉換成 `uint` 圖元，而不必擔心。 雖然它不會在此特定程式中扮演某個角色，但當您使用 `SKColor` 轉換來設定圖元時，您也應該指定， `SKAlphaType.Unpremul` 因為 `SKColor` 不會依 Alpha 值 premultiply 其色彩元件。

然後，此函數會使用 `GetPixels` 方法來取得點陣圖第一個圖元的指標：

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

針對任何特定的資料列和資料行，必須將位移值新增至 `basePtr` 。 此位移是點陣圖寬度的資料列倍，加上資料行：

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

`SKColor`此值會使用這個指標儲存在記憶體中：

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

在的 `PaintSurface` 處理常式中 `SKCanvasView` ，點陣圖會伸展以填滿顯示區域：

[![彩虹正弦](pixel-bits-images/RainbowSine.png "彩虹正弦")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>從一個點陣圖到另一個

非常多的影像處理工作牽涉到修改圖元，因為它們會從一個點陣圖傳輸到另一個。 這項技術會在 [**色彩調整**] 頁面中示範。 頁面會載入其中一個點陣圖資源，然後允許您使用三個視圖修改影像 `Slider` ：

[![色彩調整](pixel-bits-images/ColorAdjustment.png "色彩調整")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

針對每個圖元色彩，第一個會 `Slider` 將介於0到360之間的值加上色調，然後使用模數運算子來保留0和360之間的結果，並有效地沿著色譜（如 UWP 螢幕擷取畫面所示）改變色彩。 第二種 `Slider` 方式可讓您選取介於0.5 和2之間的乘法因數以套用至飽和度，而第三個對亮度也是 `Slider` 相同的，如 Android 螢幕擷取畫面所示。

程式會維護兩個位圖、名為的原始來源點陣圖 `srcBitmap` 和名為的調整後的目的地點陣圖 `dstBitmap` 。 每次 `Slider` 移動時，程式都會計算中的所有新圖元 `dstBitmap` 。 當然，使用者會以非常快速的方式移動視圖來進行實驗 `Slider` ，因此您需要能夠管理的最佳效能。 這牽涉到 `GetPixels` 來源和目的地點陣圖的方法。

[**色彩調整**] 頁面不會控制來源與目的地點陣圖的色彩格式。 相反地，它包含和格式稍有不同的邏輯 `SKColorType.Rgba8888` `SKColorType.Bgra8888` 。 來源和目的地可以是不同的格式，而且程式仍然有效。

以下的程式除外，這是將 `TransferPixels` 圖元從來源傳送到目的地的重要方法。 此函式會將設定 `dstBitmap` 為等於 `srcBitmap` 。 `PaintSurface`處理常式會顯示 `dstBitmap` ：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

`ValueChanged`Views 的處理常式會 `Slider` 計算調整值和呼叫 `TransferPixels` 。

整個 `TransferPixels` 方法會標示為 `unsafe` 。 一開始會取得兩個位圖之圖元位的位元組指標，然後在所有的資料列和資料行中執行迴圈。 從來源點陣圖，方法會取得每個圖元的四個位元組。 這些可能是 `Rgba8888` 或 `Bgra8888` 順序。 檢查色彩類型是否允許 `SKColor` 建立值。 接著會將 HSL 元件解壓縮、調整，並用來重新建立 `SKColor` 值。 視目的地點陣圖是 `Rgba8888` 或 `Bgra8888` 而定，位元組會儲存在目的地 bitmp 中：

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

藉由為來源與目的地點陣圖的各種色彩類型組合建立個別的方法，並避免檢查每個圖元的類型，這種方法的效能可能會更進一步改善。 另一個選項是 `for` 根據色彩類型，針對變數擁有多個迴圈 `col` 。

## <a name="posterization"></a>Posterization

包含存取圖元位的另一個常見作業是_posterization_。 如果以點陣圖圖元來編碼的色彩已縮減，讓結果類似于使用有限色板的手繪海報，則為數字。

[**色調分離**] 頁面會在其中一個猴子映射上執行此程式：

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
        }

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
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

此函式中的程式碼會存取每個圖元，以0xE0E0E0F光圈值執行位 AND 運算，然後將結果儲存回點陣圖中。 [值] 0xE0E0E0FF 會保留每個色彩元件的高3個位，並將較低的5位設定為0。 點陣圖會縮減為 2<sup>9</sup>或512色，而不是 2<sup>24</sup>或16777216色：

[![色調](pixel-bits-images/Posterize.png "色調")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
