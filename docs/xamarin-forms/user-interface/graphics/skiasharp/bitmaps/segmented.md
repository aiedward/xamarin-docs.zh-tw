---
title: 分段的顯示的 SkiaSharp 點陣圖
description: 如此會延展，某些區域，而且某些區域中不會顯示 SkiaSharp 點陣圖。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79AE2033-C41C-4447-95A6-76D22E913D19
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: e5bfa076a8746abd6275e9d7a8393c7c8ab53294
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615232"
---
# <a name="segmented-display-of-skiasharp-bitmaps"></a>分段的顯示的 SkiaSharp 點陣圖

SkiaSharp`SKCanvas`物件會定義一個名為方法`DrawBitmapNinePatch`和名為兩個方法`DrawBitmapLattice`非常類似。 這些方法會呈現目的地矩形的大小的點陣圖同時而不是一致的方式自動縮放點陣圖，顯示部分點陣圖的像素尺寸的人員，並自動縮放點陣圖的其他部分，使其符合矩形：

![分割的範例](segmented-images/SegmentedSample.png "分割範例")

這些方法通常用於轉譯點陣圖組成使用者介面物件，例如按鈕。 在設計按鈕時，通常您想要依據內容的按鈕，按鈕的大小，但您可能想要不論按鈕的內容相同的寬度的按鈕的框線。 這是理想的應用程式的`DrawBitmapNinePatch`。

`DrawBitmapNinePatch` 是一種特殊情況`DrawBitmapLattice`但很容易使用，並了解兩種方法。

## <a name="the-nine-patch-display"></a>修補程式的、 包含九部顯示 

就概念而言， [ `DrawBitmapNinePatch` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmapNinePatch/p/SkiaSharp.SKBitmap/SkiaSharp.SKRectI/SkiaSharp.SKRect/SkiaSharp.SKPaint/)分成九個矩形的點陣圖：

![九個修補](segmented-images/NinePatch.png "九個修補程式")

矩形四角會顯示在其像素大小。 因為箭號表示，點陣圖的邊緣上的其他區域會縮放水平或垂直，以目的地矩形的面積。 水平和垂直縮放中心點的矩形。 

如果目的矩形，以顯示其像素尺寸甚至四個角落中沒有足夠的空間，然後可用的大小，並執行任何動作相應減少，但會顯示四角。

若要將點陣圖分成下列九個矩形，才需要指定矩形的中心。 這是語法`DrawBitmapNinePatch`方法：

```csharp
canvas.DrawBitmapNinePatch(bitmap, centerRectangle, destRectangle, paint);
```

在中央矩形是相對於點陣圖。 很`SKRectI`值 (整數版本`SKRect`) 以及所有座標和大小都是以像素為單位。 目的矩形是相對於在顯示介面。 `paint` 引數是選擇性的。

**九個修補程式顯示器**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例會先使用靜態建構函式來建立類型的公用靜態屬性`SKBitmap`:

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

這篇文章中的其他兩個頁面會使用該相同的點陣圖。 點陣圖為 500 像素的正方形，並由 25 的圓形，陣列的所有相同大小，每個佔用 100 像素的方形區域：

![圓圈，方格](segmented-images/CircleGrid.png "Circle 方格")

程式的執行個體建構函式建立`SKCanvasView`具有`PaintSurface`使用的處理常式`DrawBitmapNinePatch`顯示延展到整個顯示表面的點陣圖：

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

`centerRect`矩形包含 16 圓形的中央的陣列。 邊角圓圈會顯示在他們的像素尺寸，和其他項目會據以延伸：

