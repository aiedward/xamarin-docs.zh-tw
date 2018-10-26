---
title: 建置現代化的 macOS 應用程式
description: 本文涵蓋幾個秘訣、 功能和技術的開發人員可用來建置現代化的 macOS 中的應用程式 Xamarin.Mac。
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 53bfc9f147b6cf369b8f5ce8d1257dbaf6b0f807
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113517"
---
# <a name="building-modern-macos-apps"></a>建置現代化的 macOS 應用程式

_本文涵蓋幾個秘訣、 功能和技術的開發人員可用來建置現代化的 macOS 中的應用程式 Xamarin.Mac。_

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>建置新式看起來與現今的檢視

現代化的外觀會包括例如如下所示的範例應用程式的視窗和工具列新式外觀：

[![](modern-cocoa-apps-images/content08.png "現代的 Mac 應用程式 UI 的範例")](modern-cocoa-apps-images/content08.png#lightbox)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>啟用完整大小的內容檢視

若要達到這看起來的 Xamarin.Mac 應用程式中，開發人員會想要使用_完整內容] 檢視的大小_表示內容擴充 [工具] 和 [標題列區域底下，將會自動模糊 macos。

若要啟用這項功能在程式碼中的，建立的自訂類別`NSWindowController`，使它看起來如下所示：

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

這項功能也可以啟用在 Xcode 的 Interface Builder 中選取視窗，並檢查**完整大小的內容檢視**:

[![](modern-cocoa-apps-images/content01.png "編輯主要在 Xcode 的 Interface Builder 分鏡腳本")](modern-cocoa-apps-images/content01.png#lightbox)

使用完整的大小內容檢視時，開發人員可能需要位移下的標題和工具的列區域的內容，以便在其下的特定內容 （例如標籤） 不投影片。

這個問題變得複雜，標題 和 工具列區域可以有動態高度，以根據使用者目前正在執行，動作的 macOS 使用者安裝和/或 Mac 硬體上執行的應用程式的版本。

如此一來，只是硬式編碼的位移，使用者介面版面配置時無法運作。 開發人員必須採用動態方法。

已包含了 Apple[機碼值可觀察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes)`ContentLayoutRect`屬性`NSWindow`類別，以取得目前的內容區域中的程式碼。 開發人員可以使用此值，以手動方式放置必要的項目，當內容區域變更時。

更好的解決方案是使用自動版面配置和大小類別放置在程式碼或介面產生器中的 UI 項目。

如下列範例所示的程式碼可用於放置在應用程式的檢視控制器中使用自動版面配置和大小類別的 UI 項目：

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

此程式碼會建立最上層的條件約束，將會套用至標籤的儲存體 (`ItemTitle`) 以確保它不會略過標題和工具列區域下：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

藉由覆寫檢視控制器的`UpdateViewConstraints`方法，開發人員可以進行測試，以了解是否已建置所需的條件約束並視需要建立它。

如果必須建置，新的條件約束`ContentLayoutGuide`屬性的視窗控制項需要限制存取資料並轉換成`NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

TopAnchor 屬性`NSLayoutGuide`存取，並且如果有的話，它用來建立新的條件約束，以所需的位移量，並可作用中，以將它套用新的條件約束：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>啟用簡化的工具列

一般的 macOS 視窗包含位於標題列執行視窗頂端的標準。 如果視窗也包含工具列，它將會顯示此標題列區域下：

[![](modern-cocoa-apps-images/content02.png "標準的 Mac 工具列")](modern-cocoa-apps-images/content02.png#lightbox)

當使用簡化的工具列，標題區會消失，並向上移動至標題列的位置的工具列時，內建視窗關閉、 最小化和最大化按鈕：

[![](modern-cocoa-apps-images/content03.png "簡化的 Mac 工具列")](modern-cocoa-apps-images/content03.png#lightbox)

簡化的工具列已藉由覆寫`ViewWillAppear`方法的`NSViewController`並讓它看起來如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

這種效果通常是為了_Shoebox 應用程式_對應、 行事曆、 記事和系統喜好設定，例如 （一個視窗應用程式）。 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>使用配件的檢視控制器

根據應用程式的設計、 開發人員也可能想要補充附屬應用程式檢視控制器標題/工具列區域提供區分內容控制項新增至活動為基礎的使用者其下方的右邊區域會在標題列目前參與：

[![](modern-cocoa-apps-images/content04.png "範例附屬應用程式檢視控制器")](modern-cocoa-apps-images/content04.png#lightbox)

附屬應用程式檢視控制器會自動模糊並調整大小，系統不需要開發人員介入。

若要新增附屬應用程式檢視控制器，請執行下列作業：

1. 在方案總管中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
2. 拖曳**自訂檢視控制器**到視窗的階層： 

    [![](modern-cocoa-apps-images/content05.png "加入新的自訂檢視控制器")](modern-cocoa-apps-images/content05.png#lightbox)
3. 配件的檢視版面配置 UI: 

    [![](modern-cocoa-apps-images/content06.png "設計新的檢視")](modern-cocoa-apps-images/content06.png#lightbox)
4. 做為 [配件] 檢視會公開**插座**以及任何其他**動作**或**輸出**其 ui: 

    [![](modern-cocoa-apps-images/content07.png "新增必要的輸出")](modern-cocoa-apps-images/content07.png#lightbox)
5. 儲存變更。
6. 返回 Visual Studio for Mac 將變更同步。

編輯`NSWindowController`，使它看起來如下所示：

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

此程式碼的重點是，檢視設為自訂檢視的 Interface Builder 中所定義並公開為**插座**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

而`LayoutAttribute`定義_其中_附屬應用程式將會顯示：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

MacOS 現在完全當地語系化，因為`Left`並`Right``NSLayoutAttribute`屬性已被取代，用來取代`Leading`和`Trailing`。

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>使用索引標籤式的 Windows

此外，macOS 系統可能會將附屬應用程式檢視控制器新增至應用程式的視窗。 例如，若要建立索引標籤式的 Windows，其中有幾個應用程式的 Windows 會合併成一個虛擬的視窗：

[![](modern-cocoa-apps-images/content08.png "索引標籤式的 [Mac] 視窗的範例")](modern-cocoa-apps-images/content08.png#lightbox)

一般而言，開發人員必須採用有限的動作使用 Xamarin.Mac 應用程式中的索引標籤式 Windows，系統會自動，如下所示處理：

- Windows 會自動成為索引標籤式時`OrderFront`叫用方法。
- Windows 會自動成為 Untabbed 時`OrderOut`叫用方法。
- 不過在程式碼中所有索引標籤式視窗仍會視為 「 可見 」，任何非最上層索引標籤會隱藏使用 CoreGraphics 系統。
- 使用`TabbingIdentifier`屬性`NSWindow`來彙整到索引標籤的群組 Windows。
- 如果是`NSDocument`型的應用程式，這些功能將不執行任何開發人員動作啟用自動 （例如新增至索引標籤工具列的加號按鈕）。
- 非`NSDocument`根據應用程式可以啟用 「 加號 」 中的 [按鈕] 索引標籤群組，以新增新的文件，藉由覆寫`GetNewWindowForTab`方法`NSWindowsController`。

將所有的部分結合在一起，`AppDelegate`想要使用的應用程式為基礎的系統索引標籤式 Windows 看起來可能如下所示：

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

何處`NewDocumentNumber`屬性會追蹤的新建立的文件數目和`NewDocument`方法會建立新的文件，並顯示它。

`NSWindowController`然後如下所示：

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

其中的靜態`App`屬性所提供的捷徑，以前往`AppDelegate`。 `SetDefaultDocumentTitle`方法會設定新的文件標題，根據新建立的文件數目。

下列程式碼中，會告知應用程式會優先使用索引標籤的 macOS，並提供可讓應用程式的 Windows 分組為索引標籤的字串：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

及的下列覆寫方法將會建立新的文件時使用者按下索引標籤列中的加號按鈕：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>使用核心動畫

Core 動畫是內建於 macOS 與強大的圖形轉譯引擎。 Core 動畫已經過最佳化，以善用 GPU （圖形處理單元） 提供現代化的 macOS 硬體，而不需要執行圖形作業，CPU，其可能會降低電腦上。

`CALayer`、 Core Animation 所提供、 可用於快速且流暢的捲動和動畫等工作。 應用程式的使用者介面應該包含多個子檢視和圖層以充分利用 Core 動畫。

A`CALayer`物件提供可讓開發人員控制提供內容給使用者畫面上這類的數個屬性：

- `Content` -可以是`NSImage`或`CGImage`提供圖層的內容。
- `BackgroundColor` -設定為圖層的背景色彩 `CGColor`
- `BorderWidth` -設定的框線寬度。
- `BorderColor` -設定的框線色彩。

若要利用應用程式的 UI 中的核心圖形，它必須使用_層支援_Apple 建議開發人員最好永遠啟用視窗的內容檢視中的檢視。 如此一來，所有的子檢視會自動繼承層備份以及。

此外，Apple 建議使用而不加入新的圖層支援檢視`CALayer`副層為因為系統會自動處理數個必要的設定 （例如所需的 Retina 顯示器）。

可以藉由設定啟用備份的層`WantsLayer`的`NSView`要`true`也在 Xcode 的 Interface Builder**檢視效果偵測器**藉由檢查**Core 動畫層**:

[![](modern-cocoa-apps-images/content09.png "檢視效果偵測器")](modern-cocoa-apps-images/content09.png#lightbox)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>重繪個層級的檢視

另一個重要步驟設定在 Xamarin.Mac 應用程式使用圖層支援的檢視時`LayerContentsRedrawPolicy`的`NSView`要`OnSetNeedsDisplay`在`NSViewController`。 例如: 

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

每當變更其框架的來源時，如果開發人員未設定這個屬性，將會重繪檢視其中不想要基於效能考量。 此屬性設定為`OnSetNeedsDisplay`開發人員以手動方式將需要設定`NeedsDisplay`到`true`以強制重繪，不過內容。

當檢視標示為 「 中途 」 時，系統會檢查`WantsUpdateLayer`檢視的屬性。 如果它傳回`true`則`UpdateLayer`呼叫的方法，否則`DrawRect`會呼叫檢視的方法來更新檢視的內容。

Apple 已更新的檢視內容時所需的下列建議：

- Apple 會優先使用`UpdateLater`透過`DrawRect`每當因為它可能會提供大幅提升效能。
- 使用相同`layer.Contents`看起來類似的 UI 項目。
- Apple 也慣用的開發人員撰寫使用標準的檢視，例如其 UI `NSTextField`、 再次盡可能。

若要使用`UpdateLayer`，建立的自訂類別`NSView`並讓程式碼，如下所示：

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

## <a name="using-modern-drag-and-drop"></a>使用新式儲存拖放功能

若要呈現使用者新式拖放體驗，開發人員應該採用_拖曳群群聚_其應用程式中拖放作業。 拖曳 Flocking 是其中每個個別的檔案或一開始被拖曳的項目會顯示為個別的項目，草 （群組在一起游標下的項目數目的計數），使用者即繼續拖曳作業。

如果使用者終止拖曳作業，將 Unflock 的個別項目，並將其傳回至其原始位置。

下列範例程式碼可讓拖曳群群聚在自訂檢視：

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

藉由傳送要拖曳到每個項目已達到 flocking 的效果`BeginDraggingSession`方法的`NSView`做為陣列中的個別項目。

使用時`NSTableView`或是`NSOutlineView`，使用`PastboardWriterForRow`方法`NSTableViewDataSource`開始拖曳作業的類別：

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

這可讓開發人員提供個人`NSDraggingItem`每個項目中的資料表，而不是較舊的方法被拖曳`WriteRowsWith`，寫入所有資料列做為單一群組剪貼板。

使用時`NSCollectionViews`，再使用`PasteboardWriterForItemAt`方法，而不是`WriteItemsAt`方法拖曳時開始。

開發人員能免則免放上剪貼板的大型檔案。 Macos Sierra，新_檔案承諾_可讓開發人員將參考放在要給予檔案會稍後在使用者完成使用新的拖放作業時才完成剪貼板`NSFilePromiseProvider`和`NSFilePromiseReceiver`類別。

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>使用最新的事件追蹤

使用者介面項目 (例如`NSButton`) 已加入至 [標題] 或 [工具列] 區域中，使用者應該能夠按一下的項目，並使其引發事件，如同標準 （例如顯示快顯視窗）。 不過，由於項目也是在 [標題] 或 [工具列] 區域中，使用者應該能夠按一下並拖曳以移動視窗以及項目。

若要在程式碼中這麼做，建立自訂項目 (例如`NSButton`)，並覆寫`MouseDown`事件，如下所示：

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

此程式碼會使用`TrackEventsMatching`方法`NSWindow`UI 項目附加至攔截`LeftMouseUp`和`LeftMouseDragged`事件。 針對`LeftMouseUp`正常回應的事件，UI 項目。 針對`LeftMouseDragged`事件，事件傳遞給`NSWindow`的`PerformWindowDrag`移動視窗在螢幕上的方法。

呼叫`PerformWindowDrag`方法的`NSWindow`類別提供下列優點：

- 它可讓視窗中，若要移動，即使應用程式已停止 (例如當處理深入迴圈)。
- 空間切換將如預期運作。
- [空格] 列會顯示為正常。
- 貼齊的視窗和對齊方式正常運作。

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>使用新式容器檢視控制項

macOS Sierra 提供給舊版作業系統中可用的現有容器檢視控制項的許多現代的增強功能。

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>資料表檢視表增強功能

開發人員應該一律使用新`NSView`這類架構版本的控制項檢視`NSTableView`。 例如: 

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

這可讓您自訂要附加至指定的資料表 （例如撥動由右至刪除的資料列） 的資料列的資料表資料列動作。 若要啟用此行為，請覆寫`RowActions`方法的`NSTableViewDelegate`:

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

靜態`NSTableViewRowAction.FromStyle`用來建立新的資料表資料列動作的下列樣式：

- `Regular` -執行標準的非破壞性動作，例如 編輯資料列的內容。
- `Destructive` -執行破壞性的動作，例如刪除資料列的資料表中。 這些動作將會呈現紅色背景。

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>捲軸檢視表增強功能 

使用捲動檢視時 (`NSScrollView`) 直接或做為另一個控制項的一部分 (例如`NSTableView`)，捲動檢視的內容可以投影片標題和工具列在 Xamarin.Mac 應用程式中使用新式查詢和檢視區域。

如此一來，捲動檢視內容 區域中的第一個項目可以藉此部分遮蔽的標題和工具列區域中。

若要修正此問題，Apple 已新增兩個新的屬性，以`NSScrollView`類別：

- `ContentInsets` -可讓開發人員提供`NSEdgeInsets`物件，定義將套用至捲動檢視頂端的位移。
- `AutomaticallyAdjustsContentInsets` -如果`true`捲動檢視會自動處理`ContentInsets`開發人員。

使用`ContentInsets`開發人員可以調整捲軸檢視，以便包含 附屬應用程式的這類開頭：

- 排序指標，例如郵件應用程式中所示。
- [搜尋] 欄位中。
- 重新整理 或 更新 按鈕。

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>自動版面配置和現代化應用程式中的當地語系化

Apple 已包含數種技術可讓開發人員輕鬆地建立國際化的 macOS 應用程式的 Xcode 中。 Xcode 現在可讓開發人員從應用程式的使用者介面設計在分鏡腳本檔分隔使用者端文字，並提供工具來維護這種區隔，在 UI 變更時。

如需詳細資訊，請參閱 Apple[國際化和當地語系化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>實作基底的國際化 

藉由實作基底國際化，開發人員可以提供單一的分鏡腳本檔案，來代表應用程式的 UI，並區隔出所有的使用者互動的字串。 

當開發人員會建立初始的分鏡腳本檔案 （或檔案），定義應用程式的使用者介面，它們便會建置在基底國際化 （開發人員使用的語言）。

接下來，開發人員可以匯出當地語系化和可以翻譯成多種語言的基底國際化字串 （以分鏡腳本 UI 設計）。

更新版本中，可以匯入這些當地語系化和 Xcode 會產生特定語言字串檔案的分鏡腳本。

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>實作以支援當地語系化的自動版面配置

因為當地語系化版本的字串值可以有大為不同大小和/或讀取方向，開發人員應該使用自動版面配置定位和調整大小在分鏡腳本檔案中的應用程式的使用者介面。

Apple 建議執行下列動作：

- **移除固定寬度條件約束**-所有以文字為基礎的檢視應允許調整大小，根據其內容。 固定寬度 檢視，可能會裁剪其內容以特定語言。
- **使用內建函式的內容大小**-依預設以文字為基礎的檢視會自動調整大小以符合其內容。 提供的文字為基礎的檢視，會不正確的調整大小，請在 Xcode 的 Interface Builder 中選取它們，然後選擇**編輯** > **大小來容納內容**。
- **適用於前置和尾端的屬性**文字的方向可以變更，因為根據使用者的語言，使用新`Leading`並`Trailing`相對於現有的條件約束屬性`Right`和`Left`屬性。 `Leading` 和`Trailing`會自動調整根據語言的方向。
- **釘選的檢視，以相鄰檢視**-這可讓檢視表來定位並調整大小，因為其周圍的檢視變更所選取的語言。
- **未設定 Windows 的最小值和/或最大大小**-允許的 Windows 變更大小，隨著選取的語言調整其內容的區域。
- **測試持續配置變更**-期間應持續測試開發的應用程式，以不同的語言。 請參閱 Apple[測試您的國際化的應用程式](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1)文件，如需詳細資訊。
- **使用要一起 Pin 檢視 NSStackViews**  -  `NSStackViews`允許轉移，並以可預測的方式拓展其內容與內容變更所選取的語言為基礎的大小。

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的 Interface Builder 中當地語系化

Apple 提供的幾項功能 Xcode 的 Interface Builder，開發人員可以使用在設計或編輯應用程式的 UI 支援當地語系化。 **文字方向**一節**屬性偵測器**可讓開發人員提供應該如何使用和更新選取的文字為基礎的檢視上的方向上的提示 (例如`NSTextField`):

[![](modern-cocoa-apps-images/content10.png "文字方向選項")](modern-cocoa-apps-images/content10.png#lightbox)

有三個可能的值，如**文字方向**:

- **自然**-版面配置根據指派給控制項的字串。
- **由左到右**-版面配置會永遠強制從左到右。
- **由右至左**-版面配置會永遠強制從右至左。

有兩個可能的值，如**版面配置**:

- **由左到右**-配置一律由左到右。
- **由右至左**-配置一律是由右至左。

通常這些不應該變更除非需要特定的對齊方式。

**鏡像**屬性會告知系統翻轉特定的控制項屬性 （例如儲存格的映像位置）。 有三個可能的值：

- **自動**-位置會自動將變更根據選取的語言的方向。
- **從右到左介面中**-從右至左的基礎語言中，只變更位置。
- **永遠不會**-永遠不會變更位置。

如果開發人員指定**Center**， **Justify**或是**完整**內容對齊方式的文字為基礎的檢視，絕對不會根據已翻轉選取的語言。

在 macOS Sierra 之前程式碼中建立的控制項就無法鏡像自動。 開發人員則必須使用如下所示的程式碼來處理鏡像：

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

其中`Alignment`並`ImagePosition`設定根據`UserInterfaceLayoutDirection`的控制項。

macOS Sierra 新增數個新的便利建構函式 (透過靜態`CreateButton`方法) 採用數個參數 （例如標題、 影像和動作） 並將自動左右反轉正確。 例如: 

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>使用系統的外觀

現代化的 macOS 應用程式可以採用新的深色介面外觀，很適合映像的建立、 編輯或簡報的應用程式：

[![](modern-cocoa-apps-images/content11.png "深色的 Mac 視窗 UI 的範例")](modern-cocoa-apps-images/content11.png#lightbox)

這可以藉由新增一行程式碼，在呈現視窗之前完成。 例如: 

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

靜態`GetAppearance`方法`NSAppearance`類別用來從系統取得具名的外觀 (在此情況下`NSAppearance.NameVibrantDark`)。

Apple 會有下列建議使用系統的外觀：

- 命名的色彩，而不用硬式編碼值 (例如`LabelColor`和`SelectedControlColor`)。
- 可能的話，請使用系統標準控制項的樣式。

已啟用的 系統偏好設定應用程式的協助工具功能的使用者，使用系統外觀的 macOS 應用程式將會自動正常運作。 如此一來，Apple 建議開發人員，應該一律使用系統外觀 macOS 應用程式中。

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>使用分鏡腳本設計 Ui

分鏡腳本可讓開發人員不只是個別的元件，讓應用程式的使用者介面，但以視覺化方式檢視和設計 UI 流程的設計和指定的項目階層架構。

控制站可讓開發人員項目收集至組合和 Segue 抽象單位，並移除一般 「 黏附程式碼 」，才能在檢視階層移動：

[![](modern-cocoa-apps-images/content12.png "編輯 Xcode 的 Interface Builder 中的 UI")](modern-cocoa-apps-images/content12.png#lightbox)

如需詳細資訊，請參閱我們[分鏡腳本簡介](~/mac/platform/storyboards/index.md)文件。

有許多情況下，分鏡腳本中定義的指定的場景會需要從先前的場景檢視階層中的資料。 Apple 有場景之間傳遞資訊的下列建議：

- 資料 dependancies 應該一律會重疊顯示向下整個階層。
- 避免硬式編碼 UI 結構 dependancies，因為這會限制 UI 彈性。
- 使用C#介面，以提供泛用資料 dependancies。

檢視控制器做為來源的 Segue，可以覆寫`PrepareForSegue`方法，並執行任何初始設定 （例如傳遞資料） 所需的 Segue 之前執行用來顯示目標檢視控制器。 例如: 

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

如需詳細資訊，請參閱我們[Segue](~/mac/platform/storyboards/indepth.md#Segues)文件。

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>傳播動作

根據 macOS 應用程式的設計，有時候可能會當最佳的處理常式，在 UI 控制項上的動作可能在其他地方 UI 階層架構中。 這是通常為 true，功能表和功能表項目存在於自己場景，不同於其他的應用程式的 UI 中的項目。

若要處理這種情況下，開發人員可以建立自訂動作，並傳遞回應者鏈結的動作。 如需詳細資訊，請參閱我們[使用自訂視窗動作](~/mac/user-interface/menu.md)文件。

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>現代的 Mac 功能

Apple 已包含數個使用者面向功能可讓開發人員充分利用在 Mac 平台，這類的 macOS Sierra 中：

- **NSUserActivity** -這可讓應用程式，以說明使用者目前參與的活動。 `NSUserActivity` 一開始建立以支援遞移式，無法挑選並繼續在其他裝置上啟動其中一個使用者的裝置上的活動。 `NSUserActivity` 相同的 macOS 作為它未在 iOS 中的運作方式，因此請參閱我們[遞移式簡介](~/ios/platform/handoff.md)iOS 文件，如需詳細資訊。
- **在 Mac 上的 Siri** -Siri 使用目前的活動 (`NSUserActivity`)，內容提供給使用者可以發出的命令。
- **狀態還原**-當使用者自動結束應用程式在 macOS 上，稍後再重新啟動它，應用程式會自動傳回至其先前的狀態。 開發人員可以使用狀態還原 API 來編碼和使用者介面顯示給使用者之前，還原暫時性的 UI 狀態。 如果應用程式是`NSDocument`架構，狀態還原會自動處理。 若要啟用狀態還原為非`NSDocument`根據應用程式，設定`Restorable`的`NSWindow`類別`true`。
- **在雲端中的文件**-macOS Sierra 前，應用程式必須以明確地加入至使用中使用者的 iCloud 磁碟機的文件。 在 macOS Sierra 使用者的**桌面**並**文件**資料夾可能會由系統自動同步具有其 iCloud Drive。 如此一來，可能會刪除文件的本機複本，以釋出使用者的電腦上的空間。 `NSDocument` 根據應用程式會自動處理這項變更。 所有其他應用程式類型將會需要使用`NSFileCoordinator`同步讀取和寫入的文件。

<a name="Summary" />

## <a name="summary"></a>總結

本文涵蓋幾個秘訣、 功能和技術的開發人員可用來建置現代化的 macOS 中的應用程式 Xamarin.Mac。



## <a name="related-links"></a>相關連結

- [macOS 範例](https://developer.xamarin.com/samples/mac/)
