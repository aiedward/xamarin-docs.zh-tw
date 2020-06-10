---
title： "顯示 SkiaSharp 點陣圖" 描述： "瞭解如何以圖元大小顯示 SkiaSharp 點陣圖，並展開以填滿矩形，同時保留外觀比例。"
skiasharp 的 assetid： 8E074F8D-4715-4146-8CC0-FD7A8290EDE9 author： davidbritch ms-chap： dabritch ms. date： 07/17/2018 no-loc： [ Xamarin.Forms ，]，-. Xamarin.Essentials
---

# <a name="displaying-skiasharp-bitmaps"></a>顯示 SkiaSharp 點陣圖

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

SkiaSharp 點陣圖的主旨是在**[SkiaSharp 的點陣圖基本概念](../basics/bitmaps.md)** 一文中引進。 該文章已說明載入點陣圖的三種方式，以及三種顯示點陣圖的方式。 本文將探討載入點陣圖並更深入使用之方法的技巧 `DrawBitmap` `SKCanvas` 。

![顯示範例](displaying-images/DisplayingSample.png "顯示範例")

`DrawBitmapLattice`和 `DrawBitmapNinePatch` 方法會在**[分段顯示的 SkiaSharp 點陣圖](segmented.md)** 文章中討論。

此頁面上的範例來自**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式。 從該應用程式的首頁中，選擇 [SkiaSharp] [**點陣圖**]，然後移至 [**顯示點陣圖**] 區段。

## <a name="loading-a-bitmap"></a>載入點陣圖

SkiaSharp 應用程式所使用的點陣圖通常來自三個不同來源的其中一個：

- 透過網際網路的
- 從可執行檔中內嵌的資源
- 從使用者的相片媒體櫃

SkiaSharp 應用程式也可以建立新的點陣圖，然後在其上繪製或設定點陣圖位以演算法方式。 這些技術會在**[建立和繪製 SkiaSharp 點陣圖](drawing.md)** 和**[存取 SkiaSharp 點陣圖圖元](pixel-bits.md)** 文章中討論。

在下列三個載入點陣圖的程式碼範例中，類別會假設為包含類型的欄位 `SKBitmap` ：

```csharp
SKBitmap bitmap;
```

如 SkiaSharp 中所述的**[點陣圖基本概念](../basics/bitmaps.md)** 一文，透過網際網路載入點陣圖的最佳方式是使用 [`HttpClient`](xref:System.Net.Http.HttpClient) 類別。 類別的單一實例可以定義為欄位：

```csharp
HttpClient httpClient = new HttpClient();
```

