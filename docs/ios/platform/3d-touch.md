---
title: 在 Xamarin.iOS 中的 3D 觸控簡介
description: 這篇文章描述如何使用 3D 觸控筆勢，引進與 iPhone 6s 和 iPhone 6s Plus。 這些筆勢啟用壓力敏感度、 查看和 pop 和快速動作。
ms.prod: xamarin
ms.assetid: 806D051E-3791-40F7-9776-4E4D3E56F7F3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6a12d157b3de7c3841f5d69d209c01fbc612f79b
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668734"
---
# <a name="introduction-to-3d-touch-in-xamarinios"></a>在 Xamarin.iOS 中的 3D 觸控簡介

_這篇文章將說明如何使用新 iPhone 6s 和 iPhone 6s Plus 3D 觸控筆勢在您的應用程式。_

[![](3d-touch-images/info01.png "啟用應用程式的 3D 觸控的範例")](3d-touch-images/info01.png#lightbox)

這篇文章會提供和簡介使用新的 3D 觸控 Api 將壓力機密筆勢新增至您的 Xamarin.iOS 應用程式執行新的 iphone 6s 和 iPhone 6s Plus 裝置。

使用 3D 觸控，現在就可以不只告訴使用者會觸碰裝置的畫面上，但能夠感知多少使用者 exerting 不足的壓力，以及回應不同的壓力層級的 iPhone 應用程式。

3D Touch 提供您的應用程式的下列功能：

- [壓力敏感度](#Pressure-Sensitivity)-應用程式現在可以測量嚴格程度或淺使用者會觸碰該項資訊的畫面並利用。
  比方說，繪製應用程式可以進行線條較粗或 thinner 根據如何強制使用者會觸碰螢幕。
- [查看和快顯](#Peek-and-Pop)-您的應用程式現在可以讓使用者與資料互動，而不必巡覽到其目前的內容。 藉由按下 固定在螢幕畫面上，它們可以查看他們感興趣 （例如預覽訊息） 的項目。 藉由按下 困難，他們可以取出到項目。
- [快速動作](#Quick-Actions)-認為的快速動作，例如，不可以是快顯總當使用者以滑鼠右鍵按一下桌面應用程式中的項目內容功能表。
  使用 快速動作，您可以新增捷徑函式應用程式中直接從 首頁 畫面上的應用程式圖示。
- [在模擬器中測試 3D 觸控](#Testing-3D-Touch-in-the-Simulator)-與正確的 Mac 硬體上，您可以在 iOS 模擬器中測試 3D 觸控啟用應用程式。

<a name="Pressure-Sensitivity" />

## <a name="pressure-sensitivity"></a>壓力敏感度

如上所述，所使用的新屬性[UITouch](xref:UIKit.UITouch)類別，您可以衡量的使用者所套用到 iOS 裝置螢幕的壓力總量，並在您的使用者介面中使用這項資訊。 比方說，做出的筆刷筆劃更有半透明或不透明的壓力量。

[![](3d-touch-images/pressure01.png "轉譯為更有半透明或不透明的筆刷筆劃為基礎的壓力總量")](3d-touch-images/pressure01.png#lightbox)

3D 觸控，因為在 iOS 9 （或更新版本） 上執行應用程式和 iOS 裝置可以支援的 3D 觸控，如果變更在壓力會導致`TouchesMoved`會引發事件。

例如，當監視`TouchesMoved`事件的[UIView](xref:UIKit.UIView)，您可以使用下列程式碼，以取得使用者所套用到螢幕的目前壓力：

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

`MaximumPossibleForce`屬性會傳回最大的可能值，如`Force`屬性[UITouch](xref:UIKit.UITouch) iOS 裝置上執行的應用程式為基礎。

> [!IMPORTANT]
> 變更在壓力會導致`TouchesMoved`事件引發，即使 X / Y 座標維持不變。 此行為變更，因為您的 iOS 應用程式應該準備`TouchesMoved`事件要叫用的頻率和 x / Y 座標，使與上次相同`TouchesMoved`呼叫。




如需詳細資訊，請參閱 Apple 的[TouchCanvas:有效率且有效地使用 UITouch](https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/)範例應用程式及[UITouch 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/)。

<a name="Peek-and-Pop" />

## <a name="peek-and-pop"></a>查看和 Pop

3D Touch 提供新的方法，讓使用者以互動您比以往更快速的應用程式中的資訊，而不必瀏覽目前所在的位置。

例如，如果您的應用程式會顯示訊息的資料表，使用者可以按硬預覽中的覆疊檢視其內容的項目 (其 Apple 將稱為*查看*)。

[![](3d-touch-images/peekandpop01.png "舉例來說，查看在內容")](3d-touch-images/peekandpop01.png#lightbox)

如果使用者按下更困難，他們將輸入規則的訊息 檢視 (這指*快顯*-偵測到檢視)。

### <a name="checking-for-3d-touch-availability"></a>3D 觸控可用性檢查

當使用`UIViewController`您可以使用下列程式碼執行應用程式的 iOS 裝置是否支援 3D 觸控：

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

之前，可能會呼叫此方法*或之後* `ViewDidLoad()`。

### <a name="handling-peek-and-pop"></a>處理查看和 Pop

在 iOS 裝置可以處理 3D 觸控，我們可以使用的執行個體`UIViewControllerPreviewingDelegate`類別來處理的顯示器**查看**並**快顯**項目詳細資料。 例如，如果我們有一個資料表檢視控制器呼叫`MasterViewController `我們可以使用下列程式碼來支援**查看**並**快顯**:

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

`GetViewControllerForPreview`方法用來執行**查看**作業。 它會取得資料表儲存格和備份資料的存取權，然後載入`DetailViewController`從目前的分鏡腳本。 藉由設定`PreferredContentSize`(0，0) 中，我們正要求的預設值**查看**檢視大小。 最後，我們模糊但我們會使用顯示的資料格的所有項目`previewingContext.SourceRect = cell.Frame`，我們會傳回顯示的新檢視。

`CommitViewController`重複使用我們在建立檢視**查看**如**快顯**檢視時使用者按下更困難。

### <a name="registering-for-peek-and-pop"></a>註冊預覽和 Pop

從我們想要允許使用者檢視控制器**Peek**並**快顯**中的項目，我們需要向此服務。 在上述資料表檢視控制器的範例 (`MasterViewController`)，我們會使用下列程式碼：

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

這裡我們會撥打`RegisterForPreviewingWithDelegate`方法的執行個體`PreviewingDelegate`前面所建立。 在支援 3D Touch 的 iOS 裝置，使用者可以按硬查看它的項目。 如果他們按下更加困難，項目會顯示到標準檢視顯示。

如需詳細資訊，請參閱我們[iOS 9 ApplicationShortcuts 範例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)與 Apple [ViewControllerPreviews:使用預覽 Api UIViewController](https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html)範例應用程式， [UIPreviewAction 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/)， [UIPreviewActionGroup 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/)和[UIPreviewActionItem通訊協定參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/)。

<a name="Quick-Actions" />

## <a name="quick-actions"></a>快速動作

使用 3D 觸控和快速動作，您可以新增常見、 可快速及輕鬆存取捷徑函式應用程式中從 iOS 裝置上的 [首頁] 畫面圖示。

如上所述，您可以快速動作視為關聯式功能表，可以是快顯總當使用者以滑鼠右鍵按一下桌面應用程式中的項目。 您應該使用快速動作，以提供最常見的函式或功能的應用程式的捷徑。

[![](3d-touch-images/quickactions01.png "快速動作 功能表的範例")](3d-touch-images/quickactions01.png#lightbox)


### <a name="defining-static-quick-actions"></a>定義靜態的快速動作

如果一或多個應用程式所需的快速動作都是靜態的而且不需要變更，您可以定義其在應用程式的`Info.plist`檔案。 編輯外部編輯器中的這個檔案並加入下列機碼：

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
* `UIApplicationShortcutItemType` -這是識別應用程式中的項目，我們將使用的字串值。 如需詳細資訊，請參閱下一節。

> [!IMPORTANT]
> 快速動作捷徑項目中所設定的`Info.plist`無法存取檔案，與`Application.ShortcutItems`屬性。 它們只會在給`HandleShortcutItem`事件處理常式。





### <a name="identifying-quick-action-items"></a>找出 快速動作項目

如上所示，當您定義您的快速動作項目中的應用程式`Info.plist`，您指派字串值`UIApplicationShortcutItemType`識別它們的索引鍵。

若要讓這些識別項在程式碼中使用的工作變得更容易，將呼叫類別新增`ShortcutIdentifier`到您的應用程式專案，並讓它看起來如下：

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

接下來，您需要修改您的應用程式`AppDelegate.cs`檔，以處理使用者從您的應用程式圖示，在 首頁 畫面上選取 快速動作項目。

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

首先，我們會定義公用`LaunchedShortcutItem`屬性來追蹤使用者的最後一個選取的快速動作項目。 然後，我們會覆寫`FinishedLaunching`方法，請參閱如果`launchOptions`已傳遞，而且它們包含的快速動作項目。 如果沒有的話，我們會儲存在 快速動作`LaunchedShortcutItem`屬性。

接下來，我們會覆寫`OnActivated`方法，然後選取要快速啟動項目，請將任何傳入`HandleShortcutItem`處理的方法。 目前我們只會寫入訊息至**主控台**。 在實際的應用程式中，您會處理假設任何動作所需。 已採取的動作之後，`LaunchedShortcutItem`清除屬性。

最後，如果您的應用程式已在執行`PerformActionForShortcutItem`會呼叫方法，來處理的快速動作項目，因此我們需要覆寫它，並呼叫我們`HandleShortcutItem`方法以及這裡。


### <a name="creating-dynamic-quick-action-items"></a>建立動態的快速動作項目

除了定義靜態的快速動作項目中的應用程式`Info.plist`檔案中，您可以建立動態上即時快速動作。 若要定義兩個新的動態快速動作，請編輯您`AppDelegate.cs`檔案一次，並修改`FinishedLaunching`方法看起來如下所示：

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

現在我們會查看是否`application`已經包含一組以動態方式建立`ShortcutItems`，如果不是，我們將建立兩個新`UIMutableApplicationShortcutItem`物件來定義新的項目，並將它們新增至`ShortcutItems`陣列。

程式碼我們已新增[處理的快速動作](#Handling-a-Quick-Action)上一節將會處理這些動態的快速動作，就像靜態的。

請注意，（如同我們在這邊），您可以建立混合靜態和動態的快速動作項目，您並不限於其中一種。

如需詳細資訊，請我們[iOS 9 ViewControllerPreview 範例](https://developer.xamarin.com/samples/monotouch/iOS9/ViewControllerPreview/)，請參閱 Apple 的[ApplicationShortcuts:使用 UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/)範例應用程式， [UIApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/)， [UIMutableApplicationShortcutItem 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/)和[UIApplicationShortcutIcon 類別參考](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/)。

<a name="Testing-3D-Touch-in-the-Simulator" />

## <a name="testing-3d-touch-in-the-simulator"></a>在模擬器中測試的 3D 觸控

當使用最新版的 Xcode 和 iOS 模擬器相容在 Mac 上以強制觸控啟用軌跡板時，您可以在模擬器中測試 3D 觸控功能。

若要啟用這項功能，請在模擬的 iPhone 支援硬體的 3D 觸控執行任何應用程式 (iPhone 6s 或更新版本)。 接下來，選取**硬體**iOS 模擬器和啟用功能表**3D 觸控的使用軌跡板強制**功能表項目：

[![](3d-touch-images/simulator01.png "選取 iOS 模擬器中的 [硬體] 功能表，並啟用使用軌跡板強制的 3D 觸控功能表項目")](3d-touch-images/simulator01.png#lightbox)

利用此作用中的功能，您可以按很難在 Mac 的軌跡板啟用 3D 觸控，就如同在實際的 iPhone 的硬體上。

## <a name="summary"></a>總結

這篇文章已導入新 3D Touch Api 可在 iOS 9 iPhone 6s 和 iPhone 6s Plus。 它涵蓋新增至應用程式; 的壓力敏感度使用 Peek 和 Pop 來快速顯示從目前的內容，而不需要瀏覽; 的應用程式資訊並提供您的應用程式的捷徑使用快速動作的最常用的功能。



## <a name="related-links"></a>相關連結

- [iOS 9 ViewControllerPreview 範例](https://developer.xamarin.com/samples/monotouch/ios9/ViewControllerPreview/)
- [iOS 9 ApplicationShortcuts 範例](https://developer.xamarin.com/samples/monotouch/ios9/ApplicationShortcuts/)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [準備好您的 iPhone 應用程式的 3D 觸控](https://developer.apple.com/ios/3d-touch/)
