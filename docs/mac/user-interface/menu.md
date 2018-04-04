---
title: Menus
description: 本文件涵蓋使用 Xamarin.Mac 應用程式中的功能表。 它說明建立和維護功能表和功能表項目在 Xcode 和介面產生器，並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 50c9cf333ff7965bbdfbb964a2301e677eb6aa59
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="menus"></a>Menus

_本文件涵蓋使用 Xamarin.Mac 應用程式中的功能表。它說明建立和維護功能表和功能表項目在 Xcode 和介面產生器，並以程式設計方式使用它們。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您會有在 Objective C 和 Xcode 中工作的開發人員必須執行的相同 Cocoa 功能表存取。 因為 Xamarin.Mac 會直接與 Xcode 整合，您可以使用 Xcode 的介面產生器建立，並維護您的功能表列、 功能表和功能表項目 （或您可以選擇直接在 C# 程式碼中建立它們）。

功能表的 Mac 應用程式的使用者經驗中不可或缺的一部分，而且通常會出現在使用者介面的各個部分：

- **應用程式的功能表列**-這是主功能表，在每個 Mac 應用程式的畫面最上方會出現。
- **關聯式功能表**-這些會出現在使用者上按一下滑鼠右鍵或按一下控制項視窗中的項目時。
- **[狀態] 列**-這是在應用程式功能表列 （功能表列時鐘的左邊） 畫面頂端會出現，並隨著左邊加入項目最右側的區域。
- **停駐功能表**-每個應用程式中停駐的功能表顯示當使用者以滑鼠右鍵按一下控制項點選應用程式的圖示，或當使用者 left-clicks 圖示，並按住滑鼠按鈕。
- **快顯的按鈕和下拉式清單**-快顯的按鈕會顯示選取的項目，並會顯示使用者所按下時，從選取的選項清單。 下拉式清單是按鈕的一種快顯通常用來選取特定的目前工作的內容命令。 同時可以在視窗中任何位置出現。

