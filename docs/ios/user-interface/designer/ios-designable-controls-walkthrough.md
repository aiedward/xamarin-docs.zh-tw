---
title: 搭配 iOS 設計工具使用自訂控制項
description: 本文件說明如何建立自訂控制項，並將它與 Xamarin 設計工具，適用於 iOS。 它會顯示如何將控制項設為 iOS 設計工具的工具箱中可用、 實作控制項，讓它正確轉譯，以及設計時間，以及更多。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 98504c9d5f210d55a2be4c85c52d4bc1418fc223
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61154363"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>搭配 iOS 設計工具使用自訂控制項

## <a name="requirements"></a>需求

Visual Studio for Mac 和 Visual Studio 2017 和更新版本在 Windows 中使用 Xamarin iOS 設計工具。

本指南假設您熟悉內容涵蓋[快速入門引導](~/ios/get-started/index.md)。

## <a name="walkthrough"></a>逐步解說

> [!IMPORTANT]
> 在 Xamarin.Studio 5.5 中從開始，在其中建立自訂控制項的方式是稍有不同於舊版。 若要建立自訂控制項，即`IComponent`介面是必要項目 （相關聯的實作方法中） 或類別，可以使用註解`[DesignTimeVisible(true)]`。 第二個方法是在下列範例中，逐步解說使用。


1. 建立新的方案，從**iOS > 應用程式 > 單一檢視應用程式 > C#** 範本，其命名為`ScratchTicket`，然後繼續執行 [新增專案] 精靈：

    [![](ios-designable-controls-walkthrough-images/01new.png "建立新的方案")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 建立新的空的類別檔案，名為`ScratchTicketView`:

    [![](ios-designable-controls-walkthrough-images/02new.png "建立新的 ScratchTicketView 類別")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 新增下列程式碼`ScratchTicketView`類別：

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


1. 新增`FillTexture.png`，`FillTexture2.png`並`Monkey.png`檔案 (可用[從 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) 來**資源**資料夾。
    
1. 按兩下`Main.storyboard`檔案，以在設計工具中開啟它：

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS 設計工具")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. 拖放**映像檢視**從**工具箱**拖曳至腳本中的檢視。

    [![](ios-designable-controls-walkthrough-images/04new.png "映像檢視加入至版面配置")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. 選取 **映像檢視**並變更其**映像**屬性設`Monkey.png`。

    [![](ios-designable-controls-walkthrough-images/05new.png "將映像檢視映像屬性設定為 Monkey.png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. 因為我們會使用大小類別我們需要以限制此映像檢視。 按兩次，以將它放入條件約束模式映像上。 讓我們圖形中央 center 釘選的控制代碼，即可限制，並且垂直和水平對齊：

    [![](ios-designable-controls-walkthrough-images/06new.png "將影像置中")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 若要限制的高度和寬度，大小固定的控制代碼 （'骨' 形狀控點） 上按一下並選取寬度和高度分別：

    [![](ios-designable-controls-walkthrough-images/07new.png "新增條件約束")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. 更新根據條件約束，依序按一下 [更新] 按鈕的工具列中的框架：

    [![](ios-designable-controls-walkthrough-images/08new.png "限制式工具列")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. 接下來，建置專案，讓**臨時票證檢視**下方會出現 **「 自訂 」 元件**[工具箱] 中：

    [![](ios-designable-controls-walkthrough-images/09new.png "自訂元件 [工具箱]")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. 將拖放**臨時票證檢視**，使其出現透過 monkey 映像。 調整拖曳控點，讓 [Scratch] 票證檢視涵蓋 monkey 完全，如下所示：

    [![](ios-designable-controls-walkthrough-images/10new.png "透過映像檢視一個臨時票證檢視")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 藉由繪製以選取這兩個檢視的周框限制臨時票證檢視到映像檢視。 選取選項，以限制根據條件約束，寬度、 高度、 中央和中間和更新畫面格，如下所示：

    [![](ios-designable-controls-walkthrough-images/11new.png "置中顯示與新增的條件約束")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. 執行應用程式，並 「 臨時關閉 」 以顯示 monkey 映像。

    [![](ios-designable-controls-walkthrough-images/10-app.png "執行範例應用程式")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>新增設計階段屬性

設計工具也會包含自訂控制項的屬性類型的數值、 列舉、 字串、 bool、 CGSize、 UIColor 和 UIImage 的設計階段支援。 為了示範，讓我們將屬性新增至`ScratchTicketView`設為"稍微示範一下關閉。 」 的映像

將下列程式碼加入`ScratchTicketView`屬性類別：

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

我們可能也想要新增 null 檢查，以`Draw`方法，就像這樣：

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

包括`ExportAttribute`並`BrowsableAttribute`引數設定為使用`true`顯示在設計工具的屬性會導致**屬性**面板。 屬性變更為另一個映像包含的專案，例如`FillTexture2.png`，導致在設計階段控制項更新，如下所示：

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "編輯設計階段屬性")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>總結

在本文中我們逐步解說如何建立自訂控制項，以及使用 iOS 設計工具的 iOS 應用程式中使用該程式。 我們了解如何建立並建置將它提供給應用程式在設計工具中的控制項**工具箱**。 此外，我們探討了如何實作控制項，使它正確轉譯在設計階段和執行階段，以及如何公開 （expose） 的設計工具中的自訂控制項屬性。



## <a name="related-links"></a>相關連結

- [ScratchTicket （範例）](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [所需的映像 （範例）](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
