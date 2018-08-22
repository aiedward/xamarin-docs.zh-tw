---
title: Xamarin.mac 的功能表
description: 本文涵蓋在 Xamarin.Mac 應用程式中的功能表。 它說明建立和維護功能表和功能表項目在 Xcode 和 Interface Builder，並以程式設計方式處理它們。
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: d84910cd5c2bc72a563fb04457532d544aedf571
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2018
ms.locfileid: "40250943"
---
# <a name="menus-in-xamarinmac"></a>Xamarin.mac 的功能表

_本文涵蓋在 Xamarin.Mac 應用程式中的功能表。它說明建立和維護功能表和功能表項目在 Xcode 和 Interface Builder，並以程式設計方式處理它們。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您會有開發人員使用 OBJECTIVE-C 和 Xcode 的相同 Cocoa 功能表存取。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的 Interface Builder 來建立，並維護您的功能表列、 功能表和功能表項目 （或選擇直接在 C# 程式碼中建立它們）。

功能表是 Mac 應用程式的使用者體驗中不可或缺的一部分，而且通常會出現在使用者介面的各個部分：

- **應用程式的功能表列**-這是主要出現在每個 Mac 應用程式的畫面頂端的功能表。
- **關聯式功能表**-這些會出現在使用者上按一下滑鼠右鍵或控制項-按一下視窗中的項目時。
- **[狀態] 列**-這是在最右邊的應用程式功能表列頂端的 [（到左邊的功能表列時鐘）] 畫面隨即出現，並隨著左邊項目會新增到該區域。
- **停駐功能表**-在 dock 中的每個應用程式的功能表，顯示當使用者以滑鼠右鍵按一下控制項-按一下應用程式的圖示，或當使用者 left-clicks 圖示，並按住滑鼠按鈕。
- **快顯的按鈕和下拉式清單**-快顯的按鈕會顯示選取的項目，並顯示當使用者按下時，從選取的選項清單。 下拉式清單是按鈕的一種快顯通常用於選取特定內容的目前工作的命令。 這兩者可以在視窗中任何位置出現。

