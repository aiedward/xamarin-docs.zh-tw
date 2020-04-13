---
title: 從效果叫用事件
description: 效果可以定義並叫用事件，以通知基礎原生檢視中有變更。 本文說明如何實作低層級的多點觸控手指追蹤，以及如何產生通知觸控活動的事件。
ms.prod: xamarin
ms.assetid: 6A724681-55EB-45B8-9EED-7E412AB19DD2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/14/2018
ms.openlocfilehash: cf5a97bca7c827101db951a440863839539c7e48
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725256"
---
# <a name="invoking-events-from-effects"></a>從效果叫用事件

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)

_效果可以定義和調用事件,發出基礎本機檢視中更改的信號。本文演示如何實現低級多點觸摸手指跟蹤,以及如何生成信號觸摸活動的事件。_

本文所述的效果可提供對低層級觸控事件的存取。 這些低層級事件不能透過現有的 `GestureRecognizer` 類別取得，但它們對某些類型的應用程式來說非常重要。 例如，指繪應用程式需要追蹤個人手指在螢幕上的移動。 音樂鍵盤需要偵測對個別按鍵的點選和釋放，以及手指在滑奏中從某個按鍵滑到另一個按鍵的動作。

效果很適合用於多點觸控手指追蹤，因為它可以附加到任何 Xamarin.Forms 項目。

## <a name="platform-touch-events"></a>平台觸控事件

iOS、Android 和通用 Windows 平台都包含一個低層級的 API，可讓應用程式偵測觸控活動。 這些平台都能區分三種基本類型的觸控事件：

- *按下*，當手指觸控螢幕時
- *移動*，當觸控螢幕的手指移動時
- *放開*，當從螢幕放開手指時

在多點觸控的環境中，多個手指可同時觸控螢幕。 各種不同的平台包含應用程式可用來區分多個手指的識別碼 (ID)。

在 iOS 中，`UIView` 類別會定義三個可覆寫的方法 (`TouchesBegan`、`TouchesMoved` 和 `TouchesEnded`)，對應至這三種基本事件。 [多點觸控手指追蹤](~/ios/app-fundamentals/touch/touch-tracking.md)一文描述如何使用這些方法。 不過，iOS 程式不需要覆寫衍生自 `UIView` 的類別來使用這些方法。 iOS `UIGestureRecognizer` 也會定義這三個相同的方法，而且您可以將衍生自 `UIGestureRecognizer` 的類別執行個體附加至任何 `UIView` 物件。

在 Android 中，`View` 類別會定義名為 `OnTouchEvent` 的可覆寫方法，以處理所有觸控活動。 觸控活動類型是由列舉成員 `Down`、`PointerDown`、`Move`、`Up` 和 `PointerUp` 所定義，如[多點觸控手指追蹤](~/android/app-fundamentals/touch/touch-tracking.md)一文所述。 Android `View` 也會定義名為 `Touch` 的事件，其可讓事件處理常式附加至任何 `View` 物件。

在通用 Windows 平台 (UWP) 上，`UIElement` 類別會定義名為 `PointerPressed`、`PointerMoved` 和 `PointerReleased` 的事件。 在 [MSDN 上的處理指標輸入](/windows/uwp/input-and-devices/handle-pointer-input/)一文和 [`UIElement`](/uwp/api/windows.ui.xaml.uielement/) 類別的 API 文件中會描述這些事件。

通用 Windows 平台的 `Pointer` API 主要用於整合滑鼠、觸控及手寫筆輸入。 因此，當滑鼠移過項目時，即使未按下滑鼠按鈕，也會叫用 `PointerMoved` 事件。 伴隨著這些事件的 `PointerRoutedEventArgs` 物件具有名為 `Pointer` 的屬性，該屬性具有名為 `IsInContact` 的屬性，用來表示是否按下滑鼠按鈕或手指是否接觸到螢幕。

