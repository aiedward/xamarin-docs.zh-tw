---
title: 多點觸控手指追蹤
description: 本文件將示範如何從多個指追蹤觸控事件
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 178bd0a68166f033293b2d807fea449355c1c584
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="multi-touch-finger-tracking"></a>多點觸控手指追蹤

_本文件將示範如何從多個指追蹤觸控事件_

有多點觸控應用程式需要追蹤個別隻手指放在螢幕上同時移動的時間。 一個典型的應用程式是 finger-paint 程式。 您想要能夠使用單指，繪製，也同時繪製多個手指與使用者。 當您的程式處理多個觸控事件時，它需要區分這些手指。

當第一次，手指接觸到螢幕時，iOS 會建立[ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/)該指的物件。 手指在螢幕上移動，然後在畫面中，物件已處置的時間點取消，因為此物件會維持相同。 若要追蹤的手指，程式應該避免儲存這`UITouch`直接物件。 相反地，它可以使用[ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)型別的屬性`IntPtr`來唯一識別這些`UITouch`物件。

幾乎，追蹤個別指的程式會維護追蹤觸控的字典。 為 iOS 程式中，字典索引鍵是`Handle`識別特定指紋的值。 字典值取決於應用程式。 在[FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程式 （從發行的觸控式） 每個手指筆劃趨物件，包含呈現該食指繪製的線條所需的所有資訊與相關聯。 程式會定義一個小型`FingerPaintPolyline`針對此用途的類別：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

每個聚合線條的色彩、 筆劃寬度，，iOS 圖形[ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/)累積並轉譯為所繪製的線的多個點的物件。


所有其他的如下所示的程式碼都包含在`UIView`衍生名為`FingerPaintCanvasView`。 類別會維護的物件類型的字典`FingerPaintPolyline`它們會主動繪製由一或多個隻手指放在時間：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

這個字典可讓快速取得檢視`FingerPaintPolyline`食指每個相關聯的資訊根據`Handle`屬性`UITouch`物件。

`FingerPaintCanvasView`類別也會維護`List`已完成的聚合線條的物件：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在這個物件`List`所繪製的順序相同。

`FingerPaintCanvasView` 所定義的五個方法會覆寫`View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

各種`Touches`覆寫累積組成聚合線條的點。

[`Draw`] 已完成的聚合線條，然後再進行中的聚合線條會繪製覆寫：

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

每個`Touches`覆寫可能報告的多個指，由一或多個動作`UITouch`物件儲存在`touches`方法引數。 `TouchesBegan`覆寫這些物件的迴圈。 每個`UITouch`物件，該方法會建立並初始化新`FingerPaintPolyline`物件，包括儲存取自手指的初始位置`LocationInView`方法。 這`FingerPaintPolyline`物件加入至`InProgressPolylines`字典使用`Handle`屬性`UITouch`做為字典索引鍵的物件：

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

此方法結束時，會呼叫`SetNeedsDisplay`來產生呼叫`Draw`覆寫，並更新畫面。

當手指移動螢幕上`View`取得多個呼叫其`TouchesMoved`覆寫。 此覆寫同樣的循環`UITouch`物件儲存在`touches`引數並將目前位置的指紋加入至圖形路徑：

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

`touches`集合包含只有`UITouch`物件之後，最後呼叫移動的指`TouchesBegan`或`TouchesMoved`。 如果您需要`UITouch`物件對應至*所有*目前與其螢幕接觸指，該資訊是透過`AllTouches`屬性`UIEvent`方法引數。

`TouchesEnded`覆寫具有兩個工作。 它必須加入的圖形路徑和傳輸的最後一個點`FingerPaintPolyline`物件從`inProgressPolylines`字典`completedPolylines`清單：

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

`TouchesCancelled`覆寫由只要放棄`FingerPaintPolyline`字典中的物件：

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

這項處理，讓[FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程式以追蹤個別手指和繪製在螢幕上的結果：

[![](touch-tracking-images/image01.png "追蹤個別手指與螢幕上繪製結果")](touch-tracking-images/image01.png#lightbox)

您現在已經瞭解如何追蹤個別隻手指放在畫面上，並加以區別。



## <a name="related-links"></a>相關連結

- [對等的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
