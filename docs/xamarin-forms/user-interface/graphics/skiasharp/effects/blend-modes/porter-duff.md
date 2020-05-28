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
ms.openlocfilehash: a1e6290c0f85b54c3fd8958bc43667714bdece20
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131049"
---
# <a name="porter-duff-blend-modes"></a>Porter-Duff blend 模式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Porter-Duff blend 模式的命名方式為 Thomas Porter 和 Tom Duff，其在處理 Lucasfilm 時開發了合成的代數。 其紙張[_組合數位影像_](https://graphics.pixar.com/library/Compositing/paper.pdf)已發行于1984年7月的_電腦圖形_，第 259 253 頁的問題。 這些 blend 模式對於組合很重要，它會將各種影像組合成複合場景：

![Porter-Duff 範例](porter-duff-images/PorterDuffSample.png "Porter-Duff 範例")

## <a name="porter-duff-concepts"></a>Porter-Duff 概念

假設 brownish 矩形會佔用您的顯示介面的左邊和前三分之二：

![Porter-Duff 目的地](porter-duff-images/PorterDuffDst.png "Porter-Duff 目的地")

這個區域稱為 [_目的地_] 或 [有時_背景_] 或 [背景 _]。_

您想要繪製下列矩形，這與目的地的大小相同。 矩形是透明的，不同之處在于 bluish 區域會佔據右和下三分之二：

![Porter-Duff 來源](porter-duff-images/PorterDuffSrc.png "Porter-Duff 來源")

這稱為「_來源_」或「有時_前景_」。

當您在目的地上顯示來源時，以下是您所預期的結果：

![Porter-Duff 來源](porter-duff-images/PorterDuffSrcOver.png "Porter-Duff 來源")

來源的透明圖元允許背景顯示，而 bluish 來源圖元則會遮蔽背景。 這是正常情況，在 SkiaSharp 中稱為 `SKBlendMode.SrcOver` 。 `BlendMode` `SKPaint` 第一次具現化物件時，該值是屬性的預設設定。

不過，您可以為不同的效果指定不同的 blend 模式。 如果您指定 `SKBlendMode.DstOver` ，則在來源與目的地相交的區域中，會顯示目的地，而不是來源：

![Porter-Duff 目的地](porter-duff-images/PorterDuffDstOver.png "Porter-Duff 目的地")

`SKBlendMode.DstIn`Blend 模式只會顯示目的地和來源使用目的地色彩交集的區域：

![Porter-Duff 目的地于](porter-duff-images/PorterDuffDstIn.png "Porter-Duff 目的地于")

Blend 模式（[ `SKBlendMode.Xor` 排除] 或 []）會導致兩個區域重迭時不會出現任何內容：

![Porter-Duff 獨佔 Or](porter-duff-images/PorterDuffXor.png "Porter-Duff 獨佔 Or")

彩色的目的地和來源矩形會有效地將顯示介面分割成四個唯一的區域，而這些區域可以用各種方式著色，以對應至目的地和來源矩形的存在：

![Porter-Duff](porter-duff-images/PorterDuff.png "Porter-Duff")

右上方和左下方的矩形一律是空白的，因為目的地和來源在這些區域中都是透明的。 [目的地色彩] 會佔用左上角區域，因此該區域可以使用 [目的地色彩] 或 [完全不] 來著色。 同樣地，來源色彩會佔用右下方的區域，因此該區域可以使用來源色彩來著色，或完全不使用。 在中間的目的地和來源的交集可以使用目的地色彩、來源色彩，或完全不加上。

組合總數為2（左上方）次2（適用于右下角）3（代表中心）或12。 這些是12個基本的 Porter-Duff 合成模式。

在撰寫_數位影像_（頁面256）的結尾，Porter 和 Duff 會新增名為_加號_的第13個模式（對應至 SkiaSharp `SKBlendMode.Plus` 成員和 w3c_較淡_的模式（這不會與 w3c_淡化_模式混淆）。此 `Plus` 模式會加入目的地和來源色彩，這是稍後將更詳細說明的程式。

Skia 會新增名 `Modulate` 為的第14個模式，其非常類似， `Plus` 不同之處在于目的地和來源色彩會相乘。 它可以被視為額外的 Porter Duff blend 模式。

以下是 SkiaSharp 中所定義的14個 Porter Duff 模式。 此表格顯示其如何為上圖中三個非空白區域的色彩著色：

