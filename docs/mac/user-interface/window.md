---
title: Xamarin 中的 Windows
description: 本文說明如何在 Xamarin 應用程式中使用 windows 和麵板。 它描述如何在 Xcode 和 Interface Builder 中建立視窗和麵板、從分鏡腳本和 xib 檔案載入它們，以及以程式設計方式使用它們。
ms.prod: xamarin
ms.assetid: 4F6C67E9-BBFF-44F7-B29E-AB47D7F44287
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 2cacbf5640da60a7d084bf4ab113ef62fad097d8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436390"
---
# <a name="windows-in-xamarinmac"></a>Xamarin 中的 Windows

_本文說明如何在 Xamarin 應用程式中使用 windows 和麵板。它描述如何在 Xcode 和 Interface Builder 中建立視窗和麵板、從分鏡腳本和 xib 檔案載入它們，以及以程式設計方式使用它們。_

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *目標 C* 和 *Xcode* 中運作的相同視窗和麵板。 由於 Xamarin 與 Xcode 直接整合，因此您可以使用 Xcode 的 _Interface Builder_ 來建立和維護您的 Windows 和麵板 (或選擇性地在 c # 程式碼) 中直接建立它們。

根據其用途，Xamarin 應用程式可能會在螢幕上顯示一或多個視窗，以管理及協調它所顯示的資訊並使用。 視窗的主要功能如下：

1. 提供可放置和管理檢視和控制項的區域。
2. 接受和回應事件，以回應使用者與鍵盤和滑鼠的互動。

Windows 可以在非模式狀態中使用 (例如可以開啟多個檔的文字編輯器) 或強制回應 (例如必須先關閉才能繼續) 應用程式的 [匯出] 對話方塊。

面板是一種特殊的視窗 (基類) 的子類別 `NSWindow` ，通常會在應用程式中提供輔助函式，例如文字格式偵測器和系統色彩選擇器之類的公用程式視窗。

[![在 Xcode 中編輯視窗](window-images/intro01.png)](window-images/intro01.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用 Windows 和麵板的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 C 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和命令。

## <a name="introduction-to-windows"></a>Windows 簡介

如上所述，視窗會提供一個區域，讓您可以根據使用者互動 (透過鍵盤或滑鼠) 來放置和管理檢視和控制項，以及回應事件。

根據 Apple，macOS 應用程式中有五種主要的 Windows 類型：

- **文件視窗** ：文件視窗包含以檔案為基礎的使用者資料，例如試算表或文字檔。
- 應用程式**視窗**-應用程式視窗是應用程式的主視窗，不是以檔為基礎的 (，例如 Mac) 上的行事曆應用程式。
- **面板** -面板會浮動在其他視窗上方，並提供使用者在檔開啟時可使用的工具或控制項。 在某些情況下，面板可以是半透明的 (例如使用大型圖形) 時。
- **對話方塊** -回應使用者動作時會出現對話方塊，而且通常會提供使用者完成動作的方式。 對話需要使用者的回應後才能關閉。  (參閱 [使用對話](~/mac/user-interface/dialog.md)) 
- **警示** -警示是一種特殊的對話類型，會在發生嚴重問題時出現 (例如錯誤) 或警告 (例如準備刪除檔案) 。 因為警示是對話方塊，所以它也需要使用者回應才能關閉。  (參閱 [使用警示](~/mac/user-interface/alert.md)) 

如需詳細資訊，請參閱 Apple [macOS 設計主題](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的「[關於 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) 」一節。

### <a name="main-key-and-inactive-windows"></a>主要、關鍵和非使用中視窗

Xamarin. Mac 應用程式中的 Windows 可以根據使用者目前與其互動的方式，以不同的方式進行外觀和行為。 目前著重于使用者注意力的最重要檔或應用程式視窗稱為 _主視窗_。 在大部分的情況下，此視窗也會是目前接受使用者輸入)  (視窗的 _主要視窗_ 。 但這不一定是這樣的情況，例如，色彩選擇器可能會開啟，而且是使用者正在與其互動的主要視窗，以變更檔視窗中專案的狀態 (這仍會是主視窗) 。

主要和主要的 Windows (如果它們是個別的) 一律為使用中，則非作用中的 _視窗_ 是開啟的視窗，不在前景。 例如，文字編輯器應用程式一次可以開啟多個檔，只有主視窗會處於作用中狀態，而其他則為非使用中。 

如需詳細資訊，請參閱 Apple [macOS 設計主題](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的「[關於 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowAppearanceBehavior.html#//apple_ref/doc/uid/20000957-CH33-SW1) 」一節。

### <a name="naming-windows"></a>命名視窗

視窗可以顯示標題列，而當標題顯示時，通常是應用程式的名稱、正在處理的檔案名稱或視窗的功能 (例如偵測器) 。 有些應用程式不會顯示標題列，因為它們可以看見，而且無法使用檔。

Apple 建議下列指導方針：

- 使用您的應用程式名稱作為主要、非文件視窗的標題。 
- 命名新的文件視窗 `untitled` 。 若為第一個新檔，請不要將數位附加至標題 (例如 `untitled 1`) 。 如果使用者在儲存前建立另一份新檔，並先將其標題為第一個檔，請呼叫該視窗 `untitled 2` 、等等 `untitled 3` 。

如需詳細資訊，請參閱 Apple [macOS 設計主題](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[命名 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowNaming.html#//apple_ref/doc/uid/20000957-CH35-SW1)一節。

### <a name="full-screen-windows"></a>全螢幕視窗

在 macOS 中，應用程式的視窗可以全螢幕隱藏所有內容，包括應用程式功能表列 (可以藉由將游標移至畫面頂端來顯示，) 以提供與內容的自由互動。

Apple 建議下列指導方針：

- 判斷視窗是否可全螢幕顯示是否合理。 提供短暫互動 (例如計算機的應用程式) 不應提供全螢幕模式。
- 如果全螢幕工作需要，請顯示工具列。 在全螢幕模式下，通常會隱藏工具列。
- 全螢幕視窗應該具有使用者完成工作所需的所有功能。
- 可能的話，請在使用者處於全螢幕視窗時，避免搜尋工具互動。
- 利用增加的螢幕空間，而不將焦點移離主要工作。

如需詳細資訊，請參閱 Apple [macOS 設計主題](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的[全螢幕 Windows](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/FullScreen.html#//apple_ref/doc/uid/20000957-CH61-SW1)一節。

### <a name="panels"></a>窗格

面板是一個次要視窗，其中包含的控制項和選項會影響作用中的檔或選取範圍 (例如系統色彩選擇器) ：

[![色彩面板](window-images/panel01.png)](window-images/panel01.png#lightbox)

面板可以是 _應用程式特定_ 或全 _系統_。 應用程式特定的面板會在應用程式的文件視窗上方浮動，並在應用程式位於背景時消失。 全系統面板 (例如 [字型] 面板) ，無論應用程式為何， **都在所有** 開啟的視窗上方浮動。 

Apple 建議下列指導方針：

- 一般情況下，使用標準面板時，透明面板應僅謹慎使用，且需要大量的工作。
- 請考慮使用面板，讓使用者輕鬆存取重要的控制項，或直接影響其工作的資訊。
- 視需要隱藏和顯示面板。
- 面板應該一律包含標題列。
- 面板不應包含有效的最小化按鈕。

#### <a name="inspectors"></a>督察

大部分新式 macOS 應用程式都會提供輔助控制項和選項，以影響使用中的檔或選取專案作為主 (視窗的偵測 _器，如_ 下列) 所示的 **頁面** 應用程式，而不是使用面板視窗：

[![範例偵測器](window-images/panel02.png)](window-images/panel02.png#lightbox)

如需詳細資訊，請參閱 Apple [macOS 設計主題](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)的 [[面板](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowPanels.html#//apple_ref/doc/uid/20000957-CH42-SW1)] 區段和我們的[MacInspector](/samples/xamarin/mac-samples/macinspector)範例應用程式，以完整執行 Xamarin 應用程式中的偵測**器介面**。

## <a name="creating-and-maintaining-windows-in-xcode"></a>在 Xcode 中建立和維護視窗

當您建立新的 Xamarin Cocoa 應用程式時，預設會取得標準空白的視窗。 此視窗定義于 `.storyboard` 自動包含在專案中的檔案。 若要編輯您的 windows 設計，請在 [ **方案總管**中，按兩下該檔案 `Main.storyboard` ：

[![選取主要分鏡腳本](window-images/edit01.png)](window-images/edit01.png#lightbox)

這會開啟 Xcode Interface Builder 中的視窗設計：

[![在 Xcode 中編輯 UI](window-images/edit02.png)](window-images/edit02.png#lightbox)

在 **屬性偵測器**中，您可以使用數個屬性來定義及控制視窗：

- **標題** -這是要顯示在視窗標題列中的文字。
- **自動** 儲存-這是在視窗的位置，以及自動儲存設定時將用來識別碼的索引 _鍵_ 。
- **標題列** -視窗會顯示標題列。
- **統一標題和工具列** -如果視窗包含工具列，則應該是標題列的一部分。
- **完整大小的內容視圖** -允許視窗的內容區域位於標題列下方。
- **陰影** -視窗是否有陰影。
- 有**紋理**紋理的 windows 可以使用效果 (例如 vibrancy) ，並可在其主體上的任何位置拖曳來四處移動。
- **Close** -視窗有 [關閉] 按鈕。
- **最小化** -視窗具有最小化按鈕。
- 重**設大小**-視窗具有調整大小控制項。
- **工具列按鈕** -視窗是否有隱藏/顯示工具列按鈕。
- 可**還原**-視窗的位置和設定會自動儲存和還原。
- **在啟動時顯示** -這是載入檔案時自動顯示的視窗 `.xib` 。
- **在停用時隱藏** -這是應用程式進入背景時隱藏的視窗。
- **在關閉時發行** -這是在關閉時從記憶體中清除的視窗。
- **一律顯示工具提示** -會不斷顯示工具提示。
- 重新**計算 View Loop** -這是在繪製視窗之前重新計算的視圖順序。
- **空格**、 **Exposé** 和 **迴圈** -全都定義視窗在這些 macOS 環境中的運作方式。 
- **全螢幕** -決定此視窗是否可進入全螢幕模式。 
- **動畫** -控制視窗可使用的動畫類型。
- **外觀** -控制視窗的外觀。 現在只有一個外觀，也就是綠色。

如需詳細資訊，請參閱 Apple 的 [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1) 和 [NSWindow](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/index.html#//apple_ref/occ/cl/NSWindow) 檔。

### <a name="setting-the-default-size-and-location"></a>設定預設大小和位置

若要設定視窗的初始位置並控制其大小，請切換至 **大小偵測器**：

[![預設大小和位置](window-images/edit07.png)](window-images/edit07.png#lightbox)

您可以從這裡設定視窗的初始大小、為它提供最小和最大大小、設定畫面上的初始位置，以及控制視窗周圍的框線。

### <a name="setting-a-custom-main-window-controller"></a>設定自訂主視窗控制器

為了能夠建立輸出和動作，以將 UI 元素公開給 c # 程式碼，Xamarin 應用程式必須使用自訂視窗控制器。

執行下列動作：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的分鏡腳本。
2. 選取 `NSWindowController` Design Surface 中的。
3. 切換至身分 **識別偵測器** 視圖，並輸入 `WindowController` 為 **類別名稱**： 

    [![設定類別名稱](window-images/windowcontroller01.png)](window-images/windowcontroller01.png#lightbox)
4. 儲存您的變更，並返回 Visual Studio for Mac 進行同步處理。
5. 檔案 `WindowController.cs` 將會新增到您的專案中 Visual Studio for Mac 的 **方案總管** ： 

    [![選取 windows 控制器](window-images/windowcontroller02.png)](window-images/windowcontroller02.png#lightbox)
6. 在 Xcode 的 Interface Builder 中重新開啟分鏡腳本。
7. 檔案 `WindowController.h` 將可供使用： 

    [![編輯 WindowController .h 檔案](window-images/windowcontroller03.png)](window-images/windowcontroller03.png#lightbox)

### <a name="adding-ui-elements"></a>加入 UI 元素

若要定義視窗的內容，請從程式庫偵測 **器** 將控制項拖曳至 **介面編輯器**。 如需有關使用 Interface Builder 來建立和啟用控制項的詳細資訊，請參閱 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 檔的簡介。

例如，讓我們將工具列從連結 **庫偵測器** 拖曳至 **介面編輯器**中的視窗：

[![從文件庫選取工具列](window-images/edit03.png)](window-images/edit03.png#lightbox)

接下來，拖曳 **文字視圖** 並調整其大小，以填滿工具列下的區域：

[![加入文字視圖](window-images/edit04.png)](window-images/edit04.png#lightbox)

由於我們希望 **文字視圖** 在視窗的大小變更時壓縮和成長，讓我們切換至 **條件約束編輯器** ，並新增下列條件約束：

[![編輯條件約束](window-images/edit05.png)](window-images/edit05.png#lightbox)

藉由按一下編輯器頂端的四個 **紅色 I 字形狀** ，然後按一下 [ **加入4個條件約束**]，我們會通知文字視圖在調整視窗大小時，水準和垂直放大或縮小指定的 X、Y 座標。

最後，使用**輸出**將**文字視圖**公開給程式碼 (請務必選取檔案 `ViewController.h`) ：

[![設定輸出](window-images/edit06.png)](window-images/edit06.png#lightbox)

儲存您的變更並切換回 Visual Studio for Mac，以與 Xcode 同步。

如需有關使用 **輸出** 和 **動作**的詳細資訊，請參閱我們的 [輸出口和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 檔。

### <a name="standard-window-workflow"></a>標準時間範圍工作流程

針對您在 Xamarin. Mac 應用程式中建立及使用的任何視窗，此程式基本上與先前所做的一樣：

1. 針對不是自動新增至專案的新視窗，請在專案中加入新的視窗定義。 以下將詳細討論這項資訊。
1. 按兩下檔案 `Main.storyboard` 以開啟 [Xcode] Interface Builder 中編輯的視窗設計。
1. 將新視窗拖曳至消費者介面的設計，並使用_segue_ (將視窗連結到主視窗中。如需詳細資訊，請參閱[我們使用](~/mac/platform/storyboards/indepth.md)分鏡指令檔) 的[segue](~/mac/platform/storyboards/indepth.md#Segues)一節。
1. 在 **屬性偵測器** 和 **大小偵測器**中設定任何必要的視窗屬性。
1. 拖曳建立您的介面所需的控制項，並在屬性偵測 **器**中進行設定。
1. 使用 **大小偵測器** 來處理 UI 元素的調整大小。
1. 透過 **輸出** 和 **動作**，將視窗的 UI 元素公開到 c # 程式碼。
1. 儲存您的變更並切換回 Visual Studio for Mac，以與 Xcode 同步。

現在我們已建立基本的視窗，接下來我們將探討 Xamarin Mac 應用程式在使用 windows 時的一般處理常式。 

## <a name="displaying-the-default-window"></a>顯示預設視窗

根據預設，新的 Xamarin 應用程式會在啟動時自動顯示檔案中定義的視窗 `MainWindow.xib` ：

[![執行的範例視窗](window-images/display01.png)](window-images/display01.png#lightbox)

由於我們已修改上述視窗的設計，因此現在會包含預設的工具列和 **文字視圖** 控制項。 檔案中的下列區段 `Info.plist` 負責顯示此視窗：

[![編輯資訊. plist](window-images/display00.png)](window-images/display00.png#lightbox)

**主要介面**下拉式清單用來選取將作為主要應用程式 UI 的分鏡腳本 (在此案例中 `Main.storyboard`) 。

系統會自動將視圖控制器新增至專案，以控制顯示 (的主視窗，以及其主要視圖) 。 它是在檔案中定義 `ViewController.cs` ，並且附加到身分**識別偵測器**下 Interface Builder 中的檔案**擁有**者：

[![設定檔案的擁有者](window-images/display02.png)](window-images/display02.png#lightbox)

在視窗中，我們想要在 `untitled` 第一次開啟時使用標題，讓我們覆寫中的 `ViewWillAppear` 方法，如下 `ViewController.cs` 所示：

```csharp
public override void ViewWillAppear ()
{
    base.ViewWillAppear ();

    // Set Window Title
    this.View.Window.Title = "untitled";
}
```    

> [!NOTE]
> 視窗的 `Title` 屬性是在方法中設定，而不是在方法中設定， `ViewWillAppear` `ViewDidLoad` 因為當此視圖可能載入記憶體時，尚未完全具現化。 存取 `Title` 方法中的屬性， `ViewDidLoad` 我們將會收到 `null` 例外狀況，因為視窗尚未經過建立和連接至屬性。

## <a name="programmatically-closing-a-window"></a>以程式設計方式關閉視窗

有時候您可能會想要以程式設計方式關閉 Xamarin 應用程式中的視窗，而不是讓使用者按一下視窗的 [ **關閉** ] 按鈕或使用功能表項目。 macOS 提供兩種不同的方式，以程式設計 `NSWindow` 方式關閉： `PerformClose` 和 `Close` 。

### <a name="performclose"></a>PerformClose

呼叫的 `PerformClose` 方法可 `NSWindow` 模擬使用者按一下視窗的 [ **關閉** ] 按鈕，方法是以立即反白顯示按鈕，然後關閉視窗。

如果應用程式會執行 `NSWindow` 的 `WillClose` 事件，則會在關閉視窗之前引發。 如果事件傳回 `false` ，則不會關閉視窗。 如果視窗沒有 [ **關閉** ] 按鈕，或因為任何原因而無法關閉，則 OS 將發出警示音效。

例如：

```csharp
MyWindow.PerformClose(this);
```

會嘗試關閉 `MyWindow` `NSWindow` 實例。 如果成功，視窗將會關閉，否則會發出警示音效，而將保持開啟狀態。

### <a name="close"></a>關閉

呼叫的 `Close` 方法不 `NSWindow` 會藉由立即反白顯示按鈕來模擬使用者按一下視窗的 [ **關閉** ] 按鈕，只會關閉視窗。

視窗不一定要關閉，且將會在 `NSWindowWillCloseNotification` 關閉視窗的預設通知中心張貼通知。

`Close`方法與方法有兩個重要的方法 `PerformClose` ：

1. 它不會嘗試引發 `WillClose` 事件。
2. 它不會藉由立即反白顯示按鈕，來模擬使用者按一下 [ **關閉** ] 按鈕。

例如：

```csharp
MyWindow.Close();
```

會關閉 `MyWindow` `NSWindow` 實例。

## <a name="modified-windows-content"></a>修改過的 windows 內容

在 macOS 中，Apple 提供了一種方法來通知使用者 () 的視窗內容已被 `NSWindow` 使用者修改，需要儲存。 如果視窗包含修改過的內容，則小的黑色點會顯示在其 **關閉** 小工具中：

[![具有已修改標記的視窗](window-images/close01.png)](window-images/close01.png#lightbox)

如果使用者嘗試在有未儲存的視窗內容變更時關閉視窗或結束 Mac 應用程式，您應該會顯示 [對話方塊](~/mac/user-interface/dialog.md) 或強制回應 [表](~/mac/user-interface/dialog.md) ，並允許使用者先儲存其變更：

[![視窗關閉時顯示的儲存工作表](window-images/close02.png)](window-images/close02.png#lightbox)

### <a name="marking-a-window-as-modified"></a>將視窗標示為已修改

若要將視窗標示為已修改的內容，請使用下列程式碼：

```csharp
// Mark Window content as modified
Window.DocumentEdited = true;
```

一旦儲存變更後，請使用下列程式清除修改過的旗標：

```csharp
// Mark Window content as not modified
Window.DocumentEdited = false;
```

### <a name="saving-changes-before-closing-a-window"></a>在關閉視窗前儲存變更

若要監看使用者關閉視窗，並讓他們事先儲存修改過的內容，您必須建立的子類別， `NSWindowDelegate` 並覆寫它的 `WindowShouldClose` 方法。 例如：

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

使用下列程式碼，將這個委派的實例附加至視窗：

```csharp
// Set delegate
Window.Delegate = new EditorWindowDelegate(Window);
```

### <a name="saving-changes-before-closing-the-app"></a>在關閉應用程式前儲存變更

最後，您的 Xamarin 應用程式應該檢查是否有任何 Windows 包含修改過的內容，並允許使用者在結束之前儲存變更。 若要這樣做，請編輯您 `AppDelegate.cs` 的檔案，並覆寫 `ApplicationShouldTerminate` 方法，使其看起來如下所示：

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

## <a name="working-with-multiple-windows"></a>使用多個視窗

大部分以檔為基礎的 Mac 應用程式可以同時編輯多份檔。 例如，文字編輯器可以同時開啟多個文字檔來進行編輯。 依預設，新的 Xamarin 應用程式具有 [檔案 **] 功能表，** 其中 **新** 的專案會自動連接到 `newDocument:` **動作**。

下列程式碼會啟用這個新的專案，並允許使用者開啟主視窗的多個複本，同時編輯多份檔。

編輯檔案 `AppDelegate.cs` ，並新增下列計算屬性：

```csharp
public int UntitledWindowCount { get; set;} =1;
```

使用此項來追蹤未儲存的檔案數目，讓我們可以將意見反應提供給使用者，如以上所述的每個 Apple 指導方針所述 () 。

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

這段程式碼會建立新版本的視窗控制器、載入新視窗、讓它成為主視窗和主要視窗，然後設定它的標題。 現在，如果我們執行應用程式，並**從 [檔案**] 功能表選取 [**新增**]，將會開啟並顯示新的編輯器視窗：

[![新增了新的未命名視窗](window-images/display04.png)](window-images/display04.png#lightbox)

如果開啟 [ **Windows** ] 功能表，您可以看到應用程式自動追蹤和處理開啟的視窗：

[![Windows 功能表](window-images/display05.png)](window-images/display05.png#lightbox)

如需在 Xamarin 應用程式中使用功能表的詳細資訊，請參閱我們 [的使用功能表](~/mac/user-interface/menu.md) 檔。

### <a name="getting-the-currently-active-window"></a>取得目前使用中視窗

在可開啟多個 windows (檔) 的 Xamarin 應用程式中，有時您會需要取得最新的視窗， (索引鍵視窗) 。 下列程式碼會傳回索引鍵視窗：

```csharp
var window = NSApplication.SharedApplication.KeyWindow;
```

您可以在需要存取目前索引鍵視窗的任何類別或方法中呼叫它。 如果目前沒有開啟視窗，則會傳回 `null` 。

### <a name="accessing-all-app-windows"></a>存取所有應用程式視窗

有時您可能需要存取您的 Xamarin 應用程式目前已開啟的所有視窗。 例如，若要查看使用者想要開啟的檔案是否已在結束視窗中開啟。

`NSApplication.SharedApplication`會維護一個 `Windows` 屬性，其中包含應用程式中所有開啟視窗的陣列。 您可以逐一查看此陣列，以存取應用程式的所有目前視窗。 例如：

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

在範例程式碼中，我們會將每個傳回的視窗轉換為 `ViewController` 應用程式中的自訂類別，並 `Path` 針對使用者想要開啟的檔案路徑測試自訂屬性的值。 如果檔案已經開啟，我們會將該視窗帶到前方。

## <a name="adjusting-the-window-size-in-code"></a>調整程式碼中的視窗大小

有時候應用程式需要在程式碼中調整視窗的大小。 若要調整視窗的大小和位置，請調整它的 `Frame` 屬性。 調整視窗的大小時，您通常也需要調整它的原點，將視窗保持在相同的位置，因為 macOS 的座標系統。

不同于 iOS，其中左上角代表 (0，0) ，macOS 會使用數學座標系統，其中螢幕左下角代表 (0，0) 。 在 iOS 中，當您向右移動時，座標會增加。 在 macOS 中，座標值會向上增加到右邊。 

下列程式碼範例會調整視窗的大小：

```csharp
nfloat y = 0;

// Calculate new origin
y = Frame.Y - (768 - Frame.Height);

// Resize and position window
CGRect frame = new CGRect (Frame.X, y, 1024, 768);
SetFrame (frame, true);
```

> [!IMPORTANT]
> 當您在程式碼中調整 windows 大小和位置時，您必須確定您已遵循 Interface Builder 中所設定的最小和最大大小。 這將不會自動被接受，而且您可以讓視窗變大或小於這些限制。

## <a name="monitoring-window-size-changes"></a>監看視窗大小變更

有時您可能需要在您的 Xamarin Mac 應用程式內監看視窗大小的變更。 例如，重繪內容以符合新的大小。

若要監視大小變更，請先確定您已在 Xcode 的 Interface Builder 中為視窗控制器指派自訂類別。 例如， `MasterWindowController` 在下列內容中：

[![身分識別偵測器](window-images/resize01.png)](window-images/resize01.png#lightbox)

接下來，編輯自訂視窗控制器類別，並監視 `DidResize` 控制器視窗上的事件，以接收即時大小變更的通知。 例如：

```csharp
public override void WindowDidLoad ()
{
    base.WindowDidLoad ();

    Window.DidResize += (sender, e) => {
        // Do something as the window is being live resized
    };
}
```

（選擇性）您可以使用 `DidEndLiveResize` 事件，只有在使用者完成變更視窗的大小時，才會收到通知。 例如：

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

## <a name="setting-a-windows-title-and-represented-file"></a>設定視窗的標題和表示的檔案

使用代表檔的視窗時， `NSWindow` 的 `DocumentEdited` 屬性會設定為 `true` 在 [關閉] 按鈕中顯示一個小點，讓使用者指出檔案已修改，而且應該在關閉之前儲存。

讓我們編輯檔案 `ViewController.cs` 並進行下列變更：

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

我們也會 `WillClose` 在視窗上監視事件，並檢查屬性的狀態 `DocumentEdited` 。 如果 `true` 需要讓使用者能夠將變更儲存至檔案。 如果我們執行應用程式並輸入一些文字，則會顯示該點：

[![已變更的視窗](window-images/file01.png)](window-images/file01.png#lightbox)

如果您嘗試關閉視窗，就會收到警示：

[![顯示儲存對話方塊](window-images/file02.png)](window-images/file02.png#lightbox)

如果您是從檔案載入檔，請使用方法將視窗的標題設定為檔案的名稱 `window.SetTitleWithRepresentedFilename (Path.GetFileName(path));` (指定 `path` 為字串，表示) 開啟的檔案。 此外，您也可以使用方法來設定檔案的 URL `window.RepresentedUrl = url;` 。

如果 URL 指向 OS 已知的檔案類型，其圖示就會顯示在標題列中。 如果使用者以滑鼠右鍵按一下圖示，將會顯示該檔案的路徑。

編輯檔案 `AppDelegate.cs` ，並新增下列方法：

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

現在，如果我們執行應用程式，請從 [檔案 **] 功能表選取 [** **開啟 ...** ]，從 [**開啟**] 對話方塊中選取文字檔，然後將它開啟：

[![開啟的對話方塊](window-images/file03.png)](window-images/file03.png#lightbox)

檔案隨即顯示，並且會以檔案的圖示來設定標題：

[![載入的檔案內容](window-images/file04.png)](window-images/file04.png#lightbox)

## <a name="adding-a-new-window-to-a-project"></a>將新視窗加入至專案

除了主文件視窗之外，Xamarin 應用程式可能需要向使用者顯示其他類型的視窗，例如喜好設定或偵測器面板。

若要加入新的視窗，請執行下列動作：

1. 在 [ **方案總管**中，按兩下檔案 `Main.storyboard` 將其開啟，以在 Xcode 的 Interface Builder 中進行編輯。
2. 從連結**庫**拖曳新的**視窗控制器**，然後將它放在**Design Surface**：

    [![在程式庫中選取新的視窗控制器](window-images/new01.png)](window-images/new01.png#lightbox)
3. 在身分 **識別偵測器**中，輸入分鏡腳本 `PreferencesWindow` **識別碼**： 

    [![設定分鏡腳本識別碼](window-images/new02.png)](window-images/new02.png#lightbox)
4. 設計您的介面： 

    [![設計 UI](window-images/new03.png)](window-images/new03.png#lightbox)
5. 開啟 [應用程式] 功能表 (`MacWindows`) ，選取 [ **喜好設定 ...**]，然後按一下控制項並拖曳至新視窗： 

    [![建立 segue](window-images/new05.png)](window-images/new05.png#lightbox)
6. 從快顯功能表中選取 [ **顯示** ]。
7. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

如果我們執行程式碼，並從 [**應用程式] 功能表**中選取 [**喜好設定 ...** ]，將會顯示視窗：

[![範例喜好設定功能表](window-images/new04.png)](window-images/new04.png#lightbox)

## <a name="working-with-panels"></a>使用面板

如同本文開頭所述，面板會浮動在其他視窗上方，並提供使用者在檔開啟時可使用的工具或控制項。 

就像您在 Xamarin 應用程式中建立及使用的任何其他類型的視窗一樣，程式基本上是一樣的：

1. 將新的視窗定義加入至專案。
2. 按兩下檔案 `.xib` 以開啟 [Xcode] Interface Builder 中編輯的視窗設計。
3. 在 **屬性偵測器** 和 **大小偵測器**中設定任何必要的視窗屬性。
4. 拖曳建立您的介面所需的控制項，並在屬性偵測 **器**中進行設定。
5. 使用 **大小偵測器** 來處理 UI 元素的調整大小。
6. 透過 **輸出** 和 **動作**，將視窗的 UI 元素公開到 c # 程式碼。
7. 儲存您的變更並切換回 Visual Studio for Mac，以與 Xcode 同步。

在 [ **屬性偵測器**] 中，您可以使用面板特定的下列選項：

[![屬性偵測器](window-images/panel03.png)](window-images/panel03.png#lightbox)

- **樣式** -可讓您從下列專案調整面板的樣式：一般面板 (看起來像標準視窗) 、公用程式面板 (有較小的標題列) 、抬頭顯示器面板 (是半透明的，且標題列是背景) 的一部分。
- **非啟用** -在面板中決定會成為索引鍵視窗。
- **檔** 強制回應-如果檔強制回應，面板只會在應用程式視窗的上方浮動，否則它會浮動在上方。

若要加入新的面板，請執行下列動作：

1. 在 [**方案總管**中，以滑鼠右鍵按一下專案，然後**選取 [**  >  **新增檔案 ...**]。
2. 在 [新增檔案] 對話方塊中，選取 [ **Xamarin**  >  **] Cocoa 視窗與控制器**：

    [![新增視窗控制器](window-images/panels00.png)](window-images/panels00.png#lightbox)

3. 輸入 `DocumentPanel` 作為 [名稱]****，然後按一下 [新增]**** 按鈕。
4. 按兩下檔案 `DocumentPanel.xib` 將其開啟，以在 Interface Builder 中編輯： 

    [![編輯面板](window-images/new02.png)](window-images/new02.png#lightbox)

5. 從**介面編輯器**中的連結**庫偵測器**，刪除現有的視窗並拖曳面板： 

    [![刪除現有視窗](window-images/panels01.png)](window-images/panels01.png#lightbox)

6. 將面板掛到檔案**的擁有**者  -  **視窗**  -  **輸出**： 

    [![拖曳以連接面板](window-images/panels02.png)](window-images/panels02.png#lightbox)

7. 切換至身分 **識別偵測器** ，並將面板的類別設定為 `DocumentPanel` ： 

    [![設定面板的類別](window-images/panels03.png)](window-images/panels03.png#lightbox)

8. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。
9. 編輯檔案 `DocumentPanel.cs` ，並將類別定義變更為下列內容： 

    `public partial class DocumentPanel : NSPanel`

10. 將變更儲存至檔案。

編輯檔案 `AppDelegate.cs` ，讓 `DidFinishLaunching` 方法看起來如下所示：

```csharp
public override void DidFinishLaunching (NSNotification notification)
{
        // Display panel
    var panel = new DocumentPanelController ();
    panel.Window.MakeKeyAndOrderFront (this);
}

```

當我們執行應用程式時，將會顯示面板：

[![正在執行之應用程式中的面板](window-images/panels04.png)](window-images/panels04.png#lightbox)

> [!IMPORTANT]
> 面板視窗已被 Apple 取代，應取代為 **偵測器介面**。 如需在 Xamarin 應用程式中建立偵測 **器** 的完整範例，請參閱我們的 [MacInspector](/samples/xamarin/mac-samples/macinspector) 範例應用程式。

## <a name="summary"></a>摘要

本文將詳細說明如何在 Xamarin 應用程式中使用 Windows 和麵板。 我們看到了不同的 Windows 和麵板類型和用途、如何建立和維護 Xcode Interface Builder 中的 Windows 和麵板，以及如何在 c # 程式碼中使用 Windows 和麵板。

## <a name="related-links"></a>相關連結

- [MacWindows (範例) ](/samples/xamarin/mac-samples/macwindows)
- [MacInspector (範例) ](/samples/xamarin/mac-samples/macinspector)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用功能表](~/mac/user-interface/menu.md)
- [ (Apple) macOS 設計主題 ](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [ (Apple) 的 Windows、面板和螢幕 ](https://developer.apple.com/documentation/appkit/windows_panels_and_screens)