此外，UWP 還會定義兩個以上名為 `PointerEntered` 和 `PointerExited` 的事件。 這些事件指出滑鼠或手指何時從某個項目移到另一個項目。 例如，假設兩個名為 A 和 B 的相鄰項目。這兩個項目已安裝指標事件的處理常式。 當手指按下 A 時，將叫用 `PointerPressed` 事件。 隨著手指移動，A 會叫用 `PointerMoved` 事件。 如果手指從 A 移到 B，A 會叫用 `PointerExited` 事件，而 B 會叫用 `PointerEntered` 事件。 如果再放開手指，B 會叫用 `PointerReleased` 事件。

iOS 和 Android 平台不同於 UWP：當手指觸控視圖時，首先呼叫 `TouchesBegan` 或 `OnTouchEvent` 的檢視會繼續取得所有觸控活動，即使手指移到不同的檢視也是一樣。 UWP 在應用程式擷取指標時具有類似的行為：在 `PointerEntered` 事件處理常式中，項目會呼叫 `CapturePointer`，然後取得該手指的所有觸控活動。

UWP 方法證實非常適合某些類型的應用程式，例如音樂鍵盤。 每個按鍵可以處理該按鍵的觸控事件，並使用 `PointerEntered` 和 `PointerExited` 事件偵測手指何時從某個按鍵滑到另一個按鍵。

基於這個理由，本文所述的觸控追蹤效果會實作 UWP 方法。

## <a name="the-touch-tracking-effect-api"></a>觸控追蹤效果 API

[**觸控追蹤效果示範**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/)範例包含實作低層級觸控追蹤的類別 (和列舉)。 這些類型屬於 `TouchTracking` 命名空間，並以 `Touch` 一字開頭。 **TouchTrackingEffectDemos** .NET Standard 程式庫專案包含觸控事件類型的 `TouchActionType` 列舉：

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

所有平台也包含表示觸控事件已取消的事件。

.NET Standard 程式庫中的 `TouchEffect` 類別衍生自 `RoutingEffect`，並定義名為 `TouchAction` 的事件以及叫用 `TouchAction` 事件的 `OnTouchAction` 方法：

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

同時也請注意 `Capture` 屬性。 若要擷取觸控事件，應用程式必須在 `Pressed` 事件之前，將此屬性設定為 `true`。 否則，觸控事件的行為類似通用 Windows 平台。

.NET Standard 程式庫中的 `TouchActionEventArgs` 類別包含每個事件隨附的所有資訊：

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

應用程式可以使用 `Id` 屬性來追蹤個人手指。 請注意 `IsInContact` 屬性。 若為 `Pressed` 事件，此屬性一律是 `true`；若為 `Released` 事件，則為 `false`。 對於 iOS 和 Android 上的 `Moved` 事件，它也一律是 `true`。 當程式在桌面上執行，且滑鼠指標在沒有按下按鈕的情況下移動，通用 Windows 平台上 `Moved` 事件的 `IsInContact` 屬性可能是 `false`。

您可以在自己的應用程式中使用 `TouchEffect` 類別，方法是在解決方案的 .NET Standard 程式庫專案中包含檔案，以及將執行個體新增至任何 Xamarin.Forms 項目的 `Effects` 集合。 請將處理常式附加到 `TouchAction` 事件以取得觸控事件。

若要在您自己的應用程式中使用 `TouchEffect`，您還需要將平台實作包含在 **TouchTrackingEffectDemos** 解決方案中。

## <a name="the-touch-tracking-effect-implementations"></a>觸控追蹤效果實作

以下描述 `TouchEffect` 的 IOS、Android 和 UWP 的實作，從最簡單的實作 (UWP) 開始，並以 iOS 實作結束，因為它在結構上比其他實作更複雜。

### <a name="the-uwp-implementation"></a>UWP 實作

`TouchEffect` 的 UWP 實作最簡單。 像往常一樣，此類別衍生自 `PlatformEffect`，並包含兩個組件屬性：

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

`OnAttached` 覆寫會將某些資訊儲存為欄位，並將處理常式附加至所有指標事件：

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

`OnPointerPressed` 處理常式會透過呼叫 `CommonHandler`方法中的 `onTouchAction` 欄位來叫用效果事件：

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

