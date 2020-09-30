---
title: 顯示 SkiaSharp 點陣圖
description: 瞭解如何以圖元大小顯示 SkiaSharp 點陣圖，並展開以填滿矩形，同時保留外觀比例。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1427b6f8461c74ded933fe562a7d17221790383a
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562310"
---
# <a name="displaying-skiasharp-bitmaps"></a>顯示 SkiaSharp 點陣圖

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp 點陣圖的主旨是在 SkiaSharp 的文章 **[點陣圖基本概念中](../basics/bitmaps.md)** 引進。 本文說明載入點陣圖的三種方式，以及用來顯示點陣圖的三種方式。 本文將探討載入點陣圖的技巧，並更深入地使用的 `DrawBitmap` 方法 `SKCanvas` 。

![顯示範例](displaying-images/DisplayingSample.png "顯示範例")

`DrawBitmapLattice`和 `DrawBitmapNinePatch` 方法會在**[分割顯示 SkiaSharp 點陣圖](segmented.md)** 的文章中討論。

此頁面上的範例是來自 **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式。 從該應用程式的 [首頁] 中，選擇 [ **SkiaSharp 點陣圖**]，然後移至 [ **顯示點陣圖** ] 區段。

## <a name="loading-a-bitmap"></a>載入點陣圖

SkiaSharp 應用程式所使用的點陣圖通常來自下列三種不同來源的其中一個：

- 從網際網路
- 從可執行檔中內嵌的資源
- 從使用者的相片媒體櫃

SkiaSharp 應用程式也可以建立新的點陣圖，然後在其上繪製，或設定點陣圖位演算法。 這些技術會在 **[建立和繪製 SkiaSharp 點陣圖](drawing.md)** 和 **[存取 SkiaSharp 點陣圖圖元](pixel-bits.md)** 的文章中討論。

在載入點陣圖的下列三個程式碼範例中，會假設類別包含類型的欄位 `SKBitmap` ：

```csharp
SKBitmap bitmap;
```

如 SkiaSharp 所述的文章 **[點陣圖基本概念](../basics/bitmaps.md)** ，透過網際網路載入點陣圖的最佳方式就是使用 [`HttpClient`](xref:System.Net.Http.HttpClient) 類別。 類別的單一實例可以定義為欄位：

```csharp
HttpClient httpClient = new HttpClient();
```

使用搭配 `HttpClient` iOS 和 Android 應用程式時，您會想要設定 **[ (TLS) 1.2 的傳輸層安全性](~/cross-platform/app-fundamentals/transport-layer-security.md)** 檔中所述的專案屬性。

使用的程式碼 `HttpClient` 通常牽涉到 `await` 運算子，因此它必須位於 `async` 方法中：

```csharp
try
{
    using (Stream stream = await httpClient.GetStreamAsync("https:// ··· "))
    using (MemoryStream memStream = new MemoryStream())
    {
        await stream.CopyToAsync(memStream);
        memStream.Seek(0, SeekOrigin.Begin);

        bitmap = SKBitmap.Decode(stream);
        ···
    };
}
catch
{
    ···
}
```

請注意， `Stream` 從取得的物件 `GetStreamAsync` 會複製到中 `MemoryStream` 。 `Stream` `HttpClient` 除了非同步方法之外，Android 不允許由主執行緒 from。 

