---
title: 建立和上 SkiaSharp 點陣圖繪製
description: 了解如何建立 SkiaSharp 點陣圖，然後藉由建立根據它們在畫布上繪製這些點陣圖上。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 9e76558597928bafa25f6a217c6f0055657e4c22
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053074"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>建立和上 SkiaSharp 點陣圖繪製

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

您已了解如何應用程式可以載入點陣圖從網站、 應用程式資源和使用者的相片媒體櫃。 它也可建立新應用程式內的點陣圖。 最簡單的方法牽涉到其中的建構函式[ `SKBitmap` ](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)):

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

`width`和`height`參數都是整數，並指定點陣圖的像素尺寸。 這個建構函式會建立全彩點陣圖使用每像素的四個位元組： 一個位元組每個的紅色、 綠色、 藍色和 alpha (opacity) 元件。 

建立新的點陣圖之後，您需要取得點陣圖的介面上的項目。 您經常進行這兩種方式之一：

- 繪製點陣圖使用標準`Canvas`繪製方法。
- 直接存取像素位元。

這篇文章會示範第一種方法：

![繪製範例](drawing-images/DrawingSample.png "繪製範例")

第二種方法文章中討論[**存取 SkiaSharp 點陣圖像素**](pixel-bits.md)。

## <a name="drawing-on-the-bitmap"></a>點陣圖的繪圖

點陣圖的介面上繪製等同於在視訊顯示器上繪圖。 若要繪製視訊的顯示畫面上，您取得`SKCanvas`物件從`PaintSurface`事件引數。 若要繪製點陣圖上，建立`SKCanvas`物件使用[ `SKCanvas` ](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap))建構函式：

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

當您完成在點陣圖繪製時，您可以處置`SKCanvas`物件。 基於這個理由，`SKCanvas`建構函式通常稱為`using`陳述式：

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

然後可以顯示點陣圖。 稍後，程式可以建立新`SKCanvas`上相同的點陣圖，，和在其上繪製多型的物件。

**Hello 點陣圖**頁面 **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式寫入的文字"Hello，點陣圖"！ 在點陣圖，則將顯示的點陣圖多次。  

建構函式`HelloBitmapPage`一開始先建立`SKPaint`顯示文字的物件。 它會判斷文字字串的維度，並會使用這些維度建立的點陣圖。 然後它會建立`SKCanvas`物件會根據該點陣圖，呼叫`Clear`，然後呼叫`DrawText`。 它一律是個不錯的主意，呼叫`Clear`與新的點陣圖因為新建立的點陣圖可能包含隨機資料。

建構函式結束時，會建立`SKCanvasView`物件，以顯示點陣圖：

```csharp
public partial class HelloBitmapPage : ContentPage
{
    const string TEXT = "Hello, Bitmap!";
    SKBitmap helloBitmap;

    public HelloBitmapPage()
    {
        Title = TEXT;

        // Create bitmap and draw on it
        using (SKPaint textPaint = new SKPaint { TextSize = 48 })
        {
            SKRect bounds = new SKRect();
            textPaint.MeasureText(TEXT, ref bounds);

            helloBitmap = new SKBitmap((int)bounds.Right,
                                       (int)bounds.Height);

            using (SKCanvas bitmapCanvas = new SKCanvas(helloBitmap))
            {
                bitmapCanvas.Clear();
                bitmapCanvas.DrawText(TEXT, 0, -bounds.Top, textPaint);
            }
        }

        // Create SKCanvasView to view result 
        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Aqua);

        for (float y = 0; y < info.Height; y += helloBitmap.Height)
            for (float x = 0; x < info.Width; x += helloBitmap.Width)
            {
                canvas.DrawBitmap(helloBitmap, x, y);
            }
    }
}
```

`PaintSurface`處理常式可以呈現多個時間的資料列和資料行中顯示的點陣圖。 請注意，`Clear`方法中的`PaintSurface`處理常式有引數的`SKColors.Aqua`，其色彩的顯示表面背景：

[![Hello，點陣圖 ！](drawing-images/HelloBitmap.png "Hello，點陣圖 ！")](drawing-images/HelloBitmap-Large.png#lightbox)

青色背景的外觀顯示點陣圖是透明的文字除外。

## <a name="clearing-and-transparency"></a>清除和透明度

顯示**Hello 點陣圖**頁面示範點陣圖建立的程式會是透明的黑色文字除外。 這就是為什麼透過青色顯示表面的色彩顯示。

文件`Clear`方法的`SKCanvas`描述這些陳述式:"會取代在畫布的目前短片中的所有像素。 」 使用文字 「 取代 」 會顯示這些方法的重要特性： 所有的繪圖方法`SKCanvas`將項目加入至現有的顯示介面。 `Clear`方法_取代_項目已經存在。

`Clear` 有兩種不同的版本： 

- [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor))方法`SKColor`參數會取代該顏色的像素中的顯示表面的像素為單位。

