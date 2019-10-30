---
title: Xamarin 中的 3D Touch 簡介
description: 本文說明如何使用 iPhone 6s 和 iPhone 6s Plus 所引進的 3D Touch 手勢。 這些手勢可以區分壓力、查看和 pop，以及快速動作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d6dec70d30929fdc545bf2ee9107297f1ed8d346
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022254"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Xamarin 中的 3D Touch 簡介

_本文說明如何在您的應用程式中使用新的 iPhone 6s 和 iPhone 6s Plus 3D Touch 手勢。_

[![](3d-touch-images/info01.png "Examples of 3D Touch enabled apps")](3d-touch-images/info01.png#lightbox)

本文將提供使用新的 3D Touch Api，並介紹如何將壓力敏感手勢新增至在新 iPhone 6s 和 iPhone 6s Plus 裝置上執行的您的 Xamarin iOS 應用程式。

有了 3D Touch，iPhone 應用程式現在可以得知使用者是否觸及裝置的螢幕，但是能夠瞭解使用者造成和回應不同壓力等級的壓力程度。

3D Touch 為您的應用程式提供下列功能：

- [壓力敏感度](#Pressure-Sensitivity)-應用程式現在可以測量使用者觸及螢幕的困難或光線，並利用該資訊。
  例如，繪製應用程式可以根據使用者觸及螢幕的程度，使線條變粗或變小。
- [查看和](#Peek-and-Pop)快顯-您的應用程式現在可以讓使用者與資料互動，而不需要流覽其目前的內容。 藉由在螢幕上按下 [hard]，可以查看他們感興趣的專案（例如預覽訊息）。 只要按下 [困難]，他們就可以彈出到專案中。
- [快速動作](#Quick-Actions)-將快速動作視為可在使用者以滑鼠右鍵按一下桌面應用程式中的專案時彈出的內容功能表。
  使用快速動作，您可以直接從主畫面上的應用程式圖示，將快捷方式新增至應用程式中的函式。
- [在模擬器中測試 3D Touch](#Testing-3D-Touch-in-the-Simulator) -使用正確的 Mac 硬體，您可以在 iOS 模擬器中測試已啟用 3D Touch 的應用程式。

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>壓力敏感度

如上所述，藉由使用[UITouch](xref:UIKit.UITouch)類別的新屬性，您可以測量使用者套用至 iOS 裝置畫面的壓力量，並在您的使用者介面中使用此資訊。 例如，根據壓力量，讓筆刷筆劃變得更半透明或不透明。

[![](3d-touch-images/pressure01.png "A brush stroke rendered as more translucent or opaque based on the amount of pressure")](3d-touch-images/pressure01.png#lightbox)

由於 3D Touch，如果您的應用程式是在 iOS 9 （或更新版本）上執行，而 iOS 裝置能夠支援 3D Touch，則壓力的變更將導致引發 `TouchesMoved` 事件。

例如，監視[UIView](xref:UIKit.UIView)的 `TouchesMoved` 事件時，您可以使用下列程式碼來取得使用者目前套用至螢幕的壓力：

```csharp
public override void TouchesMoved (NSSet touches, UIEvent evt)
{
    base.TouchesMoved (touches, evt);
    UITouch touch = touches.AnyObject as UITouch;
    if (touch != null)
    {
        // Get the pressure
        var force = touch.Force;
        var maxForce = touch.MaximumPossibleForce;

        // Do something with the touch and the pressure
        ...
    }
}
```

`MaximumPossibleForce` 屬性會根據執行應用程式的 iOS 裝置，傳回[UITouch](xref:UIKit.UITouch)的 `Force` 屬性最高的可能值。

> [!IMPORTANT]
> 壓力的變更會導致引發 `TouchesMoved` 事件，即使 X/Y 座標尚未變更也是一樣。 由於行為的變更，您的 iOS 應用程式應該準備好讓 `TouchesMoved` 事件被叫用，而 X/Y 座標則與最後一個 `TouchesMoved` 呼叫相同。

如需詳細資訊，請參閱 Apple 的[TouchCanvas：使用 UITouch 有效率且有效率](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/)的範例應用程式和[UITouch 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>查看和 Pop

3D Touch 提供新的方式，讓使用者在您的應用程式內與資訊互動的速度比以往更快，而不需要從其目前的位置進行流覽。

例如，如果您的應用程式顯示的是訊息的資料表，使用者可以在專案上按下 [hard]，以在重迭視圖中預覽其內容（Apple 指的是「*查看*」）。

[![](3d-touch-images/peekandpop01.png "An example of Peeking at content")](3d-touch-images/peekandpop01.png#lightbox)

如果使用者按下 [困難]，他們會進入一般的訊息視圖（在視圖中稱為「*快顯視窗*偵測」）。

### <a name="checking-for-3d-touch-availability"></a>檢查 3D Touch 可用性

使用 `UIViewController` 時，您可以使用下列程式碼來查看應用程式執行所在的 iOS 裝置是否支援 3D Touch：

```csharp
public override void TraitCollectionDidChange(UITraitCollection previousTraitCollection)
{
    //Important: call the base function
    base.TraitCollectionDidChange(previousTraitCollection);

    //See if the new TraitCollection value includes force touch
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        //Do something with 3D touch, for instance...
        RegisterForPreviewingWithDelegate (this, View);
        ...
```

在 `ViewDidLoad()`之前*或之後*，都可以呼叫這個方法。

### <a name="handling-peek-and-pop"></a>處理查看和 Pop

在可以處理 3D Touch 的 iOS 裝置上，我們可以使用 `UIViewControllerPreviewingDelegate` 類別的實例來處理**查看**和**Pop**專案詳細資料的顯示。 例如，如果我們有一個稱為的資料表視圖控制器 `MasterViewController` 我們可以使用下列程式碼來支援**查看**和**Pop**：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace DTouch
{
    public class PreviewingDelegate : UIViewControllerPreviewingDelegate
    {
        #region Computed Properties
        public MasterViewController MasterController { get; set; }
        #endregion

        #region Constructors
        public PreviewingDelegate (MasterViewController masterController)
        {
            // Initialize
            this.MasterController = masterController;
        }

        public PreviewingDelegate (NSObjectFlag t) : base(t)
        {
        }

        public PreviewingDelegate (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        /// Present the view controller for the "Pop" action.
        public override void CommitViewController (IUIViewControllerPreviewing previewingContext, UIViewController viewControllerToCommit)
        {
            // Reuse Peek view controller for details presentation
            MasterController.ShowViewController(viewControllerToCommit,this);
        }

        /// Create a previewing view controller to be shown at "Peek".
        public override UIViewController GetViewControllerForPreview (IUIViewControllerPreviewing previewingContext, CGPoint location)
        {
            // Grab the item to preview
            var indexPath = MasterController.TableView.IndexPathForRowAtPoint (location);
            var cell = MasterController.TableView.CellAt (indexPath);
            var item = MasterController.dataSource.Objects [indexPath.Row];

            // Grab a controller and set it to the default sizes
            var detailViewController = MasterController.Storyboard.InstantiateViewController ("DetailViewController") as DetailViewController;
            detailViewController.PreferredContentSize = new CGSize (0, 0);

            // Set the data for the display
            detailViewController.SetDetailItem (item);
            detailViewController.NavigationItem.LeftBarButtonItem = MasterController.SplitViewController.DisplayModeButtonItem;
            detailViewController.NavigationItem.LeftItemsSupplementBackButton = true;

            // Set the source rect to the cell frame, so everything else is blurred.
            previewingContext.SourceRect = cell.Frame;

            return detailViewController;
        }
        #endregion
    }
}
```

`GetViewControllerForPreview` 方法是用來執行**查看**作業。 它會取得資料表資料格和支援資料的存取權，然後從目前的腳本載入 `DetailViewController`。 藉由將 `PreferredContentSize` 設定為（0，0），我們會要求您**提供預設的查看視圖**大小。 最後，我們會模糊所有內容，但我們顯示的是 `previewingContext.SourceRect = cell.Frame` 的資料格，而我們會傳回新的顯示視圖。

當使用者按下 [困難] 時，`CommitViewController` 會重複使用我們**在查看** **Pop**視圖中所建立的視圖。

### <a name="registering-for-peek-and-pop"></a>註冊查看和 Pop

從我們**想要允許使用者查看和** **Pop**專案的 View Controller 中，我們需要註冊此服務。 在上述的資料表視圖控制器（`MasterViewController`）範例中，我們會使用下列程式碼：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Register for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

在這裡，我們會使用上面建立的 `PreviewingDelegate` 實例來呼叫 `RegisterForPreviewingWithDelegate` 方法。 在支援 3D Touch 的 iOS 裝置上，使用者可以按下某個專案的硬碟來查看它。 如果他們按較困難，專案將會彈出到 it 標準顯示視圖。

如需詳細資訊，請參閱[iOS 9 ApplicationShortcuts 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)和 Apple 的[ViewControllerPreviews：使用 UIViewController 預覽 api](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html)範例應用程式、 [UIPreviewAction 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)、 [UIPreviewActionGroup類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem 通訊協定參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速動作

使用 3D Touch 和快速動作，您可以從 iOS 裝置上的主畫面圖示，為您的應用程式中的函式新增一般、快速且容易存取的快捷方式。

如上所述，您可以將快速動作視為可以在使用者以滑鼠右鍵按一下桌面應用程式中的專案時彈出的內容功能表。 您應該使用快速動作來提供應用程式最常用的功能或功能的快捷方式。

[![](3d-touch-images/quickactions01.png "An example of a Quick Actions menu")](3d-touch-images/quickactions01.png#lightbox)

### <a name="defining-static-quick-actions"></a>定義靜態快速動作

如果您的應用程式所需的一或多個快速動作是靜態的，而且不需要變更，您可以在應用程式的 `Info.plist` 檔中定義它們。 在外部編輯器中編輯此檔案，並新增下列索引鍵：

```xml
<key>UIApplicationShortcutItems</key>
<array>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeSearch</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will search for an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Search</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.000</string>
    </dict>
    <dict>
        <key>UIApplicationShortcutItemIconType</key>
        <string>UIApplicationShortcutIconTypeShare</string>
        <key>UIApplicationShortcutItemSubtitle</key>
        <string>Will share an item</string>
        <key>UIApplicationShortcutItemTitle</key>
        <string>Share</string>
        <key>UIApplicationShortcutItemType</key>
        <string>com.company.appname.001</string>
    </dict>
</array>
```

在這裡，我們將使用下列索引鍵來定義兩個靜態快速動作專案：

- `UIApplicationShortcutItemIconType`-定義快速動作專案將顯示為下列其中一個值的圖示：
  - `UIApplicationShortcutIconTypeAdd`
  - `UIApplicationShortcutIconTypeAlarm`
  - `UIApplicationShortcutIconTypeAudio`
  - `UIApplicationShortcutIconTypeBookmark`
  - `UIApplicationShortcutIconTypeCapturePhoto`
  - `UIApplicationShortcutIconTypeCaptureVideo`
  - `UIApplicationShortcutIconTypeCloud`
  - `UIApplicationShortcutIconTypeCompose`
  - `UIApplicationShortcutIconTypeConfirmation`
  - `UIApplicationShortcutIconTypeContact`
  - `UIApplicationShortcutIconTypeDate`
  - `UIApplicationShortcutIconTypeFavorite`
  - `UIApplicationShortcutIconTypeHome`
  - `UIApplicationShortcutIconTypeInvitation`
  - `UIApplicationShortcutIconTypeLocation`
  - `UIApplicationShortcutIconTypeLove`
  - `UIApplicationShortcutIconTypeMail`
  - `UIApplicationShortcutIconTypeMarkLocation`
  - `UIApplicationShortcutIconTypeMessage`
  - `UIApplicationShortcutIconTypePause`
  - `UIApplicationShortcutIconTypePlay`
  - `UIApplicationShortcutIconTypeProhibit`
  - `UIApplicationShortcutIconTypeSearch`
  - `UIApplicationShortcutIconTypeShare`
  - `UIApplicationShortcutIconTypeShuffle`
  - `UIApplicationShortcutIconTypeTask`
  - `UIApplicationShortcutIconTypeTaskCompleted`
  - `UIApplicationShortcutIconTypeTime`
  - `UIApplicationShortcutIconTypeUpdate`

  ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType imagery")

- `UIApplicationShortcutItemSubtitle`-定義專案的子標題。
- `UIApplicationShortcutItemTitle`-定義專案的標題。
- `UIApplicationShortcutItemType`-這是我們將用來在應用程式中識別專案的字串值。 如需詳細資訊，請參閱下一節。

> [!IMPORTANT]
> 無法使用 `Application.ShortcutItems` 屬性來存取 `Info.plist` 檔案中設定的快速動作快捷方式專案。 它們只會傳入 `HandleShortcutItem` 事件處理常式。

### <a name="identifying-quick-action-items"></a>識別快速動作專案

如您在上面所見，當您在應用程式的 `Info.plist`中定義快速動作專案時，您已將字串值指派給 `UIApplicationShortcutItemType` 金鑰來識別它們。

若要讓這些識別碼更容易在程式碼中使用，請將名為 `ShortcutIdentifier` 的類別新增至您的應用程式專案，使其看起來如下所示：

```csharp
using System;

namespace AppSearch
{
    public static class ShortcutIdentifier
    {
        public const string First = "com.company.appname.000";
        public const string Second = "com.company.appname.001";
        public const string Third = "com.company.appname.002";
        public const string Fourth = "com.company.appname.003";
    }
}
```

<a name="Handling-a-Quick-Action" />

### <a name="handling-a-quick-action"></a>處理快速動作

接下來，您必須修改應用程式的 `AppDelegate.cs` 檔，以處理使用者從主畫面上的應用程式圖示選取快速動作專案。

進行下列編輯：

```csharp
using System;
...

public UIApplicationShortcutItem LaunchedShortcutItem { get; set; }

public bool HandleShortcutItem(UIApplicationShortcutItem shortcutItem) {
    var handled = false;

    // Anything to process?
    if (shortcutItem == null) return false;

    // Take action based on the shortcut type
    switch (shortcutItem.Type) {
    case ShortcutIdentifier.First:
        Console.WriteLine ("First shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Second:
        Console.WriteLine ("Second shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Third:
        Console.WriteLine ("Third shortcut selected");
        handled = true;
        break;
    case ShortcutIdentifier.Fourth:
        Console.WriteLine ("Forth shortcut selected");
        handled = true;
        break;
    }

    // Return results
    return handled;
}

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    return shouldPerformAdditionalDelegateHandling;
}

public override void OnActivated (UIApplication application)
{
    // Handle any shortcut item being selected
    HandleShortcutItem(LaunchedShortcutItem);

    // Clear shortcut after it's been handled
    LaunchedShortcutItem = null;
}

public override void PerformActionForShortcutItem (UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
{
    // Perform action
    completionHandler(HandleShortcutItem(shortcutItem));
}
```

首先，我們會定義一個公用 `LaunchedShortcutItem` 屬性，以追蹤使用者最後選取的快速動作專案。 然後，我們會覆寫 `FinishedLaunching` 方法，並查看是否已傳遞 `launchOptions`，以及是否包含快速動作專案。 若是如此，我們會在 `LaunchedShortcutItem` 屬性中儲存快速動作。

接下來，我們會覆寫 `OnActivated` 方法，並將任何選取的 [快速啟動] 專案傳遞至要處理的 `HandleShortcutItem` 方法。 目前我們只會將訊息寫入**主控台**。 在實際的應用程式中，您會處理所需的動作。 採取動作之後，會清除 [`LaunchedShortcutItem`] 屬性。

最後，如果您的應用程式已在執行中，則會呼叫 `PerformActionForShortcutItem` 方法來處理快速動作專案，因此我們也需要覆寫它，並在這裡呼叫我們的 `HandleShortcutItem` 方法。

### <a name="creating-dynamic-quick-action-items"></a>建立動態快速動作專案

除了在應用程式的 `Info.plist` 檔中定義靜態快速動作專案之外，您還可以建立動態的快速動作。 若要定義兩個新的動態快速動作，請再次編輯您的 `AppDelegate.cs` 檔案，並修改 `FinishedLaunching` 的方法，如下所示：

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    var shouldPerformAdditionalDelegateHandling = true;

    // Get possible shortcut item
    if (launchOptions != null) {
        LaunchedShortcutItem = launchOptions [UIApplication.LaunchOptionsShortcutItemKey] as UIApplicationShortcutItem;
        shouldPerformAdditionalDelegateHandling = (LaunchedShortcutItem == null);
    }

    // Add dynamic shortcut items
    if (application.ShortcutItems.Length == 0) {
        var shortcut3 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Third, "Play") {
            LocalizedSubtitle = "Will play an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Play)
        };

        var shortcut4 = new UIMutableApplicationShortcutItem (ShortcutIdentifier.Fourth, "Pause") {
            LocalizedSubtitle = "Will pause an item",
            Icon = UIApplicationShortcutIcon.FromType(UIApplicationShortcutIconType.Pause)
        };

        // Update the application providing the initial 'dynamic' shortcut items.
        application.ShortcutItems = new UIApplicationShortcutItem[]{shortcut3, shortcut4};
    }

    return shouldPerformAdditionalDelegateHandling;
}
```

現在，我們要檢查 `application` 是否已包含一組動態建立的 `ShortcutItems`，如果不是，我們會建立兩個新的 `UIMutableApplicationShortcutItem` 物件來定義新的專案，並將它們加入至 `ShortcutItems` 陣列。

我們已在[處理快速動作](#Handling-a-Quick-Action)一節中新增的程式碼，將會處理這些動態快速動作，就像靜態的一樣。

請注意，您可以同時建立靜態和動態快速動作專案的混合（如同我們在這裡所做的），而不限於其中一種。

如需詳細資訊，請參閱[iOS 9 ViewControllerPreview 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)和 Apple 的[ApplicationShortcuts：使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/)範例應用程式， [UIApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)， [UIMutableApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模擬器中測試 3D Touch

在相容的 Mac 上使用最新版本的 Xcode 和 iOS 模擬器搭配 Force Touch 啟用軌跡板時，您可以在模擬器中測試 3D Touch 功能。

若要啟用這項功能，請在支援 3D Touch （iPhone 6s 和更新版本）的模擬 iPhone 硬體中執行任何應用程式。 接下來，選取 iOS 模擬器中的 [**硬體**] 功能表，並啟用 [**使用軌跡板 Force for 3d touch** ] 功能表項目：

[![](3d-touch-images/simulator01.png "Select the Hardware menu in the iOS Simulator and enable the Use Trackpad Force for 3D touch menu item")](3d-touch-images/simulator01.png#lightbox)

使用此功能時，您可以在 Mac 的軌跡板上按較困難的方式來啟用 3D Touch，就像您在實際的 iPhone 硬體上所做的一樣。

## <a name="summary"></a>總結

本文引進了 iOS 9 中適用于 iPhone 6s 和 iPhone 6s Plus 的新 3D Touch Api。 其中涵蓋增加對應用程式的壓力敏感度;使用 [查看] 和 [Pop] 快速顯示目前內容中的應用程式內資訊，而不需要流覽;並使用快速動作來提供應用程式最常使用的功能的快捷方式。

## <a name="related-links"></a>相關連結

- [iOS 9 ViewControllerPreview 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [iOS 9 ApplicationShortcuts 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [準備好適用于 3D Touch 的 iPhone 應用程式](https://developer.apple.com/ios/3d-touch/)
