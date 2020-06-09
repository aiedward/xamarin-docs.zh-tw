---
title: 建置現代化的 macOS 應用程式
description: 本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技術。
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 13d1709f77b312dbdf357c8ce1871727b2073fef
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574427"
---
# <a name="building-modern-macos-apps"></a>建置現代化的 macOS 應用程式

_本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技術。_

<a name="Building-Modern-Looks-with-Modern-Views"></a>

## <a name="building-modern-looks-with-modern-views"></a>以現代化的觀點打造現代化的外觀

現代化的外觀會包含現代化的視窗和工具列外觀，例如如下所示的範例應用程式：

[![](modern-cocoa-apps-images/content08.png "An example of a modern Mac app UI")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views"></a>

### <a name="enabling-full-sized-content-views"></a>啟用完整大小的內容查看

為了達到此目的，開發人員會想要使用_完整大小的內容視圖_，這表示內容會延伸到工具和標題列區域底下，而且會自動以 macOS 模糊。

若要在程式碼中啟用這項功能，請建立的自訂類別， `NSWindowController` 讓它看起來如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Set window to use Full Size Content View
            Window.StyleMask = NSWindowStyle.FullSizeContentView;
        }
        #endregion
    }
}
```

您也可以在 Xcode 的 Interface Builder 中啟用這項功能，方法是選取視窗並檢查**完整大小的內容視圖**：

[![](modern-cocoa-apps-images/content01.png "Editing the main storyboard in Xcode's Interface Builder")](modern-cocoa-apps-images/content01.png#lightbox)

使用完整大小的內容視圖時，開發人員可能需要將標題和工具列區域底下的內容位移，讓特定內容（例如標籤）不會滑到其下。

若要使這個問題複雜化，標題和工具列區域可以根據使用者目前正在執行的動作、使用者已安裝的 macOS 版本，以及（或）應用程式執行所在的 Mac 硬體而有動態高度。

因此，在配置使用者介面時，只需硬編碼位移就無法使用。 開發人員必須採取動態方法。

Apple 已包含類別的索引[鍵/值可觀察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` 屬性 `NSWindow` ，以取得程式碼中目前的內容區域。 開發人員可以使用此值，在內容區域變更時，手動定位必要的元素。

較佳的解決方案是使用自動設定和大小類別，將 UI 元素放在程式碼或 Interface Builder 中。

