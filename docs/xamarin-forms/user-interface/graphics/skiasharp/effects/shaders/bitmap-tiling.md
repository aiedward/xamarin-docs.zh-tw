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
ms.openlocfilehash: 6a28dd20eb8978334365ac217df1241e5288fd28
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137419"
---
# <a name="skiasharp-bitmap-tiling"></a>SkiaSharp 點陣圖並排

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)

如您在前兩篇文章中所見， [`SKShader`](xref:SkiaSharp.SKShader) 類別可以建立線性或圓形漸層。 本文著重于 `SKShader` 使用點陣圖來並排顯示區域的物件。 點陣圖可以水準和垂直方式重複，不論其原始方向或水準和垂直翻轉。 翻轉會避免磚之間的不連續：

![點陣圖並排取樣](bitmap-tiling-images/BitmapTilingSample.png "點陣圖並排取樣")

[`SKShader.CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode))建立此著色器的靜態方法具有 `SKBitmap` 參數和列舉的兩個成員 [`SKShaderTileMode`](xref:SkiaSharp.SKShaderTileMode) ：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy)
```

這兩個參數表示用於水準並排顯示和垂直並排顯示的模式。 這是 `SKShaderTileMode` 也與漸層方法一起使用的相同列舉。

多載 [`CreateBitmap`](xref:SkiaSharp.SKShader.CreateBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKShaderTileMode,SkiaSharp.SKShaderTileMode,SkiaSharp.SKMatrix)) 包含在 `SKMatrix` 磚點陣圖上執行轉換的引數：

```csharp
public static SKShader CreateBitmap (SKBitmap src, SKShaderTileMode tmx, SKShaderTileMode tmy, SKMatrix localMatrix)
```

本文包含幾個範例，說明如何使用此矩陣轉換與磚點陣圖。

## <a name="exploring-the-tile-modes"></a>探索磚模式

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的 [**著色器和其他效果**] 頁面之 [**點陣圖**並排顯示] 區段中的第一個程式會示範這兩個 `SKShaderTileMode` 引數的效果。 **點陣圖磚翻轉模式**XAML 檔案會具現化 `SKCanvasView` ，以及兩個可 `Picker` 讓您選取 `SKShaderTilerMode` 水準和垂直並排顯示值的視圖。 請注意，成員的陣列 `SKShaderTileMode` 定義于 `Resources` 區段中：

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

程式碼後置檔案的函式會載入點陣圖資源，以顯示所坐的猴子。 它會先使用的方法裁剪影像， [`ExtractSubset`](xref:SkiaSharp.SKBitmap.ExtractSubset(SkiaSharp.SKBitmap,SkiaSharp.SKRectI)) `SKBitmap` 讓前端和腳觸及點陣圖的邊緣。 然後，此函式會使用 [`Resize`](xref:SkiaSharp.SKBitmap.Resize(SkiaSharp.SKImageInfo,SkiaSharp.SKBitmapResizeMethod)) 方法來建立大小為一半的另一個點陣圖。 這些變更讓點陣圖更適合用於並排：

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

`PaintSurface`處理常式會 `SKShaderTileMode` 從兩個 views 取得設定 `Picker` ，並根據 `SKShader` 點陣圖和這兩個值建立物件。 此著色器是用來填滿畫布：

