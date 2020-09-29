---
title: 在 Xamarin 中使用核心圖形和核心動畫
description: 本文將逐步示範如何建立使用核心圖形和核心動畫的應用程式。 它會示範如何在螢幕上繪製以回應使用者觸控，以及如何以動畫顯示影像以沿著路徑移動。
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1245531d68e43c833e892fe888830decc9d07c85
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430603"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>在 Xamarin 中使用核心圖形和核心動畫

在這個逐步解說中，我們將使用核心圖形來繪製路徑，以回應觸控輸入。 然後，我們將新增 `CALayer` 包含影像的，我們將沿著路徑建立動畫。

下列螢幕擷取畫面會顯示完成的應用程式：

![完成的應用程式](graphics-animation-walkthrough-images/00-final-app.png)

開始之前，請先下載本指南隨附的 *GraphicsDemo* 範例。 您可以在 [此](/samples/xamarin/ios-samples/graphicsandanimation) 下載它，並將其放在 **GraphicsWalkthrough** 目錄中，啟動名為 **GraphicsDemo_starter** 的專案，方法是按兩下該專案，然後開啟 `DemoView` 類別。

## <a name="drawing-a-path"></a>繪製路徑

1. 在中 `DemoView` `CGPath` ，將變數加入至類別，並在函式中將它具現化。 此外，也會宣告兩個變數，也就是 `CGPoint` `initialPoint` `latestPoint` 用來捕捉我們用來建立路徑的觸控點：

    ```csharp
    public class DemoView : UIView
    {
        CGPath path;
        CGPoint initialPoint;
        CGPoint latestPoint;

        public DemoView ()
        {
            BackgroundColor = UIColor.White;

            path = new CGPath ();
        }
    }
    ```

2. 加入以下 using 指示詞：

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. 接下來，覆寫 `TouchesBegan` 和 `TouchesMoved,` 新增下列的執行，以分別抓取初始觸控點和每個後續觸控點：

    ```csharp
    public override void TouchesBegan (NSSet touches, UIEvent evt){

        base.TouchesBegan (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt){

        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }
    ```

    `SetNeedsDisplay` 將在每次接觸移動時呼叫，以便在 `Draw` 下一次執行迴圈階段呼叫。

4. 我們會將程式程式碼新增至方法中的路徑 `Draw` ，並使用紅色的虛線來繪製。 [實行 `Draw` ](~/ios/platform/graphics-animation-ios/core-graphics.md)使用如下所示的程式碼：

    ```csharp
    public override void Draw (CGRect rect){

        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            //get graphics context
            using(CGContext g = UIGraphics.GetCurrentContext ()){

                //set up drawing attributes
                g.SetLineWidth (2);
                UIColor.Red.SetStroke ();

                //add lines to the touch points
                if (path.IsEmpty) {
                    path.AddLines (new CGPoint[]{initialPoint, latestPoint});
                } else {
                    path.AddLineToPoint (latestPoint);
                }

                //use a dashed line
                g.SetLineDash (0, new nfloat[] { 5, 2 * (nfloat)Math.PI });

                //add geometry to graphics context and draw it
                g.AddPath (path);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
    ```

如果我們現在執行應用程式，我們可以觸控在螢幕上繪製，如下列螢幕擷取畫面所示：

![在螢幕上繪製](graphics-animation-walkthrough-images/01-path.png)

## <a name="animating-along-a-path"></a>沿著路徑建立動畫

既然我們已將程式碼實作為允許使用者繪製路徑的程式碼，接下來讓我們新增程式碼，沿著繪製的路徑建立圖層的動畫。

1. 首先，將 [`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md) 變數新增至類別，並在函式中建立它：

    ```csharp
    public class DemoView : UIView
        {
            …

            CALayer layer;

            public DemoView (){
                …

                //create layer
                layer = new CALayer ();
                layer.Bounds = new CGRect (0, 0, 50, 50);
                layer.Position = new CGPoint (50, 50);
                layer.Contents = UIImage.FromFile ("monkey.png").CGImage;
                layer.ContentsGravity = CALayer.GravityResizeAspect;
                layer.BorderWidth = 1.5f;
                layer.CornerRadius = 5;
                layer.BorderColor = UIColor.Blue.CGColor;
                layer.BackgroundColor = UIColor.Purple.CGColor;
            }
    ```

2. 接下來，當使用者從螢幕中提起手指時，我們會將圖層新增為視圖圖層的子層。 然後，我們將使用路徑建立主要畫面格動畫，並以動畫顯示圖層 `Position` 。

    為了達成此目的，我們需要覆寫 `TouchesEnded` 並加入下列程式碼：

    ```csharp
    public override void TouchesEnded (NSSet touches, UIEvent evt)
        {
            base.TouchesEnded (touches, evt);

            //add layer with image and animate along path

            if (layer.SuperLayer == null)
                Layer.AddSublayer (layer);

            // create a keyframe animation for the position using the path
            layer.Position = latestPoint;
            CAKeyFrameAnimation animPosition = (CAKeyFrameAnimation)CAKeyFrameAnimation.FromKeyPath ("position");
            animPosition.Path = path;
            animPosition.Duration = 3;
            layer.AddAnimation (animPosition, "position");
        }
    ```

3. 立即執行應用程式，並在繪製之後，新增具有影像的圖層，並沿著繪製的路徑移動：

![系統會新增具有影像的圖層，並沿著繪製的路徑移動](graphics-animation-walkthrough-images/00-final-app.png)

## <a name="summary"></a>摘要

在本文中，我們會逐步解說將圖形和動畫概念系結在一起的範例。 首先，我們示範了如何使用核心圖形來繪製中的路徑，以 `UIView` 回應使用者觸控。 接著，我們示範了如何使用核心動畫來沿著該路徑進行影像行進。

## <a name="related-links"></a>相關連結

- [Core Animation](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core Graphics](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [核心動畫食譜](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)