如下列範例所示的程式碼，可以用來在應用程式的 View Controller 中使用 [自動調整] 和 [大小] 類別來放置 UI 元素：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        #region Computed Properties
        public NSLayoutConstraint topConstraint { get; set; }
        #endregion

        ...

        #region Override Methods
        public override void UpdateViewConstraints ()
        {
            // Has the constraint already been set?
            if (topConstraint == null) {
                // Get the top anchor point
                var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
                var topAnchor = contentLayoutGuide.TopAnchor;

                // Found?
                if (topAnchor != null) {
                    // Assemble constraint and activate it
                    topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
                    topConstraint.Active = true;
                }
            }

            base.UpdateViewConstraints ();
        }
        #endregion
    }
}
```

這段程式碼會建立將套用至標籤（）之 top 條件約束的儲存體 `ItemTitle` ，以確保它不會在標題和工具列區域下滑動：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

藉由覆寫 View Controller 的 `UpdateViewConstraints` 方法，開發人員可以測試以查看所需的條件約束是否已建立，並在必要時加以建立。

如果需要建立新的條件約束，則 `ContentLayoutGuide` 會存取需要限制之控制項的視窗屬性，並將其轉換為 `NSLayoutGuide` ：

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

會存取的 TopAnchor 屬性， `NSLayoutGuide` 如果有的話，就會用它來建立具有所需位移量的新條件約束，並將新的條件約束設為作用中以套用它：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars"></a>

### <a name="enabling-streamlined-toolbars"></a>啟用簡化的工具列

一般的 macOS 視窗會在執行時包含標準標題列，以及視窗的上邊緣。 如果視窗也包含工具列，它就會顯示在此標題列區域底下：

[![](modern-cocoa-apps-images/content02.png "A standard Mac Toolbar")](modern-cocoa-apps-images/content02.png#lightbox)

使用精簡的工具列時，標題區域會消失，而工具列會以視窗關閉、最小化和最大化按鈕的方式，以內嵌方式移至標題列的位置：

[![](modern-cocoa-apps-images/content03.png "A streamlined Mac Toolbar")](modern-cocoa-apps-images/content03.png#lightbox)

藉由覆寫的方法來啟用簡化的工具列 `ViewWillAppear` `NSViewController` ，使其看起來如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

這種效果通常用於_Shoebox 應用程式_（一個視窗應用程式），例如地圖、行事曆、附注和系統喜好設定。 

<a name="Using-Accessory-View-Controllers"></a>

### <a name="using-accessory-view-controllers"></a>使用附屬視圖控制器

視應用程式的設計而定，開發人員可能也會想要將標題列區域與 [標題]/[工具列] 區域下方出現的 [附屬視圖控制器] 互補，以根據目前參與的活動，為使用者提供內容相關的控制項：

[![](modern-cocoa-apps-images/content04.png "An example Accessory View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

在不需要開發人員介入的情況下，系統會自動將 [配件] 視圖控制器模糊並調整其大小。

若要新增附屬視圖控制器，請執行下列動作：

1. 在方案總管**** 中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
2. 將**自訂視圖控制器**拖曳至視窗的階層： 

    [![](modern-cocoa-apps-images/content05.png "Adding a new Custom View Controller")](modern-cocoa-apps-images/content05.png#lightbox)
3. 配置配件視圖的 UI： 

    [![](modern-cocoa-apps-images/content06.png "Designing the new view")](modern-cocoa-apps-images/content06.png#lightbox)
4. 將 [配件] 視圖公開為 [**插座**]，以及其 UI 的任何其他**動作**或**輸出**： 

    [![](modern-cocoa-apps-images/content07.png "Adding the required OUtlet")](modern-cocoa-apps-images/content07.png#lightbox)
5. 儲存變更。
6. 返回 Visual Studio for Mac 以同步處理變更。

編輯 `NSWindowController` ，使其看起來如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }
        #endregion
    }
}
```

這段程式碼的重點是，View 會設定為在 Interface Builder 中定義的自訂視圖，並公開為**插座**：

```csharp
accessoryView.View = AccessoryViewGoBar;
```

以及定義將會在 `LayoutAttribute` _何處_顯示附件的：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

因為 macOS 現已完全當地語系化，所以 `Left` 和屬性已被取代，而且 `Right` `NSLayoutAttribute` 應該取代為 `Leading` 和 `Trailing` 。

<a name="Using-Tabbed-Windows"></a>

### <a name="using-tabbed-windows"></a>使用索引標籤式視窗

此外，macOS 系統可能會將 [配件] 視圖控制器新增至應用程式的視窗。 例如，若要建立索引標籤式視窗，其中有數個應用程式的視窗會合並成一個虛擬視窗：

