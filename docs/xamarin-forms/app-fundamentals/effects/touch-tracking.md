---
title: 叫用事件的效果
description: 效果可以定義，並叫用事件，發出訊號變更基礎原生檢視中。 本文說明如何實作低層級的多點觸控手指追蹤，以及如何產生通知觸控活動的事件。
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 0a5e2c1a7a7807da91fd98e617467ea251a25bc0
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527400"
---
# <a name="invoking-events-from-effects"></a>叫用事件的效果

_效果可以定義，並叫用事件，發出訊號變更基礎原生檢視中。本文說明如何實作低層級的多點觸控手指追蹤，以及如何產生通知觸控活動的事件。_

這篇文章中所述的效果會提供低階的觸控事件的存取權。 這些低層級的事件不能透過現有`GestureRecognizer`類別，但它們都是某些類型的應用程式很重要。 比方說，finger-paint 追蹤個人的手指移動螢幕上的應用程式需求。 音樂鍵盤必須偵測點選，並在個別的索引鍵，以及至 glissando 中的另一個金鑰 gliding 手指釋放。

效果是適用於多點觸控的手指追蹤，因為它可以附加至 Xamarin.Forms 中的任何項目。

## <a name="platform-touch-events"></a>平台觸控事件

IOS、 Android 和通用 Windows 平台都包括低層級的 API，可讓應用程式偵測到同一個活動。 所有三種基本類型區分這些平台觸控事件：

- *按下*，當手指觸控螢幕
- *移動*，當移動手指觸控螢幕
- *發行*、 手指放開從螢幕時

在多點觸控的環境中，多個根手指可以觸控式螢幕，在相同的時間。 各種不同的平台包括識別碼 (ID) 應用程式可以用來區別多個根手指。

在 iOS 中，`UIView`類別會定義三個可覆寫的方法， `TouchesBegan`， `TouchesMoved`，和`TouchesEnded`對應至這三個基本事件。 發行項[多點觸控的手指追蹤](~/ios/app-fundamentals/touch/touch-tracking.md)描述如何使用這些方法。 不過，iOS 程式不需要覆寫類別，衍生自`UIView`使用這些方法。 IOS`UIGestureRecognizer`也會定義這些相同三種方法，而且您可以將衍生自類別的執行個體的連接`UIGestureRecognizer`任何`UIView`物件。

在 Android 中，`View`類別會定義名為的可覆寫方法`OnTouchEvent`處理觸控的所有活動。 觸控活動型別定義的列舉成員`Down`， `PointerDown`， `Move`， `Up`，並`PointerUp`一文所述[多點觸控的手指追蹤](~/android/app-fundamentals/touch/touch-tracking.md)。 Android`View`也會定義名為事件`Touch`，可讓事件處理常式附加至任何`View`物件。

通用 Windows 平台 (UWP) 中，`UIElement`類別會定義名為的事件`PointerPressed`， `PointerMoved`，和`PointerReleased`。 這些本文所述[MSDN 上的處理指標輸入文件](/windows/uwp/input-and-devices/handle-pointer-input/)和的 API 文件[ `UIElement` ](/uwp/api/windows.ui.xaml.uielement/)類別。

`Pointer`在通用 Windows 平台的 API 要整合滑鼠、 觸控及手寫筆輸入。 基於這個理由，`PointerMoved`滑鼠移動經過項目，即使不按下滑鼠按鈕時叫用事件。 `PointerRoutedEventArgs`伴隨著這些事件的物件具有名為`Pointer`具有屬性，名為`IsInContact`表示是否按下滑鼠按鈕或手指接觸到螢幕。

此外，UWP 定義兩個名為的多個事件`PointerEntered`和`PointerExited`。 這些資訊表示當滑鼠或手指移動從一個項目到另一個。 例如，假設兩個相鄰的項目，名為 A 和 b。這兩個項目已安裝指標事件處理的常式。 A、 按下的手指時`PointerPressed`叫用事件。 當移動手指時，會叫用的`PointerMoved`事件。 如果手指移動從 A 到 B，A 會叫用`PointerExited`事件和 B 會叫用`PointerEntered`事件。 如果再發行手指時，會叫用 B`PointerReleased`事件。

