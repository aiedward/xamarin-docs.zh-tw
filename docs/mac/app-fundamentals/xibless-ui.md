---
title: 。 xib-Xamarin. Mac 中的使用者介面設計較少
description: 本文說明如何直接從C#程式碼建立 Xamarin. Mac 應用程式的使用者介面, 而不使用分鏡腳本檔案、xib 檔或 Interface Builder。
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 1f49f3c24bc4c89edb005206b953176639214481
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647182"
---
# <a name="storyboardxib-less-user-interface-design-in-xamarinmac"></a>。 xib-Xamarin. Mac 中的使用者介面設計較少

_本文說明如何直接從C#程式碼建立 Xamarin. Mac 應用程式的使用者介面, 而不使用分鏡腳本檔案、xib 檔或 Interface Builder。_

## <a name="overview"></a>總覽

在 Xamarin. C# Mac 應用程式中使用和 .net 時, 您可以存取開發人員在*目標-C*和*Xcode*中工作的相同使用者介面元素和工具。 一般來說, 在建立 Xamarin. Mac 應用程式時, 您會使用 Xcode 的 Interface Builder 搭配. 腳本或 xib 檔案, 以建立及維護應用程式的使用者介面。

您也可以選擇直接在程式碼中C#建立部分或所有 Xamarin. Mac 應用程式的 UI。 在本文中, 我們將討論在程式碼中C#建立使用者介面和 UI 元素的基本概念。

