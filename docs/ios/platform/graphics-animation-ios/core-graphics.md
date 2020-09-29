---
title: Xamarin 中的核心圖形
description: 本文討論核心的圖形 iOS 架構。 它會顯示如何使用核心圖形來繪製幾何、影像和 Pdf。
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 281595f45db48316feeebb88debf8c70873f87f3
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430864"
---
# <a name="core-graphics-in-xamarinios"></a>Xamarin 中的核心圖形

_本文討論核心的圖形 iOS 架構。它會顯示如何使用核心圖形來繪製幾何、影像和 Pdf。_

iOS 包含 [*核心圖形*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html) 架構，以提供低層級的繪圖支援。 這些架構可讓您在 UIKit 中提供豐富的圖形功能。

核心圖形是一種低層級2D 圖形架構，可讓您繪製裝置獨立圖形。 UIKit 中的所有2D 繪圖都會在內部使用核心圖形。

核心圖形支援多種案例中的繪圖，包括：

- 透過[使用 `UIView` 繪圖至畫面](#Drawing_in_a_UIView_Subclass).
- [在記憶體或螢幕上繪製影像](#Drawing_Images_and_Text)。
- 建立和繪製至 PDF。
- 讀取和繪製現有的 PDF。

## <a name="geometric-space"></a>幾何空間

無論何種情況，使用核心圖形完成的所有繪製作業都是在幾何空間中完成，這表示它會以抽象點運作，而不是圖元。 您可以根據幾何和繪製狀態（例如色彩、線條樣式等等）來描述您想要繪製的專案，而核心圖形會將所有專案轉譯成圖元。 這種狀態會新增至圖形內容，您可以將它視為油漆畫布的外觀。

這種方法有幾個優點：

- 繪圖程式碼會變成動態，之後可在執行時間修改圖形。
- 減少應用程式套件組合中的靜態映射需求可以減少應用程式的大小。
- 圖形對於跨裝置的解析度變更會有更多的彈性。

<a name="Drawing_in_a_UIView_Subclass"></a>

## <a name="drawing-in-a-uiview-subclass"></a>UIView 子類別中的繪圖

每個 `UIView` 都有一個 `Draw` 系統在需要繪製時所呼叫的方法。 若要將繪圖程式碼加入至 view、子類別 `UIView` 和覆寫 `Draw` ：

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

不應該直接呼叫 Draw。 它是由系統在執行迴圈處理期間呼叫。 第一次在將 view 新增至 view 階層之後，執行迴圈的第一次， `Draw` 會呼叫其方法。 `Draw`當視圖被標示為需要透過呼叫 `SetNeedsDisplay` 或在視圖上繪製時，會發生後續的呼叫 `SetNeedsDisplayInRect` 。

### <a name="pattern-for-graphics-code"></a>圖形程式碼的模式

執行中的程式碼 `Draw` 應該描述其所要繪製的內容。 繪圖程式碼遵循的模式會設定某些繪製狀態，並呼叫方法來要求繪製。 此模式可以一般化，如下所示：

1. 取得圖形內容。

2. 設定繪圖屬性。

3. 從繪圖基本專案建立一些幾何。

4. 呼叫 Draw 或筆觸方法。

### <a name="basic-drawing-example"></a>基本繪圖範例

例如，請考慮下列程式碼片段：

```csharp
//get graphics context
using (CGContext g = UIGraphics.GetCurrentContext ()) {

    //set up drawing attributes
    g.SetLineWidth (10);
    UIColor.Blue.SetFill ();
    UIColor.Red.SetStroke ();

    //create geometry
    var path = new CGPath ();

    path.AddLines (new CGPoint[]{
    new CGPoint (100, 200),
    new CGPoint (160, 100),
    new CGPoint (220, 200)});

    path.CloseSubpath ();

    //add geometry to graphics context and draw it
    g.AddPath (path);
    g.DrawPath (CGPathDrawingMode.FillStroke);
}
```

讓我們將此程式碼細分：

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```

在這一行中，它會先取得要用於繪圖的目前圖形內容。 您可以將圖形內容視為繪圖發生所在的畫布，其中包含繪圖的所有狀態，例如筆劃和填滿色彩，以及要繪製的幾何。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
```

取得圖形內容之後，程式碼會設定要在繪製時使用的一些屬性，如上所示。 在此情況下，會設定線條寬度、筆觸和填滿色彩。 然後，任何後續的繪圖都會使用這些屬性，因為它們會以圖形內容的狀態進行維護。

若要建立幾何，程式碼 `CGPath` 會使用，這可讓圖形路徑從線條和曲線進行描述。 在此情況下，路徑會新增連接點陣列的線條，以組成三角形。 如下圖所示，核心圖形會使用視圖繪圖的座標系統，其中的原點位於左上角，右邊是右 x，右邊則是右 y 方向：

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100),
new CGPoint (220, 200)});

