---
title: "對話方塊"
description: "本文件涵蓋使用對話方塊和 Xamarin.Mac 應用程式中的強制回應視窗。 它描述在安裝 Xcode 和介面產生器中，使用標準的對話方塊，並與其互動 C# 程式碼中的這些控制項中建立強制回應視窗。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 661874ae8599fed35ce10213fece383eb81de94d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="dialogs"></a>對話方塊

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同的對話方塊和強制回應視窗，工作的開發人員*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的強制回應視窗 （或您可以選擇直接在 C# 程式碼中建立它們）。

對話方塊會出現在回應使用者動作，並且通常會提供方法的使用者可以完成這個動作。 可被關閉前，對話方塊會要求使用者回應。

視窗可以是兩個或非強制回應的狀態 （例如可以有多個同時開啟的文件的文字編輯器） 中使用強制回應 （例如必須在應用程式才能繼續解除匯出對話方塊）。

[![](dialog-images/dialog03.png "開啟的對話方塊")](dialog-images/dialog03.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用的對話方塊和強制回應視窗的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>對話的簡介

對話方塊會出現在回應使用者動作 （例如儲存檔案），並提供方法讓使用者以完成該動作。 可被關閉前，對話方塊會要求使用者回應。

根據 Apple，有三種方法來顯示對話方塊：

- **文件 Modal** -文件強制回應對話方塊會防止使用者進行給定文件內的其他任何動作，直到關閉。
- **應用程式的強制回應**-的強制回應對話方塊會防止使用者與應用程式互動，直到關閉應用程式。
- **非強制回應**非強制回應對話方塊可讓使用者變更對話方塊中的設定仍文件視窗進行互動時。

### <a name="modal-window"></a>強制回應視窗

任何標準`NSWindow`可供為自訂對話方塊以強制回應方式顯示：

[![](dialog-images/modal01.png "範例強制回應視窗")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>文件的強制回應對話方塊工作表

A_表_是附加至指定的文件視窗，避免使用者互動的視窗，直到關閉對話方塊，請將它們強制回應對話方塊。 在工作表會附加至從中它出現時，只有一個工作表可能在任何時候都可以開啟視窗的視窗。

[![](dialog-images/sheet08.png "範例獨佔式工作表")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>喜好設定 Windows

喜好設定 視窗是強制回應對話方塊，其中包含使用者不常變更的應用程式的設定。 喜好設定 Windows 通常包含工具列，可讓使用者切換不同的設定群組：

[![](dialog-images/dialog02.png "範例喜好設定 視窗")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>開啟的對話方塊

[開啟] 對話方塊會提供使用者一致的方法來尋找及開啟應用程式中的項目：

[![](dialog-images/dialog03.png "開啟對話方塊")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>列印和 版面設定對話方塊

macOS 提供標準列印，並安裝對話方塊，讓使用者可以擁有一致的列印功能，可顯示您的應用程式頁面中會使用每個應用程式體驗。

[列印] 對話方塊可以顯示為兩個免費浮動對話方塊：

[![](dialog-images/print01.png "列印對話方塊")](dialog-images/print01.png#lightbox)

或者，它可以顯示為工作表：

[![](dialog-images/print02.png "列印工作表")](dialog-images/print02.png#lightbox)

版面設定對話方塊可以顯示為兩個免費浮動對話方塊：

[![](dialog-images/print03.png "版面設定對話方塊")](dialog-images/print03.png#lightbox)

或者，它可以顯示為工作表：

[![](dialog-images/print04.png "頁面安裝程式工作表")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>儲存對話方塊

[儲存] 對話方塊可讓使用者將項目儲存在應用程式一致的方式。 儲存對話方塊有兩種狀態：**最少**（也稱為摺疊）：

[![](dialog-images/save01.png "儲存對話方塊")](dialog-images/save01.png#lightbox)

和**加寬**狀態：

[![](dialog-images/save02.png "展開 [儲存] 對話方塊")](dialog-images/save02.png#lightbox)

**最少**儲存對話方塊也會顯示為工作表頁：

[![](dialog-images/save03.png "最小的儲存工作表")](dialog-images/save03.png#lightbox)

可能是**加寬**儲存對話方塊：

[![](dialog-images/save04.png "展開 儲存的工作表")](dialog-images/save04.png#lightbox)

如需詳細資訊，請參閱[對話方塊](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>加入專案中的強制回應視窗

主要的文件視窗中，除了 Xamarin.Mac 應用程式可能需要其他類型的視窗顯示給使用者，例如喜好設定或偵測器面板。

若要加入新的視窗，執行下列作業：

1. 在**方案總管 中**，開啟`Main.storyboard`Xcode 的介面產生器中進行編輯的檔案。
2. 拖曳新**檢視控制器**到設計介面：

    [![](dialog-images/new01.png "從程式庫選取檢視控制器")](dialog-images/new01.png#lightbox)
3. 在**識別 Inspector**，輸入`CustomDialogController`如**類別名稱**: 

    [![](dialog-images/new02.png "設定類別名稱")](dialog-images/new02.png#lightbox)
4. 切換回 Visual Studio for Mac，允許同步 Xcode，並建立`CustomDialogController.h`檔案。
5. 返回 Xcode，並設計您的介面： 

    [![](dialog-images/new03.png "設計在 Xcode 中 UI")](dialog-images/new03.png#lightbox)
6. 建立**強制回應話題**從新檢視控制器控制項拖曳至您應用程式，將會開啟對話方塊，對話方塊視窗的 UI 項目從 主視窗。 指派**識別碼** `ModalSegue`: 

    [![](dialog-images/new06.png "強制回應 segue")](dialog-images/new06.png#lightbox)
6. 網路上任何**動作**和**插座**: 

    [![](dialog-images/new04.png "設定動作")](dialog-images/new04.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

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

這段程式碼公開一些屬性可用來設定標題和描述的對話方塊與一些事件以回應被取消，或接受對話方塊。

接著，編輯`ViewController.cs`檔案，請覆寫`PrepareForSegue`方法並看起來如下所示：

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

此程式碼初始化 segue 我們對話方塊定義 Xcode 的介面產生器，並設定標題和描述。 它也會處理使用者在對話方塊中所做的選擇。

我們可以執行我們的應用程式，並顯示 [自訂] 對話方塊：

[![](dialog-images/new05.png "範例對話方塊")](dialog-images/new05.png#lightbox)

如需有關如何使用 windows Xamarin.Mac 應用程式中的詳細資訊，請參閱我們[使用視窗](~/mac/user-interface/window.md)文件。

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>建立自訂工作表

A_表_是附加至指定的文件視窗，避免使用者互動的視窗，直到關閉對話方塊，請將它們強制回應對話方塊。 在工作表會附加至從中它出現時，只有一個工作表可能在任何時候都可以開啟視窗的視窗。 

若要建立自訂工作表 Xamarin.Mac，讓我們執行下列動作：

1. 在**方案總管 中**，開啟`Main.storyboard`Xcode 的介面產生器中進行編輯的檔案。
2. 拖曳新**檢視控制器**到設計介面：

    [![](dialog-images/new01.png "從程式庫選取檢視控制器")](dialog-images/new01.png#lightbox)
2. 設計使用者介面：

    [![](dialog-images/sheet01.png "UI 設計")](dialog-images/sheet01.png#lightbox)
3. 建立**表話題**從主視窗中，新檢視控制站： 

    [![](dialog-images/sheet02.png "選取工作表 segue 類型")](dialog-images/sheet02.png#lightbox)
4. 在**識別 Inspector**，檢視控制器**類別** `SheetViewController`: 

    [![](dialog-images/sheet03.png "設定類別名稱")](dialog-images/sheet03.png#lightbox)
5. 定義所需的任何**插座**和**動作**: 

    [![](dialog-images/sheet04.png "定義必要的插座和動作")](dialog-images/sheet04.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 同步處理。

接著，編輯`SheetViewController.cs`檔案，並讓它看起來如下所示：

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

接著，編輯`ViewController.cs`檔案中，編輯`PrepareForSegue`方法並看起來如下所示：

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

如果我們執行我們的應用程式，並開啟工作表，它會附加至視窗中：

[![](dialog-images/sheet08.png "範例工作表")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>建立喜好設定對話方塊

我們配置喜好設定中的檢視介面產生器之前，我們需要加入自訂 segue 型別以處理切換移出喜好設定。 將新類別加入您的專案，並為它`ReplaceViewSeque `。 編輯類別，並讓它看起來如下所示：

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

建立自訂 segue，我們可以在 Xcode 的介面產生器來處理我們喜好設定中新增新的視窗。

若要加入新的視窗，執行下列作業：

1. 在**方案總管 中**，開啟`Main.storyboard`Xcode 的介面產生器中進行編輯的檔案。
2. 拖曳新**視窗控制器**到設計介面：

    [![](dialog-images/pref01.png "從程式庫中選取的視窗控制站")](dialog-images/pref01.png#lightbox)
3. 排列靠近視窗**功能表列**設計工具：

    [![](dialog-images/pref02.png "加入新的視窗")](dialog-images/pref02.png#lightbox)
4. 建立附加檢視控制站的複本，在您喜好設定 檢視中會有索引標籤如下：

    [![](dialog-images/pref03.png "新增必要的檢視控制站")](dialog-images/pref03.png#lightbox)
5. 拖曳新**工具列控制器**從**文件庫**:

    [![](dialog-images/pref04.png "從程式庫選取工具列控制器")](dialog-images/pref04.png#lightbox)
6. 並將它放在設計介面中的視窗上：

    [![](dialog-images/pref05.png "加入新的工具列控制器")](dialog-images/pref05.png#lightbox)
7. 配置的設計工具列：

    [![](dialog-images/pref06.png "版面配置工具列")](dialog-images/pref06.png#lightbox)
8. 控制項按一下和拖曳每個**工具列按鈕**至先前建立的檢視。 選取**自訂**話題類型：

    [![](dialog-images/pref07.png "設定 segue 類型")](dialog-images/pref07.png#lightbox)
9. 選取新 Segue 並設定**類別**至`ReplaceViewSegue`:

    [![](dialog-images/pref08.png "設定 segue 類別")](dialog-images/pref08.png#lightbox)
10. 在**Menubar 設計師**設計介面上，從應用程式功能表中選取**喜好設定...**，控制按一下並拖曳至喜好設定的視窗，以建立**顯示**話題：

    [![](dialog-images/pref09.png "設定 segue 類型")](dialog-images/pref09.png#lightbox)
11. 儲存變更並返回 Visual Studio for Mac 同步處理。

如果我們執行程式碼，並選取**喜好設定...**從**應用程式功能表**，視窗會顯示：

[![](dialog-images/pref10.png "範例喜好設定 視窗")](dialog-images/pref10.png#lightbox)

如需使用視窗和工具列的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)和[工具列](~/mac/user-interface/toolbar.md)文件。

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>儲存及載入的喜好設定

在典型 macOS 應用程式，當使用者變更任何應用程式的使用者喜好設定，這些變更會自動儲存。 最簡單的方式來處理在 Xamarin.Mac 應用程式中，這是建立單一類別來管理所有使用者的喜好設定，並共用全系統。

首先，新增`AppPreferences`類別至專案，並繼承自`NSObject`。 喜好設定會設計成使用[資料繫結和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)都將建立的程序，並維護此喜好設定形成更簡單。 因為喜好設定將會包含少量簡單資料類型，使用內建`NSUserDefaults`來儲存和擷取值。

編輯`AppPreferences.cs`檔案，並讓它看起來如下所示：

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
        [Export("DefaultLangauge")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLangauge", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLangauge");
                SaveInt ("DefaultLangauge", value, true);
                DidChangeValue ("DefaultLangauge");
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

這個類別包含一些 helper 常式，例如`SaveInt`， `LoadInt`， `SaveColor`， `LoadColor`，讓使用等`NSUserDefaults`更容易。 此外，因為`NSUserDefaults`並沒有內建的方法來處理`NSColors`、`NSColorToHexString`和`NSColorFromHexString`方法可用來將色彩轉換成 web 為基礎的十六進位字串 (`#RRGGBBAA`其中`AA`是 alpha 透明)，可以是輕鬆地儲存和擷取。

在`AppDelegate.cs`檔案中，建立的執行個體**AppPreferences**用整個應用程式的物件：

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

接下來，連線喜好設定類別上的喜好設定 視窗和檢視上面所建立的 UI 項目。 在介面產生器中，選取喜好設定檢視控制器，並切換至**識別 Inspector**，建立控制器的自訂類別： 

[![](dialog-images/prefs12.png "身分識別檢查器")](dialog-images/prefs12.png#lightbox)

切換回 Visual Studio for Mac 同步您的變更，並開啟新建立的類別進行編輯。 請如下所示的類別：

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

請注意，這個類別已完成，以下兩件事： 首先，為 helper`App`屬性進行存取**AppDelegate**更容易。 第二個，`Preferences`屬性會公開全域**AppPreferences**類別資料繫結與任何 UI 控制項放置在這個檢視表。

接下來，按兩下 分鏡腳本檔案以重新開啟介面產生器中 （請參閱上述的變更只）。 拖曳到檢視建置喜好設定介面所需的任何 UI 控制項。 針對每個控制項中，切換至**繫結的偵測器**並繫結的個別屬性**AppPreference**類別：

[![](dialog-images/prefs13.png "繫結器")](dialog-images/prefs13.png#lightbox)

所有的面板 （檢視控制站） 重複上述步驟，喜好設定屬性必要項目。

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>套用喜好設定變更為 所有開啟的視窗

如同前面所述，在典型的 macOS 應用程式，當使用者變更任何應用程式的使用者喜好設定，這些變更會自動儲存並套用到任何 windows 使用者可能必須在應用程式中開啟。

務必先謹慎規劃和設計您的應用程式喜好設定和 windows 可讓使用者利用最少量的程式碼撰寫工作發生順暢且透明地進行此程序。

將要使用應用程式喜好設定任何視窗，請將下列 helper 屬性加入至其內容的檢視控制站以進行存取我們**AppDelegate**更容易：

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

接下來，將類別加入設定的內容或根據使用者的喜好設定的行為：

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

您需要先確認其符合使用者的喜好設定第一次開啟視窗時呼叫設定方法：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

接著，編輯`AppDelegate.cs`檔案，然後加入下列的方法，以套用任何喜好設定變更為 所有開啟的視窗：

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

接下來，加入`PreferenceWindowDelegate`類別至專案，並看起來如下所示：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWidowDelegate : NSWindowDelegate
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
        public PreferenceWidowDelegate (NSWindow window)
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

這會導致任何喜好設定變更喜好設定 視窗關閉時傳送至所有開啟的視窗。

最後，編輯喜好設定視窗控制站，並加入上面所建立的委派：

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
            Window.Delegate = new PreferenceWidowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

在進行這些變更，如果使用者編輯應用程式的喜好設定並關閉 [喜好設定] 視窗中，所做的變更會套用至所有開啟的視窗：

[![](dialog-images/prefs14.png "範例喜好設定 視窗")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>[開啟] 對話方塊

[開啟] 對話方塊可讓使用者一致的方法來尋找及開啟應用程式中的項目。 若要在 Xamarin.Mac 應用程式中顯示開啟的對話方塊，請使用下列程式碼：

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

在上述程式碼中，我們正在開啟新的文件視窗，以顯示檔案的內容。 您要取代此應用程式所需要功能的程式碼。

使用時，會提供下列屬性`NSOpenPanel`:

- **CanChooseFiles** -如果`true`使用者可以選取檔案。
- **CanChooseDirectories** -如果`true`使用者可選取目錄。
- **AllowsMultipleSelection** -如果`true`使用者可以一次選取多個檔案。
- **ResolveAliases** -如果`true`選取和別名，會將它解析成原始的檔案路徑。
- **AllowedFileTypes** -是的使用者可以選取做為任一個擴充的檔案類型的字串陣列或_UTI_。 預設值是`null`，可讓任何開啟的檔案。

`RunModal ()`方法會顯示 開啟 對話方塊可讓使用者選取檔案或目錄 （如屬性所指定） 和傳回`1`如果使用者按一下**開啟** 按鈕。

[開啟] 對話方塊會傳回使用者的選取的檔案或目錄做為陣列中的 Url 的`URL`屬性。

如果我們執行程式並選取**開啟...**項目從**檔案**就會顯示下列功能表： 

[![](dialog-images/dialog03.png "開啟的對話方塊")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>「 列印和 版面設定對話方塊

macOS 提供標準列印，並安裝對話方塊，讓使用者可以擁有一致的列印功能，可顯示您的應用程式頁面中會使用每個應用程式體驗。

下列程式碼會顯示標準的 [列印] 對話方塊：

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

如果我們將`ShowPrintAsSheet`屬性`false`，執行應用程式並顯示 [列印] 對話方塊，將會顯示下列：

[![](dialog-images/print01.png "列印對話方塊")](dialog-images/print01.png#lightbox)

如果設定`ShowPrintAsSheet`屬性`true`，執行應用程式並顯示 [列印] 對話方塊，將會顯示下列：

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

如果我們將`ShowPrintAsSheet`屬性`false`，執行應用程式和顯示 [列印版面配置] 對話方塊，將會顯示下列：

[![](dialog-images/print03.png "版面設定對話方塊")](dialog-images/print03.png#lightbox)

如果設定`ShowPrintAsSheet`屬性`true`，執行應用程式和顯示 [列印版面配置] 對話方塊，將會顯示下列：

[![](dialog-images/print04.png "頁面安裝程式工作表")](dialog-images/print04.png#lightbox)

如需有關使用 「 列印和頁面設定對話方塊的詳細資訊，請參閱 Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092)， [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080)和[簡介列印](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1)文件。

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>儲存對話方塊

[儲存] 對話方塊可讓使用者將項目儲存在應用程式一致的方式。

下列程式碼會顯示標準的 [儲存] 對話方塊：

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

`AllowedFileTypes`屬性是使用者可以選擇將檔案儲存為的檔案類型的字串陣列。 檔案類型可以是指定做為擴充或_UTI_。 預設值是`null`，可讓使用任何檔案類型。

如果我們將`ShowSaveAsSheet`屬性`false`，執行應用程式，並選取**另存新檔...**從**檔案**功能表上，將會顯示下列：

[![](dialog-images/save01.png "儲存對話方塊")](dialog-images/save01.png#lightbox)

使用者可以展開對話方塊：

[![](dialog-images/save02.png "展開 [儲存] 對話方塊")](dialog-images/save02.png#lightbox)

如果我們將`ShowSaveAsSheet`屬性`true`，執行應用程式，並選取**另存新檔...**從**檔案**功能表上，將會顯示下列：

[![](dialog-images/save03.png "儲存工作表")](dialog-images/save03.png#lightbox)

使用者可以展開對話方塊：

[![](dialog-images/save04.png "展開 儲存的工作表")](dialog-images/save04.png#lightbox)

如需使用 [儲存] 對話方塊的更詳細資訊，請參閱 Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098)文件。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得詳細的說明使用強制回應視窗、 表和 Xamarin.Mac 應用程式中的標準系統對話方塊。 我們了解不同類型和使用的強制回應視窗，工作表及對話方塊中，如何建立和維護強制回應視窗，並在 Xcode 中的工作表的介面產生器及如何使用強制回應視窗，工作表，以及 C# 程式碼對話方塊。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [功能表](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [工具列](~/mac/user-interface/toolbar.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [表簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [列印簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
