---
title: "叫用事件的效果"
description: "效果可以定義，並叫用事件，發出訊號基礎原生檢視中的變更。 本文示範如何實作低層級的多點觸控手指追蹤，以及如何產生通知觸控活動的事件。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 89d3b56a15110d0c106c43ce227f11f86bbdf404
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="invoking-events-from-effects"></a>叫用事件的效果

_效果可以定義，並叫用事件，發出訊號基礎原生檢視中的變更。本文示範如何實作低層級的多點觸控手指追蹤，以及如何產生通知觸控活動的事件。_

本文中所述的效果提供低階觸控事件的存取。 這些低階事件不是可透過現有`GestureRecognizer`類別，不過，這些都是某些類型的應用程式很重要。 例如，finger-paint 以追蹤個別手指移動螢幕上的應用程式需求。 音樂鍵盤必須偵測點選，並在個別的索引鍵，以及從一個機 gliding glissando 之間手指釋放。

影響非常適用於多點觸控手指追蹤，因為它可以附加到 Xamarin.Forms 中的任何項目。

## <a name="platform-touch-events"></a>平台觸控事件

IOS、 Android 和通用 Windows 平台都包括可讓應用程式偵測到的低階 API 觸控活動。 所有三種基本類型之間進行區別這些平台觸控事件：

- *按下*、 手指碰觸螢幕
- *移動*、 觸控螢幕手指移動
- *釋放*、 手指發行從螢幕時

在多點觸控環境中，多個指可以同時觸控螢幕。 不同的平台包括識別碼 (ID) 應用程式可以用來區別多個手指。

在 iOS 中，`UIView`類別會定義三個可覆寫的方法， `TouchesBegan`， `TouchesMoved`，和`TouchesEnded`對應至這三個基本事件。 發行項[多點觸控手指追蹤](~/ios/app-fundamentals/touch/touch-tracking.md)描述如何使用這些方法。 但是，iOS 程式不需要覆寫的類別衍生自`UIView`才能使用這些方法。 IOS`UIGestureRecognizer`也會定義這些相同三個方法，而且您可以將衍生自類別的執行個體的連接`UIGestureRecognizer`任何`UIView`物件。

在 Android`View`類別會定義名為的可覆寫方法`OnTouchEvent`處理所有觸控活動。 觸控活動的型別定義的列舉型別成員`Down`， `PointerDown`， `Move`， `Up`，和`PointerUp`發行項中所述[多點觸控手指追蹤](~/android/app-fundamentals/touch/touch-tracking.md)。 在 Android`View`也會定義名為事件`Touch`，可讓事件處理常式附加至任何`View`物件。

在通用 Windows 平台 (UWP)，`UIElement`類別會定義名為事件`PointerPressed`， `PointerMoved`，和`PointerReleased`。 這些發行項中所述[MSDN 上的處理指標輸入文件](/windows/uwp/input-and-devices/handle-pointer-input/)和應用程式開發介面文件[ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/)類別。

`Pointer`通用 Windows 平台 API 為了整合滑鼠、 觸控及畫筆輸入。 基於這個原因，`PointerMoved`滑鼠將在項目之間移動，即使不按下滑鼠按鈕時叫用事件。 `PointerRoutedEventArgs`伴隨著這些事件的物件具有內容，名為`Pointer`，具有內容，名為`IsInContact`表示是否按下滑鼠按鈕或手指與其接觸螢幕。

此外，UWP 定義兩個名為的多個事件`PointerEntered`和`PointerExited`。 這些資訊表示當滑鼠或手指移動項目到另一個。 例如，假設名為 A 和 b 的兩個相鄰項目這兩個項目已安裝指標事件處理的常式。 A、 按手指時`PointerPressed`叫用事件。 在手指移動時，會叫用的`PointerMoved`事件。 在手指移動從 A 到 B，會叫用的`PointerExited`事件和 B 會叫用`PointerEntered`事件。 手指然後發行時，會叫用 B`PointerReleased`事件。