| 模式       | Destination | 處 | 來源 |
| ---
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-----|：---標題：描述： ms-chap： assetid： author： ms. author： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------：（|）---標題：描述： ms-chap： assetid： author： ms. 作者： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

------：（|）---標題：描述： ms-chap： assetid： author： ms. 作者： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---:| |`Clear`    |            |             |       | |`Src`      |            |來源 |X | |`Dst`      |X |目的地 |       | |`SrcOver`  |X |來源 |X | |`DstOver`  |X |目的地 |X | |`SrcIn`    |            |來源 |       | |`DstIn`    |            |目的地 |       | |`SrcOut`   |            |             |X | |`DstOut`   |X |             |       | |`SrcATop`  |X |來源 |       | |`DstATop`  |            |目的地 |X | |`Xor`      |X |             |X | |`Plus`     |X |Sum |X | |`Modulate` |            |產品 |       | 

這些 blend 模式是對稱的。 可以交換來源和目的地，而且所有模式仍然可供使用。

模式的命名慣例遵循幾個簡單的規則：

- **Src**或**Dst**本身表示只能看到來源或目的地圖元。
- **Over**尾碼表示在交集中可見的內容。 來源或目的地已繪製「超過」另一個。
- **中**的尾碼表示只有交集才會彩色。 輸出僅限於來源或目的地的部分（也就是 "in"）。
- **Out**尾碼表示交集不是彩色的。 輸出只是來源或目的地的部分，屬於交集的「out」。
- 上方**的尾碼是** **In**和**Out**的聯集。其中包含來源或目的地「位於」另一個位置的區域。

請注意 `Plus` 和模式的差異 `Modulate` 。 這些模式會針對來源和目的地圖元執行不同類型的計算。 很快就會詳細說明這些功能。

[ **Porter-Duff] 格線**頁會以方格的形式，在一個畫面上顯示所有14個模式。 每個模式都是個別的實例 `SKCanvasView` 。 基於這個理由，類別衍生自 `SKCanvasView` 名為的 `PorterDuffCanvasView` 。 靜態的函式會建立兩個大小相同的點陣圖，一個在其左上角區域中具有 brownish 矩形，另一個則具有 bluish 矩形：

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

覆 `OnPaintSurface` 寫會繪製兩個位圖。 第一個是以一般方式繪製：

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

第二個是以物件繪製， `SKPaint` 其中 `BlendMode` 屬性已設定為函式引數：

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

覆寫的其餘部分會在 `OnPaintSurface` 點陣圖周圍繪製一個矩形，以指出其大小。

`PorterDuffGridPage`類別會建立十四個實例 `PorterDurffCanvasView` ，一個用於陣列的每個成員 `blendModes` 。 陣列中的成員順序與 `SKBlendModes` 資料表稍有不同，以定位彼此連續的類似模式。 的14個實例 `PorterDuffCanvasView` 會與中的標籤一起組織 `Grid` ：

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

您會想要說服自己的透明度對於 Porter-Duff blend 模式正常運作而言非常重要。 `PorterDuffCanvasView`類別總共包含三個對方法的呼叫 `Canvas.Clear` 。 它們全都使用無參數方法，將所有圖元設定為透明：

```csharp
canvas.Clear();
```

嘗試變更任何呼叫，讓圖元設定為不透明的白色：

```csharp
canvas.Clear(SKColors.White);
```

在這項變更之後，部分 blend 模式看起來會很有作用，但有些則不會。 如果您將來源點陣圖的背景設定為白色，則 `SrcOver` 模式無法使用，因為來源點陣圖中沒有透明圖元可讓目的地顯示。 如果您將目的地點陣圖或畫布的背景設定為白色，則 `DstOver` 無法使用，因為目的地不會有任何透明圖元。

在 [ **Porter-Duff] 格線**頁中，使用較簡單的呼叫來取代點陣圖可能會有一個誘惑 `DrawRect` 。 這適用于目的地矩形，但不適用於來源矩形。 來源矩形必須包含的不只是 bluish 彩色區域。 來源矩形必須包含對應至目的地之彩色區域的透明區域。 只有這樣的 blend 模式才會生效。

## <a name="using-mattes-with-porter-duff"></a>搭配 Porter 使用遮罩-Duff