path.CloseSubpath ();
```

建立路徑之後，會將它新增至圖形內容，讓呼叫和個別 `AddPath` 呼叫 `DrawPath` 可以繪製它。

產生的視圖如下所示：

 ![範例輸出三角形](core-graphics-images/00-bluetriangle.png)

## <a name="creating-gradient-fills"></a>建立漸層填滿

也提供更豐富的繪圖形式。 例如，核心圖形可讓您建立漸層填滿並套用裁剪路徑。 若要在上一個範例的路徑中繪製漸層填滿，首先必須將路徑設定為裁剪路徑：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

將目前的路徑設定為裁剪路徑，會限制路徑幾何內的所有後續繪圖，例如下列程式碼，它會繪製線性漸層：

```csharp
// the color space determines how Core Graphics interprets color information
    using (CGColorSpace rgb = CGColorSpace.CreateDeviceRGB()) {
        CGGradient gradient = new CGGradient (rgb, new CGColor[] {
        UIColor.Blue.CGColor,
        UIColor.Yellow.CGColor
    });

// draw a linear gradient
    g.DrawLinearGradient (
        gradient,
        new CGPoint (path.BoundingBox.Left, path.BoundingBox.Top),
        new CGPoint (path.BoundingBox.Right, path.BoundingBox.Bottom),
        CGGradientDrawingOptions.DrawsBeforeStartLocation);
    }
```

這些變更會產生漸層填滿，如下所示：

 ![具有漸層填滿的範例](core-graphics-images/01-gradient-fill.png)

## <a name="modifying-line-patterns"></a>修改行模式

您也可以使用核心圖形來修改線條的繪製屬性。 這包括變更線條寬度和筆觸色彩，以及行模式本身，如下列程式碼所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

在任何繪圖作業之前新增此程式碼會導致10個單位長的虛線筆觸，並在連字號之間有4個單位的間距，如下所示：

 ![在任何繪圖作業之前新增此程式碼會產生虛線筆劃](core-graphics-images/02-dashed-stroke.png)

請注意，當您在 Xamarin 中使用 Unified API 時，陣列類型必須是 `nfloat` ，而且也需要明確地轉換成 Math. PI。

<a name="Drawing_Images_and_Text"></a>

## <a name="drawing-images-and-text"></a>繪圖影像和文字

除了描繪視圖圖形內容中的路徑，核心圖形也支援繪製影像和文字。 若要繪製影像，只要建立， `CGImage` 並將它傳遞給 `DrawImage` 呼叫即可：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

不過，這會產生一個倒置的影像，如下所示：

 ![朝下繪製的影像](core-graphics-images/03-upside-down-monkey.png)

原因是影像繪圖的核心圖形原點位於左下方，而視圖的原點在左上方。 因此，若要正確地顯示影像，必須修改來源，方法是藉由修改 *目前的轉換矩陣* * (cmt) *來完成。 CMT 會定義即時點（也稱為 *使用者空間*）的位置。 以 y 方向反轉 CMT，然後以負 y 方向的系結高度進行切換，可以翻轉影像。

圖形內容具有可轉換 CMT 的 helper 方法。 在此情況下，「 `ScaleCTM` 翻轉」繪圖並將 `TranslateCTM` 它移至左上方，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using (CGContext g = UIGraphics.GetCurrentContext ()) {

        // scale and translate the CTM so the image appears upright
        g.ScaleCTM (1, -1);
        g.TranslateCTM (0, -Bounds.Height);
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
}
```

