---
title: 在 Xamarin.iOS 中的核心映像
description: Core 映像會使用 iOS 5，以提供映像處理和即時視訊的增強功能導入的新架構。 本文將介紹這些功能與 Xamarin.iOS 範例。
ms.prod: xamarin
ms.assetid: 91E0780B-FF8A-E70D-9CD4-419119612B2D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d71f14c26865b71eca991910df4a68f2540f9715
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115402"
---
# <a name="core-image-in-xamarinios"></a>在 Xamarin.iOS 中的核心映像

_Core 映像會使用 iOS 5，以提供映像處理和即時視訊的增強功能導入的新架構。本文將介紹這些功能與 Xamarin.iOS 範例。_

Core 映像會提供一些內建的濾鏡和效果套用至映像及影片，包括臉部偵測的 iOS 5 中導入的新架構。

本文件包含簡單的範例：

-  臉部偵測。
-  將篩選套用至映像
-  列出可用的篩選器。


這些範例應該協助您開始 Core 映像功能納入您的 Xamarin.iOS 應用程式。

## <a name="requirements"></a>需求

您必須使用最新版的 Xcode。

## <a name="face-detection"></a>臉部偵測

Core 映像臉部偵測功能的作用正如其名，它會嘗試識別相片中的人臉，並傳回它會辨識任何人臉的座標。 這項資訊可以用來計算映像中的使用者數目、 （例如在影像上繪製指標。 '標記' 人臉正面相片中），或任何其他您想得到。

CoreImage\SampleCode.cs 此程式碼會示範如何建立和使用內嵌影像臉部偵測：

```csharp
var image = new UIImage("photoFace.JPG");
var context = CIContext.FromOptions(null);
var detector = CIDetector.CreateFaceDetector (context, true);
var ciImage = CIImage.FromCGImage(image.CGImage);
CIFeature[] features = detector.FeaturesInImage(ciImage);
```

功能陣列將填入`CIFaceFeature`物件 （如果偵測到任何臉部）。 沒有`CIFaceFeature`每個臉部。 `CIFaceFeature` 具有下列屬性：

-  HasMouthPosition – 說話是否偵測到此表面。
-  HasLeftEyePosition – 左的眼睛是否偵測到此表面。
-  HasRightEyePosition – 右邊的眼睛是否偵測到此表面。 
-  MouthPosition – 此表面說話的座標。
-  LeftEyePosition – 此表面左眼睛的座標。
-  RightEyePosition – 此表面右邊的眼睛的座標。


所有這些屬性的座標會在左下方 – 與不同的是使用做為原點的左上方的 UIKit 中的來源。 當使用的座標上`CIFaceFeature`務必 '翻轉' 它們。 這個非常基本的自訂映像中檢視 CoreImage\CoreImageViewController.cs 示範如何在影像上繪製三角形的 '臉部指標' (請注意`FlipForBottomOrigin`方法):

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

然後 SampleCode.cs 檔案中的映像和功能會指派之前重新繪製的映像：

```csharp
faceView.Image = image;
faceView.Features = features;
faceView.SetNeedsDisplay();
```

螢幕擷取畫面顯示範例輸出： 偵測到的臉部特徵的位置會顯示在 UITextView 並繪製到使用 CoreGraphics 來源映像。

因為臉部辨識它的運作的方式會偶爾會偵測以外 （例如這些玩具猴仔 ！） 的人臉的項目。

## <a name="filters"></a>篩選條件

有超過 50 個不同的內建篩選器和架構是可延伸，以便可以實作新的篩選器。

## <a name="using-filters"></a>使用篩選

將篩選套用至映像包含四個不同的步驟： 載入映像、 建立篩選、 套用篩選器並儲存 （或顯示） 的結果。

首先，將影像變成`CIImage`物件。

```csharp
var uiimage = UIImage.FromFile ("photo.JPG");
var ciimage = new CIImage (uiimage);
```

第二，建立篩選條件類別，並設定其屬性。

```csharp
var sepia = new CISepiaTone();
sepia.Image = ciimage;
sepia.Intensity = 0.8f;
```

第三，存取`OutputImage`屬性並呼叫`CreateCGImage`方法來呈現最終的結果。

```csharp
CIImage output = sepia.OutputImage;
var context = CIContext.FromOptions(null);
var cgimage = context.CreateCGImage (output, output.Extent);
```

最後，將映像指派至檢視來查看結果。 在真實世界應用程式中產生的映像可能會儲存到檔案系統、 相簿、 推文或電子郵件。

```csharp
var ui = UIImage.FromImage (cgimage);
imgview.Image = ui;
```

這些螢幕擷取畫面顯示的結果`CISepia`和`CIHueAdjust`篩選 CoreImage.zip 中示範的範例程式碼。

請參閱[調整合約和映像的配方的亮度](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)如需範例的`CIColorControls`篩選器。

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

此程式碼從 CoreImage\SampleCode.cs 輸出內建篩選器的完整清單和其參數。

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

[CIFilter 類別參考](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.html)描述 50 的內建篩選器和其屬性。 使用您上面的程式碼可以查詢篩選條件類別，包括參數的預設值和最大和最小允許值 （這可能之前用來驗證輸入套用篩選）。

在模擬器清單分類的輸出看起來像這樣，您可以捲動清單，以查看所有篩選條件和其參數。

 [![](introduction-to-coreimage-images/coreimage05.png "在模擬器清單分類的輸出看起來像這樣")](introduction-to-coreimage-images/coreimage05.png#lightbox)

因此您也可以探索在組件瀏覽器中或使用 Visual Studio for Mac 或 Visual Studio 中的自動完成 Xamarin.iOS.CoreImage API 列出每個篩選條件已公開為 Xamarin.iOS 中的類別。 

## <a name="summary"></a>總結

這篇文章已示範如何使用一些像是臉部偵測，並將篩選套用至映像新的 iOS 5 Core 映像架構功能。 數十種不同的映像篩選條件中有您使用的架構。

## <a name="related-links"></a>相關連結

- [Core 映像 （範例）](https://developer.xamarin.com/samples/CoreImage/)
- [調整合約和亮度的映像的配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/coreimage/adjust_contrast_and_brightness_of_an_image)
- [使用核心映像篩選器](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Conceptual/CoreImaging/ci_tasks/ci_tasks.html)
- [CIFilter 類別參考](https://developer.apple.com/library/prerelease/ios/#documentation/GraphicsImaging/Reference/QuartzCoreFramework/Classes/CIFilter_Class/Reference/Reference.htm)
