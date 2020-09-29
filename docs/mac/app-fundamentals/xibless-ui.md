---
title: ： xib-Xamarin 中較少的使用者介面設計
description: '本文說明如何直接從 c # 程式碼建立 Xamarin 應用程式的使用者介面，而不需要分鏡腳本檔案、xib 檔或 Interface Builder。'
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 47025f48b1f3455f70a077f2ca55da2cdeeba761
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430892"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>： xib-Xamarin 中較少的使用者介面設計

_本文說明如何直接從 c # 程式碼建立 Xamarin 應用程式的使用者介面，而不需要分鏡腳本檔案、xib 檔或 Interface Builder。_

## <a name="overview"></a>概觀

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *c* 和 *Xcode* 中運作的相同使用者介面元素和工具。 一般來說，建立 Xamarin 的應用程式時，您將使用 Xcode 的 Interface Builder 搭配 xib 檔案來建立和維護應用程式的使用者介面。

您也可以選擇直接在 c # 程式碼中建立部分或全部 Xamarin 應用程式的 UI。 在本文中，我們將討論在 c # 程式碼中建立使用者介面和 UI 元素的基本概念。

[![Visual Studio for Mac 程式碼編輯器](xibless-ui-images/intro01.png "Visual Studio for Mac 程式碼編輯器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code"></a>

## <a name="switching-a-window-to-use-code"></a>切換視窗以使用程式碼

當您建立新的 Xamarin Cocoa 應用程式時，預設會取得標準空白的視窗。 此視窗定義于主要的 **.** 分鏡腳本 (或傳統上，會自動包含在專案中的 **MainWindow. xib**) 檔。 這也包含 **ViewController.cs** 檔，可管理應用程式的主視圖 (或傳統上) 的 **MainWindow.cs** 和 **MainWindowController.cs** 檔。

若要切換至應用程式的 Xibless 視窗，請執行下列動作：

1. 開啟您想要停止使用的應用程式， `.storyboard` 或 xib 檔案，以定義 Visual Studio for Mac 中的使用者介面。
2. 在 **Solution Pad**中，以滑鼠右鍵按一下 **主要** 的 **xib** 檔案，然後選取 [ **移除**]：

    ![移除主要腳本或視窗](xibless-ui-images/switch01.png "移除主要腳本或視窗")
3. 從 [ **移除] 對話方塊**中，按一下 [ **刪除** ] 按鈕，從專案中完整移除腳本或. xib：

    ![確認刪除](xibless-ui-images/switch02.png "確認刪除")

現在，我們需要修改 **MainWindow.cs** 檔案來定義視窗的版面配置，並修改 **ViewController.cs** 或 **MainWindowController.cs** 檔案來建立類別的實例， `MainWindow` 因為我們不再使用. 腳本或 xib 檔案。

針對其使用者介面使用分鏡腳本的新式 Xamarin 應用程式，可能不會自動包含 **MainWindow.cs**、 **ViewController.cs** 或 **MainWindowController.cs** 檔案。 必要時，只要將新的空白 c # 類別加入至專案** (新增**檔案  >  **...**  > **一般**  > **空白類別**) ，並將其命名為遺失的檔案。

### <a name="defining-the-window-in-code"></a>在程式碼中定義視窗

接著，請編輯 **MainWindow.cs** 檔案，使其看起來如下所示：

```csharp
using System;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindow : NSWindow
    {
        #region Private Variables
        private int NumberOfTimesClicked = 0;
        #endregion

        #region Computed Properties
        public NSButton ClickMeButton { get; set;}
        public NSTextField ClickMeLabel { get ; set;}
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }

        public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
            // Define the user interface of the window here
            Title = "Window From Code";

            // Create the content view for the window and make it fill the window
            ContentView = new NSView (Frame);

            // Add UI elements to window
            ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
                AutoresizingMask = NSViewResizingMask.MinYMargin
            };
            ContentView.AddSubview (ClickMeButton);

            ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
                BackgroundColor = NSColor.Clear,
                TextColor = NSColor.Black,
                Editable = false,
                Bezeled = false,
                AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
                StringValue = "Button has not been clicked yet."
            };
            ContentView.AddSubview (ClickMeLabel);
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Wireup events
            ClickMeButton.Activated += (sender, e) => {
                // Update count
                ClickMeLabel.StringValue = (++NumberOfTimesClicked == 1) ? "Button clicked one time." : string.Format("Button clicked {0} times.",NumberOfTimesClicked);
            };
        }
        #endregion

    }
}
```

讓我們來討論一些重要的元素。

首先，我們新增了一些 _計算屬性_ ，這些屬性會像是在) xib 檔案中建立的視窗一樣 (：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

這些會讓我們存取要顯示在視窗上的 UI 元素。 因為視窗不是從 xib 檔案放大，所以我們需要一個方法來具現化它 (，我們稍後會在 `MainWindowController` 類別) 中看到。 這就是這個新的方法：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

我們將在此設計視窗的版面配置，並放置建立必要使用者介面所需的任何 UI 元素。 在我們可以將任何 UI 元素新增至視窗之前，它需要 _內容視圖_ 才能包含元素：

```csharp
ContentView = new NSView (Frame);
```

這會建立將填滿視窗的內容視圖。 現在，我們會將第一個 UI 元素（ `NSButton` ）新增至視窗：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

首先要注意的是，與 iOS 不同的是，macOS 會使用數學標記法來定義其視窗座標系統。 因此原點位於視窗的左下角，且值會向右對齊，而靠近視窗的右上角。 當我們建立新的時 `NSButton` ，我們會將此納入考慮，因為我們會在螢幕上定義其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`當視窗的垂直調整大小時，屬性會指示按鈕，讓它保持在視窗頂端的相同位置。 同樣地，這是必要的，因為 (0、0) 位於視窗的左下方。

最後，此 `ContentView.AddSubview (ClickMeButton)` 方法會將加入 `NSButton` 至內容視圖，以便在執行應用程式並顯示視窗時，在螢幕上顯示該方法。

接下來會將標籤新增至視窗，以顯示已按下的次數 `NSButton` ：

```csharp
ClickMeLabel = new NSTextField (new CGRect (120, Frame.Height - 65, Frame.Width - 130, 20)) {
    BackgroundColor = NSColor.Clear,
    TextColor = NSColor.Black,
    Editable = false,
    Bezeled = false,
    AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin,
    StringValue = "Button has not been clicked yet."
};
ContentView.AddSubview (ClickMeLabel);
```

由於 macOS 沒有特定的 _標籤_ UI 元素，因此我們新增了特殊樣式的非編輯， `NSTextField` 以作為標籤。 就像之前的按鈕一樣，大小和位置會考慮 (0、0) 位於視窗的左下方。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin`屬性使用**or**運算子結合兩個 `NSViewResizingMask` 特徵。 這會讓標籤與視窗頂端的相同位置保持一致，因為視窗會水準調整大小，並在寬度的同時縮小和放大。

同樣地，此 `ContentView.AddSubview (ClickMeLabel)` 方法會將加入 `NSTextField` 至內容視圖，以便在執行應用程式並開啟視窗時顯示在畫面上。

### <a name="adjusting-the-window-controller"></a>調整視窗控制器

由於不 `MainWindow` 會再從 xib 檔案載入的設計，因此我們必須對視窗控制器進行一些調整。 編輯 **MainWindowController.cs** 檔案，使其看起來如下所示：

```csharp
using System;

using Foundation;
using AppKit;
using CoreGraphics;

namespace MacXibless
{
    public partial class MainWindowController : NSWindowController
    {
        public MainWindowController (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindowController (NSCoder coder) : base (coder)
        {
        }

        public MainWindowController () : base ("MainWindow")
        {
            // Construct the window from code here
            CGRect contentRect = new CGRect (0, 0, 1000, 500);
            base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);

            // Simulate Awaking from Nib
            Window.AwakeFromNib ();
        }

        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();
        }

        public new MainWindow Window {
            get { return (MainWindow)base.Window; }
        }
    }
}

```

讓我們討論這項修改的主要元素。

首先，我們會定義類別的新實例 `MainWindow` ，並將它指派給基底視窗控制器的 `Window` 屬性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我們會使用來定義畫面視窗的位置 `CGRect` 。 就像視窗的座標系統一樣，螢幕會定義 (0，0) 作為左下角。 接下來，我們會使用 **or** 運算子來結合兩個或多個功能，以定義視窗的樣式 `NSWindowStyle` ：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
```

提供下列 `NSWindowStyle` 功能：

- 無**邊框線-視窗**不會有框線。
- **標題為-視窗** 會有標題列。
- **Closable** -視窗有 [關閉] 按鈕，而且可以關閉。
- **Miniaturizable** -視窗有 [Miniaturize] 按鈕，而且可以最小化。
- 可重設**大小**-視窗會有調整大小的按鈕，而且可以調整大小。
- **公用程式** -此視窗是公用程式樣式視窗， (面板) 。
- **DocModal** -如果視窗是面板，則會是檔強制回應，而不是系統強制回應。
- **NonactivatingPanel** -如果視窗是面板，就不會成為主視窗。
- **TexturedBackground** -視窗會有紋理的背景。
- 未**縮放-不**會調整視窗。
- **UnifiedTitleAndToolbar** -視窗的標題和工具列區域將會聯結。
- **抬頭顯示器** -視窗會顯示為標題顯示面板。
- **FullScreenWindow** -視窗可進入全螢幕模式。
- **FullSizeContentView** -視窗的內容視圖位於標題和工具列區域後面。

最後兩個屬性會定義視窗的 _緩衝處理類型_ ，而如果視窗的繪製將會延後。 如需的詳細資訊 `NSWindows` ，請參閱 Apple [簡介的 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) 檔。

最後，因為視窗不是從 xib 檔案放大，所以我們需要呼叫 windows 方法在 **MainWindowController.cs** 中模擬它 `AwakeFromNib` ：

```csharp
Window.AwakeFromNib ();
```

這可讓您針對視窗撰寫程式碼，就像從腳本或 xib 檔案載入的標準視窗一樣。

### <a name="displaying-the-window"></a>顯示視窗

移除腳本或 xib 檔，並修改 **MainWindow.cs** 和 **MainWindowController.cs** 檔案後，您將會使用視窗，就像是在 Xcode 的 Interface Builder 中使用 xib 檔案建立的任何一般視窗一樣。

下列程式會建立新的視窗和控制器實例，並在畫面上顯示視窗：

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此時，如果執行應用程式，並按下按鈕兩次，則會顯示下列內容：

![執行範例應用程式](xibless-ui-images/run01.png "執行範例應用程式")

## <a name="adding-a-code-only-window"></a>新增僅限程式碼的視窗

如果我們只想要將程式碼新增至現有的 Xamarin 應用程式，請在**Solution Pad**中的專案上按一下滑鼠右鍵，然後選取 [**加入**  >  **新**檔案]。在 [**新增**檔案] 對話方塊中，選擇 [具有控制器的**Xamarin**  >  **Cocoa 視窗]**，如下所示：

![新增視窗控制器](xibless-ui-images/add01.png "新增視窗控制器")

就像之前一樣，我們會從專案中刪除預設的 xib 腳本或. 檔案 (在此案例中為 **SecondWindow. xib**) 並遵循上方的 [ [切換視窗以使用程式碼](#Switching_a_Window_to_use_Code) ] 一節中的步驟來涵蓋視窗的程式碼定義。

## <a name="adding-a-ui-element-to-a-window-in-code"></a>在程式碼中將 UI 元素新增至視窗

無論視窗是在程式碼中建立，還是從 xib 檔案載入，有時候我們可能會想要從程式碼將 UI 專案加入至視窗。 例如：

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上述程式碼會建立新的 `NSButton` ，並將它加入至 `MyWindow` 視窗實例以供顯示。 基本上，您可以在程式碼中建立任何可以在 Xcode 的 Interface Builder 中定義的 UI 元素，或 xib 檔案，並在視窗中顯示。

## <a name="defining-the-menu-bar-in-code"></a>在程式碼中定義功能表列

由於 Xamarin 目前的限制，因此不建議您在程式碼中建立 Xamarin 應用程式的功能表列– `NSMenuBar` -在程式碼中，但仍繼續使用 **Main.** 腳本或 **MainMenu. xib** 檔案來定義它。 話雖如此，您可以在 c # 程式碼中加入和移除功能表和功能表項目。

例如，編輯 **AppDelegate.cs** 檔案，讓 `DidFinishLaunching` 方法看起來如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    mainWindowController = new MainWindowController ();
    mainWindowController.Window.MakeKeyAndOrderFront (this);

    // Create a Status Bar Menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Phrases";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Phrases");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        Console.WriteLine("Address Selected");
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        Console.WriteLine("Date Selected");
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        Console.WriteLine("Greetings Selected");
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        Console.WriteLine("Signature Selected");
    };
    item.Menu.AddItem (signature);
}
```

上述程式碼會從程式碼建立狀態列功能表，並在應用程式啟動時顯示狀態列。 如需使用功能表的詳細資訊，請參閱我們的 [功能表](~/mac/user-interface/menu.md) 檔。

## <a name="summary"></a>摘要

本文詳細說明如何在 c # 程式碼中建立 Xamarin. Mac 應用程式的使用者介面，而不是使用 Xcode 的 Interface Builder 搭配 xib 檔案。

## <a name="related-links"></a>相關連結

- [MacXibless (範例) ](/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [macOS Human Interface Guidelines (人性化介面指導方針)](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [Windows 簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)