---
title: 顯示 SkiaSharp 點陣圖
description: 了解如何顯示的 SkiaSharp 點陣圖中像素大小，並展開以填滿矩形，同時保留外觀比例。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 8E074F8D-4715-4146-8CC0-FD7A8290EDE9
author: charlespetzold
ms.author: chape
ms.date: 07/17/2018
ms.openlocfilehash: cbe3166c4edb147f7179f2c719901b382db8ec80
ms.sourcegitcommit: 12d48cdf99f0d916536d562e137d0e840d818fa1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2018
ms.locfileid: "39615310"
---
# <a name="displaying-skiasharp-bitmaps"></a>顯示 SkiaSharp 點陣圖

發行項中引進了 SkiaSharp 點陣圖的主旨 **[SkiaSharp 點陣圖基本概念](../basics/bitmaps.md)**。 該文章會說明三種方式可以載入點陣圖並顯示點陣圖的三種方式。 這篇文章會檢閱載入點陣圖的技術，並將進入更深入`DrawBitmap`方法的`SKCanvas`。

![顯示範例](displaying-images/DisplayingSample.png "顯示範例")

`DrawBitmapLattice`並`DrawBitmapNinePatch`文章中所討論的方法會**[分段顯示 SkiaSharp 點陣圖](segmented.md)**。

