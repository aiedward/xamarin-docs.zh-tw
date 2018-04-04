---
title: 核心圖形
description: 這篇文章討論核心圖形 iOS 架構。 它示範如何使用核心圖形繪製幾何、 影像和 Pdf。
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: e7b28ae8014928d82628bd8069d30ca88a4be05f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="core-graphics"></a>核心圖形

_這篇文章討論核心圖形 iOS 架構。它示範如何使用核心圖形繪製幾何、 影像和 Pdf。_

iOS 包含[*核心圖形*](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html)提供低階繪圖支援的架構。 這些架構皆是什麼啟用內 UIKit 豐富的圖形化功能。 

核心圖形是低階 2D 圖形架構，可繪製裝置獨立圖形。 所有在 UIKit 中繪製的 2D 會在內部使用核心圖形。

核心圖形支援繪製在許多案例包括：

-  [透過螢幕繪圖`UIView` ](#Drawing_in_a_UIView_Subclass) 。
-  [在記憶體中或在螢幕上繪製影像](#Drawing_Images_and_Text)。
-  建立並繪製成 PDF。
-  讀取和繪圖現有的 PDF。


## <a name="geometric-space"></a>幾何空間

案例中，不論透過核心圖形的所有繪圖都是在幾何空間中，這表示它適用於抽象的點，而不是像素為單位。 您描述要繪製幾何和繪圖狀態，例如色彩、 樣式等方面，核心圖形處理的所有項目轉譯為像素為單位。 這類狀態加入到圖形內容，您可以將像是複製的畫布。

有幾個這種方法的優點：

-  繪圖程式碼變成動態，並可隨後修改圖形，在執行階段。
-  減少應用程式配套中的靜態影像，可以減少應用程式的大小。
-  圖形會變成更彈性地回應解析度變更裝置上。

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>UIView 子類別中的繪圖

每個`UIView`具有`Draw`需要繪製時，由系統呼叫的方法。 若要將繪圖程式碼新增至檢視時，子類別`UIView`並覆寫`Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

繪製應該永遠不會直接呼叫。 它是由系統呼叫期間執行的迴圈處理。 第一次執行迴圈之後新增的檢視階層架構中，以檢視其`Draw`方法呼叫。 後續呼叫`Draw`檢視標示為需要繪製呼叫時，會發生`SetNeedsDisplay`或`SetNeedsDisplayInRect`檢視上。

### <a name="pattern-for-graphics-code"></a>圖形的程式碼模式

中的程式碼`Draw`實作應該會描述其想繪製。 繪圖程式碼遵循的模式，它會設定一些繪圖的狀態並呼叫方法，以要求它繪製。 此模式可以歸納，如下所示：

1. 取得圖形內容。

2. 設定的繪圖屬性。

3. 建立一些幾何繪製基本項目。

4. 呼叫繪製效果或筆觸的方法。

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

讓我們細分這段程式碼：

```csharp
using (CGContext g = UIGraphics.GetCurrentContext ()) {
...
}
```
與這行，它會先取得要用於繪圖的目前圖形內容。 您可以將圖形內容視為在畫布上繪製在上發生的包含關於繪圖，例如筆觸和填滿色彩，以及要繪製幾何的所有狀態。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

取得圖形內容之後程式碼會設定某些屬性用於繪製時，顯示於上方。 在此情況下設定線條寬度、 筆劃及填滿色彩。 任何後續的繪圖就會使用這些屬性，因為它們保留的圖形內容的狀態。

若要建立幾何的程式碼會使用`CGPath`，可讓圖形的路徑描述從直線和曲線。 在此情況下，路徑加入線條連接點，以構成一個三角形的陣列。 顯示核心圖形會使用下列檢視繪圖座標系統，其中來源是在左上方的 使用右和向下的正數 y 方向的正數 x direct:

```csharp
var path = new CGPath ();

path.AddLines (new CGPoint[]{
new CGPoint (100, 200),
new CGPoint (160, 100), 
new CGPoint (220, 200)});

path.CloseSubpath ();
``` 

一旦建立路徑時，就會新增至圖形內容，以便呼叫`AddPath`和`DrawPath`分別可以繪製它。

產生的檢視如下所示：

 ![](core-graphics-images/00-bluetriangle.png "範例輸出三角形")

## <a name="creating-gradient-fills"></a>建立漸層填滿

更豐富的繪製表單還有可供使用。 例如，核心圖形可讓建立漸層填滿和套用裁剪路徑。 若要繪製路徑的漸層填滿前一個範例，請先路徑必須設定為裁剪路徑：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

這設定目前的路徑為裁剪路徑限制內之幾何的路徑，例如下列程式碼中，所有後續的繪圖繪製的線性漸層：

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

 ![](core-graphics-images/01-gradient-fill.png "漸層填滿範例")

## <a name="modifying-line-patterns"></a>修改行模式

線條繪圖屬性也可以修改與核心圖形。 這包括變更線條寬度色和筆觸色彩，以及在列模式，如下列程式碼所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

此前面加入程式碼繪製的任何作業結果單位虛線的筆劃 10 長，具有 4 個單位的間距連字號，如下所示：

 ![](core-graphics-images/02-dashed-stroke.png "新增任何繪圖作業的結果之前這段程式碼中虛線的筆劃")
 
請注意，當使用統一的 API Xamarin.iOS 中，陣列型別必須是`nfloat`，而且還需要明確轉換成 Math.PI。

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>繪圖影像和文字

繪製路徑中檢視的圖形內容，除了核心圖形也會支援影像和文字。 若要繪製影像，只要建立`CGImage`並將它傳遞給`DrawImage`呼叫：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

不過，這會產生影像繪製顛倒，如下所示：

 ![](core-graphics-images/03-upside-down-monkey.png "面朝下繪製影像")

這麼做的原因是映像繪圖核心圖形原點是左下方，但檢視的左上方原點。 因此，若要正確地顯示映像，來源必須修改，這可藉由修改*目前的轉換矩陣* *(CTM)*。 CTM 定義點居住，也稱為*使用者空間*。 反轉 y 方向的 CTM 和移位由負 y 方向的範圍的高度可以翻轉影像。

圖形內容有轉換 CTM 的 helper 方法。 在此情況下， `ScaleCTM` 「 翻轉 」 繪圖和`TranslateCTM`移到左上方，如下所示：

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

產生的映像即會顯示垂直：

 ![](core-graphics-images/04-upright-monkey.png "範例影像顯示垂直")

> [!IMPORTANT]
> 圖形內容的變更套用到後續的所有繪圖作業。 因此，CTM 轉換時，它會影響任何其他繪圖。 例如，如果您繪製三角形 CTM 轉換後，它會出現上下。

### <a name="adding-text-to-the-image"></a>將文字加入至映像

做為路徑以及映像，繪製文字的核心圖形牽涉到設定圖形的某種狀態，並呼叫方法來繪製相同的基本的模式。 在文字來顯示文字的方法須`ShowText`。 加入至繪圖範例的映像時，下列程式碼繪製某些文字時，使用核心圖形：

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

如您所見，設定文字繪製的圖形狀態是類似繪製幾何。 不過繪製文字，文字繪製模式和字型也會套用。 在此情況下，陰影也會套用，雖然套用陰影方式繪製的路徑相同。

產生的文字會顯示與映像，如下所示：

 ![](core-graphics-images/05-text-on-image.png "產生的文字會顯示與映像")

## <a name="memory-backed-images"></a>記憶體為基礎的映像

若要檢視的圖形內容的繪圖，除了核心圖形支援繪製記憶體備份映像，也稱為繪製出螢幕外。 您需要：

-  在記憶體中所建立的圖形內容可支援點陣圖
-  設定繪圖狀態並發出繪製命令
-  從內容取得映像
-  移除內容


不同於`Draw`方法，其中內容提供的檢視，在此情況下您建立內容中有兩種：

1. 藉由呼叫`UIGraphics.BeginImageContext`(或`BeginImageContextWithOptions`)

2. 藉由建立新的 `CGBitmapContextInstance`

 `CGBitmapContextInstance` 您正在直接使用的映像位元，這類情況下，如果您使用自訂映像管理演算法時很有用。 在其他情況下，您應該使用`BeginImageContext`或`BeginImageContextWithOptions`。

映像內容之後，就像它是在 繪圖程式碼會加入`UIView`子類別。 例如，使用先前用來繪製三角形程式碼範例，而不是在記憶體中的影像繪製`UIView`，如下所示：

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

以記憶體為基礎的點陣圖繪圖的常見用法是擷取映像從任何`UIView`。 例如，下列程式碼呈現檢視的圖層以點陣圖內容，並建立`UIImage`從它：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>繪圖 Pdf

除了映像，核心圖形支援 PDF 繪圖。 如同影像，您可以呈現在記憶體中的 pdf 格式，以及讀取以轉譯 PDF `UIView`。

### <a name="pdf-in-a-uiview"></a>PDF 在 UIView

核心圖形也支援從檔案讀取 PDF 和呈現檢視，使用`CGPDFDocument`類別。 `CGPDFDocument`類別代表 PDF，以在程式碼，並可用於讀取和繪製的頁面。

例如，下列程式碼中`UIView`子類別會從檔案讀取 PDF `CGPDFDocument`:

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

`Draw`方法接著可以使用`CGPDFDocument`讀取到頁面`CGPDFPage`並呈現藉由呼叫`DrawPDFPage`，如下所示：

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

您需要呼叫來建立 PDF 內容的記憶體中 PDF、 `BeginPDFContext`。 PDF 的繪圖相當精細頁面。 藉由呼叫啟動每一頁`BeginPDFPage`，藉由呼叫完成`EndPDFContent`，圖形與程式碼之間。 此外，如影像繪圖記憶體基礎繪製使用 PDF 左下方，可以藉由修改 CTM 只佔用 origin 像使用映像。

下列程式碼會示範如何以 PDF 繪製文字：

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

產生的文字會繪製成 PDF，然後才包含此`NSData`，可以儲存上, 傳、 電子郵件等。


## <a name="summary"></a>總結

本文章中我們探討了透過所提供的圖形功能*核心圖形*架構。 我們了解如何使用核心圖形的內容中繪製幾何、 影像和 Pdf`UIView,`以及以記憶體為基礎的圖形內容。

## <a name="related-links"></a>相關連結

- [核心圖形範例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core 動畫](~/ios/platform/graphics-animation-ios/core-animation.md)
- [核心動畫配方](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
