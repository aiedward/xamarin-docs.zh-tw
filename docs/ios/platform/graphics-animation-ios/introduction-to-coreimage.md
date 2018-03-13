---
title: CoreImage
description: "CoreImage 是 iOS 5，以提供映像處理並即時視訊的增強功能引進的新架構。 本文介紹 Xamarin.iOS 範例與這些功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: da9b9230a466c70cd584a00af848ffe87dacbc5b
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="coreimage"></a>CoreImage

_CoreImage 是 iOS 5，以提供映像處理並即時視訊的增強功能引進的新架構。本文介紹 Xamarin.iOS 範例與這些功能。_

CoreImage 是 iOS 5 提供許多內建篩選器和效果套用至影像和視訊，包括字體偵測中導入的新架構。

這份文件包含簡單範例：

-  面對偵測。
-  將篩選套用到映像
-  列出可用的篩選器。


這些範例應協助您快速入門 CoreImage 功能併入 Xamarin.iOS 應用程式。

## <a name="requirements"></a>需求

您必須使用 Xcode 的最新版本。

## <a name="face-detection"></a>正面偵測

CoreImage 臉偵測功能的作用只什麼，該處會指示-它會嘗試識別中之相片的字體，並傳回它會辨識任何字體的座標。 這項資訊可以用來在映像的人的次數，（例如繪製在影像上的指標 為 '標記' 人臉正面相片中），或任何其他您可以將。

從 CoreImage\SampleCode.cs 這段程式碼會示範如何建立和使用內嵌影像的臉偵測：

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

功能陣列將會填入`CIFaceFeature`物件 （如果有偵測到任何字體）。 沒有`CIFaceFeature`每一面。 `CIFaceFeature` 具有下列屬性：

-  HasMouthPosition – 說話是否偵測到此一面。
-  HasLeftEyePosition – 左的眼睛是否偵測到此一面。
-  HasRightEyePosition – 是否正確的眼睛已偵測到此一面。 
-  MouthPosition – 這一面說話的座標。
-  LeftEyePosition – 這一面左眼睛的座標。
-  RightEyePosition – 這一面右眼睛的座標。


所有這些屬性的座標左下方 – 不同於使用左上方原點為 UIKit 中有其來源。 當使用的座標上`CIFaceFeature`務必 '翻轉'。 中 CoreImage\CoreImageViewController.cs 的非常基本的自訂映像檢視示範如何在影像上繪製三角形的 '朝指標' (請注意`FlipForBottomOrigin`方法):

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

然後 SampleCode.cs 檔案中的映像和功能指派之前重繪映像時：

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

螢幕擷取畫面顯示的範例輸出： 偵測到的臉部功能的位置會顯示在 UITextView 並繪製到使用 CoreGraphics 來源映像。

因為臉部辨識它的運作的方式會偶爾會偵測除了人力表面 （例如這些玩具猴子 ！）。

## <a name="filters"></a>篩選條件

有超過 50 個不同的內建篩選器，以便您可以實作新的篩選器，framework 就是可延伸。

## <a name="using-filters"></a>使用篩選器

將篩選套用至映像具有四個不同的步驟： 載入影像、 建立篩選、 套用篩選條件和儲存 （或顯示） 的結果。

首先，請載入影像變成`CIImage`物件。

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

第二，建立篩選類別，並設定其屬性。

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

第三，存取`OutputImage`屬性並呼叫`CreateCGImage`方法以呈現最終的結果。

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

最後，指派的影像檢視，以查看結果。 在真實世界的應用程式所產生的影像檔案可能儲存至檔案系統、 相簿、 推文或電子郵件。

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

這些螢幕擷取畫面顯示的結果`CISepia`和`CIHueAdjust`篩選 CoreImage.zip 中所示範的範例程式碼。

請參閱[調整合約和亮度的映像配方](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)的範例，`CIColorControls`篩選器。

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

### <a name="listing-filters-and-their-properties"></a>列出篩選器和其屬性

這段程式碼從 CoreImage\SampleCode.cs 輸出完整的內建篩選器清單和它們的參數。

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

[CIFilter 類別參考](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html)說明 50 的內建篩選器和其屬性。 使用上方您的程式碼可以查詢的篩選器的類別，包括參數的預設值和最大和最小允許值 （這可能之前用來驗證輸入套用篩選器）。

模擬器上列示分類輸出看起來像這樣，您可以捲動清單，以查看所有篩選條件和它們的參數。

 [![](introduction-to-coreimage-images/coreimage05.png "在模擬器上列示分類輸出看起來像這樣")](introduction-to-coreimage-images/coreimage05.png#lightbox)

因此您也可以瀏覽 Xamarin.iOS.CoreImage API，在組件的瀏覽器，或是使用 Visual Studio for Mac 或 Visual Studio 中的自動完成列出每個篩選條件已公開為 Xamarin.iOS 中的類別。 

## <a name="summary"></a>總結

本文示範了如何使用某些新 iOS 5 CoreImage 架構功能，例如臉偵測和套用篩選至映像。 有許多不同的映像篩選器讓您可以使用 framework 中可用。

## <a name="related-links"></a>相關連結

- [Core 映像 （範例）](https://developer.xamarin.com/samples/CoreImage/)
- [調整合約和映像配方的亮度](https://developer.xamarin.com/recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [使用 CoreImage 篩選器](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [CIFilter 類別參考](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
