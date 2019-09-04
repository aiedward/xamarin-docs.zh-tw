---
title: 搭配 iOS 設計工具使用自訂控制項
description: 本檔描述如何建立自訂控制項, 並將它與 Xamarin Designer for iOS 搭配使用。 它會示範如何在 iOS 設計工具的 [工具箱] 中提供控制項、執行控制項, 使其正確呈現和設計階段等等。
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 65b6f11662cdb8e1814e1146faf70cd01b2c11bc
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227094"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>搭配 iOS 設計工具使用自訂控制項

## <a name="requirements"></a>需求

Xamarin Designer for iOS 可在 Windows 上的 Visual Studio for Mac 和 Visual Studio 2017 和更新版本中取得。

本指南假設您已熟悉[消費者入門指南](~/ios/get-started/index.md)中所涵蓋的內容。

## <a name="walkthrough"></a>逐步解說

> [!IMPORTANT]
> 從 Xamarin. Studio 5.5 開始, 建立自訂控制項的方式與先前的版本稍有不同。 若要建立自訂控制項, 介面`IComponent`是必要的 (使用相關聯的實方法), 或者可以使用`[DesignTimeVisible(true)]`批註類別。 在下列逐步解說範例中, 會使用後面的方法。


1. 從 iOS > 應用程式建立新的解決方案 **> 單一視圖應用程式C# >** 範本, 將`ScratchTicket`它命名為, 然後繼續進行 [新增專案] wizard:

    [![](ios-designable-controls-walkthrough-images/01new.png "建立新的解決方案")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. 建立名為`ScratchTicketView`的新空白類別檔案:

    [![](ios-designable-controls-walkthrough-images/02new.png "建立新的 ScratchTicketView 類別")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. 為類別新增下列程式`ScratchTicketView`代碼:

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


1. `FillTexture.png`將、 `FillTexture2.png` 和`Monkey.png`檔案 (可[從 GitHub](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)取得) 新增至**Resources**資料夾。

1. `Main.storyboard`按兩下檔案, 在設計工具中開啟檔案:

    [![](ios-designable-controls-walkthrough-images/03new.png "IOS 設計工具")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. 將**影像視圖**從 [**工具箱**] 拖放至分鏡腳本中的 view。

    [![](ios-designable-controls-walkthrough-images/04new.png "已新增至配置的影像視圖")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. 選取**影像視圖**, 並將其 [**影像**] `Monkey.png`屬性變更為。

    [![](ios-designable-controls-walkthrough-images/05new.png "將影像視圖影像屬性設定為猴子 .png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)


1. 當我們使用大小類別時, 我們必須限制此影像視圖。 按一下影像兩次, 使其進入條件約束模式。 讓我們將它限制在中心, 方法是按一下中央釘選控點, 並以垂直和水準方式對齊:

    [![](ios-designable-controls-walkthrough-images/06new.png "將影像置中")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. 若要限制高度和寬度, 請按一下大小固定控點 (' 骨骼 ' 形狀控點), 然後分別選取 [寬度] 和 [高度]:

    [![](ios-designable-controls-walkthrough-images/07new.png "加入條件約束")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. 按一下工具列中的 [更新] 按鈕, 根據條件約束更新框架:

    [![](ios-designable-controls-walkthrough-images/08new.png "條件約束工具列")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. 接下來, 建立專案, 讓 [**草稿票證] 視圖**出現在 [工具箱] 的 [**自訂群組件**] 底下:

    [![](ios-designable-controls-walkthrough-images/09new.png "自訂群組件工具箱")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. 拖放 [**草稿票證] 視圖**, 使其出現在猴子影像上。 調整拖曳控點, 讓「草稿票證」視圖完全涵蓋猴子, 如下所示:

    [![](ios-designable-controls-walkthrough-images/10new.png "影像視圖上的臨時票證視圖")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. 藉由繪製周框矩形來選取這兩個視圖, 將「臨時票證」視圖限制為影像視圖。 選取選項, 將其限制為寬度、高度、置中和中間, 並根據條件約束更新框架, 如下所示:

    [![](ios-designable-controls-walkthrough-images/11new.png "居中和加入條件約束")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. 執行應用程式並「暫關閉」影像以顯示猴子。

    [![](ios-designable-controls-walkthrough-images/10-app.png "執行範例應用程式")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>加入設計階段屬性

設計工具也會針對屬性類型為 numeric、enumeration、string、bool、CGSize、UIColor 和 UIImage 的自訂控制項, 提供設計階段支援。 為了示範, 讓我們將屬性新增至`ScratchTicketView` , 以設定「有劃痕」的影像。

將下列程式碼新增至`ScratchTicketView`屬性的類別:

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

我們可能也會想要在`Draw`方法中新增 null 檢查, 如下所示:

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

`ExportAttribute`將`true` 和的引數設定為,會導致屬性顯示在設計工具的[屬性`BrowsableAttribute` ] 面板中。 將屬性變更為包含在專案中的另一個影像 ( `FillTexture2.png`例如), 會導致控制項在設計階段更新, 如下所示:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "編輯設計階段屬性")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>總結

在本文中, 我們逐步解說如何建立自訂控制項, 以及如何使用 iOS 設計工具在 iOS 應用程式中取用。 我們已瞭解如何建立和建立控制項, 讓它可供設計**工具的工具箱**中的應用程式使用。 此外, 我們也探討了如何執行控制項, 使其在設計階段和執行時間都能正確轉譯, 以及如何在設計工具中公開自訂控制項屬性。



## <a name="related-links"></a>相關連結

- [ScratchTicket (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [必要的影像 (範例)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
