---
title: SkiaSharp 點陣圖並排顯示
description: 圖格使用點陣圖重複水平和垂直區域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 24c33c61002130fe645bba54c307394bbc2e0656
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322076"
---
# <a name="skiasharp-bitmap-tiling"></a>SkiaSharp 點陣圖並排顯示

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/CatClock/)

當您在兩個先前的文章中所見[ `SKShader` ](xref:SkiaSharp.SKShader)類別可以建立線性或循環的漸層。 本文著重於`SKShader`利用點陣圖來並排顯示區域的物件。 可重複的點陣圖，水平及垂直，在其原始的方向或或者翻轉水平和垂直大小。 開關，可避免磚之間的不連續：

![Bitmap 並排顯示範例](bitmap-tiling-images/BitmapTilingSample.png "Bitmap 並排顯示範例")

靜態[ `SKShader.CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode))方法，以建立此著色器有`SKBitmap`參數，有兩個成員[ `SKShaderTileMode` ](xref:SkiaSharp.SKShaderTileMode)列舉型別：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

兩個參數指出用來水平並排和垂直並排顯示模式。 這是相同`SKShaderTileMode`列舉型別，也會使用漸層停駐的方法。

A [ `CreateBitmap` ](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix))多載包含`SKMatrix`引數來執行轉換上的並排顯示的點陣圖：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

這篇文章包含數個範例使用並排顯示的點陣圖中的這個矩陣轉換。

## <a name="exploring-the-tile-modes"></a>瀏覽的並排顯示模式

中的第一個程式**點陣圖並排**一節**著色器和其他效果**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例示範兩個效果`SKShaderTileMode`引數。 **點陣圖的並排顯示翻轉模式**XAML 檔案會具現化`SKCanvasView`並將兩個`Picker`可讓您選取的檢視`SKShaderTilerMode`水平和垂直並排顯示的值。 請注意，陣列`SKShaderTileMode`中所定義的成員`Resources`區段：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BitmapTileFlipModesPage"
             Title="Bitmap Tile Flip Modes">

    <ContentPage.Resources>
        <x:Array x:Key="tileModes"
                 Type="{x:Type skia:SKShaderTileMode}">
            <x:Static Member="skia:SKShaderTileMode.Clamp" />
            <x:Static Member="skia:SKShaderTileMode.Repeat" />
            <x:Static Member="skia:SKShaderTileMode.Mirror" />
        </x:Array>
    </ContentPage.Resources>

    <StackLayout>
        <skiaforms:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="xModePicker"
                Title="Tile X Mode"
                Margin="10, 0"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

        <Picker x:Name="yModePicker"
                Title="Tile Y Mode"
                Margin="10, 10"
                ItemsSource="{StaticResource tileModes}"
                SelectedIndex="0"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />

    </StackLayout>
</ContentPage>
```

建構函式的程式碼後置檔案載入至顯示坐 monkey 點陣圖資源。 第一次裁切影像使用[ `ExtractSubset` ](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI))方法`SKBitmap`，讓前端和英呎會觸碰點陣圖的邊緣。 接著會使用建構函式[ `Resize` ](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod))方法用來建立另一個點陣圖的大小。 這些變更會讓更適用於並排顯示的點陣圖：

