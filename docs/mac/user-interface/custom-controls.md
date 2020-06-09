---
title: 在 Xamarin. Mac 中建立自訂控制項
description: 本檔說明如何在 Xamarin. Mac 中建立自訂控制項。 它會顯示如何建立自訂控制項、追蹤其狀態、繪製其介面、回應使用者輸入，以及在應用程式中使用控制項。
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: c4bec7d77e7778d8922640c75d23f4b1464f864f
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573920"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>在 Xamarin. Mac 中建立自訂控制項

在 Xamarin. Mac 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在*目標-C*、 *Swift*和*Xcode*中工作的相同使用者控制項。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來建立和維護使用者控制項（或選擇直接在 c # 程式碼中建立）。

雖然 macOS 提供了豐富的內建使用者控制項，但有時候您可能需要建立自訂控制項，以提供不是現成提供的功能，或符合自訂 UI 主題（例如遊戲介面）。

[![](custom-controls-images/intro01.png "Example of a custom UI control")](custom-controls-images/intro01.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中建立可重複使用的自訂使用者介面控制項的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在本文中使用的重要概念和技巧。

您可能想要查看[Xamarin 內部](~/mac/internals/how-it-works.md)檔的將[c # 類別/方法公開至目標-C](~/mac/internals/how-it-works.md)一節，它會說明 `Register` `Export` 用來將 C # 類別連接至目標 C 物件和 UI 元素的和命令。

<a name="Introduction-to-Outline-Views"></a>

## <a name="introduction-to-custom-controls"></a>自訂控制項簡介

如上所述，有時候您可能需要建立可重複使用的自訂使用者介面控制項，為您的 Xamarin. Mac 應用程式的 UI 提供獨特的功能，或建立自訂 UI 主題（例如遊戲介面）。

在這些情況下，您可以輕鬆地繼承 `NSControl` 並建立自訂工具，您可以透過 c # 程式碼或 Xcode 的 Interface Builder，將其新增至應用程式的 UI。 藉由從 `NSControl` 自訂控制項繼承，將會自動擁有內建使用者介面控制項所擁有的所有標準功能（例如 `NSButton` ）。

如果您的自訂使用者介面控制項只顯示資訊（例如自訂圖表和圖形工具），您可能會想要繼承自， `NSView` 而不是 `NSControl` 。

無論使用哪一個基類，建立自訂控制項的基本步驟都相同。

在本文中，會建立自訂的翻轉交換器元件，以提供獨特的使用者介面主題，以及建立功能完整的自訂使用者介面控制項的範例。

<a name="Building-the-Custom-Control"></a>

## <a name="building-the-custom-control"></a>建立自訂控制項

因為我們所建立的自訂控制項將會回應使用者輸入（按下滑鼠左鍵），所以我們將會繼承自 `NSControl` 。 如此一來，我們的自訂控制項就會自動擁有內建使用者介面控制項所擁有和回應的所有標準功能，就像標準的 macOS 控制項一樣。

在 Visual Studio for Mac 中，開啟您想要為其建立自訂使用者介面控制項的 Xamarin. Mac 專案（或建立新的）。 新增類別並呼叫它 `NSFlipSwitch` ：

[![](custom-controls-images/custom01.png "Adding a new class")](custom-controls-images/custom01.png#lightbox)

接著，編輯 `NSFlipSwitch.cs` 類別，使其看起來如下所示：

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

關於我們的自訂類別，首先要注意的是，我們會從繼承 `NSControl` 並使用**Register**命令，將此類別公開至目標-C 和 Xcode 的 Interface Builder：

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在下列各節中，我們將詳細探討上述程式碼的其餘部分。

<a name="Tracking-the-Controls-State"></a>

### <a name="tracking-the-controls-state"></a>追蹤控制項的狀態

因為我們的自訂控制項是參數，所以我們需要一種方式來追蹤交換器的開啟/關閉狀態。 我們會使用中的下列程式碼來處理這種情況 `NSFlipSwitch` ：

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

當交換器的狀態變更時，我們需要一種方法來更新 UI。 我們的作法是強制控制項使用重繪其 UI `NeedsDisplay = true` 。

如果我們的控制項需要更多的單一開啟/關閉狀態（例如具有3個位置的多重狀態切換），我們可以使用**列舉**來追蹤狀態。 在我們的範例中，簡單的**bool**會執行。

我們也新增了 helper 方法，以交換 On 和 Off 之間的交換器狀態：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

稍後，我們將展開此 helper 類別，以在交換器狀態變更時通知呼叫者。

<a name="Drawing-the-Controls-Interface"></a>

### <a name="drawing-the-controls-interface"></a>繪製控制項的介面

我們將使用核心圖形繪圖常式，在執行時間繪製自訂控制項的使用者介面。 在這麼做之前，我們必須開啟控制項的圖層。 我們使用下列私用方法來執行這項操作：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

這個方法會從每個控制項的函式呼叫，以確保控制項已正確設定。 例如：

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

接下來，我們需要覆寫 `DrawRect` 方法，並新增核心圖形常式來繪製控制項：

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

當控制項的狀態變更時（例如從**On**到**Off**），我們將調整其視覺表示。 每當狀態變更時，我們都可以使用 `NeedsDisplay = true` 命令來強制控制項重新繪製新的狀態。

<a name="Responding-to-User-Input"></a>

### <a name="responding-to-user-input"></a>回應使用者輸入

有兩個基本的方法可以將使用者輸入新增至自訂控制項：覆**寫滑鼠處理常式**或**手勢**辨識器。 我們使用的方法會以控制項所需的功能為基礎。

> [!IMPORTANT]
> 對於您所建立的任何自訂控制項，您應該使用覆**寫方法**_或_**手勢**辨識器，但不能同時與彼此衝突。

<a name="Summary"></a>

#### <a name="handling-user-input-with-override-methods"></a>使用覆寫方法處理使用者輸入

繼承自 `NSControl` （或）的物件 `NSView` 有數個用來處理滑鼠或鍵盤輸入的覆寫方法。 針對我們的範例控制項，我們想要在使用者以滑鼠左鍵按一下控制項時，將切換的狀態翻轉到 [**開啟**] 和 [**關閉**]。 我們可以將下列覆寫方法新增至 `NSFlipSwitch` 類別，以處理這種情況：

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

在上述程式碼中，我們呼叫 `FlipSwitchState` 方法（定義于上方）來翻轉方法中參數的開啟/關閉狀態 `MouseDown` 。 這也會強制重新繪製控制項，以反映目前的狀態。

<a name="Handling-User-Input-with-Gesture-Recognizers"></a>

#### <a name="handling-user-input-with-gesture-recognizers"></a>使用手勢辨識器處理使用者輸入

（選擇性）您可以使用手勢辨識器來處理與控制項互動的使用者。 移除上面新增的覆寫，編輯 `Initialize` 方法，使其看起來如下所示：

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

在這裡，我們會建立新的 `NSClickGestureRecognizer` ，並呼叫我們的 `FlipSwitchState` 方法，以在使用者按一下滑鼠左鍵時變更開關的狀態。 `AddGestureRecognizer (click)`方法會將手勢辨識器新增至控制項。

同樣地，我們使用的方法取決於我們嘗試使用自訂控制項來完成的動作。 如果我們需要低層級的存取權來進行使用者互動，請使用覆寫方法。 如果我們需要預先定義的功能（例如按一下滑鼠），請使用手勢辨識器。

<a name="Responding-to-State-Change-Events"></a>

### <a name="responding-to-state-change-events"></a>回應狀態變更事件

當使用者變更自訂控制項的狀態時，我們需要一種方法來回應程式碼中的狀態變更（例如，在按一下自訂按鈕時執行某些動作）。

若要提供這項功能，請編輯 `NSFlipSwitch` 類別並新增下列程式碼：

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

接著，編輯 `FlipSwitchState` 方法，使其看起來如下所示：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我們會提供一個 `ValueChanged` 事件，讓我們可以在 c # 程式碼中將處理常式新增至，讓我們可以在使用者變更參數的狀態時執行動作。

第二，由於我們的自訂控制項繼承自 `NSControl` ，因此它會自動具有可在 Xcode 的 Interface Builder 中指派的**動作**。 若要在狀態變更時呼叫此**動作**，我們會使用下列程式碼：

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，我們會檢查是否已將**動作**指派給控制項。 接下來，我們會呼叫**動作**（如果已定義）。

<a name="Using-the-Custom-Control"></a>

## <a name="using-the-custom-control"></a>使用自訂控制項

透過完整定義的自訂控制項，我們可以使用 c # 程式碼或 Xcode 的 Interface Builder，將其新增至 Xamarin 應用程式的 UI。

若要使用 Interface Builder 加入控制項，請先執行 Xamarin. Mac 專案的全新組建，然後按兩下該檔案， `Main.storyboard` 在 Interface Builder 進行編輯時將其開啟：

[![](custom-controls-images/custom02.png "Editing the storyboard in Xcode")](custom-controls-images/custom02.png#lightbox)

接下來，將拖曳 `Custom View` 至使用者介面設計中：

[![](custom-controls-images/custom03.png "Selecting a Custom View from the Library")](custom-controls-images/custom03.png#lightbox)

在仍選取 [自訂視圖] 的情況下，切換至 [身分**識別偵測器**]，並將視圖的**類別**變更為 `NSFlipSwitch` ：

[![](custom-controls-images/custom04.png "Setting the View's class")](custom-controls-images/custom04.png#lightbox)

切換至 [**助理編輯器**]，並建立自訂控制項的 [**輸出**] （請務必將它系結到檔案中 `ViewController.h` ，而不是檔案 `.m` ）：

[![](custom-controls-images/custom05.png "Configuring a new Outlet")](custom-controls-images/custom05.png#lightbox)

儲存您的變更，返回 Visual Studio for Mac 並允許變更同步。編輯檔案 `ViewController.cs` ，讓 `ViewDidLoad` 方法看起來如下所示：

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

在這裡，我們會回應在 `ValueChanged` 類別上定義的事件 `NSFlipSwitch` ，並在使用者按一下控制項時寫出目前的**值**。

或者，我們可以返回 Interface Builder 並在控制項上定義**動作**：

[![](custom-controls-images/custom06.png "Configuring a new Action")](custom-controls-images/custom06.png#lightbox)

再次編輯檔案， `ViewController.cs` 並新增下列方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您應該在 Interface Builder 中使用**事件**或定義**動作**，但是不應該同時使用這兩種方法，也不能彼此衝突。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文深入探討如何在 Xamarin. Mac 應用程式中建立可重複使用的自訂使用者介面控制項。 我們已瞭解如何繪製自訂控制項 UI，這是回應滑鼠和使用者輸入的兩個主要方法，以及如何將新的控制項公開至 Xcode Interface Builder 中的動作。

## <a name="related-links"></a>相關連結

- [MacCustomControl （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和機碼值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [處理滑鼠事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
