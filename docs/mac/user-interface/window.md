---
title: Xamarin.Mac 中的 Windows
description: 這篇文章涵蓋使用 「 windows 」 和 「 在 Xamarin.Mac 應用程式中的面板。 它說明建立 windows 和在 Xcode 和 Interface Builder 分鏡腳本和.xib 檔案載入它們，並以程式設計方式處理它們的面板。
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: ec907e71074a97bd5d1714e79dd504013f5c8a4b
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51526971"
---
# <a name="windows-in-xamarinmac"></a>Xamarin.Mac 中的 Windows

_這篇文章涵蓋使用 「 windows 」 和 「 在 Xamarin.Mac 應用程式中的面板。它說明建立 windows 和在 Xcode 和 Interface Builder 分鏡腳本和.xib 檔案載入它們，並以程式設計方式處理它們的面板。_

使用時C#和.NET Xamarin.Mac 應用程式中，存取相同的 Windows 和面板的工作的開發人員*OBJECTIVE-C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的 Windows 和面板 （或選擇直接在 C# 程式碼中建立它們）。

根據其用途，Xamarin.Mac 應用程式可以呈現一或多個 Windows 在畫面上管理與協調的資訊，它會顯示，並搭配。 視窗的主體的函式為：

1. 若要提供一個區域，在其中檢視和控制項可以放置與管理。
2. 若要接受並回應事件以回應使用者與鍵盤和滑鼠互動。

Windows 可以是用於非強制回應的狀態 （例如文字編輯器，可以有多個同時開啟的文件） 或強制回應 （例如必須關閉應用程式才能繼續 [匯出] 對話方塊中）。

面板是一種特殊的視窗 (子類別的基底`NSWindow`類別)，通常提供輔助函式服務應用程式，例如文字格式的偵測器等系統色彩選擇器的公用程式視窗中。

