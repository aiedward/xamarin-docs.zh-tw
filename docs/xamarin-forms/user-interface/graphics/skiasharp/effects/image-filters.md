---
title: SkiaSharp 映像篩選器
description: 了解如何建立模糊和卸除陰影中使用映像篩選器。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 173E7B22-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/27/2018
ms.openlocfilehash: 517ebfb529dd26236ba157d40168fa7c75288d27
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050370"
---
# <a name="skiasharp-image-filters"></a>SkiaSharp 映像篩選器

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

映像篩選器是構成影像的像素的所有色彩位元上操作的效果。 它們具有更多功能以上遮罩篩選，如本文所述 pracovat pouze alpha 色頻[ **SkiaSharp 遮罩篩選**](mask-filters.md)。 若要使用的映像篩選器，將[ `ImageFilter` ](xref:SkiaSharp.SKPaint.ImageFilter)屬性`SKPaint`物件的型別[ `SKImageFilter` ](xref:SkiaSharp.SKImageFilter)您已藉由呼叫其中一種類別的靜態方法。

熟悉遮罩篩選的最佳方式是藉由試驗這些靜態方法。 您可以使用遮罩篩選器設定為模糊的整個點陣圖：

![模糊範例](image-filters-images/ImageFilterExample.png "模糊範例")

這篇文章也示範如何使用映像篩選器下拉式陰影，和浮凸及建立 engraving 效果。

## <a name="blurring-vector-graphics-and-bitmaps"></a>向量圖形和點陣圖模糊處理

建立此模糊效果[ `SKImageFilter.CreateBlur` ](xref:SkiaSharp.SKImageFilter.CreateBlur*)靜態方法具有重大中的模糊方法優於[ `SKMaskFilter` ](xref:SkiaSharp.SKMaskFilter)類別： 映像篩選器可以模糊整個點陣圖。 方法具有下列語法：

```csharp
public static SkiaSharp.SKImageFilter CreateBlur (float sigmaX, float sigmaY,
                                                  SKImageFilter input = null,
                                                  SKImageFilter.CropRect cropRect = null);
```

此方法有兩個標準差值&mdash;柔邊中的延伸區的水平方向和第二個垂直方向的第一個。 您可以指定另一個映像篩選器做為選擇性的第三個引數，以重疊顯示映像篩選條件。 也可以指定的裁剪矩形。

**映像模糊實驗**頁面[ **SkiaSharpFormsDemos** ](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)包含兩個`Slider`檢視，可讓您試驗設定各種層級的模糊：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.ImageBlurExperimentPage"
             Title="Image Blur Experiment">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會使用兩個`Slider`值來呼叫`SKImageFilter.CreateBlur`如`SKPaint`物件，用來顯示文字和點陣圖：


```csharp
public partial class ImageBlurExperimentPage : ContentPage
{
    const string TEXT = "Blur My Text";

    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                            typeof(MaskBlurExperimentPage),
                            "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    public ImageBlurExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Pink);

        // Get values from sliders
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = (info.Width - 100) / (TEXT.Length / 2);
            paint.ImageFilter = SKImageFilter.CreateBlur(sigmaX, sigmaY);

            // Get text bounds and calculate display rectangle
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);
            SKRect textRect = new SKRect(0, 0, info.Width, textBounds.Height + 50);

            // Center the text in the display rectangle
            float xText = textRect.Width / 2 - textBounds.MidX;
            float yText = textRect.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);

            // Calculate rectangle for bitmap
            SKRect bitmapRect = new SKRect(0, textRect.Bottom, info.Width, info.Height);
            bitmapRect.Inflate(-50, -50);

            canvas.DrawBitmap(bitmap, bitmapRect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

三個螢幕擷取畫面顯示各種設定`sigmaX`和`sigmaY`設定：

[![映像模糊實驗](image-filters-images/ImageBlurExperiment.png "影像模糊實驗")](image-filters-images/ImageBlurExperiment-Large.png#lightbox)

為了讓柔邊一致之間不同的顯示大小和解析度，設定`sigmaX`和`sigmaY`柔邊會套用至映像轉譯的像素大小成正比的值。

## <a name="drop-shadow"></a>陰影

[ `SKImageFilter.CreateDropShadow` ](xref:SkiaSharp.SKImageFilter.CreateDropShadow*)靜態方法會建立`SKImageFilter`下拉式陰影的物件：

```csharp
public static SKImageFilter CreateDropShadow (float dx, float dy,
                                              float sigmaX, float sigmaY,
                                              SKColor color,
                                              SKDropShadowImageFilterShadowMode shadowMode,
                                              SKImageFilter input = null,
                                              SKImageFilter.CropRect cropRect = null);