IOS 和 Android 平台會與不同 UWP： 第一次取得呼叫檢視`TouchesBegan`或`OnTouchEvent`手指碰觸繼續如果手指移至不同的檢視，取得所有觸控式活動，即使檢視。 如果應用程式會擷取指標 UWP 可以同樣的行為： 在`PointerEntered`事件處理常式，項目會呼叫`CapturePointer`，然後從該手指中取得所有觸控活動。

UWP 方法確實可針對某些類型的應用程式，例如，音樂鍵盤非常有用。 每個索引鍵可以處理該金鑰的觸控事件和偵測何時手指有一個索引鍵從 slid 到另一個使用`PointerEntered`和`PointerExited`事件。

因此，本文中所述的觸控追蹤效果實作 UWP 方法。

## <a name="the-touch-tracking-effect-api"></a>觸控追蹤影響應用程式開發介面

[**觸控追蹤效果示範**](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)範例包含類別 （和列舉型別），實作觸控低層級追蹤。 這些類型屬於命名空間`TouchTracking`字開頭和`Touch`。 **TouchTrackingEffectDemos**可攜式類別庫專案會包含`TouchActionType`觸控事件類型的列舉：

```csharp
public enum TouchActionType
{
    Entered,
    Pressed,
    Moved,
    Released,
    Exited,
    Cancelled
}
```

所有平台也會包含表示已取消觸控事件的事件。

`TouchEffect` PCL 中的類別衍生自`RoutingEffect`並定義名為事件`TouchAction`和方法，名為`OnTouchAction`會叫用`TouchAction`事件：

```csharp
public class TouchEffect : RoutingEffect
{
    public event TouchActionEventHandler TouchAction;

    public TouchEffect() : base("XamarinDocs.TouchEffect")
    {
    }

    public bool Capture { set; get; }

    public void OnTouchAction(Element element, TouchActionEventArgs args)
    {
        TouchAction?.Invoke(element, args);
    }
}
```

同時也請注意`Capture`屬性。 若要擷取觸控事件，應用程式必須將此屬性設`true`之前`Pressed`事件。 否則，觸控事件的行為與那些在通用 Windows 平台。

`TouchActionEventArgs` PCL 中的類別包含所有隨附的每個事件的資訊：

```csharp
public class TouchActionEventArgs : EventArgs
{
    public TouchActionEventArgs(long id, TouchActionType type, Point location, bool isInContact)
    {
        Id = id;
        Type = type;
        Location = location;
        IsInContact = isInContact;
    }

    public long Id { private set; get; }

    public TouchActionType Type { private set; get; }

    public Point Location { private set; get; }

    public bool IsInContact { private set; get; }
}
```

應用程式可以使用`Id`屬性，用於追蹤個別手指。 請注意`IsInContact`屬性。 此屬性一律為`true`如`Pressed`事件和`false`如`Released`事件。 它也是一律`true`如`Moved`iOS 和 Android 上的事件。 `IsInContact`屬性可能是`false`如`Moved`事件通用 Windows 平台時程式正執行於桌面，且不含按鈕的滑鼠指標移上按下。

您可以使用`TouchEffect`自己的應用程式方案的 PCL 專案中，包含檔案，並可加入至執行個體中的類別`Effects`Xamarin.Forms 中的任何項目集合。 附加至處理常式`TouchAction`取得觸控事件的事件。

若要使用`TouchEffect`自己的應用程式，在您將也需要納入的平台實作**TouchTrackingEffectDemos**方案。

## <a name="the-touch-tracking-effect-implementations"></a>觸控追蹤效果實作

IOS、 Android 和 UWP 的實作`TouchEffect`下述最簡單的實作 (UWP) 為開頭和結尾 iOS 實作，因為它比其他結構更複雜。

### <a name="the-uwp-implementation"></a>UWP 實作

UWP 實作`TouchEffect`是最簡單的。 像往常一樣，此類別衍生自`PlatformEffect`並包含兩個組件屬性：

```csharp
[assembly: ResolutionGroupName("XamarinDocs")]
[assembly: ExportEffect(typeof(TouchTracking.UWP.TouchEffect), "TouchEffect")]

namespace TouchTracking.UWP
{
    public class TouchEffect : PlatformEffect
    {
        ...
    }
}
```

`OnAttached`覆寫將某些資訊儲存為指標的所有事件欄位和附加的處理常式：