```csharp
public partial class BitmapTileFlipModesPage : ContentPage
{
    SKBitmap bitmap;

    public BitmapTileFlipModesPage ()
    {
        InitializeComponent ();

        SKBitmap origBitmap = BitmapExtensions.LoadBitmapResource(
            GetType(), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

        // Define cropping rect
        SKRectI cropRect = new SKRectI(5, 27, 296, 260);

        // Get the cropped bitmap
        SKBitmap croppedBitmap = new SKBitmap(cropRect.Width, cropRect.Height);
        origBitmap.ExtractSubset(croppedBitmap, cropRect);

        // Resize to half the width and height
        SKImageInfo info = new SKImageInfo(cropRect.Width / 2, cropRect.Height / 2);
        bitmap = croppedBitmap.Resize(info, SKBitmapResizeMethod.Box);
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get tile modes from Pickers
        SKShaderTileMode xTileMode =
            (SKShaderTileMode)(xModePicker.SelectedIndex == -1 ?
                                        0 : xModePicker.SelectedItem);
        SKShaderTileMode yTileMode =
            (SKShaderTileMode)(yModePicker.SelectedIndex == -1 ?
                                        0 : yModePicker.SelectedItem);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap, xTileMode, yTileMode);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`PaintSurface`處理常式會取得`SKShaderTileMode`這兩個設定`Picker`檢視，並建立`SKShader`物件根據點陣圖和這兩個值。 這個著色器用來填滿畫布：

[![點陣圖的並排顯示翻頁動畫模式](bitmap-tiling-images/BitmapTileFlipModes.png "點陣圖 圖格翻頁動畫模式")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

在左側的 [iOS] 畫面會顯示的預設值的效果`SKShaderTileMode.Clamp`。 點陣圖位於左上角。 點陣圖，如下個像素的底端列會重複一路向下。 點陣圖的右邊，以像素為單位的最右側資料行是一直跨重複。 深棕色像素點陣圖的右下角的畫布的其餘部分著色。 很明顯，`Clamp`選項幾乎不會搭配點陣圖並排顯示 ！

在管理中心的 Android 畫面顯示的結果`SKShaderTileMode.Repeat`這兩個引數。 [] 圖格會重複水平和垂直大小。 通用 Windows 平台畫面示`SKShaderTileMode.Mirror`。 圖格會重複，但或者翻轉水平和垂直大小。 這個選項的優點是磚之間有任何不連續。

請記住，您可以使用不同的選項水平和垂直重複。 您可以指定`SKShaderTileMode.Mirror`做為第二個引數`CreateBitmap`但`SKShaderTileMode.Repeat`做為第三個引數。 在每個資料列中，一般的映像和鏡像映像，但不包括猴仔之間仍然替代猴仔會顛倒。

## <a name="patterned-backgrounds"></a>圖樣的背景

點陣圖的並排顯示常用來從相對較小的點陣圖建立圖樣的背景執行。 典型的例子是阻礙。

**演算法磚牆**頁面建立整個基礎構件和這兩半的磚，以分隔的實體，類似於一個小型點陣圖。 此磚會在下一個範例，它有靜態建構函式建立，所做的公用靜態屬性：

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    static AlgorithmicBrickWallPage()
    {
        const int brickWidth = 64;
        const int brickHeight = 24;
        const int morterThickness = 6;
        const int bitmapWidth = brickWidth + morterThickness;
        const int bitmapHeight = 2 * (brickHeight + morterThickness);

        SKBitmap bitmap = new SKBitmap(bitmapWidth, bitmapHeight);

        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint brickPaint = new SKPaint())
        {
            brickPaint.Color = new SKColor(0xB2, 0x22, 0x22);

            canvas.Clear(new SKColor(0xF0, 0xEA, 0xD6));
            canvas.DrawRect(new SKRect(morterThickness / 2,
                                       morterThickness / 2,
                                       morterThickness / 2 + brickWidth,
                                       morterThickness / 2 + brickHeight),
                                       brickPaint);

            int ySecondBrick = 3 * morterThickness / 2 + brickHeight;

            canvas.DrawRect(new SKRect(0,
                                       ySecondBrick,
                                       bitmapWidth / 2 - morterThickness / 2,
                                       ySecondBrick + brickHeight),
                                       brickPaint);

            canvas.DrawRect(new SKRect(bitmapWidth / 2 + morterThickness / 2,
                                       ySecondBrick,
                                       bitmapWidth,
                                       ySecondBrick + brickHeight),
                                       brickPaint);
        }

        // Save as public property for other programs
        BrickWallTile = bitmap;
    }

    public static SKBitmap BrickWallTile { private set; get; }
    ···
}
```

點陣圖是 70 個像素寬和高 60 像素：

![演算法的基礎構件背景牆 圖格](bitmap-tiling-images/AlgorithmicBrickWallTile.png "演算法的基礎構件背景牆 圖格")

