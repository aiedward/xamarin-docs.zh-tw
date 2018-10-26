---
title: 多點觸控手指在 Xamarin.iOS 中追蹤
description: 本文件說明如何追蹤個人的手指在 Xamarin.iOS 應用程式中的多點觸控筆勢。 它著重於手指繪製的應用程式範例。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a1ddcda84d51b5a8a9220558ddaf9476a2321ee8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105047"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>多點觸控手指在 Xamarin.iOS 中追蹤

_本文件將示範如何追蹤多個根手指觸控事件_

有多點觸控應用程式需要追蹤個人的手指，同時在螢幕上移動時的時間。 一個典型的應用程式是 finger-paint 程式。 您的使用者可繪製具有一根手指，但一次使用多個根手指繪製。 當您的程式會處理多個觸控事件，它需要區別這些手指。

當第一次，手指接觸到螢幕時，會建立 iOS [ `UITouch` ](https://developer.xamarin.com/api/type/UIKit.UITouch/)該手指的物件。 手指在螢幕上移動，並在畫面中，時間點處置物件之後再將這個物件會保持相同。 若要追蹤的手指，程式應該避免儲存這`UITouch`直接物件。 相反地，它可以使用[ `Handle` ](https://developer.xamarin.com/api/property/Foundation.NSObject.Handle/)屬性的型別`IntPtr`來唯一識別這些`UITouch`物件。

幾乎就一律會追蹤個人的手指的程式會維護追蹤觸控式的字典。 為 iOS 程式中，字典索引鍵則`Handle`識別特定的手指的值。 字典值取決於應用程式。 在  [FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程式 （從釋放觸控） 每個手指筆觸是物件，包含呈現該手指繪製的線條所需的所有資訊與相關聯。 程式會定義一個小型`FingerPaintPolyline`針對此用途的類別：

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

每一個聚合線條的色彩、 筆觸寬度，，iOS 圖形[ `CGPath` ](https://developer.xamarin.com/api/type/CoreGraphics.CGPath/)累積和轉譯線條的多個點，因為它所繪製的物件。


如下所示的程式碼的所有其餘部分包含在`UIView`衍生名為`FingerPaintCanvasView`。 類別會負責維護的物件類型的字典`FingerPaintPolyline`主動由一個或多根手指正在繪製之時間內：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

這個字典可讓檢視，以快速取得`FingerPaintPolyline`每根手指與相關聯的資訊為基礎`Handle`屬性`UITouch`物件。

`FingerPaintCanvasView`類別也會維護`List`物件已完成多線條：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在此物件`List`所畫的相同順序。

`FingerPaintCanvasView` 所定義的五個方法會覆寫`View`:

- [`TouchesBegan`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesBegan/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesMoved`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesMoved/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesEnded`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesEnded/p/Foundation.NSSet/UIKit.UIEvent/)
- [`TouchesCancelled`](https://developer.xamarin.com/api/member/UIKit.UIResponder.TouchesCancelled/p/Foundation.NSSet/UIKit.UIEvent/)
- [`Draw`](https://developer.xamarin.com/api/member/UIKit.UIView.Draw/p/CoreGraphics.CGRect/)

各種`Touches`覆寫累積組成聚合線條的點。

[`Draw`] 覆寫繪製已完成的多線條，然後進行中的多線條：

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

每個`Touches`覆寫可能報告的多個根手指，由一或多個動作`UITouch`中儲存的物件`touches`方法引數。 `TouchesBegan`覆寫這些物件的迴圈。 每個`UITouch`物件，此方法會建立並初始化新`FingerPaintPolyline`物件，包括儲存取自手指的初始位置`LocationInView`方法。 這`FingerPaintPolyline`物件加入至`InProgressPolylines`字典使用`Handle`屬性`UITouch`做為字典索引鍵的物件：

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

方法最後會呼叫`SetNeedsDisplay`來產生呼叫`Draw`覆寫，並更新畫面。

當手指移動在畫面上，`View`取得多個呼叫其`TouchesMoved`覆寫。 此覆寫同樣的循環`UITouch`中儲存的物件`touches`引數，並將手指的目前位置加入至圖形路徑：

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

`touches`集合包含只有`UITouch`物件的最後一個呼叫之後移動手指`TouchesBegan`或`TouchesMoved`。 如果您需要`UITouch`對應至物件*所有*目前接觸到螢幕的指，該資訊是透過`AllTouches`屬性`UIEvent`方法引數。

`TouchesEnded`覆寫有兩項作業。 它必須新增的最後一個點的圖形路徑和傳輸`FingerPaintPolyline`物件從`inProgressPolylines`字典`completedPolylines`清單：

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

`TouchesCancelled`覆寫由直接放棄`FingerPaintPolyline`字典中的物件：

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

總之，這項處理可讓[FingerPaint](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)程式以追蹤個人的手指，並在螢幕上繪製結果：

[![](touch-tracking-images/image01.png "追蹤個人的手指與螢幕上繪製結果")](touch-tracking-images/image01.png#lightbox)

您現在已了解如何追蹤個人的手指在螢幕上，並區別它們。



## <a name="related-links"></a>相關連結

- [對等的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monotouch/ApplicationFundamentals/FingerPaint)
