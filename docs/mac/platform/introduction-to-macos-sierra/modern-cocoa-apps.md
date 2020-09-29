---
title: 建置現代化的 macOS 應用程式
description: 本文涵蓋開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技巧。
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: d2a3181360abd7ee7a5124602d0c4186b0a8ec38
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430166"
---
# <a name="building-modern-macos-apps"></a>建置現代化的 macOS 應用程式

_本文涵蓋開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技巧。_

<a name="Building-Modern-Looks-with-Modern-Views"></a>

## <a name="building-modern-looks-with-modern-views"></a>使用新式視圖打造現代化的外觀

新式的外觀將包含新式視窗和工具列外觀，例如範例應用程式，如下所示：

[![新式 Mac 應用程式 UI 的範例](modern-cocoa-apps-images/content08.png)](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views"></a>

### <a name="enabling-full-sized-content-views"></a>啟用完整大小的內容查看

為了達到此目的，開發人員會想要使用 _完整大小的內容視圖_，這表示內容會延伸至工具和標題列區域下，而且 macOS 會自動將它模糊。

若要在程式碼中啟用這項功能，請建立的自訂類別， `NSWindowController` 並讓它看起來如下所示：

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

您也可以在 Xcode 的 Interface Builder 中啟用這項功能，方法是選取視窗並檢查 **完整大小的內容視圖**：

[![在 Xcode 的 Interface Builder 中編輯主要分鏡腳本](modern-cocoa-apps-images/content01.png)](modern-cocoa-apps-images/content01.png#lightbox)

使用完整大小的內容視圖時，開發人員可能需要將標題和工具列區域下的內容位移，讓特定內容 (例如標籤) 不會滑下它們。

若要使此問題更複雜，標題和工具列區域可以根據使用者目前正在執行的動作、使用者已安裝的 macOS 版本，以及（或）應用程式執行所在的 Mac 硬體，來擁有動態高度。

如此一來，在配置消費者介面時，只需對位移進行硬編碼就無法運作。 開發人員必須採取動態的方法。

Apple 已包含類別的索引 [鍵/值可觀察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes) `ContentLayoutRect` 屬性 `NSWindow` ，以取得程式碼中的目前內容區域。 開發人員可以使用此值，在內容區域變更時手動放置必要的元素。

更好的解決方法是使用自動設定和大小類別，在程式碼或 Interface Builder 中放置 UI 元素。

您可以使用應用程式視圖控制器中的 [自動調整] 和 [大小] 類別，使用類似下列範例的程式碼來放置 UI 元素：

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

此程式碼會建立 top 條件約束的儲存區，以套用至 () 的標籤， `ItemTitle` 以確保它不會列在標題和工具列區域底下：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

藉由覆寫 View 控制器的 `UpdateViewConstraints` 方法，開發人員可以進行測試，以查看是否已建立所需的條件約束，並在需要時建立。

如果需要建立新的條件約束，則 `ContentLayoutGuide` 會存取需要限制之控制項的視窗屬性，並轉換成 `NSLayoutGuide` ：

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

會存取的 TopAnchor 屬性，如果有的話 `NSLayoutGuide` ，則會用來建立具有所需位移金額的新條件約束，並將新的條件約束設為作用中，以套用它：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars"></a>

### <a name="enabling-streamlined-toolbars"></a>啟用簡化的工具列

一般 macOS 視窗會在執行時包含標準的標題列，以及視窗的上邊緣。 如果視窗也包含工具列，它就會顯示在此標題列區域下方：

[![標準 Mac 工具列](modern-cocoa-apps-images/content02.png)](modern-cocoa-apps-images/content02.png#lightbox)

使用簡化的工具列時，標題區域隨即消失，而且工具列會向上移至標題列的位置，並以視窗關閉、最小化和最大化按鈕為行：

[![簡化的 Mac 工具列](modern-cocoa-apps-images/content03.png)](modern-cocoa-apps-images/content03.png#lightbox)

藉由覆寫的方法來啟用簡化的工具列 `ViewWillAppear` `NSViewController` ，使其看起來如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

此效果通常用來 _Shoebox 應用程式_ (一種視窗應用程式) 例如地圖、行事曆、附注和系統偏好設定。

<a name="Using-Accessory-View-Controllers"></a>

### <a name="using-accessory-view-controllers"></a>使用配件視圖控制器

視應用程式的設計而定，開發人員可能也會想要將標題列區域補充為顯示在標題/工具列區域正下方的配件查看控制器，根據他們目前參與的活動，為使用者提供內容相關的控制項：

[![範例配件視圖控制器](modern-cocoa-apps-images/content04.png)](modern-cocoa-apps-images/content04.png#lightbox)

系統不需要開發人員介入，即可自動將配件視圖控制器模糊並調整其大小。

若要新增「配件視圖控制器」，請執行下列動作：

1. 在方案總管**** 中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
2. 將 **自訂視圖控制器** 拖曳至視窗的階層：

    [![新增自訂視圖控制器](modern-cocoa-apps-images/content05.png)](modern-cocoa-apps-images/content05.png#lightbox)
3. 配置配件視圖的 UI：

    [![設計新的視圖](modern-cocoa-apps-images/content06.png)](modern-cocoa-apps-images/content06.png#lightbox)
4. 公開配件視圖作為 **輸出** ，以及其 UI 的任何其他 **動作** 或 **輸出** ：

    [![新增必要的輸出](modern-cocoa-apps-images/content07.png)](modern-cocoa-apps-images/content07.png#lightbox)
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

此程式碼的重點是將視圖設定為 Interface Builder 中定義並公開為 **輸出**的自訂視圖：

```csharp
accessoryView.View = AccessoryViewGoBar;
```

和， `LayoutAttribute` 定義將顯示配件的 _位置_ ：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

因為 macOS 現在已完全當地語系化，所以 `Left` 和 `Right` `NSLayoutAttribute` 屬性已被取代，而且應該以 `Leading` and 取代 `Trailing` 。

<a name="Using-Tabbed-Windows"></a>

### <a name="using-tabbed-windows"></a>使用索引標籤式視窗

此外，macOS 系統可能會在應用程式的視窗中新增「配件視圖控制器」。 例如，建立索引標籤式視窗，其中有幾個應用程式的 Windows 會合並成一個虛擬視窗：

[![索引標籤式 Mac 視窗的範例](modern-cocoa-apps-images/content08.png)](modern-cocoa-apps-images/content08.png#lightbox)

一般來說，開發人員在其 Xamarin 應用程式中使用索引標籤式視窗時，需要採取有限的動作，系統會自動處理這些應用程式，如下所示：

- 當叫用方法時，Windows 會自動成為索引標籤 `OrderFront` 。
- 叫用方法時，將會自動 Untabbed 視窗 `OrderOut` 。
- 在程式碼中，所有索引標籤式視窗仍會被視為「可見」，不過，系統會使用 CoreGraphics 來隱藏任何非最前面的索引標籤。
- 使用的 `TabbingIdentifier` 屬性 `NSWindow` ，將視窗群組在一起成為索引標籤。
- 如果是以 `NSDocument` 應用程式為基礎的應用程式，這些功能將會自動啟用 (例如，新增到索引標籤列的加號按鈕) ，而不需要任何開發人員動作。
- 非 `NSDocument` 基礎的應用程式可以藉由覆寫的方法，讓索引標籤群組中的 [加號] 按鈕加入新檔 `GetNewWindowForTab` `NSWindowsController` 。

將所有的元件結合在一起 `AppDelegate` 之後，應用程式若要使用以系統為基礎的索引標籤式視窗，可能如下所示：

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

`NewDocumentNumber`屬性會追蹤所建立的新檔數目，而 `NewDocument` 方法會建立新檔並加以顯示。

`NSWindowController`之後可能會看起來像這樣：

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

其中，靜態 `App` 屬性會提供快捷方式來取得 `AppDelegate` 。 `SetDefaultDocumentTitle`方法會根據建立的新檔數目來設定新的檔標題。

下列程式碼會告訴 macOS，應用程式偏好使用索引標籤，並提供可讓應用程式的視窗分組到索引標籤的字串：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

而且，下列覆寫方法會將加號按鈕新增至索引標籤列，以在使用者按一下時建立新的檔：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation"></a>

### <a name="using-core-animation"></a>使用核心動畫

核心動畫是 macOS 中內建的高驅動圖形轉譯引擎。 核心動畫已經過優化，可利用 GPU (圖形處理單位) 可在新式 macOS 硬體中使用，而不是在 CPU 上執行圖形作業，這可能會使電腦變慢。

`CALayer`由核心動畫提供的，可用於快速、流暢的滾動和動畫等工作。 應用程式的消費者介面應由多個子檢視和圖層組成，以充分利用核心動畫。

`CALayer`物件提供數個屬性，可讓開發人員控制使用者在螢幕上呈現的內容，例如：

- `Content` -可以是 `NSImage` 或 `CGImage` ，以提供圖層的內容。
- `BackgroundColor` -將圖層的背景色彩設定為 `CGColor`
- `BorderWidth` -設定框線寬度。
- `BorderColor` -設定框線色彩。

若要在應用程式的 UI 中使用核心圖形，它必須使用 _分層支援_ 的視圖，Apple 建議開發人員應一律在視窗的內容視圖中啟用。 如此一來，所有的子視圖也會自動繼承層級的支援。

此外，Apple 建議使用分層支援的視圖，而不是將新的 `CALayer` 設定為子層，因為系統會自動處理數個必要的設定 (例如 Retina 顯示器) 所需的設定。

您可以藉由 `WantsLayer` `NSView` `true` 檢查**核心動畫層**，在 Xcode 的 Interface Builder 的 [ **View 效果**] 偵測器下，將 a 設定為或內部，藉以啟用層級支援：

[![視圖效果偵測器](modern-cocoa-apps-images/content09.png)](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers"></a>

#### <a name="redrawing-views-with-layers"></a>使用圖層重繪視圖

在 Xamarin 應用程式中使用圖層支援的視圖時，另一個重要的步驟是在中將的設定為 `LayerContentsRedrawPolicy` `NSView` `OnSetNeedsDisplay` `NSViewController` 。 例如：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

如果開發人員未設定此屬性，則會在其畫面格來源變更時重新繪製視圖，而不是基於效能考慮而需要。 將此屬性設定為時， `OnSetNeedsDisplay` 開發人員將會手動將設定 `NeedsDisplay` 為 `true` ，以強制重新繪製內容。

當某個視圖標記為中途時，系統會檢查該 `WantsUpdateLayer` 視圖的屬性。 如果傳回 `true` `UpdateLayer` ，則會呼叫方法，否則 `DrawRect` 會呼叫視圖的方法來更新視圖的內容。

Apple 有下列建議，可在需要時更新視圖內容：

- Apple 偏好盡可能 `UpdateLater` 使用 `DrawRect` ，因為它可大幅提升效能。
- `layer.Contents`針對看起來類似的 UI 元素，請使用相同的。
- Apple 也偏好開發人員使用標準的視圖（例如）來撰寫其 UI （ `NSTextField` 如果可能的話）。

若要使用 `UpdateLayer` ，請建立的自訂類別 `NSView` ，並讓程式碼看起來如下所示：

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

若要為使用者提供新式的拖放體驗，開發人員應在其應用程式的拖放作業中採用 _拖曳群群聚_ 。 拖曳群群聚是一開始拖曳的每個個別檔案或專案，都會顯示為個別元素，flocks (群組在游標下的專案，並在使用者繼續拖曳作業時) 專案數的計數。

如果使用者終止拖曳作業，則個別專案將會 Unflock 並回到其原始位置。

下列範例程式碼可讓您在自訂視圖上拖曳群群聚：

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

群群聚效果的達成方式是將拖曳的每個專案都傳送至的 `BeginDraggingSession` 方法， `NSView` 做為陣列中的個別元素。

使用 `NSTableView` 或時 `NSOutlineView` ，請使用類別的 `PastboardWriterForRow` 方法 `NSTableViewDataSource` 來開始拖曳作業：

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

這可讓開發人員針對要拖曳 `NSDraggingItem` 之資料表中的每個專案提供個別的專案，而不是較舊的方法 `WriteRowsWith` ，將所有資料列以單一群組的形式寫入至粘貼夾。

使用時 `NSCollectionViews` ，再次 `PasteboardWriterForItemAt` 開始拖曳時，請使用方法，而不是 `WriteItemsAt` 方法。

開發人員應一律避免將大型檔案放在資料夾上。 檔案保證是 macOS Sierra _的新手，可讓開發_ 人員將參考放置於粘貼程式上的指定檔案，之後當使用者使用新的和類別完成 Drop 作業時，就會完成這些檔案的參考 `NSFilePromiseProvider` `NSFilePromiseReceiver` 。

<a name="Using-Modern-Event-Tracking"></a>

## <a name="using-modern-event-tracking"></a>使用新式事件追蹤

針對消費者介面專案 (例如已 `NSButton` 加入標題或工具列區域的) ），使用者應該能夠按一下專案，並讓它以一般 (引發事件，例如顯示快顯視窗) 。 不過，因為專案也在標題或工具列區域中，所以使用者應該也可以按一下並拖曳專案來移動視窗。

若要在程式碼中完成這項操作，請為元素建立自訂類別 (例如 `NSButton`) 並覆寫事件，如下所示 `MouseDown` ：

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

此 `TrackEventsMatching` 程式碼會使用 UI 元素附加的的方法 `NSWindow` 來攔截 `LeftMouseUp` 和 `LeftMouseDragged` 事件。 若為 `LeftMouseUp` 事件，UI 元素會以正常方式回應。 針對 `LeftMouseDragged` 事件，會將事件傳遞至 `NSWindow` 的方法， `PerformWindowDrag` 以便在螢幕上移動視窗。

呼叫 `PerformWindowDrag` 類別的方法 `NSWindow` 可提供下列優點：

- 即使應用程式沒有回應，也可讓視窗移動 (例如處理深層迴圈) 時。
- 空間切換將會如預期般運作。
- 空格鍵會顯示為一般。
- 視窗貼齊和對齊正常運作。

<a name="Using-Modern-Container-View-Controls"></a>

## <a name="using-modern-container-view-controls"></a>使用新式容器視圖控制項

macOS Sierra 針對舊版作業系統所提供的現有容器視圖控制項，提供許多新式改進。

<a name="Table View Enhancements"></a>

## <a name="table-view-enhancements"></a>資料表視圖增強功能

開發人員應該一律使用 `NSView` 容器視圖控制項的新版本，例如 `NSTableView` 。 例如：

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

這可讓自訂的資料表資料列動作附加至資料表中指定的資料列 (例如，將資料列) 刪除的輕輕。 若要啟用此行為，請覆寫的 `RowActions` 方法 `NSTableViewDelegate` ：

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

- `Regular` -執行標準的非破壞性動作，例如編輯資料列的內容。
- `Destructive` -執行破壞性動作，例如從資料表中刪除資料列。 這些動作將會以紅色背景呈現。

<a name="Scroll-View-Enhancements"></a>

## <a name="scroll-view-enhancements"></a>捲軸視圖增強功能

當您直接使用滾動 `NSScrollView` 條 () ，或做為其他控制項 (例如) 時 `NSTableView` ，捲軸的內容可以在 Xamarin 應用程式中的標題和工具列區域下滑下，並使用新式的外觀和觀點。

因此，標題和工具列區域可以部分遮蔽捲軸內容區域中的第一個專案。

為了修正此問題，Apple 已將兩個新的屬性新增至 `NSScrollView` 類別：

- `ContentInsets` -允許開發人員提供一個物件，該 `NSEdgeInsets` 物件定義將套用至捲軸頂端的位移。
- `AutomaticallyAdjustsContentInsets` -如果 `true` 捲軸會自動 `ContentInsets` 為開發人員處理。

藉由使用 `ContentInsets` 開發人員，可以調整捲軸的開頭，以允許包含附屬配件，例如：

- 類似于郵件應用程式中所顯示的排序指標。
- 搜尋欄位。
- [重新整理] 或 [更新] 按鈕。

<a name="Auto-Layout-and-Localization-in-Modern-Apps"></a>

## <a name="auto-layout-and-localization-in-modern-apps"></a>新式應用程式中的自動版面配置和當地語系化

Apple 已在 Xcode 中包含數種技術，可讓開發人員輕鬆地建立國際化 macOS 應用程式。 Xcode 現在可讓開發人員在其分鏡腳本檔案中，將使用者面向的文字與應用程式的消費者介面設計分開，並提供可在 UI 變更時維護此分隔的工具。

如需詳細資訊，請參閱 Apple 的 [國際化和當地語系化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization"></a>

### <a name="implementing-base-internationalization"></a>執行基底國際化

藉由執行基底國際化，開發人員可以提供單一分鏡腳本檔案來代表應用程式的 UI，並分隔出所有使用者面向的字串。

當開發人員建立 (的初始分鏡腳本檔案或定義應用程式消費者介面的檔案) 時，它們將會以基礎國際化的方式建立， (開發人員所說的語言) 。

接下來，開發人員可以將當地語系化和基底國際化字串 (在分鏡腳本 UI 設計) 中，以轉譯成多種語言。

稍後，您可以匯入這些當地語系化，而 Xcode 會為腳本產生語言特定的字串檔案。

<a name="Implementing-Auto-Layout-to-Support-Localization"></a>

### <a name="implementing-auto-layout-to-support-localization"></a>執行自動設定以支援當地語系化

因為字串值的當地語系化版本可能會有極大的大小和（或）閱讀方向，所以開發人員應該使用自動版面配置來定位應用程式的消費者介面，並調整其大小。

Apple 建議您執行下列動作：

- **移除固定寬度條件約束** -所有以文字為基礎的視圖都應可根據其內容調整大小。 固定寬度視圖可能會以特定語言裁剪其內容。
- 使用內建的**內容大小**-根據預設，以文字為基礎的視圖會自動調整大小以符合其內容。 針對未正確調整大小的文字型視圖，請在 Xcode 的 Interface Builder 中選取它們，然後選擇 [**編輯**  >  **大小] 以符合內容**。
- 套用**前置和尾端屬性**-因為文字的方向可以根據使用者的語言而變更，所以請使用新的 `Leading` 和 `Trailing` 條件約束屬性，而非現有的 `Right` 和 `Left` 屬性。 `Leading` 而且 `Trailing` 會根據語言方向自動調整。
- 將**視圖釘選到連續的視圖**-這可讓您在變更時重新置放和調整大小，以回應所選的語言。
- **請勿設定 windows 的最小值和/或大小上限** -允許 windows 在選取的語言調整其內容區域大小時變更大小。
- **不斷測試** 配置的變更-在應用程式開發期間，應以不同的語言持續測試。 如需詳細資訊，請參閱 Apple 的 [測試您的國際化應用程式](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1) 檔。
- **使用 NSStackViews 將視圖釘選在一起**  -  `NSStackViews`允許其內容以可預測的方式轉移和成長，以及根據所選語言的內容變更大小。

<a name="Localizing-in-Xcodes-Interface-Builder"></a>

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的 Interface Builder 中進行當地語系化

Apple 在 Xcode 的 Interface Builder 中提供數個功能，可供開發人員在設計或編輯應用程式的 UI 以支援當地語系化時使用。 **屬性偵測器**的 [**文字方向**] 區段可讓開發人員提供有關如何在以文字為基礎的選取 (（例如) ）上使用和更新方向的提示 `NSTextField` ：

[![文字方向選項](modern-cocoa-apps-images/content10.png)](modern-cocoa-apps-images/content10.png#lightbox)

**文字方向**有三個可能的值：

- **自然** -版面配置是以指派給控制項的字串為基礎。
- **從左至右** -配置一律會強制由左到右。
- 由**右至左**-配置一律強制由右至左。

**版面**配置有兩個可能的值：

- **從左至右** -配置一律由左至右。
- 由**右至左**-版面配置一律是由右至左。

除非需要特定的對齊，否則通常不應變更這些變更。

**鏡像**屬性會告知系統切換特定控制項屬性 (例如，資料格影像位置) 。 它有三個可能的值：

- **自動** -位置會根據所選語言的方向自動變更。
- 從**右至左介面**-位置只會以由右至左的語言變更。
- **永遠** 不會變更位置。

如果開發人員在以文字為基礎的視圖內容中，有指定的 **中心**、 **對齊** 或 **完全** 對齊，則永遠不會根據選取的語言來翻轉這些內容。

在 macOS Sierra 之前，在程式碼中建立的控制項不會自動進行鏡像。 開發人員必須使用如下所示的程式碼來處理鏡像：

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

其中 `Alignment` 和 `ImagePosition` 是根據 `UserInterfaceLayoutDirection` 控制項的來設定。

macOS Sierra 透過靜態方法來新增數個新的便利性函式 (`CreateButton`) 會採用數個參數 (例如標題、影像和動作) ，並會自動進行正確的鏡像。 例如：

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances"></a>

## <a name="using-system-appearances"></a>使用系統外觀

新式 macOS apps 可採用新的深介面外觀，此外觀適用于映射建立、編輯或展示應用程式：

[![深色 Mac 視窗 UI 的範例](modern-cocoa-apps-images/content11.png)](modern-cocoa-apps-images/content11.png#lightbox)

這可以藉由在呈現視窗之前加入一行程式碼來完成。 例如：

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

`GetAppearance` `NSAppearance` 在此案例中，會使用類別的靜態方法來取得系統中的命名外觀 (`NSAppearance.NameVibrantDark`) 。

Apple 對於使用系統外觀有下列建議：

- 偏好使用硬式編碼值的命名色彩 (例如 `LabelColor` 和 `SelectedControlColor`) 。
- 盡可能使用系統標準控制項樣式。

如果使用者已從系統喜好設定應用程式啟用協助工具功能，則使用系統外觀的 macOS 應用程式會自動正常運作。 因此，Apple 建議開發人員在其 macOS 應用程式中一律使用系統外觀。

<a name="Designing-UIs-with-Storyboards"></a>

## <a name="designing-uis-with-storyboards"></a>使用分鏡腳本設計 Ui

分鏡腳本讓開發人員不僅能設計構成應用程式消費者介面的個別元素，還可以視覺化和設計 UI 流程以及指定專案的階層。

控制器可讓開發人員將元素收集至組合的單位，並 Segue 抽象，並移除在整個 View 階層中移動所需的一般「粘連程式碼」：

[![在 Xcode 的 Interface Builder 中編輯 UI](modern-cocoa-apps-images/content12.png)](modern-cocoa-apps-images/content12.png#lightbox)

如需詳細資訊，請參閱分鏡指令檔的 [簡介](~/mac/platform/storyboards/index.md) 。

在許多情況下，腳本中定義的指定場景將需要視圖階層中上一個場景的資料。 Apple 在幕後傳遞資訊的建議如下：

- 資料 dependancies 應該一律在階層中向下一層。
- 避免硬式編碼 UI 結構化 dependancies，因為這會限制 UI 的彈性。
- 使用 c # 介面來提供一般資料 dependancies。

作為 Segue 來源的 View 控制器可以覆寫 `PrepareForSegue` 方法，並執行任何必要的初始化工作 (例如，在 Segue 執行之前將資料傳遞) ，以顯示目標視圖控制器。 例如：

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

如需詳細資訊，請參閱我們的 [segue](~/mac/platform/storyboards/indepth.md#Segues) 檔。

<a name="Propagating-Actions"></a>

## <a name="propagating-actions"></a>傳播動作

根據 macOS 應用程式的設計，有時候 UI 控制項上動作的最佳處理常式可能位於 UI 階層中的其他位置。 這通常適用于存在於自己場景中的功能表和功能表項目，與應用程式的其餘部分不同。

若要處理這種情況，開發人員可以建立自訂動作，並將動作傳遞給回應者鏈。 如需詳細資訊，請參閱使用 [自訂視窗動作](~/mac/user-interface/menu.md) 檔。

<a name="Modern-Mac-Features"></a>

## <a name="modern-mac-features"></a>新式 Mac 功能

Apple 在 macOS Sierra 中包含了數個使用者面向的功能，可讓開發人員充分運用 Mac 平臺，例如：

- **NSUserActivity** -這可讓應用程式描述使用者目前參與的活動。 `NSUserActivity` 最初是為了支援提交而建立的，在其中一個使用者裝置上啟動的活動可以在另一個裝置上挑選並繼續。 `NSUserActivity` 在 macOS 中的運作方式與在 iOS 中的運作方式相同，如需詳細資料，請參閱《提交 ios 檔的 [簡介](~/ios/platform/handoff.md) 」。
- **Mac 上的 Siri** -Siri 使用目前的活動 (`NSUserActivity`) 來提供使用者可發出之命令的內容。
- **狀態還原** -當使用者結束 macOS 上的應用程式，然後 relaunches 該應用程式時，應用程式會自動回到先前的狀態。 開發人員可以在向使用者顯示消費者介面之前，使用狀態還原 API 來編碼和還原暫時性的 UI 狀態。 如果是以應用程式為 `NSDocument` 基礎，則會自動處理狀態還原。 若要為非基礎的應用程式啟用狀態還原 `NSDocument` ，請將類別的設定 `Restorable` `NSWindow` 為 `true` 。
- **雲端中的檔** -在 macOS Sierra 之前，應用程式必須明確地選擇使用使用者的 ICloud 磁片磁碟機中的檔。 在 macOS Sierra 使用者的 [ **桌面** ] 和 [ **檔** ] 資料夾可由系統自動與其 iCloud 磁片磁碟機進行同步處理。 因此，可能會刪除檔的本機複本，以釋出使用者電腦上的空間。 `NSDocument` 以應用程式為基礎的應用程式將會自動處理此變更。 所有其他應用程式類型都必須使用 `NSFileCoordinator` 來同步處理檔的讀取和寫入。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文涵蓋了開發人員可用來在 Xamarin 中建立新式 macOS 應用程式的幾個秘訣、功能和技巧。

## <a name="related-links"></a>相關連結

- [macOS 範例](/samples/browse/?products=xamarin&term=Xamarin.Mac)