[![](modern-cocoa-apps-images/content08.png "An example of a tabbed Mac Window")](modern-cocoa-apps-images/content08.png#lightbox)

一般而言，開發人員必須在其 Xamarin. Mac 應用程式中採取有限的動作，使用索引標籤式視窗，系統會自動處理它們，如下所示：

- 叫用方法時，Windows 會自動進行索引標籤 `OrderFront` 。
- 叫用方法時，將會自動 Untabbed Windows `OrderOut` 。
- 在程式碼中，所有索引標籤式視窗仍會被視為「可見」，不過，系統會使用 CoreGraphics 隱藏任何非最前面的索引標籤。
- 使用的 `TabbingIdentifier` 屬性 `NSWindow` ，將視窗群組在一起成為索引標籤。
- 如果是以為 `NSDocument` 基礎的應用程式，則會自動啟用其中的幾個功能（例如，新增至索引標籤列的加號按鈕），而不需要任何開發人員動作。
- 非 `NSDocument` 基礎的應用程式可以啟用索引標籤群組中的 [加號] 按鈕，藉由覆寫的方法來加入新的檔 `GetNewWindowForTab` `NSWindowsController` 。

將所有元件結合在一起， `AppDelegate` 應用程式的若要使用以系統為基礎的索引標籤式視窗，可能如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacModern
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewDocumentNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom Actions
        [Export ("newDocument:")]
        public void NewDocument (NSObject sender)
        {
            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow (this);
        } 
        #endregion
    }
}
```

其中， `NewDocumentNumber` 屬性會追蹤所建立的新檔數目，而 `NewDocument` 方法會建立新檔並加以顯示。

`NSWindowController`之後可能會如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainWindowController : NSWindowController
    {
        #region Application Access
        /// <summary>
        /// A helper shortcut to the app delegate.
        /// </summary>
        /// <value>The app.</value>
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructor
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void SetDefaultDocumentTitle ()
        {
            // Is this the first document?
            if (App.NewDocumentNumber == 0) {
                // Yes, set title and increment
                Window.Title = "Untitled";
                ++App.NewDocumentNumber;
            } else {
                // No, show title and count
                Window.Title = $"Untitled {App.NewDocumentNumber++}";
            }
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Prefer Tabbed Windows
            Window.TabbingMode = NSWindowTabbingMode.Preferred;
            Window.TabbingIdentifier = "Main";

            // Set default window title
            SetDefaultDocumentTitle ();

            // Set window to use Full Size Content View
            // Window.StyleMask = NSWindowStyle.FullSizeContentView;

            // Create a title bar accessory view controller and attach
            // the view created in Interface Builder
            var accessoryView = new NSTitlebarAccessoryViewController ();
            accessoryView.View = AccessoryViewGoBar;

            // Set the location and attach the accessory view to the
            // titlebar to be displayed
            accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
            Window.AddTitlebarAccessoryViewController (accessoryView);

        }

        public override void GetNewWindowForTab (NSObject sender)
        {
            // Ask app to open a new document window
            App.NewDocument (this);
        }
        #endregion
    }
}
```

其中，靜態 `App` 屬性會提供的快捷方式來取得 `AppDelegate` 。 方法會根據 `SetDefaultDocumentTitle` 所建立的新檔數目來設定新的檔標題。

下列程式碼會告訴 macOS，應用程式偏好使用索引標籤，並提供可讓應用程式的視窗分組為索引標籤的字串：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

而下列覆寫方法會將加號按鈕新增至索引標籤列，當使用者按下時，將會建立新的檔：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation"></a>

### <a name="using-core-animation"></a>使用核心動畫

核心動畫是 macOS 內建的高驅動圖形轉譯引擎。 核心動畫已優化，可利用新式 macOS 硬體中提供的 GPU （圖形處理單元），而不是在 CPU 上執行圖形作業，這可能會使機器速度變慢。

`CALayer`核心動畫提供的可用於快速、流暢的滾動和動畫之類的工作。 應用程式的使用者介面應由多個子檢視和圖層組成，才能充分利用核心動畫。

`CALayer`物件提供數個屬性，可讓開發人員控制使用者在螢幕上呈現的內容，例如：

- `Content`-可以是 `NSImage` `CGImage` 提供圖層內容的或。
- `BackgroundColor`-將圖層的背景色彩設定為`CGColor`
- `BorderWidth`-設定框線寬度。
- `BorderColor`-設定框線色彩。

若要利用應用程式 UI 中的核心圖形，它必須使用 [_圖層_] [受支援的視圖]，Apple 建議開發人員在視窗的內容視圖中一律啟用。 如此一來，所有的子視圖也會自動繼承圖層支援。

此外，Apple 建議使用圖層支援的視圖，而不是將新的設 `CALayer` 為子層，因為系統會自動處理數個必要的設定（例如 Retina 顯示所需的設定）。

若要啟用層級支援，您可以藉由 `WantsLayer` `NSView` `true` 檢查**核心動畫層**，在 [**視圖效果**] 偵測器底下的 Xcode Interface Builder 的中，將設為或內部：