```csharp
public class TouchEffect : PlatformEffect
{
    FrameworkElement frameworkElement;
    TouchTracking.TouchEffect effect;
    Action<Element, TouchActionEventArgs> onTouchAction;

    protected override void OnAttached()
    {
        // Get the Windows FrameworkElement corresponding to the Element that the effect is attached to
        frameworkElement = Control == null ? Container : Control;

        // Get access to the TouchEffect class in the PCL
        effect = (TouchTracking.TouchEffect)Element.Effects.
                    FirstOrDefault(e => e is TouchTracking.TouchEffect);

        if (effect != null && frameworkElement != null)
        {
            // Save the method to call on touch events
            onTouchAction = effect.OnTouchAction;

            // Set event handlers on FrameworkElement
            frameworkElement.PointerEntered += OnPointerEntered;
            frameworkElement.PointerPressed += OnPointerPressed;
            frameworkElement.PointerMoved += OnPointerMoved;
            frameworkElement.PointerReleased += OnPointerReleased;
            frameworkElement.PointerExited += OnPointerExited;
            frameworkElement.PointerCanceled += OnPointerCancelled;
        }
    }
    ...
}    
```

`OnPointerPressed`藉由呼叫處理常式叫用的效果事件`onTouchAction`欄位`CommonHandler`方法：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerPressed(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Pressed, args);

        // Check setting of Capture property
        if (effect.Capture)
        {
            (sender as FrameworkElement).CapturePointer(args.Pointer);
        }
    }
    ...
    void CommonHandler(object sender, TouchActionType touchActionType, PointerRoutedEventArgs args)
    {
        PointerPoint pointerPoint = args.GetCurrentPoint(sender as UIElement);
        Windows.Foundation.Point windowsPoint = pointerPoint.Position;  

        onTouchAction(Element, new TouchActionEventArgs(args.Pointer.PointerId,
                                                        touchActionType,
                                                        new Point(windowsPoint.X, windowsPoint.Y),
                                                        args.Pointer.IsInContact));
    }
}
```

`OnPointerPressed` 也會檢查值`Capture`屬性在作用中的類別的 PCL 和呼叫`CapturePointer`便`true`。

 其他 UWP 事件處理常式是甚至更簡單的：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    void OnPointerEntered(object sender, PointerRoutedEventArgs args)
    {
        CommonHandler(sender, TouchActionType.Entered, args);
    }
    ...
}
```

### <a name="the-android-implementation"></a>Android 的實作

Android 和 iOS 的實作不一定是更複雜，因為它們必須實作`Exited`和`Entered`手指從一個項目移到另一個時的事件。 這兩種實作都有類似的結構。

在 Android`TouchEffect`類別安裝的處理常式`Touch`事件：

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

類別也會定義兩個靜態字典：

```csharp
public class TouchEffect : PlatformEffect
{
    ...
    static Dictionary<Android.Views.View, TouchEffect> viewDictionary =
        new Dictionary<Android.Views.View, TouchEffect>();

    static Dictionary<int, TouchEffect> idToEffectDictionary =
        new Dictionary<int, TouchEffect>();
    ...
```

`viewDictionary`取得新的項目每次`OnAttached`呼叫覆寫：

```csharp
viewDictionary.Add(view, this);
```

從字典中移除的項目`OnDetached`。 每個執行個體`TouchEffect`與特定的檢視效果附加至相關聯。 靜態字典可讓任何`TouchEffect`列舉所有其他檢視，以及其對應的執行個體`TouchEffect`執行個體。 這是為了允許傳送事件到另一個檢視。

Android 會指派可讓應用程式以追蹤個別指指觸控事件識別碼。 `idToEffectDictionary`將使用此識別碼程式碼`TouchEffect`執行個體。 項目加入至這個字典時`Touch`手指按為呼叫處理常式：

```csharp
void OnTouch(object sender, Android.Views.View.TouchEventArgs args)
{
    ...
    switch (args.Event.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:
            FireEvent(this, id, TouchActionType.Pressed, screenPointerCoords, true);

            idToEffectDictionary.Add(id, this);

            capture = pclTouchEffect.Capture;
            break;

```