[![修補程式的、 包含九部顯示器](segmented-images/NinePatchDisplay.png "九個修補程式顯示")](segmented-images/NinePatchDisplay-Large.png#lightbox)

UWP 頁面剛好是 500 像素寬，並因此會顯示為一系列的相同大小的圓形的頂端和底部資料列。 否則，不在邊角中的所有圓形會都縮放，以表單的省略符號。

圓角和橢圓形的組合所組成的物件以奇怪的顯示，請嘗試定義，使它和重疊的資料列和資料行的圓形的中心矩形：

```csharp
SKRectI centerRect = new SKRectI(150, 150, 350, 350);
```

## <a name="the-lattice-display"></a>斜格紋顯示

這兩個`DrawBitmapLattice`方法很類似`DrawBitmapNinePatch`，但它們已經被一般化的任意數目的水平或垂直分割。 這些部門會定義對應到像素為單位的整數的陣列。 

[ `DrawBitmapLattice` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmapLattice/p/SkiaSharp.SKBitmap/System.Int32[]/System.Int32[]/SkiaSharp.SKRect/SkiaSharp.SKPaint/)方法參數的整數的這些陣列似乎無法運作。 [ `DrawBitmapLattice` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmapLattice/p/SkiaSharp.SKBitmap/SkiaSharp.SKLattice/SkiaSharp.SKRect/SkiaSharp.SKPaint/)具有類型參數的方法`SKLattice`運作，且可在如下所示的範例中使用。

[ `SKLattice` ](https://developer.xamarin.com/api/type/SkiaSharp.SKLattice/)結構會定義四個屬性：

- [`XDivs`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.XDivs/)整數的陣列
- [`YDivs`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.YDivs/)整數的陣列
- [`Flags`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.Flags/)陣列`SKLatticeFlags`，列舉型別
- [`Bounds`](https://developer.xamarin.com/api/property/SkiaSharp.SKLattice.Bounds/) 型別的`Nullable<SKRectI>`指定點陣圖內的選擇性來源矩形

`XDivs`陣列分成垂直帶狀線的點陣圖的寬度。 從要的像素 0 左邊的第一個區域擴充`XDivs[0]`。 這個區域會呈現其像素寬度。 第二個帶狀始`XDivs[0]`至`XDivs[1]`，和自動縮放。 第三個區域始`XDivs[1]`至`XDivs[2]`並呈現其像素寬度。 點陣圖的右邊緣，從陣列的最後一個項目延伸的最後一個區域。 如果陣列中有偶數數目的項目，它會顯示在其像素寬度。 否則，它會自動縮放。 垂直帶狀線的總數是其中的陣列中的項目數目範圍內。

`YDivs`陣列很類似。 它會將陣列的高度分割成水平帶狀線中。

共同`XDivs`和`YDivs`陣列分割成矩形，矩形的點陣圖。 矩形的數目等於水平帶狀線的數字和垂直帶狀線的數字的乘積。

根據 Skia 文件，`Flags`陣列包含一個項目針對每個矩形中，第一次頂端列的矩形，然後第二個資料列，等等。 `Flags`陣列屬於類型[ `SKLatticeFlags` ](https://developer.xamarin.com/api/type/SkiaSharp.SKLatticeFlags/)，列舉型別具有下列成員：

- `Default` 值為 0 的
- `Transparent` 具有值 1

不過，這些旗標似乎做運作如預期，而且最好是略過它們。 未設定，但`Flags`屬性設`null`。 將它設定為陣列`SKLatticeFlags`值大到足以包含矩形的總數。

**Lattice 九個修補**頁面上會使用`DrawBitmapLattice`模仿`DrawBitmapNinePatch`。 它會使用在中建立的相同點陣圖`NinePatchDisplayPage`:

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

同時`XDivs`和`YDivs`屬性會設定為將分成三個帶狀線的點陣圖，以水平和垂直的只有兩個整數的陣列： 從像素 0 像素 100 （已轉譯的像素大小），從像素 100 到 400 的像素 （延伸） 和從像素 400 像素 500 （像素大小）。 共同`XDivs`並`YDivs`定義共 9 的矩形，這是大小的`Flags`陣列。 只要建立陣列便可建立陣列`SKLatticeFlags.Default`值。

顯示等同於先前的程式：

[![斜格紋、 包含九部修補](segmented-images/LatticeNinePatch.png "斜格紋、 包含九部修補程式")](segmented-images/LatticeNinePatch-Large.png#lightbox)

**Lattice 顯示**頁面分成 16 的矩形中的點陣圖：

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

`XDivs`和`YDivs`陣列是有點不同，導致無法相當為對稱式與先前範例顯示：

[![斜格紋顯示器](segmented-images/LatticeDisplay.png "斜格紋顯示")](segmented-images/LatticeDisplay-Large.png#lightbox)

在 iOS 和 Android 的映像，在左側，較小的圓圈會呈現其像素大小。 所有其他項目會自動縮放。

**Lattice 顯示**頁面會一般化建立`Flags`陣列，可讓您試驗`XDivs`和`YDivs`更容易。 特別是，您會想要查看當您設定的第一個項目時，會發生什麼事`XDivs`或`YDivs`設為 0 的陣列。 

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
