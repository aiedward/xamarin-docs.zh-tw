---
title: Xamarin 中的多點觸控手指追蹤
description: 本主題示範如何從多個手指追蹤觸控事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 6dd3bf848d38f0211dcda100994f6f7ec8831fce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754683"
---
# <a name="multi-touch-finger-tracking"></a>多點觸控手指追蹤

_本主題示範如何從多個手指追蹤觸控事件_

有時候，多點觸控應用程式需要追蹤個別手指，因為它們同時在螢幕上移動。 一個典型的應用程式是手指繪製程式。 您想要讓使用者能夠使用單一手指來進行繪製，同時也可以一次使用多個手指繪製。 當您的程式處理多個觸控事件時，它需要區分哪些事件對應到每個手指。 Android 為此目的提供了識別碼代碼，但取得和處理該程式碼可能有點困難。

對於與特定手指相關聯的所有事件，識別碼代碼會維持不變。 當手指第一次碰觸螢幕時，會指派識別碼代碼，並在手指從螢幕上抬起之後變成無效。
這些識別碼通常是非常小的整數，而 Android 會重複使用它們來進行之後的觸控事件。

幾乎一律是追蹤個別手指的程式會維護一個用於觸控追蹤的字典。 字典索引鍵是識別特定手指的識別碼代碼。 字典值取決於應用程式。 在[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程式中，每個手指筆劃（從觸控到發行）都會與物件相關聯，其中包含呈現以該手指繪製之線條所需的所有資訊。 此程式會針對此`FingerPaintPolyline`用途定義一個小型類別：

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

每個折線都有色彩、筆劃寬度和 Android 圖形[`Path`](xref:Android.Graphics.Path)物件，可在繪製時累積和轉譯線條的多個點。

下面所示的程式碼其餘部分包含在名`View`為`FingerPaintCanvasView`的衍生中。 該類別會在一或多個手指`FingerPaintPolyline`積極繪製時，維護類型物件的字典：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典可讓視圖快速取得與特定`FingerPaintPolyline`手指相關聯的資訊。

類別也會針對已`List`完成的折線維護物件： `FingerPaintCanvasView`

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

中`List`的物件與繪製時的順序相同。

`FingerPaintCanvasView`覆寫所定義的`View`兩個方法：[`OnDraw`](xref:Android.Views.View.OnDraw*)
和[`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*)。
在其`OnDraw`覆寫中，view 會繪製完成的折線，然後繪製進行中的多線條。

`OnTouchEvent`方法的覆寫一`pointerIndex`開始是從`ActionIndex`屬性取得值。 此`ActionIndex`值會區分多個手指，但在多個事件之間並不一致。 基於`pointerIndex` 這個理由`id` ，您可以使用來取得方法的指標值。`GetPointerId` 此識別碼在多個事件之間*會*一致：

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

請注意，覆寫會`ActionMasked`使用`switch`語句中的屬性，而`Action`不是屬性。 原因如下：

當您處理多點`Action`觸控時，屬性的`MotionEventsAction.Down`值會是，而第一手指是用來觸及螢幕`Pointer2Down` ，而和`Pointer3Down`的值則是第二個和第三個手指也可以碰觸螢幕。 當第四和第五個手指接觸時`Action` ，屬性的數值甚至不會對應到`MotionEventsAction`列舉的成員！ 您必須檢查值中的位旗標值，以解讀其意義。

同樣地，當手指與螢幕保持`Action`聯繫時，屬性的`Pointer2Up`值為，而`Pointer3Up`第二個和第三個手指`Up`則為，而第一個手指為。

屬性會採用較少的值，因為它的目的是要`ActionIndex`與屬性搭配使用，以區別多個手指。 `ActionMasked` 當手指碰觸螢幕時，屬性只能與第`MotionEventActions.Down`一手指和`PointerDown`後續手指相等。 當手指離開畫面時， `ActionMasked` `Pointer1Up`針對後續手指和`Up`第一個手指具有的值。

使用`ActionMasked`時`MotionEvent` ，會區分後續的手指來碰觸和離開螢幕，但您通常不需要使用該值，除非是物件中其他方法的引數。 `ActionIndex` 針對多點觸控，上述程式碼中會`GetPointerId`呼叫其中一個最重要的方法。 該方法會傳回一個值，您可以用它來做為字典索引鍵，以便將特定事件與手指產生關聯。

[FingerPaint ](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程式中的`OnTouchEvent`覆寫會藉`MotionEventActions.Down`由`PointerDown`建立新`FingerPaintPolyline`的物件並將其加入字典中, 來處理和事件的方式相同:

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

請注意， `pointerIndex`也會用來取得在視圖中的手指位置。 所有的觸控資訊都與`pointerIndex`值相關聯。 可`id`唯一識別多個訊息上的手指，以便用來建立字典專案。

同樣地， `OnTouchEvent`覆寫也會將`Pointer1Up`已完成的聚合線條傳送至`completedPolylines`集合，以便在覆`OnDraw`寫期間繪製，以`MotionEventActions.Up`處理和相同的情況。 此程式碼也會`id`移除字典中的專案：

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

接下來就是棘手的部分。

在向下和向上事件之間，通常會有`MotionEventActions.Move`許多事件。 這些會在的單一呼叫`OnTouchEvent`中配套，而且它們必須以不同于和`Up`事件`Down`的方式來處理。 `pointerIndex` 先前`ActionIndex`從屬性取得的值必須予以忽略。 相反地，方法必須藉由`pointerIndex`在 0 `PointerCount`與屬性之間迴圈來取得多個值`id` ，然後取得每`pointerIndex`一個值的：

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

這種處理類型可讓[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程式追蹤個別手指，並在螢幕上繪製結果：

[![FingerPaint 範例中的範例螢幕擷取畫面](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

您現在已瞭解如何在畫面上追蹤個別手指，並加以區別。

## <a name="related-links"></a>相關連結

- [對等的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
