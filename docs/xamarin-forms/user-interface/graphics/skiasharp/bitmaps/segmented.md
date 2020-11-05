---
title: 分割的 SkiaSharp 點陣圖顯示
description: 顯示 SkiaSharp 點陣圖，讓某些區域伸展，某些區域則不會。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fe63f3fdab5d508ab0202fbfe93bdc223f97d28a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370165"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>分割的 SkiaSharp 點陣圖顯示

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp `SKCanvas` 物件會定義名為 `DrawBitmapNinePatch` 的方法，以及兩個名稱 `DrawBitmapLattice` 相同的方法。 這兩種方法都會將點陣圖轉譯為目的地矩形的大小，但不是一致地縮放點陣圖，而是在其圖元維度中顯示點陣圖的部分，並延展點陣圖的其他部分，使其符合矩形：

![分段範例](segmented-images/SegmentedSample.png "分段範例")

這些方法通常用於轉譯組成使用者介面物件（例如按鈕）一部分的點陣圖。 當您設計按鈕時，通常會希望按鈕的大小是根據按鈕的內容，但您可能想要讓按鈕的框線具有相同的寬度（不論按鈕的內容為何）。 這是的理想應用程式 `DrawBitmapNinePatch` 。

`DrawBitmapNinePatch` 是特殊案例， `DrawBitmapLattice` 但這是使用和瞭解這兩種方法的更簡單方法。

## <a name="the-nine-patch-display"></a>九個修補程式顯示 

