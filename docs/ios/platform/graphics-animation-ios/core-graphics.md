---
title: Xamarin 中的核心圖形
description: 本文討論核心的圖形 iOS 架構。 它會顯示如何使用核心圖形來繪製幾何、影像和 Pdf。
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 72c233dc492fe1c93546121bc9103e666c2127ac
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286254"
---
# <a name="core-graphics-in-xamarinios"></a>Xamarin 中的核心圖形

_本文討論核心的圖形 iOS 架構。它會顯示如何使用核心圖形來繪製幾何、影像和 Pdf。_

iOS 包含[*核心圖形*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html)架構，可提供低層級的繪圖支援。 這些架構可讓您在 UIKit 中提供豐富的圖形功能。

核心圖形是低層級的2D 圖形架構，可讓您繪製裝置獨立的圖形。 UIKit 中的所有2D 繪圖會在內部使用核心圖形。

核心圖形支援數種案例中的繪圖，包括：

- 透過[繪製到螢幕`UIView` ](#Drawing_in_a_UIView_Subclass) 。
- [在記憶體或螢幕上繪製影像](#Drawing_Images_and_Text)。
- 建立和繪製 PDF。
- 讀取和繪製現有的 PDF。


## <a name="geometric-space"></a>幾何空間

不論何種情況，使用核心圖形完成的所有繪製都是在幾何空間中完成，這表示它是以抽象點運作，而不是圖元。 您會根據幾何和繪圖狀態（例如色彩、線條樣式等等）來描述所要繪製的內容，而核心圖形則會處理將所有專案轉譯成圖元的情況。 這種狀態會加入圖形內容中，您可以將它視為油漆畫布。

這種方法有幾個優點：

- 繪製程式碼變成動態的，之後就可以在執行時間修改圖形。
- 減少應用程式套件組合中的靜態映射需求，可以減少應用程式的大小。
- 圖形在跨裝置的解析度變更方面變得更有彈性。

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>在 UIView 子類別中繪製

每隔`UIView`具有`Draw`需要繪製時，會將由系統呼叫的方法。 若要將繪圖程式碼加入至視圖`UIView` ，子`Draw`類別和覆寫：

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

絕對不要直接呼叫 Draw。 執行的迴圈處理期間，它會呼叫系統。 第一次執行迴圈時檢視加入至檢視階層之後, 它`Draw`呼叫方法。 後續呼叫`Draw`檢視會標示為需要繪製呼叫時，會發生`SetNeedsDisplay`或`SetNeedsDisplayInRect`檢視上。

### <a name="pattern-for-graphics-code"></a>圖形程式碼的模式

`Draw`執行程式中的程式碼應該描述它所要繪製的內容。 繪圖程式碼會遵循其設定某些繪製狀態的模式，並呼叫方法來要求繪製它。 此模式可以一般化，如下所示：

1. 取得圖形內容。

2. 設定繪圖屬性。

3. 從繪製基本專案建立一些幾何。

4. 呼叫繪製或筆觸方法。

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

使用這一行，它會先取得目前用於繪製的圖形內容。 您可以將圖形內容視為繪製發生所在的畫布，其中包含繪圖的所有狀態，例如筆劃和填滿色彩，以及要繪製的幾何。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
```

取得圖形內容之後，程式碼會設定一些要在繪製時使用的屬性，如上所示。 在此情況下，會設定線條寬度、筆觸和填滿色彩。 接著，任何後續的繪圖都會使用這些屬性，因為它們是以圖形內容的狀態進行維護。

若要建立 geometry `CGPath`，程式碼會使用，這可讓您從線條和曲線描述圖形路徑。 在此情況下，路徑會加入連接點陣列的線條來組成三角形。 如下所示，核心圖形會使用座標系統來進行視圖繪製，其中原點位於左上方，而右 x 直接指向右邊，而正 y 方向為向下：

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100),
new CGPoint (220, 200)});

path.CloseSubpath ();
```

建立路徑之後，會將它新增至圖形內容，讓呼叫`AddPath`和`DrawPath`分別可以繪製它。

產生的視圖如下所示：

 ![](core-graphics-images/00-bluetriangle.png "範例輸出三角形")

## <a name="creating-gradient-fills"></a>建立漸層填滿

也提供更豐富的繪圖形式。 例如，核心圖形允許建立漸層填滿和套用裁剪路徑。 若要在上一個範例的路徑內繪製漸層填滿，首先必須將路徑設為裁剪路徑：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

將目前的路徑設定為裁剪路徑，會限制路徑幾何中的所有後續繪製，例如下列程式碼，其繪製線性漸層：

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

 ![](core-graphics-images/01-gradient-fill.png "具有漸層填滿的範例")

## <a name="modifying-line-patterns"></a>修改線條模式

您也可以使用核心圖形來修改線條的繪圖屬性。 這包括變更線條寬度和筆觸色彩，以及線條模式本身，如下列程式碼所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

在任何繪圖作業之前加入此程式碼會產生以10個單位長的虛線筆觸，而虛線之間有4個間距，如下所示：

 ![](core-graphics-images/02-dashed-stroke.png "在任何繪製作業之前加入此程式碼會產生虛線筆劃")

請注意，當您在 Xamarin 中使用 Unified API 時，陣列型別必須是`nfloat`，而且也需要明確轉換成 Math。

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>繪製影像和文字

除了在視圖的圖形內容中繪製路徑，核心圖形也支援繪製影像和文字。 若要繪製影像，只要建立`CGImage` ，並將它傳遞`DrawImage`給呼叫即可：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

不過，這會產生一個上下繪製的影像，如下所示：

 ![](core-graphics-images/03-upside-down-monkey.png "朝下繪製的影像")

此情況的原因是影像繪圖的核心圖形原點位於左下方，而視圖的原點在左上方。 因此，若要正確顯示影像，則需要修改來源，這可以藉由修改*目前的轉換矩陣* *（cmt）* 來完成。 CMT 會定義點的上線位置，也稱為*使用者空間*。 將 y 方向的 CMT 反轉，並以負 y 方向的界限高度移位，可以翻轉影像。

圖形內容具有 helper 方法，可轉換 CMT。 在此情況下`ScaleCTM` ，「翻轉」繪圖並`TranslateCTM`將其移至左上方，如下所示：

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

產生的影像就會顯示為直立的：

 ![](core-graphics-images/04-upright-monkey.png "以垂直顯示的範例影像")

> [!IMPORTANT]
> 圖形內容的變更會套用至所有後續的繪製作業。 因此，轉換 CMT 時，它會影響任何其他繪圖。 例如，如果您在 [CMT] 轉換後繪製三角形，它會顯示為 [倒置]。

### <a name="adding-text-to-the-image"></a>將文字新增至影像

如同路徑和影像，使用核心圖形繪製文字牽涉到設定一些圖形狀態和呼叫方法來繪製的基本模式。 在文字的案例中，顯示文字的方法是`ShowText`。 新增至影像繪圖範例時，下列程式碼會使用核心圖形來繪製一些文字：

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

如您所見，設定文字繪製的圖形狀態與繪製幾何類似。 不過，文字繪製模式和字型也會一併套用。 在此情況下，也會套用陰影，雖然套用陰影的運作方式與路徑繪製相同。

產生的文字會與影像一起顯示，如下所示：

 ![](core-graphics-images/05-text-on-image.png "產生的文字會與影像一起顯示")

## <a name="memory-backed-images"></a>記憶體支援的映射

除了繪製至視圖的圖形內容，核心圖形也支援繪製記憶體備份的影像，也稱為在螢幕上繪圖。 這麼做需要：

- 建立由記憶體中點陣圖所支援的圖形內容
- 設定繪製狀態和發出繪製命令
- 從內容取得影像
- 移除內容


不同于`Draw`方法，其中的內容是由視圖提供，在此情況下，您可以使用下列兩種方式之一來建立內容：

1. 藉由`UIGraphics.BeginImageContext`呼叫（ `BeginImageContextWithOptions`或）

2. 藉由建立新的`CGBitmapContextInstance`

 `CGBitmapContextInstance`當您直接使用影像位時（例如，在使用自訂影像操作演算法的情況下），相當有用。 在所有其他情況下，您應該`BeginImageContext`使用`BeginImageContextWithOptions`或。

一旦擁有影像內容，加入繪圖程式碼就像它在子`UIView`類別中一樣。 例如，稍早用來繪製三角形的程式碼範例，可以用來繪製至記憶體中的影像`UIView`，而不是中的，如下所示：

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

繪製到記憶體支援點陣圖的常見用法是從任何`UIView`一個抓取影像。 例如，下列程式碼會將視圖的圖層轉譯為點陣圖內容，並`UIImage`從它建立：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>繪製 Pdf

除了影像，核心圖形也支援 PDF 繪圖。 就像影像一樣，您可以在記憶體中轉譯 PDF，以及讀取 PDF 以在中`UIView`呈現。

### <a name="pdf-in-a-uiview"></a>UIView 中的 PDF

核心圖形也支援從檔案讀取 PDF，並使用`CGPDFDocument`類別在視圖中呈現。 `CGPDFDocument`類別代表程式碼中的 PDF，可以用來讀取和繪製頁面。

例如，子`UIView`類別中的下列程式碼會將檔案中的 PDF 讀取`CGPDFDocument`至：

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

方法接著可以`CGPDFDocument` `DrawPDFPage`使用`CGPDFPage`來讀取頁面，並藉由呼叫來呈現它，如下所示： `Draw`

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

對於記憶體中的 PDF，您需要藉由呼叫`BeginPDFContext`來建立 pdf 內容。 繪製到 PDF 是細微的頁面。 每個頁面都是藉`BeginPDFPage`由呼叫來啟動`EndPDFContent`，並以中的圖形程式碼來完成。 此外，與影像繪圖一樣，記憶體支援的 PDF 繪圖會使用左下方的原點，只要像使用影像一樣修改 CMT 即可。

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

產生的文字會繪製到 PDF，然後包含在`NSData`可以儲存、上傳、以電子郵件傳送等的。


## <a name="summary"></a>總結

在本文中，我們探討了透過*核心圖形*架構提供的圖形功能。 我們已瞭解如何使用核心圖形，在的內容中繪製幾何、影像和 pdf， `UIView,`以及記憶體支援的圖形內容。

## <a name="related-links"></a>相關連結

- [核心圖形範例](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core 動畫](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心動畫配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
