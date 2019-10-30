---
title: Quick Interaction Techniques for watchOS 3 in Xamarin
description: This article covers the quick interaction techniques Apple has added in watchOS 3 and how to implement them in Xamarin.iOS for Apple Watch.
ms.prod: xamarin
ms.assetid: 26697F68-AF7E-4A36-988F-85E2674A4DD1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 8b851721aa5b2b993ad64b89d90d02b5f2bd0ee3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028193"
---
# <a name="quick-interaction-techniques-for-watchos-3-in-xamarin"></a>Quick Interaction Techniques for watchOS 3 in Xamarin

_This article covers the quick interaction techniques Apple has added in watchOS 3 and how to implement them in Xamarin.iOS for Apple Watch._

Providing quick user interactions are essential to creating compelling Apple Watch apps and Complications. WatchOS 3 的新手新增了對手勢辨識器的支援、Digital Crown 的存取權，以及新的使用者通知和流覽技術。 這同時提供 SceneKit 和 SpriteKit 的支援，可讓開發人員輕鬆地建立既快速又迅速的豐富 glanceable 介面。

## <a name="what-are-quick-interactions"></a>什麼是快速互動

針對用來建立 iOS 或 macOS 應用程式的開發人員（其中使用者花費在數分鐘或數小時內進行與應用程式互動的時間量），設計成功的 Apple Watch 應用程式可能會是一項挑戰，而且需要不同的出價.

在 watchOS 中，使用者通常會想要提高手腕、快速地與應用程式互動（通常只需要短暫幾秒鐘），然後卸載手腕並繼續進行。

以下是一些典型的 Apple Watch 快速互動範例：

- 啟動計時器。
- 檢查天氣。
- 將專案標記為待辦事項清單。

若要達成這些目標，Apple Watch 上的應用程式必須：

- **Glanceable** -這表示只要快速概覽，使用者就應該能夠取得所需的資訊。 
- 可**操作**-這表示使用者應該能夠快速做出明智的決策。
- **回應**式-這表示使用者絕對不應等待收到所需的資訊，或達到他們想要的動作。

### <a name="quick-interactions-length"></a>快速互動長度

基於 Apple Watch 應用程式的 glanceable 性質，Apple 建議快速互動的理想長度應為兩秒鐘或更少。 由於這兩個秒的限制，開發人員需要花相當長的時間來設計和執行 Apple Watch 應用程式。 

## <a name="new-watchos-3-features-and-apis"></a>新的 watchOS 3 功能與 Api

Apple 已將數個新功能和 Api 新增至 WatchKit，以協助開發人員新增其 Apple Watch 應用程式的快速互動：

- watchOS 3 提供對新使用者輸入類型的存取，例如：
  - 手勢辨識器
  - Digital Crown 旋轉 
- watchOS 3 提供新的方式來顯示及更新資訊，例如：
  - 增強型資料表流覽
  - 新的使用者通知架構支援
  - SpriteKit 和 SceneKit 整合

藉由實施這些新功能，開發人員可以確保其 watchOS 3 應用程式 Glanceable、可採取動作且回應迅速。

### <a name="gesture-recognizer-support"></a>手勢辨識器支援

如果開發人員已在 iOS 中執行手勢辨識器，則應該非常熟悉手勢辨識器在 watchOS 3 中的工作方式。 若要重新整理，手勢辨識器是將低層級觸控事件剖析成可辨識的預先定義手勢的物件。

watchOS 3 將支援四個下列的手勢辨識器：

- 離散手勢類型：
  - 滑動手勢（`WKSwipeGestureRecognizer`）。
  - 點一下手勢（`WKTapGestureRecognizer`）。
- 連續手勢類型：
  - 平移手勢（`WKPanGestureRecognizer`）。
  - 長按手勢（`WKLongPressGestureRecognizer`）。

若要執行其中一個新的手勢辨識器，只需將它拖曳到 iOS 設計工具的設計介面上 Visual Studio for Mac 並設定其屬性。

