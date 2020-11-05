---
title: Porter-Duff blend 模式
description: 使用 Porter-Duff blend 模式，根據來源和目的地影像來撰寫場景。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 104039bdb211eb823c4306071804e9680793840a
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365836"
---
# <a name="porter-duff-blend-modes"></a>Porter-Duff blend 模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Porter-Duff blend 模式的命名方式是在 Thomas Porter 和 Tom Duff 之後，這些開發了在工作 Lucasfilm 時的組合代數。 他們的書面 [_數位影像_](https://graphics.pixar.com/library/Compositing/paper.pdf) 已于1984年7月的 _電腦圖形_ （253到259）發行。 這些 blend 模式對組合而言是不可或缺的，這會將各種影像組合成複合場景：

![Porter-Duff 範例](porter-duff-images/PorterDuffSample.png "Porter-Duff 範例")

## <a name="porter-duff-concepts"></a>Porter-Duff 概念

假設 brownish 矩形佔用了顯示器表面的左邊和頂端三分之二：

![Porter-Duff 目的地](porter-duff-images/PorterDuffDst.png "Porter-Duff 目的地")

這個區域稱為 _目的地_ ， _有時也稱為__背景或背景_ 。

您想要繪製下列矩形，其大小與目的地相同。 矩形是透明的，但 bluish 區域會佔據右邊和下三分之二：

![Porter-Duff 來源](porter-duff-images/PorterDuffSrc.png "Porter-Duff 來源")

這稱為 _來源_ 或有時候 _前景_ 。

當您在目的地上顯示來源時，以下是您預期的結果：

