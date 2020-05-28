---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5c3909271580d0568d7c603de0d434ff5b3f3bc4
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138667"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>SkiaSharp 點陣圖的分段顯示

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp `SKCanvas` 物件會定義名為 `DrawBitmapNinePatch` 的方法，以及兩個名為且 `DrawBitmapLattice` 非常類似的方法。 這兩種方法都會將點陣圖轉譯成目的地矩形的大小，但不會一致地延展點陣圖，而是在其圖元維度中顯示點陣圖的某些部分，並延展點陣圖的其他部分，使其符合矩形：

![分段樣本](segmented-images/SegmentedSample.png "分段範例")

這些方法通常用於呈現組成使用者介面物件（例如按鈕）一部分的點陣圖。 設計按鈕時，通常您會希望按鈕的大小是根據按鈕的內容，但不論按鈕的內容為何，您可能會希望按鈕的框線具有相同的寬度。 這是的理想應用程式 `DrawBitmapNinePatch` 。

`DrawBitmapNinePatch`是特殊的案例， `DrawBitmapLattice` 但這是使用和瞭解這兩種方法的較簡單方式。

## <a name="the-nine-patch-display"></a>9修補程式顯示 

在概念上，會 [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 將點陣圖分割成九個矩形：

![九個修補程式](segmented-images/NinePatch.png "九個修補程式")

四個角落的矩形會以其圖元大小顯示。 如箭號所示，點陣圖邊緣上的其他區域會水準或垂直延展到目的矩形的區域。 中心的矩形會以水準和垂直方式伸展。 

如果目的矩形中沒有足夠的空間可顯示其圖元尺寸中的四個角落，則會將它們縮小為可用的大小，但不會顯示任何內容，但會顯示四個圓角。

若要將點陣圖分割成這九個矩形，只需要指定置中的矩形。 這是方法的語法 `DrawBitmapNinePatch` ：

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

中間矩形是相對於點陣圖。 它是 `SKRectI` 值（的整數版本 `SKRect` ），而所有座標和大小都是以圖元為單位。 目的矩形相對於顯示介面。 `paint` 引數是選擇性的。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的九個 [**修補程式顯示**] 頁面會先使用靜態的函式來建立類型的公用靜態屬性 `SKBitmap` ：

```csharp
public partial class NinePatchDisplayPage : ContentPage
{
    static NinePatchDisplayPage()
    {
        using (SKCanvas canvas = new SKCanvas(FiveByFiveBitmap))
        using (SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 10
        })
        {
            for (int x = 50; x < 500; x += 100)
                for (int y = 50; y < 500; y += 100)
                {
                    canvas.DrawCircle(x, y, 40, paint);
                }
        }
    }

    public static SKBitmap FiveByFiveBitmap { get; } = new SKBitmap(500, 500);
    ···
}
```

本文中的兩個其他頁面使用相同的點陣圖。 點陣圖是500圖元的正方形，由25個圓形的陣列組成，所有大小都相同，每個都佔用100圖元的正方形區域：

![圓形方格](segmented-images/CircleGrid.png "圓形方格")

程式的實例的「處理常式」 `SKCanvasView` 會建立，其中包含用 `PaintSurface` 來將 `DrawBitmapNinePatch` 點陣圖延伸至其整個顯示介面的處理常式：

```csharp
public class NinePatchDisplayPage : ContentPage
{
    ···
    public NinePatchDisplayPage()
    {
        Title = "Nine-Patch Display";

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

        SKRectI centerRect = new SKRectI(100, 100, 400, 400);
        canvas.DrawBitmapNinePatch(FiveByFiveBitmap, centerRect, info.Rect);
    }
}
```

`centerRect`矩形包含16個圓形的中央陣列。 角落中的圓形會顯示在其圖元尺寸中，而其他專案則會據以延展：

[![9-修補程式顯示](segmented-images/NinePatchDisplay.png "9-修補程式顯示")](segmented-images/NinePatchDisplay-Large.png#lightbox)

UWP 頁面的寬度會是500圖元，因此會將頂端和底端的資料列顯示成一系列大小相同的圓形。 否則，不在角落的所有圓形都會延展成形成橢圓形。

對於包含圓形和省略號組合的物件奇怪顯示，請嘗試定義中間矩形，使其與圓形的資料列和資料行重迭：

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>Lattice 顯示

這兩種 `DrawBitmapLattice` 方法類似 `DrawBitmapNinePatch` ，但它們會針對任何數目的水準或垂直區間進行一般化。 這些部門是由對應至圖元的整數陣列所定義。 

[`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint))具有這些整數陣列之參數的方法似乎不會運作。 [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint))具有型別參數的方法 `SKLattice` 可以運作，而這就是範例中所使用的方法。

[`SKLattice`](xref:SkiaSharp.SKLattice)結構會定義四個屬性：

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs)，整數陣列
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs)，整數陣列
- [`Flags`](xref:SkiaSharp.SKLattice.Flags)，是列舉類型的陣列。 `SKLatticeFlags`
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds)類型的 `Nullable<SKRectI>` ，用來指定點陣圖內的選擇性來源矩形

陣列會將 `XDivs` 點陣圖的寬度分割成垂直的去除。 第一個帶狀會從左邊的圖元0延伸到 `XDivs[0]` 。 此帶狀會以其圖元寬度轉譯。 第二個帶狀會將從延伸 `XDivs[0]` 至 `XDivs[1]` ，並且會伸展。 第三個帶狀會從延伸 `XDivs[1]` 至 `XDivs[2]` ，並以其圖元寬度轉譯。 最後一個帶狀會從陣列的最後一個專案延伸到點陣圖的右邊緣。 如果陣列有偶數的元素，則會以其圖元寬度顯示。 否則，它會伸展。 垂直塊的總數大於陣列中的元素數目。

`YDivs`陣列很類似。 它會將陣列的高度分割成水準去除。

和陣列結合在一起，會 `XDivs` `YDivs` 將點陣圖分割成矩形。 矩形的數目等於水準去除數的乘積和垂直線的數目。

根據 Skia 檔，陣列會 `Flags` 針對每個矩形包含一個元素，第一個是矩形的頂端資料列，另一個是第二個數據列，依此類推。 `Flags`陣列的類型是 [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) ，具有下列成員的列舉：

- `Default`值為0的
- `Transparent`值為1的

不過，這些旗標似乎不會如預期執行，因此最好忽略它們。 但不要將 `Flags` 屬性設定為 `null` 。 將它設定為 `SKLatticeFlags` 夠大的值陣列，以包含矩形總數。

**Lattice 9 修補程式**頁面會使用模擬 `DrawBitmapLattice` `DrawBitmapNinePatch` 。 它會使用在中建立的相同點陣圖 `NinePatchDisplayPage` ：

```csharp
public class LatticeNinePatchPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeNinePatchPage ()
    {
        Title = "Lattice Nine-Patch";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    `
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 400 };
        lattice.YDivs = new int[] { 100, 400 };
        lattice.Flags = new SKLatticeFlags[9]; 

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

`XDivs`和 `YDivs` 屬性都只會設定為兩個整數的陣列，並以水準和垂直方式將點陣圖分割成三個停車線：從圖元0到圖元100（以圖元大小呈現）、從圖元100到圖元400（延展），以及從圖元400到圖元500（圖元大小）。 同時 `XDivs` `YDivs` 定義總共9個矩形，這是陣列的大小 `Flags` 。 只要建立陣列就足以建立 `SKLatticeFlags.Default` 值陣列。

顯示與上一個程式相同：

[![Lattice 9-修補程式](segmented-images/LatticeNinePatch.png "Lattice 9-修補程式")](segmented-images/LatticeNinePatch-Large.png#lightbox)

**Lattice 顯示**頁面會將點陣圖分割成16個矩形：

```csharp
public class LatticeDisplayPage : ContentPage
{
    SKBitmap bitmap = NinePatchDisplayPage.FiveByFiveBitmap;

    public LatticeDisplayPage()
    {
        Title = "Lattice Display";

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

        SKLattice lattice = new SKLattice();
        lattice.XDivs = new int[] { 100, 200, 400 };
        lattice.YDivs = new int[] { 100, 300, 400 };

        int count = (lattice.XDivs.Length + 1) * (lattice.YDivs.Length + 1);
        lattice.Flags = new SKLatticeFlags[count];

        canvas.DrawBitmapLattice(bitmap, lattice, info.Rect);
    }
}
```

`XDivs`和 `YDivs` 陣列有點不同，導致顯示的效果不會與先前的範例完全相同：

[![Lattice 顯示](segmented-images/LatticeDisplay.png "Lattice 顯示")](segmented-images/LatticeDisplay-Large.png#lightbox)

在左側的 iOS 和 Android 映射中，只有較小的圓形會以其圖元大小呈現。 所有其他專案則會伸展。

**Lattice 顯示**頁面會一般化陣列的建立 `Flags` ，讓您能夠 `XDivs` `YDivs` 更輕鬆地進行實驗。 特別是，您會想要查看當您將或陣列的第一個元素設定為0時，會發生什麼事 `XDivs` `YDivs` 。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
