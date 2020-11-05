---
title: 存取 SkiaSharp 點陣圖圖元位
description: 探索用來存取和修改 SkiaSharp 點陣圖圖元位的各種技術。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6e3edf7d0e7630429f8f1c76009987ee8a4b737a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375326"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>存取 SkiaSharp 點陣圖圖元位

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如同您在將 [**SkiaSharp 點陣圖儲存至**](saving.md)檔案時所看到的，點陣圖一般會以壓縮格式（例如 JPEG 或 PNG）儲存在檔案中。 在 constrast 中，不會壓縮儲存在記憶體中的 SkiaSharp 點陣圖。 它會儲存為連續的圖元序列。 這種未壓縮的格式有助於將點陣圖傳送至顯示介面。

SkiaSharp 點陣圖所佔用的記憶體區塊是以非常直接的方式來組織的：它是從第一個資料列開始，從左至右，然後繼續第二個數據列。 針對全彩點陣圖，每個圖元都包含四個位元組，這表示點陣圖所需的總記憶體空間是其寬度和高度乘積的四倍。

本文描述應用程式如何直接存取點陣圖的圖元記憶體區塊或間接存取這些圖元，以取得這些圖元的存取權。 在某些情況下，程式可能會想要分析影像的圖元，並建立某種類型的長條圖。 更常見的情況是，應用程式可以藉由演算法建立組成點陣圖的圖元來建立唯一的影像：

![圖元位範例](pixel-bits-images/PixelBitsSample.png "圖元位範例")

## <a name="the-techniques"></a>技術

SkiaSharp 提供數種存取點陣圖圖元位的技巧。 您選擇哪一個，通常是在程式碼撰寫便利性 (之間的折衷，與維護和簡化) 和效能的調試有關。 在大部分的情況下，您將使用下列其中一個方法和屬性 `SKBitmap` 來存取點陣圖的圖元：

- `GetPixel`和 `SetPixel` 方法可讓您取得或設定單一圖元的色彩。
- `Pixels`屬性會取得整個點陣圖的圖元色彩陣列，或設定色彩的陣列。
- `GetPixels` 傳回點陣圖所使用的圖元記憶體位址。
- `SetPixels` 取代點陣圖所使用的圖元記憶體位址。

您可以將前兩個技巧視為「高階」，而第二個是「低等級」。 還有一些其他方法和屬性可供您使用，但這些都是最重要的方法和屬性。

為了讓您能夠查看這些技術之間的效能差異， [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) 應用程式包含一個名為漸層 **點陣圖** 的頁面，它會建立一個點陣圖，並以圖元結合紅色和藍色陰影來建立漸層。 此程式會建立此點陣圖的八種不同複本，全都使用不同的技術來設定點陣圖圖元。 這八個位圖中的每一個都是以不同的方法建立，也會設定技術的簡短文字描述，並計算設定所有圖元所需的時間。 每個方法都會逐一執行圖元設定邏輯100次，以獲得更好的效能評估。

### <a name="the-setpixel-method"></a>Bitmap.setpixel 方法

如果您只需要設定或取得數個個別圖元， [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) 和 [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) 方法是理想的。 針對這兩種方法，您可以指定整數資料行和資料列。 無論像素格式為何，這兩種方法都可讓您取得或設定圖元作為 `SKColor` 值：

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

自 `col` 變數的範圍必須從0到1，小於 `Width` 點陣圖的屬性，而且 `row` 範圍從0到1小於 `Height` 屬性。

以下是漸層 **點陣圖** 中的方法，會使用方法來設定點陣圖的內容 `SetPixel` 。 點陣圖是256乘以256圖元，而 `for` 迴圈會以值範圍進行硬式編碼：

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

每個圖元的色彩設定都具有等於點陣圖資料行的紅色元件，而藍色元件則等於資料列。 結果點陣圖會在左上角紅色、在左下方為紅色、在左下角為紅色，並在其他地方使用漸層。

