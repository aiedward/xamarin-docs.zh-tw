---
title: SkiaSharp 中的路徑和文字
description: 本文將探討 SkiaSharp 路徑和文字的交集，並使用範例程式碼來示範這一點。
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c78d6777db4e8dcc3a8302d418f1c87440dc90a5
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562050"
---
# <a name="paths-and-text-in-skiasharp"></a>SkiaSharp 中的路徑和文字

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_探索路徑和文字的交集_

在新式圖形系統中，文字字型是字元外框的集合，通常是由二次方貝茲曲線所定義。 因此，許多新式圖形系統都包含可將文字字元轉換成圖形路徑的功能。

您已經看過，您可以對文字字元的外框進行筆劃，也可以填滿文字字元。 這可讓您以特定筆劃寬度顯示這些字元的外框，甚至是路徑 [**效果**](effects.md) 一文所述的路徑效果。 但是，您也可以將字元字串轉換成 `SKPath` 物件。 這表示文字外框可以用來裁剪以「 [**使用路徑和區域裁剪**](clipping.md) 」一文所述的技術。

除了使用路徑效果來筆觸字元外框外，您也可以建立路徑效果，其以衍生自字元字串的路徑為基礎，而且您甚至可以結合這兩種效果：

![文字路徑效果](text-paths-images/pathsandtextsample.png)

在先前的 [**路徑效果**](effects.md)文章中，您已瞭解如何 [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) 取得的方法 `SKPaint` 可以取得邊邊路徑的外框。 您也可以使用此方法搭配衍生自字元外框的路徑。

最後，本文將示範路徑和文字的另一個交集：的 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 方法可 `SKCanvas` 讓您顯示文字字串，讓文字的基準遵循曲線路徑。

## <a name="text-to-path-conversion"></a>文字到路徑的轉換

的 [`GetTextPath`](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single)) 方法會 `SKPaint` 將字元字串轉換成 `SKPath` 物件：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和 `y` 引數表示文字左側基準的起點。 它們在此在中扮演的角色與的 `DrawText` 方法相同 `SKCanvas` 。 在路徑中，文字左邊的基準會有座標 (x，y) 。

`GetTextPath`如果您只想要填滿或繪製結果路徑，則會麻煩方法。 一般 `DrawText` 方法可讓您這樣做。 `GetTextPath`方法對於其他涉及路徑的工作更有用。

其中一項工作是剪輯。 [ **裁剪文字** ] 頁面會根據 "CODE" 這個字的字元外框來建立裁剪路徑。 此路徑會延伸至頁面大小，以裁剪包含 **裁剪文字** 原始程式碼影像的點陣圖：

[![裁剪文字頁面的三重螢幕擷取畫面](text-paths-images/clippingtext-small.png)](text-paths-images/clippingtext-large.png#lightbox "裁剪文字頁面的三重螢幕擷取畫面")

類別的函式 [`ClippingTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs) 會將儲存為內嵌資源的點陣圖載入至方案的 [ **Media** ] 資料夾中：

```csharp
public class ClippingTextPage : ContentPage
{
    SKBitmap bitmap;

    public ClippingTextPage()
    {
        Title = "Clipping Text";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        string resourceID = "SkiaSharpFormsDemos.Media.PageOfCode.png";
        Assembly assembly = GetType().GetTypeInfo().Assembly;

        using (Stream stream = assembly.GetManifestResourceStream(resourceID))
        {
            bitmap = SKBitmap.Decode(stream);
        }
    }
    ...
}
```

`PaintSurface`處理常式一開始會建立 `SKPaint` 適合文字的物件。 除了 `Typeface` 這個特定的應用程式，此屬性也會設定為 `TextSize` `TextSize` 任意屬性。 另請注意，沒有任何 `Style` 設定。

`TextSize`和 `Style` 屬性設定並不是必要的，因為此 `SKPaint` 物件僅用於 `GetTextPath` 使用文字字串 "CODE" 的呼叫。 然後，處理常式會測量結果 `SKPath` 物件，並套用三個轉換來將其置中，並將其調整為頁面的大小。 然後可以將路徑設定為裁剪路徑：

```csharp
public class ClippingTextPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Blue);

        using (SKPaint paint = new SKPaint())
        {
            paint.Typeface = SKTypeface.FromFamilyName(null, SKTypefaceStyle.Bold);
            paint.TextSize = 10;

            using (SKPath textPath = paint.GetTextPath("CODE", 0, 0))
            {
                // Set transform to center and enlarge clip path to window height
                SKRect bounds;
                textPath.GetTightBounds(out bounds);

                canvas.Translate(info.Width / 2, info.Height / 2);
                canvas.Scale(info.Width / bounds.Width, info.Height / bounds.Height);
                canvas.Translate(-bounds.MidX, -bounds.MidY);

                // Set the clip path
                canvas.ClipPath(textPath);
            }
        }

        // Reset transforms
        canvas.ResetMatrix();

        // Display bitmap to fill window but maintain aspect ratio
        SKRect rect = new SKRect(0, 0, info.Width, info.Height);
        canvas.DrawBitmap(bitmap,
            rect.AspectFill(new SKSize(bitmap.Width, bitmap.Height)));
    }
}
```

設定裁剪路徑之後，就可以顯示點陣圖，並將它裁剪成字元外框。 請注意，使用的 [`AspectFill`](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize)) 方法 `SKRect` 會計算矩形以填滿頁面，同時保留外觀比例。

**文字路徑效果**頁面會將單一連字號字元轉換成路徑，以建立1d 路徑效果。 此路徑效果的繪圖物件接著會用來對相同字元較大版本的外框進行筆劃：

[![[文字路徑效果] 頁面的三重螢幕擷取畫面](text-paths-images/textpatheffect-small.png)](text-paths-images/textpatheffect-large.png#lightbox "[文字路徑效果] 頁面的三重螢幕擷取畫面")

類別中的大部分工作都 [`TextPathEffectPath`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs) 發生在欄位和函式中。 `SKPaint`定義為欄位的兩個物件會用於兩個不同的用途：第一個名為) 的 (`textPathPaint` 會用來將具有50的連字號轉換成 `TextSize` 1d 路徑效果的路徑。 第二個 (`textPaint`) 用來顯示較大版本的連字號與該路徑效果。 基於這個理由， `Style` 這個第二個油漆物件的會設定為 `Stroke` ，但 `StrokeWidth` 不會設定此屬性，因為使用1d 路徑效果時，該屬性不是必要的：

```csharp
public class TextPathEffectPage : ContentPage
{
    const string character = "@";
    const float littleSize = 50;

