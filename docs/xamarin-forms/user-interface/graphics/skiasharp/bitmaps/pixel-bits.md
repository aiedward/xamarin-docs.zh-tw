---
title: 存取 SkiaSharp 點陣圖像素位元
description: 探索各種不同的技術，用於存取和修改 SkiaSharp 點陣圖的像素位元。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642529"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>存取 SkiaSharp 點陣圖像素位元

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

如您所見文中所[**檔案的儲存 SkiaSharp 點陣圖**](saving.md)，點陣圖通常儲存在壓縮的格式，例如 JPEG 或 PNG 檔案。 相較之下，在不壓縮 SkiaSharp 點陣圖儲存在記憶體中。 它會儲存為循序的一連串的像素為單位。 此未壓縮的格式讓點陣圖的傳輸到顯示介面。

SkiaSharp 點陣圖所佔用的記憶體區塊是以非常直接的方式組織的:其開頭為第一個資料列 (由左至右), 然後繼續第二個數據列。 全彩點陣圖片，每個像素包含四個位元組，這表示點陣圖所需的總記憶體空間的寬度和高度產品四次。

這篇文章說明如何應用程式能存取這些像素為單位，可以直接存取點陣圖的像素的記憶體區塊，或間接。 在某些情況下，程式可能會想要分析的映像素為單位，並建構某種形式的長條圖。 更常見的是應用程式可以藉由以建立構成點陣圖的像素建構唯一的映像：

![像素位元樣本](pixel-bits-images/PixelBitsSample.png "像素位元的範例")

## <a name="the-techniques"></a>技術

SkiaSharp 會提供數種技術來存取點陣圖的像素位元。 您選擇哪一個通常是撰寫程式碼 （這相關維護，以及輕鬆偵錯） 的便利性] 和 [效能的折衷辦法。 在大部分情況下，您將使用其中一種下列方法和屬性`SKBitmap`存取點陣圖的像素：

- `GetPixel`和`SetPixel`方法可讓您取得或設定單一像素的色彩。
- `Pixels`屬性會取得整個點陣圖的像素色彩的陣列，或設定色彩的陣列。
- `GetPixels` 傳回使用點陣圖的像素記憶體位址。
- `SetPixels` 會取代使用點陣圖的像素記憶體位址。

您可以想像為 「 高的層級 」 前兩項技術，兩個為 「 低層級 」。 有一些其他方法和屬性，您可以使用，但這些最有價值。

若要讓您查看這些技術，效能差異[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式包含名為的頁面**漸層停駐的點陣圖**，建立使用結合紅色和藍色陰影，來建立漸層的像素的點陣圖。 程式會建立八個不同複本的此點陣圖，全都必須使用不同的技術設定點陣圖像素為單位。 每個這些八個點陣圖會建立在不同的方法，也會設定此技術的簡短文字描述，並計算來設定所有的像素所需的時間。 每一種方法執行迴圈的像素設定邏輯 100 次取得的效能較佳評估。

### <a name="the-setpixel-method"></a>不要方法

如果您只需要設定或取得數個個別的像素[ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor))並[ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32))方法很適合。 針對每個這兩個方法中，您可以指定整數資料行和資料列。 不論像素格式，這兩種方法可讓您取得或設定以像素`SKColor`值：

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

`col`其中一個範圍從 0 引數，必須小於`Width`屬性的點陣圖，並`row`範圍從 0 到其中一個小於`Height`屬性。

以下是此方法中**漸層停駐的點陣圖**所設定的點陣圖，使用內容`SetPixel`方法。 點陣圖是 256 x 256 像素為單位，而`for`迴圈是硬式編碼的值範圍：

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

設定每個像素的色彩有點陣圖 欄中，紅色元件等於和藍色元件等於資料列。 點陣圖是黑色左上角，在右上角，藍色左下方，以及在右下方，使用其他位置的漸層的洋紅紅色。

`SetPixel`方法會呼叫 65,536 次，不論這個方法可能效率，它通常不是進行許多的 API 呼叫，如果可用的替代方式，是個不錯的主意。 幸運的是，有多個替代方案。

