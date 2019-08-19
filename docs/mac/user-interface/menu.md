---
title: Xamarin 中的功能表
description: 本文說明如何在 Xamarin. Mac 應用程式中使用功能表。 它描述如何建立和維護 Xcode 和 Interface Builder 中的功能表和功能表項目, 並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 5D367F8E-3A76-4995-8A89-488530FAD802
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 0879fcc529e72e03df4eaba7790a534ace38856f
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657348"
---
# <a name="menus-in-xamarinmac"></a>Xamarin 中的功能表

_本文說明如何在 Xamarin. Mac 應用程式中使用功能表。它描述如何建立和維護 Xcode 和 Interface Builder 中的功能表和功能表項目, 並以程式設計方式使用它們。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時, 您可以存取開發人員在目標-C 和 Xcode 中運作的相同 Cocoa 功能表。 因為 Xamarin 會直接與 Xcode 整合, 所以您可以使用 Xcode 的 Interface Builder 來建立和維護功能表列、功能表和功能表項目 (或選擇直接在程式碼中C#建立)。

功能表是 Mac 應用程式使用者體驗不可或缺的一部分, 而且通常會出現在使用者介面的各個部分:

- **應用程式的功能表列**-這是顯示在每個 Mac 應用程式畫面頂端的主功能表。
- 內容**功能表**-當使用者以滑鼠右鍵按一下或按一下視窗中的專案時, 就會出現這些控制項。
- **狀態列**-這是顯示在畫面頂端的應用程式功能表列最右邊的區域 (在功能表列時鐘的左邊), 並隨著專案新增至左側而成長。
- 停**駐功能表**-dock 中的每個應用程式的功能表, 當使用者以滑鼠右鍵按一下或按一下應用程式的圖示時, 或當使用者按下圖示並按住滑鼠按鍵時, 就會出現該控制項。
- 快顯**按鈕和下拉式清單**-彈出按鈕會顯示選取的專案, 並提供使用者按下時要選取的選項清單。 下拉式清單是一種彈出式按鈕, 通常用來選取目前工作內容的特定命令。 這兩者都可以出現在視窗中的任何位置。

[![範例功能表](menu-images/intro01.png "範例功能表")](menu-images/intro01-large.png#lightbox)

在本文中, 我們將討論在 Xamarin. Mac 應用程式中使用 Cocoa 功能表列、功能表和功能表項目的基本概念。 強烈建議您先流覽[Hello, Mac](~/mac/get-started/hello-mac.md)文章, 特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介, 其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要看一下[Xamarin 內部](~/mac/internals/how-it-works.md)檔的「將`Register` [類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md) 」一節, 它會說明用來將C#類別連線`Export`到的和屬性。目標-C 物件和 UI 元素。

## <a name="the-applications-menu-bar"></a>應用程式的功能表列 

不同于在 Windows 作業系統上執行的應用程式, 每個視窗都可以附加自己的功能表列, 在 macOS 上執行的每個應用程式都有單一功能表列, 會沿著用於該應用程式中每個視窗的畫面頂端來執行:

[![功能表列](menu-images/appmenu01.png "功能表列")](menu-images/appmenu01-large.png#lightbox)

此功能表列上的專案會根據應用程式的目前內容或狀態, 以及在任何指定時間的使用者介面來啟用或停用。 例如: 如果使用者選取文字欄位, 將會啟用 [**編輯**] 功能表上的專案, 例如 [**複製**] 和 [**剪**下]。

根據 Apple 和預設, 所有 macOS 應用程式都有一組標準的功能表和功能表項目, 會出現在應用程式的功能表列中:

- **Apple 功能表**-此功能表可讓您存取隨時可供使用者使用的系統範圍專案, 不論應用程式正在執行哪一種。 開發人員無法修改這些專案。
- **應用程式功能表**-此功能表會以粗體顯示應用程式的名稱, 並協助使用者識別目前正在執行的應用程式。 其中包含套用至整個應用程式的專案, 而不是指定的檔或進程, 例如結束應用程式。
- [檔案] 功能表-用來建立、開啟或儲存應用程式使用之**檔**的專案。 如果您的應用程式不是以檔為基礎, 您可以重新命名或移除此功能表。
- [**編輯] 功能表**-保留命令, 例如**剪**下、**複製**和**貼**上, 用來編輯或修改應用程式使用者介面中的元素。
- **格式功能表**-如果應用程式使用文字, 此功能表會保留命令來調整該文字的格式。
- [**視圖] 功能表**-包含會影響在應用程式的使用者介面中顯示內容的方式的命令。
- **應用程式特定的功能表**-這些是應用程式特定的任何功能表 (例如網頁瀏覽器的 [書簽] 功能表)。 它們應該會出現在橫條圖的 [ **View** ] 和 [ **Window]** 功能表之間。
- [**視窗] 功能表**-包含在應用程式中使用視窗的命令, 以及目前開啟視窗的清單。
- [說明]**功能表**-如果您的應用程式提供螢幕快顯功能表, [說明] 功能表應該是列上最右邊的功能表。 

如需有關應用程式功能表列和標準功能表和功能表項目的詳細資訊, 請參閱 Apple 的[人類介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)。

### <a name="the-default-application-menu-bar"></a>預設應用程式功能表列

當您建立新的 Xamarin 專案時, 您會自動取得標準的預設應用程式功能表列, 其中包含 macOS 應用程式通常會有的一般專案 (如上面的一節所述)。 應用程式的預設功能表列定義于**Solution Pad**的專案下的**主要**分鏡腳本檔案中 (以及應用程式的其餘 UI):  

![選取主要]分鏡腳本(menu-images/appmenu02.png "選取主要")分鏡腳本

按兩下**主要**的分鏡腳本檔案, 在 Xcode 的 Interface Builder 中開啟以進行編輯, 您將會看到功能表編輯器介面:

[![在 Xcode 中編輯 UI](menu-images/defaultbar01.png "在 Xcode 中編輯 UI")](menu-images/defaultbar01-large.png#lightbox)

從這裡開始, 我們可以按一下 [檔案] 功能表中的 [**開啟**] 功能表項目之類的專案, 然後在 [**屬性偵測器**] 中編輯或調整其屬性:

[![編輯功能表的屬性](menu-images/defaultbar02.png "編輯功能表的屬性")](menu-images/defaultbar02-large.png#lightbox)

我們將在本文稍後加入、編輯和刪除功能表和專案。 現在, 我們只想要查看預設可用的功能表和功能表項目, 以及如何透過一組預先定義的輸出和動作, 自動將它們公開給程式碼 (如需詳細資訊, 請參閱我們的[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)檔)。

例如, 如果我們按一下 [**開啟**] 功能表項目的`openDocument:`連接偵測**器**, 就會看到它自動連接到動作: 

[正在![觀看附加的動作]正在(menu-images/defaultbar03.png "觀看附加的動作")](menu-images/defaultbar03-large.png#lightbox)

如果您選取**介面**階層中的**第一個回應**程式, 並在**連接偵測器**中向下移動, 您會`openDocument:`看到**開啟**的功能表項目所附加的動作定義 (以及數個應用程式的其他預設動作, 也就是和不會自動連接到控制項):

[![查看所有附加的動作](menu-images/defaultbar04.png "查看所有附加的動作")](menu-images/defaultbar04-large.png#lightbox) 

這是很重要的嗎？ 在下一節中, 會看到這些自動定義的動作如何與其他 Cocoa 使用者介面元素搭配運作, 以自動啟用和停用功能表項目, 以及提供專案的內建功能。

之後, 我們將使用這些內建動作來啟用和停用程式碼中的專案, 並在選取時提供自己的功能。

<a name="Built-In_Menu_Functionality" />

### <a name="built-in-menu-functionality"></a>內建功能表功能

如果您在新增任何 UI 專案或程式碼之前, 先執行新建立的 Xamarin. Mac 應用程式, 您會發現某些專案會自動為您啟動並啟用 (完全自動內建功能), 例如, **應用程式**功能表:

![啟用的功能表項目](menu-images/appmenu03.png "啟用的功能表項目")

其他功能表項目 (例如**剪**下、**複製**和**貼**上) 則不是:

![停用的功能表項目](menu-images/appmenu04.png "停用的功能表項目")

讓我們停止應用程式, 然後按兩下**Solution Pad**中的**主要**分鏡腳本檔案, 將它開啟, 以在 Xcode 的 Interface Builder 中進行編輯。 接下來, 將 [**文字] 視圖**從連結**庫**拖曳至 [**介面編輯器**] 中的視窗視圖控制器:

[![從程式庫選取文字視圖](menu-images/appmenu05.png "從程式庫選取文字視圖")](menu-images/appmenu05-large.png#lightbox)

在 [**條件約束編輯器**] 中, 讓我們將文字視圖釘選到視窗的邊緣, 並在視窗的頂端按一下所有四個紅色的字形狀, 然後按一下 [**加入4個條件約束**] 按鈕, 將它設定為放大和縮小。

[![編輯條件約束](menu-images/appmenu06.png "編輯條件約束")](menu-images/appmenu06-large.png#lightbox)

將您的變更儲存至使用者介面設計, 並切換回 Visual Studio for Mac 以同步處理您的 Xamarin. Mac 專案的變更。 現在啟動應用程式, 在 [文字] 視圖中輸入一些文字, 加以選取, 然後開啟 [**編輯**] 功能表:

![功能表項目會自動啟用/停用](menu-images/appmenu07.png "功能表項目會自動啟用/停用")

請注意,**剪**下、**複製**和**貼**上專案如何自動啟用和完全正常運作, 而不需要撰寫任何一行程式碼。 

這是怎麼回事？ 請記住, 進入預設功能表項目的內建預先定義動作 (如上所示), 大部分屬於 macOS 的 Cocoa 使用者介面元素都具有特定動作的內建勾點 (例如`copy:`)。 因此當它們新增至視窗、作用中並選取時, 會自動啟用對應的功能表項目或附加至該動作的專案。 如果使用者選取該功能表項目, 就會呼叫並執行內建于 UI 專案的功能, 而不需要開發人員介入。

### <a name="enabling-and-disabling-menus-and-items"></a>啟用和停用功能表和專案

依預設, 每次發生使用者事件時, `NSMenu`都會根據應用程式的內容自動啟用和停用每個可見的功能表和功能表項目。 有三種方式可啟用/停用專案:

- **自動功能表啟用**-如果`NSMenu`可以找到適當的物件來回應專案所連接的動作, 則會啟用功能表項目。 例如, 上面的文字視圖與`copy:`動作有內建的勾點。
- **自訂動作和 validateMenuItem:** 對於系結至[視窗或 view controller 自訂動作](#Working-with-Custom-Window-Actions)的任何功能表項目, 您`validateMenuItem:`可以加入動作並手動啟用或停用功能表項目。
- **手動功能表啟用**-您手動設定`Enabled`每個`NSMenuItem`的屬性, 以個別啟用或停用功能表中的每個專案。

若要選擇系統, 請設定`AutoEnablesItems`的屬性。 `NSMenu` `true`是自動的 (預設行為), `false`而是手動。 

> [!IMPORTANT]
> 如果您選擇使用手動功能表啟用, 則不會自動更新功能表項目, 即使是由 AppKit 類別所`NSTextView`控制的專案也是一樣。 您將負責以手動方式啟用和停用程式碼中的所有專案。

#### <a name="using-validatemenuitem"></a>使用 validateMenuItem

如上所述, 針對系結至 [[視窗] 或 [View Controller] 自訂動作](#Working-with-Custom-Window-Actions)的任何功能表項目, 您可以`validateMenuItem:`加入動作並手動啟用或停用功能表項目。

在下列範例中, `Tag`屬性將用來根據中`NSTextView`選取之文字的狀態, 決定`validateMenuItem:`動作將啟用/停用的功能表項目類型。 已`Tag`在每個功能表項目的 Interface Builder 中設定屬性:

![設定 Tag 屬性](menu-images/validate01.png "設定 Tag 屬性")

並將下列程式碼新增至 View Controller:

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

當此程式碼執行時, 如果未在中`NSTextView`選取任何文字, 則會停用兩個 [自動換行] 功能表項目 (即使它們已連接到 view controller 上的動作也一樣):

![顯示已停用的專案](menu-images/validate02.png "顯示已停用的專案")

如果選取了一段文字, 並重新開啟功能表, 則會有兩個 [自動換行] 功能表項目:

![顯示已啟用的專案](menu-images/validate03.png "顯示已啟用的專案")

## <a name="enabling-and-responding-to-menu-items-in-code"></a>啟用和回應程式碼中的功能表項目

如先前所見, 只要將特定的 Cocoa 使用者介面元素加入至 UI 設計 (例如文字欄位), 就會啟用數個預設功能表項目並自動運作, 而不需要撰寫任何程式碼。 接下來, 我們來看一下如何C#將自己的程式碼新增至我們的 Xamarin 專案, 以啟用功能表項目, 並在使用者選取時提供功能。

例如, 假設我們想要讓使用者能夠使用 [檔案] 功能表中的 [**開啟**]專案來選取資料夾。 因為我們想要這是整個應用程式的函式, 而不限於提供的視窗或 UI 元素, 所以我們將新增程式碼來處理應用程式委派。

在  **Solution Pad**中, 按兩下`AppDelegate.CS`檔案以開啟它進行編輯:

![選取應用程式委派](menu-images/appmenu08.png "選取應用程式委派")

在`DidFinishLaunching`方法下方新增下列程式碼:

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

讓我們立即執行應用程式, 並開啟 [檔案] 功能表: 

![[]檔案] 功能表(menu-images/appmenu09.png "[")檔案] 功能表

請注意, 現在已啟用 [**開啟**] 功能表項目。 如果我們選取它, 將會顯示 [開啟] 對話方塊:

![開啟的對話方塊](menu-images/appmenu10.png "開啟的對話方塊")

如果我們按一下 [**開啟**] 按鈕, 將會顯示警示訊息:

![範例對話訊息](menu-images/appmenu11.png "範例對話訊息")

這裡的金鑰行是`[Export ("openDocument:")]`, 它會`NSMenu`告訴我們的**AppDelegate** `void OpenDialog (NSObject sender)` `openDocument:`有一個回應動作的方法。 如果您記得上面的內容, [**開啟**] 功能表項目預設會自動連接到此動作, Interface Builder 中:

[![查看附加的動作](menu-images/defaultbar03.png "查看附加的動作")](menu-images/defaultbar03-large.png#lightbox)

接下來, 我們來看一下如何建立自己的功能表、功能表項目和動作, 以及如何在程式碼中進行回應。

### <a name="working-with-the-open-recent-menu"></a>使用 [開啟最近使用] 功能表

根據預設, [檔案] 功能表包含 [**開啟最近**使用的專案], 可以追蹤使用者在您的應用程式中開啟的最後幾個檔案。 如果您要建立以`NSDocument` Xamarin 為基礎的應用程式, 系統會自動為您處理此功能表。 對於任何其他類型的 Xamarin. Mac 應用程式, 您將負責以手動方式管理和回應此功能表項目。

若要手動處理 [**最近開啟**] 功能表, 您必須先通知它已使用下列程式來開啟或儲存新的檔案:

```csharp
// Add document to the Open Recent menu
NSDocumentController.SharedDocumentController.NoteNewRecentDocumentURL(url);
```

即使您的應用程式未使用`NSDocuments`, 您仍然可以`NSDocumentController`使用來維持 [**最近** `NSUrl`開啟] 功能表, 方法是將含有檔案位置的傳送至`NoteNewRecentDocumentURL`的方法`SharedDocumentController`。

接下來, 您必須覆寫`OpenFile`應用程式委派的方法, 以開啟使用者從 [**開啟最近**] 功能表選取的任何檔案。 例如：

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

如果可以開啟檔案, 則傳回, `false`否則會傳回, 並向使用者顯示無法開啟檔案的內建警告。 `true`

因為從 [**開啟最近使用**] 功能表傳回的檔案名和路徑可能包含空格, 所以我們必須在建立之前正確地將此`NSUrl`字元換用, 否則我們會收到錯誤。 我們使用下列程式碼來執行這項操作:

```csharp
filename = filename.Replace (" ", "%20");
```

最後, 我們`NSUrl`會建立指向檔案的, 並在應用程式委派中使用 helper 方法來開啟新視窗, 並將檔案載入其中:

```csharp
var url = new NSUrl ("file://"+filename);
return OpenFile(url);
```

為了將所有內容提取在一起, 讓我們來看一個**AppDelegate.cs**檔案中的範例執行:

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

根據您的應用程式需求, 您可能不想讓使用者同時在多個視窗中開啟相同的檔案。 在我們的範例應用程式中, 如果使用者選擇已開啟的檔案 (從 [**開啟最近**] 或 [開啟]) **。** 功能表項目), 包含檔案的視窗會帶入前面。

為了達成此目的, 我們在 helper 方法中使用了下列程式碼:

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

我們設計`ViewController`了類別, 以在其`Path`屬性中保存檔案的路徑。 接下來, 我們會對應用程式中所有目前開啟的視窗執行迴圈。 如果檔案已在其中一個視窗中開啟, 則會使用將它移到所有其他視窗的前方:

```csharp
NSApplication.SharedApplication.Windows[n].MakeKeyAndOrderFront(this);
```

如果找不到相符的內容, 就會開啟新的視窗並載入檔案, 並在 [**開啟最近**] 功能表中注明檔案:

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

就像內建的**第一個回應**程式動作, 它們會預先連接至標準功能表項目, 您可以建立新的自訂動作, 並將其連線至 Interface Builder 中的功能表項目。

首先, 在其中一個應用程式的視窗控制器上定義自訂動作。 例如：

```csharp
[Action("defineKeyword:")]
public void defineKeyword (NSObject sender) {
    // Preform some action when the menu is selected
    Console.WriteLine ("Request to define keyword");
}
```

接下來, 按兩下**Solution Pad**中的應用程式分鏡腳本檔案, 將它開啟, 以在 Xcode 的 Interface Builder 中進行編輯。 選取**應用程式場景**下的**第一個回應**者, 然後切換至 [**屬性偵測器**]:

![屬性偵測器](menu-images/action01.png "屬性偵測器")

按一下  **+** 底部的按鈕**屬性偵測器**新增新的自訂動作：

![加入新動作](menu-images/action02.png "加入新動作")

提供與您在視窗控制器上建立之自訂動作相同的名稱:

![編輯動作名稱](menu-images/action03.png "編輯動作名稱")

按住 Control 並從功能表項目拖曳至**應用程式場景**下的**第一個回應**程式。 從快顯清單中, 選取您剛才建立的新動作`defineKeyword:` (在此範例中為):

![附加動作](menu-images/action04.png "附加動作")

將變更儲存至分鏡腳本, 並返回 Visual Studio for Mac 以同步處理變更。 如果您執行應用程式, 則您連接自訂動作的功能表項目會自動啟用/停用 (根據開啟動作的視窗), 然後選取功能表項目將會引發動作:

[![測試新動作](menu-images/action05.png "測試新動作")](menu-images/action05-large.png#lightbox)

<a name="Adding,_Editing_and_Deleting_Menus" />

### <a name="adding-editing-and-deleting-menus"></a>新增、編輯和刪除功能表

如先前各節所見, Xamarin. Mac 應用程式隨附預設的預設功能表和功能表項目數目, 特定的 UI 控制項將會自動啟動並回應。 我們也已瞭解如何將程式碼新增至我們的應用程式, 也會啟用並回應這些預設專案。

在本節中, 我們將探討如何移除不需要的功能表項目、重新組織功能表, 以及加入新的功能表、功能表項目和動作。

按兩下**Solution Pad**中的**主要**分鏡腳本檔案, 將其開啟以進行編輯:

[![在 Xcode 中編輯 UI](menu-images/maint01.png "在 Xcode 中編輯 UI")](menu-images/maint01-large.png#lightbox)

針對我們的特定 Xamarin 應用程式, 我們不會使用預設的 [**流覽**] 功能表, 因此我們將會移除它。 在 [**介面**階層架構] 中, 選取屬於主要功能表列的 [ **View** ] 功能表項目:

![選取 [View] 功能表項目](menu-images/maint02.png "選取 [View] 功能表項目")

按 delete 或倒退鍵刪除功能表。 接下來, 我們不會使用 [**格式**] 功能表中的所有專案, 而想要從子功能表下移動要使用的專案。 在 [**介面**階層架構] 中, 選取下列功能表項目:

反白![顯示多個專案]反白(menu-images/maint03.png "顯示多個專案")

父系下的項目拖曳 **功能表** 從其目前所在的子功能表：

[將![功能表項目拖曳至父功能表]將(menu-images/maint04.png "功能表項目拖曳至父功能表")](menu-images/maint04-large.png#lightbox)

您的功能表現在看起來應該像這樣:

[![新位置中的專案](menu-images/maint05.png "新位置中的專案")](menu-images/maint05-large.png#lightbox)

接下來, 讓我們從 [**格式**] 功能表底下拖曳 [**文字**] 子功能表, 並將它放在 [**格式**] 和 [**視窗]** 功能表之間的主功能表列上:

[![文字功能表](menu-images/maint06.png "文字功能表")](menu-images/maint06-large.png#lightbox)

讓我們回到 [**格式**] 功能表, 並刪除 [**字型**] 子功能表專案。 接下來, 選取 [**格式**] 功能表, 並將它重新命名為 "Font":

[![字型功能表](menu-images/maint07.png "字型功能表")](menu-images/maint07-large.png#lightbox)

接下來, 我們將建立一個預先定義的片語的自訂功能表, 當選取此選項時, 它會自動附加至文字視圖中的文字。 在 [程式庫] 偵測**器**底部的 [搜尋] 方塊中, 輸入「menu」。 這可讓您更輕鬆地尋找和使用所有的功能表 UI 元素:

連結![庫偵測器]連結(menu-images/maint08.png "庫偵測器")

現在讓我們執行下列動作來建立功能表:

1. 將**功能表項目**從 [連結**庫偵測器**] 拖曳至 [**文字**] 和 [**視窗]** 功能表之間的功能表列: 

    ![選取程式庫中的新功能表項目](menu-images/maint10.png "選取程式庫中的新功能表項目")
2. 將專案重新命名為「片語」: 

    [![設定功能表名稱](menu-images/maint09.png "設定功能表名稱")](menu-images/maint09-large.png#lightbox)
3. 接著您可以拖曳 **功能表** 從 **程式庫偵測器**: 

    ![從程式庫選取功能表](menu-images/maint11.png "從程式庫選取功能表")
4. 卸除再 **功能表** 上的新 **功能表項目** 我們剛剛建立，並將其名稱變更為 「 片語 」: 

    [![編輯功能表名稱](menu-images/maint12.png "編輯功能表名稱")](menu-images/maint12-large.png#lightbox)
5. 現在讓我們將三個預設的**功能表項目**「位址」、「日期」和「問候」重新命名: 

    [![[片語] 功能表](menu-images/maint13.png "[片語] 功能表")](menu-images/maint13-large.png#lightbox)
6. 讓我們新增第四個**功能表項目**, 方法是從連結**庫偵測器**拖曳**功能表項目**, 並呼叫它「簽章」: 

    [![編輯功能表項目名稱](menu-images/maint14.png "編輯功能表項目名稱")](menu-images/maint14-large.png#lightbox)
7. 將變更儲存至功能表列。

現在讓我們來建立一組自訂動作, 以便將新的功能表項目公開C#給程式碼。 在 Xcode 中, 讓我們切換至 [**助理**] 視圖:

[![建立必要的動作](menu-images/maint15.png "建立必要的動作")](menu-images/maint15-large.png#lightbox)

讓我們執行下列動作:

1. 從 [**位址**] 功能表項目將控制項拖曳至**AppDelegate**檔案。
2. 將 [連線類型] 切換為 [**動作**]: 

    [![選取動作類型](menu-images/maint17.png "選取動作類型")](menu-images/maint17-large.png#lightbox)
3. 輸入 "phraseAddress" 的**名稱**, 然後按 [**連接]** 按鈕以建立新的動作: 

    [![正在設定動作](menu-images/maint18.png "正在設定動作")](menu-images/maint18-large.png#lightbox)
4. 針對 [**日期**]、[**問候語**] 和 [簽章] 功能表項目重複上述步驟: 

    [![完成的動作](menu-images/maint19.png "完成的動作")](menu-images/maint19-large.png#lightbox)
5. 將變更儲存至功能表列。

接下來, 我們需要建立文字視圖的輸出, 以便我們可以從程式碼調整其內容。 在 [**助理編輯器**] 中選取`documentText` **ViewController**檔案, 並建立名為的新插座:

[![建立插座](menu-images/maint20.png "建立插座")](menu-images/maint20-large.png#lightbox)

返回 Visual Studio for Mac 以同步處理 Xcode 中的變更。 接著, 編輯**ViewController.cs**檔案, 使其看起來如下所示:

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

這會將文字視圖的文字公開在`ViewController`類別之外, 並在視窗獲得或失去焦點時通知應用程式委派。 現在, 編輯**AppDelegate.cs**檔案, 使其看起來如下所示:

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

我們在這裡建立`AppDelegate`了一個部分類別, 讓我們可以使用我們在 Interface Builder 中定義的動作和輸出。 我們也會公開`textEditor` , 以追蹤目前正在專注的視窗。

下列方法可用來處理我們的自訂功能表和功能表項目:

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

現在, 如果我們執行應用程式, [**片語**] 功能表中的所有專案都會啟用, 並在選取時將 [提供] 片語新增至 [文字] 視圖:

![執行應用程式的範例](menu-images/maint21.png "執行應用程式的範例")

既然我們已經有使用 [應用程式] 功能表列的基本概念, 讓我們來看一下如何建立自訂的內容功能表。

### <a name="creating-menus-from-code"></a>從程式碼建立功能表

除了使用 Xcode 的 Interface Builder 來建立功能表和功能表項目之外, 您可能會有時 Xamarin. Mac 應用程式需要從程式碼建立、修改或移除功能表、子功能表或功能表項目。

在下列範例中, 會建立一個類別, 以保存將動態建立的功能表項目和子功能表的相關資訊:

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

#### <a name="adding-menus-and-items"></a>加入功能表和專案

定義此類別之後, 下列常式將會剖析`LanguageFormatCommand`物件的集合, 並以遞迴方式建立新的功能表和功能表項目, 方法是將它們附加至已傳入的現有功能表底部 (在 Interface Builder 中建立):

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

對於任何`LanguageFormatCommand`具有空白`Title`屬性的物件, 此常式會在功能表區段之間建立一個 [**分隔符號] 功能表項目**(窄灰色線):

```csharp
menuItem = NSMenuItem.SeparatorItem;
```

如果有提供標題, 則會建立具有該標題的新功能表項目:

```csharp
menuItem = new NSMenuItem (command.Title);
``` 

如果物件包含子`LanguageFormatCommand`物件, 則會建立子`AssembleMenu`功能表, 並以遞迴方式呼叫方法來建立該功能表: `LanguageFormatCommand`

```csharp
menuItem.Submenu = new NSMenu (command.Title);
AssembleMenu (menuItem.Submenu, command.SubCommands);
```

對於沒有子功能表的任何新功能表項目, 會加入程式碼來處理使用者所選取的功能表項目:

```csharp
menuItem.Activated += (sender, e) => {
    // Do something when the menu item is selected
    ...
};
```

#### <a name="testing-the-menu-creation"></a>測試功能表建立

如果已建立下列`LanguageFormatCommand`物件集合, 則會將上述所有程式碼放在原處:

```csharp
// Define formatting commands
FormattingCommands.Add(new LanguageFormatCommand("Strong","**","**"));
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
FormattingCommands.Add(new LanguageFormatCommand("Image","![](",")"));
FormattingCommands.Add(new LanguageFormatCommand("Image Link","[![](",")](LinkImageHere)"));
```

而該集合會傳遞至`AssembleMenu`函式 (並將 [**格式**] 功能表設定為 [基底]), 將會建立下列動態功能表和功能表項目:

執行![中應用程式的新功能表項目]執行(menu-images/dynamic01.png "中應用程式的新功能表項目")

#### <a name="removing-menus-and-items"></a>移除功能表和專案

如果您需要從應用程式的使用者介面移除任何功能表或功能表項目, 您可以使用`RemoveItemAt` `NSMenu`類別的方法, 只要為它提供要移除之專案的以零為基底的索引即可。

例如, 若要移除上述常式所建立的功能表和功能表項目, 您可以使用下列程式碼:

```csharp
public void UnpopulateFormattingMenu(NSMenu menu) {

    // Remove any additional items
    for (int n = (int)menu.Count - 1; n > 4; --n) {
        menu.RemoveItemAt (n);
    }
}
```

在上述程式碼的案例中, 前四個功能表項目會建立在 Xcode 的 Interface Builder 中, 而贈品可在應用程式中使用, 因此不會動態移除它們。

<a name="Contextual_Menus" />

## <a name="contextual-menus"></a>內容功能表

當使用者以滑鼠右鍵按一下或按一下視窗中的專案時, 就會出現內容功能表。 根據預設, 內建于 macOS 中的數個 UI 專案已附加相關的內容功能表 (例如文字視圖)。 不過, 有時候我們會想要為我們新增至視窗的 UI 元素建立自己的自訂內容功能表。

讓我們在 Xcode 中編輯我們的**主要**分鏡腳本檔案, 並將**視窗**視窗加入我們的設計中, 將其**類別**設定為「NSPanel」, 在身分**識別偵測器**中, 將新的**助理**專案加入至 [**視窗]** 功能表, 並將其附加至新的使用**Show Segue**的視窗:

[![設定 segue 類型](menu-images/context01.png "設定 segue 類型")](menu-images/context01-large.png#lightbox)

讓我們執行下列動作:

1. 將**標籤**從 [連結**庫偵測器**] 拖曳至 [**面板**] 視窗, 並將其文字設為 "Property": 

    [![編輯標籤的值](menu-images/context03.png "編輯標籤的值")](menu-images/context03-large.png#lightbox)
2. 接著您可以拖曳 **功能表** 從 **程式庫偵測器** 拖曳至 檢視階層 和 重新命名其中三個預設功能表項目中的檢視控制器 **文件**，**文字** 並 **字型**:

    [![必要的功能表項目](menu-images/context02.png "必要的功能表項目")](menu-images/context02-large.png#lightbox)
3. 現在從 [**屬性] 標籤**拖曳至**功能表**上:

    [![拖曳以建立 segue](menu-images/context04.png "拖曳以建立 segue")](menu-images/context04-large.png#lightbox)
4. 從快顯對話方塊中，選取 **功能表** : 

    ![設定 segue 類型](menu-images/context05.png "設定 segue 類型")
5. 從身分**識別偵測器**, 將 View Controller 的類別設定為 "PanelViewController": 

    [![設定 segue 類別](menu-images/context10.png "設定 segue 類別")](menu-images/context10-large.png#lightbox)
6. 切換回 Visual Studio for Mac 以進行同步處理, 然後回到 Interface Builder。
7. 切換至 [**助理編輯器**], 然後選取 [ **PanelViewController** ] 檔案。
8. 為 [**檔**] 功能表項目建立名`propertyDocument`為的動作: 

    [![正在設定動作](menu-images/context06.png "正在設定動作")](menu-images/context06-large.png#lightbox)
9. 重複建立其餘功能表項目的動作: 

    [![必要的動作](menu-images/context07.png "必要的動作")](menu-images/context07-large.png#lightbox)
10. 最後, 建立名`propertyLabel`為之**屬性標籤**的插座: 

    [設定![插座]設定(menu-images/context08.png "插座")](menu-images/context08-large.png#lightbox)
11. 儲存您的變更, 並返回 Visual Studio for Mac 以與 Xcode 同步。

編輯**PanelViewController.cs**檔案, 並新增下列程式碼:

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

現在, 如果我們執行應用程式, 並以滑鼠右鍵按一下面板中的屬性標籤, 我們會看到自訂的內容功能表。 如果我們從功能表中選取和專案, 標籤的值將會變更:

![正在執行的內容功能表](menu-images/context09.png "正在執行的內容功能表")

接下來, 我們來看一下建立狀態列功能表。

## <a name="status-bar-menus"></a>狀態列功能表

狀態列功能表會顯示狀態功能表項目的集合, 這些專案可提供與使用者的互動或意見反應, 例如會反映應用程式狀態的功能表或影像。 即使應用程式是在背景中執行, 應用程式的狀態列功能表也會啟用和作用中。 全系統狀態列位於應用程式功能表列的右側, 而且是目前在 macOS 中唯一可用的狀態列。

讓我們編輯**AppDelegate.cs**檔案, 讓`DidFinishLaunching`方法看起來如下所示:

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

`NSStatusBar statusBar = NSStatusBar.SystemStatusBar;`讓我們能夠存取全系統的狀態列。 `var item = statusBar.CreateStatusItem (NSStatusItemLength.Variable);`建立新的狀態列專案。 從該處我們建立功能表和功能表項目數目，並將功能表附加至我們剛剛建立的狀態列項目。 

如果我們執行應用程式, 就會顯示新的狀態列專案。 從功能表中選取專案, 將會變更文字視圖中的文字: 

![執行 [狀態] 列功能表](menu-images/statusbar01.png "執行 [狀態] 列功能表")

接下來, 讓我們看看如何建立自訂的停駐功能表項目。

## <a name="custom-dock-menus"></a>自訂停駐功能表

當使用者以滑鼠右鍵按一下或控制按一下 dock 中的應用程式圖示時, 您的 Mac 應用程式會顯示 [dock] 功能表:

![自訂停駐功能表](menu-images/dock01.png "自訂停駐功能表")

讓我們藉由執行下列動作, 為應用程式建立自訂的 dock 功能表:

1. 在 Visual Studio for Mac 中, 以滑鼠右鍵按一下應用程式的專案,  > 然後選取 [**新增檔案 ...** ]從 [新增檔案] 對話方塊中, 選取 [ **Xamarin**  > ] [**空白介面定義**], 使用 "DockMenu" 作為**名稱**, 然後按一下 [**新增**] 按鈕以建立新的**DockMenu xib**檔案:

    ![加入空的介面定義](menu-images/dock02.png "加入空的介面定義")
2. 在  **Solution Pad**中, 按兩下**xib**檔案, 將它開啟, 以便在 Xcode 中進行編輯。 建立新 **功能表** 與下列項目：**Address**、 **Date**、**問候語**和**Signature** 

    [配置![UI]配置(menu-images/dock03.png "UI")](menu-images/dock03-large.png#lightbox)
3. 接下來, 讓我們將新的功能表項目連接到我們在上方 [[新增、編輯和刪除功能表](#Adding,_Editing_and_Deleting_Menus)] 區段中為自訂功能表所建立的現有動作。 切換至**連接偵測器**, 然後選取**介面**階層中的**第一個回應**程式。 向下滾動並尋找`phraseAddress:`動作。 從該動作的圓形中, 將線條拖曳至 [**位址**] 功能表項目:

    [![拖曳以連接動作](menu-images/dock04.png "拖曳以連接動作")](menu-images/dock04-large.png#lightbox)
4. 針對所有其他功能表項目重複執行, 將其附加至對應的動作: 

    [![必要的動作](menu-images/dock05.png "必要的動作")](menu-images/dock05-large.png#lightbox)
5. 接下來, 選取**介面**階層中的**應用程式**。 在 **連線偵測器**，將圓形線條`dockMenu`插座上我們剛剛建立的功能表：

    [將連接![向上拖曳至插座]將連接(menu-images/dock06.png "向上拖曳至插座")](menu-images/dock06-large.png#lightbox)
6. 儲存您的變更, 並切換回 Visual Studio for Mac 以與 Xcode 同步。
7. 按兩下**plist**檔案以開啟它進行編輯: 

    [![編輯 Info.plist 檔案](menu-images/dock07.png "編輯 Info.plist 檔案")](menu-images/dock07-large.png#lightbox)
8. 按一下畫面底部的 [**來源**] 索引標籤: 

    [![選取來源視圖](menu-images/dock08.png "選取來源視圖")](menu-images/dock08-large.png#lightbox)
9. 按一下 [**新增專案**], 按一下綠色的加號按鈕, 將屬性名稱設為 "AppleDockMenu", 並將值設定為 "DockMenu" (不含副檔名的新 xib 檔案名稱): 

    [![新增 DockMenu 專案](menu-images/dock09.png "新增 DockMenu 專案")](menu-images/dock09-large.png#lightbox)

現在, 如果我們執行應用程式, 並在 Dock 中以滑鼠右鍵按一下其圖示, 就會顯示新的功能表項目:

![執行的 dock 功能表範例](menu-images/dock10.png "執行的 dock 功能表範例")

如果我們從功能表中選取其中一個自訂專案, 將會修改文字視圖中的文字。

<a name="Pop-up_Menus_and_Pull-Down_Lists" />

## <a name="pop-up-button-and-pull-down-lists"></a>快顯功能表和下拉式清單

快顯按鈕會顯示選取的專案, 並提供使用者按下時要選取的選項清單。 下拉式清單是一種彈出式按鈕, 通常用來選取目前工作內容的特定命令。 這兩者都可以出現在視窗中的任何位置。

讓我們藉由執行下列動作, 為應用程式建立自訂的快顯按鈕:

1. 編輯 Xcode 中的**主要**分鏡腳本檔案, 並將**快捷方式按鈕**從程式庫偵測**器**拖曳至我們在內容[功能表](#Contextual_Menus)一節中建立的**面板**視窗: 

    [![加入快捷方式按鈕](menu-images/popup01.png "加入快捷方式按鈕")](menu-images/popup01-large.png#lightbox)
2. 加入新的功能表項目, 並將快顯視窗中專案的標題設定為:**Address**、 **Date**、**問候語**和**Signature** 

    [設定![功能表項目]設定(menu-images/popup02.png "功能表項目")](menu-images/popup02-large.png#lightbox)
3. 接下來, 讓我們將新的功能表項目連接到在上方 [[新增]、[編輯] 和 [刪除] 功能表](#Adding,_Editing_and_Deleting_Menus)一節中, 為自訂功能表所建立的現有動作。 切換至**連接偵測器**, 然後選取**介面**階層中的**第一個回應**程式。 向下滾動並尋找`phraseAddress:`動作。 從該動作的圓形中, 將線條拖曳至 [**位址**] 功能表項目: 

    [![拖曳以連接動作](menu-images/popup03.png "拖曳以連接動作")](menu-images/popup03-large.png#lightbox)
4. 針對所有其他功能表項目重複執行, 將其附加至對應的動作: 

    [![所有必要的動作](menu-images/popup04.png "所有必要的動作")](menu-images/popup04-large.png#lightbox)
5. 儲存您的變更, 並切換回 Visual Studio for Mac 以與 Xcode 同步。

現在, 如果我們執行應用程式, 並從快顯視窗中選取一個專案, 則文字視圖中的文字將會變更:

![執行]快顯的範例(menu-images/popup05.png "執行")快顯的範例

您可以使用與快顯按鈕完全相同的方式來建立和使用下拉式清單。 除了附加至現有的動作之外, 您也可以建立自己的自訂動作, 就像我們在[內容功能表](#Contextual_Menus)一節中的操作功能表一樣。

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用功能表和功能表項目。 首先, 我們檢查過應用程式的功能表列, 然後看一下建立關聯式功能表, 接著我們檢查了狀態列功能表和自訂的停駐功能表。 最後, 我們涵蓋了快顯功能表和下拉式清單。


## <a name="related-links"></a>相關連結

- [MacMenus (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macmenus)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [人力介面指導方針-功能表](https://developer.apple.com/macos/human-interface-guidelines/menus/menu-anatomy/)
- [應用程式功能表和快顯清單簡介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/MenuList/MenuList.html)