`SetPixel`方法稱為65536次，不論此方法的效率如何，通常不建議在有替代方案的情況下進行許多 API 呼叫。 幸運的是，有幾個替代方案。

### <a name="the-pixels-property"></a>圖元屬性

`SKBitmap` 定義 [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) 會傳回 `SKColor` 整個點陣圖值陣列的屬性。 您也可以使用 `Pixels` 來設定點陣圖的色彩值陣列：

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

圖元會排列于陣列中，從第一個資料列開始，從左至右，然後是第二個數據列，依此類推。 陣列中的色彩總數等於點陣圖寬度和高度的乘積。

雖然這個屬性似乎很有效率，但請記住，圖元會從點陣圖複製到陣列，然後從陣列複製到點陣圖，而圖元會從和轉換成 `SKColor` 值。

以下是 `GradientBitmapPage` 使用屬性來設定點陣圖的類別中的方法 `Pixels` 。 方法 `SKColor` 會配置所需大小的陣列，但它可以使用 `Pixels` 屬性來建立該陣列：

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

請注意， `pixels` 必須從和變數計算陣列的索引 `row` `col` 。 在此情況下，資料列會乘以每個資料列中的圖元數 (256) ，然後加入資料行。

`SKBitmap` 也會定義類似 `Bytes` 的屬性，此屬性會傳回整個點陣圖的位元組陣列，但對全彩點陣圖來說比較麻煩。

### <a name="the-getpixels-pointer"></a>GetPixels 指標

存取點陣圖圖元可能是最強大的技巧，而 [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels) 不是與 `GetPixel` 方法或 `Pixels` 屬性混淆。 您會立即注意到，因為 `GetPixels` 它會傳回 c # 程式設計中不常見的內容：

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [`IntPtr`](xref:System.IntPtr) 型別代表指標。 呼叫它的 `IntPtr` 原因是，它是程式執行所在電腦的原生處理器上的整數長度，通常是32位或64位的長度。 傳回 `IntPtr` 的 `GetPixels` 會是點陣圖物件用來儲存其圖元的實際記憶體區塊的位址。

