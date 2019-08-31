---
title: Porter Duff 混合模式
description: 您可以使用 Porter Duff 混合模式來撰寫來源和目的地的映像為基礎的場景。
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 57F172F8-BA03-43EC-A215-ED6B78696BB5
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: c15dd4606a75cc3cdffbad71f15299568157213a
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199879"
---
# <a name="porter-duff-blend-modes"></a>Porter Duff 混合模式

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Porter Duff 混合模式會命名為 Thomas Porter 和 Tom Duff，負責開發的複合 （compositing） 時運作 Lucasfilm 代數之後。 其紙張[_複合 （compositing） 的數位映像_](https://graphics.pixar.com/library/Compositing/paper.pdf) 1984 年 7 月號的已發行_電腦圖形中_，頁面為 253 超過 259。 這些混合模式是不可或缺的複合 （compositing），這複合的場景組合各種映像：

![Porter Duff 範例](porter-duff-images/PorterDuffSample.png "Porter Duff 範例")

## <a name="porter-duff-concepts"></a>Porter Duff 概念

假設 brownish 矩形佔用左端和頂端三分之二的顯示介面：

![Porter Duff 目的地](porter-duff-images/PorterDuffDst.png "Porter Duff 目的地")

這個區域稱為_目的地_有時也_背景_或是_背景_。

您想要繪製下列的矩形中，是相同大小的目的地。 矩形是透明佔用的右側和底部三分之二的藍區域除外：

![Porter Duff 來源](porter-duff-images/PorterDuffSrc.png "Porter Duff 來源")

這就叫做_來源_有時也_前景_。

當您在目的地上顯示來源時，以下是您的預期：

![Porter Duff 原始檔停留](porter-duff-images/PorterDuffSrcOver.png "Porter Duff 原始檔停留")

透明的像素的來源允許藍來源像素遮住背景時，透過顯示背景。 一般情況下，且它指為 SkiaSharp `SKBlendMode.SrcOver`。 值是預設值`BlendMode`屬性時`SKPaint`先具現化物件。

不過，就可以指定不同的 blend 模式不同的效果。 如果您指定`SKBlendMode.DstOver`，則在區域中的來源和目的地相交的地方，目的地會出現而非來源：

![透過 Porter Duff 目的地](porter-duff-images/PorterDuffDstOver.png "透過 Porter Duff 目的地")

`SKBlendMode.DstIn`混合模式會顯示只有目的地和來源相交的地方使用目的色彩的區域：

![中的 Porter Duff 目的地](porter-duff-images/PorterDuffDstIn.png "Porter Duff 目的地")

混合模式`SKBlendMode.Xor`(互斥 OR) 會導致不會出現重疊的兩個領域的地方：

![Porter Duff 獨佔或者](porter-duff-images/PorterDuffXor.png "Porter Duff 獨佔或")

有效地彩色的目的地和來源矩形會將顯示表面分成四個唯一的區域會以各種方式對應至目的地和來源矩形的目前狀態中：

![Porter Duff](porter-duff-images/PorterDuff.png "Porter Duff")

右上方和左下角的矩形永遠是空白的因為目的地和來源是透明的區域。 目的色彩會佔用左上角區域中，以便區域可能是彩色，與目的色彩或不完全。 同樣地，來源色彩會佔用右下方區域中，以便可以彩色區域，以來源色彩或不完全。 與目的色彩，其來源色彩，或完全未，可以上在中間的來源與目的地的交集。

組合的總數是 2 （代表左上角），乘以 2 (右下方） 時間 （適用於中心），3 或 12。 這些是 12 的基本 Porter Duff 複合 （compositing） 模式。

月底起_複合 （compositing） 的數位映像_（頁面 256） Porter 和 Duff 新增了 13 的模式，稱為_加上_(對應至 SkiaSharp`SKBlendMode.Plus`成員和 W3C_較淺_模式 (也就是不與 W3C 混淆_淡化_模式。)這`Plus`模式加入目的地和來源色彩會更多詳細資料中稍後說明的程序。

Skia 新增了 14 的模式，稱為`Modulate`非常類似`Plus`不同之處在於相乘的目的地和來源色彩。 它可以視為額外的 Porter Duff 混合模式。

以下是 14 Porter Duff 模式 SkiaSharp 中所定義。 下表顯示它們對每個在上圖中的三個非空白區域所做的色彩：

| 模式       | 目的地 | 交集 | 原始程式檔 |
| ---------- |:-----------:|:------------:|:------:|
| `Clear`    |             |              |        |
| `Src`      |             | 原始程式檔       | X      |
| `Dst`      | X           | 目的地  |        |
| `SrcOver`  | X           | 原始程式檔       | X      |
| `DstOver`  | X           | 目的地  | X      |
| `SrcIn`    |             | 原始程式檔       |        |
| `DstIn`    |             | 目的地  |        |
| `SrcOut`   |             |              | X      |
| `DstOut`   | X           |              |        |
| `SrcATop`  | X           | 原始程式檔       |        |
| `DstATop`  |             | 目的地  | X      |
| `Xor`      | X           |              | X      |
| `Plus`     | X           | Sum          | X      |
| `Modulate` |             | 產品      |        | 

這些混合模式是對稱的。 可交換的來源和目的地，而且仍然可以使用所有的模式。

模式的命名慣例遵循幾個簡單的規則：

- **Src**或是**Dst**本身表示，只有來源或目的地的像素是可見。
- **透過**後置詞表示項目會顯示在交集。 「 結束 」 另繪製來源或目的地其中之一。
- **在**後置詞表示著色不只有交集。 輸出會限制只有在來源或目的地。"in"其他部分。
- **出**後置詞表示交集不會以著色。 輸出是只有在來源或目的地。 「 發送 」 交集的部分。
- **之上**後置字元是等位**中**並**出**。它包含其他的來源或目的地其中是 「 之上 」 的區域。

請注意的差異`Plus`和`Modulate`模式。 這些模式的來源和目的地的像素上執行不同類型的計算。 在短時間內所述更多詳細資料。

**Porter Duff 方格**頁面會顯示在方格的表單中的某個畫面上的所有 14 的模式。 每個模式是個別的執行個體`SKCanvasView`。 基於這個理由，類別衍生自`SKCanvasView`名為`PorterDuffCanvasView`。 靜態建構函式會建立兩個相同大小，一個 brownish 的矩形，其左上角區域中，另一個以藍矩形的點陣圖：

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

執行個體建構函式具有類型參數的`SKBlendMode`。 它會將這個參數儲存在欄位中。 

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

`OnPaintSurface`覆寫繪製兩個點陣圖。 第一個是通常繪製：

```csharp
canvas.DrawBitmap(dstBitmap, rect);
```

第二個來描繪`SKPaint`物件，`BlendMode`屬性已設定為建構函式引數：

```csharp
using (SKPaint paint = new SKPaint())
{
    paint.BlendMode = blendMode;
    canvas.DrawBitmap(srcBitmap, rect, paint);
}
```

其餘部分`OnPaintSurface`覆寫周圍繪製矩形的點陣圖，以指出其大小。

`PorterDuffGridPage`類別建立的 14 個執行個體`PorterDurffCanvasView`，其中的每個成員`blendModes`陣列。 順序`SKBlendModes`陣列中的成員是稍有不同於資料表，以便將彼此相鄰的類似模式。 14 個執行個體`PorterDuffCanvasView`組織中的標籤以及`Grid`:

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

結果如下：

[![Porter Duff 方格](porter-duff-images/PorterDuffGrid.png "Porter Duff 方格")](porter-duff-images/PorterDuffGrid-Large.png#lightbox)

您會想要說服自己透明度至關重要 Porter Duff 混合模式的正常運作。 `PorterDuffCanvasView`類別包含三個呼叫的總計`Canvas.Clear`方法。 所有人都使用無參數方法，它會設定為透明的所有像素：

```csharp
canvas.Clear();
```

請嘗試變更任何這些呼叫，以便設定為不透明的白色像素：

```csharp
canvas.Clear(SKColors.White);
```

遵循這項變更，混合模式的一些看似運作，但其他人不會。 如果您設定的來源點陣圖的背景為白色，則`SrcOver`模式沒有作用，因為沒有不透明的像素為單位，以便透過顯示目的地的來源點陣圖中。 如果您設定的目的地點陣圖或畫布，然後白色背景`DstOver`沒有作用，因為目的地沒有任何透明的像素為單位。

可能有嘗試將取代中的點陣圖**Porter Duff 方格**頁面，並且更簡單`DrawRect`呼叫。 可針對目的矩形，但不適用於來源矩形。 來源矩形必須包含不只是藍彩色區域。 來源矩形必須包含對應至目的地的彩色區域中的透明區域。 這些則只會混合模式工作。

## <a name="using-mattes-with-porter-duff"></a>使用 Porter Duff mattes

[基礎構件 **-牆合成**] 頁面會顯示傳統撰寫工作的範例:圖片必須從數個部分組成, 包括具有需要排除之背景的點陣圖。 以下是**SeatedMonkey.jpg**有問題的背景點陣圖：

![插入擴充槽 Monkey](porter-duff-images/SeatedMonkey.jpg "插入擴充槽 Monkey")

在複合 （compositing），相對應的準備_草蓆_已建立，這是另一個因黑色，您想要顯示的影像和透明的點陣圖。 此檔案名為**SeatedMonkeyMatte.png**中的資源之間，且**媒體**中的資料夾[ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)範例:

![插入擴充槽 Monkey 草蓆](porter-duff-images/SeatedMonkeyMatte.png "插入擴充槽 Monkey 草蓆")

這是_不_評閱建立草蓆。 最佳情況是，草蓆應該包括邊緣的黑色像素，部分透明像素為單位，以及此草蓆則否。

XAML 檔案**磚牆複合**頁面會具現化`SKCanvasView`和`Button`，引導使用者進行撰寫最終映像的程序：

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

程式碼後置檔案會載入，它必須處理的兩個點陣圖`Clicked`事件的`Button`。 針對每個`Button`按一下此項目，`step`欄位會遞增，並使用新`Text`屬性會設為`Button`。 當`step`達到 5，它設回 0:

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

程式第一次執行時，不會顯示除了`Button`:

[![磚牆複合 （compositing） 步驟 0](porter-duff-images/BrickWallCompositing0.png "磚牆複合 （compositing） 步驟 0")](porter-duff-images/BrickWallCompositing0-Large.png#lightbox)

按下`Button`一次會造成`step`遞增為 1，而`PaintSurface`處理常式現在會顯示**SeatedMonkey.jpg**:

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

沒有任何`SKPaint`物件，因此沒有混合模式。 點陣圖會出現在畫面底部：

[![磚牆複合 （compositing） 步驟 1](porter-duff-images/BrickWallCompositing1.png "磚牆複合 （compositing） 步驟 1")](porter-duff-images/BrickWallCompositing1-Large.png#lightbox)

按下`Button`再次和`step`遞增數字 2。 這是很重要的步驟，顯示**SeatedMonkeyMatte.png**檔案：

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

混合模式是`SKBlendMode.DstIn`，這表示會保留在對應至來源的不透明區域的區域中的目的地。 對應至原始點陣圖的目的矩形的其餘部分會變成透明項目：

[![磚牆複合 （compositing） 步驟 2](porter-duff-images/BrickWallCompositing2.png "磚牆複合 （compositing） 步驟 2")](porter-duff-images/BrickWallCompositing2-Large.png#lightbox)

已移除背景。 

下一個步驟是繪製類似 monkey 正坐在人行道的矩形。 此人行道的外觀根據兩個著色器的組合： 單色著色器和 Perlin 雜訊著色器：

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

因為此人行道必須 monkey 背後，混合模式是`DstOver`。 目的地會出現只有其中背景是透明：

[![磚牆複合 （compositing） 步驟 3](porter-duff-images/BrickWallCompositing3.png "磚牆複合 （compositing） 步驟 3")](porter-duff-images/BrickWallCompositing3-Large.png#lightbox)

最後一個步驟加入阻礙。 程式會使用為靜態屬性的阻礙點陣圖 圖格可用`BrickWallTile`在`AlgorithmicBrickWallPage`類別。 轉譯轉換加入至`SKShader.CreateBitmap`移位磚，使最下方的列是完整的圖格的呼叫：

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

為了方便起見，`DrawRect`呼叫會顯示此著色器，透過整張畫布中，但`DstOver`模式將輸出限制為只有仍十分畫布的區域：

[![磚牆複合 （compositing） 步驟 4](porter-duff-images/BrickWallCompositing4.png "磚牆複合 （compositing） 步驟 4")](porter-duff-images/BrickWallCompositing4-Large.png#lightbox)

很明顯有其他方式來撰寫此場景。 它可以開始背景和前景進行建置。 但使用混合模式可讓您更大的彈性。 特別是，使用草蓆可讓要排除在組合場景點陣圖的背景。

當您了解本文[使用路徑和地區裁剪](../../curves/clipping.md)，則`SKCanvas`類別會定義三種類型的裁剪，對應至[ `ClipRect` ](xref:SkiaSharp.SKCanvas.ClipRect*)， [ `ClipPath` ](xref:SkiaSharp.SKCanvas.ClipPath*)，並[ `ClipRegion` ](xref:SkiaSharp.SKCanvas.ClipRegion*)方法。 Porter Duff blend 模式新增另一種可讓您限制任何項目，您可以繪製，包括點陣圖影像的裁剪。 用於草蓆**磚牆複合 （compositing)** 基本上定義裁剪區域。

## <a name="gradient-transparency-and-transitions"></a>漸層的透明度和轉換

Porter Duff 範例 blend 稍早所示在這篇文章中有所有相關的不透明的像素和透明的像素，但不是透明的像素包含的映像的模式。 混合模式函式會定義以及在這些像素。 下表是更正式的 Porter Duff blend 模式定義使用 Skia 中找到的標記法[ **SkBlendMode 參考**](https://skia.org/user/api/SkBlendMode_Reference)。 (因為**SkBlendMode 參考**是 Skia 參考資料，使用 c + + 語法。)

就概念而言，每個像素的紅色、 綠色、 藍色和 alpha 元件會從位元組轉換成在範圍 0 到 1 的浮點數。 Alpha 色頻，0 為完全透明，1 為完全不透明

下表中的標記會使用下列縮寫：

- **Da**是目的地 alpha 色板
- **Dc**是 RGB 色彩的目的地
- **Sa**是來源 alpha 色板
- **Sc**是 RGB 色彩的來源

RGB 色彩會先乘以 alpha 值。 例如，如果**Sc**代表純紅色但**Sa** 0x80，則是 RGB 色彩 **(0x80，0，0)** 。 如果**Sa**為 0，則所有的 RGB 元件也是零。

結果顯示在以逗號分隔的 RGB 色彩與 alpha 色頻的括號中： **[alpha 色彩]** 。 使用色彩，計算被分開的紅色、 綠色和藍色元件：

| 模式       | 運算 |
| ---------- | --------- |
| `Clear`    | [0，0]    |
| `Src`      | [Sa，Sc]  |
| `Dst`      | [Da，Dc]  |
| `SrcOver`  | [Sa + Da·(1 – Sa)，Sc + Dc·(1 – Sa) | 
| `DstOver`  | [資料 + Sa·(1 – Da)，Dc + Sc·(1 – Da) |
| `SrcIn`    | [Sa·Da Sc·Da] |
| `DstIn`    | [Da·Sa Dc·Sa] |
| `SrcOut`   | [Sa·(1 – Da)，Sc·(1 – Da)] |
| `DstOut`   | [Da·(1 – Sa)，Dc·(1 – Sa)] |
| `SrcATop`  | [Da Sc·Da + Dc·(1 – Sa)] |
| `DstATop`  | [Sa Dc·Sa + Sc·(1 – Da)] |
| `Xor`      | [Sa + Da – 2·Sa·Da Sc·(1 – Da) + Dc·(1 – Sa)] |
| `Plus`     | [Sa + Da、 Sc + Dc] |
| `Modulate` | [Sa·Da Sc·Dc] | 

這些運算全都是分析時的工作變得更容易**Da**並**Sa**是 0 或 1。 例如，若為預設值`SrcOver`模式中，如果**Sa**為 0，則**Sc**是 0，結果也是 **[Da，Dc]** ，目的地 alpha 和色。 如果**Sa**為 1，就會產生 **[Sa，Sc]** ，來源 alpha 和色，或 **[1，Sc]** 。

`Plus`和`Modulate`模式會與其他稍有不同，因為新的色彩可能是因為來源和目的地的組合。 `Plus`模式可以解譯為位元組的元件或浮點數的元件。 在**Porter Duff 方格**目的色彩稍早顯示的頁面 **(0xC0、 0x80，0x00)** 來源色彩，而且 **(0x00，0x80，0xC0)** 。 加入元件的每一對，但在 0xFF 限制的總和。 結果為色彩 **(0xC0，0xFF，0xC0)** 。 這是交集 所示的色彩。

針對`Modulate`模式中，必須轉換為浮點數的 RGB 值。 目的地色彩就 **（0.75，0.5，0）** 來源，而且 **（0，0.5，0.75）** 。 RGB 元件是每個相乘，而且結果 **（0，0.25，0）** 。 這是在交集 所示的色彩**Porter Duff 方格**這個模式中的頁面。

**Porter Duff 透明度**頁面可讓您檢查 Porter Duff blend 模式部分透明的圖形物件上的運作方式。 XAML 檔案包含`Picker`Porter Duff 模式使用：

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

程式碼後置檔案會填滿使用線形漸層的大小相同的兩個矩形。 由右至左下方，從右上角是目的地漸層。 它是 brownish 右上角然後朝中央開始為透明，淡出，但而言是透明的左下角。 

來源矩形具有從左上到右下的漸層。 左上角藍但一次會逐漸淡化為透明的且透明的右下角。 

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

此程式示範 Porter Duff blend 模式可以搭配點陣圖以外的圖形物件。 不過，來源必須包含透明的區域。 因為漸層填滿的矩形中，但是是透明的漸層的一部分，這會是以下的案例。

以下是三個範例：

[![Porter Duff 透明度](porter-duff-images/PorterDuffTransparency.png "Porter Duff 透明度")](porter-duff-images/PorterDuffTransparency-Large.png#lightbox)

目的地和來源的組態是非常類似於原始的 Porter Duff 255 頁面所示的圖表[_複合 （compositing） 的數位映像_](https://graphics.pixar.com/library/Compositing/paper.pdf)紙張，但是此頁面會示範混合模式是正常的部分透明的區域。

您可以使用透明的漸層之一些不同的效果。 其中一個可能性遮罩，如下所示的技巧[**進行遮罩處理的放射狀漸層**](../shaders/circular-gradients.md#radial-gradients-for-masking)一節**SkiaSharp 循環的漸層頁面**。 大部分**複合 （compositing） 遮罩**頁面是類似於該較早的程式。 它會載入點陣圖資源，並判斷在其中顯示它的矩形。 放射狀漸層會根據建立預先決定的中心和半徑：

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

此程式的差異是漸層中心黑色的開頭和結尾透明度。 它會顯示在混合模式中的點陣圖`DstIn`，只能在來源的不透明的區域中顯示目的地。

之後`DrawRect`呼叫中，畫布的整個介面是透明除了以放射狀漸層定義圓形。 進行最後的呼叫：

```csharp
canvas.DrawColor(SKColors.Pink, SKBlendMode.DstOver);
```

粉紅色會到畫布上的所有透明區域：

[![複合 （compositing） 遮罩](porter-duff-images/CompositingMask.png "複合 （compositing） 遮罩")](porter-duff-images/CompositingMask-Large.png#lightbox)

您也可以使用轉換到另一個映像 Porter Duff 模式和部分透明的漸層。 **漸層停駐的轉換**頁面包含`Slider`表示進行中的層級為 1，從 0 轉換和`Picker`選擇您想要的轉換類型：

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

程式碼後置檔案會載入示範轉換的兩個點陣圖資源。 這些是相同的兩個映像，以用於**點陣圖溶解**稍早在本文中的頁面。 程式碼也會列舉型別定義具有三個成員對應至三種類型的漸層&mdash;線性、 星形、 和掃掠。 這些值會載入`Picker`:

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

程式碼後置檔案會建立三個`SKPaint`物件。 `paint0`物件不會使用混合模式。 這個小畫家物件用來繪製具有漸層從黑色到透明中所示的矩形`colors`陣列。 `positions`陣列為基礎的位置`Slider`，但稍微調整。 如果`Slider`位於其最小值或最大值，`progress`的值為 0 或 1，而且兩個點陣圖的其中一個應該完全可見。 `positions`陣列必須據以設定這些值。

如果`progress`值為 0，則`positions`陣列包含-0.1，0 的值。 SkiaSharp 否則將會調整為等於 0，表示漸層是黑色只能在 0 和透明的第一個值。 當`progress`為 0.5，則陣列包含 0.45 和 0.55 的值。 漸層是從 0 到 0.45，黑色會轉換成透明的然後是完全透明的 0.55 為 1。 當`progress`為 1，`positions`陣列是 1 和 1.1 版，這表示，梯度是從 0 到 1 黑色。

`colors`並`position`兩者都使用三個方法的陣列`SKShader`，建立漸層。 只有其中一個這些著色器會根據建立`Picker`選取項目： 

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

中的矩形，而不需要混合模式會顯示該漸層。 在那之後`DrawRect`畫布的呼叫，只會包含從黑色到透明的漸層。 黑色的數量會增加與更高版本`Slider`值。

中的最後四個陳述式`PaintSurface`處理常式，會顯示兩個點陣圖。 `SrcOut` Blend 模式表示只會在背景透明區域中會顯示第一個點陣圖。 `DstOver`第二個點陣圖的模式表示其中的第一個點陣圖不會顯示這些區域中，只會顯示第二個點陣圖。

下列螢幕擷取畫面顯示三種不同的轉換類型，每一個在 50%的標示處：

[![轉換漸層停駐](porter-duff-images/GradientTransitions.png "漸層停駐的轉換")](porter-duff-images/GradientTransitions-Large.png#lightbox)

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