`OnPointerPressed` 也會檢查 .NET Standard 程式庫中效果類別的 `Capture` 屬性值，如果它是 `true`，則會呼叫 `CapturePointer`。

 其他 UWP 事件處理常式甚至更簡單：

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

### <a name="the-android-implementation"></a>Android 實作

Android 和 iOS 實作一定更複雜，因為當手指從某個項目移到另一個項目時，它們必須實作 `Exited` 和 `Entered` 事件。 這兩種實作的結構都類似。

Android `TouchEffect` 類別會安裝 `Touch` 事件的處理常式：

```csharp
view = Control == null ? Container : Control;
...
view.Touch += OnTouch;
```

此類別還會定義兩個靜態字典：

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

每次呼叫 `OnAttached` 覆寫時，`viewDictionary` 都會取得新的項目：

```csharp
viewDictionary.Add(view, this);
```

該項目將從 `OnDetached` 的字典中移除。 每個 `TouchEffect` 執行個體都會與效果附加目標的特定檢視建立關聯。 靜態字典可讓任何 `TouchEffect` 執行個體列舉所有其他的檢視，以及其對應的 `TouchEffect` 執行個體。 這是允許將某個檢視中的事件傳送到另一個檢視的必要處理。

Android 可將讓應用程式追蹤個人手指的識別碼指派給觸控事件。 `idToEffectDictionary` 會將此識別碼與 `TouchEffect` 執行個體建立關聯。 若針對手指按下動作呼叫 `Touch` 處理常式，就會在這個字典中新增一個項目：

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

從螢幕放開手指時，該項目就會從 `idToEffectDictionary` 中移除。 `FireEvent` 方法只會累積呼叫 `OnTouchAction` 方法所需的所有資訊：

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

所有其他觸控類型都以兩種不同的方式處理：如果 `Capture` 屬性為 `true`，則觸控事件是相當簡單的 `TouchEffect` 資訊轉譯。 當 `Capture` 為 `false` 時會變得更複雜，因為觸控事件可能需要從某個檢視移到另一個檢視。 這是由移動事件期間呼叫的 `CheckForBoundaryHop` 方法負責。 此方法會使用這兩個靜態字典。 它會列舉 `viewDictionary` 來判斷手指目前觸控的檢視，並使用 `idToEffectDictionary` 來儲存與特定識別碼建立關聯的目前 `TouchEffect` 執行個體 (亦即目前的檢視)：

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

如果已變更 `idToEffectDictionary`，此方法可能會針對 `Exited` 和 `Entered` 呼叫 `FireEvent`，以便從某個檢視傳送到另一個檢視。 不過，手指可能已移到未附加 `TouchEffect` 的檢視所佔用區域，或已從該區域移到附加了效果的檢視。

存取檢視時，請注意 `try` 和 `catch` 區塊。 在巡覽至該頁面，然後巡覽回首頁的頁面中，不會呼叫 `OnDetached` 方法，而且項目會保留在 `viewDictionary` 中，但 Android 會將它們視為已處置。

### <a name="the-ios-implementation"></a>iOS 實作

iOS 實作與 Android 實作類似，不同之處在於 iOS `TouchEffect` 類別必須具現化 `UIGestureRecognizer` 的衍生項目。 這是 iOS 專案中名為 `TouchRecognizer` 的類別。 此類別會維護兩個儲存 `TouchRecognizer` 執行個體的靜態字典：

```csharp
static Dictionary<UIView, TouchRecognizer> viewDictionary =
    new Dictionary<UIView, TouchRecognizer>();

static Dictionary<long, TouchRecognizer> idToTouchDictionary =
    new Dictionary<long, TouchRecognizer>();
```

此 `TouchRecognizer` 類別的大部分結構類似於 Android `TouchEffect` 類別。

> [!IMPORTANT]
> `UIKit` 中的許多檢視，預設情況下未啟用觸控功能。 可以透過將 `view.UserInteractionEnabled = true;` 新增到 iOS 專案中 `TouchEffect` 類別的 `OnAttached` 覆寫來啟用觸控。 這應該在取得對應於附加效果之元素的 `UIView` 之後發生。

