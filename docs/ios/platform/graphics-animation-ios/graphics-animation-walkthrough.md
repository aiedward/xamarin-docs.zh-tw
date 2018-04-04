---
title: 逐步解說-使用 CoreGraphics 和 CoreAnimation
description: 本文逐步解說示範如何建立使用核心圖形和核心動畫的應用程式。 它會顯示以回應使用者觸控式螢幕上繪製的方式，以及如何建立動畫沿著路徑移動映像。
ms.prod: xamarin
ms.assetid: 4B96D5CD-1BF5-4520-AAA6-2B857C83815C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f857accfcdec4cb60e781936d1d0836dbf8d6ffb
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="drawing-and-animating-along-a-path"></a>繪圖和沿著路徑製作動畫

此逐步解說中，我們即將繪製觸控輸入回應中使用核心圖形的路徑。 然後，我們會加入`CALayer`包含我們會沿著路徑製作動畫的影像。

下列螢幕擷取畫面顯示完成的應用程式：

![](graphics-animation-walkthrough-images/00-final-app.png "完成的應用程式")

我們開始下載之前*GraphicsDemo*隨附本指南範例。 您可以下載[這裡](https://developer.xamarin.com/samples/monotouch/GraphicsAndAnimation/)和位於**GraphicsWalkthrough**目錄啟動專案名為**GraphicsDemo_starter**上，按兩下和開啟`DemoView`類別。

## <a name="drawing-a-path"></a>繪製路徑


1. 在`DemoView`新增`CGPath`變數至類別和其具現化建構函式。 同時宣告兩個`CGPoint`變數`initialPoint`和`latestPoint`，我們將使用來擷取觸控點，我們可以從中建構路徑：
    
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

2. 加入下列 using 指示詞：

    ```csharp
    using CoreGraphics;
    using CoreAnimation;
    using Foundation;
    ```

3. 接下來，覆寫`TouchesBegan`和`TouchesMoved,`並加入下列實作分別擷取每個後續的觸控點與初始觸控點：

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

    `SetNeedsDisplay` 將會呼叫修飾移動順序每次`Draw`下個階段中執行的迴圈上呼叫。

4. 我們會加入在路徑行`Draw`方法和用法紅色、 虛線的線條，以使用繪製。 [實作`Draw`](~/ios/platform/graphics-animation-ios/core-graphics.md)以程式碼如下所示：

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

當我們執行應用程式現在，我們可以觸控繪製在畫面上，如下列螢幕擷取畫面所示：

![](graphics-animation-walkthrough-images/01-path.png "在螢幕上繪圖")

## <a name="animating-along-a-path"></a>沿著路徑製作動畫

既然我們已經實作的程式碼，來繪製路徑的使用者，讓我們加入的程式碼，以動畫方式顯示圖層繪製的路徑。

1. 首先，新增[ `CALayer` ](~/ios/platform/graphics-animation-ios/core-animation.md)類別變數並建立於建構函式：

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

2. 接下來，我們會將圖層新增為檢視的圖層的副層，當使用者取消其手指從螢幕上。 然後，我們會建立使用路徑主要畫面格動畫建立動畫的圖層`Position`。

    若要完成此我們需要覆寫`TouchesEnded`並加入下列程式碼：

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

3. 現在，並在 繪圖之後，請執行應用程式，包含影像的圖層會加入往返繪製的路徑：

![](graphics-animation-walkthrough-images/00-final-app.png "使用影像圖層會加入往返繪製路徑")

## <a name="summary"></a>總結

在本文中，我們會逐步執行範例的連結在一起的圖形和動畫的概念。 首先，我們也示範了如何使用核心圖形中繪製的路徑`UIView`以回應使用者觸控。 然後我們示範了如何使用核心動畫，請沿著路徑移動的映像。


## <a name="related-links"></a>相關連結

- [Core 動畫](~/ios/platform/graphics-animation-ios/core-animation.md)
- [Core 圖形](~/ios/platform/graphics-animation-ios/core-graphics.md)
- [核心動畫配方](https://developer.xamarin.com/recipes/ios/animation/coreanimation)