```

若要設定這個物件`ImageFilter`屬性`SKPaint`物件，以及您繪製與該物件的所有項目會有其背後的陰影。

`dx`和`dy`參數表示像素為單位，從 圖形物件的陰影水平和垂直位移。 2D 圖形的慣例是假設來自左上方，這表示這些引數應為正數放置陰影下方和右側的圖形物件的光源。

`sigmaX`和`sigmaY`參數非常模糊的陰影的因素。

`color`參數是延伸陰影的色彩。 這`SKColor`值可以包含透明效果。 其中一個可能性是色彩值`SKColors.Black.WithAlpha(0x80)`變暗任何色彩背景。

最後兩個參數是選擇性的。

**卸除陰影實驗**程式可讓您試驗值`dx`， `dy`， `sigmaX`，和`sigmaY`顯示下拉式陰影的文字字串。 XAML 檔案將四個會具現化`Slider`檢視，以設定下列值：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.DropShadowExperimentPage"
             Title="Drop Shadow Experiment">
    <ContentPage.Resources>
        <Style TargetType="Slider">
            <Setter Property="Margin" Value="10, 0" />
        </Style>

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment" Value="Center" />
        </Style>
    </ContentPage.Resources>

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="dxSlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dxSlider},
                              Path=Value,
                              StringFormat='Horizontal offset = {0:F1}'}" />

        <Slider x:Name="dySlider"
                Minimum="-20"
                Maximum="20"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference dySlider},
                              Path=Value,
                              StringFormat='Vertical offset = {0:F1}'}" />

        <Slider x:Name="sigmaXSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaXSlider},
                              Path=Value,
                              StringFormat='Sigma X = {0:F1}'}" />

        <Slider x:Name="sigmaYSlider"
                Maximum="10"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference sigmaYSlider},
                              Path=Value,
                              StringFormat='Sigma Y = {0:F1}'}" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案會使用這些值來建立紅色陰影的藍色文字字串：

```csharp
public partial class DropShadowExperimentPage : ContentPage
{
    const string TEXT = "Drop Shadow";