![Porter-Duff 來源](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff 來源")

來源的透明圖元允許背景顯示，而 bluish 來源圖元則遮蔽背景。 這是正常情況，而且在 SkiaSharp as 中稱為 `SKBlendMode.SrcOver` 。 `BlendMode`當 `SKPaint` 第一次具現化物件時，該值為屬性的預設設定。

不過，您可以為不同的效果指定不同的 blend 模式。 如果您指定 `SKBlendMode.DstOver` ，則在來源與目的地交集的區域中，會顯示目的地，而不是來源：

![Porter-Duff 目的地](porter-duff-images/PorterDuffDstOver.png "Porter-Duff 目的地")

`SKBlendMode.DstIn`Blend 模式只會顯示目的地和來源與使用目的色彩相交的區域：

![Porter-Duff 目的地](porter-duff-images/PorterDuffDstIn.png "Porter-Duff 目的地")

`SKBlendMode.Xor` (專有或) 的 blend 模式不會出現在兩個區域重迭的位置：

![Porter-Duff 專屬或](porter-duff-images/PorterDuffXor.png "Porter-Duff 專有或")

彩色的目的地和來源矩形可有效地將顯示介面分成四個獨特的區域，這些區域可以用不同的方式來著色，以對應至目的地和來源矩形的存在：

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter-Duff")

右上方和左下方的矩形一律是空白的，因為目的地和來源在這些區域中都是透明的。 目的色彩會佔據左上區域，因此該區域可以使用目的色彩來著色，也可以完全不彩色。 同樣地，來源色彩會佔用右下角區域，因此該區域可以使用來源色彩來著色，也可以完全不彩色。 目的地和來源的交集可以使用目的色彩、來源色彩，或是完全不彩色。

合併的總數目為 2 (，適用于左上方的) 時間 2 ( () 的右下方) 時間3，或12。 這些是12個基本 Porter-Duff 組合模式。

在 (頁面 256) 的 _組合數位影像_ 結束時，Porter 和 Duff 會新增13個模式，再 ( _加上_ 對應于 SkiaSharp 成員的第13個模式 `SKBlendMode.Plus` 和 w3c _較淺_ 模式 (，這並不會與 w3c _淡化_ 模式混淆。 ) 這個 `Plus` 模式會新增目的地和來源色彩，這是稍後會更詳細說明的進程。

Skia 會新增一個名為的14種模式 `Modulate` ，它非常類似， `Plus` 但目的地和來源色彩會相乘。 您可以將它視為額外的 Porter-Duff blend 模式。

以下是 SkiaSharp 中所定義的14個 Porter-Duff 模式。 表格顯示其如何在上圖中的三個非空白區域中著色：

| [模式]       | 目的地 | 交叉 口 | 來源 |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | 來源       | X      |
| `Dst`      | X           | 目的地  |        |
| `SrcOver`  | X           | 來源       | X      |
| `DstOver`  | X           | 目的地  | X      |
| `SrcIn`    |             | 來源       |        |
| `DstIn`    |             | Destination  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | 來源       |        |
| `DstATop`  |             | Destination  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | 總和          | X      |
| `Modulate` |             | 產品      |        | 

這些 blend 模式是對稱的。 您可以交換來源和目的地，而且所有模式仍然可供使用。

模式的命名慣例遵循一些簡單的規則：

- **Src** 或 **Dst** 本身表示只顯示來源或目的地圖元。
- **Over** 尾碼表示在交集中可見的內容。 來源或目的地是以「更多」的方式繪製。
- **In** 尾碼表示只會彩色交集。 輸出會限制為只有來源或目的地的部分是「位於」的部分。
- **輸出** 尾碼表示不彩色交集。 輸出只是來源或目的部分（交集的「輸出」）部分。
- 上方 **的尾碼是** **In** 和 **Out** 的聯集。它包含來源或目的地位於另一個位置的區域。

請注意和模式之間的差異 `Plus` `Modulate` 。 這些模式正在對來源和目的地圖元執行不同類型的計算。 稍後會更詳細地說明。

**Porter-Duff 方格** 頁面會以方格的形式，在一個畫面上顯示所有14種模式。 每個模式都是個別的實例 `SKCanvasView` 。 基於這個理由，類別衍生自指名的 `SKCanvasView` `PorterDuffCanvasView` 。 靜態的函式會建立兩個相同大小的點陣圖，一個具有左上角區域中的 brownish 矩形，另一個則具有 bluish 矩形：

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    static SKBitmap srcBitmap, dstBitmap;

    static PorterDuffCanvasView()
    {
        dstBitmap = new SKBitmap(300, 300);
        srcBitmap = new SKBitmap(300, 300);

        using (SKPaint paint = new SKPaint())
        {
            using (SKCanvas canvas = new SKCanvas(dstBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0xC0, 0x80, 0x00);
                canvas.DrawRect(new SKRect(0, 0, 200, 200), paint);
            }
            using (SKCanvas canvas = new SKCanvas(srcBitmap))
            {
                canvas.Clear();
                paint.Color = new SKColor(0x00, 0x80, 0xC0);
                canvas.DrawRect(new SKRect(100, 100, 300, 300), paint);
            }
        }
    }
    ···
}
```

實例的函式具有類型的參數 `SKBlendMode` 。 它會將此參數儲存在欄位中。 

```csharp
class PorterDuffCanvasView : SKCanvasView
{
    ···
    SKBlendMode blendMode;

    public PorterDuffCanvasView(SKBlendMode blendMode)
    {
        this.blendMode = blendMode;
    }

    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Find largest square that fits
        float rectSize = Math.Min(info.Width, info.Height);
        float x = (info.Width - rectSize) / 2;
        float y = (info.Height - rectSize) / 2;
        SKRect rect = new SKRect(x, y, x + rectSize, y + rectSize);

        // Draw destination bitmap
        canvas.DrawBitmap(dstBitmap, rect);

        // Draw source bitmap
        using (SKPaint paint = new SKPaint())
        {
            paint.BlendMode = blendMode;
            canvas.DrawBitmap(srcBitmap, rect, paint);
        }

        // Draw outline
        using (SKPaint paint = new SKPaint())
        {
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 2;
            rect.Inflate(-1, -1);
            canvas.DrawRect(rect, paint);
        }
    }
}
```

覆 `OnPaintSurface` 寫會繪製兩個位圖。 第一個會以一般方式繪製：

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

第二個是使用 `SKPaint` `BlendMode` 已設定為函式引數之屬性的物件來繪製：

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

覆寫的其餘部分會在 `OnPaintSurface` 點陣圖周圍繪製矩形，以指出其大小。

`PorterDuffGridPage`類別會建立14個實例 `PorterDurffCanvasView` ，每個陣列的成員各一個 `blendModes` 。 陣列中成員的順序與資料表稍有不同，以便將 `SKBlendModes` 類似的模式置於彼此連續的位置。 的14個實例 `PorterDuffCanvasView` 會組織，以及中的標籤 `Grid` ：

```csharp
public class PorterDuffGridPage : ContentPage
{
    public PorterDuffGridPage()
    {
        Title = "Porter-Duff Grid";

        SKBlendMode[] blendModes =
        {
            SKBlendMode.Src, SKBlendMode.Dst, SKBlendMode.SrcOver, SKBlendMode.DstOver,
            SKBlendMode.SrcIn, SKBlendMode.DstIn, SKBlendMode.SrcOut, SKBlendMode.DstOut,
            SKBlendMode.SrcATop, SKBlendMode.DstATop, SKBlendMode.Xor, SKBlendMode.Plus,
            SKBlendMode.Modulate, SKBlendMode.Clear
        };

        Grid grid = new Grid
        {
            Margin = new Thickness(5)
        };

        for (int row = 0; row < 4; row++)
        {
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Auto });
            grid.RowDefinitions.Add(new RowDefinition { Height = GridLength.Star });
        }

        for (int col = 0; col < 3; col++)
        {
            grid.ColumnDefinitions.Add(new ColumnDefinition { Width = GridLength.Star });
        }

        for (int i = 0; i < blendModes.Length; i++)
        {
            SKBlendMode blendMode = blendModes[i];
            int row = 2 * (i / 4);
            int col = i % 4;

            Label label = new Label
            {
                Text = blendMode.ToString(),
                HorizontalTextAlignment = TextAlignment.Center
            };
            Grid.SetRow(label, row);
            Grid.SetColumn(label, col);
            grid.Children.Add(label);

            PorterDuffCanvasView canvasView = new PorterDuffCanvasView(blendMode);

            Grid.SetRow(canvasView, row + 1);
            Grid.SetColumn(canvasView, col);
            grid.Children.Add(canvasView);
        }

        Content = grid;
    }
}
```

結果如下︰

[![Porter-Duff 方格](porter-duff-images/PorterDuffGrid.png "Porter-Duff 方格")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

您會想要說服自己的透明度對 Porter-Duff blend 模式正常運作至關重要。 `PorterDuffCanvasView`類別包含三個方法呼叫的總計 `Canvas.Clear` 。 它們都使用無參數方法，將所有圖元設定為透明：

```csharp
canvas.Clear();
```

請嘗試變更任何呼叫，讓圖元設定為不透明白色：

```csharp
canvas.Clear(SKColors.White);
```

在這項變更之後，某些 blend 模式似乎正常運作，但其他模式則否。 如果您將來源點陣圖的背景設定為白色，則 `SrcOver` 模式無法運作，因為來源點陣圖中沒有任何透明的圖元可讓目的地顯示。 如果您將目的地點陣圖或畫布的背景設定為白色，則 `DstOver` 不會運作，因為目的地沒有任何透明的圖元。

您可能會想要以更簡單的呼叫來取代 **Porter-Duff 方格** 頁面中的點陣圖 `DrawRect` 。 這將適用于目的地矩形，但不適用於來源矩形。 來源矩形必須只包含 bluish 色彩區域。 來源矩形必須包含對應至目的地彩色區域的透明區域。 只有這些 blend 模式才會運作。

## <a name="using-mattes-with-porter-duff"></a>搭配 Porter-Duff 使用雜邊

[基礎構件 **-牆合成** ] 頁面會顯示傳統的組合工作範例：需要從數個部分組合圖片，包括需要排除背景的點陣圖。 以下是具有問題背景的 **SeatedMonkey.jpg** 點陣圖：

![本身的猴子](porter-duff-images/SeatedMonkey.jpg "本身的猴子")

針對組合進行準備時，會建立對應的 _遮罩_ ，也就是您想要顯示影像的另一個點陣圖，否則就是透明的。 這個檔案的名稱是 **SeatedMonkeyMatte.png** ，並且是 [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中 **Media** 資料夾的資源：

![固定的猴子遮罩](porter-duff-images/SeatedMonkeyMatte.png "固定的猴子遮罩")

這並 _不_ 是經過熟練建立的遮罩。 最理想的情況是，遮罩應該在黑色圖元邊緣周圍包含部分透明的圖元，而此遮罩則否。

適用于 **磚塊組合** 頁面的 XAML 檔案會具現化 `SKCanvasView` 和，以 `Button` 引導使用者完成組成最終影像的程式：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.BrickWallCompositingPage"
             Title="Brick-Wall Compositing">

    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Button Text="Show sitting monkey"
                HorizontalOptions="Center"
                Margin="0, 10"
                Clicked="OnButtonClicked" />

    </StackLayout>
</ContentPage>
```

