---
title: ".xib 檔案"
description: "本文涵蓋.xib 檔案 Xcode 的建立和維護 Xamarin.Mac 應用程式的使用者介面的介面產生器中建立的工作。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 675B9405-D9A7-49F0-94AD-417F10A71D11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 8ca187b86126c9a0f2d9931f63d75e99ac4d2b23
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="xib-files"></a>.xib 檔案

_本文涵蓋.xib 檔案 Xcode 的建立和維護 Xamarin.Mac 應用程式的使用者介面的介面產生器中建立的工作。_

> [!NOTE]
> 若要建立 Xamarin.Mac 應用程式的使用者介面的慣用的方法是使用分鏡腳本。 這份文件已被保留在原處，由於歷史原因和處理較舊的 Xamarin.Mac 專案。 如需詳細資訊，請參閱我們[簡介分鏡腳本](~/mac/platform/storyboards/index.md)文件。

## <a name="overview"></a>總覽

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同的使用者介面項目與工具工作的開發人員*Objective-C*和*Xcode*沒有。 由於直接與 Xcode 整合 Xamarin.Mac，您可以使用 Xcode 的_介面產生器_建立，並維護您的使用者介面 （或您可以選擇直接在 C# 程式碼中建立它們）。

.Xib 檔案正由 macOS Xcode 的介面產生器中以圖形方式定義應用程式的使用者介面 （例如功能表、 Windows、 檢視、 標籤、 文字欄位），建立和維護的項目。

[![執行中應用程式的範例](xib-images/intro01.png "執行的應用程式的範例")](xib-images/intro01-large.png)

