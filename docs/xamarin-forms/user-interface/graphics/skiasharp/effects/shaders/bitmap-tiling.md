---
title: SkiaSharp 點陣圖平鋪
description: 使用以水準和垂直方式重複的點陣圖來並排顯示區域。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 9ED14E07-4DC8-4B03-8A33-772838BF51EA
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 92bcdd2c86387f229b9acf066456985c4a7ad514
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373662"
---
# <a name="skiasharp-bitmap-tiling"></a>SkiaSharp 點陣圖平鋪

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/catclock)

如您在先前的兩篇文章中所見， [`SKShader`](xref:SkiaSharp.SKShader) 類別可以建立線性或圓形漸層。 本文著重于 `SKShader` 使用點陣圖來並排顯示區域的物件。 點陣圖可以水準和垂直方式以水準和垂直方式重複，或是水準和垂直翻轉。 翻轉可避免磚之間的不連續：

![點陣圖並排平取樣](bitmap-tiling-images/BitmapTilingSample.png "點陣圖並排平取樣")

[`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode))建立此著色器的靜態方法具有 `SKBitmap` 參數和列舉的兩個成員 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

這兩個參數表示用於水準並排顯示和垂直並排顯示的模式。 這是同樣與漸層 `SKShaderTileMode` 方法一起使用的相同列舉。

多載 [`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 包含 `SKMatrix` 可在並排點陣圖上執行轉換的引數：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

本文包含數個範例，說明如何搭配使用此矩陣轉換與並排點陣圖。

## <a name="exploring-the-tile-modes"></a>探索磚模式

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例之 [著色器] **和 [其他效果** ] 頁面的 [ **點陣圖** 並排顯示] 區段中的第一個程式，會示範這兩個 `SKShaderTileMode` 引數的效果。 **點陣圖磚翻轉模式** 的 XAML 檔案具現化 `SKCanvasView` ，以及兩個可 `Picker` 讓您選取 `SKShaderTilerMode` 水準和垂直並排顯示值的視圖。 請注意， `SKShaderTileMode` 一節中定義了成員的陣列 `Resources` ：

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

程式碼後端檔案的函式會在點陣圖資源中載入，以顯示所坐的猴子。 它會先使用的方法裁剪影像， [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) `SKBitmap` 讓前端和腳觸及點陣圖邊緣。 然後，此函式會使用 [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) 方法來建立大小一半的另一個點陣圖。 這些變更讓點陣圖更適合用於並排：

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

`PaintSurface`處理常式會 `SKShaderTileMode` 從兩個視圖取得設定 `Picker` ，並 `SKShader` 根據點陣圖和這兩個值建立物件。 此著色器用來填滿畫布：

[![點陣圖磚翻轉模式](bitmap-tiling-images/BitmapTileFlipModes.png "點陣圖磚翻轉模式")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

左側的 iOS 畫面會顯示預設值的效果 `SKShaderTileMode.Clamp` 。 點陣圖位於左上角。 在點陣圖下方，最下方的圖元會一直重複。 在點陣圖右邊，最右邊的圖元會重複出現在所有的上方。 畫布的其餘部分會以點陣圖右下角的深棕色圖元為色彩。 您應該很明顯地， `Clamp` 選項幾乎不會與點陣圖平鋪搭配使用！

中央的 Android 螢幕會顯示 `SKShaderTileMode.Repeat` 兩個引數的結果。 磚會以水準和垂直方式重複。 通用 Windows 平臺畫面會顯示 `SKShaderTileMode.Mirror` 。 磚會重複，但會交替水準和垂直翻轉。 此選項的優點是磚之間沒有任何不連續。

請記住，您可以針對水準和垂直重複使用不同的選項。 您可以指定 `SKShaderTileMode.Mirror` 做為第二個引數， `CreateBitmap` 但 `SKShaderTileMode.Repeat` 做為第三個引數。 在每個資料列上，猴仔仍會在一般影像和鏡像影像之間保持替代，但不會有任何猴仔是倒置的。

## <a name="patterned-backgrounds"></a>具有圖案的背景

點陣圖圖格通常是用來從相對較小的點陣圖建立已建立圖案的背景。 典型的範例是基礎構件牆。

演算法基礎構件 **牆** 頁面會建立類似整個基礎構件的小型點陣圖，以及以實體分隔的兩個基礎構件。 由於這個基礎構件也會在下一個範例中使用，因此它是由靜態的函式所建立，並使用靜態屬性進行公用：

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

結果點陣圖為70圖元寬和60圖元高：

![演算法基礎構件牆磚](bitmap-tiling-images/AlgorithmicBrickWallTile.png "演算法基礎構件牆磚")

演算法基礎構件 **牆** 頁面的其餘部分會建立一個 `SKShader` 物件，此物件會以水準和垂直方式重複此影像：

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

結果如下︰

[![演算法基礎構件牆](bitmap-tiling-images/AlgorithmicBrickWall.png "演算法基礎構件牆")](bitmap-tiling-images/AlgorithmicBrickWall-Large.png#lightbox)

您可能會希望有一些更實際的東西。 在這種情況下，您可以拍攝實際的基礎構件牆的相片，然後將其裁剪。 此點陣圖為300圖元寬和150圖元高：

![基礎構件牆磚](bitmap-tiling-images/BrickWallTile.jpg "基礎構件牆磚")

此點陣圖用於影像基礎構件 **牆** 頁面：

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

請注意，的 `SKShaderTileMode` 引數都 `CreateBitmap` 是 `Mirror` 。 當您使用從真實世界影像建立的磚時，通常需要此選項。 鏡像磚可避免不連續：

[![影像磚牆](bitmap-tiling-images/PhotographicBrickWall.png "影像磚牆")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

需要一些工作才能取得磚的適當點陣圖。 因為較深的基礎構件太多，所以這種情況並不適合。 它會定期出現在重複的影像中，並顯示這個基礎構件牆是從較小的點陣圖所建立的事實。

[**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的 **Media** 資料夾也包含此石頭牆的影像：

![石頭牆磚](bitmap-tiling-images/StoneWallTile.jpg "石頭牆磚")

不過，原始點陣圖對磚而言有點大。 它可以調整大小，但 `SKShader.CreateBitmap` 方法也可以套用轉換來調整磚的大小。 此選項會在 [ **石頭牆** ] 頁面中示範：

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

`SKMatrix`系統會建立一個值，以將影像調整成原始大小的一半：

[![石頭牆](bitmap-tiling-images/StoneWall.png "石頭牆")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

轉換是否會在此方法中使用的原始點陣圖上運作 `CreateBitmap` ？ 還是會轉換磚的結果陣列？ 

解決此問題的簡單方法是在轉換過程中包含旋轉：

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

如果將轉換套用至個別磚，則應該旋轉磚的每個重複影像，結果會包含許多不連續。 但是，此螢幕擷取畫面顯然會轉換磚的複合陣列：

[![石頭旋轉](bitmap-tiling-images/StoneWallRotated.png "石頭旋轉")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

在圖格 [**對齊**](#tile-alignment)的區段中，您會看到套用至著色器的轉譯轉換範例。

獨立的 [**貓時鐘**](/samples/xamarin/xamarin-forms-samples/catclock) 範例 (不是 **SkiaSharpFormsDemos** ) 的一部分，會根據這個240圖元的正方形點陣圖，使用點陣圖平鋪來模擬木材的背景：

![木頭顆粒](bitmap-tiling-images/WoodGrain.png "木頭顆粒")

這是木材的照片。 此 `SKShaderTileMode.Mirror` 選項可讓它顯示為更大的木頭區域：

[![貓時鐘](bitmap-tiling-images/CatClock.png "貓時鐘")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>磚對齊

到目前為止所顯示的所有範例都使用所建立的著色器 `SKShader.CreateBitmap` 來涵蓋整個畫布。 在大部分的情況下，您會使用點陣圖平鋪來歸檔較小的區域，或 (更少的) 來填滿粗線的內部。 以下是用於較小矩形的影像磚-牆磚：

[![磚對齊](bitmap-tiling-images/TileAlignment.png "磚對齊")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

這可能會對您很有可能，也可能不會顯示。 您可能會被干擾磚模式的開頭不是矩形左上角的完整基礎構件。 這是因為著色器會與畫布對齊，而不是它們所裝飾的繪圖物件。

修正很簡單。 `SKMatrix`根據轉譯轉換建立值。 轉換可有效地將並排顯示的模式移至您想要對齊磚左上角的點。 這種方法會在 [ **圖格對齊** ] 頁面中示範，此頁面會建立上面所示未配置圖格的影像：

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

[ **圖格對齊** ] 頁面包含 `TapGestureRecognizer` 。 點擊或按一下畫面，程式就會切換至 `SKShader.CreateBitmap` 具有引數的方法 `SKMatrix` 。 這種轉換會轉移模式，讓左上角包含完整的基礎構件：

[![並排顯示對齊](bitmap-tiling-images/TileAlignmentTapped.png "並排顯示對齊")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

您也可以使用這項技術，確保並排顯示的點陣圖模式會置中繪製的區域內。 在 **中央磚** 頁面中， `PaintSurface` 處理常式會先計算座標，就好像它會在畫布的中心顯示單一點陣圖一樣。 然後，它會使用這些座標來建立的轉譯轉換 `SKShader.CreateBitmap` 。 這項轉換會移動整個模式，讓磚成為中心：

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

`PaintSurface`處理常式會藉由在畫布中央繪製圓形來結束。 確定有足夠的磚，其中一個圖格正好在圓形的中央，而其他圖格是以對稱模式排列：

[![中央磚](bitmap-tiling-images/CenteredTiles.png "中央磚")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

另一個集中的方法其實有點簡單。 與其建立將磚放在中央的轉譯轉換，您可以將磚化模式的角落置中。 在 `SKMatrix.MakeTranslation` 呼叫中，使用畫布中央的引數：

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

模式仍置中且對稱，但中間沒有磚：

[![中間磚替代](bitmap-tiling-images/CenteredTilesAlternate.png "中間磚替代")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>透過旋轉簡化

有時使用方法中的旋轉轉換 `SKShader.CreateBitmap` 可以簡化點陣圖磚。 當您嘗試定義連鎖鏈接範圍的磚時，這會變得很明顯。 **ChainLinkTile.cs** 檔案會建立如下所示的圖格 (加上粉紅色的背景，以利清楚) ：

![硬鏈-連結磚](bitmap-tiling-images/HardChainLinkTile.png "硬鏈-連結磚")

磚必須包含兩個連結，以便程式碼將磚分成四個象限。 左上角和右下角的象限都相同，但它們並不完整。 電線有小凹槽，必須使用右上角和左下象限中的一些額外繪圖來處理。 執行這項工作的檔案是174行長。

您可以更輕鬆地建立此磚：

![更輕鬆的鏈-連結磚](bitmap-tiling-images/EasierChainLinkTile.png "更輕鬆的鏈-連結磚")

如果點陣圖磚著色器旋轉90度，則視覺效果幾乎相同。

用來建立更容易連鎖鏈接圖格的程式碼，是 **鏈連結圖** 格頁面的一部分。 此函式會根據程式執行所在的裝置類型來決定磚大小，然後再呼叫 `CreateChainLinkTile` ，這會使用線條、路徑和漸層著色器在點陣圖上繪製：

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

除了電線之外，磚是透明的，這表示您可以將它顯示在其他東西上。 程式會在其中一個點陣圖資源中載入，並顯示它以填滿畫布，然後在上方繪製著色器：

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

請注意，著色器的旋轉是45度，因此它的方向類似于真實的連鎖鏈接範圍：

[![鏈-連結隔離](bitmap-tiling-images/ChainLinkFence.png "Chain-Link 範圍")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>製作點陣圖磚的動畫

您可以使用動畫顯示矩陣轉換來建立整個點陣圖磚模式的動畫。 也許您想要讓模式水準或垂直移動或兩者移動。 您可以建立以移位座標為基礎的轉譯轉換來完成這項作業。

您也可以在小型點陣圖上進行繪製，或以每秒60次的速率操作點陣圖的圖元位。 該點陣圖接著可以用於並排顯示，而整個並排顯示的模式似乎會以動畫顯示。 

**動畫點陣圖磚** 頁面會示範這種方法。 點陣圖會具現化為 64-圖元正方形的欄位。 此函 `DrawBitmap` 式會呼叫以提供初始外觀。 如果 `angle` 欄位是零 (就是第一次呼叫方法) 時，點陣圖會包含兩行，並以 X 表示。這幾行的長度足以永遠觸及點陣圖邊緣，不論 `angle` 值為何： 

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

`OnAppearing`和覆寫會產生動畫的額外負荷 `OnDisappearing` 。 `OnTimerTick`方法會 `angle` 每隔10秒將值從0度繪製到360度，以在點陣圖中旋轉 X 圖：

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

由於 X 圖的對稱，這與將 `angle` 值從0度旋轉為每2.5 秒90度的方式相同。

`PaintSurface`處理常式會從點陣圖建立著色器，並使用繪製物件來繪製整個畫布的色彩：

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

這些 `SKShaderTileMode.Mirror` 選項可確保每個點陣圖中 x 的 arm 與相鄰點陣圖中的 x 聯結，以建立會比簡單動畫建議更複雜的整體動畫模式：

[![動畫點陣圖磚](bitmap-tiling-images/AnimatedBitmapTile.png "動畫點陣圖磚")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock (範例) ](/samples/xamarin/xamarin-forms-samples/catclock)