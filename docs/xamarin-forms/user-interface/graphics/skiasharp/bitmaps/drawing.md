---
title: 在 SkiaSharp 點陣圖上建立和繪製
description: 瞭解如何建立 SkiaSharp 點陣圖，然後根據這些點陣圖來建立畫布，以繪製這些點陣圖。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 79BD3266-D457-4E50-BDDF-33450035FA0F
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3ca546f69dd8c4995747ad352c54e9ba184b2425
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373493"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>在 SkiaSharp 點陣圖上建立和繪製

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

您已瞭解應用程式如何從 Web、應用程式資源和使用者的相片媒體櫃載入點陣圖。 您也可以在應用程式中建立新的點陣圖。 最簡單的方法牽涉到下列其中一個函式 [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) ：

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

`width`和 `height` 參數為整數，並指定點陣圖的圖元尺寸。 這個函式會建立一個具有四個位元組的全彩點陣圖：紅色、綠色、藍色和 Alpha (不透明度) 元件。

建立新的點陣圖之後，您必須在點陣圖表面上取得一些東西。 您通常會以下列兩種方式之一來執行：

- 使用標準繪製方法在點陣圖上繪製 `Canvas` 。
- 直接存取圖元位。

本文會示範第一種方法：

![繪圖範例](drawing-images/DrawingSample.png "繪圖範例")

第二種方法會在 [**存取 SkiaSharp 點陣圖圖元**](pixel-bits.md)的文章中討論。

## <a name="drawing-on-the-bitmap"></a>在點陣圖上繪圖

在點陣圖表面上繪製與在影片顯示器上繪製的相同。 若要在影片顯示器上繪製，您可以 `SKCanvas` 從 `PaintSurface` 事件引數取得物件。 若要在點陣圖上繪圖，您可以使用此函式來建立 `SKCanvas` 物件 [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) ：

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

當您在點陣圖上完成繪圖時，可以處置 `SKCanvas` 物件。 基於這個理由， `SKCanvas` 通常會在語句中呼叫此函式 `using` ：

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

然後可以顯示點陣圖。 稍後，程式可以根據相同的點陣圖來建立新的 `SKCanvas` 物件，然後再進一步繪製。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中的 **Hello Bitmap** 頁面會寫入文字 "Hello，Bitmap！" 在點陣圖上，然後多次顯示該點陣圖。

的函式會 `HelloBitmapPage` 從建立用 `SKPaint` 來顯示文字的物件開始。 它會決定文字字串的維度，並建立具有這些維度的點陣圖。 然後，它會 `SKCanvas` 根據該點陣圖、呼叫，然後 `Clear` 再呼叫來建立物件 `DrawText` 。 使用新的點陣圖來呼叫是很好的主意， `Clear` 因為新建立的點陣圖可能包含亂數據。

此函式會藉由建立 `SKCanvasView` 物件來顯示點陣圖來結束：

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

此 `PaintSurface` 處理常式會在顯示的資料列和資料行中多次呈現點陣圖。 請注意， `Clear` 處理常式中的方法 `PaintSurface` 具有的引數 `SKColors.Aqua` ，其會將顯示介面的背景色彩：

[![Hello，Bitmap！](drawing-images/HelloBitmap.png "Hello，Bitmap！")](drawing-images/HelloBitmap-Large.png#lightbox)

淺綠色背景的外觀顯示點陣圖是透明的，但文字除外。

## <a name="clearing-and-transparency"></a>清除和透明度

[ **Hello 點陣圖** ] 頁面的顯示會示範程式所建立的點陣圖是透明的，但黑色文字除外。 這就是為什麼顯示介面的綠色色彩會顯示。

的 `Clear` 方法檔 `SKCanvas` 說明其使用語句：「取代畫布「目前剪輯」中的所有圖元。 使用「取代」一字會顯示這些方法的重要特性：所有繪圖方法都會 `SKCanvas` 將某些內容新增至現有的顯示介面。 這些 `Clear` 方法會 _取代_ 現有的內容。

`Clear` 存在於兩個不同版本中：

- [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor))具有參數的方法 `SKColor` 會將顯示介面的圖元取代為該色彩的圖元。

- [`Clear`](xref:SkiaSharp.SKCanvas.Clear)沒有參數的方法會以色彩取代圖元 [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) ，也就是所有元件 (紅色、綠色、藍色和 Alpha) 都會設定為零的色彩。 此色彩有時稱為「透明黑色」。