    SKPathEffect pathEffect;

    SKPaint textPathPaint = new SKPaint
    {
        TextSize = littleSize
    };

    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Black
    };

    public TextPathEffectPage()
    {
        Title = "Text Path Effect";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;

        // Get the bounds of textPathPaint
        SKRect textPathPaintBounds = new SKRect();
        textPathPaint.MeasureText(character, ref textPathPaintBounds);

        // Create textPath centered around (0, 0)
        SKPath textPath = textPathPaint.GetTextPath(character,
                                                    -textPathPaintBounds.MidX,
                                                    -textPathPaintBounds.MidY);
        // Create the path effect
        pathEffect = SKPathEffect.Create1DPath(textPath, littleSize, 0,
                                               SKPath1DPathEffectStyle.Translate);
    }
    ...
}
```

此函式會先使用 `textPathPaint` 物件來測量具有50的連字號 `TextSize` 。 該矩形中心座標的減號接著會傳遞至 `GetTextPath` 方法，以將文字轉換成路徑。 結果路徑的字元中央會有 (0、0) 點，這非常適合用於1D 路徑效果。

您可能會認為在函式 `SKPathEffect` 結尾建立的物件可以設定為 `PathEffect` 屬性， `textPaint` 而不是儲存為欄位。 但因為它會扭曲 `MeasureText` 處理常式中呼叫的結果，所以不太能順利運作 `PaintSurface` ：

```csharp
public class TextPathEffectPage : ContentPage
{
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        // Set textPaint TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Do not measure the text with PathEffect set!
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(character, ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Set the PathEffect property and display text
        textPaint.PathEffect = pathEffect;
        canvas.DrawText(character, xText, yText, textPaint);
    }
}
```

該 `MeasureText` 呼叫用來將頁面上的字元置中。 為了避免發生問題，在 `PathEffect` 測量文字之後但顯示之前，屬性會設定為繪製物件。

## <a name="outlines-of-character-outlines"></a>字元外框大綱

一般來說， [`GetFillPath`](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single)) 方法會藉 `SKPaint` 由套用繪製屬性，將一個路徑轉換成另一個路徑，最值得注意的是筆劃寬度和路徑效果。 當使用時沒有路徑效果時， `GetFillPath` 實際上會建立概述另一個路徑的路徑。 這是在[**路徑效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章中的「**點擊以概述路徑**」頁面所示範。

您也可以在 `GetFillPath` 從傳回的路徑上呼叫， `GetTextPath` 但一開始您可能不會完全確定其外觀。

**字元外框大綱**頁面會示範這項技術。 所有相關的程式碼都在 `PaintSurface` 類別的處理常式中 [`CharacterOutlineOutlinesPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs) 。

