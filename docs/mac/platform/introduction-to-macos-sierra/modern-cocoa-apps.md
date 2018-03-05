---
title: "建置現代化 macOS 應用程式"
description: "本文涵蓋幾個秘訣、 功能和技術的開發人員可以使用建置中 Xamarin.Mac macOS 現代應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: F20EE590-246E-40EB-B309-D9D8C090C7F1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 9073d64c43c6817b45dca02b870fcfe093ebf46d
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="building-modern-macos-apps"></a>建置現代化 macOS 應用程式

_本文涵蓋幾個秘訣、 功能和技術的開發人員可以使用建置中 Xamarin.Mac macOS 現代應用程式。_

<a name="Building-Modern-Looks-with-Modern-Views" />

## <a name="building-modern-looks-with-modern-views"></a>建置現代化外觀與現代化的檢視

現代化外觀，將包含例如如下所示的範例應用程式的視窗和工具列現代化外觀：

[ ![](modern-cocoa-apps-images/content08.png "現代的 Mac 應用程式 UI 的範例")](modern-cocoa-apps-images/content08.png)

<a name="Enabling-Full-Sized-Content-Views" />

### <a name="enabling-full-sized-content-views"></a>啟用全尺寸內容檢視

若要達到它看起來 Xamarin.Mac 應用程式中的，開發人員會想要使用_完整內容 檢視的大小_表示延伸的工具和標題列區域底下的內容，將由 macOS 自動模糊。

若要啟用這項功能，在程式碼中的，建立的自訂類別`NSWindowController`並看起來如下所示：

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

這項功能也可以啟用在 Xcode 的介面產生器中選取視窗，並檢查**完整大小的內容檢視**:

[ ![](modern-cocoa-apps-images/content01.png "編輯在 Xcode 的介面產生器中主要的分鏡腳本")](modern-cocoa-apps-images/content01.png)

如果使用完整的大小內容檢視，開發人員可能需要位移下方的標題和工具列區域的內容，使其下的特定內容 （例如標籤） 不投影片。

此問題變得複雜，標題和工具列區域可以有動態的高度，根據使用者目前正在執行，動作 macOS 使用者已安裝和/或 Mac 硬體上執行應用程式的版本。

如此一來，當使用者介面的版面配置時，只是硬式編碼位移無法運作。 開發人員必須採用動態方法。

