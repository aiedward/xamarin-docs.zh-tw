---
title: Xamarin 中的對話方塊
description: '本文涵蓋在 Xamarin. Mac 應用程式中使用對話方塊和強制回應視窗。 其中說明如何在 Xcode 和 Interface builder 中建立強制回應視窗、使用標準對話方塊，以及如何在 c # 程式碼中與這些控制項互動。'
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 0831ec2fae62d4e2230761a157a39f99f13b416a
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571658"
---
# <a name="dialogs-in-xamarinmac"></a>Xamarin 中的對話方塊

在 Xamarin. Mac 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在*目標-C*和*Xcode*中工作的相同對話方塊和強制回應視窗。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來建立和維護強制回應視窗（或選擇直接在 c # 程式碼中建立它們）。

隨即會出現一個對話方塊，以回應使用者動作，而且通常會提供使用者完成動作的方式。 對話方塊需要使用者的回應，才能關閉。

Windows 可以用於非模式狀態（例如可以同時開啟多個檔的文字編輯器）或強制回應（例如，必須先關閉才能繼續應用程式的 [匯出] 對話方塊）。

[![](dialog-images/dialog03.png "An open dialog box")](dialog-images/dialog03.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用對話方塊和強制回應視窗的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在本文中使用的重要概念和技巧。

您可能想要查看[Xamarin 內部](~/mac/internals/how-it-works.md)檔的將[c # 類別/方法公開至目標-C](~/mac/internals/how-it-works.md)一節，它會說明 `Register` `Export` 用來將 C # 類別連接至目標 C 物件和 UI 元素的和命令。

<a name="Introduction_to_Dialogs"></a>

## <a name="introduction-to-dialogs"></a>對話簡介

隨即會出現一個對話方塊，以回應使用者動作（例如儲存檔案），並提供使用者完成該動作的方法。 對話方塊需要使用者的回應，才能關閉。

根據 Apple，有三種方式可以呈現對話方塊：

- **檔**強制回應-檔強制回應對話方塊會防止使用者在指定檔中執行其他任何動作，直到它關閉為止。
- **應用**程式強制回應-應用程式強制回應對話方塊會防止使用者與應用程式進行互動，直到其關閉為止。
- 非**模式**非強制回應對話方塊可讓使用者在對話方塊中變更設定，同時仍然與文件視窗互動。

### <a name="modal-window"></a>強制回應視窗

任何標準 `NSWindow` 都可以藉由以強制回應方式顯示，當做自訂的對話使用：

[![](dialog-images/modal01.png "An example modal window")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>檔案模式對話方塊表

_工作表_是附加至指定文件視窗的強制回應對話方塊，可防止使用者與視窗互動，直到他們關閉對話方塊為止。 工作表會附加到它所從的視窗中，而且每次只能開啟一個視窗的工作表。

[![](dialog-images/sheet08.png "An example modal sheet")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>喜好設定視窗

[喜好設定] 視窗是非強制回應對話方塊，其中包含使用者不常變更的應用程式設定。 喜好設定 Windows 通常包含一個工具列，可讓使用者在不同的設定群組之間切換：

[![](dialog-images/dialog02.png "An example preference window")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>開啟對話方塊

[開啟] 對話方塊提供使用者一致的方式來尋找和開啟應用程式中的專案：

[![](dialog-images/dialog03.png "A open dialog box")](dialog-images/dialog03.png#lightbox)

### <a name="print-and-page-setup-dialogs"></a>列印和版面設定對話方塊

macOS 提供您的應用程式可以顯示的標準 [列印] 和 [版面設定] 對話方塊，讓使用者在其使用的每個應用程式中都可以擁有一致的列印體驗。

[列印] 對話方塊可以同時顯示為 [免費浮動] 對話方塊：

[![](dialog-images/print01.png "A print dialog box")](dialog-images/print01.png#lightbox)

或者，它可以顯示為工作表：

[![](dialog-images/print02.png "A print sheet")](dialog-images/print02.png#lightbox)

[版面設定] 對話方塊可以同時顯示為 [免費浮動] 對話方塊：

[![](dialog-images/print03.png "A page setup dialog")](dialog-images/print03.png#lightbox)

或者，它可以顯示為工作表：

[![](dialog-images/print04.png "A page setup sheet")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>儲存對話

[儲存] 對話方塊可讓使用者以一致的方式，將專案儲存在應用程式中。 [儲存] 對話方塊有兩個狀態： [**最小**] （也稱為折迭）：

[![](dialog-images/save01.png "A save dialog")](dialog-images/save01.png#lightbox)

和**展開**的狀態：

[![](dialog-images/save02.png "An expanded save dialog")](dialog-images/save02.png#lightbox)

[儲存**最少**] 對話方塊也可以顯示為工作表：

[![](dialog-images/save03.png "A minimal save sheet")](dialog-images/save03.png#lightbox)

也可以**展開**的儲存對話方塊：

[![](dialog-images/save04.png "An expanded save sheet")](dialog-images/save04.png#lightbox)

如需詳細資訊，請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[對話方塊](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1)一節

<a name="Adding_a_Modal_Window_to_a_Project"></a>

## <a name="adding-a-modal-window-to-a-project"></a>將強制回應視窗加入至專案

除了主文件視窗外，Xamarin. Mac 應用程式可能需要向使用者顯示其他類型的 windows，例如喜好設定或偵測器面板。

若要加入新的視窗，請執行下列動作：

1. 在**方案總管**中，開啟檔案，以 `Main.storyboard` 在 Xcode 的 Interface Builder 中進行編輯。
2. 將新的**View Controller**拖曳至 Design Surface：

    [![](dialog-images/new01.png "Selecting a View Controller from the Library")](dialog-images/new01.png#lightbox)
3. 在身分**識別偵測器**中， `CustomDialogController` 針對 [**類別名稱**] 輸入： 

    [![](dialog-images/new02.png "Setting the class name")](dialog-images/new02.png#lightbox)
4. 切換回 Visual Studio for Mac，讓它與 Xcode 同步並建立檔案 `CustomDialogController.h` 。
5. 返回 Xcode 並設計您的介面： 

    [![](dialog-images/new03.png "Designing the UI in Xcode")](dialog-images/new03.png#lightbox)
6. 從應用程式的主視窗中，建立強制回應**Segue**至新的 View Controller，方法是從會開啟對話方塊的 UI 專案拖曳到對話方塊視窗。 指派**識別碼** `ModalSegue` ： 

    [![](dialog-images/new06.png "A modal segue")](dialog-images/new06.png#lightbox)
7. 連接任何**動作**和**輸出**： 

    [![](dialog-images/new04.png "Configuring an Action")](dialog-images/new04.png#lightbox)
8. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

使檔案 `CustomDialogController.cs` 看起來如下所示：

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

這段程式碼會公開一些屬性，以設定對話方塊的標題和描述，以及一些事件來回應取消或接受的對話方塊。

接著，編輯檔案 `ViewController.cs` ，覆寫 `PrepareForSegue` 方法，使其看起來如下所示：

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

此程式碼會將我們在 Xcode 的 Interface Builder 中定義的 segue 初始化為對話，並設定標題和描述。 它也會處理使用者在對話方塊中所做的選擇。

我們可以執行應用程式並顯示自訂對話方塊：

[![](dialog-images/new05.png "An example dialog")](dialog-images/new05.png#lightbox)

如需在 Xamarin. Mac 應用程式中使用 windows 的詳細資訊，請參閱我們[的使用 windows](~/mac/user-interface/window.md)檔。

<a name="Creating_a_Custom_Sheet"></a>

## <a name="creating-a-custom-sheet"></a>建立自訂工作表

_工作表_是附加至指定文件視窗的強制回應對話方塊，可防止使用者與視窗互動，直到他們關閉對話方塊為止。 工作表會附加到它所從的視窗中，而且每次只能開啟一個視窗的工作表。 

若要在 Xamarin. Mac 中建立自訂工作表，讓我們執行下列動作：

1. 在**方案總管**中，開啟檔案，以 `Main.storyboard` 在 Xcode 的 Interface Builder 中進行編輯。
2. 將新的**View Controller**拖曳至 Design Surface：

    [![](dialog-images/new01.png "Selecting a View Controller from the Library")](dialog-images/new01.png#lightbox)
3. 設計您的使用者介面：

    [![](dialog-images/sheet01.png "The UI design")](dialog-images/sheet01.png#lightbox)
4. 從您的主視窗建立**工作表 Segue**至新的 View Controller： 

    [![](dialog-images/sheet02.png "Selecting the Sheet segue type")](dialog-images/sheet02.png#lightbox)
5. 在身分**識別偵測器**中，將視圖控制器的**類別**命名為 `SheetViewController` ： 

    [![](dialog-images/sheet03.png "Setting the class name")](dialog-images/sheet03.png#lightbox)
6. 定義任何所需的**輸出**和**動作**： 

    [![](dialog-images/sheet04.png "Defining the required Outlets and Actions")](dialog-images/sheet04.png#lightbox)
7. 儲存您的變更，並返回 Visual Studio for Mac 以進行同步處理。

接著，編輯檔案 `SheetViewController.cs` ，使其看起來如下所示：

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

接著，編輯檔案 `ViewController.cs` ，編輯方法， `PrepareForSegue` 使其看起來如下所示：

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

如果我們執行應用程式並開啟工作表，則會將它附加至視窗：

[![](dialog-images/sheet08.png "An example sheet")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog"></a>

## <a name="creating-a-preferences-dialog"></a>建立喜好設定對話方塊

在 Interface Builder 中配置喜好設定之前，我們必須新增自訂的 segue 類型，以處理切換出喜好設定。 將新類別新增至您的專案，並呼叫它 `ReplaceViewSeque` 。 編輯類別，使其看起來如下所示：

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

建立自訂 segue 之後，我們可以在 Xcode 的 Interface Builder 中新增視窗來處理我們的喜好設定。

若要加入新的視窗，請執行下列動作：

1. 在**方案總管**中，開啟檔案，以 `Main.storyboard` 在 Xcode 的 Interface Builder 中進行編輯。
2. 將新的**視窗控制器**拖曳至 Design Surface：

    [![](dialog-images/pref01.png "Select a Window Controller from the Library")](dialog-images/pref01.png#lightbox)
3. 將視窗排列在**功能表列**設計工具附近：

    [![](dialog-images/pref02.png "Adding the new Window")](dialog-images/pref02.png#lightbox)
4. 建立附加視圖控制器的複本，因為您的喜好設定視圖中會有索引標籤：

    [![](dialog-images/pref03.png "Adding the required View Controllers")](dialog-images/pref03.png#lightbox)
5. 從連結**庫**拖曳新的**工具列控制器**：

    [![](dialog-images/pref04.png "Select a Toolbar Controller from the Library")](dialog-images/pref04.png#lightbox)
6. 然後將它放在 Design Surface 的視窗上：

    [![](dialog-images/pref05.png "Adding a new Toolbar Controller")](dialog-images/pref05.png#lightbox)
7. 版面組態工具欄的設計：

    [![](dialog-images/pref06.png "Layout the toolbar")](dialog-images/pref06.png#lightbox)
8. 按住 Control 並從每個**工具列按鈕**拖曳到您在上方建立的 Views。 選取**自訂**的 segue 類型：

    [![](dialog-images/pref07.png "Setting the segue type")](dialog-images/pref07.png#lightbox)
9. 選取新的 Segue，並將**類別**設定為 `ReplaceViewSegue` ：

    [![](dialog-images/pref08.png "Setting the segue class")](dialog-images/pref08.png#lightbox)
10. 在 [Design Surface 上的**功能表列設計**工具中，從 [應用程式] 功能表選取 [**喜好設定 ...**]，然後按一下並拖曳至 [喜好設定] 視窗，以建立 [**顯示**] segue：

    [![](dialog-images/pref09.png "Setting the segue type")](dialog-images/pref09.png#lightbox)
11. 儲存您的變更，並返回 Visual Studio for Mac 以進行同步處理。

如果我們執行程式碼，並從 [**應用程式] 功能表**中選取 [**喜好設定 ...** ]，就會顯示視窗：

[![](dialog-images/pref10.png "An example preferences window")](dialog-images/pref10.png#lightbox)

如需有關使用 Windows 和工具列的詳細資訊，請參閱我們的[windows](~/mac/user-interface/window.md)和[工具列](~/mac/user-interface/toolbar.md)檔。

<a name="Saving-and-Loading-Preferences"></a>

### <a name="saving-and-loading-preferences"></a>儲存和載入喜好設定

在典型的 macOS 應用程式中，當使用者對應用程式的任何使用者喜好設定進行變更時，這些變更會自動儲存。 在 Xamarin. Mac 應用程式中處理這項工作的最簡單方式，就是建立單一類別來管理所有使用者的喜好設定，並共用整個系統。

首先，將新 `AppPreferences` 類別加入至專案，並繼承自 `NSObject` 。 喜好設定將設計成使用資料系結[和索引鍵-值編碼](~/mac/app-fundamentals/databinding.md)，這會使建立和維護喜好設定表單的程式變得更簡單。 因為喜好設定會包含少量的單一資料型別，所以請使用內建的 `NSUserDefaults` 來儲存和抓取值。

編輯檔案 `AppPreferences.cs` ，使其看起來如下所示：

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

這個類別包含幾個 helper 常式，例如 `SaveInt` 、 `LoadInt` 、 `SaveColor` 、等，讓您 `LoadColor` `NSUserDefaults` 更容易使用。 此外，由於沒有 `NSUserDefaults` 內建的處理方法 `NSColors` ， `NSColorToHexString` 因此會使用和方法，將 `NSColorFromHexString` 色彩轉換成以 web 為基礎的十六進位字串（ `#RRGGBBAA` 其中 `AA` 是 Alpha 透明度），可以輕鬆地加以儲存和抓取。

在檔案中 `AppDelegate.cs` ，建立將用於整個應用程式的**AppPreferences**物件實例：

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

<a name="Wiring-Preferences-to-Preference-Views"></a>

### <a name="wiring-preferences-to-preference-views"></a>喜好設定視圖的接線喜好設定

接下來，將喜好設定類別連接至喜好設定視窗上的 UI 專案，以及上面建立的 Views。 在 Interface Builder 中，選取 [喜好設定視圖控制器]，並切換至 [身分**識別偵測器**]，建立控制器的自訂類別： 

[![](dialog-images/prefs12.png "The Identity Inspector")](dialog-images/prefs12.png#lightbox)

切換回 Visual Studio for Mac 以同步處理您的變更，並開啟新建立的類別進行編輯。 讓類別看起來如下所示：

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

請注意，這個類別在此有兩個專案：首先，有一個 helper `App` 屬性可讓您更輕鬆地存取**AppDelegate** 。 第二， `Preferences` 屬性會公開全域**AppPreferences**類別，以便與此視圖上的任何 UI 控制項進行資料系結。

接下來，按兩下分鏡腳本檔案，在 Interface Builder 中重新開啟它（並查看上述所做的變更）。 將建立喜好設定介面所需的任何 UI 控制項拖曳到此視圖中。 針對每個控制項，切換至系結偵測**器**，並系結至**AppPreference**類別的個別屬性：

[![](dialog-images/prefs13.png "The Binding Inspector")](dialog-images/prefs13.png#lightbox)

針對所有需要的面板（View controller）和喜好設定屬性重複上述步驟。

<a name="Applying-Preference-Changes-to-All-Open-Windows"></a>

### <a name="applying-preference-changes-to-all-open-windows"></a>將喜好設定變更套用至所有開啟的視窗

如上所述，在一般的 macOS 應用程式中，當使用者對應用程式的任何使用者喜好設定進行變更時，這些變更會自動儲存並套用至使用者可能已在應用程式中開啟的任何視窗。

仔細規劃和設計您的應用程式的喜好設定和 windows，可讓使用者以最少的編碼工作，順暢且透明地進行這項處理。

針對將使用應用程式喜好設定的任何視窗，將下列 helper 屬性新增至其內容視圖控制器，以更輕鬆地存取我們的**AppDelegate** ：

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

接下來，新增類別，以根據使用者的喜好設定內容或行為：

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

當視窗第一次開啟時，您必須呼叫設定方法，以確保它符合使用者的喜好設定：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

接著，編輯檔案 `AppDelegate.cs` 並新增下列方法，以將任何喜好設定變更套用至所有開啟的視窗：

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

接下來，將 `PreferenceWindowDelegate` 類別新增至專案，使其看起來如下所示：

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

這會導致在喜好設定視窗關閉時，將任何喜好設定變更傳送到所有開啟的視窗。

最後，編輯喜好設定視窗控制器，並新增上面所建立的委派：

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

當所有這些變更都備妥之後，如果使用者編輯應用程式的喜好設定並關閉喜好設定視窗，這些變更將會套用到所有開啟的視窗：

[![](dialog-images/prefs14.png "An example preferences window")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog"></a>

## <a name="the-open-dialog"></a>[開啟] 對話方塊

[開啟] 對話方塊提供使用者一致的方式來尋找和開啟應用程式中的專案。 若要在 Xamarin. Mac 應用程式中顯示開啟的對話方塊，請使用下列程式碼：

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

在上述程式碼中，我們會開啟新的文件視窗，以顯示檔案的內容。 您必須使用應用程式所需的功能來取代此程式碼。

使用時，可以使用下列屬性 `NSOpenPanel` ：

- **CanChooseFiles** -如果 `true` 使用者可以選取檔案。
- **CanChooseDirectories** -如果 `true` 使用者可以選取目錄。
- **AllowsMultipleSelection** -如果 `true` 使用者可以一次選取一個以上的檔案。
- **ResolveAliases** -如果 `true` 選取和別名，則會將它解析成原始檔案的路徑。
- **AllowedFileTypes** -這是一種檔案類型的字串陣列，使用者可以將其選取為副檔名或_UTI_。 預設值為 `null` ，允許開啟任何檔案。

`RunModal ()`方法會顯示 [開啟] 對話方塊，並允許使用者選取檔案或目錄（如屬性所指定），並在 `1` 使用者按一下 [**開啟**] 按鈕時傳回。

[開啟] 對話方塊會傳回使用者選取的檔案或目錄，做為屬性中的 Url 陣列 `URL` 。

如果我們執行程式，並從 [檔案 **] 功能表中選取 [** **開啟 ...** ] 專案，則會顯示下列內容： 

[![](dialog-images/dialog03.png "An open dialog box")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs"></a>

## <a name="the-print-and-page-setup-dialogs"></a>列印和版面設定對話方塊

macOS 提供您的應用程式可以顯示的標準 [列印] 和 [版面設定] 對話方塊，讓使用者在其使用的每個應用程式中都可以擁有一致的列印體驗。

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

如果我們將 `ShowPrintAsSheet` 屬性設定為 `false` ，執行應用程式並顯示 [列印] 對話方塊，將會顯示下列內容：

[![](dialog-images/print01.png "A print dialog box")](dialog-images/print01.png#lightbox)

如果將 `ShowPrintAsSheet` 屬性設定為 `true` ，請執行應用程式並顯示 [列印] 對話方塊，將會顯示下列內容：

[![](dialog-images/print02.png "A print sheet")](dialog-images/print02.png#lightbox)

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

如果我們將 `ShowPrintAsSheet` 屬性設定為 `false` ，執行應用程式並顯示 [列印版面配置] 對話方塊，將會顯示下列內容：

[![](dialog-images/print03.png "A page setup dialog")](dialog-images/print03.png#lightbox)

如果將 `ShowPrintAsSheet` 屬性設定為 `true` ，請執行應用程式並顯示 [列印版面配置] 對話方塊，將會顯示下列內容：

[![](dialog-images/print04.png "A page setup sheet")](dialog-images/print04.png#lightbox)

如需有關使用 [列印] 和 [頁面設定] 對話方塊的詳細資訊，請參閱 Apple 的[NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092)和[NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080)檔。

<a name="The_Save_Dialog"></a>

## <a name="the-save-dialog"></a>[儲存] 對話方塊

[儲存] 對話方塊可讓使用者以一致的方式，將專案儲存在應用程式中。

下列程式碼會顯示標準儲存對話方塊：

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

`AllowedFileTypes`屬性是檔案類型的字串陣列，使用者可以選擇將檔案儲存為。 檔案類型可以指定為副檔名或_UTI_。 預設值為 `null` ，允許使用任何檔案類型。

如果我們將 `ShowSaveAsSheet` 屬性設定為 `false` ，請執行應用程式，然後從 [檔案] 功能表中**File**選取 [**另存**新檔 ...]，將會顯示下列內容：

[![](dialog-images/save01.png "A save dialog box")](dialog-images/save01.png#lightbox)

使用者可以展開對話方塊：

[![](dialog-images/save02.png "An expanded save dialog box")](dialog-images/save02.png#lightbox)

如果我們將 `ShowSaveAsSheet` 屬性設定為 `true` ，請執行應用程式，然後從 [檔案] 功能表中**File**選取 [**另存**新檔 ...]，將會顯示下列內容：

[![](dialog-images/save03.png "A save sheet")](dialog-images/save03.png#lightbox)

使用者可以展開對話方塊：

[![](dialog-images/save04.png "An expanded save sheet")](dialog-images/save04.png#lightbox)

如需使用 [儲存] 對話方塊的詳細資訊，請參閱 Apple 的[NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098)檔。

<a name="Summary"></a>

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用強制回應視窗、工作表和標準系統對話方塊。 我們看到了強制回應視窗、工作表和對話方塊的不同類型和用法，如何在 Xcode 的 Interface Builder 中建立和維護強制回應視窗和工作表，以及如何在 c # 程式碼中使用強制回應視窗、工作表和對話方塊。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [功能表](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [工具列](~/mac/user-interface/toolbar.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [工作表簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [列印簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)