此函式會先 `SKPaint` `textPaint` 使用以頁面大小為基礎的屬性來建立物件 `TextSize` 。 這會使用方法轉換成路徑 `GetTextPath` 。 在 `GetTextPath` 螢幕上有效置中路徑的座標引數：

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    using (SKPaint textPaint = new SKPaint())
    {
        // Set Style for the character outlines
        textPaint.Style = SKPaintStyle.Stroke;

        // Set TextSize based on screen size
        textPaint.TextSize = Math.Min(info.Width, info.Height);

        // Measure the text
        SKRect textBounds = new SKRect();
        textPaint.MeasureText("@", ref textBounds);

        // Coordinates to center text on screen
        float xText = info.Width / 2 - textBounds.MidX;
        float yText = info.Height / 2 - textBounds.MidY;

        // Get the path for the character outlines
        using (SKPath textPath = textPaint.GetTextPath("@", xText, yText))
        {
            // Create a new path for the outlines of the path
            using (SKPath outlinePath = new SKPath())
            {
                // Convert the path to the outlines of the stroked path
                textPaint.StrokeWidth = 25;
                textPaint.GetFillPath(textPath, outlinePath);

                // Stroke that new path
                using (SKPaint outlinePaint = new SKPaint())
                {
                    outlinePaint.Style = SKPaintStyle.Stroke;
                    outlinePaint.StrokeWidth = 5;
                    outlinePaint.Color = SKColors.Red;

                    canvas.DrawPath(outlinePath, outlinePaint);
                }
            }
        }
    }
}
```

`PaintSurface`然後，處理常式會建立名為的新路徑 `outlinePath` 。 這會成為呼叫中的目的地路徑 `GetFillPath` 。 `StrokeWidth`25 的屬性會導致 `outlinePath` 描述文字字元之25個圖元範圍路徑的外框。 然後，此路徑會以紅色顯示，筆觸寬度為5：

[![字元大綱大綱頁面的三重螢幕擷取畫面](text-paths-images/characteroutlineoutlines-small.png)](text-paths-images/characteroutlineoutlines-large.png#lightbox "字元大綱大綱頁面的三重螢幕擷取畫面")

請密切注意，您會看到路徑外框成為尖角的重迭。 這些是此程式的正常構件。

## <a name="text-along-a-path"></a>沿著路徑的文字

文字通常會以水準基準顯示。 文字可以旋轉以垂直或對角線方式執行，但基準仍然是直線。

但是，有時候您會想要讓文字沿著曲線執行。 這是的 [`DrawTextOnPath`](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint)) 方法用途 `SKCanvas` ：

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

第一個引數中指定的文字是在指定為第二個引數的路徑上執行。 您可以從路徑開頭的位移開始文字與 `hOffset` 引數。 通常路徑會形成文字的基準：文字 ascenders 在路徑的一端，而文字下行則在另一端。 但是，您可以使用引數，從路徑位移文字基準 `vOffset` 。

這個方法沒有任何機制可提供將的屬性設定為的指引， `TextSize` `SKPaint` 讓文字大小從路徑的開頭到結尾都能完美地執行。 有時您可以自行找出該文字大小。 其他時候，您將需要使用路徑測量函式，以在下一篇文章中說明 [**路徑資訊和列舉**](information.md)。

**圓形文字**程式會將文字包裝在圓形周圍。 很容易就能判斷圓形的圓周，因此可以輕鬆地調整文字大小以完全符合。 `PaintSurface`類別的處理常式會 [`CircularTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs) 根據頁面大小來計算圓形半徑。 該圓形會變成 `circularPath` ：

```csharp
public class CircularTextPage : ContentPage
{
    const string text = "xt in a circle that shapes the te";
    ...
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPath circularPath = new SKPath())
        {
            float radius = 0.35f * Math.Min(info.Width, info.Height);
            circularPath.AddCircle(info.Width / 2, info.Height / 2, radius);

            using (SKPaint textPaint = new SKPaint())
            {
                textPaint.TextSize = 100;
                float textWidth = textPaint.MeasureText(text);
                textPaint.TextSize *= 2 * 3.14f * radius / textWidth;

                canvas.DrawTextOnPath(text, circularPath, 0, 0, textPaint);
            }
        }
    }
}
```

然後，的 `TextSize` 屬性 `textPaint` 會進行調整，讓文字寬度符合圓形的圓周：

[![圓形文字頁面的三重螢幕擷取畫面](text-paths-images/circulartext-small.png)](text-paths-images/circulartext-large.png#lightbox "圓形文字頁面的三重螢幕擷取畫面")

文字本身也被選為有點圓形：「圓形」一詞是句子的主旨和介係詞片語的物件。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (範例) ](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)