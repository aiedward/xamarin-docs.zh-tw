---
title: 搭配 iOS 設計工具使用自訂控制項
description: 本檔說明如何建立自訂控制項，並搭配 Xamarin Designer for iOS 使用。 它會示範如何在 iOS 設計工具的 [工具箱] 中，將控制項設為可供使用，並執行控制項以正確轉譯和設計階段等等。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: ad07d6e7381c646273eae8fe6aaecb2d487027f7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436809"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>搭配 iOS 設計工具使用自訂控制項

## <a name="requirements"></a>需求

Xamarin Designer for iOS 可在 Windows 上的 Visual Studio for Mac 和 Visual Studio 2017 和更新版本中使用。

本指南假設您已經熟悉 [消費者入門指南](~/ios/get-started/index.md)中涵蓋的內容。

## <a name="walkthrough"></a>逐步解說

> [!IMPORTANT]
> 自 Xamarin. Studio 5.5 開始，自訂控制項的建立方式與舊版稍有不同。 若要建立自訂控制項，必須 `IComponent` 有介面 (與相關聯的實方法) 或可以使用標注類別 `[DesignTimeVisible(true)]` 。 第二個方法是在下列逐步解說範例中使用。

1. 從 IOS > 應用程式中建立新的解決方案 **> 單一視圖應用程式 > c #** 範本，將它命名 `ScratchTicket` ，然後繼續進行 [新增專案] wizard：

    [![建立新的解決方案](ios-designable-controls-walkthrough-images/01new.png)](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 建立名為的新空白類別檔案 `ScratchTicketView` ：

    [![建立新的 ScratchTicketView 類別](ios-designable-controls-walkthrough-images/02new.png)](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 為類別新增下列程式碼 `ScratchTicketView` ：

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;

    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;

            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }

            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }

            public ScratchTicketView()
            {
                Initialize();
            }

            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }

            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }

            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }

            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }

            public override void Draw(CGRect rect)
            {
                base.Draw(rect);

                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);

                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();

                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }

                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```

1. 在 `FillTexture.png` `FillTexture2.png` [資源] 資料夾中新增 `Monkey.png` (可[從 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) **Resources**取得的和檔案。

1. 按兩下該檔案， `Main.storyboard` 以在設計工具中開啟它：

    [![IOS 設計工具](ios-designable-controls-walkthrough-images/03new.png)](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. 將 **影像視圖** 從 [ **工具箱** ] 拖放到腳本的視圖上。

    [![已新增至版面配置的影像視圖](ios-designable-controls-walkthrough-images/04new.png)](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. 選取 **影像視圖** ，並將其 **影像** 屬性變更為 `Monkey.png` 。

    [![將影像視圖影像屬性設定為 Monkey.png](ios-designable-controls-walkthrough-images/05new.png)](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. 因為我們使用大小類別，所以需要限制此影像視圖。 按一下影像兩次，以使其進入條件約束模式。 讓我們將它限制在中央，方法是按一下中央釘選控點，並垂直和水準對齊：

    [![將影像置中](ios-designable-controls-walkthrough-images/06new.png)](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 若要限制高度和寬度，請按一下大小釘選控點， (' 骨骼 ' 成形的控點) ，然後分別選取寬度和高度：

    [![加入條件約束](ios-designable-controls-walkthrough-images/07new.png)](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. 按一下工具列中的 [更新] 按鈕，以根據條件約束更新框架：

    [![條件約束工具列](ios-designable-controls-walkthrough-images/08new.png)](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. 接下來，建立專案，讓 **臨時票證視圖** 出現在 [工具箱] 中的 [ **自訂群組件** ] 底下：

    [![自訂群組件工具箱](ios-designable-controls-walkthrough-images/09new.png)](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. 拖放 **臨時票證視圖** ，讓它出現在猴子影像上。 調整拖曳控點，讓臨時票證視圖完全涵蓋猴子，如下所示：

    [![影像視圖的臨時票證視圖](ios-designable-controls-walkthrough-images/10new.png)](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 藉由繪製周框矩形來選取兩個視圖，以將臨時票證視圖限制為影像視圖。 選取選項，將其限制為寬度、高度、置中和中間，以及根據條件約束更新框架，如下所示：

    [![置中和加入條件約束](ios-designable-controls-walkthrough-images/11new.png)](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. 執行應用程式並「擦掉」影像以顯示猴子。

    [![執行範例應用程式](ios-designable-controls-walkthrough-images/10-app.png)](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>加入設計階段屬性

設計工具也包含屬性類型 numeric、enumeration、string、bool、CGSize、UIColor 和 UIImage 的自訂控制項設計階段支援。 為了示範，讓我們將屬性新增至， `ScratchTicketView` 以設定「有劃痕」的影像。

將下列程式碼加入至 `ScratchTicketView` 屬性的類別：

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image
{
    get { return image; }
    set {
            image = value;
              SetNeedsDisplay ();
        }
}
```

我們可能也會想要將 null 檢查加入至 `Draw` 方法，如下所示：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);

        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

包含的 `ExportAttribute` 和會 `BrowsableAttribute` 將引數設定為，以 `true` 產生設計工具 **屬性** 面板中顯示的屬性。 將屬性變更為專案所包含的另一個影像（例如 `FillTexture2.png` ），會導致控制項在設計階段更新，如下所示：

 [![編輯設計階段屬性](ios-designable-controls-walkthrough-images/11-customproperty.png)](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>摘要

在本文中，我們逐步解說如何建立自訂控制項，以及如何在 iOS 應用程式中使用 iOS 設計工具來使用它。 我們已瞭解如何建立控制項，並建立控制項，以便在設計工具的 [ **工具箱**] 中讓應用程式使用。 此外，我們也探討了如何在設計階段和執行時間正確地轉譯控制項，以及如何在設計工具中公開自訂控制項屬性。

## <a name="related-links"></a>相關連結

- [ScratchTicket (範例) ](/samples/xamarin/ios-samples/scratchticket)
- [ (範例) 所需的映射 ](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)