在本文中，我們將討論使用.xib Xamarin.Mac 應用程式中的檔案的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)文件編號第一次，因為它涵蓋了重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / Objective C 的方法](~/mac/internals/how-it-works.md)區段[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 Objective-C 物件和 UI 項目。


## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 簡介

Xcode 的一部分，Apple 已建立名為介面產生器，可讓您以視覺化方式設計工具中建立您的使用者介面的工具。 Xamarin.Mac fluently 整合介面產生器中，可讓您利用 Objective-C 使用者執行的相同工具建立 UI。


### <a name="components-of-xcode"></a>Xcode 的元件

當您開啟.xib 檔案在 Xcode 從 Visual Studio 中的 Mac 時，則會以**專案導覽器**左側**介面階層架構**和**介面編輯器**中間與**屬性和公共事業**右邊的區段：

[![Xcode UI 元件](xib-images/xcode03.png "Xcode UI 的元件")](xib-images/xcode03-large.png)

讓我們看看哪些每一種在 Xcode 區段功能以及如何將會使用它們來建立 Xamarin.Mac 應用程式的介面。


#### <a name="project-navigation"></a>專案功能

當您開啟.xib 檔案在 Xcode 中編輯時，適用於 Mac 的 Visual Studio 會建立通訊本身與 Xcode 之間變更背景的 Xcode 專案檔。 更新版本中，當您切換回 Visual Studio for Mac 從 Xcode，對這個專案進行任何變更會同步處理與 Xamarin.Mac 專案由 Visual Studio for mac。

**專案瀏覽**區段可讓您瀏覽之間的所有檔案構成這個_填充碼_Xcode 專案。 一般而言，您就只能.xib 檔案，例如這份清單中有興趣**MainMenu.xib**和**MainWindow.xib**。


#### <a name="interface-hierarchy"></a>介面階層架構

**介面階層架構**區段可讓您輕鬆地存取使用者介面的數個索引鍵屬性例如其**預留位置**和主要**視窗**。 您也可以使用本節來存取個別元素 (views) 可讓您的使用者介面和調整它們內嵌在階層內拖曳周圍的方式。


#### <a name="interface-editor"></a>介面編輯器

**介面編輯器**> 一節提供的介面上您以圖形方式來配置您的使用者介面。 拖曳項目從**文件庫**區段**屬性和公共事業**區段來建立您的設計。 當使用者介面項目 (views) 加入至設計介面上時，會將它們加入**介面階層架構**> 一節中出現的順序**介面編輯器**。


#### <a name="properties--utilities"></a>屬性和公用程式

**屬性和公共事業**區段 devided 分成兩個主要區段，我們即將使用**屬性**（也稱為偵測器） 和**文件庫**:

![屬性偵測器](xib-images/xcode04.png "屬性偵測器")

一開始本節幾乎是空的不過如果您選取的項目**介面編輯器**或**介面階層架構**、**屬性**區段就會填入。指定的項目和屬性，您可以調整的相關資訊。

在 [屬性] 區段內，共有 8 個不同的「偵測器索引標籤」，如下圖所示：

[![所有檢查概觀](xib-images/xcode05.png "所有偵測器的概觀")](xib-images/xcode05-large.png)

這些索引標籤從左到右分別是：

-   **檔案偵測器** – 檔案偵測器顯示檔案資訊，例如正在編輯之 Xib 檔案的檔案名稱和位置。
-   **快速說明** – [快速說明] 索引標籤提供內容相關的說明，以在 Xcode 中選取的項目為基礎。
-   **身分識別偵測器** – 身分識別偵測器提供關於所選控制項/檢視的相關資訊。
-   **屬性偵測器**– 屬性偵測器可讓您自訂的選取控制項/檢視表的各種屬性。
-   **大小 Inspector** – 大小偵測器可讓您控制的大小和調整大小行為的選取控制項/檢視表。
-   **連線檢查**– 連接偵測器顯示的輸出和動作連接選取控制項。 一會兒，我們將檢驗插座和動作。
-   **繫結 Inspector** – 繫結器可讓您將控制項設定其值會自動繫結到資料模型。
-   **檢視效果 Inspector** – 檢視效果偵測器可讓您指定的控制項，例如動畫效果。

在**文件庫** 區段中，您可以找到控制項和物件以圖形方式放置於設計工具，以建置您的使用者介面：

![程式庫偵測器的範例](xib-images/xcode06.png "程式庫偵測器的範例")

既然您已熟悉 Xcode IDE 和介面產生器，讓我們看看使用它來建立使用者介面。


## <a name="creating-and-maintaining-windows-in-xcode"></a>建立和維護 windows 在 Xcode 中

建立 Xamarin.Mac 應用程式的使用者介面的慣用的方法是使用分鏡腳本 (請參閱我們[簡介分鏡腳本](~/mac/platform/storyboards/index.md)文件的詳細資訊)，如此一來，任何新的專案啟動 Xamarin.Mac 將依預設使用分鏡腳本。

若要切換到使用.xib UI，請執行下列作業：

1. 開啟 Visual Studio for Mac 並啟動新的 Xamarin.Mac 專案。
2. 在**方案板**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**
3. 選取**Mac** > **Windows 控制器**:

    ![將新的視窗控制站新增](xib-images/setup00.png "加入新的視窗控制站")
4. 輸入`MainWindow`的名稱，然後按一下**新增**按鈕：

    ![加入新的主視窗](xib-images/setup01.png "加入新的主視窗")
5. 以滑鼠右鍵按一下專案上一次，然後選取**新增** > **新的檔案...**
6. 選取**Mac** > **主功能表**:

    ![加入新的主功能表](xib-images/setup02.png "加入新的主功能表")
7. 保留相同的名稱。`MainMenu`按一下**新增** 按鈕。
8. 在**方案板**選取**Main.storyboard**檔案，以滑鼠右鍵按一下並選取**移除**:

    ![選取 主要腳本](xib-images/setup03.png "選取主要腳本")
9. 在 移除 對話方塊中，按一下 **刪除**按鈕：

    ![確認刪除](xib-images/setup04.png "確認刪除作業")
10. 在**方案板**，連按兩下**Info.plist**檔案，以開啟它進行編輯。
11. 選取`MainMenu`從**主要介面**下拉式清單中：

    [![設定主功能表](xib-images/setup05.png "設定主功能表")](xib-images/setup05-large.png)
12. 在**方案板**，連按兩下**MainMenu.xib**檔案以開啟 Xcode 的介面產生器中進行編輯。
13. 在**程式庫偵測器**，型別`object`搜尋 欄位中然後拖曳新**物件**拖曳至設計介面：

    [![編輯主功能表](xib-images/setup06.png "編輯主功能表")](xib-images/setup06-large.png)
14. 在**識別 Inspector**，輸入`AppDelegate`如**類別**:

    [![選取應用程式委派](xib-images/setup07.png "選取應用程式委派")](xib-images/setup07-large.png)
15. 選取**檔案的擁有者**從**介面階層架構**，切換至**連接偵測器**與拖曳一條線從要委派`AppDelegate` **物件**剛加入至專案：

    [![連接應用程式委派](xib-images/setup08.png "連接應用程式委派")](xib-images/setup08-large.png)
16. 儲存變更並返回 Visual Studio for Mac。

在進行這些變更，編輯**AppDelegate.cs**檔案，並讓它看起來如下所示：

```csharp
using AppKit;
using Foundation;

namespace MacXib
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        public MainWindowController mainWindowController { get; set; }

        public AppDelegate ()
        {
        }

        public override void DidFinishLaunching (NSNotification notification)
        {
            // Insert code here to initialize your application
            mainWindowController = new MainWindowController ();
            mainWindowController.Window.MakeKeyAndOrderFront (this);
        }

        public override void WillTerminate (NSNotification notification)
        {
            // Insert code here to tear down your application
        }
    }
}
```

現在.xib 中所定義的應用程式的主視窗 (OS **X*- **我**介面**B**uilder) 加入視窗時，會自動包含在專案中的檔案控制站。 若要編輯您的 windows 設計中**方案板**，按兩下 **MainWindow.xib**檔案：

![選取 MainWindow.xib 檔案](xib-images/edit01.png "選取 MainWindow.xib 檔案")

這會在 Xcode 的介面產生器中開啟視窗設計：

[![編輯 MainWindow.xib](xib-images/edit02.png "編輯 MainWindow.xib")](xib-images/edit02-large.png)


### <a name="standard-window-workflow"></a>標準的視窗的工作流程

您建立和使用 Xamarin.Mac 應用程式中的任何視窗，請在程序基本上是相同：

1. 對於新的 windows 不會自動加入到專案的預設值，將新的視窗定義加入專案。
2. 按兩下要開啟的視窗設計在 Xcode 的介面產生器中編輯的.xib 檔案。
3. 在中設定任何必要的視窗屬性**屬性偵測器**和**大小 Inspector**。
4. 在建置您的介面，然後設定中所需的控制項拖曳**屬性偵測器**。
5. 使用**大小 Inspector**來處理您的 UI 項目調整大小。
6. 會公開透過插座和動作的 C# 程式碼視窗的 UI 項目。
7. 儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。


### <a name="designing-a-window-layout"></a>設計視窗版面配置

用來配置介面產生器中的使用者介面的程序基本上是相同的每個您所加入的項目：

1. 找不到所需的控制項中**程式庫偵測器**並將它拖曳到**介面編輯器**並將它定位。
2. 在中設定任何必要的視窗屬性**屬性偵測器**。
3. 使用**大小 Inspector**來處理您的 UI 項目調整大小。
4. 如果您使用的自訂類別，將它設定**識別 Inspector**。
5. 會公開透過插座和動作的 C# 程式碼的 UI 項目。
6. 儲存變更並切換回 Visual Studio for Mac 使用 Xcode 進行同步處理。

例如：

1. 在 Xcode 中，從 [程式庫] 區段拖曳 [按鈕]：

    [![從程式庫選取按鈕](xib-images/xcode07.png "選取按鈕，從程式庫")](xib-images/xcode07-large.png)
2. 卸除按鈕拖曳至**視窗**中**介面編輯器**:

    [![將按鈕加入至視窗](xib-images/xcode08.png "將按鈕加入至視窗")](xib-images/xcode08-large.png)
3. 按一下 [屬性偵測器] 中的 [標題] 屬性，將按鈕的標題變更為 `Click Me`：

    ![設定按鈕屬性](xib-images/xcode09.png "設定按鈕屬性")
4. 從 [程式庫] 區段拖曳 [標籤]：

    [![選取程式庫中的標籤](xib-images/xcode10.png "選取文件庫中的標籤")](xib-images/xcode10-large.png)
5. 將標籤拖曳到 [介面編輯器] 中 [視窗] 上的按鈕旁：

    [![將標籤新增至視窗](xib-images/xcode11.png "將標籤新增至視窗")](xib-images/xcode11-large.png)
6. 抓住標籤上的右側控點，將它拖曳到視窗邊緣附近為止：

    [![調整大小標籤](xib-images/xcode12.png "調整大小的標籤")](xib-images/xcode12-large.png)
7. 選取的標籤**介面編輯器**，切換至**大小 Inspector**:

    ![選取大小 Inspector](xib-images/xcode13.png "選取大小偵測器")
8. 在**自動調整大小方塊**按一下**Dim 紅色括號**右邊和**Dim 紅色水平箭頭**中心：

    ![編輯自動調整大小屬性](xib-images/xcode14.png "編輯自動調整大小屬性")
9. 這可確保標籤將會延伸到擴增和縮減視窗調整大小時執行的應用程式中。 **紅色方括號**頂端和左側**自動調整大小方塊**方塊告訴積存至其指定的 X 和 Y 位置標籤。
10. 將變更儲存至使用者介面

當您調整大小並移動控制項周圍，您應該會注意到，介面產生器能讓您很有幫助的嵌入式管理單元提示為基礎的[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 這些指導方針將協助您建立包含 Mac 使用者熟悉外觀與風格的高品質應用程式。

若您查看**介面階層架構**區段中，注意配置和組成我們的使用者介面項目階層中的顯示方式：

![介面階層架構中選取項目](xib-images/xcode15.png "介面階層架構中選取項目")

從這裡您可以選取要編輯或重新排列 UI 項目，必要時拖曳項目。 比方說，如果 UI 項目已被另一個項目範圍內，您無法將它拖曳以容易視窗上的最上層項目清單的底部。

如需 Windows Xamarin.Mac 應用程式中使用的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)文件。


## <a name="exposing-ui-elements-to-c-code"></a>公開至 C# 程式碼的 UI 項目

當您完成後，在介面產生器使用者介面的外觀及操作的版面配置時，您要公開的 UI 項目，以便從 C# 程式碼可以存取它們。 若要這樣做，您將使用動作和插座。


### <a name="setting-a-custom-main-window-controller"></a>設定自訂的主視窗的控制站

若要能夠建立插座和動作公開 （expose） 至 C# 程式碼的 UI 項目，Xamarin.Mac 應用程式必須使用自訂的視窗控制站。

請執行下列動作：

1. 在 Xcode 的介面產生器中開啟應用程式的分鏡腳本。
2. 選取`NSWindowController`設計介面中。
3. 切換至**識別 Inspector**檢視及輸入`WindowController`為**類別名稱**:

    [![編輯的類別名稱](xib-images/windowcontroller01.png "編輯類別名稱")](xib-images/windowcontroller01-large.png)
4. 儲存變更並返回 Visual Studio for Mac 同步處理。
5. A **WindowController.cs**檔案會加入至您的專案中**方案板**適用於 Mac 的 Visual Studio 中：

    ![新的類別名稱在 Visual Studio for Mac](xib-images/windowcontroller02.png "新的類別名稱在 Visual Studio for Mac")
6. 重新開啟分鏡腳本 Xcode 的介面產生器中。
7. **WindowController.h**檔案可供使用：

    [![在 Xcode 中比對的.h 檔案](xib-images/windowcontroller03.png "在 Xcode 中比對的.h 檔案")](xib-images/windowcontroller03-large.png)


### <a name="outlets-and-actions"></a>插座和動作

因此為何插座和動作？ 在傳統的 .NET 使用者介面程式設計中，使用者介面的控制項在其加入時會自動公開為屬性。 在 Mac 中的運作方式則不同，只將控制項加入至檢視是無法讓程式碼存取的。 開發人員必須明確地將 UI 元素公開到程式碼。 為了這樣做，Apple 為我們提供兩個選項：

-  **輸出** – 輸出類似屬性。 如果控制項連接到插座，公開給您的程式碼，透過屬性，因此您可以執行一些作業，例如附加事件處理常式，呼叫方法，它等。
-  **動作** – 動作類似 WPF 中的命令模式。 例如，在控制項上執行動作時，例如按一下按鈕，控制項將會自動在您的程式碼中呼叫的方法。 動作是功能強大且方便，因為您可以連接許多控制項相同的動作。

在 Xcode 中，插座和動作會直接在程式碼，透過加入*控制項拖曳*。 更具體來說，這表示若要建立的輸出或動作，您選擇您想要新增輸出或動作，請按住哪些控制項項目**控制項**鍵盤上的按鈕，然後將該控制項拖曳直接插入程式碼。

Xamarin.Mac 開發人員而言，這表示，您拖曳至對應 Objective-C stub 檔至您要建立輸出或動作的 C# 檔案。 Visual Studio for Mac 建立檔案，稱為**MainWindow.h**它產生填充碼的 Xcode 專案的一部分使用的介面產生器：

[![在 Xcode 中的.h 檔案的範例](xib-images/xcode16.png "在 Xcode 中的.h 檔案的範例")](xib-images/xcode16-large.png)

此虛設常式.h 檔案會反映**MainWindow.designer.cs**自動加入至新的 Xamarin.Mac 專案`NSWindow`建立。 此檔案可用於同步處理介面產生器所做的變更，讓我們將建立您的插座和動作，讓 UI 項目都會公開至 C# 程式碼。


#### <a name="adding-an-outlet"></a>加入輸出

使用基本的了解插座和動作是什麼，讓我們看看建立輸出來公開 （expose） 至 C# 程式碼的 UI 項目。

請執行下列動作：

1. 在 Xcode 中，於畫面較靠近右上角按一下 [雙圓形] 按鈕以開啟 [助理編輯器]：

    [![選取小幫手編輯器](xib-images/outlet01.png "選取助理編輯器")](xib-images/outlet01-large.png)
2. Xcode 會切換成分割檢視模式，一邊是 [介面編輯器]，另一邊則是 [程式碼編輯器]。
3. 請注意，已自動選取 Xcode **MainWindowController.m**檔案**程式碼編輯器**，這是不正確。 如果您記得從我們什麼插座和動作是上述的討論，我們必須將**MainWindow.h**選取。
4. 在頂端**程式碼編輯器**按一下**自動連結**選取**MainWindow.h**檔案：

    [![選取正確的.h 檔案](xib-images/outlet02.png "選取正確的.h 檔案")](xib-images/outlet02-large.png)
5. Xcode 現在應該已選取正確的檔案：

    [![選取的正確檔案](xib-images/outlet03.png "選取正確的檔案")](xib-images/outlet03-large.png)
6. **最後一個步驟很重要 ！** 如果您沒有選取正確的檔案，您將無法建立插座和動作也會公開至 C# 中的錯誤類別 ！
7. 在**介面編輯器**，按住**控制項**鍵盤上的索引鍵並按一下拖曳前面所建立程式碼編輯器上的標籤正下方`@interface MainWindow : NSWindow { }`程式碼：

    [![若要建立新的輸出拖曳](xib-images/outlet04.png "拖曳來建立新的輸出")](xib-images/outlet04-large.png)
8. 隨即顯示對話方塊。 保留**連接**設插座，並輸入`ClickedLabel`如**名稱**:

    [![設定輸出屬性](xib-images/outlet05.png "設定輸出的屬性")](xib-images/outlet05-large.png)
9. 按一下**連接**按鈕以建立輸出：

    ![已完成的插座](xib-images/outlet06.png "已完成的輸出")
10. 將變更儲存到檔案。


#### <a name="adding-an-action"></a>新增動作

接下來，讓我們看看建立要公開 （expose） 至 C# 程式碼的 UI 項目與使用者互動的動作。

請執行下列動作：

1. 請確定我們仍在**助理編輯器**和**MainWindow.h**檔案會顯示在**程式碼編輯器**。
2. 在**介面編輯器**，按住**控制項**鍵盤上的索引鍵並按一下拖曳前面所建立程式碼編輯器上的按鈕正下方`@property (assign) IBOutlet NSTextField *ClickedLabel;`程式碼：

    [![建立動作拖曳](xib-images/action01.png "拖曳來建立動作")](xib-images/action01-large.png)
3. 變更**連接**動作的類型：

    [![選取動作類型](xib-images/action02.png "選取動作類型")](xib-images/action02-large.png)
4. 輸入 `ClickedButton` 作為**名稱**：

    [![設定動作](xib-images/action03.png "設定動作")](xib-images/action03-large.png)
5. 按一下**連接**按鈕來建立動作：

    ![已完成的動作](xib-images/action04.png "已完成的動作")
6. 將變更儲存到檔案。

您的使用者介面有線向上和向 C# 程式碼，以切換回 Visual Studio for Mac，並讓它從 Xcode 和介面產生器變更同步處理。


### <a name="writing-the-code"></a>撰寫程式碼

與您建立的使用者介面公開給程式碼透過插座和動作及其 UI 項目，就可以開始撰寫程式碼，以將您的程式。 例如，開啟**MainWindow.cs**檔案進行編輯按兩下**方案板**:

[![MainWindow.cs 檔案](xib-images/code01.png "MainWindow.cs 檔案")](xib-images/code01-large.png)

加入下列程式碼和`MainWindow`類別，以使用先前建立的範例輸出：

```csharp
private int numberOfTimesClicked = 0;
...

public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Set the initial value for the label
    ClickedLabel.StringValue = "Button has not been clicked yet.";
}
```

請注意，`NSLabel`存取 C# 中的直接的名稱，您指定在 Xcode 中在 Xcode 中建立其輸出時，在此情況下，它會呼叫`ClickedLabel`。 您可以存取的任何方法或屬性公開的物件相同的方式就像任何一般 C# 類別。

> [!IMPORTANT]
> 您必須使用`AwakeFromNib`，而不是另一個方法，例如`Initialize`，因為`AwakeFromNib`稱為_之後_OS 已經載入並具現化使用者介面與.xib 檔案。 如果您嘗試存取 label 控制項.xib 檔案之前已經完全載入並具現化，就會收到`NullReferenceException`錯誤因為會尚未建立 label 控制項。

接下來，加入下列的部分類別，使`MainWindow`類別：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此程式碼附加至您在 Xcode 和介面產生器中建立，並呼叫的每當使用者按一下按鈕的動作。

部分 UI 項目會自動有內建的動作，例如，預設的功能表列中的項目例如**開啟...**功能表項目 (`openDocument:`)。 在**方案板**，連按兩下`AppDelegate.CS`檔案開啟它進行編輯，並加入下列的程式碼下方`DidFinishLaunching`方法：

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

索引鍵的線路`[Export ("openDocument:")]`，它會告訴`NSMenu`， **AppDelegate**具有方法`void OpenDialog (NSObject sender)`回應`openDocument:`動作。

如需有關使用功能表的詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)文件。


