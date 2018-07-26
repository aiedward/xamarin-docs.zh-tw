---
title: 在 Xamarin.iOS 中使用核心圖形和 Core 動畫
description: 本文逐步解說示範如何建立使用核心圖形和核心動畫的應用程式。 它會顯示以回應使用者的觸控式螢幕上繪製的方式，以及如何以動畫顯示沿著路徑移動映像。
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: cecfd7f3a9678f298af3ed547aa7b50a18238729
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242000"
---
# <a name="using-core-graphics-and-core-animation-in-xamarinios"></a>在 Xamarin.iOS 中使用核心圖形和 Core 動畫

此逐步解說中我們將繪製採用回應中的核心圖形，以觸控輸入的路徑。 然後，我們會新增`CALayer`包含映像，我們會以動畫顯示的路徑。

下列螢幕擷取畫面顯示完成的應用程式：

![](graphics-animation-walkthrough-images/00-final-app.png "完成的應用程式")

我們開始下載之前請先*GraphicsDemo*本指南隨附的範例。 您可以下載[以下](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)和位於內**GraphicsWalkthrough**目錄啟動專案，命名為**GraphicsDemo_starter**上，按兩下和開啟`DemoView`類別。

## <a name="drawing-a-path"></a>繪製路徑


1. 在`DemoView`新增`CGPath`變數設為類別和其具現化建構函式。 也會宣告兩個`CGPoint`變數`initialPoint`和`latestPoint`，我們將使用來擷取觸控點，我們可以從中建構路徑：
    
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

3. 接下來，覆寫`TouchesBegan`和`TouchesMoved,`並新增下列實作分別擷取初始的觸控點和每個後續的觸控點：

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

    `SetNeedsDisplay` 將會呼叫修飾移動以便每次`Draw`呼叫在下一步 執行的迴圈行程上。

4. 我們會將行新增至路徑中`Draw`方法，並使用紅色、 虛線的線條，以使用繪圖。 [實作`Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md)以程式碼如下所示：

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

如果我們執行應用程式現在，我們可以觸控以繪製畫面上，如下列螢幕擷取畫面所示：

![](graphics-animation-walkthrough-images/01-path.png "在螢幕上繪圖")

## <a name="animating-along-a-path"></a>沿著路徑以動畫顯示

既然我們已實作的程式碼，以允許使用者繪製的路徑，讓我們新增的程式碼，以動畫顯示圖層繪製的路徑。

1. 首先，新增[ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md)變數至類別，並建立建構函式：

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

2. 接下來，我們將新增圖層為檢視的圖層的副層，當使用者將他們的手指從螢幕向上移。 接著，我們將建立使用的路徑中，主要畫面格動畫以動畫顯示圖層的`Position`。

    若要這麼做我們要覆寫`TouchesEnded`並加入下列程式碼：

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

3. 現在，並在繪圖之後，請執行應用程式，與映像圖層加入和傳輸沿著繪製的路徑：

![](graphics-animation-walkthrough-images/00-final-app.png "與映像圖層加入，並且傳送繪製路徑")

## <a name="summary"></a>總結

在本文中，我們會逐步執行的圖形和動畫概念繫結在一起的範例。 首先，我們示範了如何使用以繪製路徑的核心圖形`UIView`以回應使用者的觸控。 然後，我們會示範如何使用核心動畫若要沿該路徑移動映像。


## <a name="related-links"></a>相關連結

- [Core 動畫](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core 圖形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [Core 動畫配方](https://github.com/xamarin/recipes/tree/master/Recipes/ios/animation/coreanimation)