    public DropShadowExperimentPage ()
    {
        InitializeComponent ();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Get values from sliders
        float dx = (float)dxSlider.Value;
        float dy = (float)dySlider.Value;
        float sigmaX = (float)sigmaXSlider.Value;
        float sigmaY = (float)sigmaYSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            // Set SKPaint properties
            paint.TextSize = info.Width / 7;
            paint.Color = SKColors.Blue;
            paint.ImageFilter = SKImageFilter.CreateDropShadow(
                                    dx,
                                    dy,
                                    sigmaX,
                                    sigmaY,
                                    SKColors.Red,
                                    SKDropShadowImageFilterShadowMode.DrawShadowAndForeground);

            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            // Center the text in the display rectangle
            float xText = info.Width / 2 - textBounds.MidX;
            float yText = info.Height / 2 - textBounds.MidY;

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

以下是執行的程式：

[![卸除陰影實驗](image-filters-images/DropShadowExperiment.png "卸除陰影實驗")](image-filters-images/DropShadowExperiment-Large.png#lightbox)

最右邊的通用 Windows 平台螢幕擷取畫面中的負位移的值會導致出現上方和左邊的文字陰影。 這可能表示光源右下方，不是電腦圖形中的慣例。 但是，它似乎不是以任何方式，錯誤可能是因為陰影也會變成很模糊，且看起來更裝飾之用，比大多數的陰影。

## <a name="lighting-effects"></a>光源效果

`SKImageFilter`類別會定義擁有類似的名稱及參數，此處會增加複雜性的順序列出的六種方法：

- [`CreateDistantLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateDistantLitDiffuse*)
- [`CreateDistantLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateDistantLitSpecular*)
- [`CreatePointLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreatePointLitDiffuse*)
- [`CreatePointLitSpecular`](xref:SkiaSharp.SKImageFilter.CreatePointLitSpecular*)
- [`CreateSpotLitDiffuse`](xref:SkiaSharp.SKImageFilter.CreateSpotLitDiffuse*)
- [`CreateSpotLitSpecular`](xref:SkiaSharp.SKImageFilter.CreateSpotLitSpecular*)

這些方法會建立映像篩選器，模擬立體表面的不同類型光線的影響。 產生的映像篩選器會位於二維物件，如同它們存在於 3D 空間中，這可能會導致出現提高權限或下凹的這些物件，或使用反射反白顯示。

`Distant`淺方法假設遠距離燈。 為了清楚的物件，燈光會假設為指向在 3D 空間中，如同在地球的範圍縮小 Sun 一個一致的方向。 `Point`淺方法模擬燈泡，放在發出往所有方向的光線的 3D 空間中。 `Spot`光有位置與方向，如同手電筒。

位置和方向在 3D 空間中的同時指定值[ `SKPoint3` ](xref:SkiaSharp.SKPoint3)結構，也就是類似`SKPoint`具有名為的三個屬性，但`X`， `Y`，和`Z`。

數目和複雜度，這些方法的參數會使對它們進行實驗困難。 若要開始，**較遠的光線實驗**頁面可讓您試驗參數`CreateDistantLightDiffuse`方法：

```csharp
public static SKImageFilter CreateDistantLitDiffuse (SKPoint3 direction,
                                                     SKColor lightColor,
                                                     float surfaceScale,
                                                     float kd,
                                                     SKImageFilter input = null,
                                                     SKImageFilter.CropRect cropRect = null);
```

頁面不會使用最後兩個選擇性參數。

三個`Slider`檢視中的 XAML 檔案可讓您選取`Z`座標`SKPoint3`值，`surfaceScale`參數，而`kd`參數，為 「 擴散光源常數 」 的 API 文件中定義：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaLightExperiment.MainPage"
             Title="Distant Light Experiment">

    <StackLayout>

        <skia:SKCanvasView x:Name="canvasView"
                           PaintSurface="OnCanvasViewPaintSurface"
                           VerticalOptions="FillAndExpand" />

        <Slider x:Name="zSlider"
                Minimum="-10"
                Maximum="10"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference zSlider},
                              Path=Value,
                              StringFormat='Z = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="surfaceScaleSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference surfaceScaleSlider},
                              Path=Value,
                              StringFormat='Surface Scale = {0:F1}'}"
               HorizontalTextAlignment="Center" />

        <Slider x:Name="lightConstantSlider"
                Minimum="-1"
                Maximum="1"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference lightConstantSlider},
                              Path=Value,
                              StringFormat='Light Constant = {0:F1}'}"
               HorizontalTextAlignment="Center" />
    </StackLayout>
</ContentPage>
```

程式碼後置檔案取得這三個值，並使用它們來建立映像篩選器顯示的文字字串：

```csharp
public partial class DistantLightExperimentPage : ContentPage
{
    const string TEXT = "Lighting";

    public DistantLightExperimentPage()
    {
        InitializeComponent();
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        float z = (float)zSlider.Value;
        float surfaceScale = (float)surfaceScaleSlider.Value;
        float lightConstant = (float)lightConstantSlider.Value;

        using (SKPaint paint = new SKPaint())
        {
            paint.IsAntialias = true;

            // Size text to 90% of canvas width
            paint.TextSize = 100;
            float textWidth = paint.MeasureText(TEXT);
            paint.TextSize *= 0.9f * info.Width / textWidth;

            // Find coordinates to center text
            SKRect textBounds = new SKRect();
            paint.MeasureText(TEXT, ref textBounds);

            float xText = info.Rect.MidX - textBounds.MidX;
            float yText = info.Rect.MidY - textBounds.MidY;

            // Create distant light image filter
            paint.ImageFilter = SKImageFilter.CreateDistantLitDiffuse(
                                    new SKPoint3(2, 3, z),
                                    SKColors.White,
                                    surfaceScale,
                                    lightConstant);

            canvas.DrawText(TEXT, xText, yText, paint);
        }
    }
}
```

第一個引數`SKImageFilter.CreateDistantLitDiffuse`是燈光的方向。 正 X 和 Y 座標表示，光是向右再向下箭。 在畫面的正 Z 座標點。 XAML 檔案可讓您選取負 Z 值，但這只讓您可以查看時，才會發生什麼事： 負 Z 座標就概念而言，會導致光源點從螢幕。 任何其他再小型的負值，光源效果會停止運作。

`surfaceScale`引數的範圍可以從-1 到 1。 （高或較低的值有任何進一步的作用）。這些是相對 Z 軸中的值，指出從畫布介面圖形物件 （在此案例中的文字字串） 的替代。 使用負數值，來引發的畫布，介面上方的文字字串和要按住至畫布的正數值。

`lightConstant`值應為正數。 （您可以看到，它們會導致停止運作效果程式允許負數值）。較高的值會導致更密集的光線。

這些因素可以平衡取得浮凸時生效`surfaceScale`（如同 iOS 和 Android 的螢幕擷取畫面） 為負數和雕刻時生效`surfaceScale`為正數的做為右邊 UWP 螢幕擷取畫面：

[![較遠的光線實驗](image-filters-images/DistantLightExperiment.png "較遠的光線實驗")](image-filters-images/DistantLightExperiment-Large.png#lightbox)

Android 的螢幕擷取畫面都有 Z 值為 0，這表示到右邊，僅指光線。 不以醒目顯示背景和發亮的文字字串的介面不是其中一個。 燈光只會影響文字的邊緣十分難以察覺的效果。

本文中所示範的浮凸和雕刻文字的替代方法[轉譯轉換](../transforms/translate.md)： 文字字串會顯示兩次以不同的色彩，位移稍微彼此。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
