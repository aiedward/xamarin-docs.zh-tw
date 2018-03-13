---
title: "多點觸控手指追蹤"
description: "本主題示範如何從多個指追蹤觸控事件"
ms.topic: article
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: e90f49ba9aa217659e20d4bef1fc826fe085fa7f
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="multi-touch-finger-tracking"></a>多點觸控手指追蹤

_本主題示範如何從多個指追蹤觸控事件_

有多點觸控應用程式需要追蹤個別隻手指放在螢幕上同時移動的時間。 一個典型的應用程式是 finger-paint 程式。 您想要能夠使用單指，繪製，也同時繪製多個手指與使用者。 當您的程式處理多個觸控事件時，它需要區別哪些事件對應至每個手指。 Android 提供識別代碼基於此目的，但取得並處理該程式碼可能很有點困難。

所有相關聯的事件特定食指，識別代碼維持不變。 手指初次接觸到螢幕，並從螢幕上手指，拿起之後會變成無效時，會指派識別碼程式碼。
這些識別碼都是通常非常小的整數，和 Android 重複使用它們的更新版本的觸控事件。

幾乎，追蹤個別指的程式會維護追蹤觸控的字典。 字典索引鍵是識別特定指紋的識別代碼。 字典值取決於應用程式。 在[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程式 （從發行的觸控式） 每個手指筆劃趨物件，包含呈現該食指繪製的線條所需的所有資訊與相關聯。 程式會定義一個小型`FingerPaintPolyline`針對此用途的類別：

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

每個聚合線條的色彩、 筆劃寬度，，Android 圖形[ `Path` ](https://developer.xamarin.com/api/type/Android.Graphics.Path/)累積並轉譯為所繪製的線的多個點的物件。

如下所示的程式碼的其餘部分包含在`View`衍生名為`FingerPaintCanvasView`。 類別會維護的物件類型的字典`FingerPaintPolyline`它們會主動繪製由一或多個隻手指放在時間：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

這個字典可讓快速取得檢視`FingerPaintPolyline`特定手指與相關聯的資訊。

`FingerPaintCanvasView`類別也會維護`List`已完成的聚合線條的物件：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

在這個物件`List`所繪製的順序相同。

`FingerPaintCanvasView` 所定義的兩個方法會覆寫`View`: [ `OnDraw` ](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/p/Android.Graphics.Canvas/)和[ `OnTouchEvent` ](https://developer.xamarin.com/api/member/Android.Views.View.OnTouchEvent/p/Android.Views.MotionEvent/)。
在其`OnDraw`覆寫時，檢視已完成的聚合線條會繪製，然後繪製以進行中的聚合線條。

覆寫`OnTouchEvent`方法一開始會取得`pointerIndex`值`ActionIndex`屬性。 這`ActionIndex`值區別多個指，但不一致跨多個事件。 基於這個原因，您使用`pointerIndex`取得指標`id`值`GetPointerId`方法。 此識別碼*是*一致跨多個事件：

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

請注意，覆寫使用`ActionMasked`屬性`switch`陳述式而不是`Action`屬性。 原因如下：

當您處理多點觸控，`Action`屬性的值為`MotionEventsAction.Down`的第一個指指觸控螢幕，然後值`Pointer2Down`和`Pointer3Down`時的第二個和第三個指也觸控螢幕。 當第四個和第五個指連絡人，`Action`屬性有數字的值，即使沒有對應的成員`MotionEventsAction`列舉 ！ 您必須檢查的值來解譯及其意義的位元旗標的值。

同樣地，因為手指與螢幕，使連絡人`Action`屬性有值的`Pointer2Up`和`Pointer3Up`的第二個和第三個手指，和`Up`的第一個手指。

`ActionMasked`屬性會接受較少的數字的值因為它已用來搭配`ActionIndex`屬性來區別多個手指。 當手指觸控的螢幕時，屬性可以只等於`MotionEventActions.Down`的第一個手指和`PointerDown`後續指的。 因為手指使畫面上，`ActionMasked`具有值`Pointer1Up`的後續指和`Up`的第一個手指。

當使用`ActionMasked`、`ActionIndex`的後續指觸碰並離開畫面上，但您通常不需要使用該以外的值做為引數中的其他方法來以區分`MotionEvent`物件。 多點觸控，其中一個最重要的一種方法是`GetPointerId`上述程式碼中呼叫。 方法會傳回值，您可以使用字典索引鍵相關聯的特定事件，以手指。

`OnTouchEvent`中覆寫[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程式處理序`MotionEventActions.Down`和`PointerDown`相同方法，建立新的事件`FingerPaintPolyline`物件並將它加入到字典：

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

請注意，`pointerIndex`也用來取得手指檢視內的位置。 觸控的所有資訊與相關都聯`pointerIndex`值。 `id`隻手指放在上唯一識別多則訊息，以便的來建立字典項目。

同樣地，`OnTouchEvent`也覆寫控制代碼`MotionEventActions.Up`和`Pointer1Up`傳輸至已完成的聚合線條，以相同`completedPolylines`集合，讓它們可以繪製期間`OnDraw`覆寫。 程式碼也會移除`id`從字典的項目：

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

向下之間和事件，通常有許多`MotionEventActions.Move`事件。 這些會集結在單一呼叫`OnTouchEvent`，而且它們從必須以不同方式處理`Down`和`Up`事件。 `pointerIndex`先前從取得的值`ActionIndex`必須忽略屬性。 相反地，此方法必須取得多個`pointerIndex`值介於 0 迴圈和`PointerCount`屬性，然後取得`id`它們`pointerIndex`值：

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

此類型的處理允許[FingerPaint](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)程式以追蹤個別手指和繪製在螢幕上的結果：

[![從 FingerPaint 範例的範例螢幕擷取畫面](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

您現在已經瞭解如何追蹤個別隻手指放在畫面上，並加以區別。


## <a name="related-links"></a>相關連結

- [對等的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （範例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint)
