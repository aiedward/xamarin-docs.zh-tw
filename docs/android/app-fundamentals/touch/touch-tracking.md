---
title: 多點觸控手指在 Xamarin.Android 中追蹤
description: 本主題示範如何追蹤多個根手指觸控事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 34a9d2d9b8acb05a1b978a70e85038507032faaa
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61011777"
---
# <a name="multi-touch-finger-tracking"></a>多點觸控的手指追蹤

_本主題示範如何追蹤多個根手指觸控事件_

有多點觸控應用程式需要追蹤個人的手指，同時在螢幕上移動時的時間。 一個典型的應用程式是 finger-paint 程式。 您的使用者可繪製具有一根手指，但一次使用多個根手指繪製。 當您的程式會處理多個觸控事件，它需要區別哪些事件對應至每根手指。 Android 在基於此目的，提供使用者識別碼程式碼，但是取得並處理該程式碼可能有點棘手。

所有事件特定的手指與相關聯的識別代碼維持都不變。 手指初次接觸到螢幕，並將手指拿起從螢幕之後會變成無效時，會指派識別碼的程式碼。
這些識別碼通常非常小的整數，而 Android 重複使用它們的較新的觸控事件。

幾乎就一律會追蹤個人的手指的程式會維護追蹤觸控式的字典。 字典索引鍵是可識別特定的手指識別碼程式碼。 字典值取決於應用程式。 在  [FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程式 （從釋放觸控） 每個手指筆觸是物件，包含呈現該手指繪製的線條所需的所有資訊與相關聯。 程式會定義一個小型`FingerPaintPolyline`針對此用途的類別：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

每一個聚合線條的色彩、 筆劃寬度，，Android 的圖形[ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/)累積和轉譯線條的多個點，因為它所繪製的物件。

如下所示的程式碼的其餘部分包含在`View`衍生名為`FingerPaintCanvasView`。 類別會負責維護的物件類型的字典`FingerPaintPolyline`主動由一個或多根手指正在繪製之時間內：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

這個字典可讓檢視，以快速取得`FingerPaintPolyline`特定手指與相關聯的資訊。

`FingerPaintCanvasView`類別也會維護`List`物件已完成多線條：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在此物件`List`所畫的相同順序。

`FingerPaintCanvasView` 所定義的兩個方法會覆寫`View`: [`OnDraw`](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)
並[ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/)。
在其`OnDraw`覆寫時，檢視繪製已完成的多線條，然後繪製以進行中的多線條。

覆寫`OnTouchEvent`方法一開始會取得`pointerIndex`值從`ActionIndex`屬性。 這`ActionIndex`值區分多個根手指，但不一致跨多個事件。 基於這個理由，您使用`pointerIndex`來取得指標`id`值從`GetPointerId`方法。 此識別碼*是*一致跨多個事件：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

請注意，使用覆寫`ActionMasked`中的屬性`switch`陳述式而非`Action`屬性。 原因如下：

當您正在處理多點觸控`Action`屬性的值為`MotionEventsAction.Down`的第一個手指觸控螢幕，然後值`Pointer2Down`和`Pointer3Down`如第二個和第三個手指也觸控式螢幕。 當第四個和第五個手指連絡人`Action`屬性具有甚至沒有對應成員的數值`MotionEventsAction`列舉 ！ 您必須檢查的值以解譯這些代表什麼意思的位元旗標的值。

同樣地，因為手指與螢幕，留下連絡人`Action`屬性具有值`Pointer2Up`並`Pointer3Up`的第二個和第三個手指，和`Up`的第一個手指。

`ActionMasked`屬性會採用較少的數目值具有用來搭配使用，因此`ActionIndex`屬性來區別多個根手指。 當手指觸控式螢幕時，屬性可以只會等於`MotionEventActions.Down`的第一個手指和`PointerDown`針對後續的手指。 手指離開畫面，如`ActionMasked`具有值`Pointer1Up`的後續指的並`Up`的第一個手指。

使用時`ActionMasked`，則`ActionIndex`觸控和離開畫面中的，但您通常不需要使用該以外的值做為引數中的其他方法的後續指以區分`MotionEvent`物件。 多點觸控，其中一個最重要的一種方法是`GetPointerId`上述程式碼中呼叫。 方法會傳回值，您可以使用字典索引鍵建立關聯以手指的特定事件。

`OnTouchEvent`中覆寫[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程式處理程序`MotionEventActions.Down`並`PointerDown`完全是藉由建立新的事件`FingerPaintPolyline`物件並將它加入至字典：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

請注意，`pointerIndex`也會用來取得在檢視內手指的位置。 相關聯的所有觸控資訊`pointerIndex`值。 `id`唯一識別指多個訊息，以便的來建立字典項目。

同樣地，`OnTouchEvent`覆寫也會處理`MotionEventActions.Up`並`Pointer1Up`相同藉由傳送至已完成的聚合線條`completedPolylines`集合，因此它們可以繪製期間`OnDraw`覆寫。 程式碼也會移除`id`從字典的項目：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

現在的麻煩的部分。

之間的向下和向上事件，通常有許多`MotionEventActions.Move`事件。 這些都結合在一起的單一呼叫中`OnTouchEvent`，而且它們從必須以不同方式處理`Down`和`Up`事件。 `pointerIndex`從稍早取得的值`ActionIndex`必須忽略屬性。 相反地，此方法必須取得多個`pointerIndex`值 0 之間循環並`PointerCount`屬性，然後取得`id`對每個`pointerIndex`值：

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

處理這類可[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程式以追蹤個人的手指，並在螢幕上繪製結果：

[![FingerPaint 範例中的範例螢幕擷取畫面](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

您現在已了解如何追蹤個人的手指在螢幕上，並區別它們。


## <a name="related-links"></a>相關連結

- [對等的 Xamarin iOS 快速入門](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
