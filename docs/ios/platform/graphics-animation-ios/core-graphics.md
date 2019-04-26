---
title: 在 Xamarin.iOS 中的核心圖形
description: 這篇文章討論 Core Graphics iOS 架構。 它示範如何使用核心圖形繪製幾何、 影像和 Pdf。
ms.prod: xamarin
ms.assetid: 4A30F480-0723-4B8A-9049-7CEB6211304A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f3fe22e56a2c45524923a316ef28e54e5a3cc3f8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61372387"
---
# <a name="core-graphics-in-xamarinios"></a>在 Xamarin.iOS 中的核心圖形

_這篇文章討論 Core Graphics iOS 架構。它示範如何使用核心圖形繪製幾何、 影像和 Pdf。_

包含 iOS [ *Core Graphics* ](https://developer.apple.com/library/prerelease/ios/documentation/CoreGraphics/Reference/CoreGraphics_Framework/index.html)架構提供低階繪圖的支援。 這些架構也讓 UIKit 中豐富的圖形化功能。 

Core 圖形是允許繪圖裝置獨立圖形的低層級的 2D 圖形架構。 所有的 2D 繪圖 UIKit 中會在內部使用核心圖形。

Core 圖形支援繪製在幾個案例包括：

-  [透過螢幕繪圖`UIView` ](#Drawing_in_a_UIView_Subclass) 。
-  [在記憶體中或在螢幕上繪製影像](#Drawing_Images_and_Text)。
-  建立和繪製成 PDF。
-  讀取和繪製現有的 PDF。


## <a name="geometric-space"></a>空間幾何

此案例中，不論透過核心圖形的所有繪圖是在都完成幾何空間，這表示它適用於在抽象的點，而不是像素為單位。 您描述要繪製 geometry 和繪製狀態，例如色彩、 線條樣式等方面，核心圖形處理的所有項目轉譯為像素為單位。 這類狀態會加入至圖形內容，您可以將像是繪製器的畫布。

有這種方法的幾個優點：

-  繪圖程式碼會變成動態的並接著可以修改在執行階段的圖形。
-  減少應用程式套件組合中的靜態映像，可以減少應用程式的大小。
-  圖形跨裝置變得更有彈性，解析的變更。

<a name="Drawing_in_a_UIView_Subclass"/>

## <a name="drawing-in-a-uiview-subclass"></a>UIView 子類別中的繪圖

每隔`UIView`具有`Draw`需要繪製時，會將由系統呼叫的方法。 若要將繪圖程式碼新增至檢視中，子類別`UIView`，並覆寫`Draw`:

```csharp
public class TriangleView : UIView
{
    public override void Draw (CGRect rect)
    {
        base.Draw (rect);
    }
}
```

繪製應該永遠不會直接呼叫。 執行的迴圈處理期間，它會呼叫系統。 第一次執行迴圈時檢視加入至檢視階層之後, 它`Draw`呼叫方法。 後續呼叫`Draw`檢視會標示為需要繪製呼叫時，會發生`SetNeedsDisplay`或`SetNeedsDisplayInRect`檢視上。

### <a name="pattern-for-graphics-code"></a>如圖形程式碼的模式

中的程式碼`Draw`實作應該描述它想繪製。 繪圖程式碼會遵循的模式，它會設定一些繪圖的狀態並呼叫方法來要求它繪製。 此模式可以歸納，如下所示：

1. 取得圖形內容。

2. 設定繪製屬性。

3. 從繪圖基本項目中建立一些幾何。

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
使用下列這一行中，它會先取得用於繪製的目前圖形內容。 您可以將圖形內容做為畫布，繪圖上發生的狀況，其中包含關於繪圖，例如筆觸和填滿色彩，以及要繪製的幾何的所有狀態。

```csharp
g.SetLineWidth (10);
UIColor.Blue.SetFill ();
UIColor.Red.SetStroke ();
``` 

取得圖形內容之後程式碼設定一些屬性來繪製，如上所示時使用。 在此情況下，系統會設定線條的寬度，筆觸和填滿色彩。 任何後續的繪圖就會使用這些屬性，因為它們保留的圖形內容的狀態。

若要建立幾何的程式碼會使用`CGPath`，可讓圖形的路徑描述從直線和曲線。 在此情況下，路徑會新增線條連接點以三角形所構成的陣列。 如下所示 Core 圖形會使用檢視繪圖座標系統中，原始伺服器所在的左上方，使用右和向下的正數 y 方向的正 x direct:

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

更豐富的形式也是繪圖的可用的。 例如，核心圖形讓您建立漸層填滿和套用裁剪路徑。 若要繪製在路徑漸層填滿上一個範例中，路徑必須先設定裁剪路徑為：

```csharp
// add the path back to the graphics context so that it is the current path
g.AddPath (path);
// set the current path to be the clipping path
g.Clip ();
```

這設定目前的路徑，如同裁剪路徑限制的路徑，例如下列程式碼中，幾何內的所有後續繪圖繪製線形漸層：

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

 ![](core-graphics-images/01-gradient-fill.png "使用漸層填滿的範例")

## <a name="modifying-line-patterns"></a>修改線條模式

線條的繪製屬性也可以修改核心圖形。 這包括變更線條的寬度和筆觸色彩，以及在列模式，如下列程式碼所示：

```csharp
//use a dashed line
g.SetLineDash (0, new nfloat[] { 10, 4 * (nfloat)Math.PI });
```

新增此程式碼，再繪製的任何作業結果長，請使用連字號之間的間距的 4 個單位的虛線的筆劃 10 單位，如下所示：

 ![](core-graphics-images/02-dashed-stroke.png "虛線的筆劃加入此程式碼，再繪製的任何作業結果")
 
請注意，當使用 Unified API Xamarin.iOS 中，陣列型別必須是`nfloat`，而且也必須明確轉換成 Math.PI。

<a name="Drawing_Images_and_Text"/>

## <a name="drawing-images-and-text"></a>繪圖影像和文字

除了檢視的圖形內容中繪製路徑，Core Graphics 也支援影像和文字。 若要繪製影像，只要建立`CGImage`並將它傳遞給`DrawImage`呼叫：

```csharp
public override void Draw (CGRect rect)
{
    base.Draw (rect);

    using(CGContext g = UIGraphics.GetCurrentContext ()){
        g.DrawImage (rect, UIImage.FromFile ("MyImage.png").CGImage);
    }
}
```

不過，這會產生映像上下顚倒，如下所示：

 ![](core-graphics-images/03-upside-down-monkey.png "上下顚倒映像")

原因是映像的繪圖的核心圖形原點位於左下方，而檢視的左上方的原點。 因此，若要正確地顯示映像，來源必須經過修改，這可藉由修改*目前的轉換矩陣* *(CMT)*。 CMT 定義點所在，也稱為*使用者空間*。 反轉在 y 方向 CMT 和移位所負 y 方向的邊界的高度可以翻轉影像。

圖形內容會提供 helper 方法來轉換 CMT。 在此情況下， `ScaleCTM` 「 翻轉 」 繪圖和`TranslateCTM`移至左上方，如下所示：

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

產生的映像接著會顯示垂直：

 ![](core-graphics-images/04-upright-monkey.png "範例映像顯示垂直")

> [!IMPORTANT]
> 圖形內容的變更套用至所有後續的繪圖作業。 因此，CMT 轉換時，它會影響任何其他繪圖。 比方說，如果您可以繪製三角形 CMT 轉換之後，它會出現上下顛倒。

### <a name="adding-text-to-the-image"></a>將文字新增至映像

為使用路徑和映像，將文字繪製為核心圖形牽涉到設定某些圖形的狀態，並呼叫繪製方法的相同基本模式。 在案例文字，來顯示文字的方法是`ShowText`。 新增至繪圖範例映像時，下列程式碼就會繪製某些使用核心圖形的文字：

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

如您所見，將文字繪製的圖形狀態是類似於繪製幾何。 進行文字繪製不過、 繪圖模式和字型的文字也會套用。 在此情況下，陰影也會套用，雖然套用陰影的運作方式繪製的路徑相同。

產生的文字會顯示與映像，如下所示：

 ![](core-graphics-images/05-text-on-image.png "產生的文字會顯示與映像")

## <a name="memory-backed-images"></a>記憶體為基礎的映像

除了檢視的圖形內容的繪圖，繪製記憶體的核心圖形支援備份映像，也就繪製到螢幕外。 這種方式需要：

-  建立圖形內容會受到在記憶體中點陣圖
-  設定繪圖的狀態並發出繪製命令
-  從內容取得的映像
-  移除內容


不同於`Draw`方法，其中內容會提供檢視中，在此情況下您建立的內容中有兩種：

1. 藉由呼叫`UIGraphics.BeginImageContext`(或`BeginImageContextWithOptions`)

2. 藉由建立新的 `CGBitmapContextInstance`

 `CGBitmapContextInstance` 您正在直接使用的映像位元，這類情況下，您會在其中使用自訂映像操作演算法時很有用。 在其他情況下，您應該使用`BeginImageContext`或`BeginImageContextWithOptions`。

映像內容之後，加入 繪圖程式碼就如同在`UIView`子類別。 例如，稍早用來繪製三角形的程式碼範例可用來繪製的映像，而不是在記憶體中`UIView`，如下所示：

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

繪圖，以記憶體為基礎的點陣圖的常見用法是擷取映像從任何`UIView`。 例如，下列程式碼呈現檢視的圖層，以點陣圖內容，並建立`UIImage`從它：

```csharp
UIGraphics.BeginImageContext (cellView.Frame.Size);

//render the view's layer in the current context
anyView.Layer.RenderInContext (UIGraphics.GetCurrentContext ());

//get a UIImage from the context
UIImage anyViewImage = UIGraphics.GetImageFromCurrentImageContext ();
UIGraphics.EndImageContext ();
```

## <a name="drawing-pdfs"></a>繪製 Pdf

除了映像，核心圖形支援 PDF 繪圖。 例如映像，您可以呈現在記憶體中的 PDF，以及讀取以轉譯 PDF `UIView`。

### <a name="pdf-in-a-uiview"></a>在 UIView PDF

Core 圖形也支援從檔案讀取的 PDF 和轉譯檢視使用`CGPDFDocument`類別。 `CGPDFDocument`類別代表在程式碼中的 PDF，並可用來讀取和繪製頁面。

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

`Draw`方法可用`CGPDFDocument`讀取到頁面`CGPDFPage`轉譯藉由呼叫`DrawPDFPage`，如下所示：

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

您需要的記憶體中 PDF，藉由呼叫建立 PDF 內容`BeginPDFContext`。 繪製成 PDF 是細微的頁面。 藉由呼叫啟動每一頁`BeginPDFPage`並完成藉由呼叫`EndPDFContent`，圖形程式碼之間。 此外，因為映像的繪圖，與記憶體支援繪圖會使用 PDF 左下方，可以藉由只修改 CMT 佔用 origin 例如映像中。

下列程式碼示範如何繪製文字至 PDF:

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

產生的文字繪製成 PDF，然後包含在`NSData`，可供儲存、 上傳、 電子郵件等。


## <a name="summary"></a>總結

在本文中我們探討了透過提供的圖形功能*Core Graphics* framework。 我們了解如何使用核心圖形的內容中繪製幾何、 影像和 Pdf`UIView,`以及以記憶體為基礎的圖形內容。

## <a name="related-links"></a>相關連結

- [Core 圖形範例](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)
- [圖形和動畫逐步解說](~/ios/platform/graphics-animation-ios/graphics-animation-walkthrough.md)
- [Core 動畫](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core 動畫配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