`Clear`在新的點陣圖上不使用引數呼叫，會將整個點陣圖初始化為完全透明。 接下來在點陣圖上繪製的任何事物通常會是不透明或部分不透明。

請嘗試下列動作：在 [ **Hello 點陣圖** ] 頁面中，以下列方式取代套用 `Clear` 至的方法 `bitmapCanvas` ：

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

這些參數的順序 `SKColor` 為紅色、綠色、藍色和 Alpha，其中每個值的範圍可以從0到255。 請記住，Alpha 值為 [0] 是透明的，而 Alpha 值255則是不透明。

值 (255、0、0、128) 會將點陣圖圖元清除為具有50% 不透明度的紅色圖元。 這表示點陣圖背景是半透明的。 點陣圖的半透明紅色背景會與顯示介面的青色背景結合，以建立灰色背景。

請嘗試將下列指派放在初始化運算式中，以將文字的色彩設定為透明黑色 `SKPaint` ：

```csharp
Color = new SKColor(0, 0, 0, 0)
```

您可能會認為這個透明文字會建立點陣圖的完全透明區域，您可以在其中看到顯示介面的淺綠色背景。 但這不是如此。 文字會繪製在點陣圖上已有的內容上方。 透明文字完全不會顯示。

沒有任何 `Draw` 方法可讓點陣圖更為透明。 只 `Clear` 可以這樣做。

## <a name="bitmap-color-types"></a>點陣圖色彩類型

最簡單的函 `SKBitmap` 式可讓您指定點陣圖的整數圖元寬度和高度。 其他 `SKBitmap` 的函式更為複雜。 這些函式需要兩個列舉類型的引數： [`SKColorType`](xref:SkiaSharp.SKColorType) 和 [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) 。 其他的函式會使用 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) 結構，此結構會合並此資訊。

`SKColorType`列舉有九個成員。 這些成員每一個都描述儲存點陣圖圖元的特殊方式：