## <a name="synchronizing-changes-with-xcode"></a>Xcode 與同步處理變更

當您切換回 Visual Studio for Mac 從 Xcode 時，在 Xcode 中，您所做的任何變更將會自動同步處理與 Xamarin.Mac 專案。

如果您選取**MainWindow.designer.cs**中**方案板**您將能夠看到如何我們插座和動作已有線總我們 C# 程式碼：

[![同步處理的變更，使用 Xcode](xib-images/sync01.png "Xcode 與同步處理變更")](xib-images/sync01-large.png)

請注意如何在兩個定義**MainWindow.designer.cs**檔案：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

中的定義與對齊**MainWindow.h**在 Xcode 中的檔案：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所見，Visual Studio for Mac 的.h 檔案中，變更會接聽，並會自動同步這些變更中的個別**.designer.cs**檔案，以公開至您的應用程式。 您可能也注意**MainWindow.designer.cs**是部分類別，如此不需要修改 Visual Studio for Mac **MainWindow.cs**這會覆寫，我們對類別的任何變更。

您通常會永遠不需要開啟**MainWindow.designer.cs**自己，它已介紹教學之用。

> [!IMPORTANT]
> 在大部分情況下，適用於 Mac 的 Visual Studio 會自動看到在 Xcode 中進行任何變更，並同步它們 Xamarin.Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。


