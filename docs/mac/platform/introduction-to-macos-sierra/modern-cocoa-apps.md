---
title: 建置現代化的 macOS 應用程式
description: 本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技術。
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 0ddf6cfc26e811505a50c2d89596f830658f0c1d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029899"
---
# <a name="building-modern-macos-apps"></a>建置現代化的 macOS 應用程式

_本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技術。_

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>以現代化的觀點打造現代化的外觀

現代化的外觀會包含現代化的視窗和工具列外觀，例如如下所示的範例應用程式：

[![](modern-cocoa-apps-images/content08.png "An example of a modern Mac app UI")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>啟用完整大小的內容查看

為了達到此目的，開發人員會想要使用_完整大小的內容視圖_，這表示內容會延伸到工具和標題列區域底下，而且會自動以 macOS 模糊。

若要在程式碼中啟用這項功能，請建立 `NSWindowController` 的自訂類別，讓它看起來如下所示：

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

Apple 已包含 `NSWindow` 類別的索引[鍵/值可觀察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes)`ContentLayoutRect` 屬性，以取得程式碼中目前的內容區域。 開發人員可以使用此值，在內容區域變更時，手動定位必要的元素。

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

此程式碼會建立將套用至標籤（`ItemTitle`）之 top 條件約束的儲存體，以確保它不會在標題和工具列區域底下：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

藉由覆寫 View Controller 的 `UpdateViewConstraints` 方法，開發人員可以測試以查看所需的條件約束是否已建立，並在必要時加以建立。

如果需要建立新的條件約束，則會存取需要限制的控制項之視窗的 `ContentLayoutGuide` 屬性，並將其轉換成 `NSLayoutGuide`：

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

系統會存取 `NSLayoutGuide` 的 TopAnchor 屬性，如果有的話，就會使用它來建立具有所需位移量的新條件約束，並將新的條件約束設為作用中以套用它：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>啟用簡化的工具列

一般的 macOS 視窗會在執行時包含標準標題列，以及視窗的上邊緣。 如果視窗也包含工具列，它就會顯示在此標題列區域底下：

[![](modern-cocoa-apps-images/content02.png "A standard Mac Toolbar")](modern-cocoa-apps-images/content02.png#lightbox)

使用精簡的工具列時，標題區域會消失，而工具列會以視窗關閉、最小化和最大化按鈕的方式，以內嵌方式移至標題列的位置：

[![](modern-cocoa-apps-images/content03.png "A streamlined Mac Toolbar")](modern-cocoa-apps-images/content03.png#lightbox)

簡化的工具列是藉由覆寫 `NSViewController` 的 `ViewWillAppear` 方法來啟用，使其看起來如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

這種效果通常用於_Shoebox 應用程式_（一個視窗應用程式），例如地圖、行事曆、附注和系統喜好設定。 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>使用附屬視圖控制器

視應用程式的設計而定，開發人員可能也會想要將標題列區域與 [標題]/[工具列] 區域下方出現的 [附屬視圖控制器] 互補，以根據他們的活動提供內容相關的控制項給使用者目前參與：

[![](modern-cocoa-apps-images/content04.png "An example Accessory View Controller")](modern-cocoa-apps-images/content04.png#lightbox)

在不需要開發人員介入的情況下，系統會自動將 [配件] 視圖控制器模糊並調整其大小。

若要新增附屬視圖控制器，請執行下列動作：

1. 在方案總管中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
2. 將**自訂視圖控制器**拖曳至視窗的階層： 

    [![](modern-cocoa-apps-images/content05.png "Adding a new Custom View Controller")](modern-cocoa-apps-images/content05.png#lightbox)
3. 配置配件視圖的 UI： 

    [![](modern-cocoa-apps-images/content06.png "Designing the new view")](modern-cocoa-apps-images/content06.png#lightbox)
4. 將 [配件] 視圖公開為 [**插座**]，以及其 UI 的任何其他**動作**或**輸出**： 

    [![](modern-cocoa-apps-images/content07.png "Adding the required OUtlet")](modern-cocoa-apps-images/content07.png#lightbox)
5. 儲存變更。
6. 返回 Visual Studio for Mac 以同步處理變更。

編輯 `NSWindowController`，讓它看起來如下所示：

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

以及定義將會在_何處_顯示附件的 `LayoutAttribute`：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

由於 macOS 現在已完全當地語系化，`Left` 和 `Right` `NSLayoutAttribute` 屬性已被取代，應取代為 `Leading` 和 `Trailing`。

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>使用索引標籤式視窗

此外，macOS 系統可能會將 [配件] 視圖控制器新增至應用程式的視窗。 例如，若要建立索引標籤式視窗，其中有數個應用程式的視窗會合並成一個虛擬視窗：

[![](modern-cocoa-apps-images/content08.png "An example of a tabbed Mac Window")](modern-cocoa-apps-images/content08.png#lightbox)

一般而言，開發人員必須在其 Xamarin. Mac 應用程式中採取有限的動作，使用索引標籤式視窗，系統會自動處理它們，如下所示：

- 當叫用 `OrderFront` 方法時，Windows 會自動進行索引標籤。
- 叫用 `OrderOut` 方法時，將會自動 Untabbed Windows。
- 在程式碼中，所有索引標籤式視窗仍會被視為「可見」，不過，系統會使用 CoreGraphics 隱藏任何非最前面的索引標籤。
- 使用 `NSWindow` 的 [`TabbingIdentifier`] 屬性，將視窗群組在一起成為索引標籤。
- 如果它是以 `NSDocument` 為基礎的應用程式，則會自動啟用其中幾項功能（例如新增至索引標籤列的加號按鈕），而不需要任何開發人員動作。
- 非以`NSDocument` 為基礎的應用程式可以啟用索引標籤群組中的 [加號] 按鈕，藉由覆寫 `NSWindowsController`的 `GetNewWindowForTab` 方法來加入新的檔。

將所有元件結合在一起，想要使用以系統為基礎的索引標籤式視窗的應用程式 `AppDelegate` 可能如下所示：

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

其中 `NewDocumentNumber` 屬性會追蹤所建立的新檔數目，而 `NewDocument` 方法會建立新的檔並加以顯示。

`NSWindowController` 可能如下所示：

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

其中，static `App` 屬性會提供一個快捷方式來到達 `AppDelegate`。 `SetDefaultDocumentTitle` 方法會根據新建立的檔數目來設定新的檔標題。

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

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>使用核心動畫

核心動畫是 macOS 內建的高驅動圖形轉譯引擎。 核心動畫已優化，可利用新式 macOS 硬體中提供的 GPU （圖形處理單元），而不是在 CPU 上執行圖形作業，這可能會使機器速度變慢。

核心動畫提供的 `CALayer`可以用於快速、流暢的滾動和動畫等工作。 應用程式的使用者介面應由多個子檢視和圖層組成，才能充分利用核心動畫。

`CALayer` 物件提供數個屬性，可讓開發人員控制使用者在螢幕上呈現的內容，例如：

- `Content`-可以是提供圖層內容的 `NSImage` 或 `CGImage`。
- `BackgroundColor`-將圖層的背景色彩設定為 `CGColor`
- `BorderWidth`-設定框線寬度。
- `BorderColor`-設定框線色彩。

若要利用應用程式 UI 中的核心圖形，它必須使用 [_圖層_] [受支援的視圖]，Apple 建議開發人員在視窗的內容視圖中一律啟用。 如此一來，所有的子視圖也會自動繼承圖層支援。

此外，Apple 建議使用圖層支援的視圖，而不是將新的 `CALayer` 做為子層級，因為系統會自動處理數個必要的設定（例如 Retina 顯示所需的設定）。

藉由檢查**核心動畫層**，將 `NSView` 的 `WantsLayer` 設定為 [**觀看效果**] 偵測器底下的 [Xcode] Interface Builder 中的 `true` 或內部，可以啟用圖層支援：

[![](modern-cocoa-apps-images/content09.png "The View Effects Inspector")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>以圖層重繪視圖

在 Xamarin. Mac 應用程式中使用圖層支援的視圖時，另一個重要的步驟是將 `NSView` 的 `LayerContentsRedrawPolicy` 設定為 `NSViewController`中的 `OnSetNeedsDisplay`。 例如:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

如果開發人員未設定此屬性，則會在每次其框架來源變更時重新繪製視圖，基於效能考慮，這是不需要的。 當此屬性設定為 `OnSetNeedsDisplay`，開發人員就必須手動將 `NeedsDisplay` 設定為 `true`，以強制重新繪製內容。

當視圖標示為 [中途] 時，系統會檢查視圖的 [`WantsUpdateLayer`] 屬性。 如果它傳回 `true` 則會呼叫 `UpdateLayer` 方法，否則會呼叫 View 的 `DrawRect` 方法來更新視圖的內容。

Apple 有下列建議，可在需要時更新 Views 內容：

- Apple 偏好盡可能使用 `DrawRect` `UpdateLater`，因為它提供顯著的效能提升。
- 針對看起來類似的 UI 元素使用相同的 `layer.Contents`。
- Apple 也偏好讓開發人員使用標準的觀點（例如 `NSTextField`）來撰寫其 UI，這可能會一次。

若要使用 `UpdateLayer`，請建立 `NSView` 的自訂類別，並讓程式碼看起來如下所示：

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

<a name="Using-Modern-Drag-and-Drop" />

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

Flocking 效果的達成方式是將每個專案拖曳到 `NSView` 的 `BeginDraggingSession` 方法，做為陣列中的個別元素。

當使用 `NSTableView` 或 `NSOutlineView`時，請使用 `NSTableViewDataSource` 類別的 `PastboardWriterForRow` 方法來啟動拖曳作業：

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

這可讓開發人員針對要拖曳之資料表中的每個專案，提供個別的 `NSDraggingItem`，而不是將所有資料列當做單一群組寫入至夾的舊版方法 `WriteRowsWith`。

使用 `NSCollectionViews`時，請在開始拖曳時，再次使用 `PasteboardWriterForItemAt` 方法，而不是 `WriteItemsAt` 方法。

開發人員應該一律避免將大型檔案放在存放夾上。 檔案_承諾_可讓開發人員在使用新的 `NSFilePromiseProvider` 和 `NSFilePromiseReceiver` 類別完成 Drop 作業時，將參考放在貼文中的指定檔案，以便在稍後完成的 macOS Sierra。

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>使用新式事件追蹤

對於已加入至標題或工具列區域的使用者介面專案（例如 `NSButton`），使用者應該能夠按一下該專案，並讓它像平常一樣引發事件（例如顯示快顯視窗）。 不過，由於專案也是在標題或工具列區域中，因此使用者也應該可以按一下並拖曳元素來移動視窗。

若要在程式碼中完成這項操作，請為專案建立自訂類別（例如 `NSButton`），並覆寫 `MouseDown` 事件，如下所示：

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

這段程式碼會使用 UI 元素所附加之 `NSWindow` 的 `TrackEventsMatching` 方法，來攔截 `LeftMouseUp` 和 `LeftMouseDragged` 事件。 若為 `LeftMouseUp` 事件，UI 元素會以正常方式回應。 對於 `LeftMouseDragged` 事件，會將事件傳遞至 `NSWindow`的 `PerformWindowDrag` 方法，以在螢幕上移動視窗。

呼叫 `NSWindow` 類別的 `PerformWindowDrag` 方法，可提供下列優點：

- 它允許視窗移動，即使應用程式已停止回應（例如處理深層迴圈時）。
- 空間切換會如預期般運作。
- 空格鍵會顯示為一般。
- 視窗貼齊和對齊會正常運作。

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>使用新式容器視圖控制項

macOS Sierra 為舊版作業系統提供的現有容器視圖控制項提供許多現代化的改良功能。

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>資料表視圖的增強功能

開發人員應一律使用以新的 `NSView` 為基礎的容器視圖控制項版本，例如 `NSTableView`。 例如:

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

這可讓自訂資料表資料列動作附加至資料表中的指定資料列（例如，輕刷以刪除資料列）。 若要啟用此行為，請覆寫 `NSTableViewDelegate`的 `RowActions` 方法：

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

靜態 `NSTableViewRowAction.FromStyle` 是用來建立下列樣式的新資料表資料列動作：

- `Regular`-執行標準的非破壞性動作，例如編輯資料列的內容。
- `Destructive`-執行破壞性動作，例如刪除資料表中的資料列。 這些動作將會以紅色背景呈現。

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>捲軸視圖增強功能 

當直接使用捲軸（`NSScrollView`），或做為另一個控制項的一部分（例如 `NSTableView`）時，捲軸的內容可以使用現代化的外觀和觀點，在 Xamarin 應用程式的標題和工具列區域下滑動。

因此，[標題] 和 [工具列] 區域可以部分遮蔽 [捲軸] 內容區域中的第一個專案。

為了修正此問題，Apple 已將兩個新屬性新增至 `NSScrollView` 類別：

- `ContentInsets`-允許開發人員提供 `NSEdgeInsets` 物件，以定義將套用至捲軸頂端的位移。
- `AutomaticallyAdjustsContentInsets`-如果 `true` 捲軸會自動處理開發人員的 `ContentInsets`。

藉由使用 `ContentInsets` 開發人員可以調整捲軸的起點，以允許包含下列配件：

- 如郵件應用程式中所示的排序指示器。
- 搜尋欄位。
- [重新整理] 或 [更新] 按鈕。

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>現代化應用程式中的自動版面配置和當地語系化

Apple 在 Xcode 中包含數種技術，可讓開發人員輕鬆地建立國際化 macOS 應用程式。 Xcode 現在可讓開發人員在其分鏡腳本檔案中，將使用者面向的文字與應用程式的使用者介面設計分開，並提供在 UI 變更時維護此區隔的工具。

如需詳細資訊，請參閱 Apple 的[國際化和當地語系化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>執行基底國際化 

藉由實行「基底國際化」，開發人員可以提供單一分鏡腳本檔案來代表應用程式的 UI，並將所有的使用者面向字串分開。 

當開發人員建立的初始分鏡腳本檔案（或檔案）定義應用程式的使用者介面時，就會在基底國際化（開發人員所說的語言）中建立。

接下來，開發人員可以匯出當地語系化，以及可轉譯成多種語言的基底國際化字串（在分鏡腳本 UI 設計中）。

之後，您可以匯入這些當地語系化，Xcode 會產生腳本的語言特定字串檔案。

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>執行自動版面配置以支援當地語系化

因為當地語系化版本的字串值可能會有極大的大小和（或）讀取方向，所以開發人員應該使用自動設定來定位和調整應用程式使用者介面在分鏡腳本檔案中的大小。

Apple 建議執行下列動作：

- **移除固定寬度條件約束**-所有以文字為基礎的視圖都應該可以根據其內容調整大小。 固定寬度視圖可能會以特定語言裁剪其內容。
- **使用內建內容大小**-根據預設，以文字為基礎的視圖會自動調整以符合其內容。 針對未正確調整大小的以文字為基礎的視圖，請在 Xcode 的 Interface Builder 中選取它們，然後選擇 [**編輯** > **大小] 以符合內容**。
- 套用**前置和尾端屬性**-因為文字方向可以根據使用者的語言而變更，所以請使用新的 `Leading` 和 `Trailing` 條件約束屬性，而不是現有的 `Right` 和 `Left` 屬性。 `Leading` 和 `Trailing` 會根據語言方向自動進行調整。
- 將**Views 釘選到連續的視圖**-這可讓視圖重新置放和調整大小，以回應所選語言的變更。
- **請勿設定 windows 的最小和/或最大大小**-允許 Windows 變更大小，因為選取的語言會調整其內容區域。
- **經常測試版面配置變更**-應用程式開發期間應該以不同的語言持續測試。 如需詳細資訊，請參閱 Apple 的[測試國際化應用程式](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1)檔。
- **使用 NSStackViews 將 Views 釘**選在一起 - `NSStackViews` 可讓其內容以可預測的方式移動並成長，並根據所選取的語言來變更內容大小。

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的 Interface Builder 中當地語系化

Apple 在 Xcode 的 Interface Builder 中提供了幾項功能，開發人員可以在設計或編輯應用程式的 UI 以支援當地語系化時使用。 **屬性偵測器**的**文字方向**區段，可讓開發人員提供如何在選取的文字型視圖（例如 `NSTextField`）上使用和更新方向的提示：

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

其中 `Alignment` 和 `ImagePosition` 是根據控制項的 `UserInterfaceLayoutDirection` 進行設定。

macOS Sierra 新增數個可接受數個參數（例如標題、影像和動作）的新便利函式（透過靜態 `CreateButton` 方法），而且會自動正確地進行鏡像。 例如:

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>使用系統外觀

新式 macOS 應用程式可以採用新的暗介面外觀，適用于映射建立、編輯或展示應用程式：

[![](modern-cocoa-apps-images/content11.png "An example of a dark Mac Window UI")](modern-cocoa-apps-images/content11.png#lightbox)

這可以藉由在顯示視窗之前新增一行程式碼來完成。 例如:

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

`NSAppearance` 類別的靜態 `GetAppearance` 方法是用來從系統取得名為的外觀（在此案例中為 `NSAppearance.NameVibrantDark`）。

Apple 具有下列使用系統外觀的建議：

- 偏好以硬式編碼的值（例如 `LabelColor` 和 `SelectedControlColor`）來命名色彩。
- 可能的話，請使用系統標準控制樣式。

若使用者已從 [系統偏好設定] 應用程式啟用協助工具功能，則使用系統外觀的 macOS 應用程式將會自動正確地運作。 因此，Apple 建議開發人員應該一律在其 macOS 應用程式中使用系統外觀。

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>使用分鏡腳本設計 Ui

分鏡腳本可讓開發人員不只設計構成應用程式使用者介面的個別元素，還可以視覺化和設計 UI 流程和指定專案的階層。

控制器可讓開發人員將元素收集到組合的單位，並 Segue 抽象，並移除整個視圖階層中移動所需的一般「粘連程式碼」：

[![](modern-cocoa-apps-images/content12.png "Editing the UI in Xcode's Interface Builder")](modern-cocoa-apps-images/content12.png#lightbox)

如需詳細資訊，請參閱我們的分鏡指令檔[簡介](~/mac/platform/storyboards/index.md)。

在多個實例中，腳本中定義的指定場景將需要視圖階層中上一個場景的資料。 Apple 有下列在場景之間傳遞資訊的建議：

- 資料 dependancies 一律會在階層中往下重迭。
- 避免硬式編碼 UI 結構化 dependancies，因為這會限制 UI 彈性。
- 使用C#介面來提供一般資料 dependancies。

做為 Segue 來源的視圖控制器可以覆寫 `PrepareForSegue` 方法，並在執行 Segue 以顯示目標視圖控制器之前，執行任何所需的初始化（例如傳遞資料）。 例如:

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

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>傳播動作

根據 macOS 應用程式的設計，有時 UI 控制項上某個動作的最佳處理常式可能位於 UI 階層中的其他位置。 這通常是存在於其本身場景中的功能表和功能表項目，與應用程式的其餘 UI 分開。

若要處理這種情況，開發人員可以建立自訂動作，並將動作傳遞至回應者鏈。 如需詳細資訊，請參閱我們[的使用自訂視窗動作](~/mac/user-interface/menu.md)檔。

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>新式 Mac 功能

Apple 在 macOS Sierra 中加入了數種面向使用者的功能，可讓開發人員充分運用 Mac 平臺，例如：

- **NSUserActivity** -這可讓應用程式描述使用者目前參與的活動。 `NSUserActivity` 最初是為了支援遞交而建立的，在其中一個使用者裝置上啟動的活動可能會在另一個裝置上被挑選並繼續。 `NSUserActivity` 在 macOS 中的運作方式與在 iOS 中相同，如需詳細資訊，請參閱我們的提交 iOS 檔[簡介](~/ios/platform/handoff.md)。
- **Mac 上的 Siri** -Siri 會使用目前的活動（`NSUserActivity`），為使用者可發出的命令提供內容。
- **狀態還原**-當使用者在 macOS 上結束應用程式，然後在稍後加以 relaunches 時，應用程式會自動回到其先前的狀態。 開發人員可以使用狀態還原 API 來編碼和還原暫時性 UI 狀態，然後才向使用者顯示使用者介面。 如果應用程式是以 `NSDocument` 為基礎，則會自動處理狀態還原。 若要針對非以`NSDocument` 為基礎的應用程式啟用狀態還原，請將 `NSWindow` 類別的 `Restorable` 設定為 [`true`]。
- **雲端中的檔-在**macOS Sierra 之前，應用程式必須明確選擇使用使用者的 ICloud 磁片磁碟機中的檔。 在 macOS Sierra 使用者的 [**桌面**] 和 [**檔**] 資料夾可能會由系統自動與其 iCloud 磁片磁碟機同步。 因此，可能會刪除檔的本機複本，以釋放使用者電腦上的空間。 以 `NSDocument` 為基礎的應用程式將會自動處理這種變更。 所有其他應用程式類型都必須使用 `NSFileCoordinator` 來同步處理檔的讀取和寫入。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的數個秘訣、功能和技術。

## <a name="related-links"></a>相關連結

- [macOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
