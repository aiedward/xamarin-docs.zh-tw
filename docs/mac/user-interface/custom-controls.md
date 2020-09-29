---
title: 在 Xamarin 中建立自訂控制項
description: 本檔說明如何在 Xamarin 中建立自訂控制項。 它會顯示如何建立自訂控制項、追蹤其狀態、繪製其介面、回應使用者輸入，以及在應用程式中使用控制項。
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: db4cab313b1c1f2fc1aaa969735f7f136feb7015
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429686"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>在 Xamarin 中建立自訂控制項

在 Xamarin 應用 *程式中使用*c # 和 .net *時，您* 可以存取開發人員在 *Xcode* 中運作的相同使用者控制項。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的 _Interface Builder_ 來建立和維護使用者控制項 (或選擇性地在 c # 程式碼) 中直接建立它們。

雖然 macOS 提供豐富的內建使用者控制項，但有時候您可能需要建立自訂控制項，以提供現成可用的功能，或符合自訂 UI 主題 (例如遊戲介面) 。

[![自訂 UI 控制項的範例](custom-controls-images/intro01.png)](custom-controls-images/intro01.png#lightbox)

在本文中，我們將討論在 Xamarin 應用程式中建立可重複使用之自訂消費者介面控制項的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和命令。

<a name="Introduction-to-Outline-Views"></a>

## <a name="introduction-to-custom-controls"></a>自訂控制項簡介

如上所述，有時您可能需要建立可重複使用的自訂消費者介面控制項，以為您的 Xamarin 應用程式 UI 提供獨特的功能，或建立自訂 UI 主題 (例如遊戲介面) 。

在這些情況下，您可以輕鬆地繼承 `NSControl` 和建立自訂工具，可以透過 c # 程式碼或透過 Xcode 的 Interface Builder，新增至應用程式的 UI。 藉由繼承 `NSControl` 自訂控制項，就會自動擁有內建消費者介面控制項 (的所有標準功能，例如 `NSButton`) 。

如果您的自訂消費者介面控制項只顯示 (像自訂圖表和圖形工具) 的資訊，您可能會想要繼承 `NSView` 而不是 `NSControl` 。

無論使用哪一個基類，建立自訂控制項的基本步驟都一樣。

在本文中，您將建立自訂切換開關元件，以提供獨特的消費者介面主題，以及建立功能完整的自訂消費者介面控制項的範例。

<a name="Building-the-Custom-Control"></a>

## <a name="building-the-custom-control"></a>建立自訂控制項

由於我們要建立的自訂控制項將會回應使用者輸入 (滑鼠左鍵按一下) ，我們將會繼承自 `NSControl` 。 如此一來，我們的自訂控制項就會自動擁有內建消費者介面控制項的所有標準功能，並以標準 macOS 控制項的形式回應。

在 Visual Studio for Mac 中，開啟您要為 (建立自訂消費者介面控制項的 Xamarin 專案，或建立一個新的) 。 新增類別並呼叫它 `NSFlipSwitch` ：

[![加入新的類別](custom-controls-images/custom01.png)](custom-controls-images/custom01.png#lightbox)

接下來，編輯 `NSFlipSwitch.cs` 類別，使其看起來如下所示：

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

首先要注意的是自訂類別，我們是從繼承 `NSControl` ，並使用 **Register** 命令將此類別公開至目標 C 和 Xcode 的 Interface Builder：

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在下列各節中，我們將詳細討論上述程式碼的其餘部分。

<a name="Tracking-the-Controls-State"></a>

### <a name="tracking-the-controls-state"></a>追蹤控制項的狀態

由於我們的自訂控制項是一個交換器，因此需要一種方式來追蹤開關的開啟/關閉狀態。 我們會使用中的下列程式碼來處理 `NSFlipSwitch` ：

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

當交換器的狀態變更時，我們需要一種方式來更新 UI。 這是藉由強制控制項以重繪其 UI 來完成 `NeedsDisplay = true` 。

如果我們的控制項需要更多單一 On/Off 狀態 (例如，具有3個位置的多重狀態切換) ，我們可以使用 **列舉** 來追蹤狀態。 在我們的範例中，簡單的 **bool** 將會這麼做。

此外，我們也新增了 helper 方法，以在開啟和關閉之間交換開關的狀態：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

稍後，我們會展開此 helper 類別，以在切換狀態變更時通知呼叫者。

<a name="Drawing-the-Controls-Interface"></a>

### <a name="drawing-the-controls-interface"></a>繪製控制項介面

我們將使用核心圖形繪圖常式，在執行時間繪製自訂控制項的消費者介面。 在這麼做之前，我們必須開啟控制項的圖層。 我們會使用下列私用方法來執行這項作業：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

您可以從每個控制項的函式呼叫這個方法，以確保控制項已正確設定。 例如：

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

當控制項的狀態變更 (**例如從) 開始時，** 我們將會調整控制項的視覺標記法**Off** 。 只要狀態變更，我們就可以使用 `NeedsDisplay = true` 命令來強制控制項重新繪製新的狀態。

<a name="Responding-to-User-Input"></a>

### <a name="responding-to-user-input"></a>回應使用者輸入

有兩種基本方式可將使用者輸入新增至自訂控制項：覆 **寫滑鼠處理常式** 或 **手勢**辨識器。 我們使用的方法，將會以控制項所需的功能為基礎。

> [!IMPORTANT]
> 針對您所建立的任何自訂控制項，您應該使用覆**寫方法**_或_**手勢**辨識器，但不是兩者都可以彼此衝突的相同時間。

<a name="Summary"></a>

#### <a name="handling-user-input-with-override-methods"></a>使用 Override 方法處理使用者輸入

從 `NSControl` (或) 繼承的物件 `NSView` 會有數個覆寫方法來處理滑鼠或鍵盤輸入。 針對我們的範例控制項，我們想 **要在使用者** 以滑鼠左鍵按一下 **控制項時，** 將開關的狀態翻轉。 我們可以將下列覆寫方法加入至 `NSFlipSwitch` 類別，以處理這種情況：

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

在上述程式碼中，我們會呼叫 `FlipSwitchState` 上述 (所定義的方法，) 在方法中翻轉參數的開啟/關閉狀態 `MouseDown` 。 這也會強制重新繪製控制項，以反映目前的狀態。

<a name="Handling-User-Input-with-Gesture-Recognizers"></a>

#### <a name="handling-user-input-with-gesture-recognizers"></a>使用手勢辨識器處理使用者輸入

（選擇性）您可以使用筆勢辨識器來處理使用者與控制項的互動。 移除上述新增的覆寫，編輯 `Initialize` 方法，使其看起來如下所示：

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

在這裡，我們會建立新的， `NSClickGestureRecognizer` 並呼叫我們的 `FlipSwitchState` 方法，以在使用者按一下滑鼠左鍵時變更切換的狀態。 `AddGestureRecognizer (click)`方法會將手勢辨識器新增至控制項。

同樣地，我們使用的方法，取決於我們使用自訂控制項所嘗試完成的動作。 如果我們需要低層級的存取權來進行使用者互動，請使用覆寫方法。 如果我們需要預先定義的功能（例如按一下滑鼠），請使用筆勢辨識器。

<a name="Responding-to-State-Change-Events"></a>

### <a name="responding-to-state-change-events"></a>回應狀態變更事件

當使用者變更自訂控制項的狀態時，我們需要一種方式來回應程式碼 (中的狀態變更，例如在按一下自訂按鈕) 時執行某些動作。

若要提供這項功能，請編輯 `NSFlipSwitch` 類別並加入下列程式碼：

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

接著，請編輯 `FlipSwitchState` 方法，使其看起來如下所示：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我們會提供 `ValueChanged` 事件，讓我們可以在 c # 程式碼中新增處理常式，以便在使用者變更切換狀態時執行動作。

其次，由於自訂控制項繼承自 `NSControl` ，因此它會自動具有可在 Xcode 的 Interface Builder 中指派的 **動作** 。 若要在狀態變更時呼叫這個 **動作** ，我們會使用下列程式碼：

```csharp
if (this.Action !=null)
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，我們會檢查是否已將 **動作** 指派給控制項。 接下來，我們會呼叫已定義的 **動作** 。

<a name="Using-the-Custom-Control"></a>

## <a name="using-the-custom-control"></a>使用自訂控制項

自訂控制項完整定義之後，我們可以使用 c # 程式碼或 Xcode 的 Interface Builder，將其新增至 Xamarin 應用程式的 UI。

若要使用 Interface Builder 新增控制項，請先執行 Xamarin 專案的全新組建，然後按兩下檔案 `Main.storyboard` 以在 Interface Builder 中開啟以進行編輯：

[![編輯 Xcode 中的分鏡腳本](custom-controls-images/custom02.png)](custom-controls-images/custom02.png#lightbox)

接下來，將 `Custom View` 加入消費者介面設計：

[![從程式庫選取自訂視圖](custom-controls-images/custom03.png)](custom-controls-images/custom03.png#lightbox)

在仍選取自訂視圖的情況下，切換至身分 **識別偵測器** ，並將視圖的 **類別** 變更為 `NSFlipSwitch` ：

[![設定 View 類別](custom-controls-images/custom04.png)](custom-controls-images/custom04.png#lightbox)

切換至 [ **助理編輯器** ]，並建立自訂控制項的 **輸出** (確定將它系結至檔案 `ViewController.h` ，而不是檔案 `.m`) ：

[![設定新的輸出](custom-controls-images/custom05.png)](custom-controls-images/custom05.png#lightbox)

儲存您的變更，返回 Visual Studio for Mac 並允許同步變更。編輯檔案 `ViewController.cs` ，讓 `ViewDidLoad` 方法看起來如下所示：

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

在這裡，我們會回應 `ValueChanged` 我們在類別上定義的事件 `NSFlipSwitch` ，並在使用者按一下控制項時寫出目前的 **值** 。

（選擇性）我們可以回到 Interface Builder，並在控制項上定義 **動作** ：

[![設定新的動作](custom-controls-images/custom06.png)](custom-controls-images/custom06.png#lightbox)

同樣地，編輯檔案 `ViewController.cs` 並新增下列方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您應該在 Interface Builder 中使用 **事件** 或定義 **動作** ，但您不應該同時使用這兩種方法，也不能彼此衝突。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文詳細探討如何在 Xamarin 應用程式中建立可重複使用的自訂消費者介面控制項。 我們已瞭解如何繪製自訂控制項 UI、用來回應滑鼠和使用者輸入的兩種主要方式，以及如何在 Xcode 的 Interface Builder 中將新控制項公開至動作。

## <a name="related-links"></a>相關連結

- [MacCustomControl (範例) ](/samples/xamarin/mac-samples/maccustomcontrol)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和機碼值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [處理滑鼠事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)