## <a name="adding-a-new-window-to-a-project"></a>專案中加入新的視窗

主要的文件視窗中，除了 Xamarin.Mac 應用程式可能需要其他類型的視窗顯示給使用者，例如喜好設定或偵測器面板。 您的專案中加入新的視窗時您應該一律使用**Cocoa 視窗與控制器**選項，因為這可讓檔案更容易從.xib 載入視窗的程序。

若要加入新的視窗，執行下列作業：

1. 在**方案板**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**.
2. 在新檔案 對話方塊中，選取**Xamarin.Mac** > **Cocoa 視窗與控制器**:

    ![將新的視窗控制站新增](xib-images/new01.png "加入新的視窗控制站")
3. 輸入 `PreferencesWindow` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下**PreferencesWindow.xib**開啟介面產生器中進行編輯的檔案：

    [![編輯在 Xcode 中的視窗](xib-images/new02.png "編輯在 Xcode 中的視窗")](xib-images/new02-large.png)
5. 設計您的介面：

    [![設計 windows 配置](xib-images/new03.png "設計視窗版面配置")](xib-images/new03-large.png)
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

將下列程式碼加入**AppDelegate.cs**以顯示新的視窗：

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

`var preferences = new PreferencesWindowController ();`一行會建立從.xib 檔案載入視窗，並擴大它的視窗控制站的新執行個體。 `preferences.Window.MakeKeyAndOrderFront (this);`列向使用者顯示新的視窗。