## <a name="putting-the-touch-effect-to-work"></a>實際使用觸控效果

[**TouchTrackingEffectDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) 程式包含五個用來測試一般工作觸控追蹤效果的頁面。

[BoxView 拖曳]**** 頁面可讓您將 `BoxView` 項目新增至 `AbsoluteLayout`，然後將它們拖曳到畫面上。 [XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml)會具現化兩個 `Button` 檢視，用來將 `BoxView` 項目新增至 `AbsoluteLayout`，以及清除 `AbsoluteLayout`。

[程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BoxViewDraggingPage.xaml.cs)中將新 `BoxView` 新增至 `AbsoluteLayout` 的方法，也會將 `TouchEffect` 物件新增至 `BoxView`，並將事件處理常式附加到效果：

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

`TouchAction` 事件處理常式會處理所有 `BoxView` 項目的所有觸控事件，但需要特別小心：它不允許在單一 `BoxView` 上使用兩指，因為程式只會實作拖曳，而兩個手指會相互干擾。 基於這個理由，頁面會針對目前所追蹤的每個手指定義內嵌的類別：

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

`dragDictionary` 包含目前所拖曳每個 `BoxView` 的項目。

`Pressed` 觸控動作會將項目新增至這個字典，而 `Released` 動作會將其移除。 `Pressed` 邏輯必須檢查該 `BoxView` 的字典是否已經有項目。 如果是的話，表示 `BoxView` 已拖曳，而新事件是該相同 `BoxView` 上的第二個手指。 對於 `Moved` 和 `Released` 動作，事件處理常式必須檢查字典是否有該 `BoxView` 的項目，以及所拖曳 `BoxView` 的 `Id` 屬性是否符合字典項目的屬性：

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

`Pressed` 邏輯會將 `TouchEffect` 物件的 `Capture` 屬性設定為 `true`。 這具有將該手指的所有後續事件傳遞至相同事件處理常式的效果。

`Moved` 邏輯透過改變 `LayoutBounds` 附加的屬性來移動 `BoxView`。 事件引數的 `Location` 屬性一律相對於所拖曳 `BoxView`，如果 `BoxView` 是以固定的速率拖曳，則連續事件的 `Location` 屬性將大致相同。 例如，若在 `BoxView` 的中心點按下手指，`Pressed` 動作會儲存 `PressPoint` 屬性 (50, 50)，後續事件的該屬性維持不變。 如果以對角線方式並以固定速率拖曳 `BoxView`，`Moved` 動作期間的後續 `Location` 屬性值可能為 (55, 55)，在此案例中，`Moved` 邏輯會將 `BoxView` 的水平和垂直位置加 5。 這會移動 `BoxView`，使其中心點再次位於您手指的正下方。

您可以使用不同的手指同時移動多個 `BoxView` 項目。