程式碼後端檔案會載入所需的兩個位圖，並處理的 `Clicked` 事件 `Button` 。 每次 `Button` 按下 `step` 時，欄位都會遞增，並 `Text` 針對設定新的屬性 `Button` 。 `step`到達5時，會將它設回0：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    SKBitmap monkeyBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap matteBitmap = BitmapExtensions.LoadBitmapResource(
        typeof(BrickWallCompositingPage), 
        "SkiaSharpFormsDemos.Media.SeatedMonkeyMatte.png");

    int step = 0;

    public BrickWallCompositingPage ()
    {
        InitializeComponent ();
    }

    void OnButtonClicked(object sender, EventArgs args)
    {
        Button btn = (Button)sender;
        step = (step + 1) % 5;

        switch (step)
        {
            case 0: btn.Text = "Show sitting monkey"; break;
            case 1: btn.Text = "Draw matte with DstIn"; break;
            case 2: btn.Text = "Draw sidewalk with DstOver"; break;
            case 3: btn.Text = "Draw brick wall with DstOver"; break;
            case 4: btn.Text = "Reset"; break;
        }

        canvasView.InvalidateSurface();
    }
    
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        ···
    }
}
```

當程式首次執行時，除了下列專案之外，不會顯示任何內容 `Button` ：

[![磚-牆合成步驟0](porter-duff-images/BrickWallCompositing0.png "Brick-Wall 的組合步驟0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

按 `Button` 一次會使 `step` 遞增為1， `PaintSurface` 處理常式現在會顯示 **SeatedMonkey.jpg** ：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        float x = (info.Width - monkeyBitmap.Width) / 2;
        float y = info.Height - monkeyBitmap.Height;

        // Draw monkey bitmap
        if (step >= 1)
        {
            canvas.DrawBitmap(monkeyBitmap, x, y);
        }
        ···
    }
}
```