如果您執行的程式碼，並選取**喜好設定...**從**應用程式功能表**，視窗會顯示：

![執行範例應用程式](xib-images/new04.png "執行範例應用程式")

如需 Windows Xamarin.Mac 應用程式中使用的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)文件。


## <a name="adding-a-new-view-to-a-project"></a>將新的檢視加入至專案

但有些的時候時更輕鬆地設計您的視窗分為數個，更容易管理的.xib 檔案。 例如，像是選取中的工具列項目時，切換移出主視窗的內容**喜好設定 視窗**或回應的內容交換**來源清單**選取項目。

將新的檢視加入至您的專案時您應該一律使用**Cocoa 檢視與控制器**選項，因為這可讓檔案更容易從.xib 載入檢視的程序。

若要加入新的檢視，執行下列作業：

1. 在**方案板**，以滑鼠右鍵按一下專案，然後選取**新增** > **新的檔案...**.
2. 在新檔案 對話方塊中，選取**Xamarin.Mac** > **Cocoa 檢視與控制器**:

    ![加入新檢視](xib-images/view01.png "加入新的檢視")
3. 輸入 `SubviewTable` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下**SubviewTable.xib**檔案以開啟介面產生器中進行編輯和設計使用者介面：

    [![設計新的檢視，在 Xcode 中](xib-images/view02.png "設計在 Xcode 中新的檢視")](xib-images/view02-large.png)
