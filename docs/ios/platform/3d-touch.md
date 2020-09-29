---
title: Xamarin 中的 3D Touch 簡介
description: 本文說明如何使用 iPhone 6s 和 iPhone 6s Plus 推出的 3D Touch 手勢。 這些手勢會啟用壓力敏感度、查看和 pop，以及快速動作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6e76821033276ed3bb756cecd10e801fa615fb1e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431842"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>Xamarin 中的 3D Touch 簡介

_本文說明如何在您的應用程式中使用新的 iPhone 6s 和 iPhone 6s 加 3D Touch 手勢。_

[![3D Touch 啟用的應用程式範例](3d-touch-images/info01.png)](3d-touch-images/info01.png#lightbox)

本文將提供和簡介，說明如何使用新的 3D Touch Api，在新的 iPhone 6s 和 iPhone 6s Plus 裝置上執行的 Xamarin iOS 應用程式中新增壓力敏感性手勢。

有了 3D Touch，iPhone 應用程式現在不僅能夠分辨出使用者觸及裝置的畫面，還能判斷使用者造成和回應不同壓力等級的壓力。

3D Touch 為您的應用程式提供下列功能：

- [壓力敏感度](#Pressure-Sensitivity) -應用程式現在可以測量使用者觸及畫面的困難或光線，並利用該資訊。
  例如，繪製應用程式可能會根據使用者觸及螢幕的困難程度，使線條變粗或更細。
- [查看和](#Peek-and-Pop) 快顯-您的應用程式現在可以讓使用者與其資料互動，而不需要流覽至其目前的內容。 藉由在螢幕上按下 [hard] 畫面，他們就可以查看他們感興趣的專案 (例如預覽訊息) 。 藉由按下較困難的專案，就可以將其彈出至專案。
- [快速動作](#Quick-Actions) -在使用者以滑鼠右鍵按一下傳統型應用程式中的專案時，請考慮可彈出的快速動作，例如可彈出的內容功能表。
  您可以使用 [快速動作]，直接從主畫面上的應用程式圖示，將快捷方式新增至應用程式中的函式。
- [在模擬器中測試 3D Touch](#Testing-3D-Touch-in-the-Simulator) -使用正確的 Mac 硬體，您可以在 iOS 模擬器中測試 3D Touch 啟用的應用程式。

<a name="Pressure-Sensitivity"></a>

## <a name="pressure-sensitivity"></a>壓力敏感度

如上所述，藉由使用 [UITouch](xref:UIKit.UITouch) 類別的新屬性，您可以測量使用者套用至 iOS 裝置畫面的壓力，並在您的使用者介面中使用這項資訊。 例如，根據壓力的程度，讓筆刷筆觸更透明或不透明。

[![根據壓力量轉譯為更透明或不透明的筆刷筆劃](3d-touch-images/pressure01.png)](3d-touch-images/pressure01.png#lightbox)

由於 3D Touch，如果您的應用程式在 iOS 9 (或更高) 版本上執行，而且 iOS 裝置能夠支援 3D Touch，則壓力的變更將會 `TouchesMoved` 引發事件。

例如，在監視 UIView 的 `TouchesMoved` 事件時， [UIView](xref:UIKit.UIView)您可以使用下列程式碼來取得使用者在畫面上套用的目前壓力：

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

`MaximumPossibleForce`屬性 `Force` 會根據應用程式執行所在的 iOS 裝置，傳回[UITouch](xref:UIKit.UITouch)屬性的最大可能值。

> [!IMPORTANT]
> 壓力變更將會 `TouchesMoved` 引發事件，即使 X/Y 座標未變更也一樣。 由於這種行為的變更，您的 iOS 應用程式應該準備好讓叫 `TouchesMoved` 用更頻繁的事件，而且 X/Y 座標與最後一個 `TouchesMoved` 呼叫相同。

如需詳細資訊，請參閱 Apple 的 [TouchCanvas：使用 UITouch 有效率且有效的](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/) 範例應用程式和 [UITouch 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop"></a>

## <a name="peek-and-pop"></a>查看和 Pop

3D Touch 提供新的方式，讓使用者在您的應用程式內與您的應用程式互動，而不需要從其目前的位置進行流覽。

例如，如果您的應用程式顯示的是訊息的表格，使用者可以按下某個專案的專案，在重迭視圖中預覽其內容， (Apple 將它視為 *查看*) 。

[![查看內容的範例](3d-touch-images/peekandpop01.png)](3d-touch-images/peekandpop01.png#lightbox)

如果使用者按下較困難的畫面，他們將會進入一般訊息查看 (，這稱為「查看) 的 *快顯視窗*。

### <a name="checking-for-3d-touch-availability"></a>檢查 3D Touch 可用性

使用時， `UIViewController` 您可以使用下列程式碼來查看應用程式執行所在的 iOS 裝置是否支援 3D Touch：

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

您可以在之前 *或之後*呼叫這個方法 `ViewDidLoad()` 。

### <a name="handling-peek-and-pop"></a>處理查看和 Pop

在可以處理 3D Touch 的 iOS 裝置上，我們可以使用類別的實例  `UIViewControllerPreviewingDelegate` 來處理 **查看** 和 **Pop** 專案詳細資料的顯示。 例如，如果我們有一個稱為「資料表視圖控制器」，則 `MasterViewController` 可以使用下列程式碼來支援 **查看** 和 **Pop**：

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

`GetViewControllerForPreview`方法是用來執行**查看**作業。 它會取得資料表資料格和支援資料的存取權，然後 `DetailViewController` 從目前的分鏡腳本載入。 藉由將設定 `PreferredContentSize` 為 (0、0) 我們要求使用預設的**Peek**查看視圖大小。 最後，我們會模糊所有內容，但我們所顯示的儲存格，會傳回 `previewingContext.SourceRect = cell.Frame` 新的 view 來顯示。

`CommitViewController`當使用者按困難時，會重複**Peek**使用我們在 [查看**Pop**視圖] 中所建立的視圖。

### <a name="registering-for-peek-and-pop"></a>註冊查看和 Pop

從我們想要允許使用者**查看****和快**顯專案的 View Controller，我們必須註冊此服務。 在上述的範例中，資料表視圖控制器 (`MasterViewController`) ，我們會使用下列程式碼：

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

在這裡，我們會 `RegisterForPreviewingWithDelegate` 使用先前建立的實例來呼叫方法 `PreviewingDelegate` 。 在支援 3D Touch 的 iOS 裝置上，使用者可以按下某個專案的困難，以查看其內容。 如果他們按了更困難的專案，專案將會進入 [it 標準] 顯示視圖。

如需詳細資訊，請參閱我們的 [iOS 9 ApplicationShortcuts 範例](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) 和 Apple 的 [ViewControllerPreviews：使用 UIViewController 預覽 api](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html) 範例應用程式、 [UIPreviewAction 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)、 [UIPreviewActionGroup 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/) 和 [UIPreviewActionItem 通訊協定參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions"></a>

## <a name="quick-actions"></a>快速動作

使用 3D Touch 和快速動作，您可以從 iOS 裝置上的主畫面圖示，為應用程式中的函式新增常用、快速且容易存取的快捷方式。

如上所述，您可以考慮在使用者以滑鼠右鍵按一下傳統型應用程式中的專案時，可彈出的快速動作，例如內容功能表。 您應該使用快速動作來提供應用程式最常用的功能或功能的快捷方式。

[![快速動作功能表的範例](3d-touch-images/quickactions01.png)](3d-touch-images/quickactions01.png#lightbox)

### <a name="defining-static-quick-actions"></a>定義靜態快速動作

如果您的應用程式所需的一或多個快速動作是靜態的，而且不需要變更，您可以在應用程式的檔案中定義它們 `Info.plist` 。 在外部編輯器中編輯此檔案，並新增下列索引鍵：

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

在這裡，我們會使用下列索引鍵來定義兩個靜態快速動作專案：

- `UIApplicationShortcutItemIconType` -定義將由快速動作專案顯示為下列其中一個值的圖示：
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

  ![UIApplicationShortcutIconType 影像](3d-touch-images/uiapplicationshortcuticontype.png)

- `UIApplicationShortcutItemSubtitle` -定義專案的副標題。
- `UIApplicationShortcutItemTitle` -定義專案的標題。
- `UIApplicationShortcutItemType` -這是我們將用來在應用程式中識別專案的字串值。 如需詳細資訊，請參閱下一節。

> [!IMPORTANT]
> 在檔案中設定的快速動作快捷方式專案， `Info.plist` 無法使用屬性存取 `Application.ShortcutItems` 。 它們只會傳遞至 `HandleShortcutItem` 事件處理常式。

### <a name="identifying-quick-action-items"></a>識別快速動作專案

如您在上面所見，當您在應用程式中定義快速動作專案時 `Info.plist` ，您已將字串值指派給索引 `UIApplicationShortcutItemType` 鍵來識別它們。

若要讓這些識別碼在程式碼中更容易使用，請將呼叫的類別新增 `ShortcutIdentifier` 至您的應用程式專案，使其看起來如下所示：

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

<a name="Handling-a-Quick-Action"></a>

### <a name="handling-a-quick-action"></a>處理快速動作

接下來，您必須修改應用程式的檔案， `AppDelegate.cs` 以處理使用者從主畫面上的應用程式圖示中選取 [快速動作] 專案。

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

首先，我們會定義一個公用 `LaunchedShortcutItem` 屬性，以追蹤使用者最後選取的快速動作專案。 然後，我們會覆寫 `FinishedLaunching` 方法，並查看是否 `launchOptions` 已通過，以及其是否包含快速動作專案。 如果有的話，我們會將快速動作儲存在 `LaunchedShortcutItem` 屬性中。

接下來，我們會覆寫 `OnActivated` 方法，並將任何選取的快速啟動專案傳遞至要處理的 `HandleShortcutItem` 方法。 目前，我們只會將訊息寫入 **主控台**。 在實際的應用程式中，您會處理所需的動作。 採取動作之後， `LaunchedShortcutItem` 就會清除該屬性。

最後，如果您的應用程式已在執行中，則會 `PerformActionForShortcutItem` 呼叫方法來處理快速動作專案，因此我們也需要覆寫它，並在此處呼叫我們的 `HandleShortcutItem` 方法。

### <a name="creating-dynamic-quick-action-items"></a>建立動態快速動作專案

除了在應用程式的檔案中定義靜態快速動作專案以外 `Info.plist` ，您還可以建立動態的即時快速動作。 若要定義兩個新的動態快速動作，請再次編輯您的檔案， `AppDelegate.cs` 然後修改方法，如下 `FinishedLaunching` 所示：

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

現在我們要查看是否 `application` 已包含一組動態建立的 `ShortcutItems` ，如果不是，我們會建立兩個新 `UIMutableApplicationShortcutItem` 的物件來定義新的專案，並將它們新增至 `ShortcutItems` 陣列。

我們已在 [處理快速動作](#Handling-a-Quick-Action) 一節中新增的程式碼將會處理這些動態快速動作，就像靜態的動作一樣。

請注意，您可以同時建立靜態和動態快速動作專案的混合 (，就像我們在這裡所做的一樣) ，您不限於其中一種。

如需詳細資訊，請參閱我們的 [iOS 9 ViewControllerPreview 範例](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview) ，並查看 Apple 的 [ApplicationShortcuts：使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/) 範例應用程式、 [UIApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)、 [UIMutableApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/) 和 [UIApplicationShortcutIcon 類別](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)參考。

<a name="Testing-3D-Touch-in-the-Simulator"></a>

## <a name="testing-3d-touch-in-the-simulator"></a>在模擬器中測試 3D Touch

使用 Force Touch 啟用軌跡板的相容 Mac 上的最新版本 Xcode 和 iOS 模擬器時，您可以在模擬器中測試 3D Touch 功能。

若要啟用這項功能，請在支援 3D Touch (iPhone 6s 和更高) 的模擬 iPhone 硬體中執行任何應用程式。 接下來，選取 iOS 模擬器中的 [ **硬體** ] 功能表，並啟用 [ **將軌跡板 Force 用於3d 觸控** ] 功能表項目：

[![在 iOS 模擬器中選取 [硬體] 功能表，並啟用 [將軌跡板 Force 用於3D 觸控] 功能表項目](3d-touch-images/simulator01.png)](3d-touch-images/simulator01.png#lightbox)

使用這項功能時，您可以按下更難使用 Mac 的軌跡板來啟用 3D Touch，就像在實際的 iPhone 硬體上一樣。

## <a name="summary"></a>摘要

本文引進了在適用于 iPhone 6s 和 iPhone 6s Plus 的 iOS 9 中提供的新 3D Touch Api。 它涵蓋了在應用程式中增加壓力敏感度;使用查看和 Pop 快速顯示目前內容中的應用程式內資訊，而不需要流覽;並使用快速動作來提供應用程式最常使用功能的快捷方式。

## <a name="related-links"></a>相關連結

- [iOS 9 ViewControllerPreview 範例](/samples/xamarin/ios-samples/ios9-viewcontrollerpreview)
- [iOS 9 ApplicationShortcuts 範例](/samples/xamarin/ios-samples/ios9-applicationshortcuts)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [iOS 9。0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [將您的 iPhone 應用程式準備好進行 3D Touch](https://developer.apple.com/ios/3d-touch/)