[![範例功能表](menu-images/intro01.png "範例功能表")](menu-images/intro01-large.png#lightbox)

在本文中，我們將討論使用 Cocoa 功能表列、 功能表和功能表項目在 Xamarin.Mac 應用程式的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

## <a name="the-applications-menu-bar"></a>應用程式的功能表列 

不同於 Windows 作業系統，其中每個視窗可以有它自己附加的功能表列上執行的應用程式，在 macOS 上執行每個應用程式會有用於該應用程式中的每個視窗的螢幕上方執行的單一功能表列：

[![功能表列](menu-images/appmenu01.png "功能表列")](menu-images/appmenu01-large.png#lightbox)

此功能表列上的項目會啟用或停用在任何給定的時刻根據目前內容或應用程式和其使用者介面的狀態。 例如： 如果使用者選取的文字欄位，項目上**編輯** 功能表會出現 啟用，這類**複製**並**剪下**。

根據 Apple，並依預設，所有的 macOS 應用程式會有一組標準的功能表和應用程式的功能表列上出現的功能表項目：

- **Apple 功能表**-此功能表提供對系統的存取可供使用者隨時都，無論何種應用程式正在執行的各種項目。 開發人員無法修改這些項目。
- **應用程式功能表**-此功能表會顯示以粗體顯示的應用程式的名稱，並協助使用者識別目前正在執行哪些應用程式。 它包含套用至整個並不是給定文件或處理程序結束應用程式，例如應用程式的項目。
- **[檔案] 功能表**-項目用來建立、 開啟、 或儲存文件適用於您的應用程式。 如果您的應用程式不是文件為基礎，就可以重新命名或移除此功能表。
- **[編輯] 功能表**-這類保留命令**剪下**，**複製**，和**貼上**用來編輯或修改應用程式的使用者介面中的項目。
- **格式功能表**-如果應用程式的運作方式與這個功能表中的文字，保留命令，以調整該文字的格式。
- **檢視功能表**-保留會影響顯示內容的方式在應用程式的使用者介面中 （檢視） 的命令。
- **應用程式特有的功能表**-這些是任何專屬於您的應用程式 （例如網頁瀏覽器的書籤功能表） 的功能表。 它們應該會出現之間**檢視**並**視窗**功能表列上的。
- **視窗功能表**-包含用來處理您的應用程式，以及一份目前開啟的視窗的視窗命令。
- **[說明] 功能表**-如果您的應用程式會提供在螢幕上的說明，[說明] 功能表應該是最右邊的功能表列上。 

如需有關應用程式功能表列和標準的功能表和功能表項目的詳細資訊，請參閱 Apple[人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)。

### <a name="the-default-application-menu-bar"></a>預設應用程式的功能表列

每當您建立新的 Xamarin.Mac 專案時，自動會取得標準，預設應用程式的功能表列具有 macOS 應用程式通常只需要 （如上述小節中所述） 的一般項目。 您的應用程式預設的功能表列中定義**Main.storyboard** （以及您的應用程式 UI 的其餘部分） 中的專案下的檔案**Solution Pad**:  

![選取主要分鏡腳本](menu-images/appmenu02.png "選取主要分鏡腳本")

按兩下**Main.storyboard**檔案以開啟它，如 Xcode 的 Interface Builder 中編輯將會看到功能表編輯器介面：

[![編輯在 Xcode 中的 UI](menu-images/defaultbar01.png "編輯在 Xcode 中的 UI")](menu-images/defaultbar01-large.png#lightbox)

從這裡我們可以按一下項目這類**開放**中的功能表項目**檔案**功能表和編輯，或調整其屬性**屬性偵測器**:

[![編輯功能表的屬性](menu-images/defaultbar02.png "編輯功能表屬性")](menu-images/defaultbar02-large.png#lightbox)

我們會進入新增、 編輯和刪除功能表和稍後在本文中的項目。 現在我們只想要查看哪些功能表和功能表項目是預設可用的以及如何它們已經自動公開到透過一組預先定義的輸出和動作的程式碼 (如需詳細資訊，請參閱我們[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)文件）。

例如，如果我們按一下**連線偵測器**如**開放**我們可以看到它們會自動設定最多的功能表項目`openDocument:`動作： 

[![檢視附加的動作](menu-images/defaultbar03.png "檢視附加的動作")](menu-images/defaultbar03-large.png#lightbox)

如果您選取**第一個回應**中**介面階層架構**中向下捲動並**連線偵測器**，，您會看到的定義`openDocument:`動作所**開啟**功能表項目 （以及數個其他預設動作的應用程式，而且不會自動設定至的控制項），會附加至：

[![檢視所有附加的動作](menu-images/defaultbar04.png "檢視所有附加的動作")](menu-images/defaultbar04-large.png#lightbox) 

這為何重要？ 在下一個區段會看到這些自動定義的動作與其他 Cocoa 使用者介面項目，會自動啟用和停用功能表項目，以及、 提供項目的內建功能的運作方式。

稍後我們將啟用和停用的程式碼中的項目，並提供我們自己的功能，在選取時使用這些內建動作。

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>內建功能表功能

如果您是執行新建立的 Xamarin.Mac 應用程式，然後再加入任何 UI 項目或程式碼時，您會發現某些項目會自動連接，為您啟用 （使用完全自動內建功能），例如**Quit**中的項目**應用程式**功能表：

![已啟用的功能表項目](menu-images/appmenu03.png "啟用的功能表項目")

雖然其他功能表項目，例如**剪下**，**複製**，並**貼上**不是：

![停用功能表項目](menu-images/appmenu04.png "停用功能表項目")

讓我們先停止應用程式，然後按兩下**Main.storyboard**中的檔案**Solution Pad**開啟它進行編輯在 Xcode 的 Interface Builder。 下一步，拖曳**文字檢視**從**程式庫**拖曳至視窗中的檢視控制器**介面編輯器**:

[![從程式庫中選取 文字檢視](menu-images/appmenu05.png "從程式庫中選取 文字檢視")](menu-images/appmenu05-large.png#lightbox)

在 **條件約束編輯器**讓我們釘選到視窗邊緣的 文字 檢視，並將它其中成長和壓縮與視窗按一下 所有四個紅色 I 字形狀，編輯器頂端，按一下 設定**新增 4 個條件約束**按鈕：

[![編輯條件約束](menu-images/appmenu06.png "編輯條件約束")](menu-images/appmenu06-large.png#lightbox)

將變更儲存至使用者介面設計，並切換回 Visual Studio for Mac 將會變更與 Xamarin.Mac 專案同步處理。 立即啟動應用程式文字檢視中輸入一些文字，並加以選取，開啟**編輯**功能表：

![功能表項目會自動啟用/停用](menu-images/appmenu07.png "功能表項目會自動啟用/停用")

請注意如何**剪下**，**複製**，並**貼上**項目會自動啟用和完整的功能，完全不需要撰寫一行程式碼。 

什麼呢？ 請記住內建的預先定義動作 （如以上所顯示），連接到預設的功能表項目，大多屬於 macOS 的 Cocoa 使用者介面項目具有內建的攔截程序來採取特定動作 (例如`copy:`)。 因此當他們新增至視窗中，作用中，並選取對應的功能表項目或項目附加至該動作會自動啟用。 如果使用者選取該功能表項目時，UI 項目的內建的功能將呼叫中，並執行，完全不需要開發人員介入。

### <a name="enabling-and-disabling-menus-and-items"></a>啟用和停用功能表和項目

根據預設，每次使用者事件發生時，`NSMenu`自動啟用和停用每個顯示的功能表和功能表項目為基礎的應用程式的內容。 有三種方式可啟用/停用項目：

- **啟用自動功能表**-如果啟用功能表項目`NSMenu`可以找到適當的物件項目是有線-最多的動作回應。 例如，文字檢視上述具有內建攔截程序`copy:`動作。
- **自訂動作和 validateMenuItem:** -作為繫結至任何功能表項目[視窗或檢視的控制器自訂動作](#Working-with-Custom-Window-Actions)，您可以新增`validateMenuItem:`動作以手動方式啟用或停用功能表項目。
- **手動功能表啟用**-手動設定`Enabled`每個屬性`NSMenuItem`啟用或停用功能表中的每個項目個別。

若要選擇一種系統，將`AutoEnablesItems`屬性`NSMenu`。 `true` 為自動 （預設行為） 及`false`為手動。 

> [!IMPORTANT]
> 如果您選擇使用 [手動] 功能表啟用，沒有任何功能表項目，即使受 AppKit 類別，例如`NSTextView`，會自動更新。 您必須在 啟用和停用所有的項目，以手動方式在程式碼中。

#### <a name="using-validatemenuitem"></a>使用 validateMenuItem

如上所述，繫結至任何功能表項目的[視窗或檢視控制器的自訂動作](#Working-with-Custom-Window-Actions)，您可以新增`validateMenuItem:`動作以手動方式啟用或停用功能表項目。

在下列範例中，`Tag`屬性會用來決定，將啟用/停用的功能表項目類型`validateMenuItem:`中選取文字的狀態為基礎的動作`NSTextView`。 `Tag`屬性已設定介面產生器中每個功能表項目：

![設定的 Tag 屬性](menu-images/validate01.png "設定標記屬性")

以及下列的程式碼加入至檢視控制器：

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

當執行此程式碼時，並在未選取任何文字`NSTextView`，兩個換行的功能表項目已停用 （即使它們都設計來檢視控制器上的動作）：

![停用的顯示項目](menu-images/validate02.png "顯示停用項目")

如果已選取 一段文字，而且功能表重新開啟，將可使用兩個換行的功能表項目：

![啟用顯示項目](menu-images/validate03.png "顯示啟用項目")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>啟用和回應程式碼中的功能表項目

我們已如上所示，只要將特定的 Cocoa 使用者介面項目新增至我們的 UI 設計 （例如文字欄位），有幾個預設的功能表項目會啟用，而且自動運作，而不需要撰寫任何程式碼。 下一步 讓我們看看我們自己 C# 程式碼加入至我們的 Xamarin.Mac 專案啟用功能表項目，並提供功能，當使用者選取它。

比方說，我們想讓使用者能夠使用的假設**開放**中的項目**檔案**功能表來選取資料夾。 因為我們希望它是全應用程式函式，並不限於提供視窗或 UI 項目，我們將新增程式碼來處理這給我們的應用程式委派。

在  **Solution Pad**，連按兩下`AppDelegate.CS`檔案，以開啟它進行編輯：

![選取應用程式委派](menu-images/appmenu08.png "選取應用程式委派")

新增下列程式碼下方`DidFinishLaunching`方法：

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

讓我們執行應用程式現在並開啟**檔案**功能表： 

![[檔案] 功能表](menu-images/appmenu09.png "[檔案] 功能表")

請注意，**開啟**現在已啟用功能表項目。 如果我們選取它，就會顯示開啟的對話方塊：

![開啟的對話方塊](menu-images/appmenu10.png "開啟的對話方塊")

如果我們按一下**開啟** 按鈕，將會顯示警示訊息：

![訊息的範例對話方塊](menu-images/appmenu11.png "對話方塊訊息的範例")

關鍵的行已`[Export ("openDocument:")]`，它會告訴`NSMenu`，我們**AppDelegate**具有方法`void OpenDialog (NSObject sender)`回應`openDocument:`動作。 如果您應記得從上述**開啟**功能表項目會自動有線總此動作預設會在介面產生器：

[![檢視連結的動作](menu-images/defaultbar03.png "檢視附加的動作")](menu-images/defaultbar03-large.png#lightbox)

下一步 讓我們看看建立我們自己的功能表中，功能表項目和動作及回應這些程式碼中。

### <a name="working-with-the-open-recent-menu"></a>使用 開啟新的功能表

根據預設，**檔案** 功能表包含**開啟最近**會持續追蹤的使用者已開啟您的應用程式的最後一個數個檔案的項目。 如果您要建立`NSDocument`Xamarin.Mac 應用程式，此功能表將會為您自動處理。 對於其他類型的 Xamarin.Mac 應用程式中，您會負責管理，以及以手動方式回應這個功能表項目。

若要手動處理**開啟新** 功能表中，您必須先以新的檔案已開啟或儲存使用下列通知它：

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

即使不使用您的應用程式`NSDocuments`，您仍然可以使用`NSDocumentController`維護**開啟最近**功能表傳送`NSUrl`檔案的位置`NoteNewRecentDocumentURL`方法`SharedDocumentController`。

接下來，您需要覆寫`OpenFile`方法來開啟任何使用者從選取的檔案的應用程式委派**最近開啟**功能表。 例如: 

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

傳回`true`如果可以開啟檔案，否則會傳回`false`和內建的警告會顯示給使用者無法開啟檔案。

因為從傳回的檔案名稱和路徑**開啟最近** 功能表中，可能會加上空格，我們必須建立之前會適當逸出此字元`NSUrl`或我們會收到錯誤。 我們這麼做，以下列程式碼：

```csharp
filename = filename.Replace (" ", "%20");
```

最後，我們建立`NSUrl`指向的檔案和使用委派來開啟新視窗，並將檔案載入的應用程式中的 helper 方法：

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

若要一起提取所有項目，讓我們看看中的範例實作**AppDelegate.cs**檔案：

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

根據您的應用程式的需求，您可能不希望使用者在一個以上的視窗中開啟相同的檔案，在相同的時間。 範例應用程式，如果使用者選擇已經開啟的檔案中 (從**開啟最近**或**開啟...** 功能表項目），視窗，其中包含該檔案會被帶到前方。

若要這麼做，我們可以使用下列程式碼在我們的 helper 方法：

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

我們設計我們`ViewController`類別來保存路徑中的檔案其`Path`屬性。 接下來，我們會重複執行的應用程式中所有目前開啟的視窗。 如果檔案已在其中一個視窗中開啟，會比使用的所有其他視窗的前面：

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

如果沒有找到符合的載入檔案開啟新視窗時，檔案中註明**開啟新**功能表：

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

### <a name="working-with-custom-window-actions"></a>使用自訂的視窗動作

就像內建**第一個回應**標準功能表項目來到預先有線的動作，您可以建立新的自訂動作並將它們接軌到介面產生器中的功能表項目。

首先，在其中一個您的應用程式視窗的控制站上定義的自訂動作。 例如: 

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

接下來，按兩下 應用程式的分鏡腳本檔案，在**Solution Pad**開啟它進行編輯在 Xcode 的 Interface Builder。 選取 **第一個回應**下方**應用程式場景**，然後切換到**屬性偵測器**:

![屬性偵測器](menu-images/action01.png "屬性偵測器")

按一下  **+** 底部的按鈕**屬性偵測器**新增新的自訂動作：

![新增新的動作](menu-images/action02.png "新增新的動作")

它提供相同的名稱，做為您在您的視窗控制站建立的自訂動作：

![編輯動作名稱](menu-images/action03.png "編輯動作名稱")

Control + 按一下，然後從功能表項目拖曳**第一個回應**下方**應用程式場景**。 從快顯清單中，選取您剛才建立的新動作 (`defineKeyword:`在此範例中):

![附加動作](menu-images/action04.png "附加動作")

將變更儲存到分鏡腳本，並返回 Visual Studio for Mac 將變更同步。 如果您執行應用程式時，您連接到自訂動作的功能表項目會自動啟用/停用 （根據範圍與動作會開啟） 並選取功能表項目隨即會啟動動作：

[![測試新的動作](menu-images/action05.png "測試新的動作")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>新增、 編輯和刪除功能表

如我們所見前幾節，Xamarin.Mac 應用程式就會隨附預設功能表和功能表項目特定的 UI 控制項將會自動啟動，並回應預設數目。 我們也已了解如何將程式碼新增至應用程式時，也會啟用並回應這些預設項目。

這一節我們將探討移除我們不需要的功能表項目、 重新組織功能表並加入新的功能表、 功能表項目和動作。

按兩下**Main.storyboard**中的檔案**Solution Pad**以開啟它進行編輯：

[![編輯在 Xcode 中的 UI](menu-images/maint01.png "編輯在 Xcode 中的 UI")](menu-images/maint01-large.png#lightbox)

我們的特定 Xamarin.Mac 應用程式我們不會使用預設值**檢視**功能表，因此我們將它移除。 在 **介面階層架構**選取**檢視**屬於主功能表列的功能表項目：

![選取 [檢視] 功能表項目](menu-images/maint02.png "選取 [檢視] 功能表項目")

按下 delete 鍵或退格鍵刪除功能表。 接下來，我們不想要使用的項目中的所有**格式**功能表，然後我們想要移動的項目，我們將使用 out 底下的子功能表。 在 **介面階層架構**選取下列的功能表項目：

![反白顯示多個項目](menu-images/maint03.png "反白顯示多個項目")

父系下的項目拖曳** 功能表**從其目前所在的子功能表：

[![將功能表項目拖曳至父功能表](menu-images/maint04.png "拖曳至父功能表的功能表項目")](menu-images/maint04-large.png#lightbox)

您的功能表應該看起來像：

[![新的位置中的項目](menu-images/maint05.png "中的新位置的項目")](menu-images/maint05-large.png#lightbox)

下一步 讓我們來拖曳**文字**出下的子功能表**格式**功能表並將它放在主功能表列之間**格式**並**視窗**功能表：

[![[文字] 功能表](menu-images/maint06.png "文字功能表")](menu-images/maint06-large.png#lightbox)

讓我們回到底下**格式**功能表，然後刪除**字型**子功能表項目。 接下來，選取**格式**功能表重新命名為 「 字型 」:

[![[字型] 功能表](menu-images/maint07.png "字型功能表")](menu-images/maint07-large.png#lightbox)

接下來，讓我們建立的預先定義的字詞，將會自動取得附加至 [文字] 檢視中的文字時就會依照選取自訂功能表。 在底部的 [搜尋] 方塊中**程式庫偵測器**型別，在 [功能表]。 這會讓您更輕鬆地尋找及使用的所有功能表的 UI 項目：

![程式庫偵測器](menu-images/maint08.png "程式庫偵測器")

現在讓我們執行下列命令來建立我們的功能表：

1. 拖曳**功能表項目**從**程式庫偵測器**之間的功能表列上**文字**並**視窗**功能表： 

    ![在 程式庫中選取新的功能表項目](menu-images/maint10.png "文件庫中選取新的功能表項目")
2. 重新命名 「 片語 」 的項目： 

    [![設定功能表名稱](menu-images/maint09.png "設定功能表名稱")](menu-images/maint09-large.png#lightbox)
3. 接著您可以拖曳** 功能表**從**程式庫偵測器**: 

    ![從程式庫選取功能表](menu-images/maint11.png "從程式庫選取功能表")
4. 卸除再** 功能表**上的新**功能表項目**我們剛剛建立，並將其名稱變更為 「 片語 」: 

    [![編輯功能表名稱](menu-images/maint12.png "編輯功能表名稱")](menu-images/maint12-large.png#lightbox)
5. 現在讓我們將三個預設的重新命名**功能表項目**「 位址 」、 「 日 」 和"Greeting": 

    [![片語功能表](menu-images/maint13.png "片語功能表")](menu-images/maint13-large.png#lightbox)
6. 讓我們加入第四個**功能表項目**藉由拖曳**功能表項目**從**程式庫偵測器**，然後呼叫該 「 簽章 」: 

    [![編輯功能表項目名稱](menu-images/maint14.png "編輯功能表項目名稱")](menu-images/maint14-large.png#lightbox)
7. 在功能表列中儲存的變更。

現在讓我們建立的一組自訂的動作，以便我們新的功能表項目會公開到 C# 程式碼。 在 Xcode 中讓我們切換**助理**檢視：

[![建立必要的動作](menu-images/maint15.png "建立必要的動作")](menu-images/maint15-large.png#lightbox)

讓我們執行下列作業：

1. 從控制項拖曳**地址**功能表項目**AppDelegate.h**檔案。
2. 交換器**連接**類型**動作**: 

    [![選取的動作類型](menu-images/maint17.png "選取動作類型")](menu-images/maint17-large.png#lightbox)
3. 請輸入**名稱**"phraseAddress"然後按**Connect**按鈕，以建立新的動作： 

    [![設定動作](menu-images/maint18.png "設定動作")](menu-images/maint18-large.png#lightbox)
4. 重複上述步驟**日期**，**問候語**，並**簽章**功能表項目： 

    [![完成的動作](menu-images/maint19.png "完成的動作")](menu-images/maint19-large.png#lightbox)
5. 在功能表列中儲存的變更。

接下來，我們需要建立我們的文字檢視輸出，以便我們可以調整其內容從程式碼。 選取  **ViewController.h**中的檔案**輔助編輯器**，並建立新的輸出呼叫`documentText`:

[![建立輸出](menu-images/maint20.png "建立輸出")](menu-images/maint20-large.png#lightbox)

返回 Visual Studio for Mac 將會同步處理來自 Xcode 的變更。 接著編輯**ViewController.cs**檔案，並使它看起來如下：

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

這會公開我們的文字檢視之外的文字`ViewController`類別，並通知應用程式委派，當視窗獲得或失去焦點。 現在編輯**AppDelegate.cs**檔案，並使它看起來如下：

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

這裡我們做了`AppDelegate`部分類別，因此我們可以使用的動作和 Interface Builder 中所定義的輸出。 我們也會公開`textEditor`追蹤焦點目前位於哪一個視窗。

下列方法會用來處理我們的自訂功能表和功能表項目：

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

現在如果我們執行我們應用程式中的項目中的所有**片語**功能表將會是作用中，而且會將提供片語新增至 [文字] 檢視中選取：

![執行的應用程式的範例](menu-images/maint21.png "執行的應用程式的範例")

既然我們已使用應用程式功能表列，向下的基本概念，讓我們看看建立自訂的內容功能表。

### <a name="creating-menus-from-code"></a>從程式碼中建立功能表

除了使用 Xcode 的 Interface Builder 中建立功能表和功能表項目，可能的有時候 Xamarin.Mac 應用程式需要建立、 修改或移除程式碼中的功能表、 子功能表或功能表項目。

在下列範例中，建立類別來保存將會以動態方式建立在即時的子功能表與功能表項目相關資訊：

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

定義此類別，與下面的常式會將剖析的集合`LanguageFormatCommand`物件，然後遞迴地建立新的功能表和功能表項目將它們附加至已傳入 （Interface Builder 中建立） 的現有功能表底部：

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

針對任何`LanguageFormatCommand`具有空白物件`Title`屬性，這個常式會建立**分隔符號功能表項目**（精簡型的灰線） 功能表的各區段間：

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

如果未提供標題，則會建立新的功能表項目，這個標題：

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

如果`LanguageFormatCommand`物件包含子系`LanguageFormatCommand`物件，建立子功能表和`AssembleMenu`方法是以遞迴方式呼叫，以建置該功能表：

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

針對任何新功能表項目沒有子功能表，來處理使用者所選取的功能表項目會加入程式碼：

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>測試功能表建立

所有上述程式碼中的位置，如果以下一系列`LanguageFormatCommand`物件所建立：

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

與集合傳遞給`AssembleMenu`函式 (使用**格式**功能表設定做為基底)，會建立下列的動態功能表和功能表項目：

![在執行中應用程式中的新功能表項目](menu-images/dynamic01.png "中執行的應用程式的新功能表項目")

#### <a name="removing-menus-and-items"></a>移除功能表和項目

如果您需要從應用程式的使用者介面中移除任何功能表或功能表項目，您可以使用`RemoveItemAt`方法的`NSMenu`類別只是，提供零為起始的索引要移除的項目。

例如，若要移除功能表和上述的常式所建立的功能表項目，您可以使用下列程式碼：

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

在上述程式碼前, 四個功能表項目會建立在 Xcode 的 Interface Builder 和 aways 可用應用程式中，因此不會以動態方式移除。

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>內容功能表

使用者上按一下滑鼠右鍵或按一下控制項視窗中的項目時，會出現內容功能表。 根據預設，有幾個已經內建於 macOS 的 UI 項目有附加到它們 （例如 [文字] 檢視中） 的內容功能表。 不過，可能是我們想来建立自己自訂的內容功能表，我們已新增至視窗的 UI 元素。

讓我們編輯我們**Main.storyboard**在 Xcode 中的檔案，並新增**視窗**我們的設計視窗設定其**類別**"NSPanel 」 中以**身分識別偵測器**，新增**Assistant**項目**視窗**功能表，並將它附加至新的視窗中使用**顯示 Segue**:

[![設定 segue 類型](menu-images/context01.png "設定 segue 類型")](menu-images/context01-large.png#lightbox)

讓我們執行下列作業：

1. 拖曳**標籤**從**程式庫偵測器**拖曳至**面板**視窗，並設定它的文字為"Property": 

    [![編輯標籤的值](menu-images/context03.png "編輯標籤的值")](menu-images/context03-large.png#lightbox)
2. 接著您可以拖曳 **] 功能表**從**程式庫偵測器**拖曳至 [檢視階層] 和 [重新命名其中三個預設功能表項目中的檢視控制器**文件**，**文字**並**字型**:

    [![必要的功能表項目](menu-images/context02.png "必要的功能表項目")](menu-images/context02-large.png#lightbox)
3. 現在拖曳控制項從**屬性標籤**拖曳至**功能表**:

    [![若要建立的 segue 拖曳](menu-images/context04.png "建立 segue 拖曳")](menu-images/context04-large.png#lightbox)
4. 從快顯對話方塊中，選取** 功能表**: 

    ![設定 segue 類型](menu-images/context05.png "設定 segue 類型")
5. 從**身分識別偵測器**，設定 「 PanelViewController 「 檢視控制器的類別： 

    [![設定 segue 類別](menu-images/context10.png "設定 segue 類別")](menu-images/context10-large.png#lightbox)
6. 切換回 Visual Studio for Mac 將會同步處理，然後返回 介面產生器。
7. 切換至**輔助編輯器**，然後選取**PanelViewController.h**檔案。
8. 建立的動作**文件**功能表項目稱為`propertyDocument`: 

    [![設定動作](menu-images/context06.png "設定動作")](menu-images/context06-large.png#lightbox)
9. 針對其餘的功能表項目中重複建立動作： 

    [![必要的動作](menu-images/context07.png "必要的動作")](menu-images/context07-large.png#lightbox)
10. 最後建立的輸出**屬性標籤**稱為`propertyLabel`: 

    [![設定輸出](menu-images/context08.png "設定輸出")](menu-images/context08-large.png#lightbox)
11. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

編輯**PanelViewController.cs**檔案，並新增下列程式碼：

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

現在如果我們執行應用程式，並以滑鼠右鍵按一下屬性標籤面板中，我們會看到我們的自訂內容功能表。 如果我們選取，並從功能表項目，則會變更標籤的值：

![執行的操作功能表](menu-images/context09.png "執行的操作功能表")

下一步 讓我們看看建立狀態 列功能表。

## <a name="status-bar-menus"></a>狀態列功能表

狀態列功能表會顯示給使用者，例如功能表或反映的應用程式狀態的映像提供互動的狀態功能表項目或意見反應的集合。 應用程式的 [狀態] 列功能表是啟用或作用中，即使應用程式在背景中執行。 全系統狀態] 列位於右上方的 [應用程式功能表列，而且是唯一在 macOS 中目前可用的狀態列。

讓我們編輯我們**AppDelegate.cs**檔案，並讓`DidFinishLaunching`方法外觀如下所示：

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;` 可讓我們存取全系統狀態 列。 `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);` 建立新的狀態 列項目。 從該處我們建立功能表和功能表項目數目，並將功能表附加至我們剛剛建立的狀態列項目。 

如果我們執行應用程式時，就會顯示新的狀態 列項目。 從功能表中選取項目，將 [文字] 檢視中的文字： 

![執行 [狀態] 列功能表](menu-images/statusbar01.png "執行 [狀態] 列功能表")

接下來，讓我們看看建立自訂的停駐功能表項目。

## <a name="custom-dock-menus"></a>自訂的停駐功能表

在使用者上按一下滑鼠右鍵或在 dock 中的應用程式的圖示-按一下控制項時，為您的 Mac 應用程式會出現停駐功能表：

![自訂的停駐功能表](menu-images/dock01.png "自訂停駐功能表")

讓我們建立我們的應用程式自訂的停駐功能表執行下列動作：

1. 在 Visual Studio for Mac，以滑鼠右鍵按一下應用程式的專案，然後選取**新增** > **新檔...** 從新的 [檔案] 對話方塊中，選取**Xamarin.Mac** > **空白介面定義**，使用"DockMenu 」**名稱**按一下**新增**按鈕，以建立新**DockMenu.xib**檔案：

    ![新增空白介面定義](menu-images/dock02.png "新增空白介面定義")
2. 在  **Solution Pad**，按兩下**DockMenu.xib**檔案將它開啟以在 Xcode 中編輯。 建立新** 功能表**具有下列項目：**地址**，**日期**，**問候語**，和**簽章** 

    [![UI 的版面配置](menu-images/dock03.png "UI 的版面配置")](menu-images/dock03-large.png#lightbox)
3. 接下來，讓我們先連接到我們現有的動作，我們建立我們自訂的功能表中的 我們新的功能表項目[新增、 編輯和刪除功能表](#Adding,_Editing_and_Deleting_Menus)上一節。 切換至**連接 Inspector** ，然後選取**第一個回應**中**介面階層架構**。 向下捲動並尋找`phraseAddress:`動作。 在該動作將圓形線條**地址**功能表項目：

    [![拖曳動作連結](menu-images/dock04.png "拖曳動作連結")](menu-images/dock04-large.png#lightbox)
4. 重複的所有其他功能表項目將它們連結至其對應的動作： 

    [![必要的動作](menu-images/dock05.png "必要的動作")](menu-images/dock05-large.png#lightbox)
5. 接下來，選取**應用程式**中**介面階層架構**。 在 **連線偵測器**，將圓形線條`dockMenu`插座上我們剛剛建立的功能表：

    [![拖曳輸出接上](menu-images/dock06.png "拖曳輸出連接")](menu-images/dock06-large.png#lightbox)
6. 儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。
7. 按兩下**Info.plist**檔案，以開啟它進行編輯： 

    [![編輯 Info.plist 檔案](menu-images/dock07.png "編輯 Info.plist 檔案")](menu-images/dock07-large.png#lightbox)
8. 按一下 **來源**在畫面底部的索引標籤： 

    [![選取的來源檢視](menu-images/dock08.png "選取的來源檢視")](menu-images/dock08-large.png#lightbox)
9. 按一下 **加入新項目**、 按一下綠色加號按鈕、 設定屬性名稱，以 「 AppleDockMenu"和"DockMenu"（新.xib 檔案不含副檔名的名稱） 的值： 

    [![新增項目 DockMenu](menu-images/dock09.png "新增 DockMenu 項目")](menu-images/dock09-large.png#lightbox)

現在如果我們執行應用程式，並以滑鼠右鍵按一下它在 Dock 中的圖示，將會顯示我們新的功能表項目：

![舉例來說，執行停駐功能表](menu-images/dock10.png "停駐功能表執行的範例")

如果我們從功能表選取其中一個自訂的項目，將會修改在我們的文字檢視中的文字。

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>快顯的按鈕和下拉式清單

快顯的按鈕會顯示選取的項目，並顯示當使用者按下時，從選取的選項清單。 下拉式清單是按鈕的一種快顯通常用於選取特定內容的目前工作的命令。 這兩者可以在視窗中任何位置出現。

我們將建立的快顯的自訂按鈕，我們的應用程式執行下列動作：

1. 編輯**Main.storyboard**在 Xcode 和拖曳中的檔案**快顯視窗按鈕**從**程式庫偵測器**拖曳至**面板**我們在中建立的視窗[關聯式功能表](#Contextual_Menus)區段： 

    [![加入快顯功能表按鈕](menu-images/popup01.png "加入快顯視窗按鈕")](menu-images/popup01-large.png#lightbox)
2. 加入新的功能表項目，並以快顯視窗中設定之項目的標題：**地址**，**日期**，**問候語**，和**簽章** 

    [![設定功能表項目](menu-images/popup02.png "設定功能表項目")](menu-images/popup02-large.png#lightbox)
3. 接下來，讓我們連接到現有的動作，我們建立我們自訂的功能表中的 我們新的功能表項目[新增、 編輯和刪除功能表](#Adding,_Editing_and_Deleting_Menus)上一節。 切換至**連接 Inspector** ，然後選取**第一個回應**中**介面階層架構**。 向下捲動並尋找`phraseAddress:`動作。 在該動作將圓形線條**地址**功能表項目： 

    [![拖曳動作連結](menu-images/popup03.png "拖曳動作連結")](menu-images/popup03-large.png#lightbox)
4. 重複的所有其他功能表項目將它們連結至其對應的動作： 

    [![所有必要的動作](menu-images/popup04.png "所有必要的動作")](menu-images/popup04-large.png#lightbox)
5. 儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。

現在如果我們執行應用程式，並從快顯視窗中選取的項目，則我們的文字檢視中的文字會變更：

![執行快顯視窗的範例](menu-images/popup05.png "執行快顯視窗的範例")

您可以建立並使用下拉式清單中完全相同的方式，為快顯的按鈕。 而不是附加至現有的動作，您可以建立您自己的自訂動作就像我們在我們內容功能表[關聯式功能表](#Contextual_Menus)一節。

## <a name="summary"></a>總結

本文所深入了解使用功能表和功能表項目在 Xamarin.Mac 應用程式。 第一次我們檢查應用程式的功能表列，然後我們探討了建立內容功能表，接下來，我們檢查狀態 列功能表和自訂停駐功能表。 最後，我們將探討快顯功能表下拉式清單。


## <a name="related-links"></a>相關連結

- [MacMenus （範例）](https://developer.xamarin.com/samples/mac/MacMenus/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [人性化介面指導方針-功能表](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [應用程式功能表和快顯清單簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
