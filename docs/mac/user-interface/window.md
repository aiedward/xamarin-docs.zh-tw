---
title: Xamarin 中的 Windows
description: 本文說明如何在 Xamarin. Mac 應用程式中使用 windows 和麵板。 其中說明如何在 Xcode 和 Interface Builder 中建立 windows 和麵板, 從分鏡腳本和 xib 檔案載入, 並以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6d766e74f99e3c69259a41ce13501de80cf0231a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653108"
---
# <a name="windows-in-xamarinmac"></a>Xamarin 中的 Windows

_本文說明如何在 Xamarin. Mac 應用程式中使用 windows 和麵板。其中說明如何在 Xcode 和 Interface Builder 中建立 windows 和麵板, 從分鏡腳本和 xib 檔案載入, 並以程式設計方式使用它們。_

在 Xamarin. C# Mac 應用程式中使用和 .net 時, 您可以存取開發人員在*目標-C*和*Xcode*中工作的相同視窗和麵板。 因為 Xamarin 會直接與 Xcode 整合, 所以您可以使用 Xcode 的_Interface Builder_來建立和維護您的 Windows 和麵板 (或選擇直接在程式C#代碼中建立)。

根據其用途, Xamarin 應用程式可以在畫面上顯示一或多個視窗, 以管理和協調其所顯示的資訊並搭配使用。 視窗的主要功能包括:

1. 提供可放置和管理檢視和控制項的區域。
2. 接受和回應事件, 以回應使用者與鍵盤和滑鼠的互動。

Windows 可以用於非模式狀態 (例如可以同時開啟多個檔的文字編輯器) 或強制回應 (例如, 必須先關閉才能繼續應用程式的 [匯出] 對話方塊)。

面板是一種特殊的視窗 (基類`NSWindow`的子類別), 通常會在應用程式中提供輔助函式, 例如文字格式檢查程式和系統色彩選擇器等公用程式視窗。