### <a name="the-pixels-property"></a>像素為單位的屬性

`SKBitmap` 定義[ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels)屬性，傳回的陣列`SKColor`整個點陣圖的值。 您也可以使用`Pixels`設定點陣圖的色彩值的陣列：

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

像素會排列在陣列的第一個資料列開始，從左到右，然後第二個資料列，等等。 陣列中的色彩的總數會等於的點陣圖寬度和高度的乘積。

雖然這個屬性會出現，能夠有效率，但請記住，像素會複製到陣列中，點陣圖從和回點陣圖，陣列和像素轉換 from 和 to`SKColor`值。

以下是此方法中`GradientBitmapPage`設定點陣圖使用的類別`Pixels`屬性。 方法會配置`SKColor`陣列所需的大小，但它也可以使用`Pixels`建立該陣列的屬性：

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

請注意，索引`pixels`陣列需要從計算`row`和`col`變數。 資料列乘以每個資料列 (256 在此情況下) 中的像素數目，然後加入資料行。

`SKBitmap` 也會定義類似`Bytes`屬性，它會傳回位元組陣列的整個點陣圖，但比較麻煩的全彩點陣圖。

### <a name="the-getpixels-pointer"></a>GetPixels 指標

要存取的點陣圖像素的最強大的技術是潛在[ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels)，而不會與混淆`GetPixel`方法或`Pixels`屬性。 您會立即注意到與差異`GetPixels`，會傳回不在 C# 程式設計中很常見的項目：

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [ `IntPtr` ](xref:System.IntPtr)類型表示的指標。 它會呼叫`IntPtr`因為它是在原生處理器機器執行程式時，通常 32 位元或 64 位元長度的整數的長度。 `IntPtr` ，`GetPixels`傳回是實際的點陣圖物件用來儲存其像素為單位的記憶體區塊的位址。

您可以將轉換`IntPtr`C# 指標類型使用[ `ToPointer` ](xref:System.IntPtr.ToPointer)方法。 C# 指標語法等同於 C 和 c + +:

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

`ptr`變數屬於類型_位元組指標_。 這`ptr`變數可讓您存取個別的記憶體位元組是用來儲存點陣圖的像素為單位。 您可以使用如下的程式碼從這個記憶體讀取一個位元組，或寫入記憶體中的位元組：

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

在此情況下，星號是 C#_間接取值運算子_並用來參考所指向的記憶體內容`ptr`。 一開始，`ptr`的第一個位元組的點陣圖，但您的第一個資料列的第一個像素的點可以在上執行算術`ptr`將它移至點陣圖內的其他位置的變數。

其中一個缺點是，您可以使用這`ptr`變數只能在程式碼區塊中的標示為`unsafe`關鍵字。 此外，組件必須標示為允許不安全的區塊。 這是專案的屬性。

使用 C# 中的指標是非常強大，但也非常危險。 您必須小心，您並不會存取超過指標應該參考的記憶體。 這就是為什麼指標使用相關聯的單字"unsafe"。

以下是此方法中`GradientBitmapPage`類別，以使用`GetPixels`方法。 請注意`unsafe`包含使用位元組指標的所有程式碼區塊：

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

當`ptr`從第一次取得變數`ToPointer`方法，它會指向最左邊的像素的點陣圖的第一個資料列的第一個位元組。 `for` For 迴圈`row`並`col`設定，讓`ptr`可以隨著`++`運算子在設定每個像素的每個位元組之後。 像素為單位，透過其他 99 迴圈`ptr`必須設定回點陣圖的開頭。

每個像素都是記憶體的四個位元組，因此必須個別設定每個位元組。 此處的程式碼假設的位元組是順序紅色、 綠色、 藍色和 alpha，這是與一致`SKColorType.Rgba8888`色彩類型。 您可能還記得這是適用於 iOS 和 Android，而不是用於通用 Windows 平台的預設色彩類型。 根據預設，UWP 建立點陣圖`SKColorType.Bgra8888`色彩類型。 基於這個理由，預期會看見該平台的一些不同的結果 ！