沒有任何 `SKPaint` 物件，因此沒有 blend 模式。 點陣圖會顯示在畫面底部：

[![磚-牆合成步驟1](porter-duff-images/BrickWallCompositing1.png "Brick-Wall 的組合步驟1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

再按 `Button` 一次，並 `step` 遞增為2。 這是顯示 **SeatedMonkeyMatte.png** 檔案的重要步驟：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw matte to exclude monkey's surroundings
        if (step >= 2)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.BlendMode = SKBlendMode.DstIn;
                canvas.DrawBitmap(matteBitmap, x, y, paint);
            }
        }
        ···
    }
}
```

Blend 模式是 `SKBlendMode.DstIn` ，這表示目的地將會保留在對應到來源非透明區域的區域中。 對應至原始點陣圖之目的矩形的其餘部分會變成透明：

[![磚-牆合成步驟2](porter-duff-images/BrickWallCompositing2.png "Brick-Wall 合成步驟2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

背景已移除。 

下一步是繪製類似于人行道的矩形。 此人行道的外觀是以兩個著色器的組合為基礎：純色著色器和 Perlin 雜訊著色器：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        const float sidewalkHeight = 80;
        SKRect rect = new SKRect(info.Rect.Left, info.Rect.Bottom - sidewalkHeight,
                                 info.Rect.Right, info.Rect.Bottom);

        // Draw gravel sidewalk for monkey to sit on
        if (step >= 3)
        {
            using (SKPaint paint = new SKPaint())
            {
                paint.Shader = SKShader.CreateCompose(
                                    SKShader.CreateColor(SKColors.SandyBrown),
                                    SKShader.CreatePerlinNoiseTurbulence(0.1f, 0.3f, 1, 9));

                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(rect, paint);
            }
        }
        ···
    }
}
```

因為這個人行道必須在猴子背後，所以 blend 模式是 `DstOver` 。 目的地只會顯示背景透明的位置：