Apple 已包含[機碼值可觀察](~/mac/app-fundamentals/databinding.md#Observing_Value_Changes)`ContentLayoutRect`屬性`NSWindow`取得程式碼中目前的內容區域的類別。 開發人員可以使用此值，以手動方式放置必要的項目內容的區域變更時。

更好的解決方案是使用自動配置和大小類別定位程式碼或介面產生器中的 UI 項目。

如下列範例程式碼可以用來定位 UI 項目使用的應用程式檢視控制器中的自動版面配置和大小類別：

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

此程式碼會建立儲存體將會套用到標籤為最上層條件約束 (`ItemTitle`) 以確保它不會略過下標題和工具列區域：

```csharp
public NSLayoutConstraint topConstraint { get; set; }
```

藉由覆寫檢視控制站的`UpdateViewConstraints`方法，開發人員可以測試以查看是否已建置所需的條件約束並視需要建立它。

如果新的條件約束需要建置，`ContentLayoutGuide`屬性視窗控制項必須要限制存取資料並轉換成`NSLayoutGuide`:

```csharp
var contentLayoutGuide = ItemTitle.Window?.ContentLayoutGuide as NSLayoutGuide;
```

TopAnchor 屬性`NSLayoutGuide`存取，而如果有的話，它用來建立新的條件約束與想要的位移數量和新的條件約束由作用中將它套用至：

```csharp
// Assemble constraint and activate it
topConstraint = topAnchor.ConstraintEqualToAnchor (topAnchor, 20);
topConstraint.Active = true;
```

<a name="Enabling-Streamlined-Toolbars" />

### <a name="enabling-streamlined-toolbars"></a>啟用簡化的工具列

一般 macOS 視窗包含位於標題列執行視窗頂端的標準。 如果視窗也會包含的工具列，它將會顯示此標題列區域底下：

[ ![](modern-cocoa-apps-images/content02.png "標準的 Mac 工具列")](modern-cocoa-apps-images/content02.png)

當使用簡化的工具列，標題區會消失，工具列向上移動至標題列的位置，內建的視窗關閉、 最小化和最大化按鈕：

[ ![](modern-cocoa-apps-images/content03.png "簡化的 Mac 工具列")](modern-cocoa-apps-images/content03.png)

藉由覆寫啟用簡化工具列`ViewWillAppear`方法`NSViewController`並讓它看起來像下面這樣：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Enable streamlined Toolbars
    View.Window.TitleVisibility = NSWindowTitleVisibility.Hidden;
}
```

這個效果通常用於_鞋盒應用程式_（一個視窗應用程式） 例如對應、 行事曆、 資訊和系統喜好設定。 

<a name="Using-Accessory-View-Controllers" />

### <a name="using-accessory-view-controllers"></a>使用配件檢視控制器

根據應用程式的設計、 開發人員也可能想要補充區域右邊提供區分內容控制項加入該活動會根據使用者他們標題/工具列區域下方任一附屬應用程式檢視控制器會在標題列目前參與：

[ ![](modern-cocoa-apps-images/content04.png "範例附屬應用程式檢視控制器")](modern-cocoa-apps-images/content04.png)

附屬應用程式檢視控制站會自動模糊並調整大小，系統不需要開發人員介入。

若要新增附屬應用程式檢視控制站，執行下列作業：

1. 在方案總管中按兩下 `Main.storyboard` 檔案將其開啟以進行編輯。
2. 拖曳**自訂檢視控制器**到視窗的階層： 

    [ ![](modern-cocoa-apps-images/content05.png "加入新的自訂檢視控制器")](modern-cocoa-apps-images/content05.png)
3. 版面配置 [配件] 檢視的 UI: 

    [ ![](modern-cocoa-apps-images/content06.png "設計新的檢視")](modern-cocoa-apps-images/content06.png)
4. 公開配件檢視**插座**和任何其他**動作**或**插座**其 ui: 

    [ ![](modern-cocoa-apps-images/content07.png "加入必要的輸出")](modern-cocoa-apps-images/content07.png)
5. 儲存變更。
6. 返回 Visual Studio for Mac 同步處理變更。

編輯`NSWindowController`並看起來如下所示：

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

這個程式碼的重點是且將檢視設為 [自訂] 檢視介面產生器中定義並公開為**插座**:

```csharp
accessoryView.View = AccessoryViewGoBar;
```

和`LayoutAttribute`定義_其中_附屬應用程式將會顯示：

```csharp
accessoryView.LayoutAttribute = NSLayoutAttribute.Bottom;
```

由於 macOS 現在完全當地語系化，`Left`和`Right``NSLayoutAttribute`屬性已被取代，應該取代成`Leading`和`Trailing`。

<a name="Using-Tabbed-Windows" />

### <a name="using-tabbed-windows"></a>使用索引標籤式的視窗

此外，macOS 系統可能會將附屬應用程式檢視控制站新增至應用程式的視窗。 例如，若要建立索引標籤式視窗的數個應用程式的 Windows 會合併到一個虛擬視窗：

[ ![](modern-cocoa-apps-images/content08.png "索引標籤式的 Mac 視窗的範例")](modern-cocoa-apps-images/content08.png)

一般而言，開發人員必須採取有限的動作使用其 Xamarin.Mac 應用程式中的索引標籤式視窗，系統將它們會自動處理，如下所示：

- Windows 會自動索引標籤式時`OrderFront`叫用方法。
- Windows 會自動將 Untabbed 時`OrderOut`叫用方法。
- 程式碼中所有索引標籤式視窗仍會視為 「 可見 」，不過任何非最上層索引標籤會顯示使用 CoreGraphics 系統。
- 使用`TabbingIdentifier`屬性`NSWindow`將視窗分組在一起成索引標籤。
- 如果是`NSDocument`根據應用程式，這些功能將不執行任何動作開發人員啟用自動 （例如新增至索引標籤工具列的加號按鈕）。
- 非`NSDocument`的基礎應用程式可以啟用"加號"中的按鈕加入新的文件，藉由覆寫索引標籤群組`GetNewWindowForTab`方法`NSWindowsController`。

將各部分結合在一起，`AppDelegate`應用程式想要使用的系統以基礎索引標籤式視窗看起來可能如下所示：

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

其中`NewDocumentNumber`追蹤的屬性建立新文件的數目和`NewDocument`方法會建立新文件，並加以顯示。

`NSWindowController`然後看起來應該像：

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

其中靜態`App`屬性提供的捷徑，若要取得`AppDelegate`。 `SetDefaultDocumentTitle`方法會設定新的文件標題，根據新建立的文件數目。

下列程式碼中，會告知應用程式會優先使用索引標籤的 macOS，並提供可讓應用程式的 Windows 索引標籤組成的字串：

```csharp
// Prefer Tabbed Windows
Window.TabbingMode = NSWindowTabbingMode.Preferred;
Window.TabbingIdentifier = "Main";
```

以及下列的覆寫方法會將加上的按鈕加入至將會建立新文件時使用者按下索引標籤列：

```csharp
public override void GetNewWindowForTab (NSObject sender)
{
    // Ask app to open a new document window
    App.NewDocument (this);
}
```

<a name="Using-Core-Animation" />

### <a name="using-core-animation"></a>使用核心動畫

核心動畫是內建於 macOS 高供電的圖形轉譯引擎。 若要善用 GPU （圖形處理單元） 提供現代 macOS 硬體，而不是執行圖形作業，可能會降低電腦的 CPU 上，已經過最佳化核心動畫。

`CALayer`核心動畫所提供，可用於快速和流暢捲動及動畫等工作。 應用程式的使用者介面是由多個 subviews 及圖層以充分利用核心動畫應組成。

A`CALayer`物件提供數個屬性，可讓開發人員控制功能呈現給使用者畫面上這類：

- `Content` -可以是`NSImage`或`CGImage`提供一層的內容。
- `BackgroundColor` -設定為圖層的背景色彩 `CGColor`
- `BorderWidth` -設定框線寬度。
- `BorderColor` -設定框線色彩。

若要利用應用程式的 UI 中的核心圖形，它必須使用_層備份_Apple 建議開發人員應該永遠啟用視窗的內容檢視中的檢視。 如此一來，所有的子檢視將會自動繼承層備份以及。

此外，Apple 建議使用圖層支援的檢視，而不加入新`CALayer`副層為因為系統將會自動處理數個必要的設定 （例如所需的 Retina 顯示器）。

可以藉由設定啟用備份的層`WantsLayer`的`NSView`至`true`或 Xcode 的介面產生器，在內部**檢視效果 Inspector**藉由檢查**核心動畫層**:

[ ![](modern-cocoa-apps-images/content09.png "檢視效果偵測器")](modern-cocoa-apps-images/content09.png)

<a name="Redrawing-Views-with-Layers" />

#### <a name="redrawing-views-with-layers"></a>重繪作業檢視與圖層

當使用 Xamarin.Mac 應用程式中的層級基礎的檢視為設定的另一個重要步驟`LayerContentsRedrawPolicy`的`NSView`至`OnSetNeedsDisplay`中`NSViewController`。 例如: 

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set the content redraw policy
    View.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

每當框架原點變更時，如果開發人員不會設定這個屬性，將會重繪 檢視其不想要基於效能的考量。 此屬性設定為`OnSetNeedsDisplay`開發人員手動就必須設定`NeedsDisplay`至`true`強制重新繪製，但是內容。

當檢視標示為中途的時系統會檢查`WantsUpdateLayer`檢視的屬性。 如果它傳回`true`則`UpdateLayer`呼叫的方法，否則`DrawRect`呼叫檢視的方法來更新檢視的內容。

Apple 已更新的檢視內容時所需的下列建議：

- Apple 偏好使用`UpdateLater`透過`DrawRect`每當因為它可能提供大幅提升效能。
- 使用相同`layer.Contents`看起來類似的 UI 項目。
- Apple 也慣用的開發人員撰寫使用標準檢視，例如其 UI `NSTextField`、 再次盡可能。

若要使用`UpdateLayer`，建立的自訂類別`NSView`並進行程式碼如下所示：

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

## <a name="using-modern-drag-and-drop"></a>使用新式儲存拖放

若要呈現使用者現代拖放體驗，開發人員應該採用_拖曳 Flocking_其應用程式中拖放作業。 拖曳 Flocking 是其中每個個別的檔案或被一開始拖曳項目會顯示為個別的項目，草 （群組在一起游標下的項目數目的計數） 為使用者繼續拖曳作業。

如果使用者終止拖曳作業時，個別的項目將 Unflock 並回到其原始位置。

下列範例程式碼可讓拖曳 Flocking 上的自訂檢視：

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

Flocking 效果藉由傳送每個項目拖曳至`BeginDraggingSession`方法`NSView`為陣列中的個別元素。

當使用`NSTableView`或`NSOutlineView`，使用`PastboardWriterForRow`方法`NSTableViewDataSource`開始拖曳作業的類別：

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

這可讓開發人員提供個人`NSDraggingItem`每個項目中的資料表，而不是較舊方法拖曳至`WriteRowsWith`，寫入所有資料列做為單一群組剪貼版。

當使用`NSCollectionViews`，一次使用`PasteboardWriterForItemAt`方法與`WriteItemsAt`方法拖曳時開始。

開發人員應該一律避免將放入剪貼版的大型檔案。 新手 macOS 利也，_檔案承諾_可讓開發人員將參考放在要給予稍後當使用者完成使用新的 Drop 作業時應滿足剪貼版上的檔案`NSFilePromiseProvider`和`NSFilePromiseReceiver`類別。

<a name="Using-Modern-Event-Tracking" />

## <a name="using-modern-event-tracking"></a>使用新式事件追蹤

使用者介面項目 (例如`NSButton`) 已加入至標題或工具列的區域，使用者應該能夠按一下項目，並引發事件 （例如顯示快顯視窗），照常進行。 不過，由於項目也是標題或工具列區域中，使用者應該能夠使用按一下並拖曳以移動視窗以及項目。

若要完成此程式碼中，建立自訂類別的項目 (例如`NSButton`) 並覆寫`MouseDown`事件，如下所示：

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

此程式碼使用`TrackEventsMatching`方法`NSWindow`UI 項目附加至攔截`LeftMouseUp`和`LeftMouseDragged`事件。 如`LeftMouseUp`事件，像平常一樣回應的 UI 項目。 如`LeftMouseDragged`事件，事件傳送至`NSWindow`的`PerformWindowDrag`移動螢幕上視窗的方法。

呼叫`PerformWindowDrag`方法`NSWindow`類別提供下列優點：

- 它可讓視窗移動，即使應用程式已停止 (例如當處理深層迴圈)。
- 切換空間將如預期運作。
- [空格] 列會顯示為一般。
- 貼齊的視窗和對齊正常運作。

<a name="Using-Modern-Container-View-Controls" />

## <a name="using-modern-container-view-controls"></a>使用新式容器檢視控制項

macOS 利也提供許多新型的增強功能到先前版本的作業系統中可用的現有容器檢視控制項。

<a name="Table View Enhancements" />

## <a name="table-view-enhancements"></a>資料表檢視表增強功能

開發人員應該一律使用新`NSView`基礎容器檢視控制項的版本，例如`NSTableView`。 例如: 

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

這可讓您自訂要附加至指定的資料表 （例如撥動由右至刪除的資料列） 的資料列的資料表資料列動作。 若要啟用這項行為，請覆寫`RowActions`方法`NSTableViewDelegate`:

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

- `Regular` -執行標準的非破壞性動作，例如編輯資料列的內容。
- `Destructive` -執行破壞性的動作，例如刪除資料列的資料表中。 這些動作將會呈現具有紅色背景。

<a name="Scroll-View-Enhancements" />

## <a name="scroll-view-enhancements"></a>捲動檢視表增強功能 

當使用捲動檢視 (`NSScrollView`) 直接管理，或做為另一個控制項的一部分 (例如`NSTableView`)，捲動檢視的內容可以在 Xamarin.Mac 應用程式中使用現代尋找和檢視表的標題和工具列區域下滑動。

如此一來，您可以部分的標題及工具列區域被遮蔽捲動檢視內容 區域中的第一個項目。

若要更正此問題，Apple 已加入兩個新的屬性，以`NSScrollView`類別：

- `ContentInsets` -可讓開發人員提供`NSEdgeInsets`物件定義將套用至捲動檢視頂端的位移。
- `AutomaticallyAdjustsContentInsets` -如果`true`捲動檢視將會自動處理`ContentInsets`開發人員。

使用`ContentInsets`開發人員可以調整捲動檢視，例如允許的附屬應用程式要納入的開頭：

- 排序標記，例如郵件應用程式中所示。
- 搜尋欄位中。
- 重新整理或更新的按鈕。

<a name="Auto-Layout-and-Localization-in-Modern-Apps" />

## <a name="auto-layout-and-localization-in-modern-apps"></a>自動配置和當地語系化的現代化應用程式

Apple 並在 Xcode 可讓開發人員輕鬆地建立國際化的 macOS 應用程式中包含數種技術。 Xcode 現在可讓開發人員分開應用程式的使用者介面設計其分鏡腳本檔案中的使用者端文字，並提供工具，可在 UI 變更時，維持此分離。

如需詳細資訊，請參閱 Apple[國際化和當地語系化指南](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/InternationalizingYourUserInterface/InternationalizingYourUserInterface.html)。

<a name="Implementing-Base-Internationalization" />

### <a name="implementing-base-internationalization"></a>實作基底的國際化 

藉由實作基底國際化，開發人員可以提供單一的分鏡腳本檔案，代表應用程式的 UI，並將所有使用者互動的字串。 

當開發人員會建立初始的分鏡腳本檔案 （或檔案） 可定義應用程式的使用者介面，將會建置在基底國際化 （開發人員來說語言）。

接下來，開發人員可以匯出當地語系化資源，可以轉譯成多種語言的基底國際化字串 （以分鏡腳本 UI 設計）。

稍後，可以匯入這些當地語系化資源，Xcode 會產生特定語言的字串檔案的分鏡腳本。

<a name="Implementing-Auto-Layout-to-Support-Localization" />

### <a name="implementing-auto-layout-to-support-localization"></a>實作自動配置來支援當地語系化

因為當地語系化版本的字串值可以有截然不同的大小和/或讀取方向，開發人員應該使用自動配置來定位和調整大小的分鏡腳本檔案中的應用程式的使用者介面。

Apple 的建議執行下列動作：

- **移除固定寬度的條件約束**-文字為基礎的所有檢視都能調整的大小根據其內容。 固定寬度檢視可能裁剪其內容以特定的語言。
- **使用內建函式的內容大小**-依預設以文字為基礎的檢視會自動調整大小以符合其內容。 提供的文字為基礎的檢視，調整大小不正確，Xcode 的介面產生器中選取它們，然後選擇 **編輯** > **大小來容納內容**。
- **適用於前置和尾端屬性**-因為變更文字的方向可以根據使用者的語言，使用新`Leading`和`Trailing`相對於現有的條件約束屬性`Right`和`Left`屬性。 `Leading` 和`Trailing`會自動調整根據語言方向。
- **釘選的檢視，以相鄰的檢視**-這可讓檢視，即可重新定位和調整大小圍繞這些檢視會變更以回應所選取的語言。
- **不需要設定 Windows 的最小值及/或最大大小**-允許變更所選取的語言調整大小，其內容的區域大小的視窗。
- **測試不斷版面配置變更**-期間開發的應用程式應該經過測試，不斷地在不同的語言。 請參閱 Apple[測試您的國際化應用程式](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPInternational/TestingYourInternationalApp/TestingYourInternationalApp.html#//apple_ref/doc/uid/10000171i-CH7-SW1)文件以取得詳細資料。
- **使用要一起 Pin 檢視 NSStackViews**  -  `NSStackViews`允許移位和可預測的方式成長到其內容及內容的變更所選取的語言為基礎的大小。

<a name="Localizing-in-Xcodes-Interface-Builder" />

### <a name="localizing-in-xcodes-interface-builder"></a>在 Xcode 的介面產生器中的當地語系化

Apple 已提供開發人員可以在設計或編輯應用程式的 UI 使用來支援當地語系化 Xcode 的介面產生器中的數個功能。 **文字方向**區段**屬性偵測器**可讓開發人員提供應該如何使用和更新的文字為基礎的檢視選取方向上的提示 (例如`NSTextField`):

[ ![](modern-cocoa-apps-images/content10.png "文字方向選項")](modern-cocoa-apps-images/content10.png)

有三個可能的值為**文字方向**:

- **自然**-版面配置根據指派給控制項的字串。
- **由左到右**-左到右一律強制配置。
- **由右至左**-一律強制由右至左配置。

有兩個可能的值為**配置**:

- **由左到右**-配置一律由左到右。
- **由右至左**-配置一律是由右至左。

通常這些不應該變更除非需要特定的對齊方式。

**鏡像**屬性會告知系統翻轉特定的控制項屬性 （例如資料格影像的位置）。 有三個可能的值：

- **自動**-位置會自動隨著所選取的語言的方向。
- **從右至左介面中**-右至左的基礎語言中，只變更位置。
- **永遠不會**-永遠不會變更位置。

如果開發人員指定**Center**， **Justify**或**完整**的對齊方式的內容以文字為基礎的檢視，絕對不會根據翻轉選取的語言。

之前 macOS 利也，程式碼中建立的控制項就無法鏡像自動。 開發人員則必須使用類似下列的程式碼來處理鏡像：

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

其中`Alignment`和`ImagePosition`正在設定根據`UserInterfaceLayoutDirection`的控制項。

macOS 利也會新增數個新的便利建構函式 (透過靜態`CreateButton`方法)，需要數個參數 （例如標題、 影像和動作），且會自動正確鏡像。 例如: 

```csharp
var button2 = NSButton.CreateButton (myTitle, myImage, () => {
    // Take action when the button is pressed
    ...
});
```

<a name="Using-System-Appearances" />

## <a name="using-system-appearances"></a>使用系統外觀

現代 macOS 應用程式可以採用新的深色介面外觀，很適合映像建立、 編輯或簡報應用程式：

[ ![](modern-cocoa-apps-images/content11.png "深色的 Mac 視窗 UI 的範例")](modern-cocoa-apps-images/content11.png)

視窗會顯示之前加入一行程式碼可以完成此動作。 例如: 

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

Apple 已使用系統顯示狀態的下列建議：

- 具名的色彩，而不用硬式編碼值 (例如`LabelColor`和`SelectedControlColor`)。
- 請盡可能使用系統標準控制項的樣式。

已啟用協助工具功能，從 系統偏好設定應用程式的使用者，使用系統外觀之 macOS 應用程式將自動正常運作。 如此一來，Apple 建議開發人員，應該永遠使用系統外觀 macOS 應用程式中。

<a name="Designing-UIs-with-Storyboards" />

## <a name="designing-uis-with-storyboards"></a>使用分鏡腳本設計 Ui

分鏡腳本可讓開發人員對應用程式的使用者介面，但以視覺化方式檢視和設計 UI 的個別項目資料流程的設計，並不只指定元素的階層。

控制站將允許開發人員的構成要素和 Segues 抽象單位將收集項目，並移除一般 「 黏附追蹤程式碼 」，才能移動階層中的檢視：

[ ![](modern-cocoa-apps-images/content12.png "編輯 Xcode 的介面產生器中的 UI")](modern-cocoa-apps-images/content12.png)

如需詳細資訊，請參閱我們[簡介分鏡腳本](~/mac/platform/storyboards/index.md)文件。

有許多執行個體，其中分鏡腳本中定義的指定的場景會需要從先前的場景，檢視階層中的資料。 Apple 有場景之間傳遞資訊的下列建議：

- 資料 dependancies 應該一律重疊顯示向下透過階層架構。
- 避免硬式編碼 UI 結構 dependancies，因為這會限制 UI 彈性。
- 您可以使用 C# 介面來提供泛用資料 dependancies。

做為來源的 Segue 檢視控制器可以覆寫`PrepareForSegue`方法並執行任何初始設定 （例如將資料傳遞） 所需 Segue 之前執行用來顯示目標檢視控制站。 例如: 

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

如需詳細資訊，請參閱我們[Segues](~/mac/platform/storyboards/indepth.md#Segues)文件。

<a name="Propagating-Actions" />

## <a name="propagating-actions"></a>傳播動作

根據 macOS 應用程式的設計，有些時候可能會當最佳的處理常式，在 UI 控制項上的動作可能會在其他地方 UI 階層架構中。 這是通常為 true，功能表和即時應用程式的 UI 中的其餘部分區隔開他們自己場景中的功能表項目。

若要處理這種情況下，開發人員可以建立自訂動作，並將傳遞回應者鏈結的動作。 如需詳細資訊，請參閱我們[使用自訂視窗動作](~/mac/user-interface/menu.md)文件。

<a name="Modern-Mac-Features" />

## <a name="modern-mac-features"></a>現代 Mac 功能

Apple 已包含數個使用者互動功能，在 macOS 利也可讓開發人員充分利用的 Mac 平台，例如：

- **NSUserActivity** -這可讓應用程式，以說明目前涉及使用者的活動。 `NSUserActivity` 一開始建立以支援的遞交無法挑選並繼續執行其他裝置上啟動其中一個使用者的裝置上的活動。 `NSUserActivity` 相同的 macOS，因為它未在 iOS 中的運作方式，請參閱我們[簡介遞交](~/ios/platform/handoff.md)iOS 文件，如需詳細資訊。
- **在 Mac 上的使用 Siri** -Siri 會使用目前的活動 (`NSUserActivity`)，內容提供給使用者發出的命令。
- **狀態還原**-當使用者會結束應用程式上 macOS，並且在稍後 relaunches 它將應用程式會自動返回先前的狀態。 開發人員可以使用狀態還原應用程式開發介面來編碼和使用者介面顯示給使用者之前，還原暫時性的 UI 狀態。 如果應用程式是`NSDocument`型，狀態還原會自動處理。 若要啟用的狀態還原非`NSDocument`基礎的應用程式設定`Restorable`的`NSWindow`類別`true`。
- **在雲端中的文件**-在之前 macOS 利也，應用程式必須明確地選擇加入以使用使用者的 iCloud 磁碟機中的文件。 在使用者利也 macOS**桌面**和**文件**資料夾可能由系統自動同步與磁碟機及其 iCloud。 如此一來，文件的本機副本可能會遭到刪除，以釋出到使用者電腦上的空間。 `NSDocument` 根據應用程式將會自動處理這項變更。 所有其他應用程式類型必須使用`NSFileCoordinator`同步讀取和寫入的文件。

<a name="Summary" />

## <a name="summary"></a>總結

本文探討幾個秘訣、 功能和技術的開發人員可以使用建置中 Xamarin.Mac macOS 現代應用程式。



## <a name="related-links"></a>相關連結

- [macOS 範例](https://developer.xamarin.com/samples/mac/)