從移除項目`idToEffectDictionary`手指從螢幕中的釋放。 `FireEvent`方法只會呼叫所需的所有資訊`OnTouchAction`方法：

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.pclTouchEffect.OnTouchAction;

    // Get the location of the pointer within the view
    touchEffect.view.GetLocationOnScreen(twoIntArray);
    double x = pointerLocation.X - twoIntArray[0];
    double y = pointerLocation.Y - twoIntArray[1];
    Point point = new Point(fromPixels(x), fromPixels(y));

    // Call the method
    onTouchAction(touchEffect.formsElement,
        new TouchActionEventArgs(id, actionType, point, isInContact));
}
```

所有其他觸控類型會處理兩個不同的方式： 如果`Capture`屬性是`true`，觸控事件是相當簡單的轉譯為`TouchEffect`資訊。 它變得更複雜時`Capture`是`false`因為觸控事件可能需要不同的檢視移到另一個。 這是由負責`CheckForBoundaryHop`會在移動事件期間呼叫的方法。 這個方法會使用這兩個靜態字典。 會列舉透過`viewDictionary`來判斷目前接觸手指，而且它會使用的檢視`idToEffectDictionary`來儲存目前`TouchEffect`執行個體 （和因此，目前的檢視） 的特定識別碼相關聯：

```csharp
void CheckForBoundaryHop(int id, Point pointerLocation)
{
    TouchEffect touchEffectHit = null;

    foreach (Android.Views.View view in viewDictionary.Keys)
    {
        // Get the view rectangle
        try
        {
            view.GetLocationOnScreen(twoIntArray);
        }
        catch // System.ObjectDisposedException: Cannot access a disposed object.
        {
            continue;
        }
        Rectangle viewRect = new Rectangle(twoIntArray[0], twoIntArray[1], view.Width, view.Height);

        if (viewRect.Contains(pointerLocation))
        {
            touchEffectHit = viewDictionary[view];
        }
    }

    if (touchEffectHit != idToEffectDictionary[id])
    {
        if (idToEffectDictionary[id] != null)
        {
            FireEvent(idToEffectDictionary[id], id, TouchActionType.Exited, pointerLocation, true);
        }
        if (touchEffectHit != null)
        {
            FireEvent(touchEffectHit, id, TouchActionType.Entered, pointerLocation, true);
        }
        idToEffectDictionary[id] = touchEffectHit;
    }
}
```

如果已變更`idToEffectionDictionary`，方法可能會在呼叫`FireEvent`如`Exited`和`Entered`不同的檢視之間的傳輸。 不過，在手指可能都已移至未附加的檢視所佔用的區域`TouchEffect`，或從該區域的檢視附加的效果。

請注意`try`和`catch`存取檢視時，封鎖。 在頁面中，巡覽至，然後向後巡覽至首頁上，`OnDetached`不會呼叫方法和項目保留在`viewDictionary`但 Android 會將它們視為處置。

### <a name="the-ios-implementation"></a>IOS 實作

IOS 實作很類似，除了 Android 實作 iOS`TouchEffect`類別必須具現化的衍生`UIGestureRecognizer`。 這是在名為的 iOS 專案的類別`TouchRecognizer`。 這個類別會維護兩個存放區的靜態字典`TouchRecognizer`執行個體：

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

這個結構的大多數`TouchRecognizer`類別是類似於 Android`TouchEffect`類別。

## <a name="putting-the-touch-effect-to-work"></a>將觸控效果放到工作

[ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)程式包含五個測試的一般工作的觸控追蹤效果的頁面。

**BoxView 拖曳**頁面可讓您新增`BoxView`項目`AbsoluteLayout`然後將它們在螢幕上拖曳。 [XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml)會呈現兩個`Button`檢視新增`BoxView`項目`AbsoluteLayout`和清除`AbsoluteLayout`。

中的方法[程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs)，加入新`BoxView`至`AbsoluteLayout`也會加入`TouchEffect`物件`BoxView`，並將事件處理常式附加的效果：

```csharp
void AddBoxViewToLayout()
{
    BoxView boxView = new BoxView
    {
        WidthRequest = 100,
        HeightRequest = 100,
        Color = new Color(random.NextDouble(),
                          random.NextDouble(),
                          random.NextDouble())
    };

    TouchEffect touchEffect = new TouchEffect();
    touchEffect.TouchAction += OnTouchEffectAction;
    boxView.Effects.Add(touchEffect);
    absoluteLayout.Children.Add(boxView);
}
```

`TouchAction`事件處理常式會處理所有觸控事件的所有`BoxView`項目，但它需要一些特別小心： 它不允許兩隻手指放在單一上`BoxView`，因此程式只會實作拖曳，將兩隻手指放會影響彼此。 基於這個理由，網頁會定義目前正在追蹤的每個手指內嵌的類別：

```csharp
class DragInfo
{
    public DragInfo(long id, Point pressPoint)
    {
        Id = id;
        PressPoint = pressPoint;
    }