[![磚-牆合成步驟3](porter-duff-images/BrickWallCompositing3.png "Brick-Wall 合成步驟3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

最後一個步驟是加入基礎構件牆。 此程式會使用可作為類別中靜態屬性的基礎牆點陣圖磚 `BrickWallTile` `AlgorithmicBrickWallPage` 。 轉譯轉換會新增至轉換 `SKShader.CreateBitmap` 磚的呼叫，讓底部的資料列成為完整的磚：

```csharp
public partial class BrickWallCompositingPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        ···
        // Draw bitmap tiled brick wall behind monkey
        if (step >= 4)
        {
            using (SKPaint paint = new SKPaint())
            {
                SKBitmap bitmap = AlgorithmicBrickWallPage.BrickWallTile;
                float yAdjust = (info.Height - sidewalkHeight) % bitmap.Height;

                paint.Shader = SKShader.CreateBitmap(bitmap,
                                                     SKShaderTileMode.Repeat,
                                                     SKShaderTileMode.Repeat,
                                                     SKMatrix.MakeTranslation(0, yAdjust));
                paint.BlendMode = SKBlendMode.DstOver;
                canvas.DrawRect(info.Rect, paint);
            }
        }
    }
}
```

為了方便起見，此 `DrawRect` 呼叫會在整個畫布上顯示此著色器，但此 `DstOver` 模式會將輸出限制為只有仍為透明的畫布區域：

[![磚-牆合成步驟4](porter-duff-images/BrickWallCompositing4.png "Brick-Wall 合成步驟4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

顯然有其他方法可以撰寫這個場景。 它可以從背景開始，並在前景開始。 但是，使用 blend 模式可提供更大的彈性。 尤其是，使用遮罩可讓點陣圖的背景從組合的場景中排除。

如同您在 [使用路徑和區域來裁剪](../../curves/clipping.md)文章中所學到的， `SKCanvas` 類別會定義三種類型的裁剪，對應至 [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*) 、 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*) 和 [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) 方法。 Porter-Duff blend 模式會新增另一種類型的裁剪，可將影像限制為您可以繪製的任何文字，包括點陣圖。 基礎 **構件中使用的遮罩** 基本上會定義裁剪區域。

## <a name="gradient-transparency-and-transitions"></a>漸層透明度和轉換

本文稍早所示的 Porter-Duff blend 模式的範例，包含不透明圖元和透明圖元的所有相關影像，但不是部分透明的圖元。 混合模式函式也會針對這些圖元來定義。 下表是使用在 Skia [**SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)中找到之標記法的 Porter-Duff blend 模式更正式的定義。  (，因為 **SkBlendMode 參考** 是 Skia 參考，所以會使用 c + + 語法。 ) 

就概念而言，每個圖元的紅色、綠色、藍色和 Alpha 元件會從位元組轉換為0到1範圍內的浮點數。 Alpha 色板的0是完全透明的，1是完全不透明的

下表中的標記法使用下列縮寫：

- **Da** 是目的地 Alpha 通道
- **Dc** 是目的地 RGB 色彩
- **Sa** 是來源 Alpha 通道
- **Sc** 是來源 RGB 色彩

RGB 色彩會乘以 Alpha 值。 例如，如果 **Sc** 代表純紅色，但 **Sa** 是0x80，則 RGB 色彩是 **(0x80，0，0)** 。 如果 **Sa** 是0，則所有 RGB 元件也是零。

結果會顯示在以括弧括住，並以逗點分隔的 RGB 色彩： **[Alpha，color]** 。 針對色彩，會針對紅色、綠色和藍色元件分別執行計算：

| [模式]       | 作業 |
| ---------- | --------- |
| `Clear`    | [0，0]    |
| `Src`      | [Sa，Sc]  |
| `Dst`      | [Da，Dc]  |
| `SrcOver`  | [Sa + Da · (1 – Sa) ，Sc + Dc · (1 – Sa)  | 
| `DstOver`  | [Da + Sa · (1 – Da) ，Dc + Sc · (1 – Da)  |
| `SrcIn`    | SaDa，Sc ·Da |
| `DstIn`    | DaSa、Dc ·Sa |
| `SrcOut`   | [Sa · (1 – Da) ，Sc · (1 – Da) ] |
| `DstOut`   | [Da · (1 – Sa) ，Dc · (1 – Sa) ] |
| `SrcATop`  | [Da，Sc ·Da + Dc · (1 – Sa) ] |
| `DstATop`  | [Sa，Dc ·Sa + Sc · (1 – Da) ] |
| `Xor`      | [Sa + Da –2·SaDa、Sc · (1 – Da) + Dc · (1 – Sa) ] |
| `Plus`     | [Sa + Da，Sc + Dc] |
| `Modulate` | SaDa，Sc ·電源 | 

當 **Da** 和 **Sa** 為0或1時，這些作業比較容易分析。 例如，在預設模式下 `SrcOver` ，如果 **Sa** 是0，則 **Sc** 也是0，而結果為 **[Da，Dc]** 、目的地 Alpha 和 color。 如果 **Sa** 是1，則結果為 **[sa，Sc]** 、來源 Alpha 和色彩，或 **[1，Sc]** 。

`Plus`和 `Modulate` 模式與其他模式稍有不同，因為新的色彩會因為來源和目的地的組合而產生。 您 `Plus` 可以使用位元組元件或浮點元件來解讀模式。 在稍早所示的 **Porter-Duff 格線** 頁中，目的地色彩是 **(0xC0，0x80，0x00)** ，而來源色彩 **(0x00、0x80、0xC0)** 。 會加入每一組元件，但總和會在0xFF 壓制。 結果是 **(0xC0，0xff，0xC0)** 的色彩。 這是交集中所顯示的色彩。

在 `Modulate` 模式中，RGB 值必須轉換成浮點數。 目的地色彩是 **(0.75、0.5、0)** ，而來源 **(0、0.5、0.75)** 。 RGB 元件會彼此相乘，而且結果會是 **(0、0.25、0)** 。 這就是這種模式的 **Porter-Duff 格線** 頁交集中所顯示的色彩。

[ **Porter-Duff 透明度** ] 頁面可讓您檢查 Porter-Duff blend 模式如何在部分透明的繪圖物件上運作。 XAML 檔案包含 `Picker` 具有 Porter-Duff 模式的：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaviews="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.PorterDuffTransparencyPage"
             Title="Porter-Duff Transparency">

    <StackLayout>
        <skiaviews:SKCanvasView x:Name="canvasView"
                                VerticalOptions="FillAndExpand"
                                PaintSurface="OnCanvasViewPaintSurface" />

        <Picker x:Name="blendModePicker"
                Title="Blend Mode"
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKBlendMode}">
                    <x:Static Member="skia:SKBlendMode.Clear" />
                    <x:Static Member="skia:SKBlendMode.Src" />
                    <x:Static Member="skia:SKBlendMode.Dst" />
                    <x:Static Member="skia:SKBlendMode.SrcOver" />
                    <x:Static Member="skia:SKBlendMode.DstOver" />
                    <x:Static Member="skia:SKBlendMode.SrcIn" />
                    <x:Static Member="skia:SKBlendMode.DstIn" />
                    <x:Static Member="skia:SKBlendMode.SrcOut" />
                    <x:Static Member="skia:SKBlendMode.DstOut" />
                    <x:Static Member="skia:SKBlendMode.SrcATop" />
                    <x:Static Member="skia:SKBlendMode.DstATop" />
                    <x:Static Member="skia:SKBlendMode.Xor" />
                    <x:Static Member="skia:SKBlendMode.Plus" />
                    <x:Static Member="skia:SKBlendMode.Modulate" />
                </x:Array>
            </Picker.ItemsSource>

            <Picker.SelectedIndex>
                3
            </Picker.SelectedIndex>
        </Picker>
    </StackLayout>
</ContentPage>
```

程式碼後置於檔案中，會使用線性漸層來填滿兩個相同大小的矩形。 目的地漸層是從右下角到左下方。 它是在右上角 brownish，但朝中央開始淡出為透明，而且在左下角是透明的。 

來源矩形的漸層從左上角到右下方。 左上角是 bluish，但會再次淡化為透明，在右下角則是透明的。 

```csharp
public partial class PorterDuffTransparencyPage : ContentPage
{
    public PorterDuffTransparencyPage()
    {
        InitializeComponent();
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

        // Make square display rectangle smaller than canvas
        float size = 0.9f * Math.Min(info.Width, info.Height);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        SKRect rect = new SKRect(x, y, x + size, y + size);

        using (SKPaint paint = new SKPaint())
        {
            // Draw destination
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Right, rect.Top),
                                new SKPoint(rect.Left, rect.Bottom),
                                new SKColor[] { new SKColor(0xC0, 0x80, 0x00),
                                                new SKColor(0xC0, 0x80, 0x00, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            canvas.DrawRect(rect, paint);

            // Draw source
            paint.Shader = SKShader.CreateLinearGradient(
                                new SKPoint(rect.Left, rect.Top),
                                new SKPoint(rect.Right, rect.Bottom),
                                new SKColor[] { new SKColor(0x00, 0x80, 0xC0), 
                                                new SKColor(0x00, 0x80, 0xC0, 0) },
                                new float[] { 0.4f, 0.6f },
                                SKShaderTileMode.Clamp);

            // Get the blend mode from the picker
            paint.BlendMode = blendModePicker.SelectedIndex == -1 ? 0 :
                                    (SKBlendMode)blendModePicker.SelectedItem;

            canvas.DrawRect(rect, paint);

            // Stroke surrounding rectangle
            paint.Shader = null;
            paint.BlendMode = SKBlendMode.SrcOver;
            paint.Style = SKPaintStyle.Stroke;
            paint.Color = SKColors.Black;
            paint.StrokeWidth = 3;
            canvas.DrawRect(rect, paint);
        }
    }
}
```

此程式示範 Porter-Duff blend 模式可以與點陣圖以外的繪圖物件一起使用。 不過，來源必須包含透明區域。 這種情況是因為漸層填滿矩形，但漸層的一部分是透明的。

以下是三個範例：

[![Porter-Duff 透明度](porter-duff-images/PorterDuffTransparency.png "Porter-Duff 透明度")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

目的地和來源的設定與原始 Porter-Duff [_組合數位影像_](https://graphics.pixar.com/library/Compositing/paper.pdf) 紙張的頁面255中所顯示的圖表非常類似，但此頁面會示範 blend 模式對於部分透明度的區域是正常運作的。

您可以針對一些不同的效果使用透明漸層。 其中一個可能性是遮罩，類似于 [ **SkiaSharp 圓形** 漸層] 頁面的 [ [**遮罩**](../shaders/circular-gradients.md#radial-gradients-for-masking)漸層的星形漸層] 區段中所示的技巧。 大部分的 **組合遮罩** 頁面都類似于先前的程式。 它會載入點陣圖資源，並決定要在其中顯示的矩形。 放射狀漸層是根據預先決定的中心和半徑來建立的：

```csharp
public class CompositingMaskPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
        typeof(CompositingMaskPage),
        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    static readonly SKPoint CENTER = new SKPoint(180, 300);
    static readonly float RADIUS = 120;

    public CompositingMaskPage ()
    {
        Title = "Compositing Mask";

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

        // Find rectangle to display bitmap
        float scale = Math.Min((float)info.Width / bitmap.Width,
                               (float)info.Height / bitmap.Height);

        SKRect rect = SKRect.Create(scale * bitmap.Width, scale * bitmap.Height);

        float x = (info.Width - rect.Width) / 2;
        float y = (info.Height - rect.Height) / 2;
        rect.Offset(x, y);

        // Display bitmap in rectangle
        canvas.DrawBitmap(bitmap, rect);

        // Adjust center and radius for scaled and offset bitmap
        SKPoint center = new SKPoint(scale * CENTER.X + x,
                                        scale * CENTER.Y + y);
        float radius = scale * RADIUS;

        using (SKPaint paint = new SKPaint())
        {
            paint.Shader = SKShader.CreateRadialGradient(
                                center,
                                radius,
                                new SKColor[] { SKColors.Black,
                                                SKColors.Transparent },
                                new float[] { 0.6f, 1 },
                                SKShaderTileMode.Clamp);

            paint.BlendMode = SKBlendMode.DstIn;

            // Display rectangle using that gradient and blend mode
            canvas.DrawRect(rect, paint);
        }

        canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
    }
}
```

這項程式的差異在於，漸層以黑色開頭，並以透明結尾。 它會顯示在具有 blend 模式的點陣圖上 `DstIn` ，它只會在來源非透明的區域中顯示目的地。

在 `DrawRect` 呼叫之後，畫布的整個介面都是透明的，但星形漸層所定義的圓形除外。 進行最後的呼叫：

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

畫布的所有透明區域都有粉紅色的色彩：

[![組合遮罩](porter-duff-images/CompositingMask.png "組合遮罩")](porter-duff-images/CompositingMask-Large.png#lightbox)

您也可以使用 Porter-Duff 模式和部分透明的漸層，從某個影像轉換成另一個影像。 [漸層 **轉換** ] 頁面包含， `Slider` 指出從0到1的轉換進度層級，以及 `Picker` 選擇您想要的轉換類型：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Effects.GradientTransitionsPage"
             Title="Gradient Transitions">
    
    <StackLayout>
        <skia:SKCanvasView x:Name="canvasView"
                           VerticalOptions="FillAndExpand"
                           PaintSurface="OnCanvasViewPaintSurface" />

        <Slider x:Name="progressSlider"
                Margin="10, 0"
                ValueChanged="OnSliderValueChanged" />

        <Label Text="{Binding Source={x:Reference progressSlider},
                              Path=Value,
                              StringFormat='Progress = {0:F2}'}"
               HorizontalTextAlignment="Center" />

        <Picker x:Name="transitionPicker" 
                Title="Transition" 
                Margin="10"
                SelectedIndexChanged="OnPickerSelectedIndexChanged" />
        
    </StackLayout>
</ContentPage>
```

程式碼後端檔案會載入兩個位圖資源以示範轉換。 這些是本文稍早 **點陣圖溶解** 頁面中使用的兩個影像。 程式碼也會定義具有三個成員的列舉，其對應至三種類型的漸層 &mdash; （線性、星形和掃色）。 這些值會載入至 `Picker` ：

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    SKBitmap bitmap1 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.SeatedMonkey.jpg");

    SKBitmap bitmap2 = BitmapExtensions.LoadBitmapResource(
        typeof(GradientTransitionsPage),
        "SkiaSharpFormsDemos.Media.FacePalm.jpg");

    enum TransitionMode
    {
        Linear,
        Radial,
        Sweep
    };

    public GradientTransitionsPage ()
    {
        InitializeComponent ();

        foreach (TransitionMode mode in Enum.GetValues(typeof(TransitionMode)))
        {
            transitionPicker.Items.Add(mode.ToString());
        }

        transitionPicker.SelectedIndex = 0;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        canvasView.InvalidateSurface();
    }

    void OnPickerSelectedIndexChanged(object sender, EventArgs args)
    {
        canvasView.InvalidateSurface();
    }
    ···
}
```

程式碼後端檔案會建立三個 `SKPaint` 物件。 `paint0`物件不使用 blend 模式。 此繪製物件是用來繪製具有漸層的矩形，其漸層會從黑色變成透明，如陣列中所示 `colors` 。 `positions`陣列是以的位置為基礎 `Slider` ，但是稍微調整。 如果 `Slider` 是其最小值或最大值，則 `progress` 值為0或1，而兩個位圖中的其中一個應該是完全可見的。 `positions`必須據此設定這些值的陣列。

如果 `progress` 值為0，則 `positions` 陣列包含值-0.1 和0。 SkiaSharp 會將第一個值調整為等於0，這表示只有0的漸層是黑色，否則為透明。 當 `progress` 為0.5 時，陣列會包含值0.45 和0.55。 漸層是從0到0.45 的黑色，然後轉換成透明的，且從0.55 到1都是完全透明的。 當 `progress` 是1時， `positions` 陣列是1和1.1，這表示漸層是從0到1的黑色。

`colors`和 `position` 陣列都是用於建立漸層的三種方法中 `SKShader` 。 根據選取專案，只會建立下列其中一個著色器 `Picker` ： 

```csharp
public partial class GradientTransitionsPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Assume both bitmaps are square for display rectangle
        float size = Math.Min(info.Width, info.Height);
        SKRect rect = SKRect.Create(size, size);
        float x = (info.Width - size) / 2;
        float y = (info.Height - size) / 2;
        rect.Offset(x, y);

        using (SKPaint paint0 = new SKPaint())
        using (SKPaint paint1 = new SKPaint())
        using (SKPaint paint2 = new SKPaint())
        {
            SKColor[] colors = new SKColor[] { SKColors.Black,
                                               SKColors.Transparent };

            float progress = (float)progressSlider.Value;

            float[] positions = new float[]{ 1.1f * progress - 0.1f,
                                             1.1f * progress };

            switch ((TransitionMode)transitionPicker.SelectedIndex)
            {
                case TransitionMode.Linear:
                    paint0.Shader = SKShader.CreateLinearGradient(
                                        new SKPoint(rect.Left, 0),
                                        new SKPoint(rect.Right, 0),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Radial:
                    paint0.Shader = SKShader.CreateRadialGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        (float)Math.Sqrt(Math.Pow(rect.Width / 2, 2) +
                                                         Math.Pow(rect.Height / 2, 2)),
                                        colors,
                                        positions,
                                        SKShaderTileMode.Clamp);
                    break;

                case TransitionMode.Sweep:
                    paint0.Shader = SKShader.CreateSweepGradient(
                                        new SKPoint(rect.MidX, rect.MidY),
                                        colors,
                                        positions);
                    break;
            }

            canvas.DrawRect(rect, paint0);

            paint1.BlendMode = SKBlendMode.SrcOut;
            canvas.DrawBitmap(bitmap1, rect, paint1);

            paint2.BlendMode = SKBlendMode.DstOver;
            canvas.DrawBitmap(bitmap2, rect, paint2);
        }
    }
}
```

該漸層會在沒有 blend 模式的矩形中顯示。 在 `DrawRect` 呼叫之後，畫布只會包含從黑色到透明的漸層。 具有較高值的黑色增加量 `Slider` 。

在處理常式的最後四個語句中 `PaintSurface` ，會顯示兩個位圖。 `SrcOut`Blend 模式表示第一個點陣圖只會顯示在背景的透明區域中。 `DstOver`第二個位圖的模式表示第二個位圖只會顯示在第一個點陣圖未顯示的區域中。

下列螢幕擷取畫面顯示三種不同的轉換類型，每個都在50% 標示：

[![漸層轉換](porter-duff-images/GradientTransitions.png "漸層轉換")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)