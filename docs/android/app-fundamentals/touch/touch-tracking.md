---
title: Xamarin 中的多點觸控手指追蹤
description: 本主題示範如何從多個手指追蹤觸控事件
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8c2e06d2700cd7b61c16fc993d807ca4d042a063
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455114"
---
# <a name="multi-touch-finger-tracking"></a>多點觸控手指追蹤

_本主題示範如何從多個手指追蹤觸控事件_

有時候當多點觸控應用程式在螢幕上同時移動時，需要追蹤個別的手指。 其中一個典型的應用程式是手指繪圖程式。 您希望使用者能夠使用單一手指進行繪製，也可以一次使用多個手指繪製。 當您的程式處理多個觸控事件時，它需要區分哪些事件對應至每個手指。 Android 針對此用途提供了識別碼代碼，但是取得和處理該程式碼可能有點複雜。

針對與特定手指相關聯的所有事件，識別碼程式碼會維持不變。 當手指第一次接觸螢幕時，即會指派識別碼代碼，並在手指從螢幕中提起之後變成無效。
這些識別碼代碼通常是非常小的整數，而 Android 會重複使用它們來進行後續觸控事件。

幾乎一律是追蹤個別手指的程式會維持觸控追蹤的字典。 字典索引鍵是識別特定手指的識別碼代碼。 字典值取決於應用程式。 在 [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) 程式中，每個手指筆觸 (從觸控到 release) 都與一個物件相關聯，該物件包含呈現以該手指繪製之線條所需的所有資訊。 此程式會 `FingerPaintPolyline` 針對此目的定義一個小型類別：

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

每一個聚合線條都有色彩、筆劃寬度和 Android 圖形 [`Path`](xref:Android.Graphics.Path) 物件，可在繪製時累積並轉譯線條的多個點。

下面顯示的程式碼的其餘部分包含在 `View` 名為的導數中 `FingerPaintCanvasView` 。 該類別會在以 `FingerPaintPolyline` 一或多個手指主動繪製的時間內，維護型別物件的字典：

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

此字典可讓視圖快速取得 `FingerPaintPolyline` 與特定手指相關聯的資訊。

此 `FingerPaintCanvasView` 類別也 `List` 會針對已完成的多線條維護物件：

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

中的物件與 `List` 繪製的順序相同。

`FingerPaintCanvasView` 覆寫下列兩個定義的方法 `View` ： [`OnDraw`](xref:Android.Views.View.OnDraw*)
和 [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*)。
在它的覆 `OnDraw` 寫中，視圖會繪製已完成的多線條，然後繪製進行中的多型折線。

方法的覆寫會 `OnTouchEvent` `pointerIndex` 從屬性取得值開始 `ActionIndex` 。 此 `ActionIndex` 值會區別多個手指，但不會在多個事件中保持一致。 基於這個原因，您可以使用 `pointerIndex` 從方法取得指標 `id` 值 `GetPointerId` 。 此識別碼 *會* 在多個事件之間保持一致：

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

請注意，覆寫會 `ActionMasked` 在語句中使用屬性， `switch` 而不是使用 `Action` 屬性。 原因如下：

當您處理多點觸控時，屬性的 `Action` 值 `MotionEventsAction.Down` 為第一個手指觸碰螢幕，而和的值，以及 `Pointer2Down` `Pointer3Down` 做為第二個和第三個手指也接觸螢幕。 當第四個和第五個手指接觸時， `Action` 屬性的數值甚至不會對應到列舉的成員 `MotionEventsAction` ！ 您必須檢查值中位旗標的值，以解讀它們所代表的意義。

同樣地，當手指離開螢幕時，屬性的值為、第 `Action` `Pointer2Up` 二個 `Pointer3Up` 和第三個手指以及 `Up` 第一個手指的值。

`ActionMasked`屬性會採用較少的值，因為它的目的是要與屬性搭配使用 `ActionIndex` ，以區別多個手指。 當手指觸控式螢幕幕時，屬性只能與 `MotionEventActions.Down` 第一個手指和 `PointerDown` 後續手指相等。 當手指離開畫面時，的值會是的 `ActionMasked` 值， `Pointer1Up` 適用于後續的手指和 `Up` 第一個手指。

使用時 `ActionMasked` ， `ActionIndex` 會在後續的手指之間區別並離開畫面，但您通常不需要使用該值，除了做為物件中其他方法的引數之外 `MotionEvent` 。 針對多點觸控，上述程式碼中會呼叫其中一個最重要的方法 `GetPointerId` 。 該方法會傳回一個值，供您用來將特定事件與手指產生關聯的字典索引鍵。

`OnTouchEvent` [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)程式中的覆寫會藉 `MotionEventActions.Down` `PointerDown` 由建立新的 `FingerPaintPolyline` 物件並將其加入至字典中，以相同的方式處理和事件：

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

請注意， `pointerIndex` 也會用來取得該手指在視圖內的位置。 所有觸控資訊都會與值相關聯 `pointerIndex` 。 `id`會在多個訊息之間唯一識別手指，以便用來建立字典專案。

同樣地，覆 `OnTouchEvent` 寫也 `MotionEventActions.Up` 會藉 `Pointer1Up` 由將已完成的聚合線條傳送至集合，以便在覆 `completedPolylines` 寫期間繪製它們，來處理和相同 `OnDraw` 。 程式碼也會移除 `id` 字典中的專案：

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

現在是很棘手的部分。

在向下和向上事件之間，通常會有許多 `MotionEventActions.Move` 事件。 這些都是在的單一呼叫中組合 `OnTouchEvent` ，而且必須與和事件的處理方式不同 `Down` `Up` 。 您 `pointerIndex` 必須忽略先前從屬性取得的值 `ActionIndex` 。 相反地，方法必須透過 `pointerIndex` 迴圈0和屬性來取得多個值 `PointerCount` ，然後取得 `id` 每個 `pointerIndex` 值的：

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

這種處理類型可讓 [FingerPaint](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) 程式追蹤個別手指，並在畫面上繪製結果：

[![FingerPaint 範例的範例螢幕擷取畫面](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

您現在已瞭解如何在螢幕上追蹤個別手指，並加以區別。

## <a name="related-links"></a>相關連結

- [對等的 Xamarin iOS 指南](~/ios/app-fundamentals/touch/touch-tracking.md)
- [FingerPaint (範例) ](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)