就可以從傳回的值轉型`ToPointer`要`uint`指標，而非`byte`指標。 這可讓一個陳述式中存取整個像素。 套用`++`該指標的運算子會將其遞增以指向下一個像素的四個位元組：

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

使用設定像素`MakePixel`方法，這個方法會建構來自紅色、 綠色、 藍色和 alpha 元件的整數像素。 請記住，`SKColorType.Rgba8888`格式具有像素位元組，順序如下：

RR GG BB AA

但是，對應到這些位元組的整數是：

AABBGGRR

根據位元組由小到大的架構先儲存整數的最小顯著性位元組。 這`MakePixel`方法將無法正常運作的點陣圖使用`Bgra8888`色彩類型。

`MakePixel`方法會標示[ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions)鼓勵以避免讓這另一個方法，而編譯的程式碼會呼叫的方法編譯器選項。 這應該會改善效能。

有趣的是，`SKColor`結構定義的明確轉換`SKColor`為不帶正負號的整數，表示`SKColor`可以建立值，並轉換為`uint`而不是用於`MakePixel`:

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

唯一的問題是:`SKColor`值的整數格式是以`SKColorType.Rgba8888` `SKColorType.Bgra8888`色彩類型或色彩類型的順序表示, 還是完全是其他東西？ 該問題的答案應該會很快顯示。

### <a name="the-setpixels-method"></a>SetPixels 方法

`SKBitmap` 也會定義名為的方法[ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr))，您呼叫像這樣：

```csharp
bitmap.SetPixels(intPtr);
```

請記得，`GetPixels`取得`IntPtr`參考用來儲存其像素點陣圖的記憶體區塊。 `SetPixels`呼叫_會取代_所參考之記憶體區塊的記憶體區塊`IntPtr`指定為`SetPixels`引數。 點陣圖再釋放它先前使用的記憶體區塊。 在下一次`GetPixels`是呼叫，它會取得與設定的記憶體區塊`SetPixels`。

一開始，它看起來好像`SetPixels`可讓您不再電源和效能比`GetPixels`同時很不方便。 使用`GetPixels`您取得點陣圖的記憶體區塊，並存取它。 使用`SetPixels`配置和存取一些記憶體，並再將它設為點陣圖的記憶體區塊。

但使用`SetPixels`提供了獨特的語法優勢:它可讓您使用陣列來存取點陣圖圖元位。 以下是中的 方法`GradientBitmapPage`示範這項技術。 方法會先定義對應到點陣圖的像素為單位的記憶體位元組的多維度的位元組陣列。 第一個維度是資料列，第二個維度的資料行和第三個維度對應，每個像素的四個元件：

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

接著，等到已像素為單位，以填入陣列後`unsafe`區塊和`fixed`陳述式來取得指向這個陣列的位元組指標。 該位元組指標然後轉換成`IntPtr`傳遞至`SetPixels`。

您所建立的陣列不一定要為位元組陣列。 它可以是整數陣列與只有兩個維度的資料列和資料行：

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

`MakePixel`方法一次用來結合 32 位元的像素色彩元件。

只為求完整性，以下是相同的程式碼，但`SKColor`值轉換為不帶正負號的整數：

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

建構函式**漸層色彩**頁面呼叫所有八個以上所顯示的方法，並將結果儲存：

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

建構函式結束時，會建立`SKCanvasView`顯示產生的點陣圖。 `PaintSurface`處理常式將表面分成八個矩形和呼叫`Display`來顯示每一項：

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

若要讓編譯器最佳化的程式碼，此頁面在執行**發行**模式。 以下是 MacBook Pro、 Nexus 5 Android 手機和執行 Windows 10 的 Surface Pro 3 iPhone 8 模擬器上執行該頁面。 由於硬體的差異，避免比較裝置之間的效能時間，但改為查看每個裝置上的相對時間：

