---
title: .storyboard/.xib-less 使用者介面設計
description: 本文涵蓋直接從 C# 程式碼，而不需.storyboard 檔案、.xib 檔案或介面產生器建立 Xamarin.Mac 應用程式的使用者介面。
ms.prod: xamarin
ms.assetid: 02310F58-DCF1-4589-9F4A-065DF64FC0E1
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 66725b02d3e351e74fa79ae5336a7db3a9f2b534
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="storyboardxib-less-user-interface-design"></a>.storyboard/.xib-less 使用者介面設計

_本文涵蓋直接從 C# 程式碼，而不需.storyboard 檔案、.xib 檔案或介面產生器建立 Xamarin.Mac 應用程式的使用者介面。_


## <a name="overview"></a>總覽

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同的使用者介面項目與工具工作的開發人員*OBJECTIVE-C*和*Xcode*沒有。 一般而言，當建立 Xamarin.Mac 應用程式，您將使用 Xcode 的介面產生器與.storyboard 或.xib 檔案來建立和維護您的應用程式的使用者介面。

您也可以選擇直接在 C# 程式碼中建立部分或全部 Xamarin.Mac 應用程式的 UI。 在本文中，我們將討論的基本概念的 C# 程式碼中建立使用者介面與 UI 項目。

[![Visual Studio for Mac 程式碼編輯器](xibless-ui-images/intro01.png "Visual Studio for Mac 的程式碼編輯器")](xibless-ui-images/intro01-large.png#lightbox)

<a name="Switching_a_Window_to_use_Code" />

## <a name="switching-a-window-to-use-code"></a>切換使用程式碼視窗

當您建立新的 Xamarin.Mac Cocoa 應用程式時，預設會取得標準的空白，視窗。 此 windows 中所定義**Main.storyboard** (或傳統上**MainWindow.xib**) 會自動包含在專案中的檔案。 這也包括**ViewController.cs**管理應用程式的主要檢視的檔案 (或再次傳統**MainWindow.cs**和**MainWindowController.cs**檔案)。

若要切換至應用程式的 Xibless 視窗，請執行下列作業：

1. 開啟您想要停止使用的應用程式`.stroyboard`或.xib 檔，以在 Visual Studio 中的使用者介面定義 for mac。
2. 在**方案板**，以滑鼠右鍵按一下**Main.storyboard**或**MainWindow.xib**檔案，然後選取**移除**: 

    ![移除 [主要腳本] 或 視窗](xibless-ui-images/switch01.png "移除主要腳本或視窗")
3. 從**移除對話方塊**，按一下 **刪除**按鈕即可移除.storyboard 或.xib 完全專案： 

    ![確認刪除](xibless-ui-images/switch02.png "確認刪除作業")

現在我們需要修改**MainWindow.cs**定義視窗的版面配置，並修改檔案**ViewController.cs**或**MainWindowController.cs**檔案以建立執行個體我們`MainWindow`類別，因為我們無法再使用.storyboard 或.xib 檔案。

現代化的 Xamarin.Mac 應用程式使用其使用者介面可能不會自動包含分鏡腳本**MainWindow.cs**， **ViewController.cs**或**MainWindowController.cs**檔案。 視需要，只要加入新空 C# 類別至專案 (**新增** > **新檔案...**  > **一般** > **空類別**)，並將相同的遺漏的檔案命名。 


### <a name="defining-the-window-in-code"></a>程式碼中定義視窗

接著，編輯**MainWindow.cs**檔案，並讓它看起來如下所示：

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

讓我們來討論幾個索引鍵的項目。

首先，我們加入一些_計算屬性_可將做為插座 （就像.storyboard 或.xib 檔案中建立的視窗）：

```csharp
public NSButton ClickMeButton { get; set;}
public NSTextField ClickMeLabel { get ; set;}
```

這些可讓我們存取我們即將在視窗中顯示的 UI 項目。 由於視窗不正在從.storyboard 或.xib 膨脹，我們需要一種具現化 (我們稍後就會看到`MainWindowController`類別)。 也就是這個新的建構函式方法的用途：

```csharp
public MainWindow(CGRect contentRect, NSWindowStyle aStyle, NSBackingStore bufferingType, bool deferCreation): base (contentRect, aStyle,bufferingType,deferCreation) {
    ...
}
```

這是我們將在此設計的視窗配置和放置建立必要的使用者介面所需的任何 UI 項目。 我們可以將任何 UI 項目加入至視窗之前，它需要_內容檢視_包含的元素：

```csharp
ContentView = new NSView (Frame);
```

這會建立內容的檢視將會填滿視窗。 現在我們將加入我們第一次的 UI 項目， `NSButton`，視窗：

```csharp
ClickMeButton = new NSButton (new CGRect (10, Frame.Height-70, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
ContentView.AddSubview (ClickMeButton);
```

這裡要注意的第一件事是，不同於 iOS、 macOS 使用數學表示法來定義其視窗座標系統。 原點是視窗的左下角中，隨著增加值的右邊，靠近右上角的視窗中。 當我們建立新`NSButton`，我們將這點納入考量當我們在螢幕上定義其位置和大小。

`AutoresizingMask = NSViewResizingMask.MinYMargin`屬性會指示按鈕，我們想要時垂直調整視窗大小保持在相同的位置，從視窗頂端。 同樣地，這是因為 (0，0) 是在視窗的左下方。

最後，`ContentView.AddSubview (ClickMeButton)`方法會將`NSButton`到內容檢視，如此它就會顯示在畫面上，當應用程式執行和顯示的視窗。

接下來標籤就會加入至視窗將會顯示的次數，`NSButton`已按下： 

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

因為 macOS 沒有特定_標籤_UI 項目，我們已加入特殊的樣式，不可編輯`NSTextField`做為標籤。 就像按鈕，然後再，考量大小和位置會採用該 (0，0) 是在視窗的左下方。 `AutoresizingMask = NSViewResizingMask.WidthSizable | NSViewResizingMask.MinYMargin`屬性會使用**或**運算子以結合兩個`NSViewResizingMask`功能。 這會讓標籤時垂直調整視窗大小保持在相同的位置，從視窗頂端和壓縮，以及依水平調整視窗大小成長的寬度。

同樣地，`ContentView.AddSubview (ClickMeLabel)`方法會將`NSTextField`到內容檢視，如此它就會顯示在畫面上，當執行應用程式，且開啟視窗。


### <a name="adjusting-the-window-controller"></a>調整視窗的控制站

設計之後`MainWindow`不再載入從.storyboard 或.xib 檔案中，我們需要做一些調整視窗的控制站。 編輯**MainWindowController.cs**檔案，並讓它看起來如下所示：

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

可讓討論索引鍵的項目，這項修改。

首先，我們會定義的新執行個體`MainWindow`類別，並將它指派給基底的視窗控制站的`Window`屬性：

```csharp
CGRect contentRect = new CGRect (0, 0, 1000, 500);
base.Window = new MainWindow(contentRect, (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable), NSBackingStore.Buffered, false);
```

我們會定義視窗的螢幕位置`CGRect`。 視窗的座標系統，就像螢幕會定義為較低的左下角 (0，0)。 接下來，我們使用，會在定義視窗的樣式時**或者**運算子以結合二或多個`NSWindowStyle`功能：

```csharp
... (NSWindowStyle.Titled | NSWindowStyle.Closable | NSWindowStyle.Miniaturizable | NSWindowStyle.Resizable) ...
``` 

下列`NSWindowStyle`可用的功能：

- **無框線**-視窗都有沒有框線。
- **標題為**-視窗會有標題列。
- **Closable** -視窗已關閉 按鈕，可以關閉。
- **Miniaturizable** -視窗具有 Miniaturize 按鈕，並可降至最低。
- **可調整大小**-視窗將會有調整大小的按鈕，而且是可調整大小。
- **公用程式**-視窗是公用程式樣式視窗 （面板）。
- **DocModal** -如果視窗是面板，它會作為文件獨佔式而不是系統強制回應。 
- **NonactivatingPanel** -如果視窗是面板，它將不會成為主視窗。
- **TexturedBackground** -視窗會顯示有材質的背景。
- **無縮放**-視窗也不會隨著調整。
- **UnifiedTitleAndToolbar** -即將加入視窗的標題和工具列區域。
- **抬頭顯示器**-視窗將會顯示為平視顯示窗面板。
- **FullScreenWindow** -視窗可以進入全螢幕模式。
- **FullSizeContentView** -標題和工具列區域位於視窗的內容檢視。

最後兩個屬性會定義_緩衝處理的型別_視窗，如果將會延遲視窗的繪圖。 如需有關`NSWindows`，請參閱 Apple[簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)文件。

最後，因為視窗不正在從.storyboard 或.xib 膨脹，所以我們需要模擬在我們**MainWindowController.cs**經由呼叫 windows`AwakeFromNib`方法：

```csharp
Window.AwakeFromNib ();
```

這可讓您只是視窗的程式碼類似標準的視窗，從.storyboard 或.xib 檔案載入。


### <a name="displaying-the-window"></a>顯示視窗

.Storyboard 或.xib 檔案移除和**MainWindow.cs**和**MainWindowController.cs**檔案修改，您將使用視窗一樣，在已經建立任何一般視窗Xcode 的介面產生器與.xib 檔案。

下列將建立的視窗和其控制站的新執行個體並在螢幕上顯示視窗：

```csharp
private MainWindowController mainWindowController;
...

mainWindowController = new MainWindowController ();
mainWindowController.Window.MakeKeyAndOrderFront (this);
```

此時，如果應用程式執行，而且按一下此按鈕數次，以下將會顯示：

![範例應用程式執行](xibless-ui-images/run01.png "執行範例應用程式")


## <a name="adding-a-code-only-window"></a>加入程式碼只有視窗

如果我們想要新增至現有的 Xamarin.Mac 應用程式的僅限程式碼、 xibless 視窗，以滑鼠右鍵按一下中的專案上**方案板**選取**新增** > **新的檔案...**.在**新檔案**對話方塊選擇**Xamarin.Mac** > **Cocoa 視窗與控制器**，如下所示：

![將新的視窗控制站新增](xibless-ui-images/add01.png "加入新的視窗控制站") 

如同先前一樣，我們將會從專案刪除預設.storyboard 或.xib 檔案 (在此情況下**SecondWindow.xib**) 並遵循的步驟中[切換使用程式碼視窗](#Switching_a_Window_to_use_Code)上述涵蓋一節程式碼視窗的定義。


## <a name="adding-a-ui-element-to-a-window-in-code"></a>UI 項目加入至程式碼中的視窗

視窗建立程式碼中或從.storyboard 或.xib 檔案載入，是否可能會有時間我們要從程式碼加入至視窗的 UI 項目。 例如: 

```csharp
var ClickMeButton = new NSButton (new CGRect (10, 10, 100, 30)){
    AutoresizingMask = NSViewResizingMask.MinYMargin
};
MyWindow.ContentView.AddSubview (ClickMeButton);
```

上述程式碼建立新`NSButton`並將它加入`MyWindow`視窗顯示的執行個體。 基本上可以在 Xcode 的介面產生器中.storyboard 或.xib 檔案中定義的任何 UI 項目可以在程式碼中建立和顯示在視窗中。


## <a name="defining-the-menu-bar-in-code"></a>程式碼中定義功能表列

由於 Xamarin.Mac 中目前的限制，不建議您建立 Xamarin.Mac 應用程式的功能表列 –`NSMenuBar`– 在程式碼，但繼續使用**Main.storyboard**或**MainMenu.xib**檔案，以定義它。 話雖如此，您可以加入並移除在 C# 程式碼中的功能表和功能表項目。

例如，編輯**d**檔案並製作`DidFinishLaunching`方法看起來像下列：

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

上述從程式碼建立狀態列功能表，並顯示應用程式啟動。 如需有關使用功能表的詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)文件。


## <a name="summary"></a>總結

這篇文章已使用 C# 程式碼而不是使用.storyboard 或.xib 檔使用 Xcode 的介面產生器建立 Xamarin.Mac 應用程式的使用者介面的詳細的檢視。



## <a name="related-links"></a>相關連結

- [MacXibless （範例）](https://developer.xamarin.com/samples/mac/MacXibless/)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [簡介 Windows](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/WinPanel/Introduction.html)
