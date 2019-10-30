---
title: 在 Xamarin 中 TextKit
description: 本檔說明如何在 Xamarin 中使用 TextKit。 TextKit 提供強大的文字版面配置和呈現功能。
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6c1dee464de1f7ba708b1f7d60affc1616e71ee9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031412"
---
# <a name="textkit-in-xamarinios"></a>在 Xamarin 中 TextKit

TextKit 是新的 API，提供強大的文字版面配置和轉譯功能。 它建置於低層級核心文字架構之上，但比核心文字更容易使用。

為了讓 TextKit 的功能可供標準控制項使用，已將數個 iOS 文字控制項重新實作為使用 TextKit，包括：

- UITextView
- UITextField
- UILabel

## <a name="architecture"></a>架構

TextKit 提供了多層式架構，可將文字儲存與版面配置和顯示區隔開，包括下列類別：

- `NSTextContainer` –提供用於配置文字的座標系統和 geometry。
- `NSLayoutManager` –藉由將文字轉換成字元來配置文字。
- `NSTextStorage` –保存文字資料，以及處理批次文字屬性更新。 所有批次更新都會交給建構管理員進行實際的變更處理，例如重新計算版面配置和重繪文字。

這三個類別會套用至呈現文字的視圖。 內建的文字處理視圖（例如 `UITextView`、`UITextField`和 `UILabel` 已經設定好，但您也可以建立它們並將它們套用至任何 `UIView` 實例。

下圖說明此架構：

 ![](textkit-images/textkitarch.png "This figure illustrates the TextKit architecture")

## <a name="text-storage-and-attributes"></a>文字儲存和屬性

`NSTextStorage` 類別會保存視圖所顯示的文字。 它也會將對文字所做的任何變更（例如字元或其屬性的變更）傳達給版面建構管理員以供顯示。 `NSTextStorage` 繼承自 `MSMutableAttributed` 字串，可讓您在 `BeginEditing` 和 `EndEditing` 呼叫之間的批次中指定文字屬性的變更。

例如，下列程式碼片段會分別指定前景和背景色彩的變更，並以特定範圍為目標：

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

呼叫 `EndEditing` 之後，會將變更傳送至 [建構管理員]，然後執行任何必要的版面配置和轉譯計算，以便在視圖中顯示文字。

## <a name="layout-with-exclusion-path"></a>具有排除路徑的版面配置

TextKit 也支援版面配置，並允許複雜的案例，例如多重資料行文字，並在指定的路徑前後流動文字，稱為*排除路徑*。 排除路徑會套用至文字容器，以修改文字版面配置的幾何，使文字在指定的路徑周圍流動。

加入排除路徑需要在版面建構管理員上設定 `ExclusionPaths` 屬性。 設定此屬性會導致版面建構管理員使文字配置失效，並在排除路徑周圍傳送文字。

### <a name="exclusion-based-on-a-cgpath"></a>根據 CGPath 排除

請考慮下列 `UITextView` 子類別的執行：

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

這段程式碼會使用核心圖形，新增在文字視圖上繪製的支援。 由於 `UITextView` 類別的建立是為了將 TextKit 用於其文字轉譯和版面配置，因此可以使用 TextKit 的所有功能，例如設定排除路徑。

> [!IMPORTANT]
> 這個範例子類別 `UITextView` 新增觸控繪圖支援。 `UITextView` 不需要子類別化，就能取得 TextKit 的功能。

使用者在文字視圖上繪製之後，會藉由設定 `UIBezierPath.CGPath` 屬性，將繪製的 `CGPath` 套用至 `UIBezierPath` 實例：

```csharp
bezierPath.CGPath = exclusionPath;
```

更新下列程式程式碼，會在路徑周圍進行文字版面配置更新：

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

下列螢幕擷取畫面解說文字佈局如何變更為繪製路徑周圍的流程：

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")-->
![](textkit-images/exclusionpath2.png "This screenshot illustrates how the text layout changes to flow around the drawn path")

請注意，在此情況下，版面建構管理員的 `AllowsNonContiguousLayout` 屬性會設定為 false。 這會導致在文字變更的所有情況下重新計算版面配置。 若將此設定為 true，可以避免進行完整版面配置重新整理（尤其是在大型檔的情況下），因而受益于效能。 不過，在某些情況下，將 `AllowsNonContiguousLayout` 設定為 true 會防止排除路徑更新配置（例如，如果在執行時間輸入文字，但在設定的路徑之前沒有尾端的回車）。

## <a name="related-links"></a>相關連結

- [IOS 7 簡介（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [iOS 7 使用者介面概觀](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [背景](~/ios/app-fundamentals/backgrounding/index.md)
