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
ms.openlocfilehash: 593d6a8b30d5ed0e143d1c013849d2bef571f6dc
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140292"
---
# <a name="creating-and-drawing-on-skiasharp-bitmaps"></a>在 SkiaSharp 點陣圖上建立和繪製

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

您已瞭解應用程式如何從 Web、從應用程式資源，以及從使用者的相片媒體櫃載入點陣圖。 您也可以在應用程式內建立新的點陣圖。 最簡單的方法包含下列其中一個函式 [`SKBitmap`](xref:SkiaSharp.SKBitmap.%23ctor(System.Int32,System.Int32,System.Boolean)) ：

```csharp
SKBitmap bitmap = new SKBitmap(width, height);
```

`width`和 `height` 參數是整數，並指定點陣圖的圖元維度。 此函式會建立一個全彩色點陣圖，每圖元四個位元組：紅色、綠色、藍色和 Alpha （不透明度）元件各有一個位元組。

建立新的點陣圖之後，您必須在點陣圖表面上取得一些東西。 您通常會以下列兩種方式之一來執行此動作：

- 使用標準繪圖方法在點陣圖上繪製 `Canvas` 。
- 直接存取圖元位。

本文示範第一種方法：

![繪圖範例](drawing-images/DrawingSample.png "繪圖範例")

第二種方法會在[**存取 SkiaSharp 點陣圖圖元**](pixel-bits.md)文章中討論。

## <a name="drawing-on-the-bitmap"></a>在點陣圖上繪製

在點陣圖表面上繪製與在影片顯示器上繪製相同。 若要在影片顯示上繪製，請 `SKCanvas` 從 `PaintSurface` 事件引數取得物件。 若要在點陣圖上繪製，您可以 `SKCanvas` 使用此函數建立物件 [`SKCanvas`](xref:SkiaSharp.SKCanvas.%23ctor(SkiaSharp.SKBitmap)) ：

```csharp
SKCanvas canvas = new SKCanvas(bitmap);
```

當您完成在點陣圖上繪製時，可以處置 `SKCanvas` 物件。 基於這個理由， `SKCanvas` 通常會在語句中呼叫此函式 `using` ：

```csharp
using (SKCanvas canvas = new SKCanvas(bitmap))
{
    ··· // call drawing function
}
```

然後可以顯示該點陣圖。 稍後，程式可以根據相同的點陣圖建立新的 `SKCanvas` 物件，然後再進行更多的繪製。

**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中的**Hello 點陣圖**頁面會寫入文字 "Hello，Bitmap！" 在點陣圖上，然後多次顯示該點陣圖。

的函式一 `HelloBitmapPage` 開始會建立一個 `SKPaint` 用於顯示文字的物件。 它會判斷文字字串的維度，並使用這些維度建立點陣圖。 接著，它會 `SKCanvas` 根據該點陣圖建立物件、呼叫 `Clear` ，然後呼叫 `DrawText` 。 使用新的點陣圖來呼叫是個不錯的主意， `Clear` 因為新建立的點陣圖可能會包含亂數據。

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

`PaintSurface`處理常式會在顯示的資料列和資料行中多次呈現點陣圖。 請注意， `Clear` 處理常式中的方法 `PaintSurface` 具有的引數 `SKColors.Aqua` ，其會將顯示介面的背景色彩：