您可以使用方法，將轉換 `IntPtr` 成 c # 指標類型 [`ToPointer`](xref:System.IntPtr.ToPointer) 。 C # 指標語法與 C 和 c + + 相同：

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr`變數的類型為 _byte 指標_ 。 此 `ptr` 變數可讓您存取用來儲存點陣圖圖元的個別記憶體位元組。 您可以使用像這樣的程式碼從這個記憶體讀取位元組，或將位元組寫入記憶體：

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

在此內容中，星號是 c # _間接運算子_ ，可用來參考所指向之記憶體的內容 `ptr` 。 一開始， `ptr` 會指向點陣圖第一個資料列第一個圖元的第一個位元組，但您可以對變數執行算數運算， `ptr` 以將其移至點陣圖內的其他位置。

其中一個缺點是您只能在以 `ptr` 關鍵字標示的程式碼區塊中使用此 `unsafe` 變數。 此外，元件也必須標示為允許 unsafe 區塊。 這是在專案的屬性中完成。

使用 c # 中的指標非常強大，但也很危險。 您必須小心，不要存取指標應該參考的記憶體以外的記憶體。 這就是為什麼指標使用與「unsafe」一字相關聯的原因。

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

`ptr`第一次從方法取得變數時 `ToPointer` ，它會指向點陣圖第一個資料列最左邊圖元的第一個位元組。 `for`和的迴圈 `row` `col` 會進行設定，以便在 `ptr` `++` 每個圖元的每個位元組設定之後，使用運算子來遞增。 針對其他99迴圈到圖元， `ptr` 必須將設定回點陣圖的開頭。

每個圖元都是四個位元組的記憶體，因此每個位元組都必須分別設定。 此處的程式碼假設位元組的順序是紅色、綠色、藍色和 Alpha，與 `SKColorType.Rgba8888` 色彩類型一致。 您可能還記得這是 iOS 和 Android 的預設色彩類型，但不適用於通用 Windows 平臺。 UWP 預設會建立具有色彩類型的點陣圖 `SKColorType.Bgra8888` 。 基於這個理由，預期會在該平臺上看到一些不同的結果！

您可以將傳回的值轉換 `ToPointer` 成 `uint` 指標，而不是 `byte` 指標。 這可讓您在單一語句中存取整個圖元。 將 `++` 運算子套用至該指標會以四個位元組遞增，以指向下一個圖元：

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

您可以使用方法設定圖元， `MakePixel` 以從紅色、綠色、藍色和 Alpha 元件來建立整數圖元。 請記住， `SKColorType.Rgba8888` 格式具有如下的圖元位元組順序：

RR GG BB AA

但是，對應于這些位元組的整數是：

AABBGGRR

最小的整數位節會先根據位元組由小到大的架構來儲存。 如果 `MakePixel` 點陣圖具有色彩類型，這個方法將無法正常運作 `Bgra8888` 。

`MakePixel`方法會加上旗標， [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) 以鼓勵編譯器避免將此方法設為不同的方法，而是要編譯呼叫方法的程式碼。 這應該會改善效能。

有趣的是，此 `SKColor` 結構定義了從 `SKColor` 到不帶正負號的整數的明確轉換，這表示 `SKColor` 可以建立一個值，而且 `uint` 可以使用轉換來取代 `MakePixel` ：

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

唯一的問題是：此值的整數格式是 `SKColor` 以 `SKColorType.Rgba8888` 色彩類型或色彩類型的順序， `SKColorType.Bgra8888` 還是完全是其他專案？ 這個問題的答案應該很快就會顯示。

### <a name="the-setpixels-method"></a>SetPixels 方法

`SKBitmap` 也會定義名為的方法 [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)) ，您可以呼叫這個方法，如下所示：

```csharp
bitmap.SetPixels(intPtr);
```

回想一下，會取得 `GetPixels` `IntPtr` 參考點陣圖用來儲存其圖元的記憶體區塊。 `SetPixels`呼叫會 _replaces_ 以 `IntPtr` 指定的做為引數所參考的記憶體區塊來取代該記憶體區塊 `SetPixels` 。 然後點陣圖會釋出先前使用的記憶體區塊。 下一次 `GetPixels` 呼叫時，它會取得使用設定的記憶體區塊 `SetPixels` 。

一開始，似乎會 `SetPixels` 讓您沒有比較不方便的電源和效能 `GetPixels` 。 當 `GetPixels` 您取得點陣圖記憶體區塊並加以存取時。 `SetPixels`當您配置和存取某些記憶體時，然後將它設定為點陣圖記憶體區塊。

但是，使用可 `SetPixels` 提供不同的語法優勢：它可讓您使用陣列來存取點陣圖圖元位。 以下是中的方法 `GradientBitmapPage` ，示範這項技術。 方法會先定義多維度位元組陣列，對應至點陣圖圖元的位元組。 第一個維度是資料列、第二個維度是資料行，而第三個維度對應至每個圖元的四個元件：

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

然後，在陣列填滿圖元之後， `unsafe` `fixed` 會使用區塊和語句來取得指向這個陣列的位元組指標。 然後，可以將該位元組指標轉換成以 `IntPtr` 傳遞至 `SetPixels` 。

您建立的陣列不一定要是位元組陣列。 它可以是只有兩個數據列和資料行維度的整數陣列：

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

`MakePixel`方法會再次用來將色彩元件結合成32位圖元。

就完整性而言，以下是相同的程式碼，但將 `SKColor` 值轉換成不帶正負號的整數：

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

### <a name="comparing-the-techniques"></a>比較技術

漸層 **色彩** 頁面的函式會呼叫以上所示的八種方法，並儲存結果：

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

此函式會藉由建立 `SKCanvasView` 來顯示結果點陣圖來結束。 此 `PaintSurface` 處理常式會將它的表面分成八個矩形，並呼叫 `Display` 以顯示每一個矩形：

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

為了讓編譯器將程式碼優化，此頁面是在 **發行** 模式中執行。 以下是在 MacBook Pro 上的 iPhone 8 模擬器上執行的頁面，第5部 Android 手機和執行 Windows 10 Surface Pro 3。 由於硬體差異，請避免比較裝置之間的效能時間，但請查看每個裝置上的相對時間：

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

如預期般，呼叫 `SetPixel` 65536 時間是設定點陣圖圖元的最 effeicient 方式。 填滿 `SKColor` 陣列並設定 `Pixels` 屬性是比較好的方法，甚至比一些和技巧更有 `GetPixels` 説明 `SetPixels` 。 使用 `uint` 圖元值的速度通常比設定個別元件更快 `byte` ，而將 `SKColor` 值轉換成不帶正負號的整數會增加處理常式的額外負荷。

比較各種漸層也是很有趣的：每個平臺的頂端資料列都相同，並依預期顯示漸層。 這表示 `SetPixel` `Pixels` 無論基礎像素格式為何，方法和屬性都會正確地從色彩建立圖元。

IOS 和 Android 螢幕擷取畫面的後續兩個數據列也相同，確認 `MakePixel` 已針對這些平臺的預設像素格式正確定義小方法 `Rgba8888` 。

IOS 和 Android 螢幕擷取畫面的底端資料列是回溯的，表示藉由轉換值所取得的不帶正負號的整數 `SKColor` 的格式如下：

AARRGGBB

位元組的順序如下：

BB GG RR AA

這是 `Bgra8888` 順序，而不是 `Rgba8888` 排序。 此 `Brga8888` 格式為通用 Windows 平臺的預設值，這也是為什麼該螢幕擷取畫面最後一個資料列的漸層與第一個資料列相同的原因。 但中間的兩個數據列都不正確，因為建立這些點陣圖的程式碼假設有 `Rgba8888` 排序。

如果您想要使用相同的程式碼來存取每個平臺上的圖元位，您可以 `SKBitmap` 使用或格式來明確建立 `Rgba8888` `Bgra8888` 。 如果您想要將 `SKColor` 值轉換成點陣圖圖元，請使用 `Bgra8888` 。

## <a name="random-access-of-pixels"></a>隨機存取圖元

漸 `FillBitmapBytePtr` 層 `FillBitmapUintPtr` **點陣圖** 頁面中的和方法，是從 `for` 設計用來依序填滿點陣圖的迴圈（從頂端列到下一個資料列），以及從左至右的每個資料列中獲益。 您可以使用與指標遞增的相同語句來設定圖元。

有時必須隨機存取圖元，而不是依序存取。 如果您使用 `GetPixels` 方法，您必須根據資料列和資料行來計算指標。 這會在 **彩虹正弦** 頁面中示範，此頁面會以正弦曲線的一個迴圈形式來建立點陣圖，並顯示彩虹。

彩虹的色彩最容易使用 HSL (色調、飽和度、亮度) 色彩模型來建立。 `SKColor.FromHsl`方法 `SKColor` 會使用從0到360的色調值（範圍從0到）來建立值 (例如圓形的角度，但從紅色、到綠色和藍色，然後回到紅色) ，以及從0到100的飽和度和亮度值範圍。 針對彩虹的色彩，飽和度應設定為最大值100，並將亮度設定為50的中點。

**彩虹的正弦** 函數會藉由迴圈查看點陣圖的資料列來建立這個影像，然後迴圈到360的色調值。 在每個色調值中，它會計算也以正弦值為依據的點陣圖資料行：

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

這可讓程式在 `SKColor` 不需要擔心的情況下，將值轉換成 `uint` 圖元。 雖然它不會在這個特定程式中扮演任何角色，但只要您使用 `SKColor` 轉換來設定圖元，您也應該指定， `SKAlphaType.Unpremul` 因為 `SKColor` 不會依 Alpha 值 premultiply 其色彩元件。

然後，此函式會使用 `GetPixels` 方法來取得點陣圖第一個圖元的指標：

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

針對任何特定的資料列和資料行，必須將位移值加入至 `basePtr` 。 此位移是點陣圖寬度和資料行的資料列倍：

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

`SKColor`該值會使用下列指標儲存在記憶體中：

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

在的 `PaintSurface` 處理常式中 `SKCanvasView` ，會伸展點陣圖以填滿顯示區域：

[![彩虹正弦](pixel-bits-images/RainbowSine.png "彩虹正弦")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>從一個點陣圖到另一個點陣圖

很多影像處理工作牽涉到修改圖元，因為它們是從某個點陣圖傳送到另一個點陣圖。 這項技術會在 **色彩調整** 頁面中示範。 此頁面會載入其中一個點陣圖資源，然後讓您使用三個視圖來修改影像 `Slider` ：

[![色彩調整](pixel-bits-images/ColorAdjustment.png "色彩調整")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

針對每個圖元色彩，第一個會 `Slider` 將0到360的值加入到色調中，但接著會使用模數運算子來維持0和360之間的結果，以有效方式將色彩沿著色譜 (，如 UWP 螢幕擷取畫面所示) 。 第二個可 `Slider` 讓您選取介於0.5 和2之間的乘法因數以套用至飽和度，而第三個對 `Slider` 亮度也相同（如 Android 螢幕擷取畫面所示）。

程式會維護兩個位圖，也就是名為的原始來源點陣圖 `srcBitmap` 和已調整的目的點陣圖 `dstBitmap` 。 每次 `Slider` 移動時，程式都會在中計算所有新的圖元 `dstBitmap` 。 當然，使用者會藉由 `Slider` 非常快速地移動視圖來進行實驗，因此您想要能管理的最佳效能。 這包括 `GetPixels` 來源和目的地點陣圖的方法。

**色彩調整** 頁面不會控制來源和目的點陣圖的色彩格式。 相反地，它包含與格式稍有不同的邏輯 `SKColorType.Rgba8888` `SKColorType.Bgra8888` 。 來源和目的地可以是不同的格式，而且程式仍然可運作。

除了將 `TransferPixels` 圖元從來源傳送到目的地的重要方法之外，此程式也是唯一的。 的函式會設定 `dstBitmap` 為等於 `srcBitmap` 。 此 `PaintSurface` 處理常式會顯示 `dstBitmap` ：

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

整個 `TransferPixels` 方法會標示為 `unsafe` 。 它一開始會取得兩個位圖的圖元位的位元組指標，然後在所有資料列和資料行之間執行迴圈。 從來源點陣圖，方法會取得每個圖元的四個位元組。 這些可以是 `Rgba8888` 或 `Bgra8888` 順序。 檢查色彩類型是否允許 `SKColor` 建立值。 然後，會將 HSL 元件解壓縮、調整，然後用來重新建立 `SKColor` 值。 根據目的地點陣圖是 `Rgba8888` 或 `Bgra8888` ，這些位元組會儲存在目的地 bitmp 中：

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

這種方法的效能可能會更進一步改善，方法是為來源和目的地點陣圖的各種色彩類型組合建立不同的方法，並避免檢查每個圖元的型別。 另一個選項是 `for` 根據色彩類型，讓變數有多個迴圈 `col` 。

## <a name="posterization"></a>Posterization

牽涉到存取圖元位的另一個常見工作是 _posterization_ 。 如果以點陣圖圖元編碼的色彩減少，使結果類似于使用有限色彩調色板的手繪海報，則為數字。

[ **分離** 項] 頁面會在其中一個猴子映射上執行此程式：

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

函式中的程式碼會存取每個圖元、使用0xE0E0E0F光圈值執行位 AND 運算，然後將結果儲存回點陣圖。 值0xE0E0E0FF 會保留每個色彩元件的高3位，並將較低的5位設定為0。 點陣圖會縮減為 2<sup>9</sup>或512色彩，而不是 2<sup>24</sup>或16777216色彩：

[![分離](pixel-bits-images/Posterize.png "分離")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)