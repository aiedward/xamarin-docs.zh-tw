---
title: 路徑及 SkiaSharp 中的文字
description: 本文將探討 SkiaSharp 路徑和文字的交集，並示範此範例程式碼。
ms.prod: xamarin
ms.assetid: C14C07F6-4A84-4A8C-BDB4-CD61FBF0F79B
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 08/01/2017
ms.openlocfilehash: 366a6e9585817c5a47ba5bec14fb2f238ab23a6b
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53050190"
---
# <a name="paths-and-text-in-skiasharp"></a>路徑及 SkiaSharp 中的文字

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)

_探索路徑及文字的交集_

在現代化圖形系統中，文字字型會是字元外框輪廓，通常由二次方貝茲曲線所定義的集合。 因此，許多現代化圖形系統包含的功能，將文字字元轉換成圖形路徑。

您所見，您可以繪製的文字字元外框輪廓，以及填入。 這可讓您顯示與特定的筆劃寬度，甚至是路徑的效果，這些字元外框輪廓中所述[**路徑效果**](effects.md)文章。 但也字元字串轉換成`SKPath`物件。 這表示文字外框輪廓，可用來裁剪，以及所述的技術[**使用路徑和地區裁剪**](clipping.md)文章。

除了使用路徑效果繪製字元外框，您也可以建立衍生自字元字串，路徑為基礎的路徑效果，以及您甚至可以結合兩個效果：

![](text-paths-images/pathsandtextsample.png "文字路徑效果")

在上一篇文章中上[**路徑效果**](effects.md)，您已看到如何[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single))方法`SKPaint`可以取得繪製路徑的外框。 您也可以使用這個方法，以衍生自字元外框輪廓的路徑。

最後，本文會示範另一個的路徑及文字的交集︰ [ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint))方法`SKCanvas`可讓您顯示的文字字串，以便基準線的文字後面的彎曲的路徑。

## <a name="text-to-path-conversion"></a>路徑轉換成文字

[ `GetTextPath` ](xref:SkiaSharp.SKPaint.GetTextPath(System.String,System.Single,System.Single))方法`SKPaint`的字元字串轉換為`SKPath`物件：

```csharp
public SKPath GetTextPath (String text, Single x, Single y)
```

`x`和`y`引數表示基準線的左邊算起的起點。 相同角色中的扮演`DrawText`方法的`SKCanvas`。 在路徑中，基準線的文字的左邊會有 （x，y） 座標。

`GetTextPath`方法是大材小用，如果您只想要填滿或繪製結果的路徑。 一般`DrawText`方法可讓您執行此作業。 `GetTextPath`方法是更適合用於包含路徑的其他工作。

其中一個工作裁剪。 **裁剪文字**頁面建立裁剪路徑，根據字元外框輪廓的文字 「 程式碼。 」 此路徑會自動縮放以裁剪點陣圖，其中包含的映像頁面的大小**裁剪文字**原始程式碼：