[基礎構件 **-牆合成**] 頁面會顯示傳統合成工作的範例：必須從數個部分組合圖片，包括需要排除的背景點陣圖。 以下是具有有問題背景的**SeatedMonkey .jpg**點陣圖：

![上一個猴子](porter-duff-images/SeatedMonkey.jpg "上一個猴子")

在準備撰寫時，會建立對應的_遮罩_，這是您想要讓影像出現並以透明方式顯示的另一個點陣圖。 這個檔案的名稱是**SeatedMonkeyMatte** ，它是[**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例中**Media**資料夾內的資源：

![固定的猴子遮罩](porter-duff-images/SeatedMonkeyMatte.png "固定的猴子遮罩")

這_不_是建立專家的遮罩。 最理想的情況是，遮罩應包含黑色圖元邊緣周圍的部分透明圖元，而此遮罩則不會。

[基礎構件 **-牆合成**] 頁面的 XAML 檔案會具現化 `SKCanvasView` 和 `Button` ，以引導使用者完成組成最終影像的程式：

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

程式碼後置檔案會載入所需的兩個位圖，並處理的 `Clicked` 事件 `Button` 。 每次 `Button` 按下 `step` 時，欄位都會遞增，而且 `Text` 會針對設定新的屬性 `Button` 。 當 `step` 到達5時，它會設回0：

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

當程式第一次執行時，不會顯示任何內容，除了 `Button` ：

[![基礎構件-牆合成步驟0](porter-duff-images/BrickWallCompositing0.png "基礎構件-牆合成步驟0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

按 `Button` 一次會導致 `step` 遞增為1，且 `PaintSurface` 處理常式現在會顯示**SeatedMonkey**：

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

沒有 `SKPaint` 物件，因此沒有 blend 模式。 點陣圖會出現在畫面底部：

[![基礎構件-牆合成步驟1](porter-duff-images/BrickWallCompositing1.png "基礎構件-牆合成步驟1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

再按 `Button` 一次，並 `step` 將增加為2。 這是顯示**SeatedMonkeyMatte .png**檔案的重要步驟：

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

Blend 模式是 `SKBlendMode.DstIn` ，這表示目的地將會保留在對應至來源非透明區域的區域中。 對應至原始點陣圖之目的地矩形的其餘部分會變成透明：

[![基礎構件-牆合成步驟2](porter-duff-images/BrickWallCompositing2.png "基礎構件-牆合成步驟2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

背景已移除。 

下一個步驟是繪製類似于猴子所在人行道的矩形。 此人行道的外觀是以兩個著色器的組合為基礎：純色著色器和 Perlin 雜訊著色器：

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

因為此人行道必須在猴子背後，所以 blend 模式是 `DstOver` 。 目的地只會出現在背景為透明的位置：

[![基礎構件-牆合成步驟3](porter-duff-images/BrickWallCompositing3.png "基礎構件-牆合成步驟3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

最後一個步驟是加入基礎構件牆。 程式會使用可做為類別中靜態屬性的基礎構件-牆點陣圖磚 `BrickWallTile` `AlgorithmicBrickWallPage` 。 轉譯轉換會加入至 `SKShader.CreateBitmap` 呼叫中，以將磚移位，讓底部的資料列成為完整的磚：

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

為了方便起見， `DrawRect` 此呼叫會在整個畫布上顯示此著色器，但 `DstOver` 模式會將輸出限制為只有畫布的區域仍然是透明的：

[![基礎構件-牆合成步驟4](porter-duff-images/BrickWallCompositing4.png "基礎構件-牆合成步驟4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

顯然有其他方法可以撰寫這個場景。 它可以從背景開始，並在前景前進。 但使用 blend 模式可讓您擁有更大的彈性。 特別是，使用遮罩可讓點陣圖的背景從組成的場景中排除。

如同您在[使用路徑和區域裁剪](../../curves/clipping.md)文章中所學到的， `SKCanvas` 類別會定義三種類型的裁剪，其對應于 [`ClipRect`](xref:SkiaSharp.SKCanvas.ClipRect*) 、 [`ClipPath`](xref:SkiaSharp.SKCanvas.ClipPath*) 和 [`ClipRegion`](xref:SkiaSharp.SKCanvas.ClipRegion*) 方法。 Porter-Duff blend 模式會加入另一種裁剪類型，這可讓您將影像限制為您可以繪製的任何專案，包括點陣圖。 基礎構件 **-牆複合**中使用的遮罩基本上會定義裁剪區域。

## <a name="gradient-transparency-and-transitions"></a>梯度透明度和轉換

本文稍早所示的 Porter-Duff blend 模式範例包含不透明圖元和透明圖元的所有相關影像，但不含部分透明圖元。 Blend 模式函數也會針對這些圖元進行定義。 下表是 Porter-Duff blend 模式的更正式定義，其使用在 Skia [**SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)中找到的標記法。 （因為**SkBlendMode 參考**是 Skia 參考，所以會使用 c + + 語法）。

在概念上，每個圖元的紅色、綠色、藍色和 Alpha 元件都會從位元組轉換為0到1範圍內的浮點數。 針對 Alpha 色板，0是完全透明，1是完全不透明

下表中的標記法會使用下列縮寫：

- **Da**是目的地 Alpha 色板
- **Dc**是目的地 RGB 色彩
- **Sa**是來源 Alpha 色板
- **Sc**是來源 RGB 色彩

RGB 色彩會以 Alpha 值乘以。 例如，如果**Sc**代表純紅色，但**Sa**是0x80，則 RGB 色彩會是 **（0x80，0，0）**。 如果**Sa**為0，則所有 RGB 元件也都是零。

結果會以括弧括住，並以逗號分隔 RGB 色彩： **[Alpha，color]**。 針對色彩，會分別針對紅色、綠色和藍色元件執行計算：

| 模式       | 作業 |
| ---
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

----- |---標題：描述： ms. 生產： assetid： author： ms. author： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
標題：描述： ms-chap： ms. assetid： author： ms-chap： ms. date： no-loc：
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

----- | |`Clear`    |[0，0] | |`Src`      |[Sa，Sc] | |`Dst`      |[Da，Dc] | |`SrcOver`  |[Sa + Da ·（1-Sa），Sc + Dc ·（1-Sa） | |`DstOver`  |[Da + Sa ·（1– Da），Dc + Sc ·（1– Da） | |`SrcIn`    |SaDa，Sc ·Da] | |`DstIn`    |特裡斯坦Sa、Dc ·Sa] | |`SrcOut`   |Sa（1– Da），Sc ·（1– Da）]| |`DstOut`   |特裡斯坦（1-Sa），Dc ·（1-Sa）]| |`SrcATop`  |[Da，Sc ·Da + Dc ·（1-Sa）]| |`DstATop`  |[Sa，Dc ·Sa + Sc ·（1– Da）]| |`Xor`      |[Sa + Da –2·SaDa，Sc ·（1– Da） + Dc ·（1-Sa）]| |`Plus`     |[Sa + Da，Sc + Dc] | |`Modulate` |SaDa，Sc ·Dc] | 

當**Da**和**Sa**為0或1時，這些作業比較容易分析。 例如，針對預設 `SrcOver` 模式，如果**Sa**為0，則**Sc**也是0，而結果為 **[Da，Dc]**，目的地 Alpha 和色彩。 如果**Sa**是1，則結果為 **[sa，Sc]**、來源 Alpha 和色彩，或 **[1，Sc]**。

`Plus`和 `Modulate` 模式有點不同，因為來源和目的地的組合可能會產生新的色彩。 您 `Plus` 可以使用位元組元件或浮點元件來解讀模式。 在稍早所示的**Porter-Duff Grid**頁面中，目的地色彩為 **（0xC0，0x80，0x00）** ，而來源色彩為 **（0x00，0x80，0xC0）**。 會新增每一對元件，但總和會壓制為0xFF。 其結果為 color **（0xC0，0xff，0xC0）**。 這是交集中所顯示的色彩。

針對 `Modulate` 模式，RGB 值必須轉換成浮點。 目的地色彩為 **（0.75、0.5、0）** ，而來源為 **（0，0.5，0.75）**。 RGB 元件會彼此相乘，結果會是 **（0，0.25，0）**。 這是此模式的 [ **Porter-Duff] 格線**頁交集中所顯示的色彩。

[ **Porter-Duff 透明度**] 頁面可讓您檢查 Porter Duff blend 模式在部分透明的繪圖物件上的運作方式。 XAML 檔案包含 `Picker` 具有 Porter Duff 模式的：

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

程式碼後置檔案會使用線性漸層來填滿兩個相同大小的矩形。 目的漸層是從右上角到左下方。 它會 brownish 在右上角，然後朝向中心開始變淡到透明，而且在左下角是透明的。 

來源矩形的漸層從左上到右下。 左上角是 bluish，但會再次淡入為透明，而且在右下角是透明的。 

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

此程式示範 Porter-Duff blend 模式可以與點陣圖以外的繪圖物件搭配使用。 不過，來源必須包含透明區域。 這種情況是因為漸層填滿矩形，但漸層的一部分是透明的。

以下是三個範例：

[![Porter-Duff 透明度](porter-duff-images/PorterDuffTransparency.png "Porter-Duff 透明度")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

目的地和來源的設定與原始 Porter-Duff[_複合數位影像_](https://graphics.pixar.com/library/Compositing/paper.pdf)紙張的頁面255中所顯示的圖表非常類似，但此頁面會示範 blend 模式在部分透明度的區域中是正常運作的。

您可以使用透明漸層來進行一些不同的效果。 其中一個可能是遮罩，類似于 [ **SkiaSharp 迴圈**漸層] 頁面的 [星形漸層[**] 區段中**](../shaders/circular-gradients.md#radial-gradients-for-masking)所示的技術。 大部分的 [**組合遮罩**] 頁面與先前的程式類似。 它會載入點陣圖資源，並決定要在其中顯示的矩形。 放射狀漸層是根據預先決定的中心和半徑而建立：

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

與這個程式的差異在於，漸層的開頭為黑色，並以透明結束。 它會顯示在具有 blend 模式的點陣圖上 `DstIn` ，這只會在來源不是透明的區域中顯示目的地。

呼叫之後 `DrawRect` ，畫布的整個表面都是透明的，但放射狀漸層所定義的圓形除外。 最後進行的呼叫如下：

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

畫布的所有透明區域都會以粉紅色呈現：

[![合成 Mask](porter-duff-images/CompositingMask.png "合成 Mask")](porter-duff-images/CompositingMask-Large.png#lightbox)

您也可以使用 Porter-Duff 模式和部分透明的漸層，從一個影像轉換成另一個。 [漸層**轉換**] 頁面包含， `Slider` 以表示從0轉換到1的進度等級，以及 `Picker` 選擇您想要的轉換類型：

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

程式碼後置檔案會載入兩個位圖資源，以示範轉換。 這些是本文稍早的**點陣圖溶解**頁面中使用的兩個影像。 程式碼也會定義具有三個成員的列舉，其對應至線性、星形和清除的三種漸層類型 &mdash; 。 這些值會載入至 `Picker` ：

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

程式碼後置檔案會建立三個 `SKPaint` 物件。 `paint0`物件不會使用 blend 模式。 這個油漆物件是用來繪製一個矩形，其中的漸層會從黑色變成透明，如陣列中所示 `colors` 。 `positions`陣列是以的位置為基礎 `Slider` ，但在某些情況下會進行調整。 如果 `Slider` 是最小或最大值，則 `progress` 值為0或1，而且兩個位圖中的其中一個應該是完整可見的。 `positions`必須針對那些值設定陣列。

如果 `progress` 值為0，則 `positions` 陣列包含值-0.1 和0。 SkiaSharp 會將第一個值調整為等於0，這表示漸層僅在0且為透明，否則為透明化。 當 `progress` 為0.5 時，陣列會包含值0.45 和0.55。 漸層是從0到0.45 的黑色，然後轉換成透明，而從0.55 到1的效果完全透明。 當 `progress` 是1時， `positions` 陣列是1和1.1，這表示漸層是從0到1的黑色。

`colors`和 `position` 陣列同時用於建立漸層的三種方法 `SKShader` 。 根據選取專案，只會建立其中一個著色器 `Picker` ： 

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

該漸層會在沒有 blend 模式的矩形中顯示。 在該 `DrawRect` 呼叫之後，畫布只會包含從黑色到透明的漸層。 以較高的值增加的黑色量 `Slider` 。

在處理常式的最後四個語句中 `PaintSurface` ，會顯示兩個位圖。 `SrcOut`Blend 模式表示第一個點陣圖只會顯示在背景的透明區域中。 `DstOver`第二個位圖的模式表示第二個位圖只會顯示在第一個點陣圖未顯示的區域中。

下列螢幕擷取畫面顯示三種不同的轉換類型，每個都在 50% mark：

[![漸層轉換](porter-duff-images/GradientTransitions.png "漸層轉換")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
