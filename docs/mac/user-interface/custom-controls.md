---
title: Xamarin.Mac 中建立自訂控制項
description: 本文件說明如何建置 Xamarin.Mac 中的自訂控制項。 它示範如何建置自訂控制項，來追蹤其狀態、 繪製其介面、 回應使用者輸入，並使用應用程式中的控制項。
ms.prod: xamarin
ms.assetid: 004534B1-5AEE-452C-BBBE-8C2673FD49B7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: e4c2b2c9ee7bae3d6489fec6b22881653ec53043
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792674"
---
# <a name="creating-custom-controls-in-xamarinmac"></a>Xamarin.Mac 中建立自訂控制項

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同使用者控制項，工作的開發人員*OBJECTIVE-C*， *Swift*和*Xcode*沒有. 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的使用者控制項 （或您可以選擇直接在 C# 程式碼中建立它們）。

雖然 macOS 提供豐富的內建的使用者控制項，可能會有您需要建立自訂控制項來提供功能未提供的方塊外或是比對自訂 UI 佈景主題 （例如遊戲的介面） 的時間。

[![](custom-controls-images/intro01.png "自訂的 UI 控制項的範例")](custom-controls-images/intro01.png#lightbox)

在本文中，我們會建立可重複使用的自訂使用者介面控制項 Xamarin.Mac 應用程式中的基礎。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction-to-Outline-Views" />

## <a name="introduction-to-custom-controls"></a>自訂控制項簡介

如同前面所述，有些時候可能會當您需要建立可重複使用，自訂使用者介面控制項提供 Xamarin.Mac 應用程式的 UI 中的唯一功能，或建立自訂的佈景主題 UI （例如遊戲的介面）。

在這些情況下，您可以輕鬆地從繼承`NSControl`並建立會加入至您的應用程式 UI 透過 C# 程式碼，或透過 Xcode 的介面產生器的自訂工具。 透過繼承自`NSControl`自訂控制項將會自動擁有所有的內建的使用者介面控制項包含的標準功能 (例如`NSButton`)。

如果您的自訂使用者介面控制項只會顯示資訊 （例如自訂圖表和圖形化工具），您可能想要繼承自`NSView`而不是`NSControl`。

無論使用哪一個基底類別時，用來建立自訂控制項的基本步驟都相同。

在將此發行項，建立自訂翻轉交換器元件提供唯一的使用者介面主題和建立功能完整的自訂使用者介面控制項的範例。

<a name="Building-the-Custom-Control" />

## <a name="building-the-custom-control"></a>建置自訂控制項

自訂控制項，我們會建立會回應使用者輸入 （按一下滑鼠左的按鈕），因為我們要繼承自`NSControl`。 如此一來，我們的自訂控制項會自動擁有所有內建的使用者介面控制項包含的標準功能，並回應像標準 macOS 控制項。

在適用於 Mac 的 Visual Studio 中開啟您想要建立的自訂使用者介面控制項 （或另外新建一個） Xamarin.Mac 專案。 加入新的類別並呼叫它`NSFlipSwitch`:

[![](custom-controls-images/custom01.png "加入新類別")](custom-controls-images/custom01.png#lightbox)

接著，編輯`NSFlipSwitch.cs`類別，並讓它看起來如下所示：

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

請注意我們的自訂類別，因為我們繼承自第一件事`NSControl`和使用**註冊**將此類別公開給 Objective C 和 Xcode 的介面產生器的命令：

```csharp
[Register("NSFlipSwitch")]
public class NSFlipSwitch : NSControl
```

在下列章節中，我們將查看詳細資料中的上述程式碼的其餘部分。

<a name="Tracking-the-Controls-State" />

### <a name="tracking-the-controls-state"></a>追蹤控制項的狀態

由於我們的自訂控制項是一種交換器，我們需要一種追蹤參數的開啟/關閉狀態。 我們以下列程式碼中處理這`NSFlipSwitch`:

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

當參數的狀態變更時，我們需要更新 UI 的方式。 這是藉由強迫控制項重繪其 UI 使用`NeedsDisplay = true`。

如果我們的掌控所需的多個單一開啟/關閉狀態 （例如 3 位置的多重狀態交換器），我們能夠使用**列舉**追蹤狀態。 範例中，簡單**bool**即可。

我們也加入了要交換的狀態之間切換開啟和關閉的 helper 方法：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
}
```

更新版本中，我們會展開這個協助程式類別，以告知呼叫端切換狀態變更時。

<a name="Drawing-the-Controls-Interface" />

### <a name="drawing-the-controls-interface"></a>繪製控制項的介面

我們會用來繪製我們的自訂控制項的使用者介面，在執行階段的核心圖形的繪圖常式。 我們可以這麼做之前，我們需要在我們的掌控的圖層上開啟。 我們使用下列的私用方法執行下列動作：

```csharp
private void Initialize() {
    this.WantsLayer = true;
    this.LayerContentsRedrawPolicy = NSViewLayerContentsRedrawPolicy.OnSetNeedsDisplay;
}
```

取得呼叫這個方法，從每個控制項的建構函式，來確定已正確設定控制項。 例如: 

```csharp
public NSFlipSwitch (IntPtr handle) : base (handle)
{
    // Init
    Initialize();
}
```

接下來，我們必須覆寫`DrawRect`方法並加入要繪製控制項的核心圖形常式：

```csharp
public override void DrawRect (CGRect dirtyRect)
{
    base.DrawRect (dirtyRect);

    // Use Core Graphic routines to draw our UI
    ...

}
```

我們將會調整控制項的視覺化表示狀態變更時 (例如從**上**至**關閉**)。 任何一次狀態變更，我們可以使用`NeedsDisplay = true`命令，強制進行新的狀態重繪控制項。

<a name="Responding-to-User-Input" />

### <a name="responding-to-user-input"></a>回應使用者輸入

有兩個基本的方式，我們可以將使用者輸入到我們的自訂控制項：**處理常式覆寫滑鼠**或**筆勢辨識器**。 我們使用，哪一種方法將會根據我們的掌控所需的功能。

> [!IMPORTANT]
> 針對您所建立的任何自訂控制項，您應該使用**覆寫方法**_或_**筆勢辨識器**，但不可兩者同時它們可以彼此衝突的時間。

<a name="Summary" />

#### <a name="handling-user-input-with-override-methods"></a>覆寫方法以處理使用者輸入

繼承自物件`NSControl`(或`NSView`) 有數個覆寫方法來處理滑鼠或鍵盤輸入。 我們的範例控制項，我們想要翻轉的狀態之間切換**上**和**關閉**當使用者按一下滑鼠左按鈕控制項上。 我們可以加入下列覆寫方法以`NSFliwSwitch`類別來處理此情形：

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

在上述程式碼中，我們稱`FlipSwitchState`方法 （上面定義） 來翻轉 開啟/關閉狀態中的交換器`MouseDown`方法。 這也會強制重新繪製，以反映目前狀態的控制項。

<a name="Handling-User-Input-with-Gesture-Recognizers" />

#### <a name="handling-user-input-with-gesture-recognizers"></a>處理使用者輸入的筆勢辨識器

（選擇性） 您可以使用筆勢辨識器來處理使用者與控制項互動。 移除上述新增的覆寫，請編輯`Initialize`方法並看起來如下所示：

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

在這裡，我們會建立新`NSClickGestureRecognizer`，然後呼叫我們`FlipSwitchState`方法，當使用者在其上按一下滑鼠左按鈕變更參數的狀態。 `AddGestureRecognizer (click)`方法加入至控制項的筆勢辨識器。

同樣地，我們會使用哪一種方法會取決於我們嘗試完成的工作與我們的自訂控制項。 如果我們需要低層級的存取為使用者互動，使用覆寫方法。 如果我們需要預先定義的功能，例如滑鼠點選使用筆勢辨識器。

<a name="Responding-to-State-Change-Events" />

### <a name="responding-to-state-change-events"></a>回應狀態變更事件

當使用者變更我們的自訂控制項的狀態時，我們需要能夠回應程式碼中的狀態變更 (例如執行的項目時按一下自訂按鈕)。 

若要提供這項功能，請編輯`NSFlipSwitch`類別，然後將下列程式碼：

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

接著，編輯`FlipSwitchState`方法並看起來如下所示：

```csharp
private void FlipSwitchState() {
    // Update state
    Value = !Value;
    RaiseValueChanged ();
}
```

首先，我們提供`ValueChanged`我們可以將處理常式加入 C# 程式碼，讓使用者變更參數的狀態時，我們可以執行動作的事件。

第二，由於我們的自訂控制項都繼承自`NSControl`，所以自動有**動作**Xcode 的介面產生器中指派的。 若要呼叫這個**動作**當狀態變更時，我們會使用下列程式碼：

```csharp
if (this.Action !=null) 
    NSApplication.SharedApplication.SendAction (this.Action, this.Target, this);
```

首先，我們會檢查是否**動作**已指派給控制項。 接下來，呼叫**動作**如果已經定義。

<a name="Using-the-Custom-Control" />

## <a name="using-the-custom-control"></a>使用自訂控制項

包含完整定義我們自訂控制項，我們可以將它加入至我們的 Xamarin.Mac 應用程式的 UI 使用 C# 程式碼或在 Xcode 的介面產生器中。

若要加入的控制項使用介面產生器，第一次執行乾淨的組建 Xamarin.Mac 專案，然後按兩下`Main.storyboard`介面產生器中開啟進行編輯的檔案：

[![](custom-controls-images/custom02.png "編輯在 Xcode 中的分鏡腳本")](custom-controls-images/custom02.png#lightbox)

下一步，拖曳`Custom View`在使用者介面設計：

[![](custom-controls-images/custom03.png "從程式庫中選取自訂檢視")](custom-controls-images/custom03.png#lightbox)

仍然選取 自訂檢視，切換至**識別 Inspector**將檢視變更**類別**至`NSFlipSwitch`:

[![](custom-controls-images/custom04.png "設定檢視的類別")](custom-controls-images/custom04.png#lightbox)

切換至**助理編輯器**並建立**插座**自訂控制項 (務必要將它在繫結`ViewControler.h`檔案而非`.m`檔案):

[![](custom-controls-images/custom05.png "設定新的輸出")](custom-controls-images/custom05.png#lightbox)

儲存變更，回到 Visual Studio for Mac，並讓這些變更同步處理。編輯`ViewController.cs`檔案並製作`ViewDidLoad`方法看起來像下列：

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

在這裡，我們回應`ValueChanged`事件定義上述`NSFlipSwitch`類別，並寫出目前**值**當使用者按一下控制項上。

選擇性地，我們無法返回介面產生器，並定義**動作**控制項上：

[![](custom-controls-images/custom06.png "設定新的動作")](custom-controls-images/custom06.png#lightbox)

同樣地，編輯`ViewController.cs`檔案，然後加入下列方法：

```csharp
partial void OptionTwoFlipped (Foundation.NSObject sender) {
    // Display the state of the option switch
    Console.WriteLine("Option Two: {0}", OptionTwo.Value);
}
```

> [!IMPORTANT]
> 您應該使用**事件**或定義**動作**中介面產生器中，但您不應該同時使用這兩種方法，或它們可以彼此衝突。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得 Xamarin.Mac 應用程式中建立可重複使用的自訂使用者介面控制項的詳細的檢視。 我們了解如何繪製自訂控制項的 UI，兩種主要方式來回應滑鼠和使用者輸入以及如何公開新的控制項，以在 Xcode 的介面產生器中的動作。

## <a name="related-links"></a>相關連結

- [MacCustomControl （範例）](https://developer.xamarin.com/samples/mac/MacCustomControl/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [處理滑鼠事件](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/EventOverview/HandlingMouseEvents/HandlingMouseEvents.html)