- `Unknown`
- `Alpha8`&mdash;每個圖元都是8位，代表從完全透明到完全不透明的 Alpha 值
- `Rgb565`&mdash;每個圖元都是16位、紅色和藍色的5位，以及6表示綠色
- `Argb4444`&mdash;每個圖元都是16個位，四個分別用於 Alpha、紅色、綠色和藍色
- `Rgba8888`&mdash;每個圖元都是32位，紅色、綠色、藍色和 Alpha 各有8個
- `Bgra8888`&mdash;每個圖元都是32位，每個圖元都有藍色、綠色、紅色和 Alpha
- `Index8`&mdash;每個圖元都是8位，並代表一個索引到[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;每個圖元都是8位，代表從黑色到白色的灰色陰影
- `RgbaF16`&mdash;每個圖元都是64位，16位浮點數格式的紅色、綠色、藍色和 Alpha

這兩種格式的每個圖元都是32圖元 (4 個位元組) 通常稱為「 _完整色彩_ 」格式。 許多其他格式的日期從影片顯示時的時間，不能使用完整色彩。 有限色彩的點陣圖適合這些顯示器，而允許的點陣圖則佔用較少的記憶體空間。

這些日子，程式設計人員幾乎一律會使用全彩點陣圖，而不會干擾其他格式。 例外狀況的 `RgbaF16` 格式是，其允許比完整色彩格式更高的色彩解析度。 不過，這種格式是用於特定用途（例如醫療影像），而且在搭配使用標準的全彩顯示器時並不太合理。

當未指定任何成員時，這一系列的文章將會限制為 `SKBitmap` 預設使用的色彩格式 `SKColorType` 。 此預設格式是以基礎平臺為基礎。 針對所支援的平臺 Xamarin.Forms ，預設色彩類型為：

- `Rgba8888` 適用于 iOS 和 Android
- `Bgra8888` 適用于 UWP

唯一的差別是記憶體中4個位元組的順序，而且只有當您直接存取圖元位時，才會發生問題。 除非您進入 [**存取 SkiaSharp 點陣圖圖元**](pixel-bits.md)的文章，否則這將會變得很重要。

`SKAlphaType`列舉包含四個成員：

- `Unknown`
- `Opaque`&mdash;點陣圖沒有透明度
- `Premul`&mdash;色彩元件會預先乘以 Alpha 元件
- `Unpremul`&mdash;色彩元件未預先乘以 Alpha 元件

以下是具有50% 透明度的4位元組紅色點陣圖圖元，其中的位元組顯示順序如下：紅色、綠色、藍色、Alpha：

0xFF 0x00 0x00 0x80

在顯示介面上轉譯包含半透明圖元的點陣圖時，每個點陣圖圖元的色彩元件都必須乘以該圖元的 Alpha 值，而且顯示介面之對應圖元的色彩元件必須乘以255減去 Alpha 值。 然後可以合併這兩個圖元。 如果點陣圖圖元中的色彩元件已經過 Alpha 值的 mulitplied，則可以更快轉譯點陣圖。 相同的紅色圖元會以預先相乘的格式儲存，如下所示：

0x80 0x00 0x00 0x80

這項效能改進是 `SkiaSharp` 因為預設會使用格式建立點陣圖 `Premul` 。 但同樣地，只有當您存取和操作圖元位時，才需要知道這一點。

## <a name="drawing-on-existing-bitmaps"></a>在現有點陣圖上繪圖

不需要建立要在其上繪製的新點陣圖。 您也可以在現有點陣圖上進行繪製。

[ **猴子 Moustache** ] 頁面會使用它的函式來載入 **MonkeyFace.png** 映射。 然後，它會 `SKCanvas` 根據該點陣圖建立物件，並使用 `SKPaint` 和 `SKPath` 物件在其上繪製 moustache：

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

此函式會藉由建立 `SKCanvasView` 其 `PaintSurface` 處理常式直接顯示結果來結束：

[![猴子 Moustache](drawing-images/MonkeyMoustache.png "猴子 Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>複製和修改點陣圖

`SKCanvas`您可以用來在點陣圖上繪製的方法包括 `DrawBitmap` 。 這表示您可以在另一個點陣圖上繪製一個點陣圖，通常會以某種方式加以修改。

修改點陣圖最有彈性的方式，就是存取實際的圖元位，也就是 **[存取 SkiaSharp 點陣圖圖元](pixel-bits.md)** 的文章中所涵蓋的主題。 但是，還有許多其他技術可以用來修改不需要存取圖元位的點陣圖。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式隨附的下列點陣圖為高度360圖元寬和480圖元：

![山區 Climbers](drawing-images/MountainClimbers.jpg "山區 Climbers")

假設您尚未從左邊的猴子取得許可權來發佈此相片。 其中一個解決方法是使用稱為 _pixelization_ 的技術來遮蔽猴子的臉部。 臉部的圖元會取代為色彩的區塊，因此您無法使用這些功能。 色彩的區塊通常衍生自原始影像，方法是將對應至這些區塊的圖元色彩平均。 但您不需要自行執行這種計算。 當您將點陣圖複製到較小的圖元維度時，它就會自動發生。

左邊的猴子臉部大約占著 72-圖元的正方形區域，其左上角為 (112，238) 。 讓我們將該 72-圖元的正方形區域取代為具有 9 x 9 的彩色區塊陣列，其中每個都是 8 x 8 圖元的正方形。

**Pixelize 影像** 頁面會在該點陣圖中載入，並先建立一個小9圖元的正方形點陣圖，稱為 `faceBitmap` 。 這是只複製猴子臉部的目的地。 目的地矩形只是9個圖元的正方形，但來源矩形是 72-圖元的正方形。 每 8 x 8 個來源圖元的區塊會平均地向下合併至一個圖元。

下一步是將原始點陣圖複製到稱為的相同大小的新點陣圖 `pixelizedBitmap` 。 然後， `faceBitmap` 會使用 72-圖元的正方形目的地矩形將小小的位置複製到上面，如此一來，的每個圖元 `faceBitmap` 就會展開為其大小的8倍：

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

此函式會藉由建立 `SKCanvasView` 來顯示結果來結束：

[![Pixelize 影像](drawing-images/PixelizeImage.png "Pixelize 影像")](drawing-images/PixelizeImage-Large.png#lightbox)

## <a name="rotating-bitmaps"></a>旋轉點陣圖

另一項常見的工作是輪替點陣圖。 從 iPhone 或 iPad 照片媒體櫃抓取點陣圖時，這特別有用。 除非裝置在拍攝相片時處於特定的方向，否則圖片很可能會被倒置或側邊。

若要讓點陣圖倒置，必須建立另一個點陣圖，使其大小與第一個點陣圖相同，然後將轉換設定為旋轉180度，同時將第一個點陣圖複製到第二個。 在本節中的所有範例中， `bitmap` 是 `SKBitmap` 您需要輪替的物件：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

當旋轉90度時，您需要藉由交換高度和寬度來建立與原始大小不同的點陣圖。 例如，如果原始點陣圖的寬度為1200圖元寬和800圖元高，則旋轉點陣圖的寬度為800圖元寬和1200圖元。 設定平移和旋轉，讓點陣圖沿著左上角旋轉，然後再移至視野。  (請記住， `Translate` 和 `RotateDegrees` 方法會以與其套用方式相反的順序來呼叫。 ) 以下的程式碼會順時針旋轉90度：

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

以下是一種類似的函式，用來逆時針旋轉90度的計數器：

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

這兩種方法會用於 [**裁剪 SkiaSharp 點陣圖**](cropping.md#cropping-skiasharp-bitmaps)文章中所述的 **相片謎題** 頁面。

一種程式，可讓使用者以90度的增量來旋轉點陣圖，只需要執行一個函式以90度旋轉。 然後，使用者可以重複執行這一個函式，以90度的任何增量來旋轉。

程式也可以將點陣圖旋轉任意數量。 其中一個簡單的方法是以一般化變數取代180，以修改180度旋轉的函式 `angle` ：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

不過，在一般情況下，此邏輯將會裁剪旋轉點陣圖的角落。 更好的方法是使用三角函數來計算旋轉點陣圖的大小，以包含這些角落。

此三角函數會顯示在 **點陣圖旋轉** 器頁面中。 XAML 檔案具現化 `SKCanvasView` 和，其 `Slider` 範圍從0到360度，並 `Label` 顯示目前的值：

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

程式碼後端檔案會載入點陣圖資源，並將其儲存為名為的靜態唯讀欄位 `originalBitmap` 。 處理常式中顯示的點陣圖 `PaintSurface` 是 `rotatedBitmap` ，其最初設定為 `originalBitmap` ：

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

的 `ValueChanged` 處理常式 `Slider` 會根據旋轉角度，執行建立新的作業 `rotatedBitmap` 。 新的寬度和高度是根據原始寬度和高度的正弦值和余弦絕對值。 用來在旋轉點陣圖上繪製原始點陣圖的轉換會將原始點陣圖中心移至原點，然後依指定的度數旋轉，然後將該中心轉譯為旋轉點陣圖的中心。  (`Translate` 和 `RotateDegrees` 方法的呼叫順序與其套用方式相反。 ) 

請注意，使用 `Clear` 方法讓 `rotatedBitmap` 淺粉紅色的背景。 這只是為了說明 `rotatedBitmap` 顯示器上的大小：

[![點陣圖旋轉](drawing-images/BitmapRotator.png "點陣圖旋轉")](drawing-images/BitmapRotator-Large.png#lightbox)

旋轉點陣圖的大小夠大，足以包含整個原始點陣圖，但不能超過。

## <a name="flipping-bitmaps"></a>翻轉點陣圖

通常在點陣圖上執行的另一項作業稱為「 _翻轉_ 」。 就概念而言，點陣圖是沿著垂直軸或水準軸的三個維度旋轉到點陣圖的中心。 垂直翻轉會建立鏡像影像。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中的 **點陣圖擋板** 頁面會示範這些處理常式。 XAML 檔案包含 `SKCanvasView` 和兩個按鈕，可垂直和水準翻轉：

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

程式碼後端檔案會在這些按鈕的處理常式中執行這兩項作業 `Clicked` ：

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

垂直翻轉是由水準縮放比例比例為1的縮放轉換來完成 &ndash; 。 縮放中心是點陣圖的垂直中心。 水準翻轉是垂直調整因數為1的縮放變換 &ndash; 。

您可以從您在猴子恤的反轉信件中看到，翻轉與旋轉不同。 但如右側的 UWP 螢幕擷取畫面所示，水準和垂直翻轉與旋轉180度相同：

[![點陣圖擋板](drawing-images/BitmapFlipper.png "點陣圖擋板")](drawing-images/BitmapFlipper-Large.png#lightbox)

另一個可以使用類似的技術來處理的一般工作，是將點陣圖裁剪成矩形子集。 這會在下一篇文章中說明 [**裁剪 SkiaSharp 點陣圖**](cropping.md)。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)