[![](modern-cocoa-apps-images/content09.png "The View Effects Inspector")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers"></a>

#### <a name="redrawing-views-with-layers"></a>以圖層重繪視圖

在 Xamarin. Mac 應用程式中使用分層支援的視圖時，另一個重要的步驟是將的設定為 `LayerContentsRedrawPolicy` `NSView` 中的 `OnSetNeedsDisplay` `NSViewController` 。 例如：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

如果開發人員未設定此屬性，則會在每次其框架來源變更時重新繪製視圖，基於效能考慮，這是不需要的。 當此屬性設定為時， `OnSetNeedsDisplay` 開發人員將會手動將設定 `NeedsDisplay` 為， `true` 以強制重新繪製內容。

當視圖標示為「中途」時，系統會檢查 `WantsUpdateLayer` 視圖的屬性。 如果傳回 `true` `UpdateLayer` ，則會呼叫方法，否則 `DrawRect` 會呼叫 view 的方法來更新視圖的內容。

Apple 有下列建議，可在需要時更新 Views 內容：

- Apple 偏好盡可能 `UpdateLater` 使用 `DrawRect` ，因為它提供顯著的效能提升。
- `layer.Contents`對看起來類似的 UI 元素使用相同的。
- Apple 也偏好讓開發人員使用標準的觀點（例如）撰寫其 UI `NSTextField` 。

若要使用 `UpdateLayer` ，請建立的自訂類別， `NSView` 並讓程式碼看起來如下：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView
    {
        #region Computed Properties
        public override bool WantsLayer {
            get { return true; }
        }

        public override bool WantsUpdateLayer {
            get { return true; }
        }
        #endregion

        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void DrawRect (CoreGraphics.CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

        }

        public override void UpdateLayer ()
        {
            base.UpdateLayer ();

            // Draw view 
            Layer.BackgroundColor = NSColor.Red.CGColor;
        }
        #endregion
    }
}
```

<a name="Using-Modern-Drag-and-Drop"></a>

## <a name="using-modern-drag-and-drop"></a>使用新式拖放

若要為使用者提供現代化的拖放體驗，開發人員應該在其應用程式的拖放作業中採用_拖曳 Flocking_ 。 拖曳 Flocking 是每個個別的檔案或專案一開始就會顯示為個別元素，而該專案會在使用者繼續拖曳作業時，以 flocks （在資料指標底下，並以專案數的計數分組在一起）。

如果使用者終止拖曳作業，則個別元素會 Unflock 並回到其原始位置。

下列程式碼範例會在自訂視圖上啟用拖曳 Flocking：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public partial class MainView : NSView, INSDraggingSource, INSDraggingDestination
    {
        #region Constructor
        public MainView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void MouseDragged (NSEvent theEvent)
        {
            // Create group of string to be dragged
            var string1 = new NSDraggingItem ((NSString)"Item 1");
            var string2 = new NSDraggingItem ((NSString)"Item 2");
            var string3 = new NSDraggingItem ((NSString)"Item 3");

            // Drag a cluster of items
            BeginDraggingSession (new [] { string1, string2, string3 }, theEvent, this);
        }
        #endregion
    }
}
```

Flocking 效果的達成方式是將每個專案拖曳到的 `BeginDraggingSession` 方法，當做 `NSView` 陣列中的個別元素。

