---
title: Xamarin 中的多點觸控手指追蹤
description: 本主題示範如何從多個手指追蹤觸控事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f960c3cec90bd331f5a1433a869c7720b40c9680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024274"
---
# <a name="multi-touch-finger-tracking"></a>多點觸控手指追蹤

_本主題示範如何從多個手指追蹤觸控事件_

有時候，多點觸控應用程式需要追蹤個別手指，因為它們同時在螢幕上移動。 一個典型的應用程式是手指繪製程式。 您想要讓使用者能夠使用單一手指來進行繪製，同時也可以一次使用多個手指繪製。 當您的程式處理多個觸控事件時，它需要區分哪些事件對應到每個手指。 Android 為此目的提供了識別碼代碼，但取得和處理該程式碼可能有點困難。

對於與特定手指相關聯的所有事件，識別碼代碼會維持不變。 當手指第一次碰觸螢幕時，會指派識別碼代碼，並在手指從螢幕上抬起之後變成無效。
這些識別碼通常是非常小的整數，而 Android 會重複使用它們來進行之後的觸控事件。

幾乎一律是追蹤個別手指的程式會維護一個用於觸控追蹤的字典。 字典索引鍵是識別特定手指的識別碼代碼。 字典值取決於應用程式。 在[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程式中，每個手指筆劃（從觸控到發行）都會與物件相關聯，其中包含呈現以該手指繪製之線條所需的所有資訊。 此程式會針對此用途定義一個小型的 `FingerPaintPolyline` 類別：

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

每個折線都有色彩、筆劃寬度和 Android 圖形[`Path`](xref:Android.Graphics.Path)物件，以便在繪製時累積和轉譯線條的多個點。

下面所示的程式碼其餘部分都包含在名為 `FingerPaintCanvasView`的 `View` 衍生。 該類別會在一或多個手指主動繪製時，維護 `FingerPaintPolyline` 類型物件的字典：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典可讓視圖快速取得與特定手指相關聯的 `FingerPaintPolyline` 資訊。

`FingerPaintCanvasView` 類別也會針對已完成的折線維護 `List` 物件：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

此 `List` 中的物件與繪製的順序相同。

`FingerPaintCanvasView` 會覆寫 `View`所定義的兩個方法： [`OnDraw`](xref:Android.Views.View.OnDraw*)
和[`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*)。
在它的 `OnDraw` 覆寫中，此視圖會繪製完成的折線，然後繪製進行中的多線條。

`OnTouchEvent` 方法的覆寫一開始先從 `ActionIndex` 屬性取得 `pointerIndex` 值。 這個 `ActionIndex` 值可區分多個手指，但在多個事件之間並不一致。 基於這個理由，您可以使用 `pointerIndex` 從 `GetPointerId` 方法取得指標 `id` 值。 此識別碼在多個事件之間*會*一致：

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

請注意，覆寫會使用 `switch` 語句中的 `ActionMasked` 屬性，而不是 `Action` 屬性。 原因如下：

當您處理多點觸控時，`Action` 屬性的值為第一手指接觸螢幕的 `MotionEventsAction.Down`，而 `Pointer2Down` 和 `Pointer3Down` 的值為第二和第三個手指也會觸及螢幕。 當第四和第五個手指接觸時，`Action` 屬性會有數值，甚至不會對應到 `MotionEventsAction` 列舉的成員！ 您必須檢查值中的位旗標值，以解讀其意義。

同樣地，當手指離開畫面時，`Action` 屬性的值為 `Pointer2Up`，而 `Pointer3Up` 適用于第二和第三個手指，而第一個手指的 `Up`。

`ActionMasked` 屬性會採用較少的值，因為它是要與 `ActionIndex` 屬性搭配使用，以區別多個手指。 當手指碰觸螢幕時，屬性只能等於第一個手指 `MotionEventActions.Down`，而 `PointerDown` 用於後續手指。 當手指離開畫面時，`ActionMasked` 的下一部手指會有 `Pointer1Up` 的值，而 `Up` 則為第一台。

使用 `ActionMasked`時，`ActionIndex` 會區別後續的手指來碰觸並離開螢幕，但您通常不需要使用該值，除了作為 `MotionEvent` 物件中其他方法的引數。 針對多點觸控，上述程式碼中 `GetPointerId` 呼叫其中一個最重要的方法。 該方法會傳回一個值，您可以用它來做為字典索引鍵，以便將特定事件與手指產生關聯。

[FingerPaint](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程式中的 `OnTouchEvent` 覆寫會藉由建立新的 `FingerPaintPolyline` 物件，並將其新增至字典，來處理 `MotionEventActions.Down` 和 `PointerDown` 事件的方式相同：

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

請注意，`pointerIndex` 也會用來取得在視圖中的手指位置。 所有的觸控資訊都與 `pointerIndex` 值相關聯。 `id` 可唯一識別多個訊息的手指，以便用來建立字典專案。

同樣地，`OnTouchEvent` 覆寫也會藉由將已完成的聚合線條傳送至 `completedPolylines` 集合來處理 `MotionEventActions.Up` 和 `Pointer1Up`，以便在 `OnDraw` 覆寫期間繪製它們。 此程式碼也會移除字典中的 `id` 專案：

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

在向下和向上事件之間，通常會有許多 `MotionEventActions.Move` 事件。 這些會在 `OnTouchEvent`的單一呼叫中配套，而且它們必須以不同于 `Down` 和 `Up` 事件的方式來處理。 先前從 `ActionIndex` 屬性取得的 `pointerIndex` 值必須予以忽略。 相反地，方法必須藉由在0和 `PointerCount` 屬性之間迴圈來取得多個 `pointerIndex` 值，然後取得每一個 `pointerIndex` 值的 `id`：

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

[FingerPaint 範例中的![範例螢幕擷取畫面](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

您現在已瞭解如何在畫面上追蹤個別手指，並加以區別。

## <a name="related-links"></a>相關連結

- [對等的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