IOS 和 Android 平台會不同於 UWP： 第一次取得 呼叫檢視`TouchesBegan`或`OnTouchEvent`手指觸控的檢視會繼續取得觸控的所有活動，即使手指移至不同的檢視。 如果應用程式會擷取指標 UWP 的同樣的行為： 在`PointerEntered`事件處理常式，項目會呼叫`CapturePointer`，並再取得該手指觸控的所有活動。

UWP 方法證實是非常適合某些類型的應用程式，例如，音樂鍵盤。 每個索引鍵可以處理觸控事件，該索引鍵，並偵測何時手指已依序滑到另一個使用一個索引鍵`PointerEntered`和`PointerExited`事件。

基於這個理由，這篇文章中所述的觸控追蹤效果會實作 UWP 方法。

## <a name="the-touch-tracking-effect-api"></a>追蹤觸控效果 API

[**觸控追蹤的效果示範**](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)範例包含的類別 （和列舉型別） 實作低階的觸控追蹤。 這些類型屬於命名空間`TouchTracking`一字開頭和`Touch`。 **TouchTrackingEffectDemos** .NET Standard 程式庫專案包含`TouchActionType`觸控事件類型的列舉型別：

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

`TouchEffect` .NET Standard 程式庫中的類別衍生自`RoutingEffect`，並定義一個名稱為事件`TouchAction`和名為`OnTouchAction`叫用`TouchAction`事件：

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

另外也請注意`Capture`屬性。 若要擷取觸控事件，應用程式必須將此屬性設`true`之前`Pressed`事件。 否則，觸控事件的行為類似中通用 Windows 平台。

`TouchActionEventArgs`類別在.NET Standard 程式庫包含隨附於每個事件的所有資訊：

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

應用程式可以使用`Id`追蹤個人的手指的屬性。 請注意`IsInContact`屬性。 此屬性一律會是`true`for`Pressed`事件並`false`的`Released`事件。 它也是一直`true`針對`Moved`iOS 和 Android 上的事件。 `IsInContact`屬性可能`false`如`Moved`事件通用 Windows 平台當程式在桌面上執行，並沒有一個按鈕的滑鼠指標移上按下。

您可以使用`TouchEffect`自己的應用程式藉由在.NET Standard 程式庫專案的方案，包括檔案，並可加入至執行個體中的類別`Effects`的 Xamarin.Forms 中的任何項目集合。 附加至處理常式`TouchAction`取得觸控事件的事件。

若要使用`TouchEffect`在自己的應用程式中，您還需要包含在平台實作**TouchTrackingEffectDemos**解決方案。

## <a name="the-touch-tracking-effect-implementations"></a>追蹤觸控效果實作

IOS、 Android 和 UWP 的實作`TouchEffect`最簡單的實作 (UWP) 為開頭和結尾的 iOS 實作，因為它是比其他更多結構複雜如下所述。

### <a name="the-uwp-implementation"></a>UWP 實作

UWP 實作`TouchEffect`是最簡單。 像往常一樣，此類別衍生自`PlatformEffect`並包含兩個組件屬性：

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

`OnAttached`覆寫將某些資訊儲存為指標的所有事件的欄位和附加處理常式：

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

        // Get access to the TouchEffect class in the .NET Standard library
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

`OnPointerPressed`處理常式叫用呼叫的效果事件`onTouchAction`欄位中`CommonHandler`方法：

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

`OnPointerPressed` 也會檢查的值`Capture`作用中的類別呼叫與.NET Standard 程式庫中的屬性`CapturePointer`如果`true`。

 其他 UWP 事件處理常式是甚至更簡單：

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

Android 和 iOS 的實作是一定更複雜，因為它們必須實作`Exited`和`Entered`手指從一個項目移到另一個時的事件。 這兩種實作結構，類似。

Android`TouchEffect`類別會安裝的處理常式`Touch`事件：

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

字典中移除項目`OnDetached`。 每個執行個體`TouchEffect`效果會附加至特定檢視相關聯。 靜態字典可讓任何`TouchEffect`列舉所有其他的檢視，以及其對應的執行個體`TouchEffect`執行個體。 這是為了允許一個檢視中的事件傳送到另一個。