使用搭配 `HttpClient` iOS 和 Android 應用程式時，您會想要設定專案屬性，如**[傳輸層安全性（TLS） 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** 檔中所述。

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

請注意， `Stream` 從取得的物件 `GetStreamAsync` 會複製到 `MemoryStream` 。 `Stream` `HttpClient` 除了非同步方法以外，Android 不允許由主要執行緒 from。 

[`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream))會執行很多工作： `Stream` 傳遞給它的物件會以其中一種通用點陣圖檔案格式（通常是 JPEG、PNG 或 GIF）來參考包含整個點陣圖的記憶體區塊。 `Decode`方法必須判斷格式，然後將點陣圖檔案解碼成 SkiaSharp 本身的內部點陣圖格式。

在您的程式碼呼叫之後 `SKBitmap.Decode` ，它可能會使的失效， `CanvasView` 讓 `PaintSurface` 處理常式能夠顯示新載入的點陣圖。

第二種載入點陣圖的方式是在個別平臺專案所參考的 .NET Standard 程式庫中，將點陣圖納入為內嵌資源。 資源識別碼會傳遞至 [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) 方法。 此資源識別碼包含以句點分隔之資源的元件名稱、資料夾名稱和檔案名：

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

點陣圖檔案也可以儲存為 iOS、Android 和通用 Windows 平臺（UWP）之個別平臺專案中的資源。 不過，載入這些點陣圖需要位於平臺專案中的程式碼。

取得點陣圖的第三種方法是從使用者的圖片庫。 下列程式碼會使用包含在**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 應用程式中的相依性服務。 **SkiaSharpFormsDemo** .NET Standard 程式庫包括 `IPhotoLibrary` 介面，而每個平臺專案都包含一個可實作為 `PhotoLibrary` 該介面的類別。

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

一般來說，這類程式碼也會使其失效， `CanvasView` 讓 `PaintSurface` 處理常式可以顯示新的點陣圖。

`SKBitmap`類別會定義數個有用的屬性（包括 [`Width`](xref:SkiaSharp.SKBitmap.Width) 和），以 [`Height`](xref:SkiaSharp.SKBitmap.Height) 顯示點陣圖的圖元維度，以及許多方法，包括建立點陣圖、複製它們，以及公開圖元位的方法。 

## <a name="displaying-in-pixel-dimensions"></a>以圖元維度顯示

SkiaSharp [`Canvas`](xref:SkiaSharp.SKCanvas) 類別會定義四個 `DrawBitmap` 方法。 這些方法可讓您以兩種不同的方式來顯示點陣圖： 

- 指定 `SKPoint` 值（或個別的 `x` 和 `y` 值）會在其圖元維度中顯示點陣圖。 點陣圖的圖元會直接對應到影片顯示的圖元。
- 指定矩形會使點陣圖延伸至矩形的大小和形狀。 

您可以使用搭配 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKPoint,SkiaSharp.SKPaint)) `SKPoint` 參數或 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) 個別的 `x` 和 `y` 參數，在其圖元維度中顯示點陣圖：

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

這兩種方法的功能相同。 指定的點表示點陣圖左上角相對於畫布的位置。 因為行動裝置的圖元解析度很高，所以較小的點陣圖在這些裝置上通常會顯得很小。

選擇性 `SKPaint` 參數可讓您使用透明度來顯示點陣圖。 若要這麼做，請建立 `SKPaint` 物件，並將 `Color` 屬性設定為任何小於1的 Alpha 色板的 `SKColor` 值。 例如：

```csharp
paint.Color = new SKColor(0, 0, 0, 0x80);
```

當做最後一個引數傳遞的0x80 表示50% 的透明度。 您也可以在其中一個預先定義的色彩上設定 Alpha 色板：

```csharp
paint.Color = SKColors.Red.WithAlpha(0x80);
```

不過，色彩本身並不相關。 當您在呼叫中使用物件時，只會檢查 Alpha 色板 `SKPaint` `DrawBitmap` 。

`SKPaint`當使用 blend 模式或篩選效果來顯示點陣圖時，物件也會扮演角色。 這些專案會在[SkiaSharp 複合和 blend 模式](../effects/blend-modes/index.md)和[SkiaSharp 影像篩選](../effects/image-filters.md)文章中示範。

**[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 範例程式中的 [**圖元維度**] 頁面會顯示320圖元寬乘以240圖元高的點陣圖資源：

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

`PaintSurface`處理常式會根據 `x` `y` 顯示介面的圖元尺寸和點陣圖的圖元尺寸來計算和值，以將點陣圖置中。

[![圖元維度](displaying-images/PixelDimensions.png "圖元維度")](displaying-images/PixelDimensions-Large.png#lightbox)

如果應用程式想要在其左上角顯示點陣圖，則只會傳遞（0，0）的座標。 

## <a name="a-method-for-loading-resource-bitmaps"></a>載入資源點陣圖的方法

即將推出的許多範例都需要載入點陣圖資源。 `BitmapExtensions` **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** 方案中的靜態類別包含可協助您的方法：

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

這個 `LoadBitmapResource` 方法會用於所有需要點陣圖資源的後續範例中。

## <a name="stretching-to-fill-a-rectangle"></a>延展以填滿矩形

`SKCanvas`類別也會定義將 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 點陣圖轉譯成矩形的方法，以及將點陣圖的 [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) 矩形子集轉譯成矩形的另一個方法：

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

在這兩種情況下，會延展點陣圖以填滿名為的矩形 `dest` 。 在第二個方法中， `source` 矩形可讓您選取點陣圖的子集。 `dest`矩形相對於輸出裝置; `source` 矩形相對於點陣圖。

[**填滿矩形**] 頁面會示範這兩種方法的第一個，其方式是在矩形與畫布相同的大小中顯示先前範例中使用的相同點陣圖： 

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

請注意，使用新的 `BitmapExtensions.LoadBitmapResource` 方法來設定 `SKBitmap` 欄位。 目的地矩形是從的屬性取得 [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) `SKImageInfo` ，它會描述顯示介面的大小：

[![填滿矩形](displaying-images/FillRectangle.png "填滿矩形")](displaying-images/FillRectangle-Large.png#lightbox)

這通常_不_是您想要的。 影像會在水準和垂直方向以不同的方式伸展而扭曲。 當以圖元大小以外的地方顯示點陣圖時，通常您會想要保留點陣圖的原始外觀比例。

## <a name="stretching-while-preserving-the-aspect-ratio"></a>在保留外觀比例時延展

延伸點陣圖，同時保留外觀比例是一種程式，也就是所謂的_統一調整_。 這一詞會建議使用演算法方法。 [**統一調整**] 頁面中會顯示一個可能的解決方案：

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

`PaintSurface`處理常式會計算一個 `scale` 因數，這是顯示寬度和高度與點陣圖寬度和高度的比例下限。 `x` `y` 然後可以計算和值，以在顯示寬度和高度內將縮放點陣圖置中。 [目的地] 矩形的左上角和 `x` `y` [值] 的右下角，再加上點陣圖的縮放寬度和高度：

[![統一調整](displaying-images/UniformScaling.png "統一調整")](displaying-images/UniformScaling-Large.png#lightbox)

將電話旋轉一側，以查看延展到該區域的點陣圖：

[![統一調整橫向](displaying-images/UniformScaling-Landscape.png "統一調整橫向")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

`scale`當您想要執行稍微不同的演算法時，使用此因素的優點會變得很明顯。 假設您想要保留點陣圖的外觀比例，但也要填滿目的地矩形。 唯一可行的方式是裁剪影像的一部分，但是您可以藉由變更 `Math.Min` 上述程式碼中的來實作為演算法 `Math.Max` 。 結果如下︰ 

[![統一調整替代方案](displaying-images/UniformScaling-Alternative.png "統一調整替代方案")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

點陣圖的外觀比例會保留，但是點陣圖左邊和右邊的區域會被裁剪。

## <a name="a-versatile-bitmap-display-function"></a>多功能點陣圖顯示函式

以 XAML 為基礎的程式設計環境（例如 UWP 和 Xamarin.Forms ）有一項功能可以擴充或縮小點陣圖大小，同時保留其外觀比例。 雖然 SkiaSharp 不包含這項功能，但您可以自行執行。 `BitmapExtensions` [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)應用程式中包含的類別會顯示如何。 類別會定義兩個 `DrawBitmap` 執行外觀比例計算的新方法。 這些新方法是的擴充方法 `SKCanvas` 。

新的 `DrawBitmap` 方法包括類型的參數，也就 `BitmapStretch` 是在**BitmapExtensions.cs**檔案中定義的列舉：

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

`None`、 `Fill` 、 `Uniform` 和成員與 `UniformToFill` UWP 列舉中的相同 [`Stretch`](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx) 。 類似的 Xamarin.Forms [`Aspect`](xref:Xamarin.Forms.Aspect) 列舉會定義成員 `Fill` 、 `AspectFit` 和 `AspectFill` 。

如上所示的**統一調整**頁面會將點陣圖放在矩形內，但您可能會想要其他選項，例如將點陣圖放在矩形的左側或右側，或頂端或底端。 這就是列舉的用途 `BitmapAlignment` ：

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

與搭配使用時，對齊設定不會有任何作用 `BitmapStretch.Fill` 。

第一個 `DrawBitmap` 擴充功能函式包含目的地矩形，但沒有來源矩形。 定義預設值，如此一來，如果您想要將點陣圖置中，就只需要指定一個 `BitmapStretch` 成員：

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

這個方法的主要目的是要計算名為的縮放因數 `scale` ，然後在呼叫方法時，套用至點陣圖寬度和高度 `CalculateDisplayRect` 。 這是根據水準和垂直對齊來計算顯示點陣圖之矩形的方法：

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

`BitmapExtensions`類別包含 `DrawBitmap` 具有來源矩形的其他方法，用於指定點陣圖的子集。 這個方法類似于第一個方法，不同之處在于縮放因數是根據矩形計算 `source` ，然後套用至 `source` 呼叫中的矩形 `CalculateDisplayRect` ：

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

這兩個新方法的第一個 `DrawBitmap` 會在 [**調整模式**] 頁面中示範。 XAML 檔案包含三個 `Picker` 元素，可讓您選取和列舉的成員 `BitmapStretch` `BitmapAlignment` ：

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

`CanvasView`當任何專案變更時，程式碼後置檔案只會使失效 `Picker` 。 `PaintSurface`處理常式會存取用 `Picker` 來呼叫擴充方法的三個視圖 `DrawBitmap` ：

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

[![縮放模式](displaying-images/ScalingModes.png "縮放模式")](displaying-images/ScalingModes-Large.png#lightbox)

**矩形子集**頁面與**調整模式**幾乎具有相同的 XAML 檔案，但程式碼後置檔案會定義欄位所指定點陣圖的矩形子集 `SOURCE` ： 

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

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