[![Hello，Bitmap！](drawing-images/HelloBitmap.png "Hello，Bitmap！")](drawing-images/HelloBitmap-Large.png#lightbox)

淺綠色背景的外觀顯示點陣圖是透明的，但文字除外。

## <a name="clearing-and-transparency"></a>清除和透明度

[ **Hello 點陣圖**] 頁面的顯示會示範程式所建立的點陣圖是透明的，但黑色文字除外。 這就是為什麼顯示介面的淺綠色色彩會顯示。

的 `Clear` 方法檔 `SKCanvas` 描述了語句：「取代畫布目前剪輯中的所有圖元」。 使用「取代」這個字會顯示這些方法的重要特性：所有的繪圖方法都會將專案 `SKCanvas` 加入現有的顯示介面。 `Clear`方法會_取代_已存在的內容。

`Clear`有兩個不同的版本：

- [`Clear`](xref:SkiaSharp.SKCanvas.Clear(SkiaSharp.SKColor))具有參數的方法 `SKColor` 會將顯示介面的圖元取代為該色彩的圖元。

- [`Clear`](xref:SkiaSharp.SKCanvas.Clear)沒有參數的方法會以色彩取代圖元 [`SKColors.Empty`](xref:SkiaSharp.SKColors.Empty) ，這是所有元件（紅色、綠色、藍色和 Alpha）都設定為零的色彩。 此色彩有時稱為「透明黑色」。

`Clear`在新的點陣圖上呼叫不含引數的，會將整個點陣圖初始化為完全透明。 接下來在點陣圖上繪製的任何內容通常不透明或部分不透明。

以下是要嘗試的事項：在**Hello 點陣圖**頁面中，將套用 `Clear` 到的方法取代為 `bitmapCanvas` 這個：

```csharp
bitmapCanvas.Clear(new SKColor(255, 0, 0, 128));
```

此函式參數的順序 `SKColor` 為紅色、綠色、藍色和 Alpha，其中每個值的範圍可以從0到255。 請記住，Alpha 值0是透明的，而 Alpha 值255則是不透明的。

值（255，0，0，128）會將點陣圖圖元清除為紅色圖元，並具有50% 不透明度。 這表示點陣圖背景是半透明的。 點陣圖的半透明紅色背景會與顯示介面的淺綠色背景結合，以建立灰色背景。

嘗試將下列指派放在初始化運算式中，以將文字的色彩設定為透明黑色 `SKPaint` ：

```csharp
Color = new SKColor(0, 0, 0, 0)
```

您可能會認為，此透明文字會建立點陣圖的完全透明區域，您會在其中看到顯示介面的淺綠色背景。 但這不是這麼做。 文字會繪製在點陣圖上已有的內容之上。 透明文字完全不會顯示。

任何 `Draw` 方法都不會使點陣圖變得更透明。 只能 `Clear` 這麼做。

## <a name="bitmap-color-types"></a>點陣圖色彩類型

最簡單的函 `SKBitmap` 式可讓您指定點陣圖的整數圖元寬度和高度。 其他 `SKBitmap` 的函式則較為複雜。 這些構造函式需要兩個列舉類型的引數： [`SKColorType`](xref:SkiaSharp.SKColorType) 和 [`SKAlphaType`](xref:SkiaSharp.SKAlphaType) 。 其他的函式會使用 [`SKImageInfo`](xref:SkiaSharp.SKImageInfo) 結構，這會合並此資訊。

`SKColorType`列舉有9個成員。 這些成員的每一個都描述儲存點陣圖圖元的特定方式：

- `Unknown`
- `Alpha8`&mdash;每個圖元都是8位，代表從完全透明到完全不透明的 Alpha 值
- `Rgb565`&mdash;每個圖元都是16位、5位代表紅色和藍色，而6代表綠色
- `Argb4444`&mdash;每個圖元都是16位，4個分別用於 Alpha、紅色、綠色和藍色
- `Rgba8888`&mdash;每個圖元都是32位，分別是紅色、綠色、藍色和 Alpha 的8個
- `Bgra8888`&mdash;每個圖元都是32位，分別是藍色、綠色、紅色和 Alpha 的8個
- `Index8`&mdash;每個圖元都是8位，並代表在中的索引[`SKColorTable`](xref:SkiaSharp.SKColorTable)
- `Gray8`&mdash;每個圖元都是8位，代表從黑色到白色的灰色陰影
- `RgbaF16`&mdash;每個圖元都是64位，紅色、綠色、藍色和 Alpha 都是16位浮點格式

這兩種格式的每個圖元都是32圖元（4個位元組），通常稱為_全彩色_格式。 許多其他格式的日期會從影片顯示本身的時間開始，不能有完整的色彩。 有限色彩的點陣圖足以容納這些顯示器，而允許的點陣圖佔用較少的記憶體空間。

這幾天，程式設計人員幾乎一律會使用全彩色點陣圖，而不會干擾其他格式。 例外狀況是 `RgbaF16` 格式，其允許的色彩解析度高於全顏色的格式。 不過，這種格式適用于特殊用途，例如醫學影像處理，而且與標準全彩色顯示器搭配使用時，並不會有太大的意義。

這一系列的文章會將自己限制 `SKBitmap` 在未指定任何成員時預設使用的色彩格式 `SKColorType` 。 這個預設格式是以基礎平臺為基礎。 針對支援的平臺 Xamarin.Forms ，預設色彩類型為：

- `Rgba8888`適用于 iOS 和 Android
- `Bgra8888`適用于 UWP

唯一的差異在於記憶體中4個位元組的順序，而這只有在您直接存取圖元位時才會發生問題。 在您進入[**存取 SkiaSharp 點陣圖圖元**](pixel-bits.md)的文章之前，這不會變得很重要。

`SKAlphaType`列舉有四個成員：

- `Unknown`
- `Opaque`&mdash;點陣圖沒有透明度
- `Premul`&mdash;色彩元件是以 Alpha 元件乘上
- `Unpremul`&mdash;色彩元件不會以 Alpha 元件來預先乘以

以下是具有50% 透明度的4位元組紅色點陣圖圖元，以紅色、綠色、藍色、Alpha 的順序顯示的位元組：

0xFF 0x00 0x00 0x80

當包含半透明圖元的點陣圖在顯示介面上呈現時，每個點陣圖圖元的色彩元件都必須乘以該圖元的 Alpha 值，而顯示介面的對應圖元色彩元件則必須乘以255減去 Alpha 值。 然後可以合併這兩個圖元。 如果點陣圖圖元中的色彩元件已由 Alpha 值預先 mulitplied，則點陣圖可以更快速地呈現。 相同的紅色圖元會以前置乘的格式儲存如下：

0x80 0x00 0x00 0x80

這項效能改善是為什麼 `SkiaSharp` 點陣圖預設是以格式建立的 `Premul` 。 但同樣地，只有在您存取和操作圖元位時，才需要知道這一點。

## <a name="drawing-on-existing-bitmaps"></a>在現有的點陣圖上繪圖

您不需要建立要在其上繪製的新點陣圖。 您也可以在現有的點陣圖上繪製。

[**猴子 Moustache** ] 頁面會使用其函式來載入**MonkeyFace**影像。 接著，它會 `SKCanvas` 根據該點陣圖建立物件，並使用 `SKPaint` 和 `SKPath` 物件在其上繪製 moustache：

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

此函式最後會建立， `SKCanvasView` 其 `PaintSurface` 處理常式只會顯示結果：

[![猴子 Moustache](drawing-images/MonkeyMoustache.png "猴子 Moustache")](drawing-images/MonkeyMoustache-Large.png#lightbox)

## <a name="copying-and-modifying-bitmaps"></a>複製和修改點陣圖

`SKCanvas`您可以用來在點陣圖上繪製的方法包括 `DrawBitmap` 。 這表示您可以在另一個點陣圖上繪製，通常會以某種方式進行修改。

修改點陣圖最具彈性的方式，就是存取實際的圖元位，這是**[存取 SkiaSharp 點陣圖圖元](pixel-bits.md)** 文章中所涵蓋的主題。 但是還有許多其他技術可修改不需要存取圖元位的點陣圖。

**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式隨附的下列點陣圖是360圖元寬和480圖元（高度）：

![山區 Climbers](drawing-images/MountainClimbers.jpg "山區 Climbers")

假設您尚未收到左邊的猴子的許可權，無法發佈此相片。 其中一個解決方法是使用稱為_pixelization_的技術來隱匿猴子的臉部。 臉部的圖元會取代成色彩的區塊，因此您無法完成功能。 色彩的區塊通常是從原始影像衍生而來的，其方式是將對應于這些區塊的圖元色彩平均。 但是您不需要自行執行此平均。 當您將點陣圖複製到較小的圖元維度時，它就會自動發生。

左邊的臉部會佔用大約72圖元的正方形區域，而點的左上角（112，238）。 讓我們將 72-圖元的正方形區域取代成彩色組塊的9到9陣列，每個都是 8 x 8 圖元的正方形。

**Pixelize 影像**頁面會載入該點陣圖，並先建立名為的小型9圖元正方形點陣圖 `faceBitmap` 。 這是只複製猴子臉部的目的地。 目的矩形只是9個圖元的方形，而來源矩形則是 72-圖元正方形。 每 8 8 個的來源圖元區塊會藉由將色彩平均合併到一個圖元。

下一步是將原始點陣圖複製到相同大小的新點陣圖中，稱為 `pixelizedBitmap` 。 `faceBitmap`然後使用 72-圖元方形目的地矩形將小型複製到其上，讓的每個圖元 `faceBitmap` 擴充至其大小的8倍：

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

<a name="rotating-bitmaps" />

## <a name="rotating-bitmaps"></a>旋轉點陣圖

另一個常見的工作是旋轉點陣圖。 從 iPhone 或 iPad 相片媒體櫃抓取點陣圖時，這特別有用。 除非拍攝相片時，裝置是以特定的方向保留，否則圖片可能會反轉或橫向。

開啟點陣圖時，必須建立另一個點陣圖，使其大小與第一個相同，然後將第一個轉換成180度旋轉，同時將第一個點陣圖複製到第二個。 在本節的所有範例中， `bitmap` 是 `SKBitmap` 您需要輪替的物件：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(180, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

以90度旋轉時，您必須建立與原始大小不同的點陣圖，方法是交換高度和寬度。 例如，如果原始點陣圖是1200圖元寬和800圖元高，旋轉點陣圖會是800圖元寬和1200圖元寬。 設定轉譯和旋轉，讓點陣圖繞著其左上角旋轉，然後再移至 view。 （請記住， `Translate` 和方法的 `RotateDegrees` 呼叫方式與套用它們的順序相反）。以下是順時針旋轉90度的程式碼：

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

以下是將90度逆時針旋轉的類似函式：

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

這兩種方法會用於[**裁剪 SkiaSharp 點陣圖**](cropping.md#tile-division)一文中所述的**相片謎題**頁面。

一種程式，可讓使用者在90度的增量中旋轉點陣圖，只需要執行一個函式來旋轉90度。 然後，使用者可以重複執行這個函式，以90度的任何增量旋轉。

程式也可以使用任何數量來旋轉點陣圖。 一個簡單的方法是修改以180度旋轉的函式，方法是以一般化變數取代 180 `angle` ：

```csharp
SKBitmap rotatedBitmap = new SKBitmap(bitmap.Width, bitmap.Height);

using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
{
    canvas.Clear();
    canvas.RotateDegrees(angle, bitmap.Width / 2, bitmap.Height / 2);
    canvas.DrawBitmap(bitmap, new SKPoint());
}
```

不過，在一般情況下，此邏輯會裁剪旋轉點陣圖的角落。 較好的方法是使用三角函數來計算旋轉點陣圖的大小，以包含這些角落。

此三角函數會顯示在**點陣圖旋轉**頁頁面中。 XAML 檔案會具現化和，其 `SKCanvasView` `Slider` 範圍可以從0到360度，並 `Label` 顯示目前的值：

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

程式碼後置檔案會載入點陣圖資源，並將它儲存為靜態唯讀欄位，名稱為 `originalBitmap` 。 在處理常式中顯示的點陣圖 `PaintSurface` 是 `rotatedBitmap` ，一開始會設定為 `originalBitmap` ：

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

的 `ValueChanged` 處理常式 `Slider` 會根據旋轉角度來執行建立新的作業 `rotatedBitmap` 。 新的寬度和高度是以原始寬度和高度的正弦值和余弦絕對值為基礎。 用來在旋轉點陣圖上繪製原始點陣圖的轉換會將原始點陣圖中心移至原點，然後依指定的度數旋轉，然後將該中心轉譯成旋轉點陣圖的中央。 （ `Translate` 和 `RotateDegrees` 方法的呼叫順序與套用方式相反。）

請注意，使用 `Clear` 方法讓 `rotatedBitmap` 淺粉色的背景。 這只是為了說明顯示的大小 `rotatedBitmap` ：

[![點陣圖旋轉](drawing-images/BitmapRotator.png "點陣圖旋轉")](drawing-images/BitmapRotator-Large.png#lightbox)

旋轉的點陣圖夠大，足以包含整個原始點陣圖，而不是更大。

## <a name="flipping-bitmaps"></a>翻轉點陣圖

通常在點陣圖上執行的另一種作業稱為「_翻轉_」。 在概念上，點陣圖會沿著垂直軸或水準軸的三個維度旋轉，直到點陣圖的中央。 垂直翻轉會建立鏡像影像。

**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中的**點陣圖翻轉**頁面會示範這些進程。 XAML 檔案包含 `SKCanvasView` 和兩個按鈕，用於垂直和水準翻轉：

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

程式碼後置檔案會在按鈕的處理常式中執行這兩項作業 `Clicked` ：

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

垂直翻轉是由水準縮放比例為1的縮放轉換所完成 &ndash; 。 縮放中心是點陣圖的垂直中心。 水準翻轉是垂直縮放比例為1的縮放轉換 &ndash; 。

正如您在猴子襯衫上的反轉信件中所見，翻轉與旋轉並不相同。 但正如右側的 UWP 螢幕擷取畫面所示，水準和垂直翻轉會與旋轉180度相同：

[![點陣圖翻轉](drawing-images/BitmapFlipper.png "點陣圖翻轉")](drawing-images/BitmapFlipper-Large.png#lightbox)

另一個可以使用類似技術處理的一般工作，就是將點陣圖裁剪成矩形子集。 這會在下一篇文章[**裁剪 SkiaSharp 點陣圖**](cropping.md)中說明。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