    public long Id { private set; get; }

    public Point PressPoint { private set; get; }
}

Dictionary<BoxView, DragInfo> dragDictionary = new Dictionary<BoxView, DragInfo>();
```

`dragDictionary`包含的項目每個`BoxView`目前拖曳。

`Pressed`觸控動作到此字典，將項目和`Released`動作會移除它。 `Pressed`邏輯必須檢查是否有項目中針對該字典`BoxView`。 如果是這樣，`BoxView`且已拖曳到新的事件是第二個手指上的相同`BoxView`。 如`Moved`和`Released`動作，此事件處理常式必須檢查字典是否有項目，所需的`BoxView`，觸控`Id`屬性拖曳`BoxView`符合字典項目：

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    BoxView boxView = sender as BoxView;

    switch (args.Type)
    {
        case TouchActionType.Pressed:
            // Don't allow a second touch on an already touched BoxView
            if (!dragDictionary.ContainsKey(boxView))
            {
                dragDictionary.Add(boxView, new DragInfo(args.Id, args.Location));

                // Set Capture property to true
                TouchEffect touchEffect = (TouchEffect)boxView.Effects.FirstOrDefault(e => e is TouchEffect);
                touchEffect.Capture = true;
            }
            break;

        case TouchActionType.Moved:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                Rectangle rect = AbsoluteLayout.GetLayoutBounds(boxView);
                Point initialLocation = dragDictionary[boxView].PressPoint;
                rect.X += args.Location.X - initialLocation.X;
                rect.Y += args.Location.Y - initialLocation.Y;
                AbsoluteLayout.SetLayoutBounds(boxView, rect);
            }
            break;

        case TouchActionType.Released:
            if (dragDictionary.ContainsKey(boxView) && dragDictionary[boxView].Id == args.Id)
            {
                dragDictionary.Remove(boxView);
            }
            break;
    }
}
```

`Pressed`邏輯集`Capture`屬性`TouchEffect`物件`true`。 這有該手指的所有後續的事件傳送至相同的事件處理常式的效果。

`Moved`邏輯移`BoxView`藉由改變`LayoutBounds`附加屬性。 `Location`事件引數的屬性一律為相對於`BoxView`拖曳，而且如果`BoxView`正在拖曳以常數速率，`Location`連續事件的屬性會大約相同。 比方說，如果按下手指`BoxView`中間，`Pressed`動作存放區`PressPoint`屬性 （50，50），這仍屬於相同的後續事件。 如果`BoxView`對角線拖曳以常數速率，後續`Location`屬性期間`Moved`動作可能的值 （55，55），在此情況下`Moved`邏輯會增加 5的水平和垂直位置`BoxView`. 這會將`BoxView`使其中心再次手指在正下方。

您可以移動多個`BoxView`同時使用不同的指的項目。

[![](touch-tracking-images/boxviewdragging-small.png "BoxView 拖曳頁面的三個螢幕擷取畫面")](touch-tracking-images/boxviewdragging-large.png "BoxView 拖曳頁面的三個螢幕擷取畫面")

### <a name="subclassing-the-view"></a>子類別化檢視

通常，它是更輕鬆地處理其本身的觸控事件的 Xamarin.Forms 項目。 **Draggable BoxView 拖曳**頁函式與相同**BoxView 拖曳** 頁面上，但使用者拖曳是的執行個體的項目[ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs)類別衍生自`BoxView`:

```csharp
class DraggableBoxView : BoxView
{
    bool isBeingDragged;
    long touchId;
    Point pressPoint;

    public DraggableBoxView()
    {
        TouchEffect touchEffect = new TouchEffect
        {
            Capture = true
        };
        touchEffect.TouchAction += OnTouchEffectAction;
        Effects.Add(touchEffect);
    }

    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!isBeingDragged)
                {
                    isBeingDragged = true;
                    touchId = args.Id;
                    pressPoint = args.Location;
                }
                break;

            case TouchActionType.Moved:
                if (isBeingDragged && touchId == args.Id)
                {
                    TranslationX += args.Location.X - pressPoint.X;
                    TranslationY += args.Location.Y - pressPoint.Y;
                }
                break;

            case TouchActionType.Released:
                if (isBeingDragged && touchId == args.Id)
                {
                    isBeingDragged = false;
                }
                break;
        }
    }
}
```

建構函式會建立並附加`TouchEffect`，並設定`Capture`先具現化該物件的屬性。 沒有字典是必要的因為此類別本身會儲存`isBeingDragged`， `pressPoint`，和`touchId`食指每個相關聯的值。 `Moved`處理改變`TranslationX`和`TranslationY`屬性才可以將邏輯即使父代`DraggableBoxView`不`AbsoluteLayout`。

### <a name="integrating-with-skiasharp"></a>整合與 SkiaSharp

下面兩個示範需要圖形，因此它們使用 SkiaSharp 針對此目的。 您可能想要深入了解[Xamarin.Forms 中使用的 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)研究這些範例之前。 前兩個文件 （< SkiaSharp 繪圖基本概念 > 和 < SkiaSharp 線條和路徑 」） 會涵蓋所有您在這裡需要的項目。

**橢圓形繪圖**頁面可讓您繪製橢圓形撥動螢幕上的手指。 根據您的手指的移動方式而定，您可以繪製橢圓形從左上角到右下角，或任何其他邊角以相反邊角。 使用隨機的色彩和不透明度繪製橢圓形。

[![](touch-tracking-images/ellipsedrawing-small.png "繪製橢圓形頁面的三個螢幕擷取畫面")](touch-tracking-images/ellipsedrawing-large.png "橢圓形繪圖頁面的三個螢幕擷取畫面")

如果您再觸控省略符號的其中一個，您可以將它拖曳到另一個位置。 這需要 ant colony optimization"點擊測試，"會搜尋特定點的圖形化的物件。 SkiaSharp 省略符號不是 Xamarin.Forms 元素，因此它們無法執行自己`TouchEffect`處理。 `TouchEffect`必須套用至整個`SKCanvasView`物件。

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml)檔案會具現化`SKCanvasView`中單一儲存格`Grid`。 `TouchEffect`物件附加至的`Grid`:

```xaml
<Grid x:Name="canvasViewGrid"
        Grid.Row="1"
        BackgroundColor="White">

    <skia:SKCanvasView x:Name="canvasView"
                        PaintSurface="OnCanvasViewPaintSurface" />
    <Grid.Effects>
        <tt:TouchEffect Capture="True"
                        TouchAction="OnTouchEffectAction" />
    </Grid.Effects>
</Grid>
```

在 Android 和通用 Windows 平台`TouchEffect`可以直接附加`SKCanvasView`，但不能在 iOS 上。 請注意，`Capture`屬性設定為`true`。

SkiaSharp 呈現每個橢圓形由型別的物件`EllipseDrawingFigure`:

```csharp
class EllipseDrawingFigure
{
    SKPoint pt1, pt2;

    public EllipseDrawingFigure()
    {
    }

    public SKColor Color { set; get; }

    public SKPoint StartPoint
    {
        set
        {
            pt1 = value;
            MakeRectangle();
        }
    }

    public SKPoint EndPoint
    {
        set
        {
            pt2 = value;
            MakeRectangle();
        }
    }

    void MakeRectangle()
    {
        Rectangle = new SKRect(pt1.X, pt1.Y, pt2.X, pt2.Y).Standardized;
    }

    public SKRect Rectangle { set; get; }

    // For dragging operations
    public Point LastFingerLocation { set; get; }

    // For the dragging hit-test
    public bool IsInEllipse(SKPoint pt)
    {
        SKRect rect = Rectangle;

        return (Math.Pow(pt.X - rect.MidX, 2) / Math.Pow(rect.Width / 2, 2) +
                Math.Pow(pt.Y - rect.MidY, 2) / Math.Pow(rect.Height / 2, 2)) < 1;
    }
}
```