使用 `NSTableView` 或時 `NSOutlineView` ，請使用類別的 `PastboardWriterForRow` 方法 `NSTableViewDataSource` 來啟動拖曳作業：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDataSource: NSTableViewDataSource
    {
        #region Constructors
        public ContentsTableDataSource ()
        {
        }
        #endregion

        #region Override Methods
        public override INSPasteboardWriting GetPasteboardWriterForRow (NSTableView tableView, nint row)
        {
            // Return required pasteboard writer
            ...
            
            // Pasteboard writer failed
            return null;
        }
        #endregion
    }
}
```

這可讓開發人員針對要拖曳 `NSDraggingItem` 之資料表中的每個專案，提供個別的個人，而不是將 `WriteRowsWith` 所有資料列當做單一群組寫入至「夾」的舊版方法。

使用時 `NSCollectionViews` ，在開始拖曳時，再次使用 `PasteboardWriterForItemAt` 方法，而不是 `WriteItemsAt` 方法。

開發人員應該一律避免將大型檔案放在存放夾上。 「檔案保證」是 macOS Sierra 的新手，_可讓開發_人員將參考放在貼上，然後在使用者完成使用新的和類別的 Drop 作業時，才會完成此作業 `NSFilePromiseProvider` `NSFilePromiseReceiver` 。

<a name="Using-Modern-Event-Tracking"></a>

## <a name="using-modern-event-tracking"></a>使用新式事件追蹤

對於已經 `NSButton` 加入至標題或工具列區域的使用者介面專案（例如），使用者應該能夠按一下該專案，並讓它像平常一樣引發事件（例如顯示快顯視窗）。 不過，由於專案也是在標題或工具列區域中，因此使用者也應該可以按一下並拖曳元素來移動視窗。

若要在程式碼中完成這項操作，請為專案建立自訂類別（例如 `NSButton` ），並覆寫事件，如下所示 `MouseDown` ：

```csharp
public override void MouseDown (NSEvent theEvent)
{
    var shouldCallSuper = false;

    Window.TrackEventsMatching (NSEventMask.LeftMouseUp, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Handle event as normal
        stop = true;
        shouldCallSuper = true;
    });

    Window.TrackEventsMatching(NSEventMask.LeftMouseDragged, 2000, NSRunLoop.NSRunLoopEventTracking, (NSEvent evt, ref bool stop) => {
        // Pass drag event to window
        stop = true;
        Window.PerformWindowDrag (evt);
    });

    // Call super to handle mousedown
    if (shouldCallSuper) {
        base.MouseDown (theEvent);
    }
}
```

這段程式碼 `TrackEventsMatching` 會使用的方法 `NSWindow` ，將 UI 專案附加來攔截 `LeftMouseUp` 和 `LeftMouseDragged` 事件。 若為 `LeftMouseUp` 事件，UI 元素會以正常方式回應。 若為 `LeftMouseDragged` 事件，事件會傳遞至 `NSWindow` 的方法， `PerformWindowDrag` 以在螢幕上移動視窗。

呼叫 `PerformWindowDrag` 類別的方法 `NSWindow` 可提供下列優點：

- 它允許視窗移動，即使應用程式已停止回應（例如處理深層迴圈時）。
- 空間切換會如預期般運作。
- 空格鍵會顯示為一般。
- 視窗貼齊和對齊會正常運作。

<a name="Using-Modern-Container-View-Controls"></a>

## <a name="using-modern-container-view-controls"></a>使用新式容器視圖控制項

macOS Sierra 為舊版作業系統提供的現有容器視圖控制項提供許多現代化的改良功能。

<a name="Table View Enhancements"></a>

## <a name="table-view-enhancements"></a>資料表視圖的增強功能

開發人員應一律使用新的 `NSView` 容器視圖控制項版本，例如 `NSTableView` 。 例如：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }
        #endregion
    }
}
```