[![範例功能表](menu-images/intro01.png "範例功能表")](menu-images/intro01-large.png#lightbox)

在本文中，我們將討論使用 Cocoa 功能表列、 功能表和功能表項目在 Xamarin.Mac 應用程式的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="the-applications-menu-bar"></a>應用程式的功能表列 

有別於 Windows 作業系統，其中每個視窗可以有它自己附加的功能表列上執行的應用程式，macOS 上執行每個應用程式具有單一的功能表列執行使用針對該應用程式中的每一個視窗的螢幕的上方：

[![功能表列](menu-images/appmenu01.png "功能表列")](menu-images/appmenu01-large.png#lightbox)

此功能表列上的項目會啟用或停用在任何給定時刻根據目前內容或應用程式和其使用者介面的狀態。 例如： 如果使用者選取的文字欄位，項目上**編輯**功能表將會啟用這類**複製**和**剪下**。

根據 Apple 而且依預設，所有 macOS 應用程式就會都有一組標準的功能表和應用程式的功能表列上出現的功能表項目：

- **Apple 功能表**-此功能表提供存取系統寬的項目可供使用者在任何時候，無論何種應用程式正在執行。 開發人員，就無法修改這些項目。
- **應用程式功能表**-這個功能表以粗體顯示應用程式的名稱，並協助使用者識別目前正在執行之應用程式。 它包含套用至整個並不是給定文件或處理序結束應用程式，例如應用程式的項目。
- **檔案功能表**-項目可用來建立、 開啟，或儲存文件適用於您的應用程式。 如果您的應用程式不是文件為基礎，就可以重新命名或移除此功能表。
- **[編輯] 功能表**-保留命令，例如**剪下**，**複製**，和**貼上**用來編輯或修改應用程式的使用者介面中的項目。
- **格式功能表**-如果應用程式的運作方式與文字，這個功能表保留命令，以調整該文字的格式。
- **檢視功能表**-保留會影響顯示內容的方式在應用程式的使用者介面中 （檢視） 的命令。
- **應用程式特有的功能表**-這些是專屬於您的應用程式 （例如網頁瀏覽器的書籤功能表） 的任何功能表。 這些欄位要顯示之間**檢視**和**視窗**功能表列上的。
- **視窗功能表**-包含使用 windows 應用程式，以及一份目前開啟的視窗中的命令。
- **說明功能表**-如果您的應用程式提供在螢幕上的說明，[說明] 功能表應該是最右邊的功能表列上。 

如需應用程式功能表列和標準功能表和功能表項目詳細資訊，請參閱 Apple[人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)。

### <a name="the-default-application-menu-bar"></a>預設應用程式的功能表列

每當您建立新的 Xamarin.Mac 專案，您會自動取得 standard 中，預設應用程式的功能表列具有 macOS 應用程式通常具有 （如上述小節中討論） 的一般項目。 您的應用程式預設的功能表列中定義**Main.storyboard** （以及其餘的應用程式的 UI） 中的專案下的檔案**方案板**:  

![選取 主要腳本](menu-images/appmenu02.png "選取主要腳本")

按兩下**Main.storyboard**檔案，以開啟它以供編輯 Xcode 的介面產生器，而且將會看到功能表編輯器介面：

[![編輯在 Xcode 中的 UI](menu-images/defaultbar01.png "編輯在 Xcode 中的 UI")](menu-images/defaultbar01-large.png#lightbox)

從這裡我們可以按的項目例如**開啟**中的功能表項目**檔案**功能表和編輯，或調整其內容中的**屬性偵測器**:

[![編輯功能表屬性](menu-images/defaultbar02.png "編輯功能表的屬性")](menu-images/defaultbar02-large.png#lightbox)

我們將會進入加入、 編輯和刪除功能表和本文中稍後的項目。 現在我們只想要查看哪些功能表和功能表項目依預設使用，以及如何它們已經自動公開透過一組預先定義的插座和動作的程式碼 (如需詳細資訊，請參閱我們[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)文件）。

例如，如果我們按一下**連接偵測器**的**開啟**功能表項目，我們可以看到多達自動有線`openDocument:`動作： 

[![檢視附加的動作](menu-images/defaultbar03.png "檢視附加的動作")](menu-images/defaultbar03-large.png#lightbox)

如果您選取**第一個回應**中**介面階層架構**和向下捲動**連接偵測器**，而且您會看到的定義`openDocument:`動作的**開啟**功能表項目 （以及數個其他預設動作的應用程式，而且不會自動連線到控制項），會附加至：

[![檢視所有附加的動作](menu-images/defaultbar04.png "檢視所有附加的動作")](menu-images/defaultbar04-large.png#lightbox) 

為何重要？ 在下一節會看到這些自動定義的動作如何搭配其他 Cocoa 使用者介面項目，會自動啟用和停用功能表項目，以及，提供內建功能的項目。

稍後我們將使用這些內建動作來啟用和停用從程式碼項目時選取此選項提供自己的功能。

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>內建功能表功能

如果您執行新建立的 Xamarin.Mac 應用程式，新增任何 UI 項目或程式碼之前，您會發現，某些項目自動連結並為您啟用 （具有完整功能內建自動），例如**Quit**中的項目**應用程式**功能表：

![已啟用的功能表項目](menu-images/appmenu03.png "啟用的功能表項目")

雖然其他功能表項目，例如**剪下**，**複製**，和**貼上**不是：

![停用功能表項目](menu-images/appmenu04.png "停用功能表項目")

讓我們先停止應用程式，然後按兩下**Main.storyboard**檔案**方案板**開啟進行編輯在 Xcode 中的介面產生器。 下一步，拖曳**文字檢視**從**文件庫**到視窗中的檢視控制器**介面編輯器**:

[![從程式庫中選取文字檢視](menu-images/appmenu05.png "從程式庫中選取文字檢視")](menu-images/appmenu05-large.png#lightbox)

在**條件約束編輯器**我們釘選的文字檢視，視窗的邊緣，並將它其中它隨成長和壓縮視窗按一下所有四個紅色我的資料交換頂端的編輯器] 中，按一下 [設定**加入 4 條件約束**按鈕：

[![編輯條件約束](menu-images/appmenu06.png "編輯條件約束")](menu-images/appmenu06-large.png#lightbox)

使用者介面設計儲存變更並切換回 Visual Studio for Mac Xamarin.Mac 專案同步處理變更。 現在啟動應用程式文字 檢視中輸入一些文字，並加以選取，開啟**編輯**功能表：

![功能表項目會自動啟用/停用](menu-images/appmenu07.png "功能表項目會自動啟用/停用")

請注意如何**剪下**，**複製**，和**貼上**項目會自動啟用，且完整的功能，完全不需要撰寫一行程式碼。 

什麼呢？ 請記住內建預先定義 （如上述） 都預設功能表項目到有線的動作，大部分的屬於 macOS Cocoa 使用者介面項目具有內建的攔截程序採取特定動作 (例如`copy:`)。 因此當新增至視窗，作用中，而對應的功能表項目或附加到項目選取該動作會自動啟用。 如果使用者選取該功能表項目時，UI 項目內建的功能呼叫，並執行，而不需開發人員介入。

### <a name="enabling-and-disabling-menus-and-items"></a>啟用和停用功能表和項目

根據預設，每次使用者事件發生時，`NSMenu`自動啟用和停用每個顯示的功能表和功能表項目為基礎的應用程式內容。 有三種方式可啟用/停用項目：

- **自動功能表啟用**-如果功能表項目會啟用`NSMenu`可以找到項目是有線-最多的動作會回應適當的物件。 例如，文字從上方檢視過的內建攔截到`copy:`動作。
- **自訂動作和 validateMenuItem:** -繫結至任何功能表項目的[視窗或檢視的控制器自訂動作](#Working-with-Custom-Window-Actions)，您可以加入`validateMenuItem:`動作以手動方式啟用或停用功能表項目。
- **手動功能表啟用**-手動設定`Enabled`每個屬性`NSMenuItem`啟用或停用功能表中的每個項目個別。

若要選擇一種系統，設定`AutoEnablesItems`屬性`NSMenu`。 `true` 為自動 （預設行為） 與`false`是手動。 

> [!IMPORTANT]
> 如果您選擇使用 [手動] 功能表啟用，沒有任何功能表項目，即使受 AppKit 類別，例如`NSTextView`，會自動更新。 您將負責啟用和停用所有項目，以手動方式在程式碼中。

#### <a name="using-validatemenuitem"></a>使用 validateMenuItem

如上所述，繫結至任何功能表項目的[視窗或檢視控制器自訂動作](#Working-with-Custom-Window-Actions)，您可以加入`validateMenuItem:`動作以手動方式啟用或停用功能表項目。

在下列範例中，`Tag`屬性會用於決定功能表項目，將會啟用/停用的型別`validateMenuItem:`動作為基礎的狀態中的選取文字`NSTextView`。 `Tag`為每個功能表項目屬性設定介面產生器中：

![設定標記屬性](menu-images/validate01.png "設定標記屬性")

與下列程式碼加入至檢視控制器：

```csharp
[Action("validateMenuItem:")]
public bool ValidateMenuItem (NSMenuItem item) {

    // Take action based on the menu item type
    // (As specified in its Tag)
    switch (item.Tag) {
    case 1:
        // Wrap menu items should only be available if
        // a range of text is selected
        return (TextEditor.SelectedRange.Length > 0);
    case 2:
        // Quote menu items should only be available if
        // a range is NOT selected.
        return (TextEditor.SelectedRange.Length == 0);
    }

    return true;
}
```

當執行此程式碼時，並在未選取任何文字`NSTextView`，（即使它們都設計來檢視控制站上的動作），會停用的兩個換行功能表項目：

![顯示停用項目](menu-images/validate02.png "顯示停用項目")

如果選取的文字區段，且功能表重新開啟，將可提供兩個換行功能表項目：

![啟用顯示項目](menu-images/validate03.png "顯示啟用項目")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>啟用和回應程式碼中的功能表項目

如我們所見上方，只要將特定 Cocoa 使用者介面項目加入至我們的 UI 設計 （例如文字欄位中），有幾個預設的功能表項目就會啟用，而且自動運作，而不需要撰寫任何程式碼。 下一步 讓我們看看我們的 C# 程式碼加入至我們 Xamarin.Mac 專案啟用功能表項目，並提供的功能，當使用者選取它。

例如，我們想要使用者能夠使用可讓的說**開啟**中的項目**檔案**功能表並選取資料夾。 因為我們要的是整個應用程式的函式，並不限於提供視窗或 UI 項目，我們將加入程式碼來處理這個我們的應用程式委派。

在**方案板**，連按兩下`AppDelegate.CS`檔案，以開啟它進行編輯：

![選取應用程式委派](menu-images/appmenu08.png "選取應用程式委派")

加入下列程式碼下方`DidFinishLaunching`方法：

```csharp
[Export ("openDocument:")]
void OpenDialog (NSObject sender)
{
    var dlg = NSOpenPanel.OpenPanel;
    dlg.CanChooseFiles = false;
    dlg.CanChooseDirectories = true;

    if (dlg.RunModal () == 1) {
        var alert = new NSAlert () {
            AlertStyle = NSAlertStyle.Informational,
            InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
            MessageText = "Folder Selected"
        };
        alert.RunModal ();
    }
}
```

讓我們來執行應用程式現在並開啟**檔案**功能表： 

![[檔案] 功能表](menu-images/appmenu09.png "[檔案] 功能表")

請注意，**開啟**現在已啟用功能表項目。 如果我們選取該選項時，將會顯示 [開啟] 對話方塊：

![開啟的對話方塊](menu-images/appmenu10.png "開啟的對話方塊")

如果我們按一下**開啟** 按鈕，將會顯示警示訊息：

![「 範例對話 」 訊息](menu-images/appmenu11.png "範例對話 」 訊息")

索引鍵的線路`[Export ("openDocument:")]`，它會告訴`NSMenu`，我們**AppDelegate**具有方法`void OpenDialog (NSObject sender)`回應`openDocument:`動作。 您應記得，如果**開啟**功能表項目會自動有線總到此動作預設會在介面產生器：

[![檢視附加的動作](menu-images/defaultbar03.png "檢視附加的動作")](menu-images/defaultbar03-large.png#lightbox)

下一步 讓我們看看建立自己的功能表、 功能表項目和動作和回應給它們的程式碼中。

### <a name="working-with-the-open-recent-menu"></a>使用 開啟新的功能表

根據預設，**檔案**功能表包含**開啟最近**項目，可追蹤的使用者開啟應用程式的最後一個數個檔案。 如果您要建立`NSDocument`Xamarin.Mac 應用程式，這個功能表將會為您自動處理。 對於其他類型的 Xamarin.Mac 應用程式中，您會負責管理，並且以手動方式回應此功能表項目。

若要手動處理**開啟最近**功能表上，您先必須以新的檔案已開啟或儲存使用下列通知它：

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

即使不使用您的應用程式`NSDocuments`，您仍然可以使用`NSDocumentController`維護**開啟最近**傳送功能表`NSUrl`檔案的位置與`NoteNewRecentDocumentURL`方法`SharedDocumentController`。

接下來，您必須覆寫`OpenFile`方法要開啟使用者會從選取的任何檔案的應用程式委派**最近開啟**功能表。 例如: 

```csharp
public override bool OpenFile (NSApplication sender, string filename)
{
    // Trap all errors
    try {
        filename = filename.Replace (" ", "%20");
        var url = new NSUrl ("file://"+filename);
        return OpenFile(url);
    } catch {
        return false;
    }
}
```

傳回`true`如果可以開啟檔案，否則傳回`false`和內建的警告會顯示給使用者無法開啟檔案。

因為從傳回的檔名和路徑**開啟最近**功能表上，可能會加上空格，我們需要建立之前會適當逸出此字元`NSUrl`或我們將會收到錯誤。 這是以下列程式碼：

```csharp
filename = filename.Replace (" ", "%20");
```

最後，我們建立`NSUrl`，它會指向的檔案和應用程式中的 helper 方法會委派給開啟新視窗，並將檔案載入的使用：

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

一起提取所有項目，讓我們看看中的範例實作**d**檔案：

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace MacHyperlink
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }

        public override bool OpenFile (NSApplication sender, string filename)
        {
            // Trap all errors
            try {
                filename = filename.Replace (" ", "%20");
                var url = new NSUrl ("file://"+filename);
                return OpenFile(url);
            } catch {
                return false;
            }
        }
        #endregion

        #region Private Methods
        private bool OpenFile(NSUrl url) {
            var good = false;

            // Trap all errors
            try {
                var path = url.Path;

                // Is the file already open?
                for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
                    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
                    if (content != null && path == content.FilePath) {
                        // Bring window to front
                        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
                        return true;
                    }
                }

                // Get new window
                var storyboard = NSStoryboard.FromName ("Main", null);
                var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

                // Display
                controller.ShowWindow(this);

                // Load the text into the window
                var viewController = controller.Window.ContentViewController as ViewController;
                viewController.Text = File.ReadAllText(path);
                viewController.SetLanguageFromPath(path);
                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                viewController.View.Window.RepresentedUrl = url;

                // Add document to the Open Recent menu
                NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);

                // Make as successful
                good = true;
            } catch {
                // Mark as bad file on error
                good = false;
            }

            // Return results
            return good;
        }
        #endregion

        #region actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = true;
            dlg.CanChooseDirectories = false;

            if (dlg.RunModal () == 1) {
                // Nab the first file
                var url = dlg.Urls [0];

                if (url != null) {
                    // Open the document in a new window
                    OpenFile (url);
                }
            }
        }
        #endregion
    }
}
```

根據您的應用程式的需求，您可能不想要在多個視窗中開啟相同的檔案，同時使用者。 在我們範例應用程式，如果使用者選擇已經開啟的檔案中 (從**最近開啟**或**開啟...** 功能表項目），包含檔案的視窗帶到最上層。

若要達成此目的，我們可以使用下列程式碼在我們的 helper 方法：

```csharp
var path = url.Path;

// Is the file already open?
for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
    var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
    if (content != null && path == content.FilePath) {
        // Bring window to front
        NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
        return true;
    }
}
```

我們設計我們`ViewController`類別，以保存路徑中的檔案其`Path`屬性。 接下來，我們執行迴圈的應用程式中所有目前開啟的視窗。 如果檔案已經開啟在其中一個視窗，該資料庫處於使用的所有其他視窗的前面：

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

如果沒有找到符合的載入檔案開啟新視窗中所述的檔案是**開啟最近**功能表：

```csharp
// Get new window
var storyboard = NSStoryboard.FromName ("Main", null);
var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

// Display
controller.ShowWindow(this);

// Load the text into the window
var viewController = controller.Window.ContentViewController as ViewController;
viewController.Text = File.ReadAllText(path);
viewController.SetLanguageFromPath(path);
viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
viewController.View.Window.RepresentedUrl = url;

// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

<a name="Working-with-Custom-Window-Actions" />

### <a name="working-with-custom-window-actions"></a>使用自訂視窗動作

就像 內建**第一個回應**標準功能表項目來到預先有線的動作，您可以建立全新自訂動作和其網路介面產生器中的功能表項目。

首先，在其中一個應用程式視窗的控制站上定義的自訂動作。 例如: 

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

接下來，按兩下 應用程式中的分鏡腳本檔案**方案板**開啟進行編輯在 Xcode 中的介面產生器。 選取**第一個回應**下**應用程式場景**，然後切換到**屬性偵測器**:

![屬性偵測器](menu-images/action01.png "屬性偵測器")

按一下**+**底部的按鈕**屬性偵測器**若要加入新的自訂動作：

![加入新的動作](menu-images/action02.png "加入新的動作")

讓您在您的視窗控制站建立的自訂動作的名稱相同：

![編輯動作名稱](menu-images/action03.png "編輯動作名稱")

控制項按一下和拖曳功能表項目**第一個回應**下**應用程式場景**。 從快顯清單中，選取您剛才建立的新動作 (`defineKeyword:`在此範例中):

![附加動作](menu-images/action04.png "附加動作")

儲存變更到分鏡腳本，並返回 Visual Studio for Mac 同步處理變更。 如果您執行應用程式，連接到自訂動作的功能表項目會自動被啟用/停用 （根據視窗中開啟的動作） 並選取功能表項目關閉的動作會引發：

[![測試新的動作](menu-images/action05.png "測試新的動作")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>加入、 編輯和刪除功能表

我們已經看到與上一節，Xamarin.Mac 應用程式隨附的預設功能表和功能表項目特定 UI 控制項將會自動啟動並回應以預設的號碼。 我們也已經看到如何將程式碼加入至我們的應用程式，也會啟用並回應這些預設項目。

這一節我們將探討移除我們不需要的功能表項目、 重新組織功能表，以及加入新的功能表、 功能表項目和動作。

按兩下**Main.storyboard**檔案**方案板**開啟進行編輯：

[![編輯在 Xcode 中的 UI](menu-images/maint01.png "編輯在 Xcode 中的 UI")](menu-images/maint01-large.png#lightbox)

特定 Xamarin.Mac 應用程式不我們將會使用預設**檢視**功能表，讓我們將它移除。 在**介面階層架構**選取**檢視**屬於主功能表列的功能表項目：

![選取 [檢視] 功能表項目](menu-images/maint02.png "選取 [檢視] 功能表項目")

按下 delete 鍵或退格鍵刪除功能表。 接下來，我們不打算使用的所有項目中**格式**功能表和我們想要移動我們將使用 out 從子功能表下的項目。 在**介面階層架構**選取下列的功能表項目：

![反白顯示多個項目](menu-images/maint03.png "反白顯示多個項目")

父系下的項目拖曳**功能表**從其目前所在的子功能表：

[![將功能表項目拖曳至父功能表](menu-images/maint04.png "拖曳至父功能表的功能表項目")](menu-images/maint04-large.png#lightbox)

您的功能表現在看起來應該像：

[![在新位置的項目](menu-images/maint05.png "新位置中的項目")](menu-images/maint05-large.png#lightbox)

下一步 讓我們來拖曳**文字**out 子功能表下的**格式**功能表並將它放在之間的主功能表列上**格式**和**視窗**功能表：

[![文字功能表](menu-images/maint06.png "文字功能表")](menu-images/maint06-large.png#lightbox)

讓我們跳回下**格式**功能表和 delete**字型**子功能表項目。 接下來，選取**格式**功能表和它重新命名為 「 字型 」:

[![字型功能表](menu-images/maint07.png "字型功能表")](menu-images/maint07-large.png#lightbox)

接下來，讓我們來建立自訂功能表的預先定義的片語，將會自動取得附加至文字檢視中的文字時選取此選項。 在底部的 [搜尋] 方塊中**程式庫偵測器**類型中 「 功能表 」。 這會讓您更輕鬆地尋找及使用的所有功能表 UI 項目：

![程式庫偵測器](menu-images/maint08.png "程式庫偵測器")

現在讓我們執行下列工作來建立我們功能表：

1. 拖曳**功能表項目**從**程式庫偵測器**之間的功能表列上**文字**和**視窗**功能表： 

    ![文件庫中選取新的功能表項目](menu-images/maint10.png "文件庫中選取新的功能表項目")
2. 重新命名項目"片語": 

    [![設定的功能表名稱](menu-images/maint09.png "設定功能表名稱")](menu-images/maint09-large.png#lightbox)
3. 下一步拖曳**功能表**從**程式庫偵測器**: 

    ![從程式庫選取功能表](menu-images/maint11.png "選取功能表上，從程式庫")
4. 卸除然後**功能表**針對新**功能表項目**我們剛剛建立，並將其名稱變更為"片語": 

    [![編輯功能表名稱](menu-images/maint12.png "編輯功能表名稱")](menu-images/maint12-large.png#lightbox)
5. 現在讓我們重新命名的三個預設**功能表項目**「 位址 」、 「 日期 」，和"Greeting": 

    [![片語功能表](menu-images/maint13.png "片語功能表")](menu-images/maint13-large.png#lightbox)
6. 讓我們加入第四個**功能表項目**拖曳**功能表項目**從**程式庫偵測器**，然後呼叫該 「 簽章 」: 

    [![編輯功能表項目名稱](menu-images/maint14.png "編輯功能表項目名稱")](menu-images/maint14-large.png#lightbox)
7. 在功能表列中儲存的變更。

現在讓我們來建立自訂動作集，以便我們新的功能表項目都會公開至 C# 程式碼。 在 Xcode 中讓我們來切換至**助理**檢視：

[![建立必要的動作](menu-images/maint15.png "建立必要的動作")](menu-images/maint15-large.png#lightbox)

讓我們執行下列作業：

1. 從控制項拖曳**位址**功能表項目**h**檔案。
2. 交換器**連接**類型**動作**: 

    [![選取的動作類型](menu-images/maint17.png "選取動作類型")](menu-images/maint17-large.png#lightbox)
3. 輸入**名稱**"phraseAddress 」，然後按**連接**按鈕以建立新的動作： 

    [![設定動作](menu-images/maint18.png "設定動作")](menu-images/maint18-large.png#lightbox)
4. 重複上述步驟**日期**，**問候語**，和**簽章**功能表項目： 

    [![完成的動作](menu-images/maint19.png "完成的動作")](menu-images/maint19-large.png#lightbox)
5. 在功能表列中儲存的變更。

接下來，我們需要建立我們文字檢視輸出，以便調整其內容從程式碼。 選取**ViewController.h**檔案**助理編輯器**並建立新的輸出呼叫`documentText`:

[![建立輸出](menu-images/maint20.png "建立輸出")](menu-images/maint20-large.png#lightbox)

返回 Visual Studio for Mac 同步處理從 Xcode 變更。 接下來編輯**ViewController.cs**檔案，並讓它看起來如下所示：

```csharp
using System;

using AppKit;
using Foundation;

namespace MacMenus
{
    public partial class ViewController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public override NSObject RepresentedObject {
            get {
                return base.RepresentedObject;
            }
            set {
                base.RepresentedObject = value;
                // Update the view, if already loaded.
            }
        }

        public string Text {
            get { return documentText.Value; }
            set { documentText.Value = value; }
        } 
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any additional setup after loading the view.
        }

        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            App.textEditor = this;
        }

        public override void ViewWillDisappear ()
        {
            base.ViewDidDisappear ();

            App.textEditor = null;
        }
        #endregion
    }
}
```

這樣會公開的外部我們文字檢視文字`ViewController`類別，並通知應用程式委派，當視窗獲得或失去焦點。 現在編輯**d**檔案，並讓它看起來如下所示：

```csharp
using AppKit;
using Foundation;
using System;

namespace MacMenus
{
    [Register ("AppDelegate")]
    public partial class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public ViewController textEditor { get; set;} = null;
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
        #endregion

        #region Custom actions
        [Export ("openDocument:")]
        void OpenDialog (NSObject sender)
        {
            var dlg = NSOpenPanel.OpenPanel;
            dlg.CanChooseFiles = false;
            dlg.CanChooseDirectories = true;

            if (dlg.RunModal () == 1) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Informational,
                    InformativeText = "At this point we should do something with the folder that the user just selected in the Open File Dialog box...",
                    MessageText = "Folder Selected"
                };
                alert.RunModal ();
            }
        }

        partial void phrasesAddress (Foundation.NSObject sender) {

            textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
        }

        partial void phrasesDate (Foundation.NSObject sender) {

            textEditor.Text += DateTime.Now.ToString("D");
        }

        partial void phrasesGreeting (Foundation.NSObject sender) {

            textEditor.Text += "Dear Sirs,\n\n";
        }

        partial void phrasesSignature (Foundation.NSObject sender) {

            textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
        }
        #endregion
    }
}
```

這裡我們讓`AppDelegate`部分類別，因此我們可以使用的動作和我們介面產生器中所定義的插座。 我們也會公開`textEditor`追蹤哪一個視窗目前處於焦點。

下列方法可用來處理我們自訂功能表和功能表項目：

```csharp
partial void phrasesAddress (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Xamarin HQ\n394 Pacific Ave, 4th Floor\nSan Francisco CA 94111\n\n";
}

partial void phrasesDate (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += DateTime.Now.ToString("D");
}

partial void phrasesGreeting (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Dear Sirs,\n\n";
}

partial void phrasesSignature (Foundation.NSObject sender) {

    if (textEditor == null) return;
    textEditor.Text += "Sincerely,\n\nKevin Mullins\nXamarin,Inc.\n";
}
```

現在如果我們執行應用程式中，所有的項目中**片語** 將會啟用，並將提供片語時選取 文字 檢視：

![執行應用程式的範例](menu-images/maint21.png "執行的應用程式的範例")

現在，我們已經使用應用程式的功能表列，向下的基本概念，讓我們看看建立自訂的內容功能表。

### <a name="creating-menus-from-code"></a>從程式碼中建立功能表

除了使用 Xcode 的介面產生器中建立功能表和功能表項目，可能會有有時候 Xamarin.Mac 應用程式需要建立、 修改或移除程式碼中的功能表、 子功能表或功能表項目。

在下列範例中，建立類別以容納功能表項目和子功能表，將會動態建立的即時資訊：

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using AppKit;

namespace AppKit.TextKit.Formatter
{
    public class LanguageFormatCommand : NSObject
    {
        #region Computed Properties
        public string Title { get; set; } = "";
        public string Prefix { get; set; } = "";
        public string Postfix { get; set; } = "";
        public List<LanguageFormatCommand> SubCommands { get; set; } = new List<LanguageFormatCommand>();
        #endregion

        #region Constructors
        public LanguageFormatCommand () {

        }

        public LanguageFormatCommand (string title)
        {
            // Initialize
            this.Title = title;
        }

        public LanguageFormatCommand (string title, string prefix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
        }

        public LanguageFormatCommand (string title, string prefix, string postfix)
        {
            // Initialize
            this.Title = title;
            this.Prefix = prefix;
            this.Postfix = postfix;
        }
        #endregion
    }
}
```

#### <a name="adding-menus-and-items"></a>加入功能表和項目

下列常式會將與這個類別定義，剖析的集合`LanguageFormatCommand`物件和以遞迴方式建置新的功能表和功能表項目將它們附加至已傳入現有的功能表 （介面產生器中建立） 的下方：

```csharp
private void AssembleMenu(NSMenu menu, List<LanguageFormatCommand> commands) {
    NSMenuItem menuItem;

    // Add any formatting commands to the Formatting menu
    foreach (LanguageFormatCommand command in commands) {
        // Add separator or item?
        if (command.Title == "") {
            menuItem = NSMenuItem.SeparatorItem;
        } else {
            menuItem = new NSMenuItem (command.Title);

            // Submenu?
            if (command.SubCommands.Count > 0) {
                // Yes, populate submenu
                menuItem.Submenu = new NSMenu (command.Title);
                AssembleMenu (menuItem.Submenu, command.SubCommands);
            } else {
                // No, add normal menu item
                menuItem.Activated += (sender, e) => {
                    // Apply the command on the selected text
                    TextEditor.PerformFormattingCommand (command);
                };
            }
        }
        menu.AddItem (menuItem);
    }
}
``` 

針對任何`LanguageFormatCommand`具有空白物件`Title`屬性，這個常式會建立**分隔功能表項目**（精簡灰線） 功能表區段之間：

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

如果已提供標題，會建立新的功能表項目與該標題：

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

如果`LanguageFormatCommand`物件包含子`LanguageFormatCommand`物件，會建立一個子功能表和`AssembleMenu`方法會以遞迴方式建置出該功能表呼叫：

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

對於任何新功能表項目沒有子功能表，來處理使用者所選取的功能表項目會加入程式碼：

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>測試功能表建立

所有上述程式碼的位置，如果以下一系列`LanguageFormatCommand`物件所建立：

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Stong","**","**"));
FormattingCommands.Add(new LanguageFormatCommand("Emphasize","_","_"));
FormattingCommands.Add(new LanguageFormatCommand("Inline Code","`","`"));
FormattingCommands.Add(new LanguageFormatCommand("Code Block","```\n","\n```"));
FormattingCommands.Add(new LanguageFormatCommand("Comment","<!--","-->"));
FormattingCommands.Add (new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Unordered List","* "));
FormattingCommands.Add(new LanguageFormatCommand("Ordered List","1. "));
FormattingCommands.Add(new LanguageFormatCommand("Block Quote","> "));
FormattingCommands.Add (new LanguageFormatCommand ());

var Headings = new LanguageFormatCommand ("Headings");
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 1","# "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 2","## "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 3","### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 4","#### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 5","##### "));
Headings.SubCommands.Add(new LanguageFormatCommand("Heading 6","###### "));
FormattingCommands.Add (Headings);

FormattingCommands.Add(new LanguageFormatCommand ());
FormattingCommands.Add(new LanguageFormatCommand("Link","[","]()"));
FormattingCommands.Add(new LanguageFormatCommand("Image","![]("-")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[ ![]("-")](linkimagehere/index.md)"));
```

集合傳遞至`AssembleMenu`函式 (具有**格式**功能表設定做為基底)，就會建立下列的動態功能表和功能表項目：

![在執行中應用程式中的新功能表項目](menu-images/dynamic01.png "中執行的應用程式的新功能表項目")

#### <a name="removing-menus-and-items"></a>移除功能表和項目

如果您需要從應用程式的使用者介面中移除任何功能表或功能表項目，您可以使用`RemoveItemAt`方法`NSMenu`類別只需為它指定零起始的索引要移除的項目。

例如，若要移除功能表和功能表項目由上述的常式，您可以使用下列程式碼：

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

在上述程式碼，第四個功能表項目會建立在 Xcode 的介面產生器及 aways 可在應用程式，因此不會以動態方式移除。

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>內容功能表

內容功能表會出現在使用者上按一下滑鼠右鍵或按一下控制項視窗中的項目時。 根據預設，數個已內建 macOS 的 UI 項目具有關聯式功能表附加至它們 （例如 [文字] 檢視中）。 不過，有些時候可能會當我們想要建立自己自訂的內容功能表，我們加入一個視窗的 UI 項目。

開始編輯我們**Main.storyboard**檔案在 Xcode 中，然後加入**視窗**視窗我們設計中，將其**類別**"NSPanel 」 中以**識別 Inspector**，加入新**助理**項目**視窗**功能表上，並將它附加至新的視窗使用**顯示話題**:

[![設定 segue 類型](menu-images/context01.png "segue 類型設定")](menu-images/context01-large.png#lightbox)

讓我們執行下列作業：

1. 拖曳**標籤**從**程式庫偵測器**到**面板**視窗，並設定其 「 屬性 」 文字： 

    [![編輯標籤的值](menu-images/context03.png "編輯標籤的值")](menu-images/context03-large.png#lightbox)
2. 下一步拖曳**功能表**從**程式庫偵測器**到檢視中的控制站的檢視階層架構和重新命名之三種預設的功能表項目**文件**，**文字**和**字型**:

    [![必要的功能表項目](menu-images/context02.png "需要的功能表項目")](menu-images/context02-large.png#lightbox)
3. 現在拖曳控制項從**屬性標籤**到**功能表**:

    [![若要建立 segue 拖曳](menu-images/context04.png "拖曳來建立 segue")](menu-images/context04-large.png#lightbox)
4. 從快顯對話方塊中，選取**功能表**: 

    ![設定 segue 類型](menu-images/context05.png "segue 類型設定")
5. 從**識別 Inspector**，設定 「 PanelViewController 」 來檢視控制站的類別： 

    [![設定 segue 類別](menu-images/context10.png "設定 segue 類別")](menu-images/context10-large.png#lightbox)
6. 切換回 Visual Studio for Mac 同步處理，然後再回來介面產生器。
7. 切換至**助理編輯器**選取**PanelViewController.h**檔案。
8. 建立的動作**文件**功能表項目稱為`propertyDocument`: 

    [![設定動作](menu-images/context06.png "設定動作")](menu-images/context06-large.png#lightbox)
9. 其餘的功能表項目的重複建立的動作： 

    [![必要的動作](menu-images/context07.png "必要的動作")](menu-images/context07-large.png#lightbox)
10. 最後建立的輸出**屬性標籤**呼叫`propertyLabel`: 

    [![設定輸出](menu-images/context08.png "設定輸出")](menu-images/context08-large.png#lightbox)
11. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

編輯**PanelViewController.cs**檔案，然後加入下列程式碼：

```csharp
partial void propertyDocument (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Document";
}

partial void propertyFont (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Font";
}

partial void propertyText (Foundation.NSObject sender) {
    propertyLabel.StringValue = "Text";
}
```

現在如果我們執行應用程式，並以滑鼠右鍵按一下屬性標籤面板中，我們會看到我們的自訂內容功能表。 如果我們選取，然後從功能表項目，將會變更標籤的值：

![執行關聯式功能表](menu-images/context09.png "執行的內容功能表")

下一步 讓我們看看建立狀態列功能表。

## <a name="status-bar-menus"></a>狀態列的功能表

狀態列功能表會顯示給使用者，例如功能表或反映的應用程式狀態的影像提供與互動的狀態功能表項目或意見反應的集合。 應用程式的狀態 列功能表是啟用和使用中，即使應用程式在背景中執行。 [全系統狀態] 列位於應用程式的功能表列的右側，且只有狀態列 macOS 中目前可用。

開始編輯我們**d**檔案並製作`DidFinishLaunching`方法看起來像下列：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Create a status bar menu
    NSStatusBar statusBar = NSStatusBar.SystemStatusBar;

    var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);
    item.Title = "Text";
    item.HighlightMode = true;
    item.Menu = new NSMenu ("Text");

    var address = new NSMenuItem ("Address");
    address.Activated += (sender, e) => {
        PhraseAddress(address);
    };
    item.Menu.AddItem (address);

    var date = new NSMenuItem ("Date");
    date.Activated += (sender, e) => {
        PhraseDate(date);
    };
    item.Menu.AddItem (date);

    var greeting = new NSMenuItem ("Greeting");
    greeting.Activated += (sender, e) => {
        PhraseGreeting(greeting);
    };
    item.Menu.AddItem (greeting);

    var signature = new NSMenuItem ("Signature");
    signature.Activated += (sender, e) => {
        PhraseSignature(signature);
    };
    item.Menu.AddItem (signature);
}
```

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` 讓我們可以存取整個系統的狀態列。 `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` 建立新的狀態 列項目。 從這裡我們建立功能表和功能表項目數目，並將功能表附加至剛建立的工具列項目狀態。 

當我們執行應用程式，將會顯示新的狀態 列項目。 從功能表中選取項目將會變更 [文字] 檢視中的文字： 

![執行 [狀態] 列功能表](menu-images/statusbar01.png "執行 [狀態] 列 功能表")

接下來，讓我們看看建立自訂的停駐的功能表項目。

## <a name="custom-dock-menus"></a>自訂的停駐功能表

在使用者上按一下滑鼠右鍵或按一下控制項中停駐的應用程式的圖示時，為您的 Mac 應用程式會出現停駐功能表：

![自訂停駐功能表](menu-images/dock01.png "自訂停駐功能表")

讓我們來建立自訂的停駐功能表應用程式執行下列動作：

1. 在 Visual Studio for Mac，以滑鼠右鍵按一下應用程式的專案，然後選取**新增** > **新的檔案...**從新的檔案對話方塊中，選取**Xamarin.Mac** > **空白介面定義**，用於 「 DockMenu"**名稱**按一下**新增**來建立新的按鈕**DockMenu.xib**檔案：

    ![加入空的介面定義](menu-images/dock02.png "加入空的介面定義")
2. 在**方案板**，連按兩下**DockMenu.xib**檔案將它開啟在 Xcode 中進行編輯。 建立新**功能表**與下列項目：**位址**，**日期**，**問候語**，和**簽章** 

    [![UI 的版面配置](menu-images/dock03.png "UI 的版面配置")](menu-images/dock03-large.png#lightbox)
3. 接下來，讓我們連接到我們現有的動作，我們建立我們自訂功能表中的 新的功能表項目[新增、 編輯和刪除功能表](#Adding,_Editing_and_Deleting_Menus)上一節。 切換至**連接偵測器**選取**第一個回應**中**介面階層架構**。 向下捲動並尋找`phraseAddress:`動作。 拖曳一條線的圓形，該動作將**位址**功能表項目：

    [![拖曳動作連接至](menu-images/dock04.png "拖曳至動作連接")](menu-images/dock04-large.png#lightbox)
4. 針對所有其他功能表項目附加到其對應的動作重複： 

    [![必要的動作](menu-images/dock05.png "必要的動作")](menu-images/dock05-large.png#lightbox)
5. 接下來，選取**應用程式**中**介面階層架構**。 在**連接偵測器**，自圓圈拖曳一條線`dockMenu`功能表剛才所建立的輸出：

    [![拖曳插座接上](menu-images/dock06.png "拖曳插座接上")](menu-images/dock06-large.png#lightbox)
6. 儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。
7. 按兩下**Info.plist**檔案，以開啟它進行編輯： 

    [![編輯 Info.plist 檔案](menu-images/dock07.png "編輯 Info.plist 檔案")](menu-images/dock07-large.png#lightbox)
8. 按一下**來源**螢幕底部的索引標籤： 

    [![選取資料來源檢視](menu-images/dock08.png "選取資料來源檢視")](menu-images/dock08-large.png#lightbox)
9. 按一下**加入新項目**、 按一下綠色加號按鈕、 設定屬性名稱，"AppleDockMenu"和"DockMenu"（不含副檔名我們新.xib 檔案名稱） 的值： 

    [![DockMenu 項目加入](menu-images/dock09.png "新增 DockMenu 項目")](menu-images/dock09-large.png#lightbox)

現在如果我們執行我們的應用程式，並以滑鼠右鍵按一下 停駐在其圖示，將會顯示新的功能表項目：

![停駐功能表執行的範例](menu-images/dock10.png "的停駐功能表執行範例")

如果我們從功能表選取其中一個自訂的項目，則我們文字檢視中的文字會被修改。

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>快顯的按鈕和下拉式清單

快顯的按鈕會顯示選取的項目，並會顯示使用者所按下時，從選取的選項清單。 下拉式清單是按鈕的一種快顯通常用來選取特定的目前工作的內容命令。 同時可以在視窗中任何位置出現。

透過下列方式，讓我們來建立應用程式的自訂快顯的按鈕：

1. 編輯**Main.storyboard**中安裝 Xcode 和拖曳檔案**快顯功能表按鈕**從**程式庫偵測器**到**面板**中建立的視窗[關聯式功能表](#Contextual_Menus)> 一節： 

    [![加入快顯功能表按鈕](menu-images/popup01.png "加入快顯功能表按鈕")](menu-images/popup01-large.png#lightbox)
2. 加入新的功能表項目，並以快顯視窗中設定之項目的標題：**位址**，**日期**，**問候語**，和**簽章** 

    [![設定功能表項目](menu-images/popup02.png "設定功能表項目")](menu-images/popup02-large.png#lightbox)
3. 接下來，讓我們連接到現有的動作，我們為我們的自訂功能表中建立的 新的功能表項目[新增、 編輯和刪除功能表](#Adding,_Editing_and_Deleting_Menus)上一節。 切換至**連接偵測器**選取**第一個回應**中**介面階層架構**。 向下捲動並尋找`phraseAddress:`動作。 拖曳一條線的圓形，該動作將**位址**功能表項目： 

    [![拖曳動作連接至](menu-images/popup03.png "拖曳至動作連接")](menu-images/popup03-large.png#lightbox)
4. 針對所有其他功能表項目附加到其對應的動作重複： 

    [![所有必要的動作](menu-images/popup04.png "所有必要的動作")](menu-images/popup04-large.png#lightbox)
5. 儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。

現在如果我們執行我們的應用程式，並從快顯功能表選取項目，我們文字檢視中的文字會變更：

![執行快顯視窗的範例](menu-images/popup05.png "執行快顯視窗的範例")

您可以建立及使用下拉式清單中，以快顯的按鈕完全相同的方式。 而不是附加至現有的動作，您可以建立您自己自訂的執行方式就如同我們的內容功能表中的所[關聯式功能表](#Contextual_Menus)> 一節。

## <a name="summary"></a>總結

這篇文章已取得使用功能表和功能表項目在 Xamarin.Mac 應用程式的詳細的檢視。 首先我們檢查應用程式的功能表列中，然後我們探討了建立關聯式功能表，接下來我們檢查狀態 列功能表並自訂停駐的功能表。 最後，我們涵蓋快顯功能表和下拉式清單。


## <a name="related-links"></a>相關連結

- [MacMenus （範例）](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [人性化介面指導方針的功能表](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [應用程式功能表和快顯清單簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