[![](touch-tracking-images/boxviewdragging-small.png "Triple screenshot of the BoxView Dragging page")](touch-tracking-images/boxviewdragging-large.png#lightbox "Triple screenshot of the BoxView Dragging page")

### <a name="subclassing-the-view"></a>子類別化檢視

通常，Xamarin.Forms 項目更容易處理自己的觸控事件。 [可拖曳的 BoxView 拖曳]**** 頁面的功能與 [BoxView 拖曳]**** 頁面相同，但使用者所拖曳項目是衍生自 `BoxView` 的 [`DraggableBoxView`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/DraggableBoxView.cs) 類別執行個體：

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

建構函式會建立及附加 `TouchEffect`，並在第一次具現化該物件的屬性時設定 `Capture`。 您不需要字典，因為此類別本身會儲存與每個手指建立關聯的 `isBeingDragged`、`pressPoint` 和 `touchId` 值。 `Moved` 處理會改變 `TranslationX` 和 `TranslationY` 屬性，因此即使 `DraggableBoxView` 的父代不是 `AbsoluteLayout`，邏輯也可運作。

### <a name="integrating-with-skiasharp"></a>與 SkiaSharp 整合

以下兩個示範需要圖形，因此它們使用 SkiaSharp 來達到這個目的。 在研究這些範例之前，建議您先了解如何[在 Xamarin.Forms 中使用 SkiaSharp](~/xamarin-forms/user-interface/graphics/skiasharp/index.md)。 前兩篇文章 (「SkiaSharp 繪圖基本概念」和「SkiaSharp 線條和路徑」) 涵蓋了您在此需要的一切資訊。

[橢圓形繪圖]**** 頁面可讓您在螢幕上撥動手指來繪製橢圓形。 根據您移動手指的方式而定，您可以使用從左上角到右下角，或從任何其他一角到對角的方式繪製橢圓形。 繪製的橢圓形具有隨機色彩且不透明。

[![](touch-tracking-images/ellipsedrawing-small.png "Triple screenshot of the Ellipse Drawing page")](touch-tracking-images/ellipsedrawing-large.png#lightbox "Triple screenshot of the Ellipse Drawing page")

如果您接著觸控其中一個橢圓形，就可以將它拖曳到另一個位置。 這需要稱為「點擊測試」的技術，其牽涉到在特定一點搜尋圖形化物件。 SkiaSharp 橢圓形並不是 Xamarin.Forms 項目，因此它們無法執行自己的 `TouchEffect` 處理。 `TouchEffect` 必須套用到整個 `SKCanvasView` 物件。

[EllipseDrawPage.XAML](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml) 檔案會以單一儲存格 `Grid` 具現化 `SKCanvasView`。 `TouchEffect` 物件會附加到該 `Grid`：

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

在 Android 和通用 Windows 平台中，`TouchEffect` 可以直接附加到 `SKCanvasView`，但在 iOS 上不行。 請注意，`Capture` 屬性設定為 `true`。

SkiaSharp 呈現的每個橢圓形都是以 `EllipseDrawingFigure` 類型的物件表示：

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

當程式正在處理觸控輸入時，會使用 `StartPoint` 和 `EndPoint` 屬性；`Rectangle` 屬性則用來繪製橢圓形。 在拖曳橢圓形時，`LastFingerLocation` 屬性就會派上用場，而 `IsInEllipse` 方法有助於點擊測試。 如果點在橢圓形內，此方法會傳回 `true`。

[程式碼後置檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/EllipseDrawingPage.xaml.cs)會維護三個集合：

```csharp
Dictionary<long, EllipseDrawingFigure> inProgressFigures = new Dictionary<long, EllipseDrawingFigure>();
List<EllipseDrawingFigure> completedFigures = new List<EllipseDrawingFigure>();
Dictionary<long, EllipseDrawingFigure> draggingFigures = new Dictionary<long, EllipseDrawingFigure>();
```

`draggingFigure` 字典包含 `completedFigures` 集合的子集。 SkiaSharp `PaintSurface` 事件處理常式只會呈現 `completedFigures` 和 `inProgressFigures`集合中的這些物件：

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

觸控處理最棘手的部分是 `Pressed` 處理。 這正是執行點擊測試的地方，但如果程式碼偵測到使用者手指下方有橢圓形，則該橢圓形只有在目前未由另一個手指加以拖曳時，才能進行拖曳。 如果使用者手指下方沒有任何橢圓形，程式碼就會開始繪製新橢圓形的流程：

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

另一個 SkiaSharp 範例是 [手繪]**** 頁面。 您可以從兩個 `Picker` 檢視中選取筆觸色彩和筆觸寬度，然後用一或多個手指進行繪製：

[![](touch-tracking-images/fingerpaint-small.png "Triple screenshot of the Finger Paint page")](touch-tracking-images/fingerpaint-large.png#lightbox "Triple screenshot of the Finger Paint page")

此範例也需要不同類別來表示螢幕上繪製的每一個線條：

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

`SKPath` 物件用來呈現每一個線條。 [FingerPaint.XAML.cs](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/FingerPaintPage.xaml.cs) 檔案會維護這些物件的兩個集合，一個用於目前所繪製的聚合線條，另一個用於已完成的聚合線條：

```csharp
Dictionary<long, FingerPaintPolyline> inProgressPolylines = new Dictionary<long, FingerPaintPolyline>();
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

`Pressed` 處理會建立新的 `FingerPaintPolyline`、在用來儲存起始點的路徑物件上呼叫 `MoveTo`，並將該物件新增至 `inProgressPolylines` 字典。 `Moved` 處理會以新的手指位置在路徑物件上呼叫 `LineTo`，而 `Released` 處理會將已完成的聚合線條從 `inProgressPolylines` 傳送到 `completedPolylines`。 再次強調，實際的 SkiaSharp 繪圖程式碼相當簡單：

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

### <a name="tracking-view-to-view-touch"></a>追蹤檢視對檢視觸控

不論是建立 `TouchEffect` 或發生 `Pressed` 事件時，所有之前的範例已將 `TouchEffect` 的 `Capture` 屬性設定為 `true`。 這可確保相同的項目會接收與第一個按下檢視的手指建立關聯的所有事件。 最後一個範例「不會」** 將 `Capture` 設定為 `true`。 當接觸螢幕的手指從某個項目移到另一個項目時，這會導致不同的行為。 手指移動的來源項目會接收 `Type` 屬性設定為 `TouchActionType.Exited` 的事件，而第二個項目則接收具有 `Type` 設定 `TouchActionType.Entered` 的事件。

這種類型的觸控處理非常適用於音樂鍵盤。 按鍵應該能夠偵測其按下時間，同時也能偵測手指何時從某個按鍵滑到另一個按鍵。

[靜音鍵盤]**** 頁面可定義衍生自 [`Key`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/Key.cs) (其衍生自 `BoxView`) 的小型 [`WhiteKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/WhiteKey.cs) 和 [`BlackKey`](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/BlackKey.cs) 類別。

`Key` 類別已準備好在實際的音樂應用程式中使用。 它可定義名為 `IsPressed` 和 `KeyNumber` 的公用屬性，其目的是要設定為依照 MIDI 標準建立的按鍵碼。 `Key` 類別也會定義名為 `StatusChanged` 的事件，當 `IsPressed` 屬性變更時就會叫用該事件。

每個按鍵上允許使用多個手指。 基於這個理由，`Key` 類別會維護一份 `List`，其中包含所有手指(目前觸控該按鍵) 的觸控識別碼：

```csharp
List<long> ids = new List<long>();
```

`TouchAction` 事件處理常式會將識別碼同時新增至 `Pressed` 事件類型和 `Entered` 類型的 `ids` 清單，但只有當 `Entered` 事件的 `IsInContact` 屬性是 `true` 時才會這樣。 對於 `Released` 或 `Exited` 事件，識別碼會從 `List` 中移除：

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

`AddToList` 和 `RemoveFromList` 這兩個方法會檢查 `List` 是否已在空白與非空白之間變更，如果是的話，請叫用 `StatusChanged` 事件。

各種 `WhiteKey` 和 `BlackKey` 項目排列在頁面的 [XAML 檔案](https://github.com/xamarin/xamarin-forms-samples/blob/master/Effects/TouchTrackingEffect/TouchTrackingEffect/TouchTrackingEffect/SilentKeyboardPage.xaml)中，當行動電話保持為橫向模式時，這看起來的效果最好：

[![](touch-tracking-images/silentkeyboard-small.png "Triple screenshot of the Silent Keyboard page")](touch-tracking-images/silentkeyboard-large.png#lightbox "Triple screenshot of the Silent Keyboard page")

如果您的手指在按鍵間劃過，您可透過色彩的稍微變化看到觸控事件正在從某個按鍵傳送到另一個按鍵。

## <a name="summary"></a>總結

本文已示範如何以效果叫用事件，以及如何撰寫和使用實作低層級多點觸控處理的效果。

## <a name="related-links"></a>相關連結

- [iOS 中的多點觸控手指追蹤](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Android 中的多點觸控手指追蹤](~/android/app-fundamentals/touch/touch-tracking.md)
- [Touch Tracking Effect (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/effects-touchtrackingeffect/) (觸控追蹤效果 (範例))