Android 會指派可讓應用程式來追蹤個人的手指觸控事件識別碼。 `idToEffectDictionary`將使用此識別碼程式碼`TouchEffect`執行個體。 將項目加入至這個字典時`Touch`手指按下會呼叫處理常式：

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

            capture = libTouchEffect.Capture;
            break;

```

從移除項目`idToEffectDictionary`手指從螢幕中的釋放。 `FireEvent`方法只是往上累加的呼叫所需的所有資訊`OnTouchAction`方法：

```csharp
void FireEvent(TouchEffect touchEffect, int id, TouchActionType actionType, Point pointerLocation, bool isInContact)
{
    // Get the method to call for firing events
    Action<Element, TouchActionEventArgs> onTouchAction = touchEffect.libTouchEffect.OnTouchAction;

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

兩種不同方式處理所有其他觸控類型： 如果`Capture`屬性是`true`，觸控事件是相當簡單的轉譯成`TouchEffect`資訊。 它會取得更複雜的時機`Capture`是`false`因為觸控事件可能需要移到另一個不同的檢視。 這是由負責`CheckForBoundaryHop`移動事件期間呼叫的方法。 這個方法會使用這兩個靜態字典。 它會透過列舉`viewDictionary`來判斷，目前會觸碰手指，並使用檢視`idToEffectDictionary`來儲存目前`TouchEffect`執行個體 （並因此，目前的檢視） 具有特定識別碼相關聯：

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

如果已變更`idToEffectDictionary`，方法可能會呼叫`FireEvent`for`Exited`和`Entered`到不同的檢視之間的傳輸。 不過，手指可能都已移至未附加的檢視所佔用的區域`TouchEffect`，或從該區域以具有附加作用的檢視。

請注意`try`和`catch`封鎖存取檢視時。 在頁面中巡覽至，然後回到首頁上，瀏覽`OnDetached`不會呼叫方法和項目保留在`viewDictionary`但 Android 會將它們視為處置。

### <a name="the-ios-implementation"></a>IOS 的實作

IOS 實作很類似，不同之處在於 Android 實作 iOS`TouchEffect`類別必須具現化的衍生物`UIGestureRecognizer`。 這是名為 iOS 專案中的類別`TouchRecognizer`。 這個類別會維護兩個儲存的靜態字典`TouchRecognizer`執行個體：

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

這個結構的更`TouchRecognizer`類別是類似於 Android`TouchEffect`類別。

## <a name="putting-the-touch-effect-to-work"></a>將觸控效果放到工作

[ **TouchTrackingEffectDemos** ](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)程式包含五個測試的一般工作的觸控追蹤效果的頁面。

**BoxView 拖曳**頁面可讓您新增`BoxView`項目`AbsoluteLayout`再將它們拖曳畫面。 [XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml)具現化兩個`Button`加入檢視`BoxView`項目`AbsoluteLayout`並清除`AbsoluteLayout`。

中的方法[程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BoxViewDraggingPage.xaml.cs)，將新`BoxView`要`AbsoluteLayout`也會加入`TouchEffect`物件`BoxView`，並將事件處理常式附加的效果：

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

`TouchAction`事件處理常式會處理所有觸控事件的所有`BoxView`項目，但它需要一些特別小心： 它不允許在單一的兩指`BoxView`程式只會實作拖曳的方式，和兩隻手指會因為會影響彼此。 基於這個理由，頁面會定義目前正在追蹤每根手指的內嵌的類別：

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

`dragDictionary`包含的項目每個`BoxView`目前正在拖曳。

`Pressed`觸控動作會將項目加入至這個字典中，而`Released`動作會移除它。 `Pressed`邏輯必須檢查是否有已項目中針對該字典`BoxView`。 如果是的話`BoxView`為已拖曳，而新的事件是第二個手指該相同`BoxView`。 針對`Moved`及`Released`動作，事件處理常式必須檢查字典是否有為此項目`BoxView`且觸控`Id`屬性拖曳`BoxView`符合字典項目：

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

`Pressed`邏輯集`Capture`屬性`TouchEffect`物件`true`。 這有該手指的所有後續的事件傳遞至相同的事件處理常式的效果。

`Moved`邏輯移`BoxView`改變`LayoutBounds`附加屬性。 `Location`之事件引數的屬性一定是相對於`BoxView`被拖曳，如果`BoxView`變的速率，被拖曳`Location`連續事件的屬性會大約相同。 比方說，如果按下手指`BoxView`在其中心`Pressed`動作存放區`PressPoint`屬性 （50，50），這仍屬於相同的後續事件。 如果`BoxView`對角線方式拖曳以常數速率，後續`Location`屬性期間`Moved`動作可能的值 （55，55），在此情況下`Moved`邏輯會增加 5水平和垂直位置`BoxView`. 這會將`BoxView`使其中心點一次正下方的手指。

您可以移動多個`BoxView`同時使用不同的指的項目。

[![](touch-tracking-images/boxviewdragging-small.png "BoxView 拖曳頁面的三個螢幕擷取畫面")](touch-tracking-images/boxviewdragging-large.png#lightbox "BoxView 拖曳頁面的三個螢幕擷取畫面")

### <a name="subclassing-the-view"></a>子類別化檢視

通常，它會更容易處理它自己的觸控事件的 Xamarin.Forms 元素。 **可拖曳 BoxView 拖曳**頁函式相同**BoxView 拖曳**頁面上，但使用者拖曳是的執行個體的項目[ `DraggableBoxView` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/DraggableBoxView.cs)衍生自類別`BoxView`:

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

建構函式會建立並附加`TouchEffect`，並設定`Capture`時先具現化該物件的屬性。 沒有字典是必要的因為此類別本身會儲存`isBeingDragged`， `pressPoint`，和`touchId`每根手指與相關聯的值。 `Moved`處理改變`TranslationX`並`TranslationY`屬性，如此處理邏輯會即使父代`DraggableBoxView`不是`AbsoluteLayout`。

### <a name="integrating-with-skiasharp"></a>SkiaSharp 與整合

下面兩個示範需要圖形，因此它們使用 SkiaSharp 針對此目的。 您可能想要深入了解[Xamarin.Forms 中的使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)研究這些範例之前。 前兩個發行項 （"SkiaSharp 繪圖基本概念 」 和 「 SkiaSharp 線條和路徑 」） 可涵蓋所有這裡，您將需要的項目。

**橢圓形繪圖** 頁面可讓您撥動手指在螢幕上的繪製一個橢圓形。 根據如何您移動手指時，您可以繪製橢圓形從左上角到右下角，或任何其他邊角以對角。 橢圓形會繪製隨機色彩和不透明度。

[![](touch-tracking-images/ellipsedrawing-small.png "三個橢圓形繪圖頁面螢幕擷取畫面")](touch-tracking-images/ellipsedrawing-large.png#lightbox "三個橢圓形繪圖頁面螢幕擷取畫面")

然後將其中一個橢圓形觸控，如果您可以將它拖曳到另一個位置。 這需要稱為 「 點擊測試 」 這牽涉到搜尋的圖形化的物件，在特定時間點的技術。 SkiaSharp 省略符號並不是 Xamarin.Forms 項目，因此它們無法執行自己`TouchEffect`處理。 `TouchEffect`必須套用至整個`SKCanvasView`物件。

[EllipseDrawPage.xaml](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml)檔案會具現化`SKCanvasView`在單一儲存格`Grid`。 `TouchEffect`物件附加至的`Grid`:

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

SkiaSharp 呈現每個橢圓形由類型的物件`EllipseDrawingFigure`:

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

`StartPoint`並`EndPoint`屬性用程式處理觸控輸入; 時`Rectangle`屬性用來繪製橢圓形。 `LastFingerLocation`屬性派上用場時被拖曳橢圓形，和`IsInEllipse`方法協助點擊測試。 此方法會傳回`true`點是否在 ellipse 內。

[程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/EllipseDrawingPage.xaml.cs)維護三個集合：

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure`字典包含其中一部分`completedFigures`集合。 SkiaSharp`PaintSurface`事件處理常式只會呈現在這些物件`completedFigures`和`inProgressFigures`集合：

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

觸控處理最棘手的部分是`Pressed`處理。 這正是執行點擊測試，但如果程式碼偵測到使用者的手指底下的省略符號，該橢圓形只可以在拖曳如果目前未由另一根手指拖曳。 如果沒有任何使用者的手指底下的省略符號，程式碼就會開始繪製新的橢圓形的程序：

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

SkiaSharp 範例**手指繪製**頁面。 您可以從兩個選取的筆劃色彩和筆劃寬度`Picker`檢視，然後使用一個或多個根手指繪製：

[![](touch-tracking-images/fingerpaint-small.png "手指繪製頁面的三個螢幕擷取畫面")](touch-tracking-images/fingerpaint-large.png#lightbox "手指繪製頁面的三個螢幕擷取畫面")

這個範例也會需要個別的類別來代表在螢幕上繪製每一行：

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

`SKPath`物件用來呈現每一行。 [FingerPaint.xaml.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/FingerPaintPage.xaml.cs)檔案會維護兩個集合的這些物件，一個用於目前所繪製的多線條，另一個用於已完成的多線條：

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed`處理新建`FingerPaintPolyline`，呼叫`MoveTo`路徑物件來儲存初始的點，並將該物件加入`inProgressPolylines`字典。 `Moved`處理呼叫`LineTo`路徑物件，與新的手指的位置，而`Released`處理傳輸的已完成的聚合線條`inProgressPolylines`到`completedPolylines`。 同樣地，實際的 SkiaSharp 繪圖程式碼是相當簡單：

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

所有先前的範例已設定`Capture`的屬性`TouchEffect`要`true`，時`TouchEffect`建立或當`Pressed`發生事件。 這可確保相同的項目會接收手指第一次按下檢視相關聯的所有事件。 最後一個範例會執行*未*設定`Capture`至`true`。 手指接觸到螢幕從一個項目移到另一個時，這會導致不同的行為。 從手指移動的項目接收事件`Type`屬性設定為`TouchActionType.Exited`和第二個項目接收事件`Type`設定`TouchActionType.Entered`。

這種類型的觸控處理是非常適合音樂鍵盤。 索引鍵應該能夠偵測它按下時，同時也時手指從一個索引鍵投影片之間。

**無訊息的鍵盤**頁面會定義小[ `WhiteKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/WhiteKey.cs)並[ `BlackKey` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/BlackKey.cs)類別衍生自[ `Key` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/Key.cs)，其衍生自`BoxView`。

`Key`類別已準備好在實際的音樂應用程式中使用。 它會定義名為的公用屬性`IsPressed`和`KeyNumber`，其目的是要設為主要的程式碼建立的 MIDI 標準的方法。 `Key`類別也會定義名為事件`StatusChanged`，這當叫用`IsPressed`屬性變更。

每個索引鍵中不能使用多個根手指。 基於這個理由，`Key`類別會負責維護`List`touch ID 編號，所有目前觸碰該金鑰的指的：

```csharp
List<long> ids = new List<long>();
```

`TouchAction`事件處理常式新增至 ID`ids`兩個清單`Pressed`事件類型和`Entered`類型，但只有當`IsInContact`屬性是`true`如`Entered`事件。 識別碼移除了`List`for`Released`或`Exited`事件：

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

`AddToList`並`RemoveFromList`這兩個方法會檢查是否`List`空白] 和 [非空白，之間已經變更，如果是的話，會叫用`StatusChanged`事件。

各種`WhiteKey`並`BlackKey`頁面中的項目排列[XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffectDemos/TouchTrackingEffectDemos/TouchTrackingEffectDemos/SilentKeyboardPage.xaml)，這看起來最好的行動電話會保留在橫向模式中時：

[![](touch-tracking-images/silentkeyboard-small.png "無訊息的鍵盤頁面的三個螢幕擷取畫面")](touch-tracking-images/silentkeyboard-large.png#lightbox "的無訊息的鍵盤頁面的三個螢幕擷取畫面")

如果您執行您的手指清除到索引鍵時，您會看到稍微變更色彩觸控事件傳出到另一個索引鍵。

## <a name="summary"></a>總結

這篇文章已示範如何叫用事件的影響，以及如何撰寫和使用可實作低層級的多點觸控處理影響。


## <a name="related-links"></a>相關連結

- [在 iOS 中的多點觸控的手指追蹤](~/ios/app-fundamentals/touch/touch-tracking.md)
- [追蹤在 Android 中的多點觸控手指](~/android/app-fundamentals/touch/touch-tracking.md)
- [觸控追蹤效果 （範例）](https://developer.xamarin.com/samples/xamarin-forms/effects/TouchTrackingEffectDemos/)
