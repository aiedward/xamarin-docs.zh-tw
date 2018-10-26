---
title: 建立 xamarin.mac 的自訂控制項
description: 本文件說明如何建置 Xamarin.Mac 中的自訂控制項。 它示範如何建置自訂控制項、 追蹤其狀態、 繪製其介面、 回應使用者輸入，並使用應用程式中的控制項。
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 7fde60d48c23bc48ce1602a0643a3af8ad492ec6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104007"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>建立 xamarin.mac 的自訂控制項

當在 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取的相同使用者控制項，工作的開發人員*Objective C*， *Swift*並*Xcode*沒有. 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的使用者控制項 （或選擇直接在 C# 程式碼中建立它們）。

雖然 macOS 提供豐富的內建的使用者控制項，可能會有您需要建立自訂控制項以提供不提供的立即可用的功能，或符合自訂 UI 佈景主題 （例如遊戲的介面） 的時間。

[![](custom-controls-images/intro01.png "自訂的 UI 控制項的範例")](custom-controls-images/intro01.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中建立可重複使用的自訂使用者介面控制項的基礎。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>自訂控制項簡介

如上所述，可能是當您需要建立可重複使用自訂使用者介面控制項的 Xamarin.Mac 應用程式的 UI 提供獨特的功能，或建立自訂的 UI 佈景主題 （例如遊戲的介面）。

在這些情況下，您可以輕鬆地繼承自`NSControl`並建立自訂的工具，會加入至您的應用程式 UI，透過 C# 程式碼，或透過 Xcode 的 Interface Builder。 藉由繼承自`NSControl`您的自訂控制項將會自動擁有所有的內建的使用者介面控制項的標準功能 (例如`NSButton`)。

如果您的自訂使用者介面控制項只會顯示資訊 （例如自訂的圖表和圖形化工具），您可能想要繼承自`NSView`而不是`NSControl`。

無論使用哪一個基底類別，用來建立自訂控制項的基本步驟都相同。

在此文章將建立一個自訂翻轉交換器元件，提供唯一的使用者介面佈景主題和範例來建立功能完整的自訂使用者介面控制項。

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>建置自訂控制項

因為我們要建立自訂控制項將會回應使用者輸入 （按一下滑鼠左的按鈕），我們要繼承自`NSControl`。 如此一來，我們的自訂控制項會自動擁有所有的內建的使用者介面控制項的標準功能，並回應如同標準的 macOS 的控制項。

在 Visual Studio for Mac 中，開啟您想要建立的自訂使用者介面控制項 （或建立一個新） Xamarin.Mac 專案。 加入新的類別，並取名為`NSFlipSwitch`:

[![](custom-controls-images/custom01.png "加入新的類別")](custom-controls-images/custom01.png#lightbox)

接著，編輯`NSFlipSwitch.cs`類別，並使它看起來如下：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using AppKit;
using CoreGraphics;

namespace MacCustomControl
{
    [Register("NSFlipSwitch")]
    public class NSFlipSwitch : NSControl
    {
        #region Private Variables
        private bool _value = false;
        #endregion

        #region Computed Properties
        public bool Value {
            get { return _value; }
            set {
                // Save value and force a redraw
                _value = value;
                NeedsDisplay = true;
            }
        }
        #endregion

        #region Constructors
        public NSFlipSwitch ()
        {
            // Init
            Initialize();
        }

        public NSFlipSwitch (IntPtr handle) : base (handle)
        {
            // Init
            Initialize();
        }

        [Export ("initWithFrame:")]
        public NSFlipSwitch (CGRect frameRect) : base(frameRect) {
            // Init
            Initialize();
        }

        private void Initialize() {
            this.WantsLayer = true;
            this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
        }
        #endregion

        #region Draw Methods
        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Use Core Graphic routines to draw our UI
            ...

        }
        #endregion

        #region Private Methods
        private void FlipSwitchState() {
            // Update state
            Value = !Value;
        }
        #endregion

    }
}
```

首先要注意到我們的自訂類別，因為我們繼承自`NSControl`並使用**註冊**來公開這個類別，以 OBJECTIVE-C 和 Xcode 的 Interface Builder 的命令：

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在下列章節中，我們將查看上述的程式碼，在詳細資料中的其餘部分。

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>追蹤控制項的狀態

我們的自訂控制項是一種交換器，因為我們需要設法追蹤切換的開啟/關閉狀態。 我們使用中的下列程式碼來處理， `NSFlipSwitch`:

```csharp
private bool _value = false;
...

public bool Value {
    get { return _value; }
    set {
        // Save value and force a redraw
        _value = value;
        NeedsDisplay = true;
    }
}
```

當此參數的狀態變更時，我們需要想辦法更新 UI。 我們的做法是強制控制項重繪其 UI `NeedsDisplay = true`。

如果我們的控制項需要更多的單一開啟/關閉狀態 （例如使用 3 個位置的多重狀態交換器），我們也可以使用**Enum**追蹤狀態。 針對本範例中，簡單**bool**即可。

我們也新增 helper 方法來開啟和關閉交換的狀態之間切換：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

稍後，我們會擴充這個協助程式類別，以通知呼叫端切換狀態已變更時。

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>繪製控制項的介面

我們要使用核心圖形繪圖常式繪製自訂控制項的使用者介面，在執行階段。 我們可以這麼做之前，我們需要開啟圖層，我們的控制項。 我們這麼做，使用下列的私用方法：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

從每個控制項的建構函式，以確保已正確設定控制項，會呼叫這個方法。 例如: 

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

接下來，我們需要覆寫`DrawRect`方法，並新增來繪製控制項的核心圖形常式：

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

我們將會調整控制項的視覺表示法其狀態變更時 (例如從**上**要**關閉**)。 任何一次狀態變更，我們可以使用`NeedsDisplay = true`強制新的狀態來重繪控制項的命令。

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>回應使用者輸入

有兩種基本方式，我們可以加入自訂控制項的使用者輸入：**處理常式以覆寫滑鼠**或是**筆勢辨識器**。 我們使用，哪一種方法會根據我們的控制項所需的功能。

> [!IMPORTANT]
> 對於您所建立的任何自訂控制項，您應該使用**覆寫方法**_或是_**筆勢辨識器**，但不可兩者同時它們可以彼此衝突的時間。

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>處理使用者輸入，以覆寫方法

物件，繼承自`NSControl`(或`NSView`) 有數個覆寫方法來處理滑鼠或鍵盤輸入。 我們範例中的控制項，我們想要翻轉的狀態之間切換**上**並**關閉**當使用者按一下滑鼠左按鈕控制項上。 我們可以新增下列覆寫方法以`NSFliwSwitch`類別來處理這種情形：

```csharp
#region Mouse Handling Methods
// --------------------------------------------------------------------------------
// Handle mouse with Override Methods.
// NOTE: Use either this method or Gesture Recognizers, NOT both!
// --------------------------------------------------------------------------------
public override void MouseDown (NSEvent theEvent)
{
    base.MouseDown (theEvent);

    FlipSwitchState ();
}

public override void MouseDragged (NSEvent theEvent)
{
    base.MouseDragged (theEvent);
}

public override void MouseUp (NSEvent theEvent)
{
    base.MouseUp (theEvent);
}

public override void MouseMoved (NSEvent theEvent)
{
    base.MouseMoved (theEvent);
}
## endregion
```

在上述程式碼中，我們稱`FlipSwitchState`方法 （上面定義） 來翻轉 \ 開啟/關閉狀態中的交換器`MouseDown`方法。 這也會強制重新繪製以反映目前狀態的控制項。

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>處理筆勢辨識器使用的使用者輸入

（選擇性） 您可以使用筆勢辨識器來處理使用者與控制項互動。 移除上述新增的覆寫，編輯`Initialize`方法，使它看起來如下所示：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;

    // --------------------------------------------------------------------------------
    // Handle mouse with Gesture Recognizers.
    // NOTE: Use either this method or the Override Methods, NOT both!
    // --------------------------------------------------------------------------------
    var click = new NSClickGestureRecognizer (() => {
        FlipSwitchState();
    });
    AddGestureRecognizer (click);
}
```

在這裡，我們會建立新`NSClickGestureRecognizer`，然後呼叫我們`FlipSwitchState`方法來變更參數的狀態，當使用者在其上按一下滑鼠左按鈕。 `AddGestureRecognizer (click)`方法加入至控制項的筆勢辨識器。

同樣地，我們會使用哪個方法取決於我們嘗試使用自訂控制項完成的工作。 如果我們需要低層級存取使用者互動，使用 覆寫方法。 如果我們需要預先定義的功能，例如按一下滑鼠，就會使用筆勢辨識器。

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>回應狀態變更事件

當使用者變更自訂控制項的狀態時，我們需要想辦法回應程式碼中的狀態變更 (例如執行的項目時按一下自訂按鈕)。 

若要提供這項功能，請編輯`NSFlipSwitch`類別，並新增下列程式碼：

```csharp
#region Events
public event EventHandler ValueChanged;

internal void RaiseValueChanged() {
    if (this.ValueChanged != null)
        this.ValueChanged (this, EventArgs.Empty);

    // Perform any action bound to the control from Interface Builder
    // via an Action.
    if (this.Action !=null) 
        NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
}
## endregion
```

接著，編輯`FlipSwitchState`方法，使它看起來如下所示：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我們提供`ValueChanged`事件，我們可以加入處理常式以 C# 程式碼，以便我們可以執行動作，當使用者變更參數的狀態。

第二個，因為我們的自訂控制項是繼承自`NSControl`，它會自動擁有**動作**Xcode 的 Interface Builder 中指派的。 若要將此稱為**動作**當狀態變更時，我們會使用下列程式碼：

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，我們檢查看**動作**已指派給控制項。 接下來，我們會呼叫**動作**如果已經定義。

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>使用自訂控制項

使用完整定義自訂控制項，我們可以將它加入至我們的 Xamarin.Mac 應用程式的 UI 使用 C# 程式碼，或在 Xcode 的 Interface Builder 中。

若要新增使用 Interface Builder 的控制項，第一次執行清除建置 Xamarin.Mac 專案中，然後按兩下`Main.storyboard`介面產生器中開啟進行編輯的檔案：

[![](custom-controls-images/custom02.png "編輯在 Xcode 中的分鏡腳本")](custom-controls-images/custom02.png#lightbox)

下一步，拖曳`Custom View`在使用者介面設計：

[![](custom-controls-images/custom03.png "從程式庫中選取 自訂檢視")](custom-controls-images/custom03.png#lightbox)

[自訂] 檢視仍處於選取狀態的情況下，切換到**身分識別偵測器**並變更檢視的**類別**到`NSFlipSwitch`:

[![](custom-controls-images/custom04.png "設定檢視的類別")](custom-controls-images/custom04.png#lightbox)

切換至**輔助編輯器**並建立**輸出**自訂控制項 (務必將它在繫結`ViewControler.h`檔案而非`.m`檔案):

[![](custom-controls-images/custom05.png "設定新的輸出")](custom-controls-images/custom05.png#lightbox)

儲存變更，回到 Visual Studio for Mac 並讓這些變更同步處理。編輯`ViewController.cs`檔案，並讓`ViewDidLoad`方法外觀如下所示：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Do any additional setup after loading the view.
    OptionTwo.ValueChanged += (sender, e) => {
        // Display the state of the option switch
        Console.WriteLine("Option Two: {0}", OptionTwo.Value);
    };
}
``` 

在這裡，我們會回應`ValueChanged`上面定義的事件`NSFlipSwitch`類別，並寫出目前**值**當使用者按一下控制項上。

（選擇性） 我們可以返回介面產生器，並定義**動作**控制項上：

[![](custom-controls-images/custom06.png "設定新的動作")](custom-controls-images/custom06.png#lightbox)

同樣地，編輯`ViewController.cs`檔案，並新增下列方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您應該使用**事件**，或是定義**動作**中介面產生器中，但您不應該同時使用這兩種方法，或它們可以彼此衝突。

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解在 Xamarin.Mac 應用程式中建立可重複使用的自訂使用者介面控制項。 我們看到繪製自訂控制項的 UI，兩種主要方式來回應滑鼠和使用者輸入的方式以及如何公開 （expose） 新的控制項，Xcode 的 Interface Builder 中的動作。

## <a name="related-links"></a>相關連結

- [MacCustomControl （範例）](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [處理滑鼠事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