[`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream))會執行許多工作： `Stream` 傳遞給它的物件會參考記憶體區塊，其中一個常見點陣圖檔案格式（通常是 JPEG、PNG 或 GIF）中包含整個點陣圖。 `Decode`方法必須判斷格式，然後將點陣圖檔案解碼成 SkiaSharp 本身的內部點陣圖格式。

在您的程式碼呼叫之後 `SKBitmap.Decode` ，它可能會使 `CanvasView` 處理常式失效，讓 `PaintSurface` 處理常式可以顯示新載入的點陣圖。

載入點陣圖的第二種方式是在個別平臺專案所參考的 .NET Standard 程式庫中加入點陣圖作為內嵌資源。 資源識別碼會傳遞給 [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) 方法。 此資源識別碼包含以句號分隔之資源的元件名稱、資料夾名稱和檔案名：

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

點陣圖檔案也可以儲存為 iOS、Android 和通用 Windows 平臺 (UWP) 的個別平臺專案中的資源。 不過，載入這些點陣圖需要位於平臺專案中的程式碼。

取得點陣圖的第三種方法是從使用者的圖片庫。 下列程式碼會使用 **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中所包含的相依性服務。 **SkiaSharpFormsDemo** .NET Standard 程式庫包含 `IPhotoLibrary` 介面，而每個平臺專案都包含可執行 `PhotoLibrary` 該介面的類別。

```csharp
IPhotoicturePicker picturePicker = DependencyService.Get<IPhotoLibrary>();

using (Stream stream = await picturePicker.GetImageStreamAsync())
{
    if (stream != null)
    {
        bitmap = SKBitmap.Decode(stream);
        ···
    }
}
```

一般而言，這類程式碼也 `CanvasView` `PaintSurface` 會使處理常式無法顯示新的點陣圖。

`SKBitmap`類別會定義數個實用的屬性（包括 [`Width`](xref:SkiaSharp.SKBitmap.Width) 和），以 [`Height`](xref:SkiaSharp.SKBitmap.Height) 顯示點陣圖的圖元維度以及許多方法，包括用來建立點陣圖、複製它們，以及公開圖元位的方法。 

## <a name="displaying-in-pixel-dimensions"></a>以圖元維度顯示

SkiaSharp [`Canvas`](xref:SkiaSharp.SKCanvas) 類別會定義四個 `DrawBitmap` 方法。 這些方法可讓您以兩種截然不同的方式來顯示點陣圖： 

- 將 `SKPoint` 值指定 (或分隔 `x` 值 `y`) 會在其圖元維度中顯示點陣圖。 點陣圖的圖元會直接對應到影片顯示的圖元。
- 指定矩形會讓點陣圖延伸至矩形的大小和形狀。 

您可以使用 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) with `SKPoint` 參數或 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) 個別的 `x` 和 `y` 參數，以圖元維度顯示點陣圖：

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

這兩種方法的功能完全相同。 指定的點表示相對於畫布之點陣圖左上角的位置。 因為行動裝置的圖元解析度很高，所以較小的點陣圖通常會在這些裝置上顯得相當小。

選擇性的 `SKPaint` 參數可讓您使用透明度來顯示點陣圖。 若要這樣做，請建立 `SKPaint` 物件，並將 `Color` 屬性設定為 `SKColor` Alpha 通道小於1的任何值。 例如：

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

以最後一個引數傳遞的0x80 指出50% 的透明度。 您也可以使用其中一個預先定義的色彩來設定 Alpha 色板：

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

不過，色彩本身是不相關的。 當您在呼叫中使用物件時，只會檢查 Alpha 通道 `SKPaint` `DrawBitmap` 。

`SKPaint`當使用 blend 模式或篩選效果顯示點陣圖時，物件也會扮演角色。 這些文章會在文章中示範 [SkiaSharp 組合和 blend 模式](../effects/blend-modes/index.md) ，以及 [SkiaSharp 影像篩選](../effects/image-filters.md)。

**[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 範例程式中的 [**圖元維度**] 頁面會顯示點陣圖資源，其寬度為320圖元寬、240圖元高：

```csharp
public class PixelDimensionsPage : ContentPage
{
    SKBitmap bitmap;

    public PixelDimensionsPage()
    {
        Title = "Pixel Dimensions";

        // Load the bitmap from a resource
        string resourceID = "SkiaSharpFormsDemos.Media.Banana.jpg";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }

        // Create the SKCanvasView and set the PaintSurface handler
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

        float x = (info.Width - bitmap.Width) / 2;
        float y = (info.Height - bitmap.Height) / 2;

        canvas.DrawBitmap(bitmap, x, y);
    }
}
```

`PaintSurface`處理常式會 `x` `y` 根據顯示介面的圖元維度和點陣圖的圖元尺寸來計算和值，以將點陣圖置中：

[![圖元維度](displaying-images/PixelDimensions.png "圖元維度")](displaying-images/PixelDimensions-Large.png#lightbox)

如果應用程式想要在左上角顯示點陣圖，則只會傳遞 (0，0) 的座標。 

## <a name="a-method-for-loading-resource-bitmaps"></a>載入資源點陣圖的方法

許多即將推出的範例都需要載入點陣圖資源。 `BitmapExtensions` **[SkiaSharpFormsDemos](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 方案中的靜態類別包含可協助您的方法：

```csharp
static class BitmapExtensions
{
    public static SKBitmap LoadBitmapResource(Type type, string resourceID)
    {
        Assembly assembly = type.GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            return SKBitmap.Decode(stream);
        }
    }
    ···
}
```

請注意 `Type` 參數。 這可以是 `Type` 與儲存點陣圖資源之元件中任何類型相關聯的物件。

此 `LoadBitmapResource` 方法將用於需要點陣圖資源的所有後續範例中。

## <a name="stretching-to-fill-a-rectangle"></a>延展以填滿矩形

`SKCanvas`類別也會定義將 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 點陣圖轉譯成矩形的方法，以及將點陣圖的 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 矩形子集轉譯為矩形的另一個方法：

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

在這兩種情況下，點陣圖都會伸展以填滿名為的矩形 `dest` 。 在第二個方法中， `source` 矩形可讓您選取點陣圖的子集。 `dest`矩形相對於輸出裝置; `source` 矩形相對於點陣圖。

**填滿矩形**頁面會示範這兩個方法中的第一個，方法是在與畫布相同大小的矩形中顯示先前範例中所使用的相同點陣圖： 

```csharp
public class FillRectanglePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public FillRectanglePage ()
    {
        Title = "Fill Rectangle";

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

請注意，使用新的 `BitmapExtensions.LoadBitmapResource` 方法來設定 `SKBitmap` 欄位。 目的地矩形是從的屬性取得 [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) `SKImageInfo` ，描述顯示介面的大小：

[![填滿矩形](displaying-images/FillRectangle.png "填滿矩形")](displaying-images/FillRectangle-Large.png#lightbox)

這通常 _不_ 是您想要的。 影像會因水準和垂直方向的伸展不同而失真。 在圖元大小以外的地方顯示點陣圖時，通常您會想要保留點陣圖的原始外觀比例。

## <a name="stretching-while-preserving-the-aspect-ratio"></a>在保留外觀比例時延展

延伸點陣圖，同時保留外觀比例是一種進程，也稱為 _一致調整_。 這一期建議演算法方法。 [ **統一調整** ] 頁面中會顯示一個可能的解決方案：

```csharp
public class UniformScalingPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(UniformScalingPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public UniformScalingPage()
    {
        Title = "Uniform Scaling";

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

        float scale = Math.Min((float)info.Width / bitmap.Width, 
                               (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect destRect = new SKRect(x, y, x + scale * bitmap.Width, 
                                           y + scale * bitmap.Height);

        canvas.DrawBitmap(bitmap, destRect);
    }
}
```

`PaintSurface`處理常式會計算 `scale` 最小值為點陣圖寬度和高度之顯示寬度和高度比例的係數。 `x` `y` 然後可以計算和值，以便在顯示寬度和高度中將縮放點陣圖置中。 目的地矩形的左上角和 `x` `y` 這些值的右下角加上點陣圖的縮放寬度和高度：

[![一致調整](displaying-images/UniformScaling.png "一致調整")](displaying-images/UniformScaling-Large.png#lightbox)

將電話側邊旋轉以查看延伸至該區域的點陣圖：

[![統一調整橫向](displaying-images/UniformScaling-Landscape.png "統一調整橫向")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

`scale`當您想要執行稍微不同的演算法時，使用這項因素的優點會變得很明顯。 假設您想要保留點陣圖的外觀比例，但也填滿目的地矩形。 唯一可行的方法是裁剪影像的一部分，但是您可以藉由變更 `Math.Min` 上述程式碼中的來執行該演算法 `Math.Max` 。 結果如下︰ 

[![一致的調整替代方案](displaying-images/UniformScaling-Alternative.png "一致的調整替代方案")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

點陣圖的外觀比例會保持不變，但點陣圖左邊和右邊的區域會被裁剪。

## <a name="a-versatile-bitmap-display-function"></a>多功能點陣圖顯示函數

以 XAML 為基礎的程式設計環境 (例如 UWP 和 Xamarin.Forms) 有一個可展開或縮小點陣圖大小的功能，同時保留其外觀比例。 雖然 SkiaSharp 不包含這項功能，但您可以自行執行。 `BitmapExtensions` [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式中包含的類別會顯示如何。 類別會定義兩個 `DrawBitmap` 執行外觀比例計算的新方法。 這些新的方法是的擴充方法 `SKCanvas` 。

新 `DrawBitmap` 方法包含類型的參數，也就是 `BitmapStretch` **BitmapExtensions.cs** 檔中定義的列舉：

```csharp
public enum BitmapStretch
{
    None,
    Fill,
    Uniform,
    UniformToFill,
    AspectFit = Uniform,
    AspectFill = UniformToFill
}
```

`None`、、 `Fill` `Uniform` 和成員與 `UniformToFill` UWP 列舉中的成員相同 [`Stretch`](/uwp/api/Windows.UI.Xaml.Media.Stretch) 。 類似的 Xamarin.Forms [`Aspect`](xref:Xamarin.Forms.Aspect) 列舉定義了成員 `Fill` 、 `AspectFit` 和 `AspectFill` 。

上面所示的 **統一縮放** 頁面會將點陣圖放在矩形內，但您可能會想要其他選項，例如將點陣圖放置在矩形的左邊或右邊，或是頂端或底部。 這就是列舉的用途 `BitmapAlignment` ：

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

搭配使用時，對齊設定沒有任何作用 `BitmapStretch.Fill` 。

第一個延伸模組函式 `DrawBitmap` 包含目的地矩形，但沒有來源矩形。 已定義預設值，因此，如果您想要讓點陣圖置中，您只需要指定 `BitmapStretch` 成員：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect dest, 
                                  BitmapStretch stretch, 
                                  BitmapAlignment horizontal = BitmapAlignment.Center, 
                                  BitmapAlignment vertical = BitmapAlignment.Center, 
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / bitmap.Width, dest.Height / bitmap.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * bitmap.Width, scale * bitmap.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, display, paint);
        }
    }
    ···
}
```

這個方法的主要目的是要計算一個名為的縮放因數 `scale` ，然後在呼叫方法時，套用至點陣圖寬度和高度 `CalculateDisplayRect` 。 這是根據水準和垂直對齊來計算顯示點陣圖之矩形的方法：

```csharp
static class BitmapExtensions
{
    ···
    static SKRect CalculateDisplayRect(SKRect dest, float bmpWidth, float bmpHeight, 
                                       BitmapAlignment horizontal, BitmapAlignment vertical)
    {
        float x = 0;
        float y = 0;

        switch (horizontal)
        {
            case BitmapAlignment.Center:
                x = (dest.Width - bmpWidth) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                x = dest.Width - bmpWidth;
                break;
        }

        switch (vertical)
        {
            case BitmapAlignment.Center:
                y = (dest.Height - bmpHeight) / 2;
                break;

            case BitmapAlignment.Start:
                break;

            case BitmapAlignment.End:
                y = dest.Height - bmpHeight;
                break;
        }

        x += dest.Left;
        y += dest.Top;

        return new SKRect(x, y, x + bmpWidth, y + bmpHeight);
    }
}
```

`BitmapExtensions`類別包含一個 `DrawBitmap` 具有來源矩形的額外方法，以指定點陣圖的子集。 這個方法與第一個方法類似，不同之處在于縮放比例是根據矩形計算 `source` ，然後套用至 `source` 呼叫中的矩形 `CalculateDisplayRect` ：

```csharp
static class BitmapExtensions
{
    ···
    public static void DrawBitmap(this SKCanvas canvas, SKBitmap bitmap, SKRect source, SKRect dest,
                                  BitmapStretch stretch,
                                  BitmapAlignment horizontal = BitmapAlignment.Center,
                                  BitmapAlignment vertical = BitmapAlignment.Center,
                                  SKPaint paint = null)
    {
        if (stretch == BitmapStretch.Fill)
        {
            canvas.DrawBitmap(bitmap, source, dest, paint);
        }
        else
        {
            float scale = 1;

            switch (stretch)
            {
                case BitmapStretch.None:
                    break;

                case BitmapStretch.Uniform:
                    scale = Math.Min(dest.Width / source.Width, dest.Height / source.Height);
                    break;

                case BitmapStretch.UniformToFill:
                    scale = Math.Max(dest.Width / source.Width, dest.Height / source.Height);
                    break;
            }

            SKRect display = CalculateDisplayRect(dest, scale * source.Width, scale * source.Height, 
                                                  horizontal, vertical);

            canvas.DrawBitmap(bitmap, source, display, paint);
        }
    }
    ···
}
```

這兩個新方法的第一個 `DrawBitmap` 會在 [ **調整模式** ] 頁面中示範。 XAML 檔案包含三個 `Picker` 元素，可讓您選取和列舉的成員 `BitmapStretch` `BitmapAlignment` ：

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:SkiaSharpFormsDemos"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.ScalingModesPage"
             Title="Scaling Modes">

    <Grid Padding="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="Auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <skia:SKCanvasView x:Name="canvasView" 
                           Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Label Text="Stretch:"
               Grid.Row="1" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="stretchPicker"
                Grid.Row="1" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapStretch}">
                    <x:Static Member="local:BitmapStretch.None" />
                    <x:Static Member="local:BitmapStretch.Fill" />
                    <x:Static Member="local:BitmapStretch.Uniform" />
                    <x:Static Member="local:BitmapStretch.UniformToFill" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Horizontal Alignment:"
               Grid.Row="2" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="horizontalPicker" 
                Grid.Row="2" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>

        <Label Text="Vertical Alignment:"
               Grid.Row="3" Grid.Column="0"
               VerticalOptions="Center" />

        <Picker x:Name="verticalPicker" 
                Grid.Row="3" Grid.Column="1"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type local:BitmapAlignment}">
                    <x:Static Member="local:BitmapAlignment.Start" />
                    <x:Static Member="local:BitmapAlignment.Center" />
                    <x:Static Member="local:BitmapAlignment.End" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                0
            </Picker.SelectedIndex>
        </Picker>
    </Grid>
</ContentPage>
```

`CanvasView`當任何專案變更時，程式碼後端檔案就會失效 `Picker` 。 `PaintSurface`處理常式會存取用 `Picker` 來呼叫擴充方法的三個視圖 `DrawBitmap` ：

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public ScalingModesPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, dest, stretch, horizontal, vertical);
    }
}
```

以下是一些選項的組合：

[![調整模式](displaying-images/ScalingModes.png "調整模式")](displaying-images/ScalingModes-Large.png#lightbox)

**矩形子集**頁面的 XAML 檔案幾乎與**縮放模式**相同，但程式碼後端檔案會定義欄位所指定點陣圖的矩形子集 `SOURCE` ： 

```csharp
public partial class ScalingModesPage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(ScalingModesPage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");

    static readonly SKRect SOURCE = new SKRect(94, 12, 212, 118);

    public RectangleSubsetPage()
    {
        InitializeComponent();
    }

    private void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        SKRect dest = new SKRect(0, 0, info.Width, info.Height);

        BitmapStretch stretch = (BitmapStretch)stretchPicker.SelectedItem;
        BitmapAlignment horizontal = (BitmapAlignment)horizontalPicker.SelectedItem;
        BitmapAlignment vertical = (BitmapAlignment)verticalPicker.SelectedItem;

        canvas.DrawBitmap(bitmap, SOURCE, dest, stretch, horizontal, vertical);
    }
}
```

這個矩形來源會隔離猴子的標頭，如下列螢幕擷取畫面所示：

[![矩形子集](displaying-images/RectangleSubset.png "矩形子集")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)