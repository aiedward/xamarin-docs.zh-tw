---
title: Xamarin.mac 的對話方塊
description: 本文涵蓋在 Xamarin.Mac 應用程式中的強制回應視窗與對話方塊。 它會描述建立強制回應視窗在 Xcode 和 Interface builder，使用標準的對話方塊，並與 C# 程式碼中的這些控制項互動。
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f53c1afd7c3f835bff2aae507b9937067bde4bec
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865738"
---
# <a name="dialogs-in-xamarinmac"></a>Xamarin.mac 的對話方塊

使用時C#和.NET 在 Xamarin.Mac 應用程式中，您可以存取相同的對話方塊和強制回應的 Windows，工作的開發人員*Objective C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的強制回應 Windows （或選擇直接在 C# 程式碼中建立它們）。

對話方塊會出現在回應使用者動作，而且通常會提供使用者可以完成的動作。 可被關閉前，對話方塊會要求使用者回應。

Windows 可以是用於非強制回應的狀態 （例如文字編輯器，可以有多個同時開啟的文件） 或強制回應 （例如必須關閉應用程式才能繼續 [匯出] 對話方塊中）。

[![](dialog-images/dialog03.png "開啟的對話方塊")](dialog-images/dialog03.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用的對話方塊和強制回應 Windows 的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>對話方塊的簡介

對話方塊會出現在回應使用者動作 （例如儲存檔案），並提供讓使用者以完成該動作。 可被關閉前，對話方塊會要求使用者回應。

根據 Apple，有三種方式來顯示對話方塊時：

- **文件的強制回應**-文件強制回應對話方塊會防止使用者執行特定的文件內的其他任何項目關閉。
- **應用程式強制回應**-的強制回應對話方塊會避免使用者進行互動的應用程式，直到關閉的應用程式。
- **非強制回應**非強制回應對話方塊可讓使用者變更對話方塊中的設定還是文件視窗進行互動時。

### <a name="modal-window"></a>強制回應視窗

任何標準`NSWindow`可供為自訂對話方塊以強制回應方式顯示：

[![](dialog-images/modal01.png "範例強制回應視窗")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>文件的強制回應對話方塊表

A_表_是強制回應對話方塊附加至指定的文件視窗中，防止使用者互動的視窗，直到他們關閉對話方塊。 在工作表會附加至從中它會形成，和只有一張紙可能隨時都可以開啟視窗的視窗。

[![](dialog-images/sheet08.png "範例強制回應的工作表")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>喜好設定 Windows

喜好設定 視窗是強制回應對話方塊，其中包含使用者不常變更的應用程式的設定。 喜好設定 Windows 通常會包含一個工具列，可讓使用者切換不同的設定群組：

[![](dialog-images/dialog02.png "範例的喜好設定 視窗")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>開啟的對話方塊

[開啟] 對話方塊可讓使用者以一致的方式，來尋找及開啟應用程式中的項目：

[![](dialog-images/dialog03.png "開啟的對話方塊")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>列印和版面設定對話方塊

macOS 提供標準列印與頁面設定對話方塊，讓使用者可以擁有一致的列印功能，可以顯示您的應用程式的經驗，它們會使用每個應用程式。

[列印] 對話方塊可顯示為兩個免費浮動的對話方塊中：

[![](dialog-images/print01.png "列印對話方塊")](dialog-images/print01.png#lightbox)

或者，它可以顯示為工作表頁：

[![](dialog-images/print02.png "列印工作表")](dialog-images/print02.png#lightbox)

版面設定對話方塊可顯示為兩個免費浮動的對話方塊中：

[![](dialog-images/print03.png "版面設定對話方塊")](dialog-images/print03.png#lightbox)

或者，它可以顯示為工作表頁：

[![](dialog-images/print04.png "將頁面設定表")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>儲存對話方塊

[儲存] 對話方塊可讓使用者以一致的方式儲存應用程式中的項目。 [儲存] 對話方塊中有兩種狀態：**最小**（也稱為摺疊）：

[![](dialog-images/save01.png "儲存對話方塊")](dialog-images/save01.png#lightbox)

而**展開**狀態：

[![](dialog-images/save02.png "擴充的儲存對話方塊")](dialog-images/save02.png#lightbox)

**最小**儲存對話方塊也會顯示為工作表頁：

[![](dialog-images/save03.png "最少的儲存工作表")](dialog-images/save03.png#lightbox)

如同**展開**儲存對話方塊：

[![](dialog-images/save04.png "擴充的儲存工作表")](dialog-images/save04.png#lightbox)

如需詳細資訊，請參閱 <<c0> [ 對話](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>加入專案中的強制回應視窗

主要的文件視窗中，除了 Xamarin.Mac 應用程式可能需要其他類型的視窗顯示給使用者，例如喜好設定或偵測器面板。

若要加入新的視窗，執行下列作業：

1. 在 [**方案總管] 中**，開啟`Main.storyboard`以在 Xcode 的 Interface Builder 中編輯的檔案。
2. 拖曳新**檢視控制器**到設計介面：

    [![](dialog-images/new01.png "從程式庫中選取檢視控制器")](dialog-images/new01.png#lightbox)
3. 在 **身分識別偵測器**，輸入`CustomDialogController`如**類別名稱**: 

    [![](dialog-images/new02.png "設定的類別名稱")](dialog-images/new02.png#lightbox)
4. 切換回到 Visual Studio for Mac，讓它與 Xcode 同步處理，並建立`CustomDialogController.h`檔案。
5. 返回 Xcode，並設計您的介面： 

    [![](dialog-images/new03.png "設計在 Xcode 中的 UI")](dialog-images/new03.png#lightbox)
6. 建立**強制回應的 Segue**從您的應用程式至新的檢視控制器，藉由拖曳控制項從 UI 項目，將會開啟對話方塊的視窗對話方塊的 [主要] 視窗。 指派**識別碼** `ModalSegue`: 

    [![](dialog-images/new06.png "強制回應的 segue")](dialog-images/new06.png#lightbox)
7. 網路上任何**動作**並**輸出**: 

    [![](dialog-images/new04.png "設定動作")](dialog-images/new04.png#lightbox)
8. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

請`CustomDialogController.cs`檔案外觀如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

此程式碼會公開一些屬性來設定標題和描述的對話方塊和幾個事件，以回應對話方塊正在取消，或接受。

接著，編輯`ViewController.cs`檔案中，覆寫`PrepareForSegue`方法，使它看起來如下所示：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

此程式碼會初始化至我們的對話方塊在 Xcode 的 Interface Builder 中定義的 segue，並設定的標題和描述。 它也會處理使用者在對話方塊中所做的選擇。

我們可以執行我們的應用程式，並顯示自訂對話方塊：

[![](dialog-images/new05.png "範例對話方塊")](dialog-images/new05.png#lightbox)

如需使用 windows 在 Xamarin.Mac 應用程式的詳細資訊，請參閱我們[使用 Windows](~/mac/user-interface/window.md)文件。

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>建立自訂的工作表

A_表_是強制回應對話方塊附加至指定的文件視窗中，防止使用者互動的視窗，直到他們關閉對話方塊。 在工作表會附加至從中它會形成，和只有一張紙可能隨時都可以開啟視窗的視窗。 

若要建立 Xamarin.Mac 的自訂工作表，讓我們執行下列作業：

1. 在 [**方案總管] 中**，開啟`Main.storyboard`以在 Xcode 的 Interface Builder 中編輯的檔案。
2. 拖曳新**檢視控制器**到設計介面：

    [![](dialog-images/new01.png "從程式庫中選取檢視控制器")](dialog-images/new01.png#lightbox)
3. 設計您的使用者介面：

    [![](dialog-images/sheet01.png "UI 設計")](dialog-images/sheet01.png#lightbox)
4. 建立**工作表的 Segue**從您的主視窗至新的檢視控制器： 

    [![](dialog-images/sheet02.png "選取工作表 segue 類型")](dialog-images/sheet02.png#lightbox)
5. 在 **身分識別偵測器**，檢視控制器**類別** `SheetViewController`: 

    [![](dialog-images/sheet03.png "設定的類別名稱")](dialog-images/sheet03.png#lightbox)
6. 定義所需的任何**插座**並**動作**: 

    [![](dialog-images/sheet04.png "定義必要的輸出和動作")](dialog-images/sheet04.png#lightbox)
7. 儲存變更並返回 Visual Studio for Mac 中進行同步處理。

接著，編輯`SheetViewController.cs`檔案，並使它看起來如下：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

接著，編輯`ViewController.cs`檔案中，編輯`PrepareForSegue`方法，使它看起來如下所示：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

如果我們執行應用程式，並開啟工作表，它會附加至 視窗中：

[![](dialog-images/sheet08.png "範例工作表")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>建立喜好設定 對話方塊

我們來配置介面產生器中的 [喜好設定] 檢視之前，我們需要新增自訂的 segue 類型來處理切換移出喜好設定。 將新類別新增至您的專案，並呼叫它`ReplaceViewSeque`。 編輯類別，並讓它看起來如下：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

建立自訂 segue 的情況下，我們可以將新的視窗新增在 Xcode 的 Interface Builder 來處理我們的喜好設定。

若要加入新的視窗，執行下列作業：

1. 在 [**方案總管] 中**，開啟`Main.storyboard`以在 Xcode 的 Interface Builder 中編輯的檔案。
2. 拖曳新**視窗控制器**到設計介面：

    [![](dialog-images/pref01.png "選取 從程式庫的 視窗控制器")](dialog-images/pref01.png#lightbox)
3. 排列視窗上的靠近**單列**設計工具：

    [![](dialog-images/pref02.png "加入新的視窗")](dialog-images/pref02.png#lightbox)
4. 在您喜好設定 檢視中會有索引標籤，建立連接的檢視控制器的複本：

    [![](dialog-images/pref03.png "新增必要的檢視控制器")](dialog-images/pref03.png#lightbox)
5. 拖曳新**工具列控制器**從**程式庫**:

    [![](dialog-images/pref04.png "選取工具列控制器從程式庫")](dialog-images/pref04.png#lightbox)
6. 並將它放在設計介面中的視窗上：

    [![](dialog-images/pref05.png "加入新的工具列控制站")](dialog-images/pref05.png#lightbox)
7. 配置您的工具列的設計：

    [![](dialog-images/pref06.png "版面配置 工具列")](dialog-images/pref06.png#lightbox)
8. Control + 按一下，然後從每個拖曳**工具列按鈕**至您先前建立的檢視。 選取 **自訂**segue 類型：

    [![](dialog-images/pref07.png "設定 segue 類型")](dialog-images/pref07.png#lightbox)
9. 選取新的 Segue，並設定**類別**到`ReplaceViewSegue`:

    [![](dialog-images/pref08.png "設定 segue 類別")](dialog-images/pref08.png#lightbox)
10. 在  **Menubar 設計師**設計介面上，從應用程式功能表中選取**喜好設定...** ，control + 按一下並拖曳至 喜好設定 視窗，來建立**顯示**segue:

    [![](dialog-images/pref09.png "設定 segue 類型")](dialog-images/pref09.png#lightbox)
11. 儲存變更並返回 Visual Studio for Mac 中進行同步處理。

如果我們執行程式碼，並選取**喜好設定...** 從**應用程式功能表**，視窗隨即出現：

[![](dialog-images/pref10.png "範例的喜好設定 視窗")](dialog-images/pref10.png#lightbox)

如需有關使用 Windows 和工具列的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)並[工具列](~/mac/user-interface/toolbar.md)文件。

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>儲存及載入的喜好設定

在典型 macOS 應用程式，當使用者變更任何應用程式的使用者喜好設定，這些變更會自動儲存。 處理此 Xamarin.Mac 應用程式，最簡單的方式是建立單一類別以管理所有使用者的喜好設定，並共用整個系統。

首先，新增`AppPreferences`類別加入專案，並繼承自`NSObject`。 喜好設定會設計成使用[資料繫結和鍵值編碼](~/mac/app-fundamentals/databinding.md)這麼做會讓建立程序，並維護喜好設定形成更簡單。 因為喜好設定將會包含少量的簡單資料型別，使用內建`NSUserDefaults`來儲存和擷取值。

編輯`AppPreferences.cs`檔案，並使它看起來如下：

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLanguage")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLanguage", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLanguage");
                SaveInt ("DefaultLanguage", value, true);
                DidChangeValue ("DefaultLanguage");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

這個類別包含一些協助程式常式這類`SaveInt`， `LoadInt`， `SaveColor`， `LoadColor`，以便於使用的等`NSUserDefaults`更容易。 此外，因為`NSUserDefaults`沒有內建的方式來處理`NSColors`，則`NSColorToHexString`並`NSColorFromHexString`方法用來將色彩轉換成 web 為基礎的十六進位字串 (`#RRGGBBAA`其中`AA`是 alpha 透明度) 就會輕鬆地儲存和擷取。

在 `AppDelegate.cs`檔案中，建立的執行個體**AppPreferences**將用於應用程式範圍的物件：

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>連接至喜好設定檢視的喜好設定

接下來，連接到喜好設定 視窗和檢視上面所建立的 UI 元素的喜好設定的類別。 在 介面產生器中，選取 喜好設定的檢視控制器，並切換至**身分識別偵測器**，建立控制器的自訂類別： 

[![](dialog-images/prefs12.png "身分識別偵測器")](dialog-images/prefs12.png#lightbox)

切換回到 Visual Studio for Mac 將會同步處理您的變更，並開啟新建立的類別進行編輯。 請如下所示的類別：

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

請注意，此類別有完成以下兩件事：第一，沒有 helper`App`內容，以便能夠存取**AppDelegate**更容易。 第二個，`Preferences`屬性會公開全域**AppPreferences**類別與任何 UI 控制項繫結資料放在這個檢視表。

接下來，按兩下 分鏡腳本檔案，以重新開啟介面產生器中 （請參閱上述的變更只）。 拖曳到檢視中建置的喜好設定介面所需的任何 UI 控制項。 針對每個控制項中，切換至**繫結偵測器**並繫結至的個別屬性**AppPreference**類別：

[![](dialog-images/prefs13.png "繫結偵測器")](dialog-images/prefs13.png#lightbox)

所有面板 （檢視控制器） 重複上述步驟，並需要喜好設定的屬性。

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>套用喜好設定變更為 所有開啟的 Windows

如上所述，在典型的 macOS 應用程式，當使用者變更任何應用程式的使用者喜好設定，這些變更會自動儲存並套用到任何 windows 使用者可能必須在應用程式中開啟。

由仔細規劃和設計您的應用程式喜好設定與 windows 可讓終端使用者，使用最少量的程式碼撰寫工作發生順暢且透明地進行此程序。

將要使用的應用程式喜好設定的任何視窗，請將下列協助程式屬性新增至其內容的檢視控制器，才能存取我們**AppDelegate**更容易：

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

接下來，新增類別，以設定的內容或根據使用者的喜好設定的行為：

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

您必須呼叫設定方法，以確定它符合使用者的喜好設定第一次開啟視窗時：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

接著，編輯`AppDelegate.cs`檔案，並新增下列方法，以套用任何喜好設定變更為 所有開啟的視窗：

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

接下來，新增`PreferenceWindowDelegate`類別至專案，並使它看起來如下：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWindowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

這會導致任何喜好設定的變更時，喜好設定 視窗會關閉所有開啟的 Windows，傳送。

最後，編輯喜好設定視窗控制器，並加入上面所建立的委派：

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

與所有這些變更之後，如果使用者編輯應用程式的喜好設定，並關閉 [喜好設定] 視窗中，所做的變更會套用至所有開啟的 Windows:

[![](dialog-images/prefs14.png "範例的喜好設定 視窗")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>[開啟] 對話方塊

[開啟] 對話方塊可讓使用者以一致的方式，來尋找及開啟應用程式中的項目。 若要在 Xamarin.Mac 應用程式中顯示開啟的對話方塊，請使用下列程式碼：

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

在上述程式碼中，我們要開啟新的文件視窗，以顯示檔案的內容。 您要取代此應用程式所需要功能的程式碼。

使用時，會提供下列屬性`NSOpenPanel`:

- **CanChooseFiles** -如果`true`使用者可以選取檔案。
- **CanChooseDirectories** -如果`true`使用者可選取目錄。
- **AllowsMultipleSelection** -如果`true`使用者可以選取多個檔案一次。
- **ResolveAliases** -如果`true`選取與別名會將它解析成原始的檔案路徑。
- **AllowedFileTypes** -是的使用者可以選取其中一個延伸模組的檔案類型的字串陣列或_UTI_。 預設值是`null`，可讓任何開啟的檔案。

`RunModal ()`方法會顯示 開啟 對話方塊，並允許使用者選取檔案或目錄 （如屬性所指定），並傳回`1`如果使用者按一下**開啟** 按鈕。

[開啟] 對話方塊中 Url 的陣列形式傳回使用者的選取的檔案或目錄`URL`屬性。

如果我們執行程式，並選取**開啟...** 項目從**檔案**就會顯示下列功能表： 

[![](dialog-images/dialog03.png "開啟的對話方塊")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>「 列印和版面設定對話方塊

macOS 提供標準列印與頁面設定對話方塊，讓使用者可以擁有一致的列印功能，可以顯示您的應用程式的經驗，它們會使用每個應用程式。

下列程式碼將會顯示標準的 [列印] 對話方塊：

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

如果我們將`ShowPrintAsSheet`屬性設`false`，執行應用程式並顯示 [列印] 對話方塊，將會顯示下列：

[![](dialog-images/print01.png "列印對話方塊")](dialog-images/print01.png#lightbox)

如果設定`ShowPrintAsSheet`屬性設`true`，執行應用程式並顯示 [列印] 對話方塊，將會顯示下列：

[![](dialog-images/print02.png "列印工作表")](dialog-images/print02.png#lightbox)

下列程式碼會顯示 [頁面配置] 對話方塊：

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

如果我們將`ShowPrintAsSheet`屬性設`false`，執行應用程式並顯示 [整頁] 對話方塊，將會顯示下列：

[![](dialog-images/print03.png "版面設定對話方塊")](dialog-images/print03.png#lightbox)

如果設定`ShowPrintAsSheet`屬性設`true`，執行應用程式並顯示 [整頁] 對話方塊，將會顯示下列：

[![](dialog-images/print04.png "將頁面設定表")](dialog-images/print04.png#lightbox)

如需有關使用 「 列印和頁面設定對話方塊的詳細資訊，請參閱 Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092)， [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080)並[簡介列印](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1)文件。

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>儲存對話方塊

[儲存] 對話方塊可讓使用者以一致的方式儲存應用程式中的項目。

下列程式碼將會顯示標準的 [儲存] 對話方塊：

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

`AllowedFileTypes`屬性是使用者可以選擇將檔案儲存為的檔案類型的字串陣列。 檔案類型可以是指定為延伸模組或_UTI_。 預設值是`null`，可讓使用任何檔案類型。

如果我們將`ShowSaveAsSheet`屬性，以`false`，執行應用程式，並選取**另存新檔...** 從**檔案** 功能表中，將會顯示下列：

[![](dialog-images/save01.png "儲存對話方塊")](dialog-images/save01.png#lightbox)

使用者可以展開對話方塊：

[![](dialog-images/save02.png "擴充的儲存對話方塊")](dialog-images/save02.png#lightbox)

如果我們將`ShowSaveAsSheet`屬性，以`true`，執行應用程式，並選取**另存新檔...** 從**檔案** 功能表中，將會顯示下列：

[![](dialog-images/save03.png "儲存工作表")](dialog-images/save03.png#lightbox)

使用者可以展開對話方塊：

[![](dialog-images/save04.png "擴充的儲存工作表")](dialog-images/save04.png#lightbox)

如需有關使用 [儲存] 對話方塊的詳細資訊，請參閱 Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098)文件。

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解使用強制回應的 Windows、 試算表與 Xamarin.Mac 應用程式中的標準系統對話方塊。 我們可了解不同類型以及使用強制回應的 Windows、 試算表和對話方塊，如何建立和維護 Windows 強制回應和在 Xcode 中的工作表的介面產生器及如何使用強制回應的 Windows，工作表和 C# 程式碼中的對話方塊。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [功能表](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [工具列](~/mac/user-interface/toolbar.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [工作表的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [列印的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