這可讓自訂資料表資料列動作附加至資料表中的指定資料列（例如，輕刷以刪除資料列）。 若要啟用此行為，請覆寫的 `RowActions` 方法 `NSTableViewDelegate` ：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace MacModern
{
    public class ContentsTableDelegate : NSTableViewDelegate
    {
        #region Constructors
        public ContentsTableDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
        {
            // Build new view
            var view = new NSView ();
            ...

            // Return the view representing the item to display
            return view;
        }

        public override NSTableViewRowAction [] RowActions (NSTableView tableView, nint row, NSTableRowActionEdge edge)
        {
            // Take action based on the edge
            if (edge == NSTableRowActionEdge.Trailing) {
                // Create row actions
                var editAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Regular, "Edit", (action, rowNum) => {
                    // Handle row being edited
                    ...
                });

                var deleteAction = NSTableViewRowAction.FromStyle (NSTableViewRowActionStyle.Destructive, "Delete", (action, rowNum) => {
                    // Handle row being deleted
                    ...
                });

                // Return actions
                return new [] { editAction, deleteAction };
            } else {
                // No matching actions
                return null;
            }
        }
        #endregion
    }
}
```

靜態 `NSTableViewRowAction.FromStyle` 用來建立下列樣式的新資料表資料列動作：

- `Regular`-執行標準的非破壞性動作，例如編輯資料列的內容。
- `Destructive`-執行破壞性動作，例如刪除資料表中的資料列。 這些動作將會以紅色背景呈現。

<a name="Scroll-View-Enhancements"></a>

## <a name="scroll-view-enhancements"></a>捲軸視圖增強功能 

當直接使用捲軸（ `NSScrollView` ）或做為另一個控制項的一部分（例如 `NSTableView` ）時，捲軸的內容可以使用現代化的外觀和觀點，在 Xamarin 應用程式的標題和工具列區域下滑動。

因此，[標題] 和 [工具列] 區域可以部分遮蔽 [捲軸] 內容區域中的第一個專案。

為了修正此問題，Apple 已將兩個新的屬性新增至 `NSScrollView` 類別：

- `ContentInsets`-允許開發人員提供物件，以 `NSEdgeInsets` 定義將套用至捲軸頂端的位移。
- `AutomaticallyAdjustsContentInsets`-如果 `true` 捲軸會自動處理 `ContentInsets` 開發人員的。

藉由使用， `ContentInsets` 開發人員可以調整捲軸的起點，以允許包含下列配件：

- 如郵件應用程式中所示的排序指示器。
- 搜尋欄位。
- [重新整理] 或 [更新] 按鈕。

<a name="Auto-Layout-and-Localization-in-Modern-Apps"></a>

## <a name="auto-layout-and-localization-in-modern-apps"></a>現代化應用程式中的自動版面配置和當地語系化

Apple 在 Xcode 中包含數種技術，可讓開發人員輕鬆地建立國際化 macOS 應用程式。 Xcode 現在可讓開發人員在其分鏡腳本檔案中，將使用者面向的文字與應用程式的使用者介面設計分開，並提供在 UI 變更時維護此區隔的工具。

如需詳細資訊，請參閱 Apple 的[國際化和當地語系化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization"></a>

### <a name="implementing-base-internationalization"></a>執行基底國際化 

藉由實行「基底國際化」，開發人員可以提供單一分鏡腳本檔案來代表應用程式的 UI，並將所有的使用者面向字串分開。 

當開發人員建立的初始分鏡腳本檔案（或檔案）定義應用程式的使用者介面時，就會在基底國際化（開發人員所說的語言）中建立。

接下來，開發人員可以匯出當地語系化，以及可轉譯成多種語言的基底國際化字串（在分鏡腳本 UI 設計中）。

之後，您可以匯入這些當地語系化，Xcode 會產生腳本的語言特定字串檔案。

<a name="Implementing-Auto-Layout-to-Support-Localization"></a>

### <a name="implementing-auto-layout-to-support-localization"></a>執行自動版面配置以支援當地語系化

因為當地語系化版本的字串值可能會有極大的大小和（或）讀取方向，所以開發人員應該使用自動設定來定位和調整應用程式使用者介面在分鏡腳本檔案中的大小。

Apple 建議執行下列動作：

- **移除固定寬度條件約束**-所有以文字為基礎的視圖都應該可以根據其內容調整大小。 固定寬度視圖可能會以特定語言裁剪其內容。
- **使用內建內容大小**-根據預設，以文字為基礎的視圖會自動調整以符合其內容。 針對未正確調整大小的以文字為基礎的視圖，請在 Xcode 的 Interface Builder 中選取它們，然後選擇 [**編輯**  >  **大小] 以符合內容**。
- 套用**前置和尾端屬性**-因為文字的方向可以根據使用者的語言而變更，所以請使用 new `Leading` 和 `Trailing` constraint 屬性，而不是現有的 `Right` 和 `Left` 屬性。 `Leading`和 `Trailing` 會根據語言方向自動調整。
- 將**Views 釘選到連續的視圖**-這可讓視圖重新置放和調整大小，以回應所選語言的變更。
- **請勿設定 windows 的最小和/或最大大小**-允許 Windows 變更大小，因為選取的語言會調整其內容區域。
- **經常測試版面配置變更**-應用程式開發期間應該以不同的語言持續測試。 如需詳細資訊，請參閱 Apple 的[測試國際化應用程式](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1)檔。
- **使用 NSStackViews 將 Views 釘選在一起**  -  `NSStackViews`允許其內容以可預測的方式移動並成長，並根據選取的語言來變更內容大小。

<a name="Localizing-in-Xcodes-Interface-Builder"></a>

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的 Interface Builder 中當地語系化

Apple 在 Xcode 的 Interface Builder 中提供了幾項功能，開發人員可以在設計或編輯應用程式的 UI 以支援當地語系化時使用。 **屬性偵測器**的**文字方向**區段，可讓開發人員提供如何在選取的文字型視圖上使用及更新方向的提示（例如 `NSTextField` ）：

[![](modern-cocoa-apps-images/content10.png "The Text Direction options")](modern-cocoa-apps-images/content10.png#lightbox)

**文字方向**有三個可能的值：

- **自然**-版面配置是以指派給控制項的字串為基礎。
- 由**左至右**-版面配置一律會由左至右強制。
- 由**右至左**-版面配置一律由右至左強制。

**版面**配置有兩個可能的值：

- 由**左到右**-版面配置一律由左至右。
- 由**右至左**-版面配置一律由右至左。

除非需要特定的對齊方式，否則通常不應變更。

[**鏡像**] 屬性會告知系統切換特定控制項屬性（例如資料格影像位置）。 它有三個可能的值：

- **自動**-位置會根據選取的語言方向自動變更。
- 從**右至左介面**-位置只會從右至左的語言變更。
- **永不**-位置永遠不會變更。

如果開發人員已指定以文字為基礎之視圖內容的**置**中、**對齊**或**完整**對齊，則永遠都不會根據選取的語言來翻轉。

在 macOS Sierra 之前，程式碼中建立的控制項不會自動進行鏡像。 開發人員必須使用如下所示的程式碼來處理鏡像：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Setting a button's mirroring based on the layout direction
    var button = new NSButton ();
    if (button.UserInterfaceLayoutDirection == NSUserInterfaceLayoutDirection.LeftToRight) {
        button.Alignment = NSTextAlignment.Right;
        button.ImagePosition = NSCellImagePosition.ImageLeft;
    } else {
        button.Alignment = NSTextAlignment.Left;
        button.ImagePosition = NSCellImagePosition.ImageRight;
    }
}
```