其餘**演算法磚牆**頁面會建立`SKShader`水平和垂直重複此映像的物件：

```csharp
public class AlgorithmicBrickWallPage : ContentPage
{
    ···
    public AlgorithmicBrickWallPage ()
    {
        Title = "Algorithmic Brick Wall";

        // Create SKCanvasView
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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(BrickWallTile,
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

結果如下：

[![演算法磚牆](bitmap-tiling-images/AlgorithmicBrickWall.png "演算法的阻礙")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

您可能會偏好更實際的項目。 在此情況下，您可以取得實際的基礎構件牆的相片，並再將其裁剪。 這個點陣圖是 300 像素寬和高 150 個像素：

![磚背景牆 圖格](bitmap-tiling-images/BrickWallTile.jpg "磚背景牆 圖格")

用於此點陣圖**Photographic 磚牆**頁面：

```csharp
public class PhotographicBrickWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PhotographicBrickWallPage),
                        "SkiaSharpFormsDemos.Media.BrickWallTile.jpg");

    public PhotographicBrickWallPage()
    {
        Title = "Photographic Brick Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

請注意，`SKShaderTileMode`引數`CreateBitmap`兩者都是`Mirror`。 使用真實世界的映像建立的圖格時，通常會需要此選項。 鏡像圖格時，可避免不連續：

[![相片磚牆](bitmap-tiling-images/PhotographicBrickWall.png "相片的阻礙")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

某些工作，才能取得適當的點陣圖，圖格。 此不能很深的基礎構件凸顯出來，因為太多。 定期出現內重複的映像，並揭露此磚牆從較小的點陣圖建構的事實。

**媒體**的資料夾[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)範例也包含石刀牆上的這個映像：

![如此背景牆 圖格](bitmap-tiling-images/StoneWallTile.jpg "石背景牆 圖格")

不過，原始點陣圖是有點太大的磚。 它可以調整大小，但`SKShader.CreateBitmap`方法也可以藉由將轉換套用至該調整圖格。 此選項所示**石牆**頁面：

```csharp
public class StoneWallPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(StoneWallPage),
                        "SkiaSharpFormsDemos.Media.StoneWallTile.jpg");

    public StoneWallPage()
    {
        Title = "Stone Wall";

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

        using (SKPaint paint = new SKPaint())
        {
            // Create scale transform
            SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);

            // Create bitmap tiling
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror,
                                                 matrix);
            // Draw background
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKMatrix`值用來調整回其原始的一半大小的映像：

[![極 Wall](bitmap-tiling-images/StoneWall.png "石牆")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

轉換運作上用於原始點陣圖`CreateBitmap`方法？ 或未轉換圖格的結果陣列嗎？ 

回答這個問題的簡單方法是加入的旋轉轉換的一部分：

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

如果轉換套用至個別的圖格，然後應旋轉圖格的每個重複的映像，和結果會包含許多不連續。 但很明顯地，此圖格的複合的陣列會轉換的螢幕擷取畫面：

[![極旋轉的牆](bitmap-tiling-images/StoneWallRotated.png "石牆旋轉")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

一節[**圖格對齊**](#tile-alignment)，您會看到範例平移轉換套用至著色器。

獨立[ **Cat 時鐘**](https://developer.xamarin.com/samples/xamarin-forms/CatClock)範例 (不屬於**SkiaSharpFormsDemos**) 會模擬使用點陣圖並排顯示依據此 240 像素的正方形點陣圖 wood 資料粒度背景：

![木資料粒度](bitmap-tiling-images/WoodGrain.png "木資料粒度")

這是木地板的相片。 `SKShaderTileMode.Mirror`選項可讓它顯示為 wood 的更大的空間：

[![Cat 時鐘](bitmap-tiling-images/CatClock.png "Cat 時鐘")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>圖格的對齊方式

所有顯示到目前為止的範例已使用著色器所建立`SKShader.CreateBitmap`以涵蓋整個畫布。 在大部分情況下，您將使用點陣圖並排顯示來提出較小的區域，或 （更多很少） 來填滿內部的粗線。 以下是使用較小的矩形相片磚牆 圖格：

[![圖格對齊](bitmap-tiling-images/TileAlignment.png "圖格的對齊方式")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

這可能會看起來沒問題，或也許不是。 也許您是打擾的並排顯示模式不是以完整的基礎構件在左上角矩形的開頭。 這是因為著色器會對齊畫布並不是它們所裝飾的圖形化物件。

修正很簡單。 建立`SKMatrix`轉移變形為基礎的值。 轉換實際上會移到想對齊 圖格的左上角之點的並排顯示的圖樣。 這種方法所示**圖格的對齊方式**頁面上，建立映像未對齊的圖格，如上所示：

```csharp
public class TileAlignmentPage : ContentPage
{
    bool isAligned;

    public TileAlignmentPage()
    {
        Title = "Tile Alignment";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;

        // Add tap handler
        TapGestureRecognizer tap = new TapGestureRecognizer();
        tap.Tapped += (sender, args) =>
        {
            isAligned ^= true;
            canvasView.InvalidateSurface();
        };
        canvasView.GestureRecognizers.Add(tap);

        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            SKRect rect = new SKRect(info.Width / 7,
                                     info.Height / 7,
                                     6 * info.Width / 7,
                                     6 * info.Height / 7);

            // Get bitmap from other program
            SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;

            // Create bitmap tiling
            if (!isAligned)
            {
                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat);
            }
            else
            {
                SKMatrix matrix = SKMatrix.MakeTranslation(rect.Left, rect.Top);

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     matrix);
            }

            // Draw rectangle
            canvas.DrawRect(rect, paint);
        }
    }
}
```

**圖格對齊**頁面包含`TapGestureRecognizer`。 請點選或按一下畫面中，而程式回到`SKShader.CreateBitmap`方法使用`SKMatrix`引數。 這項轉換會轉移的模式，使左上角包含完整的基礎構件：

[![圖格點選對齊](bitmap-tiling-images/TileAlignmentTapped.png "圖格點選的對齊方式")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

您也可以使用這項技術，以確保並排顯示的點陣圖模式會置中繪製它的區域內。 在 **置中的圖格**頁面上，`PaintSurface`處理常式先計算座標，如同它便會顯示單一點陣圖畫布的中央。 它會使用這些座標來建立針對平移轉換`SKShader.CreateBitmap`。 如此圖格會置中，這項轉換會轉移與整個模式：

```csharp
public class CenteredTilesPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.monkey.png");

    public CenteredTilesPage ()
    {
        Title = "Centered Tiles";

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

        // Find coordinates to center bitmap in canvas...
        float x = (info.Width - bitmap.Width) / 2f;
        float y = (info.Height - bitmap.Height) / 2f;

        using (SKPaint paint = new SKPaint())
        {
            // ... but use them to create a translate transform
            SKMatrix matrix = SKMatrix.MakeTranslation(x, y);
            paint.Shader = SKShader.CreateBitmap(bitmap, 
                                                 SKShaderTileMode.Repeat, 
                                                 SKShaderTileMode.Repeat, 
                                                 matrix);

            // Use that tiled bitmap pattern to fill a circle
            canvas.DrawCircle(info.Rect.MidX, info.Rect.MidY,
                              Math.Min(info.Width, info.Height) / 2,
                              paint);
        }
    }
}
```

`PaintSurface`處理常式結束時，會在畫布的正中央繪製圓形。 果然，其中一個圖格處於完全的圓圈，中心及其他會排列在對稱式模式：

[![置中的磚](bitmap-tiling-images/CenteredTiles.png "磚置中對齊")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

中間的另一種方法是實際稍微更容易。 而不是建構在管理中心中放置的圖格平移轉換，您可以置邊角的並排顯示圖樣。 在 `SKMatrix.MakeTranslation`呼叫，請使用畫布的正中央的引數：

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

模式還是置中，也是對稱的但是沒有磚是位在中心：

[![置中的圖格替代](bitmap-tiling-images/CenteredTilesAlternate.png "磚替代置中對齊")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>簡化透過旋轉

有時候，使用中的旋轉轉換`SKShader.CreateBitmap`方法可以簡化 [點陣圖] 圖格。 當您嘗試定義鏈結連結圍欄的圖格時，這將成為很明顯。 **ChainLinkTile.cs**檔案會建立如下所示 （具有為了加以釐清粉紅色背景） 的圖格：

![硬式鏈結-連結 圖格](bitmap-tiling-images/HardChainLinkTile.png "硬式鏈結-連結 圖格")

[] 圖格必須包含兩個連結，使程式碼會分成四個象限中的圖格。 左上角和右下方象限都相同，但並不完整。 線路擁有一些其他的繪製在右上方和左下方象限中必須處理一些的色塊。 沒有這項工作的檔案是 174 行長度。

它其實更輕鬆地建立此圖格：

![更容易鏈結-[連結] 圖格](bitmap-tiling-images/EasierChainLinkTile.png "更容易鏈結-[連結] 圖格")

如果點陣圖 圖格著色器會旋轉 90 度，視覺效果為幾乎完全相同。

程式碼以建立更容易鏈結-連結 圖格會是一部分**鏈結連結圖格**頁面。 建構函式決定的程式執行所在的裝置，然後呼叫類型為基礎的圖格大小`CreateChainLinkTile`，其中繪製線條、 路徑和漸層著色器所使用的點陣圖：

```csharp
public class ChainLinkFencePage : ContentPage
{
    ···
    SKBitmap tileBitmap;

    public ChainLinkFencePage ()
    {
        Title = "Chain-Link Fence";

        // Create bitmap for chain-link tiling
        int tileSize = Device.Idiom == TargetIdiom.Desktop ? 64 : 128;
        tileBitmap = CreateChainLinkTile(tileSize);

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    SKBitmap CreateChainLinkTile(int tileSize)
    {
        tileBitmap = new SKBitmap(tileSize, tileSize);
        float wireThickness = tileSize / 12f;

        using (SKCanvas canvas = new SKCanvas(tileBitmap))
        using (SKPaint paint = new SKPaint())
        {
            canvas.Clear();
            paint.Style = SKPaintStyle.Stroke;
            paint.StrokeWidth = wireThickness;
            paint.IsAntialias = true;

            // Draw straight wires first
            paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                         new SKPoint(0, tileSize),
                                                         new SKColor[] { SKColors.Silver, SKColors.Black },
                                                         new float[] { 0.4f, 0.6f },
                                                         SKShaderTileMode.Clamp);

            canvas.DrawLine(0, tileSize / 2,
                            tileSize / 2, tileSize / 2 - wireThickness / 2, paint);

            canvas.DrawLine(tileSize, tileSize / 2,
                            tileSize / 2, tileSize / 2 + wireThickness / 2, paint);

            // Draw curved wires
            using (SKPath path = new SKPath())
            {
                path.MoveTo(tileSize / 2, 0);
                path.LineTo(tileSize / 2 - wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 + wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.Silver, SKColors.Black },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);

                path.Reset();
                path.MoveTo(tileSize / 2, tileSize);
                path.LineTo(tileSize / 2 + wireThickness / 2, tileSize / 2);
                path.ArcTo(wireThickness / 2, wireThickness / 2,
                           0,
                           SKPathArcSize.Small,
                           SKPathDirection.CounterClockwise,
                           tileSize / 2, tileSize / 2 - wireThickness / 2);

                paint.Shader = SKShader.CreateLinearGradient(new SKPoint(0, 0),
                                                             new SKPoint(0, tileSize),
                                                             new SKColor[] { SKColors.White, SKColors.Silver },
                                                             null,
                                                             SKShaderTileMode.Clamp);
                canvas.DrawPath(path, paint);
            }
            return tileBitmap;
        }
    }
    ···
}
```

除了線、 [] 圖格是透明的這表示，您可以將它顯示在其他項目之上。 程式會載入其中一個點陣圖資源、 顯示以填滿畫布，然後繪製在最上層的 著色器：

```csharp
public class ChainLinkFencePage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(ChainLinkFencePage), "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");
    ···

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        canvas.DrawBitmap(monkeyBitmap, info.Rect, BitmapStretch.UniformToFill, 
                            BitmapAlignment.Center, BitmapAlignment.Start);

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(tileBitmap, 
                                                 SKShaderTileMode.Repeat,
                                                 SKShaderTileMode.Repeat,
                                                 SKMatrix.MakeRotationDegrees(45));
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

請注意著色器也旋轉 45 度，因此很像真正的鏈結連結範圍導向：

[![鏈結連結圍欄](bitmap-tiling-images/ChainLinkFence.png "鏈結連結範圍")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>動畫點陣圖圖格

您可以建立動畫矩陣轉換，以動畫顯示整個點陣圖並排顯示模式。 也許您想要水平或垂直移動的模式，或兩者。 您可以藉由建立多變的座標為基礎的轉譯轉換來這麼做。

它也是一個小型的點陣圖上繪製，或在操作點陣圖的像素的位元速率為 60 次秒。 該點陣圖就用於並排顯示，而且整個的並排顯示的圖樣似乎會以動畫顯示。 

**動畫點陣圖 圖格**頁面會示範這種方法。 點陣圖是做為欄位是 64 像素正方形具現化。 建構函式呼叫`DrawBitmap`，賦予其初始的外觀。 如果`angle`欄位是零 （因為它是第一次呼叫方法時），點陣圖會包含為 X 跨越兩行。線條都夠長，一律都能到達無關的點陣圖邊緣`angle`值： 

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    const int SIZE = 64;

    SKCanvasView canvasView;
    SKBitmap bitmap = new SKBitmap(SIZE, SIZE);
    float angle;
    ···

    public AnimatedBitmapTilePage ()
    {
        Title = "Animated Bitmap Tile";

        // Initialize bitmap prior to animation
        DrawBitmap();

        // Create SKCanvasView 
        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
    void DrawBitmap()
    {
        using (SKCanvas canvas = new SKCanvas(bitmap))
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Blue;
            paint.StrokeWidth = SIZE / 8;

            canvas.Clear();
            canvas.Translate(SIZE / 2, SIZE / 2);
            canvas.RotateDegrees(angle);
            canvas.DrawLine(-SIZE, -SIZE, SIZE, SIZE, paint);
            canvas.DrawLine(-SIZE, SIZE, SIZE, -SIZE, paint);
        }
    }
    ···
}
```

中發生的動畫額外負荷`OnAppearing`和`OnDisappearing`會覆寫。 `OnTimerTick`方法以動畫顯示`angle`值從 0 度到 360 度每隔 10 秒旋轉點陣圖內 X 圖：

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    // For animation
    bool isAnimating;
    Stopwatch stopwatch = new Stopwatch();
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
        const int duration = 10;     // seconds
        angle = (float)(360f * (stopwatch.Elapsed.TotalSeconds % duration) / duration);
        DrawBitmap();
        canvasView.InvalidateSurface();

        return isAnimating;
    }
    ···
}
```

因為 X 圖的對稱，這等同於旋轉`angle`從 0 度到 90 度每隔 2.5 秒值。

`PaintSurface`從點陣圖建立著色器處理常式，並使用小畫家物件來色彩整個畫布：

```csharp
public class AnimatedBitmapTilePage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateBitmap(bitmap,
                                                 SKShaderTileMode.Mirror,
                                                 SKShaderTileMode.Mirror);
            canvas.DrawRect(info.Rect, paint);
        }
    }
}
```

`SKShaderTileMode.Mirror`選項可讓您確保，在每個點陣圖中 X 的庇護下的加入相鄰的點陣圖中的 X，以建立似乎大部分整體動畫的模式更為複雜，比簡單的動畫會建議：

[![動畫點陣圖 圖格](bitmap-tiling-images/AnimatedBitmapTile.png "動畫點陣圖 圖格")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
- [CatClock （範例）](https://developer.xamarin.com/samples/xamarin-forms/CatClock/)