[![漸層停駐的點陣圖](pixel-bits-images/GradientBitmap.png "漸層停駐的點陣圖")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

以下是會彙總執行時間，以毫秒為單位的資料表：

| API       | 資料類型 | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| 不要  |           | 3.17 |   10.77 | 3.49 |
| 阻擋的    |           | $0.32 |    1.23 | 0.07 |
| GetPixels | byte      | 0.09 |    $0.24 | 0.10 |
|           | uint      | $0.06 |    0.26 | 0.05 |
|           | SKColor   | 0.29 |    0.99 | 0.07 |
| SetPixels | byte      | 1.33 版 |    6.78 | 0.11 |
|           | uint      | $0.14 元 |    0.69 | $0.06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

如預期般，呼叫`SetPixel`65536 的時候，是設定點陣圖的像素為單位的最少 effeicient 方式。 填滿`SKColor`陣列和設定`Pixels`屬性是更好的而且甚至會比較有利的某些`GetPixels`和`SetPixels`技術。 使用`uint`像素值的速度通常比設定的個別`byte`元件，並將轉換`SKColor`為不帶正負號的整數值的程序會增加負擔。

比較各種漸層也很有趣:每個平臺的前幾個資料列都相同, 並依預期顯示漸層。 這表示`SetPixel`方法和`Pixels`屬性正確建立像素為單位的色彩，不論其基礎的像素格式。

IOS 和 Android 的螢幕擷取畫面的下面兩個資料列也會相同，這會確認的小`MakePixel`方法已正確定義預設`Rgba8888`這些平台的像素格式。

IOS 和 Android 的螢幕擷取畫面的底部資料列是回溯，這表示不帶正負號的整數取得轉換`SKColor`值的格式：

AARRGGBB

位元組的順序：

BB GG RR AA

這是`Bgra8888`順序而非`Rgba8888`排序。 `Brga8888`格式是針對通用 Windows 平台，這就是為什麼在該螢幕擷取畫面的最後一個資料列的漸層的第一個資料列與相同的預設值。 但中間的兩個資料列不正確，因為建立這些點陣圖的程式碼假設`Rgba8888`排序。

如果您想要使用相同的程式碼存取每個平台上的像素位元，您明確地可以建立`SKBitmap`採用`Rgba8888`或`Bgra8888`格式。 如果您想要轉型`SKColor`值的點陣圖像素為單位，以使用`Bgra8888`。

## <a name="random-access-of-pixels"></a>像素的隨機存取

`FillBitmapBytePtr`並`FillBitmapUintPtr`中的方法**漸層停駐的點陣圖**受惠於頁面`for`迴圈設計成循序填滿點陣圖，從頂端列下方的列，並以從左到右的每個資料列。 遞增指標的相同陳述式，可以設定像素。

有時候很需要隨機而不是以循序方式存取像素為單位。 如果您使用`GetPixels`方法時，您必須計算的資料列和資料行的指標。 這示範於**彩虹正弦**網頁，當中會建立一個循環的正弦曲線形式顯示彩虹的點陣圖。

彩虹的色彩是最簡單的方式使用 （色調、 飽和度、 亮度） HSL 色彩模型所建立的。 `SKColor.FromHsl`方法會建立`SKColor`使用範圍從 0 到 360 （例如的角度圓形，但從紅色、 綠色和藍色，並回到 red） 的色相值的值，範圍從 0 到 100 的飽和度和亮度值。 彩虹的色彩，則應該設定為最多 100 和以點為 50 的明暗度的飽和度。

**Rainbow 正弦**此映像建立的點陣圖，資料列執行迴圈，然後透過 360 的色調值。 從每個的色相值，它會計算的點陣圖資料行，也根據正弦函數值：

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

請注意，建構函式會建立以點陣圖`SKColorType.Bgra8888`格式：

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

這可讓程式能夠使用的轉換`SKColor`值`uint`像素為單位，而不必擔心。 雖然它不在這個特定的程式扮演的角色，每當您使用`SKColor`轉換成設定像素為單位，您也應該指定`SKAlphaType.Unpremul`因為`SKColor`不預其色彩元件乘的 alpha 值。

建構函式接著會使用`GetPixels`方法，以取得變數的指標，第一個像素的點陣圖：

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

針對任何特定資料列和資料行中，位移的值必須新增至`basePtr`。 這個位移會乘以點陣圖寬度，加上資料行的資料列：

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

`SKColor`值會儲存在使用這個指標的記憶體：

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

在 `PaintSurface`處理常式`SKCanvasView`，點陣圖會縮放以填滿顯示區域：

[![Rainbow 正弦](pixel-bits-images/RainbowSine.png "彩虹正弦值")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>從到另一個點陣圖

很多的映像處理工作都涉及修改一個點陣圖從傳送到另一個像素為單位。 這項技術所示**色彩調整**頁面。 頁面載入其中一個點陣圖資源，並允許您修改映像使用三個`Slider`檢視：

[![色彩調整](pixel-bits-images/ColorAdjustment.png "色彩調整")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

每個像素的色彩，第一個`Slider`0 的值將 360 色調，但接著會使用模數運算子来保留的結果，介於 0 和 360 之間，有效地轉移以及頻譜色彩 （如 UWP 螢幕擷取畫面所示）。 第二個`Slider`可讓您選取介於 0.5 至 2 套用至飽和度和第三個乘法因數`Slider`對執行相同作業亮度，Android 的螢幕擷取畫面所示。

程式會維護兩個點陣圖，名為原始的來源點陣圖`srcBitmap`和名為已調整的目的地點陣圖`dstBitmap`。 每次`Slider`移動時，程式會計算在所有新的像素`dstBitmap`。 當然，使用者會利用移動來試驗`Slider`非常快速地檢視，因此您可以管理的最佳效能。 這牽涉到`GetPixels`方法在來源和目的地點陣圖。

**色彩調整**頁面未控制的來源和目的地點陣圖的色彩格式。 相反地，其中包含稍有不同的邏輯，如`SKColorType.Rgba8888`和`SKColorType.Bgra8888`格式。 來源和目的地可以是不同的格式，以及程式仍然可以運作。

以下是除了重要程式`TransferPixels`傳輸的像素為單位的方法會形成來源到目的地。 建構函式集合`dstBitmap`等於`srcBitmap`。 `PaintSurface`處理常式顯示`dstBitmap`:

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

`ValueChanged`處理常式`Slider`檢視計算調整值，並呼叫`TransferPixels`。

將整個`TransferPixels`方法會標示為`unsafe`。 它藉由取得兩個點陣圖的像素位元的位元組指標開始，然後逐一執行所有的資料列和資料行。 從來源點陣圖，方法會取得每個像素的四個位元組。 這些可能是在`Rgba8888`或`Bgra8888`順序。 檢查色彩類型可讓`SKColor`要建立的值。 HSL 元件則是擷取、 調整，而且用來重新建立`SKColor`值。 根據目的地點陣圖是否`Rgba8888`或`Bgra8888`，位元組會儲存在目的地 bitmp:

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

很可能無法藉由建立不同的方法的來源和目的地點陣圖的色彩類型的各種組合來更進一步改善這個方法的效能，並避免檢查每個像素的類型。 另一個選項是有多個`for`迴圈的`col`變數根據色彩類型。

## <a name="posterization"></a>色調分離

另一個常見的作業涉及存取像素位元正在_色調分離_。 如果色彩編碼點陣圖的像素為單位的數目會減少，使結果類似使用有限的色彩調色盤的手繪海報。

**Posterize**網頁上其中一個 monkey 映像執行此程序：

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

建構函式中的程式碼存取每個像素執行 0xE0E0E0FF，值的位元 AND 運算，然後將結果儲存回在點陣圖中。 0xE0E0E0FF 的值會保留 3 個高位元的每個色彩元件，並將較低的 5 位元設為 0。 而不是 2<sup>24</sup>或 16777216 色彩點陣圖會減少到 2<sup>9</sup>或 512 色彩：

[![Posterize](pixel-bits-images/Posterize.png "Posterize")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
