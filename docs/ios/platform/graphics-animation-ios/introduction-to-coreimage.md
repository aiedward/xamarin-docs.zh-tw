---
title: Xamarin 中的核心映射
description: 核心映射是 iOS 5 引進的新架構，可提供影像處理和即時影片增強功能。 本文介紹這些功能與 Xamarin iOS 範例。
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: ffaa6553830a64589818c991e8f729ff7232e367
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752838"
---
# <a name="core-image-in-xamarinios"></a>Xamarin 中的核心映射

_核心映射是 iOS 5 引進的新架構，可提供影像處理和即時影片增強功能。本文介紹這些功能與 Xamarin iOS 範例。_

核心映射是 iOS 5 中引進的新架構，提供許多內建的篩選準則和效果，可套用至影像和影片，包括臉部偵測。

本檔包含簡單的範例：

- 臉部偵測。
- 將篩選套用至影像
- 列出可用的篩選準則。

這些範例應協助您開始將核心映射功能整合到您的 Xamarin iOS 應用程式中。

## <a name="requirements"></a>需求

您必須使用最新版本的 Xcode。

## <a name="face-detection"></a>臉部偵測

核心影像臉部偵測功能只會執行它所說的動作，它會嘗試識別相片中的臉部，並傳回它辨識之任何臉部的座標。 這項資訊可以用來計算影像中的人員人數、繪製影像上的指標（例如 適用于相片中的「標記」人員，或您可以想要的任何其他專案。

來自 CoreImage\SampleCode.cs 的這段程式碼示範如何在內嵌影像上建立和使用臉部偵測：

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

功能陣列將會填入`CIFaceFeature`物件（如果偵測到任何臉部）。 每個臉部`CIFaceFeature`都有一個。 `CIFaceFeature`具有下列屬性：

- HasMouthPosition –是否偵測到此臉部的嘴。
- HasLeftEyePosition –是否已偵測到此臉部的向左眼睛。
- HasRightEyePosition –是否在此臉部中偵測到適當的眼睛。 
- MouthPosition –此臉部的嘴座標。
- LeftEyePosition –此臉部的左邊眼座標。
- RightEyePosition –此臉部的適當眼睛座標。

所有這些屬性的座標在左下方都有其原點，與使用左上角作為原點的 UIKit 不同。 使用上`CIFaceFeature`的座標時，請務必「翻轉」它們。 在 CoreImage\CoreImageViewController.cs 中，這個非常基本的自訂影像視圖會示範如何在影像上繪製「臉部指標」 `FlipForBottomOrigin`三角形（請注意方法）：

```csharp
public class FaceDetectImageView : UIView
{
    public Xamarin.iOS.CoreImage.CIFeature[] Features;
    public UIImage Image;
    public FaceDetectImageView (RectangleF rect) : base(rect) {}
    CGPath path;
    public override void Draw (RectangleF rect) {
        base.Draw (rect);
        if (Image != null)
            Image.Draw(rect);

        using (var context = UIGraphics.GetCurrentContext()) {
            context.SetLineWidth(4);
            UIColor.Red.SetStroke ();
            UIColor.Clear.SetFill ();
            if (Features != null) {
                foreach (var feature in Features) { // for each face
                    var facefeature = (CIFaceFeature)feature;
                    path = new CGPath ();
                    path.AddLines(new PointF[]{ // assumes all 3 features found
                        FlipForBottomOrigin(facefeature.LeftEyePosition, 200),
                        FlipForBottomOrigin(facefeature.RightEyePosition, 200),
                        FlipForBottomOrigin(facefeature.MouthPosition, 200)
                    });
                    path.CloseSubpath();
                    context.AddPath(path);
                    context.DrawPath(CGPathDrawingMode.FillStroke);
                }
            }
        }
    }
    /// <summary>
    /// Face recognition coordinates have their origin in the bottom-left
    /// but we are drawing with the origin in the top-left, so "flip" the point
    /// </summary>
    PointF FlipForBottomOrigin (PointF point, int height)
    {
        return new PointF(point.X, height - point.Y);
    }
}
```

然後在 SampleCode.cs 檔案中，映射和功能會在重新繪製映射之前指派：

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

