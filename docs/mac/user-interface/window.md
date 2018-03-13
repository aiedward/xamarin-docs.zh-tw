---
title: Windows
description: "本文涵蓋了使用 windows 和 Xamarin.Mac 應用程式中的面板。 它說明建立 windows 和 Xcode 和介面產生器中，從 分鏡腳本和.xib 檔案載入它們以及它們以程式設計方式使用中的面板。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: f483fcfa9dfca1eb476ceab2b67e7a03bf4b6354
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="windows"></a>Windows

_本文涵蓋了使用 windows 和 Xamarin.Mac 應用程式中的面板。它說明建立 windows 和 Xcode 和介面產生器中，從 分鏡腳本和.xib 檔案載入它們以及它們以程式設計方式使用中的面板。_

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您具有存取權的 Windows 和面板，開發人員使用*OBJECTIVE-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的 Windows 和面板 （或您可以選擇直接在 C# 程式碼中建立它們）。

根據它的用途，Xamarin.Mac 應用程式，可能會造成一或多個視窗在螢幕上管理和協調其顯示和與運作方式的資訊。 Windows 的主要功能是：

1. 若要提供的檢視與控制項中的區域可以放置與管理。
2. 若要接受並回應使用者互動，使用鍵盤和滑鼠事件。

視窗可以是兩個或非強制回應的狀態 （例如可以有多個同時開啟的文件的文字編輯器） 中使用強制回應 （例如必須在應用程式才能繼續解除匯出對話方塊）。

面板是一種特殊的視窗 (子類別的基底`NSWindow`類別)，通常提供輔助函式服務應用程式，例如文字格式的偵測器 」 和 「 系統色彩選擇器等公用程式視窗中。