產生的影像接著會以直立的顯示：

 ![以直立顯示的範例影像](core-graphics-images/04-upright-monkey.png)

> [!IMPORTANT]
> 圖形內容的變更會套用至所有後續的繪圖作業。 因此，轉換 CMT 時，它會影響任何額外的繪圖。 例如，如果您在 CMT 轉換之後繪製三角形，它就會顯示為上下。

### <a name="adding-text-to-the-image"></a>將文字新增至影像

如同路徑和影像，使用核心圖形繪製文字，會牽涉到設定某些圖形狀態和呼叫方法來繪製的相同基本模式。 在文字的案例中，顯示文字的方法是 `ShowText` 。 新增至影像繪圖範例時，下列程式碼會使用核心圖形來繪製一些文字：

```csharp
public override void Draw (RectangleF rect)
{
    base.Draw (rect);

    // image drawing code omitted for brevity ...

    // translate the CTM by the font size so it displays on screen
    float fontSize = 35f;
    g.TranslateCTM (0, fontSize);

    // set general-purpose graphics state
    g.SetLineWidth (1.0f);
    g.SetStrokeColor (UIColor.Yellow.CGColor);
    g.SetFillColor (UIColor.Red.CGColor);
    g.SetShadow (new CGSize (5, 5), 0, UIColor.Blue.CGColor);

    // set text specific graphics state
    g.SetTextDrawingMode (CGTextDrawingMode.FillStroke);
    g.SelectFont ("Helvetica", fontSize, CGTextEncoding.MacRoman);

    // show the text
    g.ShowText ("Hello Core Graphics");
}
```

如您所見，設定文字繪圖的圖形狀態與繪製幾何類似。 但是，如果是文字繪圖，也會套用文字繪圖模式和字型。 在此情況下，也會套用陰影，雖然套用陰影的效果與路徑繪製相同。

產生的文字會與影像一起顯示，如下所示：

 ![產生的文字會與影像一起顯示](core-graphics-images/05-text-on-image.png)

## <a name="memory-backed-images"></a>記憶體支援的影像

除了繪製到視圖的圖形內容之外，核心圖形還支援繪圖記憶體支援的影像，也稱為螢幕上的繪圖。 這麼做需要：

- 建立由記憶體中點陣圖所支援的圖形內容
- 設定繪製狀態和發出繪製命令
- 從內容取得影像
- 移除內容

不同于 `Draw` 方法（內容是由視圖提供），在此情況下，您會以下列兩種方式之一來建立內容：

1. 藉由呼叫 `UIGraphics.BeginImageContext` (或 `BeginImageContextWithOptions`) 

2. 藉由建立新的 `CGBitmapContextInstance`

 `CGBitmapContextInstance` 當您直接使用影像位（例如您使用自訂影像操作演算法的情況）時，會很有用。 在所有其他情況下，您應該使用 `BeginImageContext` 或 `BeginImageContextWithOptions` 。

當您擁有影像內容之後，新增繪圖程式碼就像它在子類別中一樣 `UIView` 。 例如，稍早用來繪製三角形的程式碼範例，可以用來繪製至記憶體中的影像，而不是中的 `UIView` ，如下所示：

```csharp
UIImage DrawTriangle ()
{
    UIImage triangleImage;

    //push a memory backed bitmap context on the context stack
    UIGraphics.BeginImageContext (new CGSize (200.0f, 200.0f));

    //get graphics context
    using(CGContext g = UIGraphics.GetCurrentContext ()){

        //set up drawing attributes
        g.SetLineWidth(4);
        UIColor.Purple.SetFill ();
        UIColor.Black.SetStroke ();

        //create geometry
        path = new CGPath ();

        path.AddLines(new CGPoint[]{
            new CGPoint(100,200),
            new CGPoint(160,100),
            new CGPoint(220,200)});

        path.CloseSubpath();

        //add geometry to graphics context and draw it
        g.AddPath(path);
        g.DrawPath(CGPathDrawingMode.FillStroke);

        //get a UIImage from the context
        triangleImage = UIGraphics.GetImageFromCurrentImageContext ();
    }

    return triangleImage;
}
```

