---
title: 在 Xamarin 中使用核心圖形和核心動畫
description: 本文會逐步示範如何建立使用核心圖形和核心動畫的應用程式。 它會示範如何在螢幕上繪製以回應使用者觸控，以及如何以動畫顯示影像沿著路徑移動。
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b35e88cfdc0bce321068951f1617885c90331c83
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032443"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>在 Xamarin 中使用核心圖形和核心動畫

在此逐步解說中，我們將使用核心圖形來繪製路徑，以回應觸控輸入。 然後，我們會新增一個 `CALayer`，其中包含我們將沿著路徑建立動畫的影像。

下列螢幕擷取畫面顯示已完成的應用程式：

![](graphics-animation-walkthrough-images/00-final-app.png "The completed application")

在我們開始之前，請先下載本指南隨附的*GraphicsDemo*範例。 您可以在[這裡](https://docs.microsoft.com/samples/xamarin/ios-samples/graphicsandanimation)下載它，並在**GraphicsWalkthrough**目錄內，藉由按兩下來啟動名為**GraphicsDemo_starter**的專案，然後開啟 [`DemoView`] 類別。

## <a name="drawing-a-path"></a>繪製路徑

1. 在 `DemoView` 將 `CGPath` 變數新增至類別，並在此函式中將它具現化。 同時宣告兩個 `CGPoint` 變數，`initialPoint` 和 `latestPoint`，我們將用它來捕捉我們用來建立路徑的觸控點：

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

2. 新增下列 using 指示詞：

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. 接下來，覆寫 `TouchesBegan` 並 `TouchesMoved,`，並新增下列執行來分別捕捉初始觸控點和每個後續的觸控點：

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

    系統會在每次接觸移動時呼叫 `SetNeedsDisplay`，以便在下一個執行迴圈傳遞時呼叫 `Draw`。

4. 我們會將行新增至 `Draw` 方法中的路徑，並使用紅色的虛線來繪製。 使用如下所示的程式碼來[執行 `Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md) ：

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

如果現在執行應用程式，我們可以觸控在畫面上繪製，如下列螢幕擷取畫面所示：

![](graphics-animation-walkthrough-images/01-path.png "Drawing on the screen")

## <a name="animating-along-a-path"></a>沿著路徑建立動畫

既然我們已實作為允許使用者繪製路徑的程式碼，讓我們新增程式碼，沿著繪製的路徑建立圖層的動畫。

1. 首先，將[`CALayer`](~/ios/platform/graphics-animation-ios/core-animation.md)變數新增至類別，並在此函式中建立它：

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

2. 接下來，當使用者從螢幕中提起手指時，我們會將圖層新增為視圖圖層的子層。 然後，我們將使用路徑建立主要畫面格動畫，並以動畫顯示圖層的 `Position`。

    若要完成這項操作，我們需要覆寫 `TouchesEnded` 並新增下列程式碼：

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

3. 立即執行應用程式，在繪製之後，會加入具有影像的圖層，並沿著繪製的路徑進行移動：

![](graphics-animation-walkthrough-images/00-final-app.png "A layer with an image is added and travels along the drawn path")

## <a name="summary"></a>總結

在本文中，我們會逐步解說將圖形和動畫概念系結在一起的範例。 首先，我們示範了如何使用核心圖形在 `UIView` 中繪製路徑，以回應使用者觸控。 然後，我們示範了如何使用核心動畫來沿著該路徑進行影像行進。

## <a name="related-links"></a>相關連結

- [Core 動畫](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core 圖形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [核心動畫配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