，其中 `Alignment` 和 `ImagePosition` 會根據控制項的來設定 `UserInterfaceLayoutDirection` 。

macOS Sierra 加入數個 `CreateButton` 可接受數個參數（例如標題、影像和動作）的新便利函式（透過靜態方法），而且會自動正確地進行鏡像。 例如：

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances"></a>

## <a name="using-system-appearances"></a>使用系統外觀

新式 macOS 應用程式可以採用新的暗介面外觀，適用于映射建立、編輯或展示應用程式：

[![](modern-cocoa-apps-images/content11.png "An example of a dark Mac Window UI")](modern-cocoa-apps-images/content11.png#lightbox)

這可以藉由在顯示視窗之前新增一行程式碼來完成。 例如：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacModern
{
    public partial class ViewController : NSViewController
    {
        ...
    
        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Apply the Dark Interface Appearance
            View.Window.Appearance = NSAppearance.GetAppearance (NSAppearance.NameVibrantDark);

            ...
        }
        #endregion
    }
}
```

類別的靜態 `GetAppearance` 方法 `NSAppearance` 是用來從系統取得名為的外觀（在此案例中為 `NSAppearance.NameVibrantDark` ）。

Apple 具有下列使用系統外觀的建議：

- 偏好使用硬式編碼值（例如 `LabelColor` 和 `SelectedControlColor` ）的命名色彩。
- 可能的話，請使用系統標準控制樣式。

若使用者已從 [系統偏好設定] 應用程式啟用協助工具功能，則使用系統外觀的 macOS 應用程式將會自動正確地運作。 因此，Apple 建議開發人員應該一律在其 macOS 應用程式中使用系統外觀。

<a name="Designing-UIs-with-Storyboards"></a>

## <a name="designing-uis-with-storyboards"></a>使用分鏡腳本設計 Ui

分鏡腳本可讓開發人員不只設計構成應用程式使用者介面的個別元素，還可以視覺化和設計 UI 流程和指定專案的階層。

控制器可讓開發人員將元素收集到組合的單位，並 Segue 抽象，並移除整個視圖階層中移動所需的一般「粘連程式碼」：

[![](modern-cocoa-apps-images/content12.png "Editing the UI in Xcode's Interface Builder")](modern-cocoa-apps-images/content12.png#lightbox)

如需詳細資訊，請參閱我們的分鏡指令檔[簡介](~/mac/platform/storyboards/index.md)。

在多個實例中，腳本中定義的指定場景將需要視圖階層中上一個場景的資料。 Apple 有下列在場景之間傳遞資訊的建議：

- 資料 dependancies 一律會在階層中往下重迭。
- 避免硬式編碼 UI 結構化 dependancies，因為這會限制 UI 彈性。
- 使用 c # 介面來提供一般資料 dependancies。

做為 Segue 來源的視圖控制器可以覆寫 `PrepareForSegue` 方法，並在執行 Segue 以顯示目標視圖控制器之前，執行所需的任何初始化（例如傳遞資料）。 例如：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on Segue ID
    switch (segue.Identifier) {
    case "MyNamedSegue":
        // Prepare for the segue to happen
        ...
        break;
    }
}
```