5. 連接任何必要的動作和插座。
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

接下來編輯**SubviewTable.cs**並加入下列程式碼加入**AwakeFromNib**來填入新的檢視，載入時將它的檔案：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create the Product Table Data Source and populate it
    var DataSource = new ProductTableDataSource ();
    DataSource.Products.Add (new Product ("Xamarin.iOS", "Allows you to develop native iOS Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Android", "Allows you to develop native Android Applications in C#"));
    DataSource.Products.Add (new Product ("Xamarin.Mac", "Allows you to develop Mac native Applications in C#"));
    DataSource.Sort ("Title", true);

    // Populate the Product Table
    ProductTable.DataSource = DataSource;
    ProductTable.Delegate = new ProductTableDelegate (DataSource);

    // Auto select the first row
    ProductTable.SelectRow (0, false);
}
```

將列舉加入至專案，以追蹤哪些檢視目前正在顯示。 例如， **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

編輯.xib 檔案會耗用檢視，並顯示它的視窗。 新增**自訂檢視**，做為容器檢視一旦載入到記憶體的 C# 程式碼，並呼叫它到插座公開`ViewContainer`:

[![建立必要的插座](xib-images/view03.png "建立必要的輸出")](xib-images/view03-large.png)

儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

接著，編輯.cs 檔案中將會顯示新檢視的視窗 (例如， **MainWindow.cs**) 並加入下列程式碼：

```csharp
private SubviewType ViewType = SubviewType.None;
private NSViewController SubviewController = null;
private NSView Subview = null;
...

