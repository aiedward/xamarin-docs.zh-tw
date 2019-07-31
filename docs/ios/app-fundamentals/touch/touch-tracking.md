---
title: 在 Xamarin 中的多點觸控手指追蹤
description: 本檔說明如何在 Xamarin iOS 應用程式中, 以多點觸控手勢追蹤個人手指。 它會以手指繪製應用程式範例為中心。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: cdf6e78356ee1c846b5921957e8eda53931a3c6b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655161"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>在 Xamarin 中的多點觸控手指追蹤

_本檔示範如何從多個手指追蹤觸控事件_

有時候, 多點觸控應用程式需要追蹤個別手指, 因為它們同時在螢幕上移動。 一個典型的應用程式是手指繪製程式。 您想要讓使用者能夠使用單一手指來進行繪製, 同時也可以一次使用多個手指繪製。 當您的程式處理多個觸控事件時, 它需要區分這兩個手指。

當手指第一次接觸畫面時, iOS 會[`UITouch`](xref:UIKit.UITouch)為該手指建立物件。 這個物件會維持不變, 因為手指會在畫面上移動, 然後從畫面中抬起, 而在該位置會處置物件。 若要追蹤手指, 程式應該避免直接儲存此`UITouch`物件。 相反地, 它可以使用[`Handle`](xref:Foundation.NSObject.Handle)類型`IntPtr`的屬性來唯一識別這些`UITouch`物件。

幾乎一律是追蹤個別手指的程式會維護一個用於觸控追蹤的字典。 若是 iOS 程式, 字典索引鍵是識別特定`Handle`手指的值。 字典值取決於應用程式。 在[FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)程式中, 每個手指筆劃 (從觸控到發行) 都會與物件相關聯, 其中包含呈現以該手指繪製之線條所需的所有資訊。 此程式會針對此`FingerPaintPolyline`用途定義一個小型類別:

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

每個折線都有色彩、筆劃寬度和 iOS 圖形[`CGPath`](xref:CoreGraphics.CGPath)物件, 可在繪製時累積和轉譯線條的多個點。


下面顯示的所有程式碼都包含在名為`UIView` `FingerPaintCanvasView`的衍生中。 該類別會在一或多個手指`FingerPaintPolyline`積極繪製時, 維護類型物件的字典:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

此字典可讓視圖根據`FingerPaintPolyline` `UITouch`物件的`Handle`屬性, 快速取得與每個手指相關聯的資訊。

類別也會針對已`List`完成的折線維護物件: `FingerPaintCanvasView`

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

中`List`的物件與繪製時的順序相同。

`FingerPaintCanvasView`覆寫所定義的`View`五個方法:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

各種`Touches`覆寫會累積組成折線的點。

[`Draw`] 覆寫會繪製已完成的多線條, 然後再繪製進行中的折線:

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

每個`UITouch` `touches`覆寫可能會報告多個手指的動作, 這是由方法的引數中儲存的一或多個物件所表示。 `Touches` `TouchesBegan`覆寫會迴圈執行這些物件。 針對每`UITouch`個物件, 方法會建立並初始化新`FingerPaintPolyline`的物件, 包括儲存從`LocationInView`方法取得之指標的初始位置。 這個`FingerPaintPolyline`物件會`InProgressPolylines` `Handle`使用物件的屬性做為字典索引鍵,加入字典中:`UITouch`

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

方法最後會呼叫`SetNeedsDisplay`來產生覆寫的`Draw`呼叫, 並更新螢幕。

當手指或手指在螢幕上移動時, 會`View`取得對其`TouchesMoved`覆寫的多次呼叫。 這會覆寫在`UITouch` `touches`引數中儲存之物件的類似迴圈, 並將目前的手指位置加入圖形路徑:

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

集合只包含`TouchesMoved`自上一次呼叫或之後移動的手指物件。`TouchesBegan` `UITouch` `touches` `UITouch`如果您需要的物件對應到目前與該螢幕接觸的*所有*手指, 可以透過`AllTouches`方法`UIEvent`引數的屬性取得該資訊。

覆`TouchesEnded`寫有兩個作業。 它必須將最後一個點加入圖形路徑, 並將`FingerPaintPolyline`物件`inProgressPolylines`從字典傳送到`completedPolylines`清單:

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

只要放棄字典中的`FingerPaintPolyline`物件, 就可以處理覆寫:`TouchesCancelled`

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

此處理可讓[FingerPaint](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)程式追蹤個別手指, 並在螢幕上繪製結果:

[![](touch-tracking-images/image01.png "追蹤個別手指並在螢幕上繪製結果")](touch-tracking-images/image01.png#lightbox)

您現在已瞭解如何在畫面上追蹤個別手指, 並加以區別。



## <a name="related-links"></a>相關連結

- [對等的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
