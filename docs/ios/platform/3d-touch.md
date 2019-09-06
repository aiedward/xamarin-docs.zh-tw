---
title: Xamarin 中的 3D Touch 簡介
description: 本文說明如何使用 iPhone 6s 和 iPhone 6s Plus 所引進的 3D Touch 手勢。 這些手勢可以區分壓力、查看和 pop，以及快速動作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 46db28dab32f14fa476b9fbb42b788feb669aa74
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291857"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Xamarin 中的 3D Touch 簡介

_本文說明如何在您的應用程式中使用新的 iPhone 6s 和 iPhone 6s Plus 3D Touch 手勢。_

[![](3d-touch-images/info01.png "啟用 3D Touch 的應用程式範例")](3d-touch-images/info01.png#lightbox)

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

[![](3d-touch-images/pressure01.png "根據壓力量，呈現為更半透明或不透明的筆刷筆劃")](3d-touch-images/pressure01.png#lightbox)

由於 3D Touch，如果您的應用程式是在 ios 9 （或更新版本）上執行，而 ios 裝置能夠支援 3D Touch，則壓力的變更將導致`TouchesMoved`引發事件。

例如，監視`TouchesMoved` [UIView](xref:UIKit.UIView)的事件時，您可以使用下列程式碼來取得使用者目前套用至螢幕的壓力：

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

`MaximumPossibleForce` 屬性會根據執行應用程式的 iOS 裝置 , 傳回 [UITouch](xref:UIKit.UITouch) 的`Force`屬性最高的可能值。

> [!IMPORTANT]
> 壓力的變更會導致`TouchesMoved`引發事件，即使 X/Y 座標尚未變更也是一樣。 由於行為的變更，您的 iOS 應用程式應該準備好讓`TouchesMoved`事件被叫用，而 X/Y 座標則與最後一個`TouchesMoved`呼叫相同。




如需詳細資訊，請參閱 Apple [的 TouchCanvas：有效率且有效率](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/)地使用 UITouch 範例應用程式和[UITouch 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>查看和 Pop

3D Touch 提供新的方式，讓使用者在您的應用程式內與資訊互動的速度比以往更快，而不需要從其目前的位置進行流覽。

例如，如果您的應用程式顯示的是訊息的資料表，使用者可以在專案上按下 [hard]，以在重迭視圖中預覽其內容（Apple 指的是「*查看*」）。

[![](3d-touch-images/peekandpop01.png "查看內容的範例")](3d-touch-images/peekandpop01.png#lightbox)

如果使用者按下 [困難]，他們會進入一般的訊息視圖（在視圖中稱為「*快顯視窗*偵測」）。

### <a name="checking-for-3d-touch-availability"></a>檢查 3D Touch 可用性

使用時`UIViewController` ，您可以使用下列程式碼來查看應用程式執行所在的 iOS 裝置是否支援 3D Touch：

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

這個方法可以在之前*或之後* `ViewDidLoad()`呼叫。

### <a name="handling-peek-and-pop"></a>處理查看和 Pop

在可以處理 3D Touch 的 iOS 裝置上，我們可以使用`UIViewControllerPreviewingDelegate`類別的實例來處理**查看**和**Pop**專案詳細資料的顯示。 例如，如果我們有名`MasterViewController`為「資料表視圖控制器」，可以使用下列程式碼來支援**查看**和**Pop**：

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

方法是用來執行查看作業。 `GetViewControllerForPreview` 它會取得資料表資料格和支援資料的存取權，然後`DetailViewController`從目前的腳本載入。 藉由將`PreferredContentSize`設定為（0，0），我們會要求您**提供預設的查看視圖**大小。 最後，我們會模糊所有內容，但我們會以`previewingContext.SourceRect = cell.Frame`顯示的儲存格為您，而我們會傳回新的顯示視圖。

當使用者按下 [困難] 時 ，會 重複使用我們在查看Pop視圖中所`CommitViewController`建立的視圖。

### <a name="registering-for-peek-and-pop"></a>註冊查看和 Pop

從我們**想要允許使用者查看和** **Pop**專案的 View Controller 中，我們需要註冊此服務。 在上述的資料表視圖控制器（`MasterViewController`）的範例中，我們會使用下列程式碼：

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

在這裡，我們會`RegisterForPreviewingWithDelegate`使用上面建立的`PreviewingDelegate`實例來呼叫方法。 在支援 3D Touch 的 iOS 裝置上，使用者可以按下某個專案的硬碟來查看它。 如果他們按較困難，專案將會彈出到 it 標準顯示視圖。

如需詳細資訊，請參閱[iOS 9 ApplicationShortcuts 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)和 Apple 的[ViewControllerPreviews：使用 UIViewController 預覽](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) api 範例應用程式、 [UIPreviewAction 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)、 [UIPreviewActionGroup 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem 通訊協定參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速動作

使用 3D Touch 和快速動作，您可以從 iOS 裝置上的主畫面圖示，為您的應用程式中的函式新增一般、快速且容易存取的快捷方式。

如上所述，您可以將快速動作視為可以在使用者以滑鼠右鍵按一下桌面應用程式中的專案時彈出的內容功能表。 您應該使用快速動作來提供應用程式最常用的功能或功能的快捷方式。

[![](3d-touch-images/quickactions01.png "[快速動作] 功能表的範例")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>定義靜態快速動作

如果您的應用程式所需的一或多個快速動作是靜態的，而且不需要變更，您可以在應用程式的`Info.plist`檔案中定義它們。 在外部編輯器中編輯此檔案，並新增下列索引鍵：

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

  ![](3d-touch-images/uiapplicationshortcuticontype.png "UIApplicationShortcutIconType 影像")

- `UIApplicationShortcutItemSubtitle`-定義專案的子標題。
- `UIApplicationShortcutItemTitle`-定義專案的標題。
- `UIApplicationShortcutItemType`-這是我們將用來識別應用程式中專案的字串值。 如需詳細資訊，請參閱下一節。

> [!IMPORTANT]
> 在檔案中`Info.plist`設定的快速動作快捷方式專案無法`Application.ShortcutItems`使用屬性來存取。 它們只會傳遞至`HandleShortcutItem`事件處理常式。





### <a name="identifying-quick-action-items"></a>識別快速動作專案

如上所示，當您在應用程式的`Info.plist`中定義快速動作專案時，您已將字串值`UIApplicationShortcutItemType`指派給金鑰來識別它們。

若要讓這些識別碼更容易在程式碼中使用，請將`ShortcutIdentifier`名為的類別新增至您的應用程式專案，使其看起來如下所示：

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

接下來，您需要修改應用程式的`AppDelegate.cs`檔案，以處理使用者從主畫面上的應用程式圖示選取快速動作專案。

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

首先，我們會定義一個`LaunchedShortcutItem`公用屬性，以追蹤使用者最後選取的快速動作專案。 然後，我們會覆`FinishedLaunching`寫方法，並`launchOptions`查看是否已傳遞，以及是否包含快速動作專案。 若是如此，我們會在`LaunchedShortcutItem`屬性中儲存快速動作。

接下來，我們會`OnActivated`覆寫方法，並將任何選取的 [ `HandleShortcutItem`快速啟動] 專案傳遞至要處理的方法。 目前我們只會將訊息寫入**主控台**。 在實際的應用程式中，您會處理所需的動作。 採取動作之後， `LaunchedShortcutItem`會清除屬性。

最後，如果您的應用程式已在執行`PerformActionForShortcutItem`中，則會呼叫方法來處理快速動作專案，因此我們需要覆寫它， `HandleShortcutItem`並在這裡呼叫我們的方法。


### <a name="creating-dynamic-quick-action-items"></a>建立動態快速動作專案

除了在應用程式的`Info.plist`檔案中定義靜態快速動作專案之外，您還可以建立動態的快速動作。 若要定義兩個新的動態快速動作`AppDelegate.cs` ，請再次編輯您`FinishedLaunching`的檔案，並修改方法，使其看起來如下所示：

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

現在， `application`我們要檢查是否已包含一組動態建立`ShortcutItems`的，如果不是，我們會建立兩個新`UIMutableApplicationShortcutItem` `ShortcutItems`的物件來定義新的專案，並將它們加入至陣列。

我們已在[處理快速動作](#Handling-a-Quick-Action)一節中新增的程式碼，將會處理這些動態快速動作，就像靜態的一樣。

請注意，您可以同時建立靜態和動態快速動作專案的混合（如同我們在這裡所做的），而不限於其中一種。

如需詳細資訊，請參閱[iOS 9 ViewControllerPreview 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)，並查看[Apple 的 ApplicationShortcuts：使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/)範例應用程式、 [UIApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)、 [UIMutableApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模擬器中測試 3D Touch

在相容的 Mac 上使用最新版本的 Xcode 和 iOS 模擬器搭配 Force Touch 啟用軌跡板時，您可以在模擬器中測試 3D Touch 功能。

若要啟用這項功能，請在支援 3D Touch （iPhone 6s 和更新版本）的模擬 iPhone 硬體中執行任何應用程式。 接下來，選取 iOS 模擬器中的 [**硬體**] 功能表，並啟用 [**使用軌跡板 Force for 3d touch** ] 功能表項目：

[![](3d-touch-images/simulator01.png "選取 iOS 模擬器中的 [硬體] 功能表，並啟用使用軌跡板強制的 3D 觸控功能表項目")](3d-touch-images/simulator01.png#lightbox)

使用此功能時，您可以在 Mac 的軌跡板上按較困難的方式來啟用 3D Touch，就像您在實際的 iPhone 硬體上所做的一樣。

## <a name="summary"></a>總結

本文引進了 iOS 9 中適用于 iPhone 6s 和 iPhone 6s Plus 的新 3D Touch Api。 其中涵蓋增加對應用程式的壓力敏感度;使用 [查看] 和 [Pop] 快速顯示目前內容中的應用程式內資訊，而不需要流覽;並使用快速動作來提供應用程式最常使用的功能的快捷方式。



## <a name="related-links"></a>相關連結

- [iOS 9 ViewControllerPreview 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [iOS 9 ApplicationShortcuts 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [準備好適用于 3D Touch 的 iPhone 應用程式](https://developer.apple.com/ios/3d-touch/)