[![Visual Studio for Mac 程式碼編輯器](xibless-ui-images/intro01.png "Visual Studio for Mac 程式碼編輯器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>將視窗切換為使用程式碼

當您建立新的 Xamarin Cocoa 應用程式時, 預設會取得標準的空白視窗。 此視窗會在專案中自動包含的**主要**腳本 (或傳統**mainwindow.xaml. xib**) 檔案中定義。 這也包括一個**ViewController.cs**檔案, 它會管理應用程式的主視圖 (或傳統上是**MainWindow.cs**和**MainWindowController.cs**檔案)。

若要切換至應用程式的 Xibless 視窗, 請執行下列動作:

1. 開啟您想要停止使用`.storyboard`的應用程式, 或 xib 檔案以在 Visual Studio for Mac 中定義使用者介面。
2. 在  **Solution Pad**中, 以滑鼠右鍵按一下  **mainwindow.xaml xib**檔案, 然後選取 **移除**: 

    ![移除 [主要腳本] 或視窗](xibless-ui-images/switch01.png "移除主要腳本或視窗")
3. 從 [**移除] 對話方塊**中, 按一下 [**刪除**] 按鈕, 即可從專案中完全移除腳本或. xib: 

    ![確認刪除](xibless-ui-images/switch02.png "確認刪除")

現在, 我們必須修改**MainWindow.cs**檔案以定義視窗的版面配置, 並修改**ViewController.cs**或**MainWindowController.cs**檔案, 以`MainWindow`建立類別的實例, 因為我們不再使用。分鏡腳本或 xib 檔案。

針對其使用者介面使用分鏡腳本的現代化 Xamarin 應用程式, 可能不會自動包含**MainWindow.cs**、 **ViewController.cs**或**MainWindowController.cs**檔案。 視需要, 只要將新的空白C#類別加入至專案 (**加入** > 新的檔案 **...**  > 一般空白 > **類別**), 並將其命名為與遺漏檔案相同。 


### <a name="defining-the-window-in-code"></a>以程式碼定義視窗

接著, 編輯**MainWindow.cs**檔案, 使其看起來如下所示:

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

讓我們來討論幾個重要的元素。

首先, 我們新增了幾個_計算屬性_, 其作用就像是輸出 (就像是在 xib 檔案中建立視窗一樣):

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

這會讓我們存取要在視窗上顯示的 UI 元素。 由於視窗不會從 xib 檔案擴大, 因此我們需要一個方法來具現化 (我們將在稍後的`MainWindowController`類別中看到)。 這就是這個新的「函式」方法所做的動作:

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

我們將在此設計視窗的版面配置, 並放置建立必要的使用者介面所需的任何 UI 元素。 在我們可以將任何 UI 元素新增至視窗之前, 它需要_內容視圖_來包含元素:

```csharp
ContentView = new NSView (Frame);
```

這會建立將填滿視窗的內容視圖。 現在`NSButton`, 我們將第一個 UI 元素 () 新增至視窗:

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

這裡要注意的第一件事是, 與 iOS 不同的是, macOS 會使用數學標記法來定義其視窗座標系統。 因此, 原點位於視窗的左下角, 而值會向右和視窗的右上角增加。 當我們建立新`NSButton`的時, 我們會將此納入考慮, 因為我們會在螢幕上定義其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`屬性會告訴按鈕, 當視窗垂直調整大小時, 我們想要將它留在視窗頂端的相同位置。 同樣地, 這是必要的, 因為 (0, 0) 位於視窗的左下方。

最後, `ContentView.AddSubview (ClickMeButton)`方法會`NSButton`將新增至內容視圖, 使其在應用程式執行時顯示在螢幕上, 並顯示視窗。

接下來, 會將標籤新增至視窗, 以顯示已按`NSButton`下的次數: 

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

由於 macOS 沒有特定的_標籤_UI 元素, 因此我們新增了特殊樣式、不可編輯`NSTextField`的作為標籤。 就像之前的按鈕一樣, 大小和位置會考慮 (0, 0) 位於視窗的左下方。 屬性會使用**or**運算子來結合兩個`NSViewResizingMask`功能。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin` 這會讓標籤保持在視窗頂端的相同位置, 而視窗會垂直調整大小, 並在視窗重設成水準大小時縮小並擴大寬度。

同樣`NSTextField`地, `ContentView.AddSubview (ClickMeLabel)`方法會將新增至內容視圖, 使其在應用程式執行時顯示在螢幕上, 並開啟視窗。


### <a name="adjusting-the-window-controller"></a>調整視窗控制器

由於不`MainWindow`會再從 xib 檔案載入的設計, 因此我們需要對視窗控制器進行一些調整。 編輯**MainWindowController.cs**檔案, 使其看起來如下所示:

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

讓我們來討論這項修改的重要元素。

首先, 我們會定義`MainWindow`類別的新實例, 並將它指派給基底視窗控制器的`Window`屬性:

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我們使用`CGRect`來定義畫面的視窗位置。 就像視窗的座標系統, 螢幕會將 (0, 0) 定義為左下角。 接下來, 我們會使用**Or**運算子來結合兩個或多個`NSWindowStyle`功能, 以定義視窗的樣式:

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

可用的`NSWindowStyle`功能如下:

- 無邊框-視窗不會有框線。
- **標題**-視窗會有標題列。
- **Closable** -視窗具有 [關閉] 按鈕, 可以關閉。
- **Miniaturizable** -此視窗具有 [Miniaturize] 按鈕, 而且可以最小化。
- 可重設**大小**-視窗會有調整按鈕的大小, 而且可以調整大小。
- **公用程式**-視窗是公用程式樣式視窗 (面板)。
- **DocModal** -如果視窗是面板, 它會是檔強制回應, 而不是系統模式。 
- **NonactivatingPanel** -如果視窗是面板, 則不會成為主視窗。
- **TexturedBackground** -視窗會有紋理的背景。
- 未**縮放**-不會縮放視窗。
- **UnifiedTitleAndToolbar** -將會聯結視窗的標題和工具列區域。
- **抬頭顯示器**-此視窗會顯示為標題顯示面板。
- **FullScreenWindow** -視窗可以進入全螢幕模式。
- **FullSizeContentView** -視窗的內容視圖位於 [標題] 和 [工具列] 區域的後方。

最後兩個屬性會定義視窗的_緩衝類型_, 如果將延遲視窗的繪製, 則為。 如需的詳細`NSWindows`資訊, 請參閱 Apple 的[Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)檔。

最後, 由於不會從 xib 檔案擴大視窗, 因此我們需要呼叫 windows `AwakeFromNib`方法, 在**MainWindowController.cs**中模擬它:

```csharp
Window.AwakeFromNib ();
```

這可讓您針對視窗撰寫程式碼, 就像是從分鏡腳本或 xib 檔案載入的標準視窗一樣。


### <a name="displaying-the-window"></a>顯示視窗

移除 xib 檔案, 並修改**MainWindow.cs**和**MainWindowController.cs**檔案後, 您就會使用視窗, 就像您在 Xcode 的 Interface Builder 中使用 xib 檔案所建立的一般視窗一樣。

下列將會建立新的視窗實例和其控制器, 並在螢幕上顯示視窗:

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此時, 如果應用程式已執行, 而按鈕按了幾次, 將會顯示下列內容:

![範例應用程式執行](xibless-ui-images/run01.png "範例應用程式執行")


## <a name="adding-a-code-only-window"></a>加入僅限程式碼視窗

如果我們只想新增程式碼, 請將 xibless 視窗加入現有的 Xamarin. Mac 應用程式, 以滑鼠右鍵按一下**Solution Pad**中的專案,  > 然後選取 [**新增檔案 ...** ]在 [**新增**檔案] 對話方塊   > 中, 選擇 [**包含控制器的 Xamarin Cocoa 視窗]** , 如下所示:

![加入新的視窗控制器](xibless-ui-images/add01.png "加入新的視窗控制器") 

就像之前一樣, 我們將會從專案中刪除 xib 檔案 (在此例中為**SecondWindow. xib**), 並遵循上方的[切換視窗以使用程式碼](#Switching_a_Window_to_use_Code)一節中的步驟, 來涵蓋視窗的程式碼定義。


## <a name="adding-a-ui-element-to-a-window-in-code"></a>在程式碼中將 UI 元素加入至視窗

不論視窗是在程式碼中建立, 或是從分鏡腳本或. xib 檔案載入, 有時我們可能會想要從程式碼將 UI 元素加入至視窗。 例如：

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上述程式碼會建立新`NSButton`的, 並將它`MyWindow`新增至視窗實例以供顯示。 基本上, 您可以在程式碼中建立可在 Xcode 的 Interface Builder 中定義的任何 UI 元素, 並在視窗中顯示。


## <a name="defining-the-menu-bar-in-code"></a>在程式碼中定義功能表列

由於 Xamarin 目前的限制, 因此不建議您在程式碼中建立 Xamarin. Mac 應用程式的功能表列–`NSMenuBar`-但繼續使用**xib**檔案來定義它  。 話雖如此, 您可以在程式碼中C#加入和移除功能表和功能表項目。

例如, 編輯**AppDelegate.cs**檔案, 讓`DidFinishLaunching`方法看起來如下所示:

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

上述會從程式碼建立狀態列功能表, 並在應用程式啟動時加以顯示。 如需有關使用功能表的詳細資訊, 請參閱我們的[功能表](~/mac/user-interface/menu.md)檔。


## <a name="summary"></a>總結

本文深入探討如何在程式碼中C#建立 Xamarin. Mac 應用程式的使用者介面, 而不是使用 Xcode 的 Interface Builder 搭配. 腳本或 xib 檔案。



## <a name="related-links"></a>相關連結

- [MacXibless (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macxibless)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
