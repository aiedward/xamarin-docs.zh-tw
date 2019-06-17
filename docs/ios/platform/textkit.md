---
title: 在 Xamarin.iOS 中 TextKit
description: 本文件說明如何在 Xamarin.iOS 中使用 TextKit。 TextKit 提供功能強大的文字版面配置和轉譯功能。
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: f08e37d17cc32e45232d54cc4a51bb48d7ec94b1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61184502"
---
# <a name="textkit-in-xamarinios"></a>在 Xamarin.iOS 中 TextKit

TextKit 是新的 API，提供功能強大的文字版面配置和轉譯功能。 它以低層的核心文字架構，建置，但更容易就能使用比核心文字。

若要讓 TextKit 的功能可供標準控制項，數個 iOS 文字控制項已經重新實使用 TextKit，包括：

-  UITextView
-  UITextField
-  UILabel

## <a name="architecture"></a>架構

TextKit 提供文字儲存分開的版面配置和顯示，包括下列類別的多層式的架構：

-  `NSTextContainer` -提供用來配置文字的 geometry 與座標系統。
-  `NSLayoutManager` – 說明文字藉由將文字轉換成圖像 （glyph）。 
-  `NSTextStorage` – 包含文字資料，以及處理批次文字的屬性更新。 任何批次更新會散發給配置管理員的變更，例如重新計算版面配置，以及重新繪製文字的實際處理。


這三個類別會套用至轉譯文字檢視。 處理檢視，例如內建文字`UITextView`， `UITextField`，並`UILabel`已經有這些設定，但您可以建立並將它們套用至任何`UIView`執行個體。

下圖說明此架構：

 ![](textkit-images/textkitarch.png "下圖說明如何 TextKit 架構")

## <a name="text-storage-and-attributes"></a>文字的儲存體和屬性

`NSTextStorage`類別包含的檢視所顯示的文字。 它也會通訊文字-例如變更成字元或其屬性-配置管理員顯示的任何變更。 `NSTextStorage` 繼承自`MSMutableAttributed`允許變更之間的批次中指定的文字屬性的字串`BeginEditing`和`EndEditing`呼叫。

比方說，下列程式碼片段指定變更前景和背景色彩，分別與目標特定的範圍：

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

之後`EndEditing`是呼叫，將變更傳送到配置管理員，它接著會執行任何必要的版面配置和轉譯計算要在檢視中顯示的文字。

## <a name="layout-with-exclusion-path"></a>排除路徑的版面配置

TextKit 也支援版面配置，並允許複雜的情況下，例如呼叫多重資料行的文字和指定之路徑的流動繞*排除路徑*。 排除路徑會套用至文字容器，可修改的文字版面配置，而導致文字周圍的指定路徑幾何。

新增排除路徑需要設定`ExclusionPaths`配置管理員上的屬性。 設定這個屬性會導致配置管理員，使其失效的文字版面配置和流程周圍排除路徑的文字。

### <a name="exclusion-based-on-a-cgpath"></a>根據 CGPath 排除

請考慮下列`UITextView`子類別的實作：

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

此程式碼加入使用核心圖形文字檢視上所繪製的支援。 因為`UITextView`類別現已內建 TextKit 用於其文字轉譯和版面配置，它可以使用 TextKit，例如設定排除路徑的所有功能。

> [!IMPORTANT]
> 此範例中的子類別`UITextView`將描繪支援的觸控。 子類別化`UITextView`不需要取得 TextKit 的功能。



使用者繪製文字的檢視，繪製後`CGPath`套用至`UIBezierPath`執行個體，藉由設定`UIBezierPath.CGPath`屬性：

```csharp
bezierPath.CGPath = exclusionPath;
```

更新下列程式碼行，可讓更新括住路徑的文字版面配置：

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

下列螢幕擷取畫面說明如何將文字版面配置變更為周圍繪製的路徑：

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "此螢幕擷取畫面說明如何將文字版面配置變更為周圍繪製的路徑")

請注意，配置管理員`AllowsNonContiguousLayout`屬性在此情況下設定為 false。 這會導致版面配置，以重新計算所有案例中，文字會變更位置。 將此設為 true 可能有益於效能藉由避免完整版面配置的重新整理，尤其是在大型的文件。 不過，設定`AllowsNonContiguousLayout`以 true 會防止排除路徑更新版面配置，在某些情況下-例如，如果在執行階段輸入的文字，則沒有尾端換行之前所設定的路徑。


## <a name="related-links"></a>相關連結

- [IOS 7 （範例） 的簡介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
