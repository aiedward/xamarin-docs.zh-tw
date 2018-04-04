---
title: 3D Touch 簡介
description: 本文將說明如何使用新 iPhone 6s 和 iPhone 6s Plus 3D 觸控筆勢應用程式中。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a0d89315b82f4931538cdabe64aade7986b2a42e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-3d-touch"></a>3D Touch 簡介

_本文將說明如何使用新 iPhone 6s 和 iPhone 6s Plus 3D 觸控筆勢應用程式中。_

[![](3d-touch-images/info01.png "啟用應用程式的 3D Touch 的範例")](3d-touch-images/info01.png#lightbox)

這篇文章會提供和不足的壓力機密筆勢加入您正在新 iPhone 6s 和 iPhone 6s 的 Xamarin.iOS 應用程式使用新的 3D Touch Api 簡介再加上裝置。

使用 3D Touch iPhone 應用程式現在就可以不只知道使用者觸控裝置的畫面上，但可有意義的使用者 exerting 多少壓力和回應不同的壓力層級。

3D Touch 提供您的應用程式的下列功能：

- [壓力敏感度](#Pressure-Sensitivity)-應用程式現在可以手動測量或 light 使用者接觸該資訊的螢幕，並採取優點。
  例如，繪製應用程式可以進行線條較粗或 thinner 根據使用者已手動觸控螢幕。
- [查看和 Pop](#Peek-and-Pop) -您的應用程式現在可讓使用者與資料互動，而不必巡覽超出其目前的內容。 按下螢幕的螢幕上的硬碟，它們可以查看他們感興趣 （例如預覽訊息） 的項目。 按下更困難，他們可以取出到項目。
- [快速動作](#Quick-Actions)-認為的快速之類的動作，不可以是快顯總當使用者以滑鼠右鍵按一下桌面應用程式中的項目內容功能表。
  使用 快速動作，您可以新增捷徑函式在您的應用程式直接從 首頁 螢幕上的應用程式圖示。
- [在模擬器中測試 3D Touch](#Testing-3D-Touch-in-the-Simulator) -與正確的 Mac 硬體上，您可以在 iOS 模擬器中測試 3D Touch 啟用應用程式。

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>壓力敏感度

如上所述，所使用的新屬性[UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/)類別，您可以衡量的壓力使用者所套用到 iOS 裝置的螢幕大小，並在您的使用者介面中使用這項資訊。 例如，讓筆刷筆觸更半透明或不透明根據數量不足的壓力。

[![](3d-touch-images/pressure01.png "呈現為更半透明或不透明的筆刷筆觸為基礎的數量不足的壓力")](3d-touch-images/pressure01.png#lightbox)

由於 3D Touch，如果您的應用程式正在執行 iOS 9 （或以上） 上，而且能夠支援 3D Touch，iOS 裝置中變更不足的壓力會使`TouchesMoved`會引發事件。

例如，當監視`TouchesMoved`事件[UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)，您可以使用下列程式碼，以取得目前使用者所套用至螢幕的壓力：

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

`MaximumPossibleForce`屬性會傳回最大的可能值為`Force`屬性[UITouch](https://developer.xamarin.com/api/type/UIKit.UITouch/)根據 iOS 裝置上執行應用程式。

> [!IMPORTANT]
> 不足的壓力的變更會導致`TouchesMoved`事件引發，即使 X / Y 座標維持不變。 由於此項變更的行為，您的 iOS 應用程式應該做好準備`TouchesMoved`事件要叫用的頻率和 x / Y 座標，使與上次相同`TouchesMoved`呼叫。




如需詳細資訊，請參閱 Apple [TouchCanvas： 有效率且有效地使用 UITouch](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/)範例應用程式和[UITouch 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>查看和 Pop

3D Touch 提供新的方法，讓使用者以互動比以往，加快應用程式中的資訊，而不需要從其目前所在位置瀏覽。

例如，如果您的應用程式會顯示訊息的表格，使用者可以按硬碟上預覽覆疊的檢視，其內容的項目 (它會做為參考 Apple*查看*)。

[![](3d-touch-images/peekandpop01.png "舉例來說，查看在內容")](3d-touch-images/peekandpop01.png#lightbox)

如果使用者按下更困難，他們將輸入一般訊息檢視 (也就是為*Pop*-ping 到檢視)。

### <a name="checking-for-3d-touch-availability"></a>3D Touch 可用性檢查

當使用[UIViewController]()您可以使用 iOS 裝置執行應用程式是否支援 3D Touch 的下列程式碼：

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

可能之前呼叫此方法*之後或* `ViewDidLoad()`。 

### <a name="handling-peek-and-pop"></a>處理查看和 Pop

在 iOS 裝置可以處理 3D Touch，我們可以使用的執行個體`UIViewControllerPreviewingDelegate`類別來處理的顯示**查看**和**Pop**項目詳細資料。 例如，如果我們在資料表檢視控制器呼叫`MasterViewController `我們可以使用下列程式碼，以支援**查看**和**Pop**:

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

`GetViewControllerForPreview`方法用來執行**查看**作業。 它會取得資料表的資料格及備份資料的存取權，然後再載入`DetailViewController`從目前的分鏡腳本。 藉由設定`PreferredContentSize`我們要求的預設值 (0，0) 到**查看**檢視大小。 最後，我們模糊我們顯示的資料格以外的所有內容`previewingContext.SourceRect = cell.Frame`和我們決定傳回顯示新的檢視。

`CommitViewController`會重複使用我們在建立檢視**查看**如**Pop**時使用者按下不容易檢視。

### <a name="registering-for-peek-and-pop"></a>註冊查看和 Pop

從我們想要允許使用者檢視控制器**查看**和**Pop**中的項目，我們需要此服務註冊。 在範例中上面所列的資料表檢視控制器 (`MasterViewController`)，我們會使用下列程式碼：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Check to see if 3D Touch is available
    if (TraitCollection.ForceTouchCapability == UIForceTouchCapability.Available) {
        // Regiser for Peek and Pop
        RegisterForPreviewingWithDelegate(new PreviewingDelegate(this), View);
    }
    ...

}
```

這裡我們正在撥打`RegisterForPreviewingWithDelegate`方法與執行個體`PreviewingDelegate`前面所建立。 在支援 3D Touch 的 iOS 裝置，使用者可以按硬碟以查看在它的項目。 如果更加按項目會顯示成其標準顯示檢視。

如需詳細資訊，請參閱我們[iOS 9 ApplicationShortcuts 範例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)與 Apple [ViewControllerPreviews： 使用預覽應用程式開發介面 UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html)範例應用程式， [UIPreviewAction 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)， [UIPreviewActionGroup 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem 通訊協定參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速動作

使用 3D 觸碰並快速控制項目，您可以加入常見快速且輕鬆函式的存取捷徑在您的應用程式從 iOS 裝置上的 [首頁] 畫面圖示。

如前所述，您可以將的快速動作類似關聯式功能表，可以是快顯總當使用者以滑鼠右鍵按一下桌面應用程式中的項目。 您應該使用的快速動作，以提供最常見的函數或功能的應用程式的捷徑。

[![](3d-touch-images/quickactions01.png "快速動作 功能表的範例")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>定義靜態的快速動作

如果一或多個應用程式所需的快速動作都是靜態的而且不需要變更，您可以定義它們的應用程式中`Info.plist`檔案。 在 外部編輯器編輯此檔案，並加入下列機碼：

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

這裡我們會定義兩個靜態的快速動作項目具有下列索引鍵：

- `UIApplicationShortcutItemIconType` -定義的圖示，將會快速動作項目顯示為下列值之一：
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

* `UIApplicationShortcutItemSubtitle` -定義項目的子標題。
* `UIApplicationShortcutItemTitle` -定義項目的標題。
* `UIApplicationShortcutItemType` -這是字串值，我們會使用它來識別我們的應用程式中的項目。 如需詳細資訊，請參閱下一節。

> [!IMPORTANT]
> 快速動作捷徑項目中設定的`Info.plist`檔案無法以存取`Application.ShortcutItems`屬性。 它們只會在給`HandleShortcutItem`事件處理常式。 





### <a name="identifying-quick-action-items"></a>識別的快速動作項目

如同上述，當您您快速動作項目中定義您的應用程式`Info.plist`，指派到的字串值`UIApplicationShortcutItemType`識別它們的索引鍵。

若要讓您更輕鬆地在程式碼中使用這些識別項，將呼叫類別加入`ShortcutIdentifier`至您的應用程式專案，並讓它看起來如下所示：

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

### <a name="handling-a-quick-action"></a>處理的快速動作

接下來，您必須修改您的應用程式`AppDelegate.cs`檔，以處理使用者從您的應用程式圖示，在 首頁 螢幕上選取 快速動作項目。

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

首先，我們會定義公用`LaunchedShortcutItem`使用者追蹤最後一個選取的快速動作項目屬性。 然後，我們的覆寫`FinishedLaunching`方法，請參閱如果`launchOptions`已傳遞，而且它們包含的快速動作項目。 如果沒有的話，我們會將儲存中的快速動作`LaunchedShortcutItem`屬性。

接下來，我們會覆寫`OnActivated`方法，然後選取任何快速啟動項目，以傳遞`HandleShortcutItem`方法進行任何動作。 目前我們只會寫入訊息至**主控台**。 在實際的應用程式，您就需要處理的任何動作的必要。 已採取的動作之後，`LaunchedShortcutItem`清除屬性。

最後，如果您的應用程式已在執行，`PerformActionForShortcutItem`方法會呼叫以處理的快速動作項目，因此我們需要覆寫它，然後呼叫我們`HandleShortcutItem`方法以及這裡。


### <a name="creating-dynamic-quick-action-items"></a>建立動態的快速動作項目

除了定義靜態的快速動作項目在您的應用程式中`Info.plist`檔案中，您可以建立動態上作業的快速動作。 若要定義兩個新的動態快速動作，請編輯您`AppDelegate.cs`檔案一次，並修改`FinishedLaunching`方法看起來像下面這樣：

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

現在我們會檢查是否`application`已包含一組動態建立`ShortcutItems`，如果不是，我們將建立兩個新`UIMutableApplicationShortcutItem`物件定義新的項目，並將其新增至`ShortcutItems`陣列。

程式碼我們已加入[處理快速動作](#Handling-a-Quick-Action)上一節將會處理這些動態的快速動作，就像靜態的。

您應該注意，（如同我們這裡），您可以建立混合靜態和動態的快速動作項目，您不一定要將其中一個。

如需詳細資訊，請我們[iOS 9 ViewControllerPreview 範例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)，請參閱 Apple [ApplicationShortcuts： 使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/)範例應用程式， [UIApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)， [UIMutableApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模擬器中測試 3D Touch

當使用最新版本的安裝 Xcode 和 iOS 模擬器與強制觸控相容的 Mac 上啟用 trackpad 時，您可以在模擬器中測試 3D 觸控功能。

若要啟用這項功能，執行任何應用程式中支援 3D Touch 的模擬的 iPhone 硬體 (iPhone 6s 和更新版本)。 接下來，選取**硬體**在 iOS 模擬器和啟用功能表**3D touch 的使用 Trackpad Force**功能表項目：

[![](3d-touch-images/simulator01.png "在 iOS 模擬器中選取 [硬體] 功能表，並啟用 3D touch 功能表項目的使用 Trackpad Force")](3d-touch-images/simulator01.png#lightbox)

使用這項功能，您可以按難以上的 Mac trackpad 啟用 3D Touch 就像真正的 iPhone 硬體。

## <a name="summary"></a>總結

這篇文章已導入新 3D Touch 的 Api 可在 iOS 9 iPhone 6s 和 iPhone 6s Plus。 它涵蓋新增至應用程式; 的壓力敏感度使用 Peek 和 Pop 來快速顯示從目前的內容，而瀏覽; 不在應用程式資訊並提供您的應用程式的捷徑使用提示的快速動作的最常用的功能。



## <a name="related-links"></a>相關連結

- [iOS 9 ViewControllerPreview 範例](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts 範例](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [取得您的 iPhone 應用程式供 3D Touch](https://developer.apple.com/ios/3d-touch/)