[![點陣圖磚翻轉模式](bitmap-tiling-images/BitmapTileFlipModes.png "點陣圖磚翻轉模式")](bitmap-tiling-images/BitmapTileFlipModes-Large.png#lightbox)

左側的 iOS 畫面會顯示預設值的效果 `SKShaderTileMode.Clamp` 。 點陣圖位於左上角。 在點陣圖底下，圖元的底部資料列會一直向下重複。 在點陣圖的右邊，圖元最右邊的資料行會在之間重複。 畫布的其餘部分會以點陣圖右下角的深棕色圖元彩色。 應該很明顯地，此 `Clamp` 選項幾乎不會搭配點陣圖並排使用！

中央的 Android 螢幕會顯示 `SKShaderTileMode.Repeat` 兩個引數的結果。 此圖格會以水準和垂直方式重複。 [通用 Windows 平臺] 畫面會顯示 `SKShaderTileMode.Mirror` 。 磚會重複，但會以水準和垂直方式翻轉。 此選項的優點是磚之間沒有任何不連續。

請記住，您可以針對水準和垂直重複使用不同的選項。 您可以將指定 `SKShaderTileMode.Mirror` 為的第二個引數， `CreateBitmap` 但 `SKShaderTileMode.Repeat` 做為第三個引數。 在每個資料列上，猴仔仍會在一般映射和鏡像映射之間交替，但沒有任何猴仔會反轉。

## <a name="patterned-backgrounds"></a>已有圖案的背景

點陣圖並排通常是用來從相對較小的點陣圖建立帶圖案的背景。 傳統範例是基礎構件的牆。

[**演算法**基礎構件牆] 頁面會建立一個類似整個基礎構件的小型點陣圖，以及兩個以實體分隔的基礎構件。 因為此基礎構件也會用於下一個範例中，所以它是由靜態的函式所建立，並使用靜態屬性進行公用：

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

結果點陣圖為70圖元寬，60圖元高：

![演算法基礎構件牆磚](bitmap-tiling-images/AlgorithmicBrickWallTile.png "演算法基礎構件牆磚")

[演算法基礎構件**牆**] 頁面的其餘部分 `SKShader` 會建立物件，以水準和垂直方式重複此影像：

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

您可能會偏好一些更實際的東西。 在這種情況下，您可以拍攝實際的基礎構件牆的相片，然後裁剪該磚。 這個點陣圖是300圖元寬，150圖元高：

![基礎構件牆磚](bitmap-tiling-images/BrickWallTile.jpg "基礎構件牆磚")

這個點陣圖用於 [攝影基礎構件**牆**] 頁面：

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

請注意，的 `SKShaderTileMode` 引數 `CreateBitmap` 都是 `Mirror` 。 當您使用從真實世界影像建立的磚時，通常需要此選項。 鏡像磚可避免不連續：

[![攝影基礎構件牆](bitmap-tiling-images/PhotographicBrickWall.png "攝影基礎構件牆")](bitmap-tiling-images/PhotographicBrickWall-Large.png#lightbox)

需要一些工作，才能為磚取得適當的點陣圖。 這種方式並不可行，因為較暗的基礎構件會有太多的表現。 它會定期出現在重複的影像中，以縮小從較小的點陣圖中建造此基礎構件的一面。

[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例的**Media**資料夾也包含此石頭牆的影像：

![石頭牆磚](bitmap-tiling-images/StoneWallTile.jpg "石頭牆磚")

不過，對於磚而言，原始點陣圖會變得太大。 它可以調整大小，但 `SKShader.CreateBitmap` 方法也可以藉由套用轉換來調整磚的大小。 此選項會在 [**石頭牆**] 頁面中示範：

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

`SKMatrix`系統會建立一個值，將影像調整為其原始大小的一半：

[![石頭牆](bitmap-tiling-images/StoneWall.png "石頭牆")](bitmap-tiling-images/StoneWall-Large.png#lightbox)

轉換會在方法中使用的原始點陣圖上運作 `CreateBitmap` 嗎？ 或者，它是否轉換磚的結果陣列？ 

回答這個問題的簡單方法是在轉換過程中包含旋轉：

```csharp
SKMatrix matrix = SKMatrix.MakeScale(0.5f, 0.5f);
SKMatrix.PostConcat(ref matrix, SKMatrix.MakeRotationDegrees(15));
```

如果將轉換套用到個別的磚，則應該旋轉磚的每個重複影像，而結果會包含許多不連續。 但是從這個螢幕擷取畫面看出，磚的複合陣列會被轉換：

[![石頭牆旋轉](bitmap-tiling-images/StoneWallRotated.png "石頭牆旋轉")](bitmap-tiling-images/StoneWallRotated-Large.png#lightbox)

在圖格[**對齊**](#tile-alignment)的區段中，您會看到套用至著色器的轉譯轉換範例。

獨立的[**Cat 時鐘**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)範例（不是**SkiaSharpFormsDemos**的一部分）會根據這個240圖元的正方形點陣圖，使用點陣圖並排來模擬木頭的背景：

![木材顆粒](bitmap-tiling-images/WoodGrain.png "木材顆粒")

這是木材樓層的相片。 `SKShaderTileMode.Mirror`選項可讓它顯示成更大的木材區域：

[![貓時鐘](bitmap-tiling-images/CatClock.png "貓時鐘")](bitmap-tiling-images/CatClock-Large.png#lightbox)

## <a name="tile-alignment"></a>磚對齊

到目前為止所顯示的所有範例，都使用了所建立的著色器 `SKShader.CreateBitmap` 來涵蓋整個畫布。 在大部分情況下，您會使用點陣圖並排來將較小的區域或（較少）用來填滿粗線的內部。 以下是用於較小矩形的影像基礎構件-牆磚：

[![磚對齊](bitmap-tiling-images/TileAlignment.png "磚對齊")](bitmap-tiling-images/TileAlignment-Large.png#lightbox)

這可能會對您很有問題，或也許不會。 也許您已經干擾磚模式不是以矩形左上角的完整基礎構件開頭。 這是因為著色器會與畫布對齊，而不是其所裝飾的繪圖物件。

修正程式很簡單。 `SKMatrix`根據轉譯轉換來建立值。 轉換可有效地將並排顯示的模式移至您要對齊磚左上角的點。 這個方法會在 [**磚對齊**] 頁面中示範，其會建立上面顯示的未對齊磚影像：

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

[**磚對齊**] 頁面包含 `TapGestureRecognizer` 。 請點一下或按一下畫面，程式會切換至 `SKShader.CreateBitmap` 具有 `SKMatrix` 引數的方法。 這種轉換會將模式移位，讓左上角包含完整的基礎構件：

[![並排顯示圖格對齊](bitmap-tiling-images/TileAlignmentTapped.png "並排顯示圖格對齊")](bitmap-tiling-images/TileAlignmentTapped-Large.png#lightbox)

您也可以使用這項技術來確保磚點陣圖模式在繪製的區域內置中。 在**中央磚**頁面中， `PaintSurface` 處理常式會先計算座標，就好像它會在畫布中央顯示單一點陣圖一樣。 然後，它會使用這些座標來建立的轉譯轉換 `SKShader.CreateBitmap` 。 這種轉換會移動整個模式，讓磚的中心置中：

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

`PaintSurface`處理常式會在畫布中央繪製一個圓圈來結束。 請確定有足夠的磚，其中一個圖格剛好位於圓形的中央，而其他則以對稱模式排列：

[![中央磚](bitmap-tiling-images/CenteredTiles.png "中央磚")](bitmap-tiling-images/CenteredTiles-Large.png#lightbox)

另一個中心的方法其實比較簡單。 不是建立將磚放在中央的轉譯轉換，而是置中並排顯示的圖樣。 在 `SKMatrix.MakeTranslation` 呼叫中，使用畫布中央的引數：

```csharp
SKMatrix matrix = SKMatrix.MakeTranslation(info.Rect.MidX, info.Rect.MidY);
```

模式仍會置中且對稱，但中央沒有磚：

[![中間磚替代](bitmap-tiling-images/CenteredTilesAlternate.png "中間磚替代")](bitmap-tiling-images/CenteredTilesAlternate-Large.png#lightbox)

## <a name="simplification-through-rotation"></a>透過旋轉簡化

有時在方法中使用「旋轉」轉換 `SKShader.CreateBitmap` 可以簡化點陣圖磚。 當您嘗試定義連鎖鏈接範圍的磚時，這會變得很明顯。 **ChainLinkTile.cs**檔案會建立如下所示的圖格（有一個粉紅色的背景供您清楚明瞭）：

![硬鏈連結磚](bitmap-tiling-images/HardChainLinkTile.png "硬鏈連結磚")

磚需要包含兩個連結，以便讓程式碼將磚分割成四個象限。 左上角和右下方象限相同，但不完整。 線路的凹槽必須以右上角和左下象限的一些額外繪圖來處理。 執行所有此工作的檔案長度為174行。

建立此磚會變得更容易：

![更輕鬆的連鎖鏈接磚](bitmap-tiling-images/EasierChainLinkTile.png "更輕鬆的連鎖鏈接磚")

如果點陣圖磚著色器旋轉90度，則視覺效果幾乎相同。

建立更簡單的連鎖鏈接磚的程式碼是 [**連鎖鏈接] 磚**頁面的一部分。 此函式會根據程式執行所在的裝置類型來決定磚大小，然後 `CreateChainLinkTile` 使用線條、路徑和漸層著色器呼叫，這會在點陣圖上繪製：

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

除了線路以外，磚是透明的，這表示您可以將它顯示在其他東西上。 程式會載入其中一個點陣圖資源、顯示它以填滿畫布，然後在上方繪製著色器：

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

請注意，著色器會旋轉45度，因此它的方向就像是真正的連鎖鏈接範圍：

[![連鎖鏈接的範圍](bitmap-tiling-images/ChainLinkFence.png "連鎖鏈接的範圍")](bitmap-tiling-images/ChainLinkFence-Large.png#lightbox)

## <a name="animating-bitmap-tiles"></a>製作點陣圖磚的動畫

您可以製作矩陣轉換的動畫，以建立整個點陣圖磚模式的動畫。 也許您想要以水準或垂直方式或兩者來移動模式。 您可以根據變換座標建立轉譯轉換來執行此動作。

您也可以在小型點陣圖上繪製，或以每秒60倍的速率操作點陣圖的圖元位。 然後，該點陣圖可以用於並排顯示，整個並排顯示的圖樣看起來就像是動畫。 

**動畫點陣圖磚**頁面會示範這種方法。 點陣圖會具現化為 64-圖元正方形的欄位。 此函 `DrawBitmap` 式會呼叫，以提供其初始外觀。 如果 `angle` 欄位為零（就如同第一次呼叫方法時），則點陣圖會包含兩個以 X 為單位的行。這幾行的長度夠長，可以隨時到達點陣圖邊緣，而不論其 `angle` 值為何： 

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

動畫的額外負荷會出現在 `OnAppearing` 和 `OnDisappearing` 覆寫中。 `OnTimerTick`方法會 `angle` 每隔10秒將值從0度動畫到360度，以旋轉點陣圖中的 X 圖形：

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

由於 X 圖的對稱性，這與 `angle` 每2.5 秒從0度旋轉到90度的值相同。

`PaintSurface`處理常式會從點陣圖建立著色器，並使用 paint 物件來為整個畫布上色：

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

這些 `SKShaderTileMode.Mirror` 選項可確保每個點陣圖中 x 的臂會與相鄰點陣圖中的 x 聯結，以建立比簡單動畫所建議更複雜的整體動畫模式：

[![動畫點陣圖磚](bitmap-tiling-images/AnimatedBitmapTile.png "動畫點陣圖磚")](bitmap-tiling-images/AnimatedBitmapTile-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [CatClock （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/catclock)