[![](text-paths-images/clippingtext-small.png "裁剪文字頁面的三個螢幕擷取畫面")](text-paths-images/clippingtext-large.png#lightbox "裁剪文字頁面的三個螢幕擷取畫面")

[ `ClippingTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/ClippingTextPage.cs)類別建構函式載入點陣圖儲存為中的內嵌資源**媒體**方案的資料夾：

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

`PaintSurface`處理常式一開始先建立`SKPaint`適用於文字的物件。 `Typeface`屬性設定，以及`TextSize`、 針對此特定的應用程式雖然`TextSize`屬性純粹是任意。 另請注意有沒有`Style`設定。

`TextSize`並`Style`屬性設定不需要因為這`SKPaint`物件只用於`GetTextPath`呼叫使用文字字串"CODE"。 處理常式，然後測量結果`SKPath`物件，並套用它置並調整頁面大小為三個轉換。 路徑可以設定為裁剪路徑：

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

裁剪路徑設定後，可以顯示點陣圖，，則會裁剪以字元外框。 請注意，使用[ `AspectFill` ](xref:SkiaSharp.SKRect.AspectFill(SkiaSharp.SKSize))方法`SKRect`計算矩形來填滿頁面，同時維持外觀比例。

**文字路徑效果**頁面會將單一連字號字元轉換成路徑，以建立 1d 路徑效果。 此路徑效果繪製物件再來繪製外框的該相同字元的較大版本：

[![](text-paths-images/textpatheffect-small.png "文字路徑效果頁面的三個螢幕擷取畫面")](text-paths-images/textpatheffect-large.png#lightbox "文字路徑效果頁面的三個螢幕擷取畫面")

太多的工作[ `TextPathEffectPath` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/TextPathEffectPage.cs)類別中的欄位和建構函式，就會發生。 這兩個`SKPaint`物件定義為欄位用於兩個不同的用途： 第一個 (名為`textPathPaint`) 用來將轉換與連字號`TextSize`的 50%到 1d 路徑效果的路徑。 第二個 (`textPaint`) 用來顯示與該路徑效果連字號的較大版本。 基於這個理由，`Style`物件設定為此第二個小畫家`Stroke`，但`StrokeWidth`因為使用 1d 路徑效果時，不需要該屬性未設定屬性：

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

建構函式會先使用`textPathPaint`物件以測量與連字號`TextSize`為 50。 接著會傳遞至該矩形的中心座標的否定`GetTextPath`方法，將文字轉換成路徑。 結果的路徑具有 （0，0） 中的字元，也就是適合 1d 路徑效果的中心點。

您可能會認為`SKPathEffect`結尾的建構函式所建立的物件，可以設定為`PathEffect`屬性`textPaint`而不是儲存為欄位。 但非常正常運作，因為它失真的結果，所以此開啟出不`MeasureText`呼叫中`PaintSurface`處理常式：

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

`MeasureText`呼叫用來置中對齊頁面上的字元。 若要避免發生問題，`PathEffect`測量文字後，但它會顯示屬性設定為 [小畫家] 物件。

## <a name="outlines-of-character-outlines"></a>字元外框輪廓的大綱

通常[ `GetFillPath` ](xref:SkiaSharp.SKPaint.GetFillPath(SkiaSharp.SKPath,SkiaSharp.SKPath,SkiaSharp.SKRect,System.Single))方法`SKPaint`藉由套用 [小畫家] 屬性，將一個路徑轉換成另一個最值得注意的是筆劃寬度及路徑的效果。 未路徑的效果，搭配使用時`GetFillPath`實際上會建立概述另一個路徑的路徑。 這所示**點選以外框路徑**頁面[**路徑效果**](~/xamarin-forms/user-interface/graphics/skiasharp/curves/effects.md)文章。

您也可以呼叫`GetFillPath`所傳回的路徑上`GetTextPath`但一開始您可能不完全確定什麼樣子。

**字元外框輪廓**頁面會示範此技術。 所有相關的程式碼位於`PaintSurface`處理常式[ `CharacterOutlineOutlinesPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CharacterOutlineOutlinesPage.cs)類別。

建構函式一開始先建立`SKPaint`名為物件`textPaint`與`TextSize`屬性，根據頁面的大小。 這會轉換成路徑，使用`GetTextPath`方法。 座標的引數`GetTextPath`有效 center 在螢幕上的路徑：

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

`PaintSurface`處理常式接著會建立名為的新路徑`outlinePath`。 這會成為目的地路徑的呼叫中`GetFillPath`。 `StrokeWidth` 25 會導致屬性`outlinePath`來描述的 25 像素寬路徑繪製文字字元外框。 此路徑接著會顯示紅色筆劃寬度為 5:

[![](text-paths-images/characteroutlineoutlines-small.png "字元外框輪廓頁面的三個螢幕擷取畫面")](text-paths-images/characteroutlineoutlines-large.png#lightbox "字元外框輪廓頁面的三個螢幕擷取畫面")

仔細看，您會看到重疊路徑外框，使尖角。 這些是一般的成品，此程序。

## <a name="text-along-a-path"></a>沿著路徑的文字

文字通常會顯示在水平的基準線中。 文字可旋轉以便執行垂直或對角線方式，但是基準仍是一條直線。

有的時間，不過，當您想要執行沿著曲線的文字。 這是目的[ `DrawTextOnPath` ](xref:SkiaSharp.SKCanvas.DrawTextOnPath(System.String,SkiaSharp.SKPath,System.Single,System.Single,SkiaSharp.SKPaint))方法`SKCanvas`:

```csharp
public Void DrawTextOnPath (String text, SKPath path, Single hOffset, Single vOffset, SKPaint paint)
```

第一個引數中指定的文字對執行做為第二個引數所指定的路徑。 您可以開始使用做為路徑開頭的位移文字`hOffset`引數。 路徑通常 form 基準線的文字： 文字包含上格其中一端的路徑，而文字的伸尾部分其他。 但您可以位移之路徑的文字基準`vOffset`引數。

這個方法沒有任何設備可提供設定的指引`TextSize`屬性`SKPaint`，使大小完全執行路徑的開頭到結尾的文字。 有時候您找出您自己的文字大小。 有時候，您必須使用路徑測量函式中的下一篇文章上所述[**路徑資訊和列舉型別**](information.md)。

**循環的文字**程式將文字換行圓圈。 它很容易，方便您以完全符合的文字大小，決定圓形的圓周。 `PaintSurface`處理常式[ `CircularTextPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Curves/CircularTextPage.cs)類別就會計算根據頁面大小圓形的半徑。 該圓圈會變成`circularPath`:

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

`TextSize`屬性`textPaint`然後進行調整，讓文字寬度符合圓形的圓周：

[![](text-paths-images/circulartext-small.png "循環的文字頁面的三個螢幕擷取畫面")](text-paths-images/circulartext-large.png#lightbox "循環的文字頁面的三個螢幕擷取畫面")

選擇文字本身也是有點循環： word"circle"是兩個句子的主旨和前置詞片語的物件。

## <a name="related-links"></a>相關連結

- [SkiaSharp Api](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos （範例）](https://developer.xamarin.com/samples/xamarin-forms/SkiaSharpForms/Demos/)