`StartPoint`和`EndPoint`程式正在處理觸控輸入; 時使用屬性`Rectangle`屬性用來繪製橢圓形。 `LastFingerLocation`屬性派上用場當被拖曳的省略符號，而`IsInEllipse`方法協助點擊測試。 方法會傳回`true`點是否在橢圓形內。

[程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs)維持三個集合：

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure`字典包含子集`completedFigures`集合。 SkiaSharp`PaintSurface`事件處理常式只會轉譯在這些物件`completedFigures`和`inProgressFigures`集合：

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Fill
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (EllipseDrawingFigure figure in completedFigures)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
    foreach (EllipseDrawingFigure figure in inProgressFigures.Values)
    {
        paint.Color = figure.Color;
        canvas.DrawOval(figure.Rectangle, paint);
    }
}
```

觸控處理最棘手的部分是`Pressed`處理。 這是 where 執行點擊測試，但是如果程式碼偵測到使用者的手指下橢圓形，該橢圓形只可拖曳如果目前未由另一個手指拖曳。 如果沒有在使用者的手指沒有橢圓形，程式碼就會開始繪製新的橢圓形的程序：

```csharp
case TouchActionType.Pressed:
    bool isDragOperation = false;

    // Loop through the completed figures
    foreach (EllipseDrawingFigure fig in completedFigures.Reverse<EllipseDrawingFigure>())
    {
        // Check if the finger is touching one of the ellipses
        if (fig.IsInEllipse(ConvertToPixel(args.Location)))
        {
            // Tentatively assume this is a dragging operation
            isDragOperation = true;

            // Loop through all the figures currently being dragged
            foreach (EllipseDrawingFigure draggedFigure in draggingFigures.Values)
            {
                // If there's a match, we'll need to dig deeper
                if (fig == draggedFigure)
                {
                    isDragOperation = false;
                    break;
                }
            }

            if (isDragOperation)
            {
                fig.LastFingerLocation = args.Location;
                draggingFigures.Add(args.Id, fig);
                break;
            }
        }
    }

    if (isDragOperation)
    {
        // Move the dragged ellipse to the end of completedFigures so it's drawn on top
        EllipseDrawingFigure fig = draggingFigures[args.Id];
        completedFigures.Remove(fig);
        completedFigures.Add(fig);
    }
    else // start making a new ellipse
    {
        // Random bytes for random color
        byte[] buffer = new byte[4];
        random.NextBytes(buffer);

        EllipseDrawingFigure figure = new EllipseDrawingFigure
        {
            Color = new SKColor(buffer[0], buffer[1], buffer[2], buffer[3]),
            StartPoint = ConvertToPixel(args.Location),
            EndPoint = ConvertToPixel(args.Location)
        };
        inProgressFigures.Add(args.Id, figure);
    }
    canvasView.InvalidateSurface();
    break;
```

其他 SkiaSharp 範例是**手指小畫家**頁面。 您可以從兩個選取的筆觸色彩和筆劃寬度`Picker`然後繪製以一個或多個手指與檢視：

[![](touch-tracking-images/fingerpaint-small.png "手指 [小畫家] 頁面的三個螢幕擷取畫面")](touch-tracking-images/fingerpaint-large.png "手指 [小畫家] 頁面的三個螢幕擷取畫面")

這個範例也會需要不同的類別來代表在螢幕上繪製每一行：

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new SKPath();
    }

    public SKPath Path { set; get; }

    public Color StrokeColor { set; get; }

    public float StrokeWidth { set; get; }
}
```

`SKPath`物件用來呈現每一行。 [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs)檔案會維護兩個集合，這些物件，一個用於目前所繪製的聚合線條，另一個用於已完成的聚合線條的：

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed`處理會建立新`FingerPaintPolyline`，呼叫`MoveTo`路徑物件來儲存初始的點，並將該物件加入`inProgressPolylines`字典。 `Moved`處理呼叫`LineTo`上新指位置的路徑物件和`Released`處理傳送已完成的聚合線條從`inProgressPolylines`至`completedPolylines`。 同樣地，實際的 SkiaSharp 繪圖程式碼是相當簡單：