[![](window-images/intro01.png "在 Xcode 中編輯視窗")](window-images/intro01.png#lightbox)

在本文中, 我們將討論在 Xamarin. Mac 應用程式中使用 Windows 和麵板的基本概念。 強烈建議您先流覽[Hello, Mac](~/mac/get-started/hello-mac.md)文章, 特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介, 其中涵蓋了我們將在中使用的重要概念和技巧。本文。

您可能想要查看[Xamarin. Mac 內部](~/mac/internals/how-it-works.md)檔`Register`的將[類別/方法公開C#至目標-C](~/mac/internals/how-it-works.md)一節, 它會說明用來將C#類別連線到`Export`的和命令。目標-C 物件和 UI 元素。

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Windows 簡介

如上面所述, 視窗會提供一個區域, 讓您可以放置和管理檢視和控制項, 並根據使用者互動 (透過鍵盤或滑鼠) 來回應事件。

根據 Apple, macOS 應用程式中有五種主要的視窗類型:

- **文件視窗**-文件視窗包含以檔案為基礎的使用者資料, 例如試算表或文字檔。
- **應用程式視窗**-應用程式視窗是不是以檔為基礎之應用程式的主視窗 (例如 Mac 上的行事曆應用程式)。
- **面板**-面板會浮動在其他視窗上方, 並提供使用者可在開啟檔時使用的工具或控制項。 在某些情況下, 面板可以是半透明的 (例如使用大型圖形時)。
- **對話方塊**-回應使用者動作時會出現一個對話方塊, 而且通常會提供使用者完成動作的方式。 對話方塊需要使用者的回應, 才能關閉。 (請參閱[使用對話方塊](~/mac/user-interface/dialog.md))
- **警示**-警示是一種特殊類型的對話方塊, 會在發生嚴重問題 (例如錯誤) 或警告 (例如準備刪除檔案) 時出現。 因為警示是對話方塊, 所以它也需要使用者回應才能關閉。 (請參閱[使用警示](~/mac/user-interface/alert.md))

如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[關於 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)一節。

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>主要、金鑰和非使用中的視窗

Xamarin. Mac 應用程式中的 Windows 可以根據使用者目前與其互動的方式來查看和行為不同。 目前著重于使用者注意的最重要檔或應用程式視窗稱為_主要視窗_。 在大部分的情況下, 此視窗也會是索引_鍵視窗_(目前接受使用者輸入的視窗)。 但這不一定是這樣的情況, 例如, 色彩選擇器可以開啟, 而是使用者與之互動的關鍵視窗, 以變更文件視窗中專案的狀態 (這仍然是主視窗)。

主要和主要視窗 (如果不同) 一律為使用中, 非作用中_視窗_是開啟的視窗, 不在前景。 例如, 文字編輯器應用程式可能會一次開啟一個以上的檔, 只有主視窗會處於作用中狀態, 其他則不會使用。 

如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[關於 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)一節。

<a name="Naming_Windows" />

### <a name="naming-windows"></a>命名視窗

視窗可以顯示標題列, 而當標題顯示時, 通常是應用程式的名稱、正在處理的檔案名稱或視窗的功能 (例如偵測器)。 有些應用程式不會顯示標題列, 因為它們可以透過可見方式辨識, 而且不會與檔搭配使用。

Apple 建議下列指導方針:

- 使用您的應用程式名稱作為主要非文件視窗的標題。 
- 命名新的文件視窗`untitled`。 針對第一個新檔, 請勿在標題後面加上數位 (例如`untitled 1`)。 如果使用者在儲存前先建立另一份新檔並將其標題為`untitled 2`第`untitled 3`一個, 請呼叫該視窗、等。

如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[命名 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1)一節。

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>全螢幕視窗

在 macOS 中, 應用程式的視窗可以全螢幕隱藏所有專案, 包括應用程式功能表列 (可透過將游標移至畫面頂端來顯示), 以提供與它內容的自由互動。

Apple 建議下列指導方針:

- 判斷是否適合讓視窗變成全螢幕。 提供短暫互動的應用程式 (例如計算機) 不應該提供全螢幕模式。
- 如果全螢幕工作需要, 則顯示工具列。 在全螢幕模式中, 通常會隱藏工具列。
- 全螢幕視窗應該具有使用者完成工作所需的所有功能。
- 可能的話, 請避免使用者在全螢幕視窗中的搜尋工具互動。
- 利用增加的螢幕空間, 而不將焦點從主要工作轉移出來。

如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[全螢幕視窗](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1)一節。

<a name="Panels" />

### <a name="panels"></a>面板

「面板」是一個次要視窗, 其中包含影響現用檔或選取範圍的控制項和選項 (例如系統色彩選擇器):

[![](window-images/panel01.png "色彩面板")](window-images/panel01.png#lightbox)

面板可以是_應用程式特定_或全_系統_的。 應用程式特定的面板會浮動在應用程式文件視窗的頂端, 並在應用程式處於背景時消失。 全系統面板 (例如 [字型] 面板), 不論應用程式如何, 都能在所有開啟的視窗上浮動。 

Apple 建議下列指導方針:

- 一般而言, 使用標準面板時, 透明面板應該只會謹慎使用, 並用於以圖形方式耗用的工作。
- 請考慮使用面板, 讓使用者能夠輕鬆存取直接影響其工作的重要控制項或資訊。
- 視需要隱藏和顯示面板。
- 面板應一律包含標題列。
- 面板不應包含作用中的最小化按鈕。

#### <a name="inspectors"></a>偵測器

大部分的新式 macOS 應用程式都有輔助控制項和選項, 會影響作用中的檔或選取專案, 作為主要視窗中的偵測器 (如下面所示的**頁面**應用程式), 而不是使用面板視窗:

[![](window-images/panel02.png "範例偵測器")](window-images/panel02.png#lightbox)

如需詳細資訊, 請參閱 Apple [OS X 人體介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)一節, 以及我們的[MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector)範例應用程式, 以取得 Xamarin. Mac 應用程式中的偵測**器介面**完整執行。

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>在 Xcode 中建立和維護視窗

當您建立新的 Xamarin Cocoa 應用程式時, 預設會取得標準的空白視窗。 此視窗會在專案中`.storyboard`自動包含的檔案中定義。 若要編輯您的 windows 設計, 請在 **方案總管**中`Main.storyboard`按兩下該檔案:

[![](window-images/edit01.png "選取主要分鏡腳本")](window-images/edit01.png#lightbox)

這會在 Xcode 的 Interface Builder 中開啟視窗設計:

[![](window-images/edit02.png "在 Xcode 中編輯 UI")](window-images/edit02.png#lightbox)

在**屬性偵測器**中, 有數個屬性可供您用來定義和控制視窗:

- **標題**-這是將顯示在視窗標題列中的文字。
- **自動**儲存-這是當視窗的位置和設定自動儲存時, 將用來為其設置識別碼的索引_鍵_。
- **標題列**-視窗會顯示標題列。
- **統一標題和工具列**-如果視窗包含工具列, 則應該是標題列的一部分。
- **完整大小的內容視圖**-允許視窗的內容區域位於標題列底下。
- **Shadow** -視窗是否有陰影。
- **紋理**紋理的 windows 可以使用效果 (例如 vibrancy), 而且可以在其主體上的任何位置拖曳來四處移動。
- **關閉**-視窗具有 [關閉] 按鈕。
- **最小化**-視窗有最小化按鈕。
- 重**設大小**-視窗具有調整大小控制項。
- **工具列按鈕**-視窗具有 [隱藏]/[顯示] 工具列按鈕。
- 可**還原**-是自動儲存和還原視窗的位置和設定。
- **在啟動時顯示**-這是載入檔案時`.xib`自動顯示的視窗。
- **停用時隱藏**-這是應用程式進入背景時隱藏的視窗。
- **關閉時釋放**-這是關閉時從記憶體中清除的視窗。
- **一律顯示工具提示**-經常顯示工具提示。
- 重新**計算視圖迴圈**-這是在繪製視窗之前重新計算的視圖順序。
- **空格**、 **Exposé**和**迴圈**-All 會定義視窗在那些 macOS 環境中的行為。 
- **全螢幕**-決定此視窗是否可以進入全螢幕模式。 
- **動畫**-控制可用於視窗的動畫類型。
- **外觀**-控制視窗的外觀。 現在只有一個外觀, 也就是淺綠色。

如需詳細資訊, 請參閱 Apple 的[Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)和[NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow)檔。

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>設定預設大小和位置

若要設定視窗的初始位置, 並控制其大小, 請切換至 [大小] 偵測**器**:

[![](window-images/edit07.png "預設大小和位置")](window-images/edit07.png#lightbox)

您可以從這裡設定視窗的初始大小、提供最小和最大大小、設定畫面上的初始位置, 以及控制視窗周圍的框線。

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>設定自訂主視窗控制器

為了能夠建立輸出和動作將 UI 元素公開給C#程式碼, Xamarin 應用程式必須使用自訂的視窗控制器。

請執行下列動作：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的腳本。
2. `NSWindowController`在 Design Surface 中選取。
3. 切換至 [身分**識別偵測器**] `WindowController`視圖, 並輸入作為 [**類別名稱**]: 

    [![](window-images/windowcontroller01.png "設定類別名稱")](window-images/windowcontroller01.png#lightbox)
4. 儲存您的變更, 並返回 Visual Studio for Mac 以進行同步處理。
5. 檔案會新增至您在 方案總管中的專案 Visual Studio for Mac: `WindowController.cs` 

    [![](window-images/windowcontroller02.png "選取 windows 控制器")](window-images/windowcontroller02.png#lightbox)
6. 在 Xcode 的 Interface Builder 中重新開啟分鏡腳本。
7. `WindowController.h`檔案將可供使用: 

    [![](window-images/windowcontroller03.png "編輯 WindowController 檔")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>新增 UI 元素

若要定義視窗的內容, 請將控制項從 [程式庫] 偵測**器**拖曳至 [**介面編輯器**]。 如需有關使用 Interface Builder 來建立及啟用控制項的詳細資訊, 請參閱我們的[Xcode 和 Interface Builder 檔簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)。

例如, 讓我們將工具列從連結**庫偵測器**拖曳至**介面編輯器**中的視窗:

[![](window-images/edit03.png "從程式庫選取工具列")](window-images/edit03.png#lightbox)

接下來, 拖曳**文本視圖**並調整其大小, 以填滿工具列底下的區域:

[![](window-images/edit04.png "加入文字視圖")](window-images/edit04.png#lightbox)

因為我們想要在視窗的大小變更時縮小並成長**文本視圖**, 所以讓我們切換到 [**條件約束編輯器**], 並新增下列條件約束:

[![](window-images/edit05.png "編輯條件約束")](window-images/edit05.png#lightbox)

藉由按一下編輯器頂端的 [for **Red 字形狀**], 然後按一下 [**加入4個條件約束**], 我們會告訴文字視圖在調整視窗大小時, 水準和垂直地停留在指定的 X、Y 座標和放大或縮小。

最後, 讓我們使用**插座**將`ViewController.h` **文本視圖**公開至程式碼 (請務必選取檔案):

[![](window-images/edit06.png "設定插座")](window-images/edit06.png#lightbox)

儲存您的變更, 並切換回 Visual Studio for Mac 以與 Xcode 同步。

如需使用**輸出**和**動作**的詳細資訊, 請參閱我們的[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)檔。

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>標準視窗工作流程

針對您在 Xamarin. Mac 應用程式中建立及使用的任何視窗, 其程式基本上與先前所做的一樣:

1. 針對不是自動新增至專案的新視窗, 請將新的視窗定義加入至專案。 下面將詳細討論這一點。
1. `Main.storyboard`按兩下檔案以開啟視窗設計, 以在 Xcode 的 Interface Builder 中進行編輯。
1. 將新的視窗拖曳至使用者介面的設計, 並使用_segue_將視窗連結到主視窗 (如需詳細資訊, 請參閱[使用分鏡](~/mac/platform/storyboards/indepth.md)指令檔的[segue](~/mac/platform/storyboards/indepth.md#Segues)一節)。
1. 在 [**屬性偵測器**] 和 [**大小偵測器**] 中設定任何必要的視窗屬性。
1. 拖曳所需的控制項, 以建立您的介面, 並在**屬性偵測器**中進行設定。
1. 使用 [**大小**] 偵測器來處理 UI 元素的調整大小。
1. 透過**輸出**和**動作**, 將視窗C#的 UI 元素公開至程式碼。
1. 儲存您的變更, 並切換回 Visual Studio for Mac 以與 Xcode 同步。

既然我們已經建立了基本的視窗, 我們將探討 Xamarin 應用程式在使用 windows 時的一般進程。 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>顯示預設視窗

根據預設, 新的 Xamarin. Mac 應用程式會在檔案啟動時, 自動`MainWindow.xib`顯示在檔案中定義的視窗:

[![](window-images/display01.png "執行的範例視窗")](window-images/display01.png#lightbox)

由於我們已修改上述視窗的設計, 因此現在會包含預設的工具列和**文字視圖**控制項。 檔案中`Info.plist`的下列區段會負責顯示此視窗:

[![](window-images/display00.png "編輯資訊。 plist")](window-images/display00.png#lightbox)

**主要介面**下拉式清單是用來選取將用來做為主要應用程式 UI (在此案例`Main.storyboard`中為) 的分鏡腳本。

視圖控制器會自動加入至專案, 以控制所顯示的主視窗 (連同其主要視圖)。 它會在檔案中`ViewController.cs`定義, 並在身分**識別偵測器**下的 Interface Builder 中附加至檔案**的擁有**者:

[![](window-images/display02.png "設定檔案的擁有者")](window-images/display02.png#lightbox)

在我們的視窗中, 我們希望它在第一次`untitled`開啟時具有標題, 讓我們覆`ViewWillAppear`寫中`ViewController.cs`的方法, 如下所示:

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> 我們會`Title` `ViewWillAppear`在方法中設定視窗的屬性值, 而不是`ViewDidLoad`方法, 因為雖然此視圖可能會載入記憶體中, 但尚未完全具現化。 如果我們嘗試存取`Title` `ViewDidLoad`方法中的屬性, 我們會收到例外狀況,因為視窗尚未經過結構化,而且尚未連接至屬性。`null`

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>以程式設計方式關閉視窗

有時候您可能想要以程式設計方式關閉 Xamarin. Mac 應用程式中的視窗, 而不是讓使用者按一下視窗的 [**關閉**] 按鈕或使用功能表項目。 macOS 提供兩種不同的方式, `NSWindow`以程式`PerformClose`設計`Close`方式關閉: 和。

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

呼叫的`NSWindow`方法會模擬使用者按一下視窗的 [關閉] 按鈕, 方法是暫時反白顯示按鈕, 然後關閉視窗。 `PerformClose`

如果應用程式`NSWindow`會執行的`WillClose`事件, 則會在關閉視窗之前引發。 如果事件`false`傳回, 則不會關閉視窗。 如果視窗沒有 [**關閉**] 按鈕, 或因為任何原因而無法關閉, 作業系統就會發出警示音效。

例如：

```csharp
MyWindow.PerformClose(this);
```

會嘗試關閉`MyWindow` `NSWindow`實例。 如果成功, 視窗將會關閉, 否則會發出警示音效, 而且會保持開啟狀態。

<a name="Close" />

### <a name="close"></a>關閉

呼叫的`NSWindow`方法並不會模擬使用者按一下視窗的 [關閉] 按鈕, 只要將按鈕反白顯示, 它就會關閉視窗。 `Close`

視窗不一定要關閉, 而且`NSWindowWillCloseNotification`系統會將通知張貼到要關閉之視窗的預設通知中心。

方法與`PerformClose`方法有兩個重要的方式不同: `Close`

1. 它不會嘗試引發`WillClose`事件。
2. 它不會以反白顯示按鈕的方式, 模擬使用者按一下 [**關閉**] 按鈕。

例如：

```csharp
MyWindow.Close();
```

會關閉`MyWindow` `NSWindow`實例。

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>修改過的 Windows 內容

在 macOS 中, Apple 提供了一種方式, 通知使用者視窗 (`NSWindow`) 的內容已由使用者修改, 需要儲存。 如果視窗包含修改過的內容, 就會在其 [**關閉**] widget 中顯示一個小的黑點:

[![](window-images/close01.png "具有已修改標記的視窗")](window-images/close01.png#lightbox)

如果使用者嘗試在有未儲存的視窗內容變更時關閉視窗或結束 Mac 應用程式, 您應該顯示[對話方塊](~/mac/user-interface/dialog.md)或強制回應[表](~/mac/user-interface/dialog.md), 並允許使用者先儲存變更:

[![](window-images/close02.png "視窗關閉時顯示的儲存工作表")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>將視窗標示為已修改

若要將視窗標示為已修改的內容, 請使用下列程式碼:

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

儲存變更之後, 請使用來清除已修改的旗標:

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>在關閉視窗前儲存變更

若要監看使用者是否關閉視窗, 並讓他們事先儲存修改過的內容, 您必須建立的`NSWindowDelegate`子類別, 並覆寫其`WindowShouldClose`方法。 例如：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWindowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            // is the window dirty?
            if (Window.DocumentEdited) {
                var alert = new NSAlert () {
                    AlertStyle = NSAlertStyle.Critical,
                    InformativeText = "Save changes to document before closing window?",
                    MessageText = "Save Document",
                };
                alert.AddButton ("Save");
                alert.AddButton ("Lose Changes");
                alert.AddButton ("Cancel");
                var result = alert.RunSheetModal (Window);

                // Take action based on result
                switch (result) {
                case 1000:
                    // Grab controller
                    var viewController = Window.ContentViewController as ViewController;

                    // Already saved?
                    if (Window.RepresentedUrl != null) {
                        var path = Window.RepresentedUrl.Path;

                        // Save changes to file
                        File.WriteAllText (path, viewController.Text);
                        return true;
                    } else {
                        var dlg = new NSSavePanel ();
                        dlg.Title = "Save Document";
                        dlg.BeginSheet (Window, (rslt) => {
                            // File selected?
                            if (rslt == 1) {
                                var path = dlg.Url.Path;
                                File.WriteAllText (path, viewController.Text);
                                Window.DocumentEdited = false;
                                viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
                                viewController.View.Window.RepresentedUrl = dlg.Url;
                                Window.Close();
                            }
                        });
                        return true;
                    }
                    return false;
                case 1001:
                    // Lose Changes
                    return true;
                case 1002:
                    // Cancel
                    return false;
                }
            }

            return true;
        }
        #endregion
    }
}
```

使用下列程式碼, 將此委派的實例附加至您的視窗:

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>在關閉應用程式前儲存變更

最後, 您的 Xamarin 應用程式應該檢查是否有任何 Windows 包含修改過的內容, 並允許使用者在結束前儲存變更。 若要這麼做, 請`AppDelegate.cs`編輯您的`ApplicationShouldTerminate`檔案, 覆寫方法, 使其看起來如下所示:

```csharp
public override NSApplicationTerminateReply ApplicationShouldTerminate (NSApplication sender)
{
    // See if any window needs to be saved first
    foreach (NSWindow window in NSApplication.SharedApplication.Windows) {
        if (window.Delegate != null && !window.Delegate.WindowShouldClose (this)) {
            // Did the window terminate the close?
            return NSApplicationTerminateReply.Cancel;
        }
    }

    // Allow normal termination
    return NSApplicationTerminateReply.Now;
}
```

<a name="Working_with_Multiple_Windows" />

## <a name="working-with-multiple-windows"></a>使用多個視窗

大部分以檔為基礎的 Mac 應用程式都可以同時編輯多個檔。 例如, 文字編輯器可以同時開啟多個文字檔來進行編輯。 根據預設, 我們的新 Xamarin 應用程式會有一個 [檔案] 功能表, 其中**新**的專案會自動`newDocument:`連接到**動作**。

我們即將啟動這個新專案, 並允許使用者開啟主視窗的多個複本, 一次編輯多份檔。

讓我們編輯`AppDelegate.cs`檔案, 並新增下列計算屬性:

```csharp
public int UntitledWindowCount { get; set;} =1;
```

我們會使用此資訊來追蹤未儲存的檔案數目, 讓我們可以將意見反應提供給使用者 (如上面所述的每一 Apple 指導方針)。

接下來, 新增下列方法:

```csharp
[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

這段程式碼會建立視窗控制器的新版本、載入新視窗、使其成為主要和金鑰視窗, 並設定它的標題。 現在, 如果我們執行應用程式, 然後從 [檔案 ] 功能表中選取 [**新增**], 就會開啟並顯示新的編輯器視窗:

[![](window-images/display04.png "已新增新的未命名視窗")](window-images/display04.png#lightbox)

如果開啟 [ **windows** ] 功能表, 您可以看到應用程式會自動追蹤並處理我們開啟的視窗:

[![](window-images/display05.png "Windows 功能表")](window-images/display05.png#lightbox)

如需在 Xamarin. Mac 應用程式中使用功能表的詳細資訊, 請參閱我們[的使用功能表](~/mac/user-interface/menu.md)檔。

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>取得目前使用中視窗

在可以開啟多個視窗 (檔) 的 Xamarin 應用程式中, 有時您需要取得最新的最上層視窗 ([金鑰] 視窗)。 下列程式碼會傳回索引鍵視窗:

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

您可以在任何需要存取目前索引鍵視窗的類別或方法中呼叫它。 如果目前沒有開啟視窗, 則會`null`傳回。

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>存取所有應用程式視窗

有時候您可能需要存取目前已開啟的 Xamarin. Mac 應用程式的所有視窗。 例如, 若要查看使用者想要開啟的檔案是否已在現有視窗中開啟。

會維護一個`Windows`屬性, 其中包含應用程式中所有已開啟視窗的陣列。 `NSApplication.SharedApplication` 您可以逐一查看此陣列, 以存取所有應用程式目前的視窗。 例如：

```csharp
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

在範例程式碼中, 我們會將每個傳回的`ViewController`視窗轉換成應用程式中的自訂類別, 並`Path`針對使用者想要開啟的檔案路徑, 測試自訂屬性的值。 如果檔案已開啟, 我們會將該視窗帶入前端。

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>在程式碼中調整視窗大小

有時候, 應用程式需要在程式碼中調整視窗大小。 若要調整視窗的大小和位置, 您可以`Frame`調整它的屬性。 調整視窗的大小時, 您通常也需要調整它的原點, 將視窗保留在相同的位置, 因為 macOS 的座標系統。

不同于 iOS 的左上角代表 (0, 0), macOS 使用做數學座標系統, 螢幕的左下角代表 (0, 0)。 在 iOS 中, 座標會隨著您向右移動而增加。 在 macOS 中, 座標會在值向上增加到右邊。 

下列範例程式碼會調整視窗大小:

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> 當您在程式碼中調整 windows 大小和位置時, 您必須確定您已遵守 Interface Builder 中所設定的最小和最大大小。 這不會自動接受, 而且您可以讓視窗放大或小於這些限制。

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>監看視窗大小變更

有時候, 您可能需要在 Xamarin Mac 應用程式內監看視窗大小的變更。 例如, 重繪內容以符合新的大小。

若要監視大小變更, 請先確定您已在 Xcode 的 Interface Builder 中為視窗控制器指派自訂類別。 例如, `MasterWindowController`在下列內容中:

[![](window-images/resize01.png "身分識別偵測器")](window-images/resize01.png#lightbox)

接下來, 編輯自訂視窗控制器類別, 並`DidResize`監視控制器視窗上的事件, 以接收即時大小變更的通知。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

(選擇性) 您可以使用`DidEndLiveResize`事件, 只在使用者完成變更視窗的大小後收到通知。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

        Window.DidEndLiveResize += (sender, e) => {
        // Do something after the user's finished resizing
        // the window
    };
}
```

<a name="Setting_a_Window’s_Title_and_Represented_File" />

## <a name="setting-a-windows-title-and-represented-file"></a>設定視窗的標題和表示檔案

當使用代表檔的視窗時, `NSWindow`有一個`DocumentEdited`屬性, 如果設定為`true`在 [關閉] 按鈕中顯示一個小點, 讓使用者知道檔案已經修改, 應該在關閉前儲存。

讓我們編輯`ViewController.cs`檔案, 並進行下列變更:

```csharp
public bool DocumentEdited {
    get { return View.Window.DocumentEdited; }
    set { View.Window.DocumentEdited = value; }
}
...

public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";

    View.Window.WillClose += (sender, e) => {
        // is the window dirty?
        if (DocumentEdited) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to give the user the ability to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    };
}

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Show when the document is edited
    DocumentEditor.TextDidChange += (sender, e) => {
        // Mark the document as dirty
        DocumentEdited = true;
    };

    // Overriding this delegate is required to monitor the TextDidChange event
    DocumentEditor.ShouldChangeTextInRanges += (NSTextView view, NSValue[] values, string[] replacements) => {
        return true;
    };

}
```

我們也會監看視窗`WillClose`上的事件, 並檢查`DocumentEdited`屬性的狀態。 如果是`true` , 我們必須讓使用者能夠將變更儲存至檔案。 如果我們執行應用程式並輸入一些文字, 則會顯示點:

[![](window-images/file01.png "已變更的視窗")](window-images/file01.png#lightbox)

如果我們嘗試關閉視窗, 就會收到警示:

[![](window-images/file02.png "顯示儲存對話方塊")](window-images/file02.png#lightbox)

如果我們從檔案載入檔, 我們可以使用`window.SetTitleWithRepresentedFilename (Path.GetFileName(path));`方法, 將視窗的標題設定為檔案的名稱 ( `path`指定的字串代表要開啟的檔案)。 此外, 我們也可以使用`window.RepresentedUrl = url;`方法來設定檔案的 URL。

如果 URL 指向 OS 所知的檔案類型, 則它的圖示會顯示在標題列中。 如果使用者以滑鼠右鍵按一下圖示, 將會顯示檔案的路徑。

讓我們編輯`AppDelegate.cs`檔案, 並新增下列方法:

```csharp
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
            var path = url.Path;

            // Get new window
            var storyboard = NSStoryboard.FromName ("Main", null);
            var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

            // Display
            controller.ShowWindow(this);

            // Load the text into the window
            var viewController = controller.Window.ContentViewController as ViewController;
            viewController.Text = File.ReadAllText(path);
                    viewController.View.Window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
            viewController.View.Window.RepresentedUrl = url;

        }
    }
}
```

現在, 如果我們執行應用程式, 請從 [檔案] 功能表中選取 [**開啟 ...** ], 從 [**開啟**] 對話方塊中選取一個文字檔, 然後將它開啟:

[![](window-images/file03.png "開啟的對話方塊")](window-images/file03.png#lightbox)

將會顯示檔案, 並使用檔案的圖示來設定標題:

[![](window-images/file04.png "已載入檔案的內容")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>將新視窗加入至專案

除了主文件視窗外, Xamarin. Mac 應用程式可能需要向使用者顯示其他類型的 windows, 例如喜好設定或偵測器面板。

若要加入新的視窗, 請執行下列動作:

1. 在 **方案總管**中, 按兩下`Main.storyboard`檔案以開啟檔案, 以在 Xcode 的 Interface Builder 中進行編輯。
2. 從連結**庫**拖曳新的 **視窗控制器** , 並將它放在  **Design Surface**:

    [![](window-images/new01.png "在程式庫中選取新的視窗控制器")](window-images/new01.png#lightbox)
3. 在身分**識別偵測器**中`PreferencesWindow` , 為分鏡腳本**識別碼**輸入: 

    [![](window-images/new02.png "設定分鏡腳本識別碼")](window-images/new02.png#lightbox)
4. 設計您的介面: 

    [![](window-images/new03.png "設計 UI")](window-images/new03.png#lightbox)
5. 開啟應用程式功能表 (`MacWindows`), 選取 [**喜好設定 ...** ], 然後按一下並拖曳至新視窗: 

    [![](window-images/new05.png "建立 segue")](window-images/new05.png#lightbox)
6. 從快顯功能表中選取 [**顯示**]。
7. 儲存您的變更, 並返回 Visual Studio for Mac 以與 Xcode 同步。

如果我們執行程式碼, 並從 [**應用程式] 功能表**中選取 [**喜好設定 ...** ], 就會顯示視窗:

[![](window-images/new04.png "範例喜好設定功能表")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>使用面板

如同本文開頭所述, 面板會浮動在其他視窗上方, 並提供使用者可在開啟檔時使用的工具或控制項。 

就像您在 Xamarin. Mac 應用程式中建立及使用的任何其他類型的視窗一樣, 程式基本上是相同的:

1. 將新的視窗定義加入至專案。
2. `.xib`按兩下檔案以開啟視窗設計, 以在 Xcode 的 Interface Builder 中進行編輯。
3. 在 [**屬性偵測器**] 和 [**大小偵測器**] 中設定任何必要的視窗屬性。
4. 拖曳所需的控制項, 以建立您的介面, 並在**屬性偵測器**中進行設定。
5. 使用 [**大小**] 偵測器來處理 UI 元素的調整大小。
6. 透過**輸出**和**動作**, 將視窗C#的 UI 元素公開至程式碼。
7. 儲存您的變更, 並切換回 Visual Studio for Mac 以與 Xcode 同步。

在 [**屬性偵測器**] 中, 您有下列面板特有的選項:

[![](window-images/panel03.png "屬性偵測器")](window-images/panel03.png#lightbox)

- **樣式**-可讓您調整面板的樣式, 其方式如下:一般面板 (看起來像標準視窗)、公用程式面板 (具有較小的標題列)、抬頭顯示器面板 (是半透明的, 而且標題列是背景的一部分)。
- [**非啟用**]-在面板中決定成為金鑰視窗。
- **檔**強制回應-如果檔案模式, 面板只會浮動在應用程式的視窗上方, 否則會浮動在全部上方。


若要新增新的面板, 請執行下列動作:

1. 在 **方案總管**中, 以滑鼠右鍵按一下專案, 然後選取 **加入** > **新檔案 ...** 。
2. 在 [新增檔案] 對話方塊中,  > 選取**具有控制器的 [Xamarin Cocoa] 視窗**:

    [![](window-images/panels00.png "加入新的視窗控制器")](window-images/panels00.png#lightbox)
3. 輸入 `DocumentPanel` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. `DocumentPanel.xib`按兩下檔案將其開啟, 以在 Interface Builder 中進行編輯: 

    [![](window-images/new02.png "編輯面板")](window-images/new02.png#lightbox)
5. 從 [**介面編輯器**] 中的 [程式庫] 偵測**器**刪除現有的視窗並拖曳面板: 

    [![](window-images/panels01.png "刪除現有的視窗")](window-images/panels01.png#lightbox)
6. 將面板掛上至檔案**的擁有** - 者**視窗** - **插座**: 

    [![](window-images/panels02.png "拖曳以連接面板")](window-images/panels02.png#lightbox)
7. 切換至身分**識別偵測器**, 並將面板的類別`DocumentPanel`設定為: 

    [![](window-images/panels03.png "設定面板的類別")](window-images/panels03.png#lightbox)
8. 儲存您的變更, 並返回 Visual Studio for Mac 以與 Xcode 同步。
9. `DocumentPanel.cs`編輯檔案, 並將類別定義變更為下列內容: 

    `public partial class DocumentPanel : NSPanel`
10. 將變更儲存到檔案。

編輯檔案, `DidFinishLaunching`讓方法看起來如下所示: `AppDelegate.cs`

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

如果我們執行應用程式, 將會顯示面板:

[![](window-images/panels04.png "執行中應用程式的面板")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> Apple 已淘汰面板視窗, 應該以**偵測器介面**取代。 如需在 Xamarin. Mac 應用程式中建立偵測**器**的完整範例, 請參閱我們的[MacInspector](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector)範例應用程式。

<a name="Summary" />

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用 Windows 和麵板。 我們看到了各種不同的 Windows 和麵板類型和用途, 如何在 Xcode 的 Interface Builder 中建立和維護 Windows 和麵板, 以及如何在程式碼中C#使用 Windows 和麵板。

## <a name="related-links"></a>相關連結

- [MacWindows (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [MacInspector (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macinspector)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用功能表](~/mac/user-interface/menu.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