- [ `Clear` ](xref:SkiaSharp.SKCanvas.Clear)不含任何參數的方法會取代以像素[ `SKColors.Empty` ](xref:SkiaSharp.SKColors.Empty)色彩，也就是其中的所有元件 （紅色、 綠色、 藍色和 alpha） 會設定為零的色彩。 這個色彩有時稱為 「 透明的黑色。 」

呼叫`Clear`上新的點陣圖不含引數初始化整個點陣圖是完全透明。 後續繪製點陣圖上的任何項目通常會是不透明或部分的不透明。

以下是要嘗試的項目： 在**Hello 點陣圖**頁面上，取代`Clear`方法套用至`bitmapCanvas`以此：

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

順序`SKColor`建構函式參數是紅色、 綠色、 藍色和 alpha，其中每個值的範圍可以從 0 到 255 之間。 請記住，alpha 值為 0 為透明，而 255 的 alpha 值是不透明。

值 （255，0，0，128） 會清除為紅色與 50%不透明的像素的點陣圖像素。 這表示點陣圖的背景是半透明效果。 半透明的紅色背景的點陣圖結合青色建立灰色背景顯示表面的背景。

嘗試設定文字的色彩為透明的黑色，將下列指派放入`SKPaint`初始設定式：

```csharp
Color = new SKColor(0, 0, 0, 0)
```

您可能會認為此透明的文字會建立完全透明的區域，透過它，您會看到顯示表面的青色背景的點陣圖。 但也就是不然。 上方項目已經在點陣圖上繪製文字。 透明的文字將不會顯示根本。

否`Draw`方法曾經讓點陣圖更透明化。 只有`Clear`可以這麼做。

## <a name="bitmap-color-types"></a>點陣圖色彩類型

