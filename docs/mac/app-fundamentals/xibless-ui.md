---
title: xamarin.mac.storyboard/.xib-less 使用者介面設計
description: 本文涵蓋建立 Xamarin.Mac 應用程式的使用者介面，直接從C#程式碼，而不需要.storyboard 檔案、.xib 檔案或介面產生器。
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 076c6464359a58c2b36d157d9620673b0644cd4a
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459833"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>xamarin.mac.storyboard/.xib-less 使用者介面設計

_本文涵蓋建立 Xamarin.Mac 應用程式的使用者介面，直接從C#程式碼，而不需要.storyboard 檔案、.xib 檔案或介面產生器。_

## <a name="overview"></a>總覽

當使用C#和.NET Xamarin.Mac 應用程式中，您可以存取相同的使用者介面項目與工具工作的開發人員*Objective C*並*Xcode*沒有。 一般而言，在建立 Xamarin.Mac 應用程式時，您將使用 Xcode 的 Interface Builder.storyboard 或.xib 檔案來建立和維護您應用程式的使用者介面。

您也可以選擇建立部分或全部的 Xamarin.Mac 應用程式的 UI 直接在C#程式碼。 在本文中，我們將討論建立使用者介面和 UI 項目中的基本概念C#程式碼。

[![Visual Studio for Mac 的程式碼編輯器](xibless-ui-images/intro01.png "Visual Studio for Mac 的程式碼編輯器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>切換視窗，以使用程式碼

當您建立新的 Xamarin.Mac 的 Cocoa 應用程式時，您會取得預設的標準的空白，視窗。 此 windows 定義於**Main.storyboard** (或傳統**MainWindow.xib**) 會自動包含在專案中的檔案。 這也包括**ViewController.cs**管理應用程式的主要檢視的檔案 (或傳統上一次**MainWindow.cs**並**MainWindowController.cs**檔案)。

若要切換至應用程式的 Xibless 視窗，執行下列作業：

1. 開啟您想要停止使用該應用程式`.storyboard`或.xib 檔案，用於定義在 Visual Studio 中的使用者介面，for mac。
2. 在  **Solution Pad**，以滑鼠右鍵按一下**Main.storyboard**或是**MainWindow.xib**檔案，然後選取**移除**: 

    ![移除 [主要腳本] 或視窗](xibless-ui-images/switch01.png "移除主要腳本或視窗")
3. 從**移除對話方塊**，按一下**刪除**.storyboard 或.xib 完全從專案中移除的按鈕： 

    ![確認刪除](xibless-ui-images/switch02.png "確認刪除")

現在我們需要修改**MainWindow.cs**定義視窗的版面配置及修改的檔案**ViewController.cs**或是**MainWindowController.cs**檔案以建立執行個體我們`MainWindow`類別，因為我們不會再使用.storyboard 或.xib 檔案。

現代的 Xamarin.Mac 應用程式，其使用者介面可能不會自動包含使用分鏡腳本**MainWindow.cs**， **ViewController.cs**或**MainWindowController.cs**檔案。 視需要，只要將新的空C#專案的類別 (**新增** > **新增檔案...**  > **一般** > **空類別**) 並將它命名為遺漏的檔案相同。 


### <a name="defining-the-window-in-code"></a>程式碼定義視窗

接著，編輯**MainWindow.cs**檔案，並使它看起來如下：

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

讓我們討論幾個重要的項目。

首先，我們新增了少數_計算內容_可將做為輸出 （如同.storyboard 或.xib 檔案中建立的視窗）：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

這些可讓我們存取我們將在視窗上顯示的 UI 項目。 視窗不正在從.storyboard 或.xib 檔案擴大，因為我們需要想辦法加以具現化 (稍後，我們會看到`MainWindowController`類別)。 這就是這個新的建構函式方法的用途：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

這是我們將在此設計的視窗配置和放置建立必要的使用者介面所需的任何 UI 項目。 我們可以將任何 UI 項目加入至視窗之前，它需要_內容檢視_包含的項目：

```csharp
ContentView = new NSView (Frame);
```

這會建立一個會填滿視窗的內容檢視。 現在我們加入我們第一次的 UI 項目， `NSButton`，視窗：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

這裡要注意的第一件事是，不同於 iOS、 macOS 會使用數學標記法來定義其視窗座標系統。 因此目標原點會處於較低的左下角的視窗中，使用值提高權限，而是使用視窗的右上角。 當我們建立的新`NSButton`，我們將此考慮在內，我們會在畫面上定義其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`屬性會指示按鈕，我們想要掌握在相同的位置，從視窗頂端，時機垂直調整視窗大小。 同樣地，這是因為 (0，0) 位於左下角的視窗。

最後，`ContentView.AddSubview (ClickMeButton)`方法會將`NSButton`到內容檢視，因此它將顯示在畫面上，當應用程式執行和顯示的視窗。

接下來標籤新增至視窗將顯示的次數時，`NSButton`已按下： 

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

因為 macOS 沒有特定_標籤_UI 項目，我們新增了特別的樣式，不可編輯`NSTextField`做為標籤。 就像按鈕，然後再，考量此大小和位置會採用該 (0，0) 位於左下角的視窗。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin`屬性會使用**或是**運算子以結合兩個`NSViewResizingMask`功能。 這會讓停留在視窗頂端的相同位置，當垂直調整視窗大小和縮小，並隨著寬度水平調整視窗大小的標籤。

同樣地，`ContentView.AddSubview (ClickMeLabel)`方法會將`NSTextField`到內容檢視，因此它將會顯示在畫面上，執行應用程式和視窗開啟時。


### <a name="adjusting-the-window-controller"></a>調整視窗控制器

設計，因為`MainWindow`不會再載入從.storyboard 或.xib 檔案，我們需要做一些調整視窗的控制站。 編輯**MainWindowController.cs**檔案，並使它看起來如下：

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

可讓討論這項修改的索引鍵的項目。

首先，我們定義的新執行個體`MainWindow`類別，並將它指派給基底的視窗控制站的`Window`屬性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我們會定義視窗的畫面位置`CGRect`。 就像視窗座標系統中，螢幕會定義為較低的左下角 (0，0)。 接下來，我們使用，會在定義視窗的樣式時**或是**運算子以結合二或多個`NSWindowStyle`功能：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

下列`NSWindowStyle`功能都可使用：

- **無框線**-視窗會有沒有框線。
- **標題為**-視窗會有標題列。
- **Closable** -視窗已關閉 按鈕，並可被關閉。
- **Miniaturizable** -視窗具有 Miniaturize 按鈕，並可以降到最低。
- **可調整大小**-視窗會有一個調整大小的按鈕，而且是可調整大小。
- **公用程式**-視窗是公用程式的樣式視窗 （面板）。
- **DocModal** -如果視窗是一個面板，則是文件強制回應而不是系統強制回應。 
- **NonactivatingPanel** -如果視窗是一個面板，它將不會成為主視窗。
- **TexturedBackground** -視窗會顯示有材質的背景。
- **無縮放**-視窗也不會隨著調整。
- **UnifiedTitleAndToolbar** -即將加入視窗的標題和工具列區域。
- **抬頭顯示器**-視窗將會顯示為抬頭顯示面板。
- **FullScreenWindow** -視窗可以進入全螢幕模式。
- **FullSizeContentView** -標題和工具列區域位於視窗的內容檢視。

最後兩個屬性會定義_緩衝處理的型別_視窗以及是否會延後視窗的繪圖。 如需詳細資訊`NSWindows`，請參閱 Apple[簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)文件。

最後，視窗不正在從.storyboard 或.xib 檔案擴大，因為我們需要模擬它在我們**MainWindowController.cs**經由呼叫 windows`AwakeFromNib`方法：

```csharp
Window.AwakeFromNib ();
```

這可讓您只是視窗的程式碼像標準的視窗，從.storyboard 或.xib 檔案載入。


### <a name="displaying-the-window"></a>顯示視窗

使用.storyboard 或.xib 檔案移除和**MainWindow.cs**並**MainWindowController.cs**檔案修改，您將使用視窗就像任何標準的視窗中所建立Xcode 的 Interface Builder.xib 檔案。

下列會建立視窗和它的控制站的新執行個體，並在螢幕上顯示的視窗：

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此時，如果應用程式執行，並按下的按鈕數次，以下將會顯示：

![執行範例應用程式](xibless-ui-images/run01.png "執行範例應用程式")


## <a name="adding-a-code-only-window"></a>加入程式碼的唯一視窗

如果我們想要將僅限程式碼、 xibless 視窗新增至現有的 Xamarin.Mac 應用程式，以滑鼠右鍵按一下專案中**Solution Pad** ，然後選取**新增** > **新檔...**.在 **新的檔案**對話方塊中選擇**Xamarin.Mac** > **控制器的 Cocoa 視窗**，如下所示：

![加入新的視窗控制器](xibless-ui-images/add01.png "新增新的視窗控制器") 

就像之前，我們將從專案刪除預設.storyboard 或.xib 檔案 (在此情況下**SecondWindow.xib**) 並遵循[切換視窗，以使用程式碼](#Switching_a_Window_to_use_Code)上述涵蓋一節程式碼視窗的定義。


## <a name="adding-a-ui-element-to-a-window-in-code"></a>將 UI 項目新增至程式碼中的視窗

是否已在程式碼中建立視窗，或從.storyboard 或.xib 檔案載入，可能會有時間我們想要用來將 UI 項目加入至視窗中，從程式碼。 例如: 

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上述程式碼會建立新`NSButton`並將它加入`MyWindow`顯示的視窗執行個體。 基本上可以在 Xcode 的 Interface Builder.storyboard 或.xib 檔案中定義的任何 UI 項目可以在程式碼中建立，並顯示在視窗中。


## <a name="defining-the-menu-bar-in-code"></a>程式碼中定義的功能表列

由於 Xamarin.Mac 中目前的限制，不建議您在建立 Xamarin.Mac 應用程式的功能表列 –`NSMenuBar`– 在程式碼，但繼續使用**Main.storyboard**或**MainMenu.xib**檔案，以定義它。 話雖如此，您可以新增和移除功能表和功能表項目在C#程式碼。

例如，編輯**AppDelegate.cs**檔案，並讓`DidFinishLaunching`方法外觀如下所示：

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

上述程式碼從建立狀態列的功能表，並顯示應用程式啟動時。 如需有關使用功能表的詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)文件。


## <a name="summary"></a>總結

本文所深入了解建立 Xamarin.Mac 應用程式的使用者介面，在C#程式碼，而不是使用 Xcode 的 Interface Builder.storyboard 或.xib 檔案。



## <a name="related-links"></a>相關連結

- [MacXibless （範例）](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
