---
title: TextKit
description: "文字套件 API 提供功能強大的文字 Xamarin.iOS 配置和轉譯功能。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: d3a370c3a8d930c817d38422b249b496532fa33f
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="text-kit"></a>文字套件

文字套件是新的 API 可提供配置和轉譯功能強大的文字。 它會建置於核心文字架構的低層級之上，但更容易使用的核心文字以外。

若要讓使用標準控制項文字套件的功能，數個 iOS 文字控制項已被重新實作使用文字套件，包括：

-  UITextView
-  UITextField
-  UILabel


## <a name="architecture"></a>架構

文字套件提供隔開的版面配置和顯示方式，包括下列類別中的文字儲存體分層的架構：

-  `NSTextContainer` – 提供座標系統以及用來配置文字的幾何。
-  `NSLayoutManager` – 配置文字藉由開啟成圖像的文字。 
-  `NSTextStorage` – 保存的文字資料，以及處理批次文字屬性的更新。 任何批次更新都交給的實際處理的變更，例如重新計算配置，而且重新繪製文字的版面配置管理員。


這三個類別會套用至會呈現文字的檢視。 內建的文字處理檢視，例如`UITextView`， `UITextField`，和`UILabel`這些設定，但您可以建立並將它們套用至任何`UIView`以及執行個體。

下圖說明這個架構：

 ![](textkit-images/textkitarch.png "此圖說明文字套件架構")

## <a name="text-storage-and-attributes"></a>文字的儲存體和屬性

`NSTextStorage`類別保存的檢視所顯示的文字。 它也會變更文字-例如對字元或其屬性-顯示配置管理員進行通訊。 `NSTextStorage` 繼承自`MSMutableAttributed`允許變更文字屬性之間的批次中指定的字串`BeginEditing`和`EndEditing`呼叫。

比方說，下列程式碼片段指定變更前景和背景色彩，分別為目標的特定範圍：

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

之後`EndEditing`是呼叫，所做的變更傳送至版面配置管理員中，接著會執行任何必要的配置和轉譯計算要在檢視中顯示的文字。

## <a name="layout-with-exclusion-path"></a>排除路徑的版面配置

文字套件也支援版面配置，並允許複雜的案例，例如呼叫多重資料行的文字和指定的路徑的流動繞*排除路徑*。 排除路徑會套用至文字容器，以修改文字版面配置，導致繞過指定的路徑文字的幾何。

加入排除路徑必須設定`ExclusionPaths`配置管理員上的屬性。 將此屬性會使文字版面配置和資料流程周圍排除路徑的文字配置管理員。

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

這個程式碼加入 [文字] 檢視使用核心圖形上繪製的支援。 因為`UITextView`類別現在建立要用於其文字轉譯和版面配置中的文字套件，它使用的組件的文字，例如設定排除路徑的所有功能。

> [!IMPORTANT]
> 此範例子類別`UITextView`新增觸控描繪支援。 子類別化`UITextView`，就不需要取得文字套件的功能。



使用者繪製文字的檢視，繪製之後`CGPath`套用至`UIBezierPath`藉由設定執行個體`UIBezierPath.CGPath`屬性：

```csharp
bezierPath.CGPath = exclusionPath;
```

更新下列程式碼會進行更新住路徑的文字版面配置：

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

下列螢幕擷取畫面說明了如何文字版面配置變更為流程周圍繪製的路徑：

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")--> 
![](textkit-images/exclusionpath2.png "這個螢幕擷取畫面說明了如何文字版面配置變更為流程周圍繪製的路徑")

請注意，配置管理員`AllowsNonContiguousLayout`屬性在此情況下設定為 false。 這會導致重新計算所有的案例，其中的文字變更版面配置。 將此設定為 true，可能藉由避免發生完整配置重新整理，特別是在大型文件的情況下改善效能。 不過，設定`AllowsNonContiguousLayout`至 true 可避免對排除路徑更新配置，在某些情況下-例如，如果在執行階段輸入文字，則沒有尾端換行之前所設定的路徑。


## <a name="related-links"></a>相關連結

- [IOS 7 （範例） 的簡介](https://developer.xamarin.com/samples/monotouch/IntroToiOS7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
