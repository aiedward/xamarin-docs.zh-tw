---
title: "逐步解說-使用 Xamarin 設計工具中的自訂控制項，適用於 iOS"
description: "本文章提供逐步解說顯示如何建立自訂控制項，以及適用於 iOS Xamarin 設計工具中使用它。 它會顯示如何讓控制項使用設計工具的工具箱中，因此它可以拖/放至檢視。 此外，它會顯示如何實作控制項，讓它正確呈現在設計階段與執行階段，以及如何建立可以在設計階段設定的屬性。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/22/2017
ms.openlocfilehash: e78b76a531e9f8ea88adca46fc59b2063fce14cc
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="walkthrough---using-custom-controls-with-the-xamarin-designer-for-ios"></a>逐步解說-使用 Xamarin 設計工具中的自訂控制項，適用於 iOS

_本文章提供逐步解說顯示如何建立自訂控制項，以及適用於 iOS Xamarin 設計工具中使用它。它會顯示如何讓控制項使用設計工具的工具箱中，因此它可以拖/放至檢視。此外，它會顯示如何實作控制項，讓它正確呈現在設計階段與執行階段，以及如何建立可以在設計階段設定的屬性。_

## <a name="requirements"></a>需求

針對 Mac 和 Visual Studio 2015 2017 Windows 上的 Visual Studio 中使用 Xamarin 設計工具，適用於 iOS。

本指南假設熟悉內容涵蓋[快速入門引導](~/ios/get-started/index.md)。

## <a name="walkthrough"></a>逐步解說

> [!IMPORTANT]
> 從開始 Xamarin.Studio 5.5，在其中建立自訂控制項的方式會稍有不同於舊版。 若要建立自訂控制項，或是`IComponent`介面 （具有相關聯的實作的方法） 或需要的類別可以是以註解`[DesignTimeVisible(true)]`。 第二個方法用於下列逐步解說範例。


1. 建立新的方案從**iOS > 應用程式 > 單一檢視應用程式 > C#**範本，其命名`ScratchTicket`，然後繼續進行 [新增專案] 精靈：


    [![](ios-designable-controls-walkthrough-images/01new.png "建立新的方案")](ios-designable-controls-walkthrough-images/01new.png)


1. 建立新的空的類別檔案命名為`ScratchTicketView`:


    [![](ios-designable-controls-walkthrough-images/02new.png "建立新的 ScratchTicketView 類別")](ios-designable-controls-walkthrough-images/02new.png)


1. 加入下列程式碼`ScratchTicketView`類別：

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


1. 新增`FillTexture.png`，`FillTexture2.png`和`Monkey.png`檔案 (使用[從 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) 至**資源**資料夾。
    
1. 按兩下`Main.storyboard`在設計工具中開啟的檔案：

    
    [![](ios-designable-controls-walkthrough-images/03new.png "IOS 設計工具")](ios-designable-controls-walkthrough-images/03new.png)



1. 拖放**影像檢視**從**工具箱**到分鏡腳本中的檢視。

    
    [![](ios-designable-controls-walkthrough-images/04new.png "影像檢視加入至配置")](ios-designable-controls-walkthrough-images/04new.png)


1. 選取**影像檢視**並變更其**映像**屬性`Monkey.png`。

    
    [![](ios-designable-controls-walkthrough-images/05new.png "將映像 [檢視] 影像屬性設定為 Monkey.png")](ios-designable-controls-walkthrough-images/05new.png)

    
1. 當我們使用大小類別我們需要以限制此映像的檢視。 按兩次，將它放入條件約束模式映像上。 讓我們來限制它中央按一下 center 固定控制代碼，並將其對齊垂直和水平：
    
    
    [![](ios-designable-controls-walkthrough-images/06new.png "置中顯示的影像")](ios-designable-controls-walkthrough-images/06new.png)

    
1. 若要限制的高度和寬度，按一下大小釘選的控制代碼 （'骨' 形狀控點），分別選取寬度和高度：

    
    [![](ios-designable-controls-walkthrough-images/07new.png "加入條件約束")](ios-designable-controls-walkthrough-images/07new.png)


1. 更新依據條件約束，依序按一下 [更新] 按鈕，在工具列上的框架：


    [![](ios-designable-controls-walkthrough-images/08new.png "條件約束工具列")](ios-designable-controls-walkthrough-images/08new.png)


1. 接下來，建置專案，讓**Scratch 票證檢視**會出現在**「 自訂 」 元件**工具箱中：

    
    [![](ios-designable-controls-walkthrough-images/09new.png "「 自訂 」 元件工具箱")](ios-designable-controls-walkthrough-images/09new.png)


1. 將拖放**Scratch 票證檢視**使其顯示透過猴子映像。 調整拖曳控點，讓 Scratch 票證檢視涵蓋猴子完全，如下所示：

    
    [![](ios-designable-controls-walkthrough-images/10new.png "在影像檢視可用的票證檢視")](ios-designable-controls-walkthrough-images/10new.png)


1. 限制在影像檢視 Scratch 票證檢視藉由繪製以選取這兩個檢視的周框。 選取選項，以限制它根據條件約束、 寬度、 高度、 中央和中介和更新的畫面格，如下所示：
 
    
    [![](ios-designable-controls-walkthrough-images/11new.png "置中和加入條件約束")](ios-designable-controls-walkthrough-images/11new.png)


1. 執行應用程式，「 scratch 關閉 」 以顯示猴子映像。


 [ ![](ios-designable-controls-walkthrough-images/10-app.png "執行範例應用程式")](ios-designable-controls-walkthrough-images/10-app.png)

## <a name="adding-design-time-properties"></a>將設計階段屬性

在設計工具也包含的屬性類型的數值、 列舉、 字串、 bool、 CGSize UIColor 和 UIImage 自訂控制項的設計階段支援。 為了示範，我們將屬性加入至`ScratchTicketView`設為 「 向關閉。 」 的映像

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

我們也可能想要加入 null 檢查，以`Draw`方法，就像這樣：

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

包括`ExportAttribute`和`BrowsableAttribute`引數設定為與`true`在設計工具中顯示的屬性會導致**屬性**面板。 屬性變更為另一個映像包含的專案，例如`FillTexture2.png`，導致控制項更新在設計階段，如下所示：

 [ ![](ios-designable-controls-walkthrough-images/11-customproperty.png "編輯設計階段屬性")](ios-designable-controls-walkthrough-images/10-app.png)

## <a name="summary"></a>總結

本文章中我們逐步解說如何建立自訂控制項，以及使用 iOS 設計工具的 iOS 應用程式中使用該程式。 我們了解如何建立並建置該控制項以將其提供給應用程式在設計工具中**工具箱**。 此外，我們討論了如何實作控制項，如此可正確轉譯在設計階段和執行階段，以及如何將自訂控制項設計工具中的屬性公開。



## <a name="related-links"></a>相關連結

- [ScratchTicket （範例）](https://developer.xamarin.com/samples/monotouch/ScratchTicket/)
- [必要的映像 （範例）](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