[![](window-images/intro01.png "編輯在 Xcode 中的視窗")](window-images/intro01.png#lightbox)

在本文中，我們將討論 Xamarin.Mac 應用程式中使用 Windows 和面板的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於網路接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>簡介 Windows

如前所述，視窗會提供一個區域中檢視及控制項可以放置與管理，並回應使用者互動 （是透過鍵盤或滑鼠） 為基礎的事件。

根據 Apple，有五個主要類型的 Windows 中 macOS 應用程式：

- **文件視窗**-文件視窗包含以檔案為基礎的使用者資料，例如試算表或文字文件。
- **應用程式視窗**-應用程式視窗是不是文件為基礎 （例如在 Mac 上的行事曆應用程式） 的應用程式的主視窗。
- **面板**-面板浮動於其他視窗，並提供工具，或開啟文件時，使用者可以如常使用的控制項。 在某些情況下，工作面板可以是半透明 (例如當使用大量圖形)。
- **對話方塊**-對話方塊會出現在回應使用者動作，並通常會提供方法的使用者可以完成這個動作。 可被關閉前，對話方塊會要求使用者回應。 (請參閱[使用對話](~/mac/user-interface/dialog.md))
- **警示**-警示是一種特殊的錯誤） （例如，就會發生嚴重的問題時出現的對話方塊或警告 （例如準備要刪除的檔案）。 因為警示是一個對話方塊，它也需要使用者回應可被關閉前。 (請參閱[使用警示](~/mac/user-interface/alert.md))

如需詳細資訊，請參閱[有關 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>Main、 金鑰和非使用中視窗

Xamarin.Mac 應用程式中的 Windows 可以檢視，並根據在使用者目前如何互動與它們的行為。 最重要的文件或應用程式視窗的目前使用者注意的重點稱為_主視窗_。 在大部分情況下此視窗也會_金鑰視窗_（目前正接受使用者輸入的視窗）。 但這不一定要這樣，比方說，色彩選擇器無法開啟，而且是使用者互動變更狀態的文件視窗 （這仍會是主視窗） 中的項目索引鍵 視窗。

主報表和金鑰 Windows （如果它們是各自獨立） 會永遠啟用，_非使用中視窗_是開啟的視窗不在前景中。 例如，文字編輯器應用程式可能會有一個以上的文件開啟時，只有主視窗會是作用中，其他所有項目會變成非使用中。 

如需詳細資訊，請參閱[有關 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>命名 Windows

視窗可以顯示標題列，顯示標題時，它通常是應用程式名稱、 視窗 （例如偵測器） 的函式或正在有效執行的文件名稱。 因為它們是可辨認視線，而且不處理的文件，某些應用程式不會顯示標題列。

Apple 的建議下列指導方針：

- 使用您的應用程式名稱的主要、 非文件視窗的標題。 
- 命名新的文件視窗`untitled`。 第一個新的文件，請勿將編號附加到標題 (例如`untitled 1`)。 如果使用者建立另一個之前儲存和標題的第一個新的文件時，呼叫該視窗`untitled 2`，`untitled 3`等等。

如需詳細資訊，請參閱[命名 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1) Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>全螢幕視窗

在 macOS，應用程式的視窗可以移全螢幕隱藏所有項目包括應用程式功能表列 （這可以會將游標移至畫面頂端揭露） 來提供受干擾可用互動是內容。

Apple 建議下列指導方針：

- 判斷是否合理到全螢幕視窗。 提供簡短互動 （例如計算機） 的應用程式不應提供全螢幕模式。
- 全螢幕工作是否需要它會顯示工具列。 通常在全螢幕模式中隱藏工具列。
- 全螢幕視窗應該有完成工作所需的所有功能使用者。
- 可能的話，請避免 Finder 互動，但使用者卻在全螢幕視窗。
- 利用增加的螢幕空間不含移位將焦點從主要工作。

如需詳細資訊，請參閱[全螢幕視窗](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>面板

面板是輔助視窗，包含控制項和會影響主動式文件或選取項目 （例如系統色彩選擇器等） 的選項：

[![](window-images/panel01.png "色彩面板")](window-images/panel01.png#lightbox)

面板可以是_應用程式專屬_或_全系統_。 應用程式專屬面板頂端的應用程式的文件視窗上方 float，和應用程式是在背景中消失。 全系統面板 (例如**字型**面板)，不論應用程式的所有已開啟視窗的最上層的浮點數。 

Apple 建議下列指導方針：

- 一般情況下，使用標準的面板，透明面板只應謹慎使用，並針對大量繪圖功能的工作。
- 請考慮使用工作面板，讓使用者輕鬆存取重要的控制項或直接影響其工作的資訊。
- 隱藏和顯示所需的面板。
- 面板應該永遠包含標題列。
- 面板不應該包含使用中的最小化按鈕。

#### <a name="inspectors"></a>偵測器

大多數最新型的 macOS 應用程式在輔助控制項與選項會影響主動式文件或選取項目為_偵測器_一部分之主視窗 (像是**頁面**應用程式，如下所示)，而不是使用面板 Windows:

[![](window-images/panel02.png "偵測器範例")](window-images/panel02.png#lightbox)

如需詳細資訊，請參閱[面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)Apple 的區段[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)以及我們[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) 完整實作的範例應用程式**偵測器介面**Xamarin.Mac 應用程式中。

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>建立和維護 Windows 在 Xcode 中

當您建立新的 Xamarin.Mac Cocoa 應用程式時，預設會取得標準的空白，視窗。 此 windows 中所定義`.storyboard`自動包含在專案中的檔案。 若要編輯您的 windows 設計中**方案總管] 中**，按兩下 [`Main.storyboard`檔案：

[![](window-images/edit01.png "選取 主要腳本")](window-images/edit01.png#lightbox)

這會在 Xcode 的介面產生器中開啟視窗設計：

[![](window-images/edit02.png "編輯在 Xcode 中 UI")](window-images/edit02.png#lightbox)

在**屬性偵測器**，有幾個屬性可讓您定義及控制您的視窗：

- **標題**-這是將會顯示在視窗的標題列的文字。
- **自動儲存**-這是_金鑰_，將會用於識別碼視窗時自動儲存它的位置和設定。
- **標題列**-視窗是否顯示標題列。
- **統一的標題和工具列**-如果視窗包含的工具列上，它應該是標題列的一部分。
- **完整大小的內容檢視**-允許內容的視窗會顯示在標題列區域。
- **陰影**-視窗是否有陰影。
- **材質**-材質的 windows 可以使用效果 （例如 vibrancy) 和可移動拖曳其主體的任何位置。
- **關閉**-視窗是否已關閉 按鈕。
- **最小化**-視窗是否有最小化按鈕。
- **調整大小**-視窗是否具有調整控制項。
- **工具列按鈕**-視窗是否有隱藏/顯示工具列按鈕。
- **可還原的已**-是視窗的位置及設定自動儲存和還原。
- **顯示在啟動**-會自動顯示視窗時`.xib`檔案載入。
- **隱藏上停用**-視窗時隱藏應用程式進入背景。
- **發行時關閉**-是視窗關閉時，會從記憶體清除。
- **永遠顯示工具提示**-會不斷顯示的工具提示。
- **重新計算檢視迴圈**-檢視順序繪製視窗前重新計算。
- **空格**， **Exposé**和**Cycling** -所有定義視窗這些 macOS 環境中的運作方式。 
- **全螢幕**-決定是否這個視窗可以輸入全螢幕模式。 
- **動畫**-控制可用的視窗動畫的類型。
- **外觀**-控制視窗的外觀。 現在是只有一個外觀，青色。

請參閱 Apple[簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)和[NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow)文件以取得詳細資料。

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>設定的預設大小和位置

若要設定您的視窗的初始位置，並控制它的大小，切換至**大小 Inspector**:

[![](window-images/edit07.png "預設大小和位置")](window-images/edit07.png#lightbox)

從這裡您可以設定視窗的初始大小，提供最小和最大大小，在螢幕上設定的初始位置並控制視窗周圍的框線。

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>設定自訂的主視窗的控制站

若要能夠建立插座和動作公開 （expose） 至 C# 程式碼的 UI 項目，Xamarin.Mac 應用程式必須使用自訂的視窗控制站。

請執行下列動作：

1. 在 Xcode 的介面產生器中開啟應用程式的分鏡腳本。
2. 選取`NSWindowController`設計介面中。
3. 切換至**識別 Inspector**檢視及輸入`WindowController`為**類別名稱**: 

    [![](window-images/windowcontroller01.png "設定類別名稱")](window-images/windowcontroller01.png#lightbox)
4. 儲存變更並返回 Visual Studio for Mac 同步處理。
5. A`WindowController.cs`檔案會加入至您的專案中**方案總管 中**適用於 Mac 的 Visual Studio 中： 

    [![](window-images/windowcontroller02.png "選取的 windows 控制站")](window-images/windowcontroller02.png#lightbox)
6. 重新開啟分鏡腳本 Xcode 的介面產生器中。
7. `WindowController.h`檔案可供使用： 

    [![](window-images/windowcontroller03.png "編輯 WindowController.h 檔案")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>將 UI 項目

若要定義 視窗的內容，將控制項從**程式庫偵測器**到**介面編輯器**。 請參閱我們[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)如需有關使用來建立和啟用控制項的介面產生器的文件。

例如，讓我們來拖曳工具列的**程式庫偵測器**到中的視窗**介面編輯器**:

[![](window-images/edit03.png "從程式庫選取工具列")](window-images/edit03.png#lightbox)

下一步，拖曳**文字檢視**並調整大小以填滿在工具列底下的區域：

[![](window-images/edit04.png "加入文字檢視")](window-images/edit04.png#lightbox)

因為我們想要**文字檢視**壓縮和成長為視窗大小變更，讓我們來切換至**條件約束編輯器**並新增下列條件約束：

[![](window-images/edit05.png "編輯條件約束")](window-images/edit05.png#lightbox)

依序按一下的**紅色我的資料交換**位於頂端的編輯器 中，而按一下**加入 4 條件約束**，我們將告訴黏貼到指定的 X，Y 座標和擴張或縮小水平及垂直文字檢視為調整視窗大小。

最後，讓我們來公開**文字檢視**編寫程式碼使用**插座**(務必要選取`ViewController.h`檔案):

[![](window-images/edit06.png "設定輸出")](window-images/edit06.png#lightbox)

儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。

如需有關使用**插座**和**動作**，請參閱我們[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)文件。

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>標準的視窗的工作流程

您建立和使用 Xamarin.Mac 應用程式中的任何視窗，請在程序基本上是我們做了什麼就上述相同：

1. 對於新的 windows 不會自動加入到專案的預設值，將新的視窗定義加入專案。 這將在下面將詳細討論。
2. 按兩下`Main.storyboard`来開啟的視窗設計在 Xcode 的介面產生器中編輯檔案。
3. 將新的視窗拖曳至使用者介面的設計，以及連結至使用主視窗的視窗_Segues_ (如需詳細資訊，請參閱[Segues](~/mac/platform/storyboards/indepth.md#Segues)區段我們[使用分鏡腳本](~/mac/platform/storyboards/indepth.md)文件)。
3. 在中設定任何必要的視窗屬性**屬性偵測器**和**大小 Inspector**。
4. 在建置您的介面，然後設定中所需的控制項拖曳**屬性偵測器**。
5. 使用**大小 Inspector**來處理您的 UI 項目調整大小。
6. 公開視窗的 UI 項目，以 C# 程式碼透過**插座**和**動作**。
7. 儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。

現在，我們已經建立的基本視窗，我們會探討的一般程序的 Xamarin.Mac，應用程式會使用 windows 時。 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>顯示預設的視窗

根據預設，新的 Xamarin.Mac 應用程式會自動顯示在定義視窗`MainWindow.xib`檔案已啟動：

[![](window-images/display01.png "執行範例視窗")](window-images/display01.png#lightbox)

因為我們修改上述該視窗的設計，所以現在包含預設值 工具列和**文字檢視**控制項。 下列主題中`Info.plist`檔案會負責顯示這個視窗：

[![](window-images/display00.png "編輯 Info.plist")](window-images/display00.png#lightbox)

**主要介面**下拉式清單中用來選取將使用與主應用程式 UI 的分鏡腳本 (在此情況下`Main.storyboard`)。

檢視控制器會自動加入至專案，以控制 （以及其主要檢視） 會顯示該主視窗。 其定義在`ViewController.cs`檔案，並附加至**檔案的擁有者**底下的介面產生器中**識別 Inspector**:

[![](window-images/display02.png "設定檔案的擁有者")](window-images/display02.png#lightbox)

我們視窗中，我們想要有標題的`untitled`當它第一次開啟現在讓我們來覆寫`ViewWillAppear`方法中的`ViewController.cs`至如下所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> 我們正在設定視窗的值`Title`屬性`ViewWillAppear`方法，而非`ViewDidLoad`方法因為時檢視可能會載入記憶體，它不尚未完整具現化。 如果我們嘗試存取`Title`屬性`ViewDidLoad`方法則會得到`null`因為視窗尚未建構並有線總屬性尚未例外狀況。

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>以程式設計方式關閉視窗

可能是您想要以程式設計方式關閉 Xamarin.Mac 應用程式視窗的時間，不讓使用者按一下視窗的**關閉**按鈕或使用功能表項目。 macOS 提供兩種不同的方式關閉`NSWindow`以程式設計的方式：`PerformClose`和`Close`。

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

呼叫`PerformClose`方法`NSWindow`會模擬使用者按一下視窗的**關閉**立刻顯示反白顯示的按鈕，然後關閉視窗 按鈕。

如果應用程式實作`NSWindow`的`WillClose`事件，將會在視窗關閉之前引發。 如果此事件傳回`false`，便不會關閉視窗。 如果視窗並沒有**關閉**按鈕，或無法關閉基於任何理由，作業系統會發出警示音效。

例如: 

```csharp
MyWindow.PerformClose(this);
```

嘗試關閉`MyWindow``NSWindow`執行個體。 如果已順利完成，將關閉視窗、 否則會發出警示音效和將會保持開啟。

<a name="Close" />

### <a name="close"></a>關閉

呼叫`Close`方法`NSWindow`無法模擬使用者按一下視窗的**關閉**按鈕透過暫時反白顯示的按鈕，它只會關閉視窗。

視窗並沒有看見要關閉和`NSWindowWillCloseNotification`通知將會張貼到視窗關閉的預設通知中心。

`Close`方法從兩個重要方面不同`PerformClose`方法：

1. 它不會引發`WillClose`事件。
2. 它不會模擬使用者按一下**關閉**透過暫時反白顯示的按鈕的按鈕。

例如: 

```csharp
MyWindow.Close();
```

要關閉`MyWindow``NSWindow`執行個體。

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>修改的 Windows 內容

在 macOS Apple 已提供方法來通知使用者之視窗的內容 (`NSWindow`) 使用者已修改，且必須儲存。 如果視窗包含修改過的內容，小黑色點將會顯示在它的**關閉**widget:

[![](window-images/close01.png "視窗中，以修改過的標記")](window-images/close01.png#lightbox)

如果使用者嘗試關閉視窗或結束 Mac 應用程式，同時有未儲存變更視窗的內容，您應該呈現[對話方塊](~/mac/user-interface/dialog.md)或[強制回應的工作表](~/mac/user-interface/dialog.md)並允許使用者儲存其變更第一個：

[![](window-images/close02.png "儲存工作表視窗關閉時顯示")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>標示為已修改的視窗

若要將標示為具有修改內容的視窗，請使用下列程式碼：

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

並已儲存的變更，一旦清除已修改的旗標使用：

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>儲存變更之前關閉視窗

若要監看使用者關閉視窗，並讓它們能夠在事先儲存修改過的內容，您必須建立的子類別`NSWindowDelegate`並覆寫其`WindowShouldClose`方法。 例如: 

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class EditorWidowDelegate : NSWindowDelegate
    {
        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public EditorWidowDelegate (NSWindow window)
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

                // Take action based on resu;t
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

您可以使用下列程式碼，將這個委派的執行個體附加至您的視窗：

```csharp
// Set delegate
Window.Delegate = new EditorWidowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>儲存變更，再關閉應用程式

最後，Xamarin.Mac 應用程式應該檢查以查看它的任何視窗包含修改過的內容，讓使用者在結束之前儲存的變更。 若要這樣做，請編輯您`AppDelegate.cs`檔案，請覆寫`ApplicationShouldTerminate`方法並看起來如下所示：

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

大部分的文件 Mac 應用程式可以同時編輯多個文件。 例如，文字編輯器中可以有開啟的同時編輯多個文字檔案。 根據預設，新的 Xamarin.Mac 應用程式具有**檔案**功能表與**新增**項目自動有線總至`newDocument:`**動作**。

我們以啟動這個新項目並允許使用者開啟我們同時編輯多個文件的主視窗的多個複本。

開始編輯我們`AppDelegate.cs`檔案，然後加入下列計算的屬性：

```csharp
public int UntitledWindowCount { get; set;} =1;
```

我們將使用此追蹤的未儲存的檔案數目，以便我們可以提供意見反應給使用者 （每個 Apple 的指導方針如同上面所討論）。

接下來，加入下列方法：

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

這段程式碼建立視窗 Controller 的新版本、 載入新的視窗，便可主報表和金鑰] 視窗中，並設定為 [標題。 現在如果我們執行我們的應用程式，並選取**新增**從**檔案**會開啟並顯示新的編輯器視窗 功能表：

[![](window-images/display04.png "已加入新的未命名的視窗")](window-images/display04.png#lightbox)

如果我們開啟**Windows**功能表上，您可以看到應用程式會自動追蹤和處理我們開啟的視窗：

[![](window-images/display05.png "視窗功能表")](window-images/display05.png#lightbox)

如需有關 Xamarin.Mac 應用程式中使用功能表的詳細資訊，請參閱我們[使用功能表](~/mac/user-interface/menu.md)文件。

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>取得目前作用中視窗

在可以開啟多個視窗 （文件） 的 Xamarin.Mac 應用程式，有當您將需要取得的目前最上層視窗 （索引鍵的視窗） 的時間。 下列程式碼會傳回索引鍵的視窗：

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

它可以呼叫任何類別或方法需要存取金鑰的目前視窗中。 如果沒有任何視窗已開啟，則會傳回`null`。

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>存取所有的應用程式視窗

有些時候可能會是在您要存取所有 Xamarin.Mac 應用程式目前已開啟的視窗。 例如，如果使用者想要開啟的檔案，請參閱 < 是已經在現有的視窗中開啟。

`NSApplication.SharedApplication`維護`Windows`屬性，其中包含所有開啟的視窗，在您的應用程式中的陣列。 您可以逐一查看陣列來存取所有應用程式的目前 windows。 例如: 

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

在範例程式碼中，我們和轉換至自訂每個傳回的視窗`ViewController`類別在我們的應用程式和測試的自訂值`Path`屬性對使用者想要開啟的檔案路徑。 如果檔案已經開啟，我們會將該視窗帶到最上層。

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>調整視窗大小，在程式碼

但有些的時候，應用程式需要調整大小的視窗中的程式碼。 若要調整大小，並調整視窗的位置，您加以調整的`Frame`屬性。 當調整視窗的大小，您通常需要也調整它的原點，視窗保持在相同的位置，因為 macOS 的座標系統。

不同於 iOS 左上角位置表示 (0，0)、 macOS 使用數學座標系統，左下角的螢幕位置表示 (0，0)。 在 iOS 中座標增加向右向下移動。 在 macOS 座標增加向上向右的值。 

下列範例程式碼調整視窗大小時：

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> 當您調整視窗大小和位置，在程式碼中的時，您需要確定您遵守您具有在介面產生器中設定的最小和最大大小。 這不會自動生效，您將無法使視窗變大或變小超過這些限制。

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>監視的視窗大小變更

有些時候可能會是在您要監視 Xamarin.Mac 應用程式內的視窗大小變更。 例如，若要重繪內容以符合新的大小。

若要監視大小變更，請先確定您已在 Xcode 的介面產生器中的視窗控制站指派的自訂類別。 例如，`MasterWindowController`下列：

[![](window-images/resize01.png "身分識別檢查器")](window-images/resize01.png#lightbox)

接著，編輯自訂視窗控制器類別和監視`DidResize`即時大小變更的通知 視窗的控制站上的事件。 例如: 

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

或者，您可以使用`DidEndLiveResize`事件只會收到通知之後在使用者完成變更視窗的大小。 例如：

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

## <a name="setting-a-windows-title-and-represented-file"></a>設定視窗的標題，並且表示檔案

使用代表文件的 windows 時`NSWindow`具有`DocumentEdited`屬性設定為，如果`true`中檔案已經過修改，而應儲存在關閉前給使用者的 [關閉] 按鈕顯示小的點。

開始編輯我們`ViewController.cs`檔案，然後進行下列變更：

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

我們也會監視`WillClose`視窗與檢查的狀態事件`DocumentEdited`屬性。 如果是`true`我們要讓使用者能夠將變更儲存至檔案。 如果我們執行我們的應用程式，並輸入一些文字，將會顯示該點：

[![](window-images/file01.png "變更的視窗")](window-images/file01.png#lightbox)

如果我們嘗試關閉視窗，我們會得到警示：

[![](window-images/file02.png "顯示儲存對話方塊")](window-images/file02.png#lightbox)

如果我們會從檔案載入文件我們可以設定視窗的標題為的檔案名稱並使用`window.SetTitleWithRepresentedFilename (Path.GetFileName(path));`方法 (假設`path`為字串，代表所開啟的檔案)。 此外，我們可以設定的檔案使用 URL`window.RepresentedUrl = url;`方法。

如果 URL 會指向由 OS 已知檔案類型，它的圖示將顯示在標題列中。 如果使用者以滑鼠右鍵按一下圖示上時，將會顯示檔案的路徑。

開始編輯`AppDelegate.cs`檔案，然後加入下列方法：

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

現在如果我們執行我們的應用程式，請選取**開啟...**從**檔案**功能表上，選取從文字檔**開啟**對話方塊方塊中，並將它開啟：

[![](window-images/file03.png "開啟的對話方塊")](window-images/file03.png#lightbox)

檔案將會顯示，而且標題將會設定使用之檔案的圖示：

[![](window-images/file04.png "載入檔案的內容")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>專案中加入新的視窗

主要的文件視窗中，除了 Xamarin.Mac 應用程式可能需要其他類型的視窗顯示給使用者，例如喜好設定或偵測器面板。

若要加入新的視窗，執行下列作業：

1. 在**方案總管 中**，連按兩下`Main.storyboard`檔案以開啟 Xcode 的介面產生器中進行編輯。
2. 拖曳新**視窗控制器**從**文件庫**並放在**設計介面**:

    [![](window-images/new01.png "文件庫中選取新的視窗控制站")](window-images/new01.png#lightbox)
3. 在**識別 Inspector**，輸入`PreferencesWindow`如**分鏡腳本識別碼**: 

    [![](window-images/new02.png "設定分鏡腳本識別碼")](window-images/new02.png#lightbox)
5. 設計您的介面： 

    [![](window-images/new03.png "設計 UI")](window-images/new03.png#lightbox)
6. 開啟應用程式功能表 (`MacWindows`)，請選取**喜好設定...**，控制按一下並拖曳至新的視窗： 

    [![](window-images/new05.png "建立 segue")](window-images/new05.png#lightbox)
7. 選取**顯示**從快顯功能表。
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

如果我們執行程式碼，並選取**喜好設定...**從**應用程式功能表**，視窗會顯示：

[![](window-images/new04.png "範例喜好設定 功能表")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>使用面板

如同本文開頭所述，面板浮動於其他視窗，並提供工具或使用者開啟文件時可使用的控制項。 

就像任何其他類型，您建立及處理 Xamarin.Mac 應用程式中，處理程序基本上是視窗的相同：

1. 將新的視窗定義加入至專案。
2. 按兩下`.xib`来開啟的視窗設計在 Xcode 的介面產生器中編輯檔案。
2. 在中設定任何必要的視窗屬性**屬性偵測器**和**大小 Inspector**。
4. 在建置您的介面，然後設定中所需的控制項拖曳**屬性偵測器**。
5. 使用**大小 Inspector**來處理您的 UI 項目調整大小。
6. 公開視窗的 UI 項目，以 C# 程式碼透過**插座**和**動作**。
7. 儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。

在**屬性偵測器**，您有特定的面板的下列選項：

[![](window-images/panel03.png "屬性偵測器")](window-images/panel03.png#lightbox)

- **樣式**-可讓您調整從面板的樣式： 一般面板 （看起來像是標準的視窗）、 公用程式的面板 （具有較小的標題列），抬頭顯示器面板 (是半透明的標題列，且 背景的一部份)。
- **非啟用**-決定在面板變成索引鍵的視窗。
- **文件 Modal** -如果文件強制回應，面板會只一直浮在應用程式的 windows，否則它會浮動優先於所有。


若要加入新的面板，執行下列作業：

1. 在**方案總管 中**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**.
2. 在新檔案 對話方塊中，選取**Xamarin.Mac** > **Cocoa 視窗與控制器**:

    [![](window-images/panels00.png "加入新的視窗控制站")](window-images/panels00.png#lightbox)
3. 輸入 `DocumentPanel` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下`DocumentPanel.xib`開啟介面產生器中進行編輯的檔案： 

    [![](window-images/new02.png "編輯 pannel")](window-images/new02.png#lightbox)
5. 刪除現有的視窗和拖曳的面板從**程式庫偵測器**中**介面編輯器**: 

    [![](window-images/panels01.png "刪除現有的視窗")](window-images/panels01.png#lightbox)
6. 最多攔截面板 **檔案的擁有者*-**視窗*- **插座**: 

    [![](window-images/panels02.png "拖曳到 [面板] 中連接")](window-images/panels02.png#lightbox)
7. 切換至**識別 Inspector**設面板的類別和`DocumentPanel`: 

    [![](window-images/panels03.png "設定面板的類別")](window-images/panels03.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。
7. 編輯`DocumentPanel.cs`檔案，並將類別定義變更為下列： 

    `public partial class DocumentPanel : NSPanel`
8. 將變更儲存到檔案。

編輯`AppDelegate.cs`檔案並製作`DidFinishLaunching`方法看起來像下列：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

當我們執行我們的應用程式，將會顯示 [面板] 中：

[![](window-images/panels04.png "執行中應用程式中的面板")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> 面板 Windows apple 已被取代，而且應該取代**偵測器介面**。 如需完整的範例建立的**Inspector**是 Xamarin.Mac 應用程式，請參閱我們[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/)範例應用程式。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得 Xamarin.Mac 應用程式中使用 Windows 和面板的詳細的檢視。 我們已看到不同的類型，並使用視窗和面板、 如何建立和維護 Windows 和面板在 Xcode 的介面產生器及如何在 C# 程式碼中使用 Windows 和面板。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector （範例）](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用功能表](~/mac/user-interface/menu.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