在程式碼中，回應辨識器的動作，以處理使用者所觸發的手勢。 同樣地，這會以在 iOS 中處理的相同方式來完成。

#### <a name="discrete-gesture-states"></a>離散手勢狀態

針對離散手勢，當手勢被辨識且狀態（`WKGestureRecognizerState`）指派為時，就會呼叫此動作：

[![](quick-interaction-techniques-images/quick01.png "Discrete Gesture States")](quick-interaction-techniques-images/quick01.png#lightbox)

所有離散手勢會在 `Possible` 狀態開始，並轉換成 `Failed` 或 `Recognized` 狀態。 使用離散手勢時，開發人員通常不會直接處理狀態。 相反地，它們會依賴只辨識手勢時所呼叫的動作。

#### <a name="continuous-gesture-states"></a>連續手勢狀態

連續手勢與離散手勢稍有不同，因為在辨識手勢時，會多次呼叫動作：

[![](quick-interaction-techniques-images/quick02.png "Continuous Gesture States")](quick-interaction-techniques-images/quick02.png#lightbox)

同樣地，連續手勢會在 `Possible` 狀態下開始，但會透過多個更新進行。 在這裡，開發人員必須考慮辨識器的狀態，並在 `Changed` 階段期間更新應用程式的 UI，直到手勢最後 `Recognized` 或 `Canceled`為止。

#### <a name="gesture-recognizer-usage-tips"></a>手勢辨識器使用提示

使用 watchOS 3 中的手勢辨識器時，Apple 建議下列各項：

- 將手勢辨識器新增至群組元素，而不是個別的控制項。 Since the Apple Watch has a smaller physical screen size, Group Elements tend to be bigger and easier targets for the user to hit. Also, the Gesture Recognizers can conflict with built in gestures already in the native UI Controls.
- Set dependency relationships in the watch app's Storyboard.
- Some gesture take precedence over other gesture types, such as:
  - 捲動
  - Force Touch

### <a name="digital-crown-rotation"></a>Digital Crown Rotation

By implementing Digital Crown Support in their watchOS 3 apps, a developer can provide increased navigation speed and precision interactions for their users.

Since watchOS 2, Apple Watch app's could use the `WKInterfacePicker` object to access the Digital Crown by providing a list of `WKPickerItems` and a Picker Style (List, Stacked or Image Sequence). watchOS then allowed the user to use the Digital Crown to select an item from the list.

When using a `WKInterfacePicker`, WatchKit is handling most of the work by:

- Drawing the list and the individual interface elements.
- Processing the Digital Crown events.
- Calling an Action when an item is selected.

New to watchOS 3, the developer now has direct access to the Digital Crown rotation events which allows them to create their own UI elements that respond to rotation values.

Digital Crown access is provided by the following elements:

- `WKCrownSequencer` - Provides access to rotations per second.
- `WKCrownDelegate` - Provides access to rotational delta events.

#### <a name="rotations-per-second"></a>Rotations Per Second

Accessing the Rotations Per Second from the Digital Crown is useful when working with physics based animations. To access the Rotations Per Second, use the `CrownSequencer` property of the `WKInterfaceController` of the Watch Extension. 例如:

```csharp
var rotationsPerSecond = CrownSequencer.RotationsPerSecond;
```

#### <a name="rotational-deltas"></a>Rotational Deltas

Use the Rotational Deltas from the Digital Crown to count the number of rotations. Use the `CrownDidRotate` override method of the `WKCrownDelegate` to access the Rotational Deltas. 例如:

```csharp
using System;
using WatchKit;
using Foundation;

namespace MonkeyWatch.MonkeySeeExtension
{
  public class CrownDelegate : WKCrownDelegate
  {
    #region Computed Properties
    public double AccumulatedRotations { get; set;}
    #endregion

    #region Constructors
    public CrownDelegate ()
    {
    }
    #endregion

    #region Override Methods
    public override void CrownDidRotate (WKCrownSequencer crownSequencer, double rotationalDelta)
    {
      base.CrownDidRotate (crownSequencer, rotationalDelta);

      // Accumulate rotations
      AccumulatedRotations += rotationalDelta;
    }
    #endregion
  }
}
```

Here the app maintains an accumulator (`AccumulatedRotations`) to determine the number of rotations. One full rotation of the Digital Crown is equal to an accumulated delta of `1.0` and a half rotation would be `0.5`.

Apple has left it up to the developer to determine how the rotation counts correspond to the sensitivity of changes on the UI element being updated.

The sign (`+/-`) of the Rotational Delta indicates the direction that the user is turning the Digital Crown:

[![](quick-interaction-techniques-images/quick03.png "The sign of the Rotational Delta indicates the direction that the user is turning the Digital Crown")](quick-interaction-techniques-images/quick03.png#lightbox)

If the user is scrolling up, WatchKit will return positive deltas and if scrolling down, then negative deltas will be returned, no matter what orientation the user is wearing the watch in.

#### <a name="digital-crown-focus"></a>Digital Crown Focus

Just like any other interface elements, the Digital Crown has the concept of Focus. This Focus can be shifted away from the Digital Crown to other interface elements based on how the user is interacting with the watch. 

For example, any of the following controls could steal the Focus of the Digital Crown:

- Picker
- 滑桿
- Scrolling Controller

It is up to the developer to determine when their custom interface element needs to be the Focus of the Digital Crown. Apple suggests using the new Gesture Recognizers to gain focus in the custom UI element.

### <a name="vertical-paging"></a>Vertical Paging

The standard way that a user navigates a Table View in a watchOS app is to scroll to the desired piece of data, tap on a specific row to display the Detailed View, tap the back button when finished viewing the details and repeat the process for any other information that they are interested in from within the table:

[![](quick-interaction-techniques-images/quick04.png "Moving between a table and the Detail view")](quick-interaction-techniques-images/quick04.png#lightbox)

New to watchOS 3, the developer can enable Vertical Paging on their Table View controls. With this feature enabled, the user can scroll to find a Table View row and tap the row to view its detail as before. However, they can now swipe up to select the next row in the table or down to select the previous row (or use the Digital Crown), all without having to return to the Table View first:

[![](quick-interaction-techniques-images/quick05.png "Moving between a table and the Detail view and swiping up and down to move between the other rows")](quick-interaction-techniques-images/quick05.png#lightbox)

To enable this mode, open the watchOS app's Storyboard in Xcode for editing, select the Table View and check the **Vertical Detail Paging** checkbox:

[![](quick-interaction-techniques-images/quick06.png "Check the Vertical Detail Paging checkbox")](quick-interaction-techniques-images/quick06.png#lightbox)

Ensure that the Table is using Segues to display the Detailed View and Save the changes to the Storyboard and return to Visual Studio for Mac to sync.

The developer can programmatically engage Vertical Paging to a specific row using the following code against a Table View:

```csharp
// Segue into Vertical Paging and select the first row
MenuTable.PerformSegue (0);
```

When using Vertical Paging, the developer needs to be aware that WatchKit will automatically handle the preloading of controllers and as a result, some controller lifecycle methods can be called before the UI is actually visible.

### <a name="notification-enhancements"></a>Notification Enhancements

Notification are the primary form of Quick Interaction that a user typically experiences on watchOS and have been available since the first Apple Watch and watchOS 1.

A typical Notification Quick Interaction is as follows:

1. The user feels the Notification Haptic when a new Notification is received.
2. They raise their wrist to see the Short Look interface for the Notification.
3. If they continue to keep their wrist raised, watchOS automatically transitions into the Long Look Notification interface.

There are several ways that a user may respond to the Notification:

- For a well defined and presented Notification, the user will do nothing and simply dismiss the Notification.
- They might also tap of the Notification to launch the watchOS app.
- For a Notification that supports Custom Actions, the user might select one of the custom actions. These can either be:
  - **Foreground Actions** - These launch the app to perform the action.
  - **Background Actions** - Were always routed to the iPhone in watchOS 2 but can be routed to the watchApp in watchOS 3.

New for watchOS 3:

- Notification use a similar API across all platforms (iOS, watchOS, tvOS and macOS).
- Local Notification can be scheduled on the Apple Watch.
- Background Notification will be routed to the app's Extension if they were scheduled on the Apple Watch.

#### <a name="notification-scheduling-and-delivery"></a>Notification Scheduling and Delivery

Notification from the user's iPhone will be forward to the Apple Watch when the following occurs:

- The iPhone's screen is off.
- The Apple Watch is being worn and has been unlocked.

In watchOS 3, Local Notifications can be scheduled on the Apple Watch and are only delivered on the watch. It is up the developer to schedule a corresponding iPhone Notification if it is required by the app.

By including the same Notification Identifier on both the Apple Watch and iPhone versions of the Notifications, it prevents duplicate Notifications from being displayed on the watch. The Apple Watch version of the Notification will take precedence over the iPhone version.

Since watchOS 3 uses the same `UINotification` API framework as iOS 10, please see our iOS 10 [User Notification Framework](~/ios/platform/user-notifications/index.md) documentation for more details.

### <a name="using-spritekit-and-scenekit"></a>Using SpriteKit and SceneKit

New to watchOS 3, the developer can now use both SpritKit and SceneKit objects in their app's User Interface design to present both 2D and 3D graphics.

Two new interface classes have been added to support this feature:

- `WKInterfaceSKScene` - For working with SpriteKit 2D graphics.
- `WKInterfaceSCNScene` - For working with SceneKit 3D graphics.

To use these objects, simply drag them onto the design surface inside of the watch app's Storyboard in Xcode's Interface Builder and use the **Attributes Inspector** to configure them.

From this point, working with either the SpriteKit or SceneKit scenes works the same as it does inside of an iOS app. The watch app will present a `WKInterfaceSKScene` by calling one of the `Present` methods. For SceneKit, simply set the `Scene` property of the `WKInterfaceSCNScene` object.

## <a name="actionable-complications"></a>Actionable Complications

In watchOS 2, Apple introduced Complications for 3rd party apps. In watchOS 3, Apple has expanded the abilities that a developer can include in a WatchKit Complication. 

Additionally, more of the built in watch faces can now include Complications and existing watch faces that already supported Complications can now included even more Complications.

Also new is the ability for a user to quickly swipe left or right to transition through all of the watch faces they have installed on their Apple Watch. Using the new gallery on the Apple Watch's companion iPhone app, the user can add and customize new watch faces and any of the Complications that they can include.

Because of these new features, Apple suggests that every app on Apple Watch should also include at least one Complication and as such, all of the Native Apple Watch app now have Complications.

Complications provide the following features to an app:

- They are highly glanceable since they are always present on the watch face.
- Complications are frequently updated by watchOS. Any app that includes a Complication on the user's currently displayed watch face is updated at least twice an hour.
- Any app with a Complication on the user's currently displayed watch face is kept in memory which makes the app launch quickly and improves the speed of responses from the app.
- Complications make it easy for the user to launch specific functionality in a watchOS app.

## <a name="glanceable-notification"></a>Glanceable Notification

Notification on Apple Watch provide a great, customizable way to quickly inform the user of events or new information such as incoming messages or achieving a goal in a workout app.

By using a Notification, valuable information can be quickly presented to the user. In many situations, a well-designed Notification can remove the necessity for the user to actually launch the app.

New to watchOS 3, all notifications now support:

- SpriteKit
- SceneKit
- Inline Video

## <a name="enhanced-ui-with-spritekit-and-scenekit"></a>Enhanced UI with SpriteKit and SceneKit

Typically, a developer might think of game UI when SpriteKit and SceneKit are mentioned. However, both SpriteKit and SceneKit can be useful for creating non-gaming UIs that include customized layouts, content and animations that are not otherwise possible in WatchKit alone.

For example, a user notification from a photo sharing app can use SpriteKit to provide a rich user experience by including the user that posted the picture along with an actual image and other customized information that enriches the user experience.

Additionally, both SpriteKit and SceneKit can be mixed with standard WatchKit UI elements in the app's User Interface design.

## <a name="simple-navigation"></a>Simple Navigation

watchOS 3 presents several ways that a developer can simplify the navigation within their watchOS apps such as the new [Vertical Paging](#vertical-paging), [Gesture Recognizer Support](#gesture-recognizer-support) and [Digital Crown Rotation](#digital-crown-rotation) features presented above.

The Digital Crown is unique to the Apple Watch and can be used in many different ways to simplify navigation. For example, a timer application can use the Digital Crown to scrub through available timer lengths.

Custom Gestures can present new and unique ways for the user to interact with a watch app and can also be used to simplify app navigation.

Apple suggest looking for ways to combine all of the new Quick Interaction features added in watchOS 3 to present rich, easy and quick to use watchOS app interfaces.

## <a name="finishing-the-quick-interaction"></a>Finishing the Quick Interaction

A well designed quick interaction experience will give the user the confidence to drop their wrist (and disengage with the app) when they have finished the current interaction.

Where this specifically becomes an issue is when the watch app is doing any type of network connection or sharing information with its companion iPhone app. This can often lead to a waiting indicator while the transaction is taking place, which is not desirable during a quick interaction. 請使用以下範例：

[![](quick-interaction-techniques-images/quick07.png "Diagram of the watch app doing a network connection and sharing information with its companion iPhone app")](quick-interaction-techniques-images/quick07.png#lightbox)

1. The user chooses an item to purchase on the watch.
2. They tap the buy button.
3. The app starts the network transaction and displays a loading indicator.
4. Some time later, the transaction completes and the app displays a purchase conformation.
5. The user drops their wrist and disengages with the app.

From the time the user taps the buy button until the transaction is completed, they have their wrist raised looking at a loading indicator. To solve this situation, Apple suggests that the developer should present instant feedback to the user instead of showing a loading indicator. 

Using Apple's suggested model, take a look at the same quick interaction again:

[![](quick-interaction-techniques-images/quick08.png "Apples suggested model diagram")](quick-interaction-techniques-images/quick08.png#lightbox)

1. The user chooses an item to purchase on the watch.
2. They tap the buy button.
3. The app starts the network transaction and displays a message saying that the purchase has successfully started.
4. The user drops their wrist and disengages with the app.
5. When the transaction successfully completes some time later, the app displays a Local Notification to inform the user of a successful purchase.

This time, as soon as the user taps the buy button they are shown a message stating that the purchase has started, so they can confidently drop their wrist and end the quick interaction at this point. Later they are informed of the success or failure of the transaction in a User Notification. In this way, the user is only interacting with the app during the "active" phases of the process.

For apps that are doing networking, they can use a background `NSURLSession` to handle the network communication with a download task. This will allow the app to be woken up in the background to process the downloaded information. For app that require background processing, use a Background Task Assertion to handle the required processing.

## <a name="quick-interaction-design-tips"></a>Quick Interaction Design Tips

Since the desired length of a Quick Interaction is two seconds or less, the developer should focus on the design of the app's interactions from the very beginning of the design process. Find areas where those interactions can be simplified (using the technique presented above) and use the new features of watchOS 3 to make the app quick and responsive.

Apple suggests the following:

- 藉由將應用程式最常使用的功能帶往前，將焦點放在快速互動。
- 使用複雜和使用者通知來呈現常用的功能和功能。
- 使用 SceneKit 和 SpriteKit 建立豐富、glanceable 的使用者介面。
- 請盡可能簡化應用程式內的流覽。
- 絕對不要讓使用者等待，讓他們卸載手腕，並儘快卸載應用程式。

## <a name="summary"></a>總結

本文涵蓋 Apple 已在 watchOS 3 中新增的快速互動技術，以及如何在 Apple Watch 的 Xamarin 中執行。

## <a name="related-links"></a>相關連結

- [watchOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