[![](window-images/intro01.png "編輯視窗，以在 Xcode 中")](window-images/intro01.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用 Windows 」 和 「 面板的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`命令用於連線接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。

<a name="Introduction_to_Windows" />

## <a name="introduction-to-windows"></a>Windows 的簡介

如上所述，視窗會提供在其中檢視和控制項可以放置與管理的區域，並回應使用者互動 （可能是透過鍵盤或滑鼠） 為基礎的事件。

根據 Apple，有五個主要類型的 Windows 中的 macOS 應用程式：

- **文件視窗**-文件視窗包含檔案為基礎的使用者資料，例如試算表或文字文件。
- **應用程式視窗**-應用程式視窗是不是文件為基礎 （例如，在 Mac 上的行事曆應用程式） 的應用程式的主視窗。
- **面板**-其他視窗上方會浮動面板，並提供工具或控制項，使用者可使用文件時開啟。 在某些情況下，工作面板可以半透明 (例如當處理大型圖形)。
- **對話方塊**-對話方塊會出現在回應使用者動作，而且通常會提供使用者可以完成的動作。 可被關閉前，對話方塊會要求使用者回應。 (請參閱[使用對話方塊](~/mac/user-interface/dialog.md))
- **警示**-表示警示是一種特殊的嚴重問題發生 （例如錯誤） 時，會出現的對話方塊或警告 （例如正在準備刪除檔案）。 由於警示是一個對話方塊，它還需要使用者回應之前予以關閉。 (請參閱[使用警示](~/mac/user-interface/alert.md))

如需詳細資訊，請參閱 <<c0> [ 關於 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Main_Key_and_Inactive_Windows" />

### <a name="main-key-and-inactive-windows"></a>主要、 金鑰和非使用中的 Windows

在 Xamarin.Mac 應用程式中的 Windows 可以外觀和行為 根據使用者如何目前與它們互動。 最重要的文件或目前使用者的注意力的重點的應用程式視窗稱為_主視窗_。 在大部分情況下此視窗也會_機碼 視窗_（目前接受使用者輸入的視窗）。 但這不一定如此，例如，色彩選擇器無法開啟，而是使用者互動變更文件視窗 （這仍會是主視窗） 中的項目狀態的 [金鑰] 視窗。

主報表和索引鍵 （如果它們是各自獨立） 的 Windows 會永遠作用中，_非作用中的 Windows_是開啟的視窗不在前景中。 例如，文字編輯器應用程式可能會有一個以上的文件開啟時，一次只有主視窗會是作用中，所有其他項目會變成非使用中。 

如需詳細資訊，請參閱 <<c0> [ 關於 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Naming_Windows" />

### <a name="naming-windows"></a>命名 Windows

視窗可以顯示的標題列，並出現標題時，它通常是應用程式的名稱、 名稱正在處理的文件或視窗 （例如偵測器） 的函式。 有些應用程式不顯示標題列，因為它們是可辨識的深入解析，而且不使用文件。

Apple 建議下列指導方針：

- 使用您的應用程式名稱的主要、 非文件視窗的標題。 
- 命名新的文件視窗`untitled`。 第一個新的文件，不要附加一個數字標題 (例如`untitled 1`)。 如果使用者建立另一個新文件儲存和標題的第一個之前，呼叫該視窗`untitled 2`，`untitled 3`等等。

如需詳細資訊，請參閱 <<c0> [ 命名 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Full-Screen_Windows" />

### <a name="full-screen-windows"></a>全螢幕 Windows

在 macOS 應用程式的視窗可以移全螢幕隱藏所有項目包括應用程式功能表列 （這可以顯示游標移至畫面頂端） 來提供受干擾，免費互動是內容。

Apple 建議下列指導方針：

- 判斷是否有意義前往全螢幕視窗。 提供簡短的互動 （例如計算機） 的應用程式不應提供全螢幕模式。
- 全螢幕工作是否需要它顯示工具列。 通常會隱藏工具列，在全螢幕模式中。
- 全螢幕視窗應該有完成工作所需要的所有功能使用者。
- 可能的話，請避免搜尋工具互動，但使用者卻在全螢幕視窗中。
- 利用增加的螢幕空間而不轉移將焦點從主要的工作。

如需詳細資訊，請參閱 <<c0> [ 全螢幕 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Panels" />

### <a name="panels"></a>面板

面板是輔助視窗，其中包含控制項和會影響使用中的文件或選取項目 （例如系統色彩選擇器中） 的選項：

[![](window-images/panel01.png "色彩面板")](window-images/panel01.png#lightbox)

面板可以是_應用程式專屬_或是_全系統_。 應用程式特定面板 float 頂端的 應用程式的文件視窗上方，和應用程式處於背景時，會消失。 全系統面板 (例如**字型**面板)，不論應用程式所有開啟的視窗上方的浮點數。 

Apple 建議下列指導方針：

- 一般情況下，使用標準的面板，謹慎及圖形密集的工作，應該只使用透明的面板。
- 請考慮使用一個面板，讓使用者輕鬆存取重要的控制項，或直接影響其工作的資訊。
- 隱藏和顯示所需的面板。
- 面板應該永遠包含標題列。
- 面板不應包含使用中的最小化 按鈕。

#### <a name="inspectors"></a>偵測器

現今大部分的 macOS 應用程式提供輔助的控制項和會影響主動式文件或做為選取的選項_偵測器_屬於主視窗 (例如**頁**如下所示的應用程式)，而不是使用 Windows 控制台：

[![](window-images/panel02.png "在範例中的偵測器")](window-images/panel02.png#lightbox)

如需詳細資訊，請參閱 <<c0> [ 面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)一節的 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)以及我們[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/) 的完整實作的範例應用程式**偵測器介面**Xamarin.Mac 應用程式中。

<a name="Creating_and_Maintaining_Windows_in_Xcode" />

## <a name="creating-and-maintaining-windows-in-xcode"></a>建立和維護在 Xcode 中的 Windows

當您建立新的 Xamarin.Mac 的 Cocoa 應用程式時，您會取得預設的標準的空白，視窗。 此 windows 定義於`.storyboard`自動包含在專案中的檔案。 若要編輯您的 windows 設計，在**方案總管**，按兩下 `Main.storyboard`檔案：

[![](window-images/edit01.png "選取主要分鏡腳本")](window-images/edit01.png#lightbox)

這會在 Xcode 的 Interface Builder 中開啟的視窗設計：

[![](window-images/edit02.png "編輯在 Xcode 中的 UI")](window-images/edit02.png#lightbox)

在 **屬性偵測器**，有幾個屬性，可用來定義及控制您的視窗：

- **標題**-這是會顯示在視窗的標題列的文字。
- **自動儲存**-這是_金鑰_，將用於識別碼視窗時自動儲存它的位置和設定。
- **標題列**-視窗是否顯示標題列。
- **統一的標題和工具列**-如果視窗包含工具列，它應該會在標題列的一部分。
- **完整大小的內容檢視**-可讓視窗標題列下方的內容區域。
- **陰影**-視窗是否有陰影。
- **材質**-材質的 windows 可以使用 （例如 vibrancy) 的效果，並可在任何位置拖曳其主體上四處移動。
- **關閉**-視窗是否已關閉 按鈕。
- **最小化**-視窗是否有最小化按鈕。
- **調整大小**-視窗是否有一個調整大小的控制項。
- **工具列按鈕**-視窗是否有隱藏/顯示工具列按鈕。
- **可還原的已**-視窗的位置以及設定自動儲存和還原。
- **顯示在啟動**-會自動顯示視窗時`.xib`載入檔案。
- **隱藏上停用**-視窗中隱藏應用程式進入背景時。
- **發行時關閉**-是視窗關閉時，從記憶體清除。
- **永遠顯示工具提示**-會持續顯示工具提示。
- **重新計算檢視迴圈**-檢視順序繪製視窗前重新計算。
- **空格**， **Exposé**並**Cycling** -全都定義視窗這些 macOS 環境中的運作方式。 
- **全螢幕**-決定是否這個視窗可以輸入的全螢幕模式。 
- **動畫**-控制可以使用視窗動畫的類型。
- **外觀**-控制視窗的外觀。 現在是只有一個外觀，青色。

請參閱 Apple [Introduction to Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)並[NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow)文件，如需詳細資訊。

<a name="Setting_the_Default_Size_and_Location" />

### <a name="setting-the-default-size-and-location"></a>設定預設大小和位置

若要設定您的視窗的初始位置，並控制它的大小，切換至**大小偵測器**:

[![](window-images/edit07.png "預設大小和位置")](window-images/edit07.png#lightbox)

從這裡您可以設定視窗的初始大小、 為它提供的最小和最大的大小、 在螢幕上設定的初始位置和控制視窗周圍的框線。

<a name="Setting-a-Custom-Main-Window-Controller" />

### <a name="setting-a-custom-main-window-controller"></a>設定自訂的主視窗的控制站

若要能夠建立輸出和動作，以公開到 C# 程式碼的 UI 項目，Xamarin.Mac 應用程式必須使用自訂視窗控制器。

請執行下列動作：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的分鏡腳本。
2. 選取`NSWindowController`設計介面中。
3. 切換至**身分識別偵測器**檢視，然後輸入`WindowController`作為**類別名稱**: 

    [![](window-images/windowcontroller01.png "設定的類別名稱")](window-images/windowcontroller01.png#lightbox)
4. 儲存變更並返回 Visual Studio for Mac 中進行同步處理。
5. A`WindowController.cs`檔案會新增至您的專案中**方案總管 中**在 Visual Studio for Mac: 

    [![](window-images/windowcontroller02.png "選取的 windows 控制站")](window-images/windowcontroller02.png#lightbox)
6. 重新開啟在 Xcode 的 Interface Builder 分鏡腳本。
7. `WindowController.h`檔案可供使用： 

    [![](window-images/windowcontroller03.png "編輯 WindowController.h 檔案")](window-images/windowcontroller03.png#lightbox)

<a name="Adding_UI_Elements" />

### <a name="adding-ui-elements"></a>將 UI 項目

若要定義 視窗的內容，將控制項從**程式庫偵測器**拖曳至**介面編輯器**。 請參閱我們[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)文件以使用 Interface Builder 來建立並啟用控制項的詳細資訊。

例如，讓我們來拖曳工具列**程式庫偵測器**拖曳至視窗中**介面編輯器**:

[![](window-images/edit03.png "從程式庫選取工具列")](window-images/edit03.png#lightbox)

接下來，拖曳**文字檢視**並調整大小以填滿的區域，在工具列底下：

[![](window-images/edit04.png "加入文字檢視")](window-images/edit04.png#lightbox)

因為我們希望**文字檢視**若要壓縮，並隨著視窗大小變更，讓我們切換**條件約束編輯器**並新增下列條件約束：

[![](window-images/edit05.png "編輯條件約束")](window-images/edit05.png#lightbox)

依序按一下針對**紅色 I 字形狀**頂端的編輯器] 和 [**加入 4 個條件約束**，告訴堅持指定的 X，Y 座標和擴張或縮小水平和垂直文字檢視為調整視窗大小。

最後，我們會公開**文字檢視**若要使用程式碼**插座**(務必要選取`ViewController.h`檔案):

[![](window-images/edit06.png "設定輸出")](window-images/edit06.png#lightbox)

儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。

如需使用詳細資訊**插座**並**動作**，請參閱我們[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)文件。

<a name="Standard_Window_Workflow" />

### <a name="standard-window-workflow"></a>標準的視窗的工作流程

您建立並使用您的 Xamarin.Mac 應用程式中的任何視窗，請在程序基本上是我們剛才已經上述相同：

1. 適用於新的 windows 不會自動加入到專案的預設值，將新的視窗定義加入專案。 這將在以下將詳細討論。
2. 按兩下`Main.storyboard`来開啟的視窗設計在 Xcode 的 Interface Builder 中編輯檔案。
3. 將新的視窗拖曳至使用者介面的設計，並將視窗連線到使用主視窗_Segue_ (如需詳細資訊，請參閱[Segue](~/mac/platform/storyboards/indepth.md#Segues)一節我們[使用分鏡腳本](~/mac/platform/storyboards/indepth.md)文件)。
3. 設定任何所需的視窗屬性**屬性偵測器**並**大小偵測器**。
4. 在建置您的介面和設定它們所需的控制項中拖曳**屬性偵測器**。
5. 使用**大小偵測器**來處理您的 UI 項目調整大小。
6. 公開到 C# 程式碼透過視窗的 UI 項目**插座**並**動作**。
7. 儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。

既然我們已經建立的基本視窗時，我們將探討常見的處理序 Xamarin.Mac 應用程式會使用 windows 時。 

<a name="Displaying_the_Default_Window" />

## <a name="displaying-the-default-window"></a>顯示預設的視窗

根據預設，新的 Xamarin.Mac 應用程式會自動顯示在定義視窗`MainWindow.xib`檔案會在啟動時：

[![](window-images/display01.png "執行範例視窗")](window-images/display01.png#lightbox)

因為我們會修改該視窗上方的設計，所以它現在包含預設值 工具列並**文字檢視**控制項。 下一節中`Info.plist`檔案會負責顯示這個視窗：

[![](window-images/display00.png "編輯 Info.plist")](window-images/display00.png#lightbox)

**主要介面**下拉式清單來選取將用於主要的應用程式 UI 的分鏡腳本 (在此情況下`Main.storyboard`)。

檢視控制器會自動加入至專案，以控制 （以及其主要的檢視） 會顯示該主要 Windows。 它定義於`ViewController.cs`檔案，並附加至**檔案的擁有者**下的 Interface Builder 中**身分識別偵測器**:

[![](window-images/display02.png "設定檔案的擁有者")](window-images/display02.png#lightbox)

我們的視窗中，我們想要它具有的標題`untitled`當它第一次開啟現在讓我們來覆寫`ViewWillAppear`方法中的`ViewController.cs`來看起來如下：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
``` 

> [!NOTE]
> 我們要設定視窗的值`Title`中的屬性`ViewWillAppear`方法，而非`ViewDidLoad`方法因為雖然檢視可能會載入記憶體，它不尚未完全具現化。 如果我們嘗試存取`Title`中的屬性`ViewDidLoad`方法會得到`null`因為視窗尚未被建構與有線向上屬性尚未的例外狀況。

<a name="Programmatically_Closing_a_Window" />

## <a name="programmatically-closing-a-window"></a>以程式設計方式關閉視窗

可能有您想要以程式設計方式關閉視窗，以在 Xamarin.Mac 應用程式中的時間，而非擁有使用者按一下視窗**關閉**按鈕或使用功能表項目。 macOS 提供兩個不同的方式，以關閉`NSWindow`程式設計的方式：`PerformClose`和`Close`。

<a name="PerformClose" />

### <a name="performclose"></a>PerformClose

呼叫`PerformClose`方法`NSWindow`模擬使用者按一下視窗**關閉**立刻顯示反白顯示的按鈕，然後再關閉視窗按鈕。

如果應用程式實作`NSWindow`的`WillClose`事件，會在關閉視窗之前引發。 如果此事件傳回`false`，便不會關閉視窗。 如果視窗並沒有**關閉**按鈕，或無法關閉，因為任何原因，OS 就會發出警示音效。

例如: 

```csharp
MyWindow.PerformClose(this);
```

會嘗試關閉`MyWindow``NSWindow`執行個體。 如果已順利完成，將會關閉視窗，否則將會發出警示音效且將保持開啟狀態。

<a name="Close" />

### <a name="close"></a>關閉

呼叫`Close`方法`NSWindow`無法模擬使用者按一下視窗**關閉**按鈕透過暫時反白顯示的按鈕，只需關閉視窗。

視窗沒有顯示關閉和`NSWindowWillCloseNotification`通知將會張貼到預設的通知中心，正在關閉視窗。

`Close`在兩個重要的方面，與不同的方法`PerformClose`方法：

1. 它不會嘗試引發`WillClose`事件。
2. 它不會模擬使用者按一下 **關閉**立刻顯示反白按鈕的按鈕。

例如: 

```csharp
MyWindow.Close();
```

通常用來關閉`MyWindow``NSWindow`執行個體。

<a name="Modified-Windows-Content" />

## <a name="modified-windows-content"></a>修改過的 Windows 內容

在 macOS，Apple 已提供一個方式來通知使用者，視窗的內容 (`NSWindow`) 已由使用者修改，而必須儲存。 如果視窗包含修改過的內容，小型的黑點會顯示在其**關閉**widget:

[![](window-images/close01.png "具有已修改的標記的視窗")](window-images/close01.png#lightbox)

如果使用者嘗試關閉視窗，或結束 Mac 應用程式時有未儲存變更視窗的內容，您應該呈現[ 對話方塊中](~/mac/user-interface/dialog.md)或[強制回應的工作表](~/mac/user-interface/dialog.md)，並允許使用者儲存其變更第一個：

[![](window-images/close02.png "儲存視窗關閉時顯示的工作表")](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>標示為已修改的視窗

若要將標示為具有修改內容的視窗，請使用下列程式碼：

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

並已儲存的變更之後, 清除修改的旗標使用：

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>儲存變更之前關閉視窗

若要觀看使用者關閉視窗，並且允許使用者以事先儲存修改過的內容，您必須建立的子類別`NSWindowDelegate`，並覆寫其`WindowShouldClose`方法。 例如: 

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

您可以使用下列程式碼，將這個委派的執行個體附加至您的視窗：

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>正在儲存變更，再關閉應用程式

最後，Xamarin.Mac 應用程式應該檢查以查看任何其 Windows 是否包含修改過的內容，並允許使用者前先予儲存的變更。 若要這樣做，請編輯您`AppDelegate.cs`檔案中，覆寫`ApplicationShouldTerminate`方法，使它看起來如下所示：

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

## <a name="working-with-multiple-windows"></a>使用多個 Windows

大部分的文件以 Mac 應用程式可以同時編輯多份文件。 例如，文字編輯器可以有多個文字檔案開啟進行編輯，在相同的時間。 根據預設，新的 Xamarin.Mac 應用程式具有**檔案**功能表，其中**新增**項目自動有線-最多`newDocument:`**動作**。

我們會啟用這個新的項目，並允許使用者開啟我們的主視窗一次編輯多份文件的多個複本。

讓我們編輯我們`AppDelegate.cs`檔案，並新增下列計算的屬性：

```csharp
public int UntitledWindowCount { get; set;} =1;
```

我們將使用此追蹤的未儲存的檔案數目，以便我們可以提供意見反應給使用者 （每個 Apple 的指導方針如同上面所討論）。

接下來，新增下列方法：

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

此程式碼會建立新視窗控制器版本、 載入新的視窗、 主報表和金鑰 視窗中，可讓和設定其標題。 現在如果我們執行我們的應用程式，並選取**的新**從**檔案**會開啟並顯示新的 [編輯器] 視窗的功能表：

[![](window-images/display04.png "已新增新的未命名的視窗")](window-images/display04.png#lightbox)

如果我們開啟**Windows**  功能表中，您可以看到應用程式會自動追蹤並處理我們開啟的視窗：

[![](window-images/display05.png "Windows 功能表")](window-images/display05.png#lightbox)

如需有關在 Xamarin.Mac 應用程式中使用功能表的詳細資訊，請參閱我們[使用功能表](~/mac/user-interface/menu.md)文件。

<a name="Getting_the_Currently_Active_Window" />

### <a name="getting-the-currently-active-window"></a>取得目前使用中視窗

在 Xamarin.Mac 應用程式可以開啟多個視窗 （文件），有一些當您將的需要取得的目前最上層視窗 （[金鑰] 視窗）。 下列程式碼會傳回索引鍵的視窗：

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

它可以呼叫任何類別或方法需要存取金鑰的目前視窗中。 如果沒有任何視窗已開啟，它會傳回`null`。

<a name="Accessing-All-App-Windows" />

### <a name="accessing-all-app-windows"></a>存取所有的應用程式 Windows

有時候可能會是在您要存取所有 Xamarin.Mac 應用程式目前已開啟的視窗。 比方說，若要查看如果使用者想要開啟的檔案是已在現有的視窗中開啟。

`NSApplication.SharedApplication`維護`Windows`屬性，其中包含所有開啟的視窗，在您的應用程式中的陣列。 您可以逐一查看陣列存取所有的應用程式的目前視窗。 例如: 

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

在範例程式碼中，我們正在轉換至自訂每個傳回的視窗`ViewController`類別，在我們的應用程式和測試的自訂值`Path`對使用者想要開啟的檔案路徑的屬性。 如果檔案已經開啟，我們要帶領前端該視窗。

<a name="Adjusting_the_Window_Size_in_Code" />

## <a name="adjusting-the-window-size-in-code"></a>調整視窗大小，在程式碼

有些的時候，應用程式需要調整大小的視窗中的程式碼。 若要調整大小，並調整視窗的位置，您進行調整的`Frame`屬性。 當調整視窗的大小，您通常需要調整它的來源，以讓視窗保持在相同的位置，因為 macOS 的座標系統。

不同於 iOS 左上角位置代表 (0，0)，macOS 會使用數學的座標系統左下角的螢幕表示 (0，0) 的位置。 在 iOS 中的座標會增加隨著您往右邊向下移動。 在 macOS 中往上向右的值增加座標。 

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
> 當您調整視窗大小和位置，在程式碼中的時，您必須確定您遵守您已在 介面產生器中設定的最小和最大大小。 這不會自動接受，您將能夠讓視窗放大或縮小比這些限制。

<a name="Monitoring-Window-Size-Changes" />

## <a name="monitoring-window-size-changes"></a>監視視窗大小變更

有時候可能會是在您要監視的 Xamarin.Mac 應用程式內的視窗大小的變更。 例如，若要重繪內容，以符合新的大小。

若要監視的大小變更，請先確定您已在 Xcode 的 Interface Builder 中的視窗控制站指派的自訂類別。 比方說，`MasterWindowController`如下：

[![](window-images/resize01.png "身分識別偵測器")](window-images/resize01.png#lightbox)

接著，編輯 [自訂] 視窗的控制器類別和監視`DidResize`即時的大小變更的通知控制器的視窗上的事件。 例如: 

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

或者，您可以使用`DidEndLiveResize`事件只會收到通知之後使用者已完成變更視窗的大小。 例如：

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

## <a name="setting-a-windows-title-and-represented-file"></a>設定視窗的標題，並表示檔案

使用代表文件的 windows 時`NSWindow`已經`DocumentEdited`屬性，如果設定為`true`[關閉] 按鈕，讓使用者能夠指出檔案已經過修改，以及應該儲存在關閉前顯示一個小點。

讓我們編輯我們`ViewController.cs`檔案，並進行下列變更：

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

我們也會監視`WillClose` 視窗和檢查的狀態事件`DocumentEdited`屬性。 如果是`true`我們要讓使用者能夠將變更儲存至檔案。 如果我們執行我們的應用程式，並輸入一些文字，將會顯示該點：

[![](window-images/file01.png "變更的視窗")](window-images/file01.png#lightbox)

如果我們嘗試關閉視窗，我們會收到警示：

[![](window-images/file02.png "顯示儲存對話方塊")](window-images/file02.png#lightbox)

如果我們從檔案載入文件我們可以設定視窗的標題為檔案的名稱並使用`window.SetTitleWithRepresentedFilename (Path.GetFileName(path));`方法 (假設`path`是字串，表示正在開啟的檔案)。 此外，我們可以在其中設定檔使用的 URL`window.RepresentedUrl = url;`方法。

如果 URL 會指向由 OS 已知檔案類型，它的圖示將顯示在標題列中。 如果使用者以滑鼠右鍵按一下此圖示，將會顯示檔案的路徑。

讓我們編輯`AppDelegate.cs`檔案，並新增下列方法：

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

現在如果我們執行我們的應用程式，請選取**開啟...** 從**檔案**功能表上，選取從文字檔**開啟**對話方塊方塊中，並將它開啟：

[![](window-images/file03.png "開啟的對話方塊")](window-images/file03.png#lightbox)

檔案將會顯示與標題會設定使用之檔案的圖示：

[![](window-images/file04.png "載入檔案的內容")](window-images/file04.png#lightbox)

<a name="Adding_a_New_Window_to_a_Project" />

## <a name="adding-a-new-window-to-a-project"></a>將新的視窗新增至專案

主要的文件視窗中，除了 Xamarin.Mac 應用程式可能需要其他類型的視窗顯示給使用者，例如喜好設定或偵測器面板。

若要加入新的視窗，執行下列作業：

1. 在 **方案總管**，按兩下`Main.storyboard`檔案將它開啟以在 Xcode 的 Interface Builder 中編輯。
2. 拖曳新**視窗控制器**從**程式庫**並將它放在**設計介面**:

    [![](window-images/new01.png "文件庫中選取新的視窗控制器")](window-images/new01.png#lightbox)
3. 在 **身分識別偵測器**，輸入`PreferencesWindow`如**分鏡腳本識別碼**: 

    [![](window-images/new02.png "設定分鏡腳本識別碼")](window-images/new02.png#lightbox)
5. 設計介面： 

    [![](window-images/new03.png "設計 UI")](window-images/new03.png#lightbox)
6. 開啟應用程式功能表 (`MacWindows`)，請選取**喜好設定...**，control + 按一下和拖曳至新的視窗： 

    [![](window-images/new05.png "建立的 segue")](window-images/new05.png#lightbox)
7. 選取 **顯示**快顯功能表。
6. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

如果我們執行程式碼，並選取**喜好設定...** 從**應用程式功能表**，視窗隨即出現：

[![](window-images/new04.png "範例偏好設定 功能表")](window-images/new04.png#lightbox)

<a name="Working_with_Panels" />

## <a name="working-with-panels"></a>使用面板

如本文開頭所述，面板會漂浮其他視窗上方，並提供工具或使用者可使用文件開啟時的控制項。 

就像任何其他類型的視窗中，建立和使用 Xamarin.Mac 應用程式中，處理程序大致相同：

1. 將新的視窗定義加入至專案。
2. 按兩下`.xib`来開啟的視窗設計在 Xcode 的 Interface Builder 中編輯檔案。
2. 設定任何所需的視窗屬性**屬性偵測器**並**大小偵測器**。
4. 在建置您的介面和設定它們所需的控制項中拖曳**屬性偵測器**。
5. 使用**大小偵測器**來處理您的 UI 項目調整大小。
6. 公開到 C# 程式碼透過視窗的 UI 項目**插座**並**動作**。
7. 儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。

在 **屬性偵測器**，您有特定面板的下列選項：

[![](window-images/panel03.png "屬性偵測器")](window-images/panel03.png#lightbox)

- **樣式**-可讓您調整從面板的樣式： 一般面板 （看起來像標準的視窗）、 公用程式的面板 （具有較小的標題列），抬頭顯示器面板 (是半透明的標題列，且 背景的一部分)。
- **非啟用**-決定要在 [面板] 中成為主要的視窗。
- **文件的強制回應**-如果文件強制回應，面板會只一直浮在應用程式的 windows，否則它會浮動勝於一切。


若要新增新的面板，請執行下列作業：

1. 在 **方案總管**，以滑鼠右鍵按一下專案，然後選取**新增** > **新檔案...**.
2. 在 [新增檔案] 對話方塊中，選取**Xamarin.Mac** > **與控制器的 Cocoa 視窗**:

    [![](window-images/panels00.png "加入新的視窗控制器")](window-images/panels00.png#lightbox)
3. 輸入 `DocumentPanel` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下`DocumentPanel.xib`以開啟它進行編輯介面產生器中的檔案： 

    [![](window-images/new02.png "編輯面板")](window-images/new02.png#lightbox)
5. 刪除現有的視窗，並將從面板拖曳**程式庫偵測器**中**介面編輯器**: 

    [![](window-images/panels01.png "刪除現有的視窗")](window-images/panels01.png#lightbox)
6. 連結 [面板] 中，最多**檔案的擁有者** - **視窗** - **插座**: 

    [![](window-images/panels02.png "拖曳面板的連結")](window-images/panels02.png#lightbox)
7. 切換至**身分識別偵測器**而設定的面板類別為`DocumentPanel`: 

    [![](window-images/panels03.png "設定的面板類別")](window-images/panels03.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。
7. 編輯`DocumentPanel.cs`檔案，並變更類別定義如下： 

    `public partial class DocumentPanel : NSPanel`
8. 將變更儲存到檔案。

編輯`AppDelegate.cs`檔案，並讓`DidFinishLaunching`方法外觀如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

如果我們執行我們的應用程式時，將會顯示 [面板] 中：

[![](window-images/panels04.png "執行中應用程式中的面板")](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> 面板 Windows 已被取代 apple，並應該取代成**檢查介面**。 如需完整的範例，建立**Inspector** Xamarin.Mac 應用程式，請參閱我們[MacInspector](https://developer.xamarin.com/samples/mac/MacInspector/)範例應用程式。

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解在 Xamarin.Mac 應用程式中使用的 Windows 」 和 「 面板。 我們會看到不同的類型，並使用 Windows 和面板、 如何建立和維護 Windows 和 Xcode 的 Interface Builder 中的面板和如何在 C# 程式碼中使用 Windows 」 和 「 面板。

## <a name="related-links"></a>相關連結

- [MacWindows （範例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [MacInspector （範例）](https://developer.xamarin.com/samples/mac/MacInspector/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用功能表](~/mac/user-interface/menu.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