螢幕擷取畫面顯示範例輸出：偵測到的臉部特徵的位置會顯示在 UITextView 中，並使用 CoreGraphics 繪製到來源影像。

因為臉部辨識的運作方式，有時會偵測到人臉以外的事物（例如，這些玩具猴仔！）。

## <a name="filters"></a>篩選器

有超過50個不同的內建篩選準則，而架構是可擴充的，因此可以實作為新的篩選準則。

## <a name="using-filters"></a>使用篩選

將篩選套用至影像有四個不同的步驟：載入影像、建立篩選、套用篩選，以及儲存（或顯示）結果。

首先，將影像載入`CIImage`物件中。

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

第二，建立篩選類別並設定其屬性。

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

第三，存取`OutputImage`屬性並`CreateCGImage`呼叫方法來呈現最終結果。

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

最後，將影像指派給 view 以查看結果。 在真實世界的應用程式中，產生的影像可能會儲存到檔案系統、相片專輯、推文或電子郵件。

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

這些螢幕擷取畫面顯示 CoreImage 範例程式`CISepia`代碼`CIHueAdjust`中所示範之和篩選準則的結果。

如需`CIColorControls`篩選準則的範例，請參閱[調整影像配方的合約和亮度](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)。

```csharp
var uiimage = UIImage.FromFile("photo.JPG");
var ciimage = new CIImage(uiimage);
var hueAdjust = new CIHueAdjust();   // first filter
hueAdjust.Image = ciimage;
hueAdjust.Angle = 2.094f;
var sepia = new CISepiaTone();       // second filter
sepia.Image = hueAdjust.OutputImage; // output from last filter, input to this one
sepia.Intensity = 0.3f;
CIFilter color = new CIColorControls() { // third filter
    Saturation = 2,
    Brightness = 1,
    Contrast = 3,
    Image = sepia.OutputImage    // output from last filter, input to this one
};
var output = color.OutputImage;
var context = CIContext.FromOptions(null);
// ONLY when CreateCGImage is called do all the effects get rendered
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

```csharp
var context = CIContext.FromOptions (null);
```

```csharp
var context = CIContext.FromOptions(new CIContextOptions() {
    UseSoftwareRenderer = true  // CPU
});
var cgimage = context.CreateCGImage (output, output.Extent);
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

### <a name="listing-filters-and-their-properties"></a>列出篩選器及其屬性

來自 CoreImage\SampleCode.cs 的此程式碼會輸出內建篩選準則及其參數的完整清單。

```csharp
var filters = CIFilter.FilterNamesInCategories(new string[0]);
foreach (var filter in filters){
   display.Text += filter +"\n";
   var f = CIFilter.FromName (filter);
   foreach (var key in f.InputKeys){
     var attributes = (NSDictionary)f.Attributes[new NSString(key)];
     var attributeClass = attributes[new NSString("CIAttributeClass")];
     display.Text += "   " + key;
     display.Text += "   " + attributeClass + "\n";
   }
}
```

[CIFilter 類別參考](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html)描述50內建篩選準則和其屬性。 使用上述程式碼，您可以查詢篩選準則類別，包括參數的預設值，以及允許的最大值和最小值（這可用來驗證輸入，然後再套用篩選）。

清單類別目錄輸出在模擬器上看起來像這樣，您可以在清單中滾動，以查看所有篩選和其參數。

 [![](introduction-to-coreimage-images/coreimage05.png "清單類別目錄輸出在模擬器上看起來像這樣")](introduction-to-coreimage-images/coreimage05.png#lightbox)

列出的每個篩選器都已公開為 Xamarin 中的類別，因此您也可以在 [元件瀏覽器] 中流覽 CoreImage API，或在 Visual Studio for Mac 或 Visual Studio 中使用自動完成功能。 

## <a name="summary"></a>總結

本文示範如何使用一些新的 iOS 5 核心影像架構功能，例如臉部偵測，以及將篩選套用至影像。 架構中有數十種不同的影像篩選器可供您使用。

## <a name="related-links"></a>相關連結

- [核心影像（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/coreimage)
- [調整影像配方的合約和亮度](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [使用核心影像篩選器](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [CIFilter 類別參考](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