在此頁面上的範例是來自**[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式。 從該應用程式的首頁上，選擇**SkiaSharp 點陣圖**，然後移至**顯示點陣圖**一節。

## <a name="loading-a-bitmap"></a>正在載入點陣圖

SkiaSharp 應用程式通常使用的點陣圖來自三個不同來源的其中一個：

- 透過網際網路
- 從資源內嵌在可執行檔
- 從使用者的相片媒體櫃

您也可建立新的點陣圖，然後在其上繪製或演算法來設定點陣圖位元 SkiaSharp 應用程式。 在文章中討論這些技巧**[建立和上 SkiaSharp 點陣圖繪製](drawing.md)** 並**[存取 SkiaSharp 點陣圖像素](pixel-bits.md)**.

在下列三個程式碼範例中的載入點陣圖，類別會假設包含型別的欄位`SKBitmap`:

```csharp
SKBitmap bitmap;
```

為發行項 **[SkiaSharp 點陣圖基本概念](../basics/bitmaps.md)** 所述，若要透過網際網路載入點陣圖，最好是使用[ `HttpClient` ](xref:System.Net.Http.HttpClient)類別。 類別的單一執行個體可以定義為欄位：

```csharp
HttpClient httpClient = new HttpClient();
```

使用時`HttpClient`iOS 和 Android 應用程式，您會想要設定專案屬性中所述的文件上**[傳輸層安全性 (TLS) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)**。

使用程式碼`HttpClient`通常會涉及`await`運算子，因此它必須位於`async`方法：

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

請注意，`Stream`物件將會取自`GetStreamAsync`複製到`MemoryStream`。 Android 不允許`Stream`從`HttpClient`處理由主執行緒中非同步的方法除外。 

[ `SKBitmap.Decode` ](https://developer.xamarin.com/api/member/SkiaSharp.SKBitmap.Decode/p/System.IO.Stream/)會執行許多工作：`Stream`傳遞給它的物件參考包含其中一種常見的點陣圖檔案格式、 通常 JPEG、 PNG 或 GIF 整個點陣圖的記憶體區塊。 `Decode`方法必須決定的格式，並再將點陣圖檔解碼 SkiaSharp 自己內部的點陣圖格式。

在您程式碼會呼叫後`SKBitmap.Decode`，它可能會失效`CanvasView`以便`PaintSurface`處理常式可以顯示新載入的點陣圖。

第二種載入點陣圖是做為內嵌資源，在.NET Standard 程式庫中包含點陣圖的專案所參考個別的平台。 資源識別碼傳遞給[ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String))方法。 此資源識別碼是由組件名稱、 資料夾名稱，並以句號分隔的資源的檔案名稱所組成：

```csharp
string resourceID = "assemblyName.folderName.fileName";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    bitmap = SKBitmap.Decode(stream);
    ···
}
```

點陣圖檔也可以儲存為 iOS、 Android 和通用 Windows 平台 (UWP) 的個別平台專案中的資源。 不過，載入這些點陣圖所需要的平台專案中的程式碼。

取得點陣圖的第三個方法是從使用者的圖片媒體櫃。 下列程式碼會使用相依性服務中包含**[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 應用程式。 **SkiaSharpFormsDemo** .NET Standard 程式庫包含`IPhotoLibrary`介面，而每個平台專案包含`PhotoLibrary`實作該介面的類別。

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

一般而言，這類程式碼也會讓`CanvasView`以便`PaintSurface`處理常式可以顯示新的點陣圖。

`SKBitmap`類別會定義數個實用的屬性，包括[ `Width` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Width/)並[ `Height` ](https://developer.xamarin.com/api/property/SkiaSharp.SKBitmap.Height/)，所顯示的點陣圖，以及許多方法，包括的像素尺寸若要建立點陣圖，複製它們，並公開 （expose） 的像素位元的方法。 

## <a name="displaying-in-pixel-dimensions"></a>顯示像素尺寸

SkiaSharp [ `Canvas` ](https://developer.xamarin.com/api/type/SkiaSharp.SKCanvas/)類別會定義四個`DrawBitmap`方法。 這些方法可以讓兩個完全不同的方式顯示的點陣圖： 

- 指定`SKPoint`值 (或個別`x`和`y`值) 顯示點陣圖的像素尺寸。 點陣圖的像素會直接對應到像素為單位的視訊的顯示。
- 指定的矩形，會導致要自動縮放以的大小和矩形之形狀的點陣圖。 

您可以顯示點陣圖中使用的像素尺寸[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKPoint/SkiaSharp.SKPaint/)具有`SKPoint`參數或[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/System.Single/System.Single/SkiaSharp.SKPaint/)使用不同`x`和`y`參數：

```csharp
DrawBitmap(SKBitmap bitmap, SKPoint pt, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, float x, float y, SKPaint paint = null)
```

這兩種方法會在功能上完全相同的。 指定的點表示相對於畫布點陣圖左上角的位置。 因為行動裝置的像素解析度相當高，所以較小的點陣圖通常會出現在這些裝置上非常小。

選擇性`SKPaint`參數可讓您顯示點陣圖使用混合模式或篩選的效果。 這些將會在之後的文章中示範。

**像素尺寸**頁面**[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 範例程式會顯示為 320 像素寬 240 像素高的點陣圖資源：

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

`PaintSurface`處理常式中心藉由計算的點陣圖`x`和`y`值根據顯示表面的像素尺寸和點陣圖的像素尺寸：

[![像素尺寸](displaying-images/PixelDimensions.png "像素尺寸")](displaying-images/PixelDimensions-Large.png#lightbox)

如果應用程式想要在其左上角顯示的點陣圖，它只會傳送的座標 （0，0）。 

## <a name="a-method-for-loading-resource-bitmaps"></a>正在載入資源點陣圖方法

接下來的範例有許多需要載入點陣圖資源。 靜態`BitmapExtensions`類別內**[SkiaSharpFormsDemos](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)** 方案包含可以助一臂之力的方法：

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

請注意`Type`參數。 這可以是`Type`儲存點陣圖資源的組件中的任何類型相關聯的物件。

這`LoadBitmapResource`方法將會用於所有後續的範例需要點陣圖的資源。

## <a name="stretching-to-fill-a-rectangle"></a>自動縮放以填滿矩形

`SKCanvas`類別也會定義[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKPaint/)呈現到矩形中，而另一個點陣圖方法[ `DrawBitmap` ](https://developer.xamarin.com/api/member/SkiaSharp.SKCanvas.DrawBitmap/p/SkiaSharp.SKBitmap/SkiaSharp.SKRect/SkiaSharp.SKRect/SkiaSharp.SKPaint/)呈現的點陣圖矩形子集方法矩形：

```
DrawBitmap(SKBitmap bitmap, SKRect dest, SKPaint paint = null)

DrawBitmap(SKBitmap bitmap, SKRect source, SKRect dest, SKPaint paint = null)
```

在這兩種情況下，點陣圖會縮放以填滿的矩形，名為`dest`。 在第二個方法中，`source`矩形可讓您選取點陣圖的子集。 `dest`矩形是相對於輸出裝置;`source`矩形是相對於點陣圖。

**填滿矩形**頁面會示範這兩種方法，藉由顯示相同的點陣圖中的第一個範例中使用先前在矩形中相同的大小做為畫布： 

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

請注意，使用新`BitmapExtensions.LoadBitmapResource`方法來設定`SKBitmap`欄位。 目的矩形取自[ `Rect` ](https://developer.xamarin.com/api/property/SkiaSharp.SKImageInfo.Rect/)屬性`SKImageInfo`，此描述顯示介面的大小：

[![填滿矩形](displaying-images/FillRectangle.png "填滿矩形")](displaying-images/FillRectangle-Large.png#lightbox)

這通常是_不_想項目。 映像會扭曲過度使用以不同方式水平和垂直方向。 當顯示點陣圖中像素大小以外的項目，通常是您想要保留點陣圖的原始外觀比例。

## <a name="stretching-while-preserving-the-aspect-ratio"></a>自動縮放，同時維持外觀比例

自動縮放點陣圖，同時維持外觀比例是一個程序也稱為_統一的縮放比例_。 該字詞會建議演算法的方法。 一個可行的解決方案所示**統一的縮放比例**頁面：

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

`PaintSurface`處理常式會計算`scale`而言是最小值的顯示寬度和高度的點陣圖寬度和高度的比率。 `x`和`y`值再將置中縮放的點陣圖內的顯示寬度和高度來計算。 目的地矩形的左上角`x`和`y`和右下角，這些值再加上縮放的寬度和高度的點陣圖：

[![統一的縮放比例](displaying-images/UniformScaling.png "統一的縮放比例")](displaying-images/UniformScaling-Large.png#lightbox)

開啟電話轉向側邊，以查看該區域會自動縮放的點陣圖：

[![統一縮放比例橫向](displaying-images/UniformScaling-Landscape.png "統一的縮放比例的橫印")](displaying-images/UniformScaling-Landscape-Large.png#lightbox)

使用這樣的好處`scale`因素就很明顯，當您想要實作稍有不同的演算法。 假設您想要保留點陣圖的長寬比，但是也填滿目的矩形。 這是可行的唯一方法是透過修剪的映像的一部分，但您可以藉由變更實作該演算法`Math.Min`至`Math.Max`將上述程式碼。 結果如下： 

[![統一的縮放比例替代](displaying-images/UniformScaling-Alternative.png "統一的縮放比例的替代方案")](displaying-images/UniformScaling-Alternative-Large.png#lightbox)

點陣圖的長寬比會保留，但從裁剪區域左側和右側的點陣圖。

## <a name="a-versatile-bitmap-display-function"></a>多用途的點陣圖顯示函式

以 XAML 為基礎 （例如 UWP 和 Xamarin.Forms） 的程式設計環境會有的設施來展開或壓縮點陣圖的大小，同時保留其外觀比例。 雖然 SkiaSharp 不包含這項功能，您可以實作它自己。 `BitmapExtensions`類別中包含[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)應用程式會顯示方式。 此類別會定義兩個新`DrawBitmap`執行外觀比例計算的方法。 這些新的方法是擴充方法的`SKCanvas`。

新`DrawBitmap`方法包含參數的型別`BitmapStretch`中, 定義的列舉型別**BitmapExtensions.cs**檔案：

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

`None`， `Fill`， `Uniform`，以及`UniformToFill`成員是 UWP 相同[ `Stretch` ](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.stretch.aspx)列舉型別。 類似的 Xamarin.Forms [ `Aspect` ](xref:Xamarin.Forms.Aspect)列舉會定義成員`Fill`， `AspectFit`，和`AspectFill`。

**統一的縮放比例**如上所示的頁面中心內的矩形中，點陣圖，但您可能需要其他選項，例如定位的左側或右側的矩形中，或在頂部或底部的點陣圖。 這是目的`BitmapAlignment`列舉型別：

```csharp
public enum BitmapAlignment
{
    Start,
    Center,
    End
}
```

對齊方式設定都與搭配使用時沒有作用`BitmapStretch.Fill`。

第一個`DrawBitmap`擴充程式函式包含目的地矩形，但沒有來源矩形。 預設值所定義，因此如果您想置中對齊的點陣圖，您只需要指定`BitmapStretch`成員：

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

這個方法主要目的是要計算名為的縮放係數`scale`，然後套用至點陣圖的寬度和高度呼叫時`CalculateDisplayRect`方法。 這是計算矩形來顯示點陣圖的水平和垂直對齊方式為基礎的方法：

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

`BitmapExtensions`類別包含額外`DrawBitmap`來源矩形的方法來指定點陣圖的子集。 此方式類似於第一個不同之處在於根據計算縮放比例`source`矩形中，並套用至`source`呼叫中的矩形`CalculateDisplayRect`:

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

這兩個新的第一個`DrawBitmap`方法所示**調整模式**頁面。 XAML 檔案包含三種`Picker`項目，可讓您選取的成員`BitmapStretch`和`BitmapAlignment`列舉型別：

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

程式碼後置檔案只要失效`CanvasView`任何時`Picker`項目已變更。 `PaintSurface`處理常式會存取三`Picker`呼叫的檢視`DrawBitmap`擴充方法：

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

以下是一些選項組合：

[![縮放模式](displaying-images/ScalingModes.png "縮放模式")](displaying-images/ScalingModes-Large.png#lightbox)

**矩形子集**頁面就幾乎具有相同的 XAML 檔案**調整模式**，但程式碼後置檔案會定義所指定的點陣圖矩形子集`SOURCE`欄位： 

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

此矩形來源隔離 monkey 的前端，如下列螢幕擷取畫面所示：

[![矩形子集](displaying-images/RectangleSubset.png "矩形子集")](displaying-images/RectangleSubset-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://developer.xamarin.com/api/root/SkiaSharp/)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

