---
title: Xamarin 中的多點觸控手指追蹤
description: 本檔說明如何在 Xamarin iOS 應用程式的多點觸控手勢中追蹤個別手指。 它是以手指繪製應用程式範例為中心。
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: deda3a96272db42af17221e613822b858d57abb1
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436340"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Xamarin 中的多點觸控手指追蹤

_本檔示範如何從多個手指追蹤觸控事件_

有時候當多點觸控應用程式在螢幕上同時移動時，需要追蹤個別的手指。 其中一個典型的應用程式是手指繪圖程式。 您希望使用者能夠使用單一手指進行繪製，也可以一次使用多個手指繪製。 當您的程式處理多個觸控事件時，它需要區別這些手指。

當手指先觸及螢幕時，iOS [`UITouch`](xref:UIKit.UITouch) 會為該手指建立物件。 這個物件保持不變，因為手指會在螢幕上移動，然後從畫面中提起，此時會處置物件。 為了追蹤手指，程式應避免直接儲存此 `UITouch` 物件。 相反地，它可以使用 [`Handle`](xref:Foundation.NSObject.Handle) 型別的屬性 `IntPtr` 來唯一識別這些 `UITouch` 物件。

幾乎一律是追蹤個別手指的程式會維持觸控追蹤的字典。 若為 iOS 程式，則字典索引鍵是 `Handle` 識別特定手指的值。 字典值取決於應用程式。 在 [FingerPaint](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) 程式中，每個手指筆觸 (從觸控到 release) 都與一個物件相關聯，該物件包含呈現以該手指繪製之線條所需的所有資訊。 此程式會 `FingerPaintPolyline` 針對此目的定義一個小型類別：

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

每一個聚合線條都有色彩、筆劃寬度和 iOS 圖形 [`CGPath`](xref:CoreGraphics.CGPath) 物件，可在繪製時累積並轉譯線條的多個點。

以下顯示的所有程式碼其餘部分都包含在名為的 `UIView` 導數中 `FingerPaintCanvasView` 。 該類別會在以 `FingerPaintPolyline` 一或多個手指主動繪製的時間內，維護型別物件的字典：

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

此字典可讓視圖 `FingerPaintPolyline` 根據物件的屬性，快速取得與每個手指相關聯的資訊 `Handle` `UITouch` 。

此 `FingerPaintCanvasView` 類別也 `List` 會針對已完成的多線條維護物件：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

中的物件與 `List` 繪製的順序相同。

`FingerPaintCanvasView` 覆寫定義的五個方法 `View` ：

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

各種 `Touches` 覆寫會累積組成折線的點。

[ `Draw` ] 覆寫會繪製已完成的多線條，然後是進行中的多型折線：

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

每個 `Touches` 覆寫可能會報告多個手指的動作， `UITouch` 並以儲存在方法的引數中的一個或多個物件表示 `touches` 。 `TouchesBegan`覆寫會透過這些物件執行迴圈。 針對每個 `UITouch` 物件，此方法會建立並初始化新的 `FingerPaintPolyline` 物件，包括儲存從方法取得之指標的初始位置 `LocationInView` 。 `FingerPaintPolyline` `InProgressPolylines` 使用 `Handle` 物件的屬性 `UITouch` 做為字典索引鍵，即可將這個物件加入至字典：

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

方法會藉由呼叫 `SetNeedsDisplay` 來產生覆寫的呼叫 `Draw` ，並更新螢幕來結束。

當手指或手指在螢幕上移動時，會 `View` 取得多個對其覆寫的呼叫 `TouchesMoved` 。 這會覆寫與 `UITouch` 引數中所儲存之物件相同的迴圈 `touches` ，並將手指的目前位置新增至圖形路徑：

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

`touches`集合只包含 `UITouch` 自上一次呼叫或之後移動的手指物件 `TouchesBegan` `TouchesMoved` 。 如果您需要的 `UITouch` 物件與螢幕上目前正在聯絡的 *所有* 手指相對應，該資訊可透過方法的 `AllTouches` `UIEvent` 引數屬性取得。

覆 `TouchesEnded` 寫有兩個作業。 它必須將最後一個點新增至圖形路徑，並將 `FingerPaintPolyline` 物件從字典傳送 `inProgressPolylines` 至 `completedPolylines` 清單：

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

覆 `TouchesCancelled` 寫的處理方式是直接放棄 `FingerPaintPolyline` 字典中的物件：

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

這種處理常式全部都可讓 [FingerPaint](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) 程式追蹤個別手指，並在畫面上繪製結果：

[![追蹤個別手指並在螢幕上繪製結果](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

您現在已瞭解如何在螢幕上追蹤個別手指，並加以區別。

## <a name="related-links"></a>相關連結

- [對等的 Xamarin Android 指南](~/android/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (範例) ](/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)