```csharp
SKPaint paint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    StrokeCap = SKStrokeCap.Round,
    StrokeJoin = SKStrokeJoin.Round
};
...
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKCanvas canvas = args.Surface.Canvas;
    canvas.Clear();

    foreach (FingerPaintPolyline polyline in completedPolylines)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }

    foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
    {
        paint.Color = polyline.StrokeColor.ToSKColor();
        paint.StrokeWidth = polyline.StrokeWidth;
        canvas.DrawPath(polyline.Path, paint);
    }
}
```

### <a name="tracking-view-to-view-touch"></a>追蹤檢視來檢視觸控

設定所有先前的範例`Capture`屬性`TouchEffect`至`true`，任一時`TouchEffect`建立或當`Pressed`發生事件。 這可確保此相同的項目收到食指第一次按下檢視相關聯的所有事件。 最後一個範例會執行*不*設定`Capture`至`true`。 中斷與連線螢幕手指從一個項目移到另一個時，這會導致不同的行為。 此項目會手指移動中收到的事件，該`Type`屬性設定為`TouchActionType.Exited`和第二個項目接收的事件，該`Type`設定`TouchActionType.Entered`。

這種類型的觸控處理是非常有用的音樂鍵盤。 索引鍵應該能夠偵測到它按下時，同時也時手指從一個索引鍵投影片之間。

**無訊息的鍵盤**頁面會定義小[ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs)和[ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs)類別衍生自[ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs)，其衍生自`BoxView`。

`Key`類別是可供使用實際的音樂播放程式中。 它會定義名為的公用屬性`IsPressed`和`KeyNumber`，這要設為索引鍵 MIDI 標準所建立的程式碼。 `Key`類別也會定義名為事件`StatusChanged`，而當叫用`IsPressed`屬性變更。

允許多個指每個索引鍵。 基於這個理由，`Key`類別會維護`List`所有目前碰觸該金鑰的指觸控 ID 編號的：

```csharp
List<long> ids = new List<long>();
```

`TouchAction`事件處理常式新增至 ID`ids`兩個清單`Pressed`事件類型和`Entered`類型，但只有在`IsInContact`屬性是`true`如`Entered`事件。 識別碼已從`List`如`Released`或`Exited`事件：

```csharp
void OnTouchEffectAction(object sender, TouchActionEventArgs args)
{
    switch (args.Type)
    {
      case TouchActionType.Pressed:
          AddToList(args.Id);
          break;

        case TouchActionType.Entered:
            if (args.IsInContact)
            {
                AddToList(args.Id);
            }
            break;

        case TouchActionType.Moved:
            break;

        case TouchActionType.Released:
        case TouchActionType.Exited:
            RemoveFromList(args.Id);
            break;
    }
}

```

`AddToList`和`RemoveFromList`這兩個方法會檢查如果`List`空白且非空白、 之間已經變更，如果是的話，會叫用`StatusChanged`事件。

各種`WhiteKey`和`BlackKey`頁面中的項目排列[XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml)，電話會保留以橫向模式時，其中查詢最佳：

[![](touch-tracking-images/silentkeyboard-small.png "三個螢幕擷取畫面的無訊息的鍵盤頁面")](touch-tracking-images/silentkeyboard-large.png "的無訊息的鍵盤頁面的三個螢幕擷取畫面")

如果您執行您的手指清除整個索引鍵，您會看到有些微變更色彩觸控事件傳出至另一個索引鍵。

## <a name="summary"></a>總結

本文示範如何叫用事件的影響，以及如何撰寫和使用實作低層級的多點觸控處理效果。


## <a name="related-links"></a>相關連結

- [在 iOS 中的多點觸控手指追蹤](~/ios/app-fundamentals/touch/touch-tracking.md)
- [在 Android 中追蹤的多點觸控手指](~/android/app-fundamentals/touch/touch-tracking.md)
- [觸控追蹤效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