private void DisplaySubview(NSViewController controller, SubviewType type) {

    // Is this view already displayed?
    if (ViewType == type) return;

    // Is there a view already being displayed?
    if (Subview != null) {
        // Yes, remove it from the view
        Subview.RemoveFromSuperview ();

        // Release memory
        Subview = null;
        SubviewController = null;
    }

    // Save values
    ViewType = type;
    SubviewController = controller;
    Subview = controller.View;

    // Define frame and display
    Subview.Frame = new CGRect (0, 0, ViewContainer.Frame.Width, ViewContainer.Frame.Height);
    ViewContainer.AddSubview (Subview);
}
```

當我們需要顯示新的檢視從.xib 檔案載入視窗的容器中 (**自訂檢視**上方新增)，此程式碼控制代碼，移除任何現有檢視，以及交換試用新的。 它會查看您已經擁有檢視顯示，因此它從螢幕。 接下來會採用傳遞了無效的檢視中 （如從檢視控制器載入） 調整來容納內容區域，並將它加入顯示的內容。

若要顯示新的檢視，請使用下列程式碼：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

這會建立新檢視的檢視控制站要顯示的新執行個體、 設定其類型 （如加入專案中的列舉所指定），並使用`DisplaySubview`方法加入至以實際顯示在檢視視窗的類別。 例如: 

[![執行範例應用程式](xib-images/view04.png "執行範例應用程式")](xib-images/view04-large.png)

如需 Windows Xamarin.Mac 應用程式中使用的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)和[對話方塊](~/mac/user-interface/dialog.md)文件。


## <a name="summary"></a>總結

這篇文章已取得詳細的探討使用.xib Xamarin.Mac 應用程式中的檔案。 若要建立您的應用程式使用者介面，如何建立和維護.xib 檔案在 Xcode 的介面產生器及如何使用 C# 程式碼中的.xib 檔案的不同類型和.xib 檔案的用途，我們可了解。


## <a name="related-links"></a>相關連結

- [MacImages （範例）](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [對話方塊](~/mac/user-interface/dialog.md)
- [使用映像](~/mac/app-fundamentals/image.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