繪製至記憶體支援點陣圖的常見用法是從任何影像中捕獲映射 `UIView` 。 例如，下列程式碼會將視圖圖層轉譯為點陣圖內容，並 `UIImage` 從它建立：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>繪圖 Pdf

除了影像之外，核心圖形還支援 PDF 繪圖。 如同影像，您可以在記憶體中轉譯 PDF，也可以讀取 PDF 以在中轉譯 `UIView` 。

### <a name="pdf-in-a-uiview"></a>UIView 中的 PDF

核心圖形也支援從檔案讀取 PDF，並使用類別在視圖中轉譯 `CGPDFDocument` 。 `CGPDFDocument`類別代表程式碼中的 PDF，而且可以用來讀取和繪製頁面。

例如，子類別中的下列程式碼會從檔案將 `UIView` PDF 讀入 `CGPDFDocument` ：

```csharp
public class PDFView : UIView
{
    CGPDFDocument pdfDoc;

    public PDFView ()
    {
        //create a CGPDFDocument from file.pdf included in the main bundle
        pdfDoc = CGPDFDocument.FromFile ("file.pdf");
    }

     public override void Draw (Rectangle rect)
    {
        ...
    }
}
```

`Draw`然後，方法可以使用 `CGPDFDocument` 來讀取頁面，然後藉 `CGPDFPage` 由呼叫來呈現頁面 `DrawPDFPage` ，如下所示：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    //flip the CTM so the PDF will be drawn upright
    using (CGContext g = UIGraphics.GetCurrentContext ()) {
        g.TranslateCTM (0, Bounds.Height);
        g.ScaleCTM (1, -1);

        // render the first page of the PDF
        using (CGPDFPage pdfPage = pdfDoc.GetPage (1)) {

        //get the affine transform that defines where the PDF is drawn
        CGAffineTransform t = pdfPage.GetDrawingTransform (CGPDFBox.Crop, rect, 0, true);

        //concatenate the pdf transform with the CTM for display in the view
        g.ConcatCTM (t);

        //draw the pdf page
        g.DrawPDFPage (pdfPage);
        }
    }
}
```

### <a name="memory-backed-pdf"></a>記憶體支援的 PDF

對於記憶體中的 PDF，您需要呼叫來建立 PDF 內容 `BeginPDFContext` 。 繪圖至 PDF 的頁面很精細。 每一頁都是藉由呼叫 `BeginPDFPage` 來啟動 `EndPDFContent` ，並以兩者之間的圖形程式碼來完成。 此外，如同使用影像繪圖，以記憶體支援的 PDF 繪圖會使用左下角的原點，只要修改 CMT 就像影像一樣。

下列程式碼示範如何將文字繪製至 PDF：

```csharp
//data buffer to hold the PDF
NSMutableData data = new NSMutableData ();

//create a PDF with empty rectangle, which will configure it for 8.5x11 inches
UIGraphics.BeginPDFContext (data, CGRect.Empty, null);

//start a PDF page
UIGraphics.BeginPDFPage ();

using (CGContext g = UIGraphics.GetCurrentContext ()) {
    g.ScaleCTM (1, -1);
    g.TranslateCTM (0, -25);
    g.SelectFont ("Helvetica", 25, CGTextEncoding.MacRoman);
    g.ShowText ("Hello Core Graphics");
    }

//complete a PDF page
UIGraphics.EndPDFContent ();
```

產生的文字會繪製至 PDF，然後包含在 `NSData` 可儲存、上傳、電子郵件等的中。

## <a name="summary"></a>摘要

在本文中，我們探討了透過 *核心圖形* 架構所提供的圖形功能。 我們已瞭解如何使用核心圖形，在的內容中繪製幾何、影像和 Pdf，以及 `UIView,` 記憶體支援的圖形內容。

## <a name="related-links"></a>相關連結

- [核心圖形範例](/samples/xamarin/ios-samples/graphicsandanimation)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心動畫食譜](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)