最簡單`SKBitmap`建構函式可讓您指定整數像素寬度] 和 [點陣圖的高度。 其他`SKBitmap`建構函式是更複雜。 這些建構函式需要兩個列舉類型的引數： [ `SKColorType` ](xref:SkiaSharp.SKColorType)並[ `SKAlphaType` ](xref:SkiaSharp.SKAlphaType)。 其他建構函式使用[ `SKImageInfo` ](xref:SkiaSharp.SKImageInfo)結構中，會將此項資訊合併。

`SKColorType`列舉型別有 9 的成員。 每一個這些成員會描述特定的方式儲存的點陣圖像素的：

- `Unknown`
- `Alpha8` &mdash; 每個像素都是代表 alpha 值完全透明到完全不透明的 8 位元
- `Rgb565` &mdash; 每個像素為 16 位元，5 個位元，紅色和藍色和綠色的 6
- `Argb4444` &mdash; 每個像素都是 16 位元，每個對於 4 alpha、 紅色、 綠色和藍色
- `Rgba8888` &mdash; 每個像素都是 32 位元，每個為 8 紅色、 綠色、 藍色和 alpha
- `Bgra8888` &mdash; 每個像素都是 32 位元，8 每個藍色、 綠色、 紅色和 alpha
- `Index8` &mdash; 每個像素 8 位元，並代表內的索引 [`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8` &mdash; 每個像素都是從黑色的灰色陰影代表白色的 8 位元
- `RgbaF16` &mdash; 每個像素都是 64 位元，具有紅色、 綠色、 藍色和 alpha 以 16 位元浮點數格式

其中每個像素是 32 像素 （4 個位元組） 的兩種格式通常稱為_全彩_格式。 許多時間，當視訊顯示本身的其他格式日期不是色彩的能夠完整。 有限的色彩的點陣圖已足夠用於這些顯示，並允許以佔用較少的空間，在記憶體中的點陣圖。 

如今，程式設計人員幾乎一律使用全彩點陣圖並不必擔心會有其他格式。 例外狀況是`RgbaF16`允許更高的色彩解析度比甚至是全彩格式的格式。 不過，這種格式使用於特製化的用途，例如醫學造影，而且沒什麼特別意義與標準的全彩顯示搭配使用時。

這一系列的文章會限制本身`SKBitmap`色彩，依預設，若未使用的格式`SKColorType`指定成員。 這個預設的格式根據基礎平台。 Xamarin.Forms 所支援的平台的預設色彩類型是：

- `Rgba8888` 適用於 iOS 和 Android
- `Bgra8888` 適用於 UWP

唯一的差別是在記憶體中，4 個位元組的順序，這才會發生問題，當您直接存取像素位元。 這不會變得重要直到您取得文件[**存取 SkiaSharp 點陣圖像素**](pixel-bits.md)。

`SKAlphaType`列舉型別有四個成員：

- `Unknown`
- `Opaque` &mdash; 點陣圖已不透明
- `Premul` &mdash; 色彩元件先乘以 alpha 元件
- `Unpremul` &mdash; 色彩元件未預先相乘的 alpha 元件

以下是 4 位元組的紅色點陣圖像素與 50%透明效果，使用位元組順序紅色、 綠色、 藍色、 alpha 所示：

0xFF 0x00 0x00 0x80

顯示介面上呈現時包含半透明的像素的點陣圖，每個點陣圖像素的色彩元件必須先乘以該像素的 alpha 值，並顯示表面的相對應的像素的色彩元件必須相乘由 255 的 alpha 值減去。 然後可以結合兩個像素為單位。 點陣圖時，可以轉譯速度的點陣圖像素的色彩元件都已經預先 mulitplied 的 alpha 值。 就預乘的格式，儲存該相同的紅色像素像這樣：

0x80 0x00 0x00 0x80

此效能改善就是為什麼`SkiaSharp`以建立預設的點陣圖`Premul`格式。 但是，同樣地，您需要了解這只有當您存取和操作像素位元。

## <a name="drawing-on-existing-bitmaps"></a>在現有的點陣圖上的繪圖

您不需要建立新的點陣圖，以在其上繪製。 您也可以將現有點陣圖上繪製。 

**Monkey 鬍髭**頁面會使用其建構函式來載入**MonkeyFace.png**映像。 然後它會建立`SKCanvas`物件會根據該點陣圖，並且使用`SKPaint`和`SKPath`物件在其上繪製鬍髭：

```csharp
public partial class MonkeyMoustachePage : ContentPage
{
    SKBitmap monkeyBitmap;

    public MonkeyMoustachePage()
    {
        Title = "Monkey Moustache";

        monkeyBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MonkeyFace.png");

        // Create canvas based on bitmap
        using (SKCanvas canvas = new SKCanvas(monkeyBitmap))
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Style = SKPaintStyle.Stroke;
                paint.Color = SKColors.Black;
                paint.StrokeWidth = 24;
                paint.StrokeCap = SKStrokeCap.Round;

                using (SKPath path = new SKPath())
                {
                    path.MoveTo(380, 390);
                    path.CubicTo(560, 390, 560, 280, 500, 280);

                    path.MoveTo(320, 390);
                    path.CubicTo(140, 390, 140, 280, 200, 280);

                    canvas.DrawPath(path, paint);
                }
            }
        }

        // Create SKCanvasView to view result 
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
        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

建構函式結束時，會建立`SKCanvasView`其`PaintSurface`處理常式只會顯示結果：

[![Monkey 鬍髭](drawing-images/MonkeyMoustache.png "Monkey 鬍髭")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>複製及修改點陣圖

方法`SKCanvas`您可以使用上繪製點陣圖包含`DrawBitmap`。 這表示，您可以繪製一個點陣圖另一個，通常以某種方式加以修改。

若要修改點陣圖的最具彈性的方式是透過存取實際的像素位元、 主旨的文章涵蓋 **[存取 SkiaSharp 點陣圖像素](pixel-bits.md)** 。 但有許多其他技術來修改並不需要存取的像素位元的點陣圖。

隨附下列點陣圖 **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式是 360 像素寬度和高度 480 像素：

![Mountain Climbers](drawing-images/MountainClimbers.jpg "Mountain Climbers")

假設您還沒有收到左側 monkey，若要發佈此相片的權限。 其中一個解決方案是遮蔽 monkey 臉部使用稱為的技巧_pixelization_。 所面臨的像素會將數字取代色彩區塊，因此您不能進行的功能。 方法是平均化對應至這些區塊的像素的色彩，色彩區塊通常被衍生自原始的映像。 但您不需要執行此平均計算自己。 它會自動進行時您將點陣圖複製到較小的像素尺寸。 

左側的 monkey 臉部會佔用大約 72 個像素正方形區域的左上角的位置 （112、 238）。 讓我們來取代每一個都是 8-8 像素的正方形彩色磚塊，9-9 陣列該 72 個像素的方形區域。

**Pixelize 映像**網頁載入該點陣圖中，並先會建立稱為小 9 個像素正方形點陣圖`faceBitmap`。 這是複製剛 monkey 臉部的目的地。 目的矩形只是 9 個像素的正方形，但來源矩形是 72 個像素的正方形。 來源像素為單位的每個 8-8 區塊會彙總到單一像素，方法是平均化色彩。

下一個步驟是將原始點陣圖複製到新的點陣圖，呼叫的相同大小的`pixelizedBitmap`。 小型`faceBitmap`然後 72 個像素的正方形目的地矩形，除此之外，複製，讓每個像素的`faceBitmap`8 次擴大其成：

```csharp
public class PixelizedImagePage : ContentPage
{
    SKBitmap pixelizedBitmap;

    public PixelizedImagePage ()
    {
        Title = "Pixelize Image";

        SKBitmap originalBitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        // Create tiny bitmap for pixelized face
        SKBitmap faceBitmap = new SKBitmap(9, 9);

        // Copy subset of original bitmap to that
        using (SKCanvas canvas = new SKCanvas(faceBitmap))
        {
            canvas.Clear();
            canvas.DrawBitmap(originalBitmap,
                              new SKRect(112, 238, 184, 310),   // source
                              new SKRect(0, 0, 9, 9));          // destination

        }

        // Create full-sized bitmap for copy
        pixelizedBitmap = new SKBitmap(originalBitmap.Width, originalBitmap.Height);

        using (SKCanvas canvas = new SKCanvas(pixelizedBitmap))
        {
            canvas.Clear();

            // Draw original in full size
            canvas.DrawBitmap(originalBitmap, new SKPoint());

            // Draw tiny bitmap to cover face
            canvas.DrawBitmap(faceBitmap, 
                              new SKRect(112, 238, 184, 310));  // destination
        }

        // Create SKCanvasView to view result
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
        canvas.DrawBitmap(pixelizedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

建構函式結束時，會建立`SKCanvasView`來顯示結果：

[![Pixelize 映像](drawing-images/PixelizeImage.png "Pixelize 映像")](drawing-images/PixelizeImage-Large.png#lightbox)

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>旋轉點陣圖

另一個常見的工作旋轉點陣圖。 從 iPhone 或 iPad 相片媒體櫃中擷取的點陣圖時，這會特別有用。 除非裝置保留特定方向時拍攝的相片，圖片就會是有可能為上下顛倒或兩側。

將為上下顛倒的點陣圖時，需要建立另一個點陣圖作為第一個相同的大小，然後設定 來旋轉 180 度，同時將第一個複製到第二個轉換。 在本節中，範例的所有`bitmap`是`SKBitmap`您要旋轉的物件：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

當旋轉 90 度，您需要建立點陣圖是不同的大小大於原始交換的高度和寬度。 例如，如果原始點陣圖是 1200年像素寬和高 800 像素，旋轉的點陣圖是 800 像素寬和 1200年像素寬。 設定平移和旋轉，以便繞其左上角旋轉點陣圖，然後移至檢視。 (請記住`Translate`和`RotateDegrees`它們所套用的方式的相反順序呼叫方法。)以下是旋轉 90 度順時針旋轉的程式碼：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(bitmap.Height, 0);
    canvas.RotateDegrees(90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```                        

而以下是用於輪替逆時針旋轉 90 度的類似函式：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.Translate(0, bitmap.Width);
    canvas.RotateDegrees(-90);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

這兩種方法會在**相片拼圖**一文所述的頁面[**裁剪 SkiaSharp 點陣圖**](cropping.md#tile-division)。

可以讓使用者旋轉 90 度遞增中的點陣圖的程式只需要實作一個函式，來旋轉 90 度。 使用者可以再旋轉 90 度的任何增量重複執行此一函式。

程式也可以根據任何數量來旋轉點陣圖。 其中一個簡單的方法是修改 180 度旋轉 180 取代一般化的函式`angle`變數：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

不過，在一般的情況下，此邏輯會裁剪關閉旋轉點陣圖的邊角。 更好的方法是計算使用三角函數包含這些邊角旋轉點陣圖的大小。 

顯示此三角函數**點陣圖輪值表**頁面。 XAML 檔案會具現化`SKCanvasView`並`Slider`的範圍則可從 0 到 360 度與`Label`顯示目前的值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapRotatorPage"
             Title="Bitmap Rotator">
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="slider"
                Maximum="360"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference slider},
                              Path=Value,
                              StringFormat='Rotate by {0:F0}&#x00B0;'}"
               HorizontalTextAlignment="Center" />

    </StackLayout>
</ContentPage>
```

程式碼後置檔案載入點陣圖資源，並將其儲存為名稱為的靜態唯讀欄位`originalBitmap`。 在顯示的點陣圖`PaintSurface`處理常式`rotatedBitmap`，一開始設定為`originalBitmap`:

```csharp
public partial class BitmapRotatorPage : ContentPage
{
    static readonly SKBitmap originalBitmap = 
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.Banana.jpg");

    SKBitmap rotatedBitmap = originalBitmap;

    public BitmapRotatorPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(rotatedBitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        double angle = args.NewValue;
        double radians = Math.PI * angle / 180;
        float sine = (float)Math.Abs(Math.Sin(radians));
        float cosine = (float)Math.Abs(Math.Cos(radians));
        int originalWidth = originalBitmap.Width;
        int originalHeight = originalBitmap.Height;
        int rotatedWidth = (int)(cosine * originalWidth + sine * originalHeight);
        int rotatedHeight = (int)(cosine * originalHeight + sine * originalWidth);

        rotatedBitmap = new SKBitmap(rotatedWidth, rotatedHeight);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear(SKColors.LightPink);
            canvas.Translate(rotatedWidth / 2, rotatedHeight / 2);
            canvas.RotateDegrees((float)angle);
            canvas.Translate(-originalWidth / 2, -originalHeight / 2);
            canvas.DrawBitmap(originalBitmap, new SKPoint());
        }

        canvasView.InvalidateSurface();
    }
}
```

`ValueChanged`處理常式`Slider`會建立新的作業執行`rotatedBitmap`根據旋轉角度。 新的寬度和高度，根據 absolute values 的正弦和餘弦的原始寬度和高度的影響。 用來繪製旋轉點陣圖上的原始點陣圖的轉換將原始點陣圖中心移至原點，旋轉 180 度，然後再轉譯該中心旋轉點陣圖的中央。 (`Translate`和`RotateDegrees`的相反順序呼叫方法，會比套用的方式。)

請注意，使用`Clear`方法，讓背景`rotatedBitmap`淺粉紅。 這只是要說明的大小`rotatedBitmap`顯示器上：

[![點陣圖 Rotator](drawing-images/BitmapRotator.png "點陣圖輪值表")](drawing-images/BitmapRotator-Large.png#lightbox)

旋轉的點陣圖夠大正好足以包含整個原始點陣圖，但不是大。

## <a name="flipping-bitmaps"></a>翻轉的點陣圖

呼叫另一個作業通常對點陣圖_翻轉_。 就概念而言，在垂直軸或透過中央的點陣圖的水平軸周圍的三個維度旋轉點陣圖。 垂直翻轉建立鏡像映像。

**點陣圖擋板**頁面 **[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式會示範這些程序。 XAML 檔案包含`SKCanvasView`和的垂直和水平翻轉的兩個按鈕：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.BitmapFlipperPage"
             Title="Bitmap Flipper">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        
        <skia:SKCanvasView x:Name="canvasView"
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Flip Vertical"
                Grid.Row="1" Grid.Column="0"
                Margin="0, 10"
                Clicked="OnFlipVerticalClicked" />

        <Button Text="Flip Horizontal"
                Grid.Row="1" Grid.Column="1"
                Margin="0, 10"
                Clicked="OnFlipHorizontalClicked" />
    </Grid>
</ContentPage>
```

程式碼後置檔案會實作這兩項作業中的`Clicked`按鈕處理常式： 

```csharp
public partial class BitmapFlipperPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(BitmapRotatorPage),
            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public BitmapFlipperPage()
    {
        InitializeComponent();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnFlipVerticalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(-1, 1, bitmap.Width / 2, 0);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnFlipHorizontalClicked(object sender, ValueChangedEventArgs args)
    {
        SKBitmap flippedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

        using (SKCanvas canvas = new SKCanvas(flippedBitmap))
        {
            canvas.Clear();
            canvas.Scale(1, -1, 0, bitmap.Height / 2);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = flippedBitmap;
        canvasView.InvalidateSurface();
    }
}
```

垂直翻轉，即可使用的水平縮放比例的縮放轉換&ndash;1。 縮放的中心是點陣圖的垂直中心。 水平翻轉為使用垂直的縮放比例的縮放轉換&ndash;1。 

您可以看到從 monkey 襯衫上反轉的傳送失敗，翻轉不旋轉相同。 但如右邊 UWP 螢幕擷取畫面所示，水平及垂直翻轉兩者都是相同旋轉 180 度：

[![點陣圖擋板](drawing-images/BitmapFlipper.png "點陣圖擋板")](drawing-images/BitmapFlipper-Large.png#lightbox)

另一個常見的工作，可以使用類似的技巧來處理已裁剪的點陣圖矩形子集。 這下一步 一文所述[**裁剪 SkiaSharp 點陣圖**](cropping.md)。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