從概念上來說， [`DrawBitmapNinePatch`](xref:SkiaSharp.SKCanvas.DrawBitmapNinePatch(SkiaSharp.SKBitmap,SkiaSharp.SKRectI,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 將點陣圖分割成九個矩形：

![九個修補程式](segmented-images/NinePatch.png "九個修補程式")

四個角落的矩形會以圖元大小顯示。 當箭號表示時，點陣圖邊緣上的其他區域會水準或垂直延展至目的矩形的區域。 中心內的矩形會以水準和垂直方式伸展。 

如果目的矩形沒有足夠的空間可顯示其圖元尺寸內的四個角落，則會相應減少為可用的大小，而不會顯示四個角落。

若要將點陣圖分割成這九個矩形，您只需要在中央指定矩形。 這是方法的語法 `DrawBitmapNinePatch` ：

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

中心矩形相對於點陣圖。 它是 `SKRectI` () 整數版本的值 `SKRect` ，而且所有座標和大小都是以圖元為單位。 目的地矩形相對於顯示介面。 `paint` 引數是選擇性的。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中的 **九個 Patch 顯示** 頁面會先使用靜態的函式來建立類型的公用靜態屬性 `SKBitmap` ：

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

本文中的其他兩頁使用相同的點陣圖。 點陣圖是500圖元正方形，包含25個圓形的陣列、所有相同的大小，每個都佔用100圖元的正方形區域：

![圓形方格](segmented-images/CircleGrid.png "圓形方格")

程式的實例函式會建立 `SKCanvasView` 具有 `PaintSurface` 處理常式的，此處理程式會使用 `DrawBitmapNinePatch` 來顯示延伸至整個顯示介面的點陣圖：

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

`centerRect`矩形包含16個圓形的中央陣列。 角落中的圓形會以圖元尺寸顯示，其他所有專案則會據以伸展：

[![九-修補程式顯示](segmented-images/NinePatchDisplay.png "Nine-Patch 顯示")](segmented-images/NinePatchDisplay-Large.png#lightbox)

UWP 頁面的寬度會是500圖元，因此會以相同大小的一連串圓形來顯示頂端和底部資料列。 否則，所有不在角落的圓形都會延伸成形成省略號。

若要以奇怪的方式顯示包含圓圈和省略號組合的物件，請試著定義中間矩形，使其與圓形的資料列和資料行重迭：

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>>lattice 顯示

這兩種 `DrawBitmapLattice` 方法類似于 `DrawBitmapNinePatch` ，但會針對任何數目的水準或垂直部門進行一般化。 這些分割是由對應至圖元的整數陣列所定義。 

[`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,System.Int32[],System.Int32[],SkiaSharp.SKRect,SkiaSharp.SKPaint))具有這些整數陣列之參數的方法似乎沒有作用。 [`DrawBitmapLattice`](xref:SkiaSharp.SKCanvas.DrawBitmapLattice(SkiaSharp.SKBitmap,SkiaSharp.SKLattice,SkiaSharp.SKRect,SkiaSharp.SKPaint))具有型別參數的方法 `SKLattice` 會運作，而這就是範例中所使用的方法。

[`SKLattice`](xref:SkiaSharp.SKLattice)結構會定義四個屬性：

- [`XDivs`](xref:SkiaSharp.SKLattice.XDivs)，整數陣列
- [`YDivs`](xref:SkiaSharp.SKLattice.YDivs)，整數陣列
- [`Flags`](xref:SkiaSharp.SKLattice.Flags)，列舉型別的陣列。 `SKLatticeFlags`
- [`Bounds`](xref:SkiaSharp.SKLattice.Bounds) 類型的 `Nullable<SKRectI>` ，用來指定點陣圖內的選擇性來源矩形

陣列會將 `XDivs` 點陣圖的寬度分割成分隔號紋。 第一個帶狀會從左邊的圖元0延伸至 `XDivs[0]` 。 此帶狀線會以圖元寬度呈現。 第二個帶狀會從延伸 `XDivs[0]` 至 `XDivs[1]` ，而且會伸展。 第三個帶狀會從延伸 `XDivs[1]` 至 `XDivs[2]` ，並且以圖元寬度呈現。 最後一個帶狀會從陣列的最後一個元素擴充到點陣圖的右邊緣。 如果陣列有偶數的元素，則會以圖元寬度顯示。 否則，它會伸展。 分隔號的總數比陣列中的元素數目還要多。

`YDivs`陣列很類似。 它會將陣列的高度分成水準條紋。

`XDivs`和陣列一起將 `YDivs` 點陣圖分成矩形。 矩形的數目等於水準條紋數目和垂直停車數的乘積。

根據 Skia 檔，陣列中 `Flags` 包含每個矩形的一個元素、第一個矩形的頂端列、第二個數據列等等。 `Flags`陣列的類型是 [`SKLatticeFlags`](xref:SkiaSharp.SKLatticeFlags) ，具有下列成員的列舉：

- `Default` 值為0的
- `Transparent` 值1

不過，這些旗標看起來似乎不像預期的一樣，最好是忽略這些旗標。 但不要將 `Flags` 屬性設定為 `null` 。 將它設定為 `SKLatticeFlags` 夠大的值陣列，以包含矩形總數。

[ **>lattice 9 Patch** ] 頁面會使用 `DrawBitmapLattice` 模仿 `DrawBitmapNinePatch` 。 它會使用在中建立的相同點陣圖 `NinePatchDisplayPage` ：

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

`XDivs`和 `YDivs` 屬性都設定為只有兩個整數的陣列，以水準和垂直方式將點陣圖分割成三個圖 (100：圖元大小) 、圖元100到圖元 400 (延展) ，以及從圖元400到圖元 500 (圖元大小) 。 同時 `XDivs` `YDivs` 定義總計9個矩形，也就是陣列的大小 `Flags` 。 只要建立陣列就足以建立值的陣列 `SKLatticeFlags.Default` 。

顯示與上一個程式相同：

[![>lattice 9-修補程式](segmented-images/LatticeNinePatch.png ">lattice Nine-Patch")](segmented-images/LatticeNinePatch-Large.png#lightbox)

**>lattice 顯示** 頁面會將點陣圖分成16個矩形：

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

`XDivs`和 `YDivs` 陣列稍有不同，導致顯示器與先前的範例不會有相當的對稱：

[![>lattice 顯示](segmented-images/LatticeDisplay.png ">lattice 顯示")](segmented-images/LatticeDisplay-Large.png#lightbox)

在左側的 iOS 和 Android 影像中，只會以圖元大小轉譯較小的圓形。 所有其他專案都已伸展。

**>lattice 顯示** 頁面一般化了陣列的建立 `Flags` ，可讓您 `XDivs` `YDivs` 更輕鬆地進行實驗。 尤其是，您會想要查看當您將或陣列的第一個元素設定為0時，會發生什麼事 `XDivs` `YDivs` 。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)