如需詳細資訊，請參閱我們的[segue](~/mac/platform/storyboards/indepth.md#Segues)檔。

<a name="Propagating-Actions"></a>

## <a name="propagating-actions"></a>傳播動作

根據 macOS 應用程式的設計，有時 UI 控制項上某個動作的最佳處理常式可能位於 UI 階層中的其他位置。 這通常是存在於其本身場景中的功能表和功能表項目，與應用程式的其餘 UI 分開。

若要處理這種情況，開發人員可以建立自訂動作，並將動作傳遞至回應者鏈。 如需詳細資訊，請參閱我們[的使用自訂視窗動作](~/mac/user-interface/menu.md)檔。

<a name="Modern-Mac-Features"></a>

## <a name="modern-mac-features"></a>新式 Mac 功能

Apple 在 macOS Sierra 中加入了數種面向使用者的功能，可讓開發人員充分運用 Mac 平臺，例如：

- **NSUserActivity** -這可讓應用程式描述使用者目前參與的活動。 `NSUserActivity`最初是為了支援遞交而建立的，在其中一個使用者裝置上啟動的活動可以在另一個裝置上繼續進行。 `NSUserActivity`在 macOS 中的運作方式與在 iOS 中相同，因此如需詳細資訊，請參閱提交 iOS 檔[簡介](~/ios/platform/handoff.md)。
- **Mac 上的 Siri** -Siri 會使用目前的活動（ `NSUserActivity` ），為使用者可發出的命令提供內容。
- **狀態還原**-當使用者在 macOS 上結束應用程式，然後在稍後加以 relaunches 時，應用程式會自動回到其先前的狀態。 開發人員可以使用狀態還原 API 來編碼和還原暫時性 UI 狀態，然後才向使用者顯示使用者介面。 如果應用程式是以為 `NSDocument` 基礎，則會自動處理狀態還原。 若要啟用非型應用程式的狀態還原 `NSDocument` ，請將類別的設定 `Restorable` `NSWindow` 為 `true` 。
- **雲端中的檔-在**macOS Sierra 之前，應用程式必須明確選擇使用使用者的 ICloud 磁片磁碟機中的檔。 在 macOS Sierra 使用者的 [**桌面**] 和 [**檔**] 資料夾可能會由系統自動與其 iCloud 磁片磁碟機同步。 因此，可能會刪除檔的本機複本，以釋放使用者電腦上的空間。 `NSDocument`以為基礎的應用程式會自動處理這種變更。 所有其他應用程式類型都必須使用 `NSFileCoordinator` 來同步處理檔的讀取和寫入。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的數個秘訣、功能和技術。

## <a name="related-links"></a>相關連結

- [macOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
