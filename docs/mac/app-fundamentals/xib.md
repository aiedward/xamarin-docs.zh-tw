---
title: xamarin.mac.xib 檔案
description: 本文涵蓋在 Xcode 的 Interface Builder 來建立和維護的 Xamarin.Mac 應用程式的使用者介面中建立的.xib 檔案。
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 45eeee745b133646aef0f775bc879fa6a5d867c7
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109428"
---
# <a name="xib-files-in-xamarinmac"></a>xamarin.mac.xib 檔案

_本文涵蓋在 Xcode 的 Interface Builder 來建立和維護的 Xamarin.Mac 應用程式的使用者介面中建立的.xib 檔案。_

> [!NOTE]
> 建立 Xamarin.Mac 應用程式的使用者介面的慣用的方法是使用分鏡腳本。 這份文件已被保留在原處，基於歷史原因，並使用較舊的 Xamarin.Mac 專案。 如需詳細資訊，請參閱我們[分鏡腳本簡介](~/mac/platform/storyboards/index.md)文件。

## <a name="overview"></a>總覽

當使用C#和.NET Xamarin.Mac 應用程式中，您可以存取相同的使用者介面項目與工具工作的開發人員*Objective C*並*Xcode*沒有。 由於 Xamarin.Mac 直接與 Xcode 整合，您可以使用 Xcode 的_Interface Builder_來建立，並維護您的使用者介面 （或選擇直接在 C# 程式碼中建立它們）。

.Xib 檔案使用 macOS Xcode 的 Interface Builder 中以圖形方式定義您的應用程式使用者介面 （例如功能表、 Windows、 檢視表、 標籤、 文字欄位），建立和維護的項目。

[![執行中應用程式範例](xib-images/intro01.png "執行的應用程式的範例")](xib-images/intro01-large.png#lightbox)

在本文中，我們將討論使用.xib 檔案在 Xamarin.Mac 應用程式的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)文件編號第一次，因為其中包含重要概念和技術，我們將在本文中使用。

您可能想要看看[公開 C# 類別 / 方法，以 OBJECTIVE-C](~/mac/internals/how-it-works.md)一節[Xamarin.Mac 內部](~/mac/internals/how-it-works.md)文件，它會說明`Register`和`Export`屬性用來連接您的 C# 類別 OBJECTIVE-C 物件和 UI 項目。


## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 簡介

Xcode 的一部分，Apple 已建立名為 Interface Builder，可讓您在設計工具中以視覺化方式建立您的使用者介面的工具。 Xamarin.Mac 與 Interface Builder，讓您能夠使用 OBJECTIVE-C 使用者執行的相同工具建立 UI 整合流暢。


### <a name="components-of-xcode"></a>Xcode 的元件

當您在從 Visual Studio 的 Xcode 中開啟.xib 檔案 for Mac 時，這會開啟內含**專案導覽器**左側**介面階層架構**並**介面編輯器**中間與**屬性和公用程式**區段在右側：

[![Xcode UI 的元件](xib-images/xcode03.png "Xcode UI 的元件")](xib-images/xcode03-large.png#lightbox)

讓我們看看哪些每一個 Xcode 區段不，而且您將如何使用它們來建立 Xamarin.Mac 應用程式的介面。


#### <a name="project-navigation"></a>專案巡覽

當您開啟以在 Xcode 中編輯.xib 檔案時，Visual Studio for Mac 會在本身和 Xcode 之間傳達變更背景中建立的 Xcode 專案檔。 稍後，當您切換回 Visual Studio for Mac 從 Xcode，此專案所做的變更會同步處理與 Xamarin.Mac 專案由 Visual Studio for mac。

**專案巡覽**區段可讓您瀏覽之間的所有檔案構成這個_填充碼_Xcode 專案。 一般而言，您才會.xib 檔案，例如這份清單中有興趣**MainMenu.xib**並**MainWindow.xib**。


#### <a name="interface-hierarchy"></a>介面階層架構

**介面階層架構**區段可讓您輕鬆存取的使用者介面的數個索引鍵屬性，例如其**預留位置**和主要**視窗**。 您也可以使用本節中，以存取個別元素 （檢視），讓您的使用者介面和調整，內嵌在階層內拖曳它們的方式。


#### <a name="interface-editor"></a>介面編輯器

**介面編輯器**區段提供的介面上您以圖形方式配置使用者介面。 您會將項目，從**程式庫**一節**屬性和公用程式**一節，以建立您的設計。 將使用者介面元素 （檢視） 新增至設計介面時，會將它們加入**介面階層架構**一節中出現的順序**介面編輯器**。


#### <a name="properties--utilities"></a>屬性和公用程式

**屬性和公用程式**區段會分成兩個主要部分，我們會使用，**屬性**（也稱為偵測器） 和**文件庫**:

![屬性偵測器](xib-images/xcode04.png "屬性偵測器")

一開始本節幾乎是空白，不過如果您選取的項目**介面編輯器**或**介面階層架構**，則**屬性**會填入區段使用指定的項目和屬性，您可以調整的相關資訊。

在 [屬性] 區段內，共有 8 個不同的「偵測器索引標籤」，如下圖所示：

[![所有偵測器的概觀](xib-images/xcode05.png "所有偵測器的概觀")](xib-images/xcode05-large.png#lightbox)

這些索引標籤從左到右分別是：

-   **檔案偵測器** – 檔案偵測器顯示檔案資訊，例如正在編輯之 Xib 檔案的檔案名稱和位置。
-   **快速說明** – [快速說明] 索引標籤提供內容相關的說明，以在 Xcode 中選取的項目為基礎。
-   **身分識別偵測器** – 身分識別偵測器提供關於所選控制項/檢視的相關資訊。
-   **屬性偵測器**– 屬性偵測器可讓您自訂的所選控制項/檢視的各種屬性。
-   **大小偵測器**– 大小偵測器可讓您控制的大小和調整大小行為所選控制項/檢視。
-   **連線偵測器**– 連線偵測器顯示的輸出和動作連接一個選取的控制項。 稍後，我們將檢驗輸出和動作。
-   **繫結偵測器**– 繫結偵測器可讓您設定控制項，使其值會自動繫結到資料模型。
-   **檢視效果偵測器**– 檢視效果偵測器可讓您指定控制項，例如動畫的效果。

在 [**程式庫**] 區段中，您可以尋找控制項和物件，將圖形放入設計工具，以建置您的使用者介面：

![程式庫偵測器的範例](xib-images/xcode06.png "程式庫偵測器的範例")

既然您已熟悉運用 Xcode IDE 和 Interface Builder，讓我們看看使用它來建立使用者介面。


## <a name="creating-and-maintaining-windows-in-xcode"></a>建立和維護在 Xcode 中的 windows

建立 Xamarin.Mac 應用程式的使用者介面的慣用的方法是使用分鏡腳本 (請參閱我們[分鏡腳本簡介](~/mac/platform/storyboards/index.md)文件的詳細資訊)，如此一來，Xamarin.Mac 會在啟動任何新的專案依預設使用分鏡腳本。

若要切換到使用.xib UI，請執行下列：

1. 開啟 Visual Studio for Mac，並啟動新的 Xamarin.Mac 專案。
2. 在  **Solution Pad**，以滑鼠右鍵按一下專案，然後選取**新增** > **新檔...**
3. 選取  **Mac** > **Windows 控制器**:

    ![加入新的視窗控制器](xib-images/setup00.png "新增新的視窗控制器")
4. 請輸入`MainWindow`的名稱，然後按一下**新增**按鈕：

    ![加入新的主要視窗](xib-images/setup01.png "加入新的主要視窗")
5. 以滑鼠右鍵按一下專案一次，然後選取**新增** > **新檔...**
6. 選取  **Mac** > **主功能表**:

    ![加入新的主要功能表](xib-images/setup02.png "新增新的主功能表")
7. 保留的名稱，作為`MainMenu`，按一下 [**新增**] 按鈕。
8. 在  **Solution Pad**選取**Main.storyboard**檔案，以滑鼠右鍵按一下，然後選取**移除**:

    ![選取主要分鏡腳本](xib-images/setup03.png "選取主要分鏡腳本")
9. 在 [移除] 對話方塊中，按一下**刪除**按鈕：

    ![確認刪除](xib-images/setup04.png "確認刪除")
10. 在  **Solution Pad**，按兩下**Info.plist**檔案，以開啟它進行編輯。
11. 選取 `MainMenu`從**主要介面**下拉式清單中：

    [![設定主功能表](xib-images/setup05.png "設定主功能表")](xib-images/setup05-large.png#lightbox)
12. 在  **Solution Pad**，按兩下**MainMenu.xib**檔案將它開啟以在 Xcode 的 Interface Builder 中編輯。
13. 在 **程式庫偵測器**，型別`object`在 搜尋 欄位然後拖曳新**物件**拖曳至設計介面：

    [![編輯主要功能表](xib-images/setup06.png "編輯主功能表")](xib-images/setup06-large.png#lightbox)
14. 在 **身分識別偵測器**，輸入`AppDelegate`如**類別**:

    [![選取應用程式委派](xib-images/setup07.png "選取應用程式委派")](xib-images/setup07-large.png#lightbox)
15. 選取 **檔案的擁有者**從**介面階層架構**，切換至**連線偵測器**並將從要委派的線條`AppDelegate` **物件**剛加入至專案：

    [![連接應用程式委派](xib-images/setup08.png "連接應用程式委派")](xib-images/setup08-large.png#lightbox)
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

現在應用程式的主視窗中定義 **.xib**新增視窗控制器時，會自動包含在專案中的檔案。 若要編輯您的 windows 設計中**方案板**，按兩下 **MainWindow.xib**檔案：

![選取 MainWindow.xib 檔案](xib-images/edit01.png "選取 MainWindow.xib 檔案")

這會在 Xcode 的 Interface Builder 中開啟的視窗設計：

[![編輯 MainWindow.xib](xib-images/edit02.png "編輯 MainWindow.xib")](xib-images/edit02-large.png#lightbox)


### <a name="standard-window-workflow"></a>標準的視窗的工作流程

您建立並使用您的 Xamarin.Mac 應用程式中的任何視窗，請在程序大致相同：

1. 適用於新的 windows 不會自動加入到專案的預設值，將新的視窗定義加入專案。
2. 按兩下以開啟的視窗設計在 Xcode 的 Interface Builder 中編輯.xib 檔案。
3. 設定任何所需的視窗屬性**屬性偵測器**並**大小偵測器**。
4. 在建置您的介面和設定它們所需的控制項中拖曳**屬性偵測器**。
5. 使用**大小偵測器**來處理您的 UI 項目調整大小。
6. 公開 （expose) 至視窗的 UI 項目C#透過輸出和動作的程式碼。
7. 儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。


### <a name="designing-a-window-layout"></a>設計視窗配置

介面產生器中的使用者介面版面配置的程序基本上是適用於您所加入的每個項目：

1. 找不到所需的控制項中**程式庫偵測器**並將它拖曳到**介面編輯器**並將其放置。
2. 設定任何所需的視窗屬性**屬性偵測器**。
3. 使用**大小偵測器**來處理您的 UI 項目調整大小。
4. 如果您使用的自訂類別，會將它設定**身分識別偵測器**。
5. 公開 （expose) 之 UI 項目的C#透過輸出和動作的程式碼。
6. 儲存變更並切換回 Visual Studio for Mac 與 Xcode 同步處理。

例如：

1. 在 Xcode 中，從 [程式庫] 區段拖曳 [按鈕]：

    [![從程式庫選取按鈕](xib-images/xcode07.png "選取按鈕，從程式庫")](xib-images/xcode07-large.png#lightbox)
2. 該按鈕拖曳至** 視窗**中**介面編輯器**:

    [![將按鈕加入至視窗](xib-images/xcode08.png "將按鈕加入至視窗")](xib-images/xcode08-large.png#lightbox)
3. 按一下 [屬性偵測器] 中的 [標題] 屬性，將按鈕的標題變更為 `Click Me`：

    ![設定按鈕的屬性](xib-images/xcode09.png "設定按鈕屬性")
4. 從 [程式庫] 區段拖曳 [標籤]：

    [![選取程式庫中的 [標籤]](xib-images/xcode10.png "文件庫中選取 [標籤]")](xib-images/xcode10-large.png#lightbox)
5. 將標籤拖曳到 [介面編輯器] 中 [視窗] 上的按鈕旁：

    [![將標籤新增至視窗](xib-images/xcode11.png "將標籤新增至視窗")](xib-images/xcode11-large.png#lightbox)
6. 抓住標籤上的右側控點，將它拖曳到視窗邊緣附近為止：

    [![調整標籤大小](xib-images/xcode12.png "調整標籤大小")](xib-images/xcode12-large.png#lightbox)
7. 在仍選取的標籤**介面編輯器**，切換至**大小偵測器**:

    ![選取大小偵測器](xib-images/xcode13.png "選取大小偵測器")
8. 在**調整方塊**按一下  **Dim Red 括號**右邊和**暗紅色的水平箭頭**中心：

    ![編輯自動調整大小屬性](xib-images/xcode14.png "編輯自動調整大小屬性")
9. 這可確保標籤將會延伸到放大和縮小調整視窗大小執行的應用程式。 **紅色方括號**頂端和左邊**自動調整大小方塊**方塊告訴固定在其指定之 X 和 Y 位置的標籤。
10. 將變更儲存至使用者介面

當您已調整大小並移動控制項，您應該已經注意到，Interface Builder 可讓您很有幫助的貼齊提示為基礎[OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 這些指導方針可協助您建立包含 Mac 使用者熟悉外觀與風格的高品質應用程式。

如果您查看**介面階層架構**區段中，注意 版面配置和構成使用者介面項目階層架構如何顯示：

![在 介面階層架構中選取項目](xib-images/xcode15.png "介面階層架構中選取項目")

從這裡您可以選取要編輯或拖曳來重新排列 UI 元素，如有需要的項目。 比方說，如果 UI 項目已被另一個項目範圍內，您無法將它拖曳讓視窗上的最上層項目清單的底部。

如需有關在 Xamarin.Mac 應用程式使用 Windows 的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)文件。


## <a name="exposing-ui-elements-to-c-code"></a>公開至 UI 項目C#程式碼

一旦您完成您的使用者介面，介面產生器中的外觀與風格的版面配置時，您必須公開 UI 項目，以便他們可以從存取C#程式碼。 若要這樣做，您將使用動作與出口。


### <a name="setting-a-custom-main-window-controller"></a>設定自訂的主視窗的控制站

若要能夠建立輸出和動作，以公開到 C# 程式碼的 UI 項目，Xamarin.Mac 應用程式必須使用自訂視窗控制器。

請執行下列動作：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的分鏡腳本。
2. 選取`NSWindowController`設計介面中。
3. 切換至**身分識別偵測器**檢視，然後輸入`WindowController`作為**類別名稱**:

    [![編輯類別名稱](xib-images/windowcontroller01.png "編輯類別名稱")](xib-images/windowcontroller01-large.png#lightbox)
4. 儲存變更並返回 Visual Studio for Mac 中進行同步處理。
5. A **WindowController.cs**檔案會新增至您的專案中**Solution Pad**在 Visual Studio for Mac:

    ![新的類別名稱在 Visual Studio for Mac](xib-images/windowcontroller02.png "新的類別名稱在 Visual Studio for Mac")
6. 重新開啟在 Xcode 的 Interface Builder 分鏡腳本。
7. **WindowController.h**檔案可供使用：

    [![在 Xcode 中相符的.h 檔案](xib-images/windowcontroller03.png "Xcode 對應的.h 檔案")](xib-images/windowcontroller03-large.png#lightbox)


### <a name="outlets-and-actions"></a>輸出和動作

因此有哪些？ 輸出和動作 在傳統的 .NET 使用者介面程式設計中，使用者介面的控制項在其加入時會自動公開為屬性。 在 Mac 中的運作方式則不同，只將控制項加入至檢視是無法讓程式碼存取的。 開發人員必須明確地將 UI 元素公開到程式碼。 為了這樣做，Apple 提供兩個選項：

-  **輸出** – 輸出類似屬性。 如果您將控制項傳送至輸出，它會公開您的程式碼，透過屬性，因此您可以執行一些作業，例如附加事件處理常式，呼叫方法等。
-  **動作** – 動作類似 WPF 中的命令模式。 例如，在控制項上執行動作時，例如按一下按鈕，控制項將自動程式碼中呼叫方法。 動作是功能強大又方便，因為您可以將許多控制項連接至相同的動作。

在 Xcode 中，輸出和動作會直接在透過程式碼中加入*拖曳控制項*。 更具體來說，這表示若要建立動作的輸出，您選擇哪個控制項項目，您想要加入輸出或動作，請按住**控制**在鍵盤上的按鈕，然後拖曳該控制項直接插入程式碼。

適用於 Xamarin.Mac 開發人員，這表示您將拖曳至對應到 OBJECTIVE-C stub 檔案C#您要建立動作的輸出檔案。 Visual Studio for Mac 建立檔案，稱為**MainWindow.h**它產生填充碼 Xcode 專案的一部分使用 Interface Builder:

[![在 Xcode 中的.h 檔案的範例](xib-images/xcode16.png ".h 檔案在 Xcode 中的範例")](xib-images/xcode16-large.png#lightbox)

此虛設常式.h 檔案映射**MainWindow.designer.cs** ，會自動新增至新的 Xamarin.Mac 專案`NSWindow`建立。 這個檔案將用來同步處理 Interface Builder 所做的變更，我們會建立您的輸出和動作，讓 UI 元素公開到C#程式碼。


#### <a name="adding-an-outlet"></a>加入輸出

輸出和動作的基本了解，讓我們看看建立公開 UI 項目，以輸出您C#程式碼。

請執行下列動作：

1. 在 Xcode 中，於畫面較靠近右上角按一下 [雙圓形] 按鈕以開啟 [助理編輯器]：

    [![選取 助理編輯器](xib-images/outlet01.png "選取 [助理編輯器]")](xib-images/outlet01-large.png#lightbox)
2. Xcode 會切換成分割檢視模式，一邊是 [介面編輯器]，另一邊則是 [程式碼編輯器]。
3. 請注意，Xcode 已自動選取**MainWindowController.m**中的檔案**程式碼編輯器**，這是不正確。 如果您記得從我們的討論輸出和動作的上方，什麼，我們必須能夠**MainWindow.h**選取。
4. 在頂端**程式碼編輯器**上按一下**自動連結**，然後選取**MainWindow.h**檔案：

    [![選取正確的.h 檔案](xib-images/outlet02.png "選取正確的.h 檔案")](xib-images/outlet02-large.png#lightbox)
5. Xcode 現在應該已選取正確的檔案：

    [![選取正確的檔案](xib-images/outlet03.png "選取正確的檔案")](xib-images/outlet03-large.png#lightbox)
6. **最後一個步驟很重要 ！** 如果您沒有選取正確的檔案，您將無法建立輸出和動作，或它們會公開至中的錯誤類別C#！
7. 在 **介面編輯器**，按住**控制**鍵盤上的索引鍵並按一下拖曳我們先前建立到程式碼編輯器中的標籤正下方`@interface MainWindow : NSWindow { }`程式碼：

    [![若要建立新的輸出拖曳](xib-images/outlet04.png "拖曳來建立新的輸出")](xib-images/outlet04-large.png#lightbox)
8. 隨即顯示對話方塊。 離開**連接**設定為輸出，然後輸入`ClickedLabel`如**名稱**:

    [![設定輸出屬性](xib-images/outlet05.png "設定輸出屬性")](xib-images/outlet05-large.png#lightbox)
9. 按一下  **Connect**按鈕，以建立輸出：

    ![已完成的插座](xib-images/outlet06.png "已完成的輸出")
10. 將變更儲存到檔案。


#### <a name="adding-an-action"></a>加入動作

接下來，讓我們看看建立動作，以公開 UI 項目與的使用者互動，您C#程式碼。

請執行下列動作：

1. 請確定我們仍在**輔助編輯器**並**MainWindow.h**檔案會顯示在**程式碼編輯器**。
2. 在 **介面編輯器**，按住**控制**鍵盤上的索引鍵並按一下拖曳我們先前建立到程式碼編輯器上的按鈕正下方`@property (assign) IBOutlet NSTextField *ClickedLabel;`程式碼：

    [![若要建立動作拖曳](xib-images/action01.png "拖曳來建立動作")](xib-images/action01-large.png#lightbox)
3. 變更**連線**動作的類型：

    [![選取動作類型](xib-images/action02.png "選取動作類型")](xib-images/action02-large.png#lightbox)
4. 輸入 `ClickedButton` 作為**名稱**：

    [![設定動作](xib-images/action03.png "設定動作")](xib-images/action03-large.png#lightbox)
5. 按一下  **Connect**按鈕來建立動作：

    ![已完成的動作](xib-images/action04.png "已完成的動作")
6. 將變更儲存到檔案。

您的使用者介面與有線並公開至C#程式碼，請切換回到 Visual Studio for Mac 並讓它可以同步處理 Xcode 和 Interface Builder 的變更。


### <a name="writing-the-code"></a>撰寫程式碼

使用您建立的使用者介面和其 UI 元素公開到程式碼，透過輸出和動作，您已準備好撰寫程式碼，以實現您的程式項目。 例如，開啟**MainWindow.cs**檔案進行編輯按兩下**Solution Pad**:

[![MainWindow.cs 檔案](xib-images/code01.png "MainWindow.cs 檔案")](xib-images/code01-large.png#lightbox)

並新增下列程式碼`MainWindow`類別來使用您先前建立的範例輸出：

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

請注意，`NSLabel`存取C#直接指派的名稱您它在 Xcode 中您在 Xcode 中建立其輸出時，在此情況下，它稱為`ClickedLabel`。 您可以存取任何方法或屬性公開的物件相同的方式就和任何一般C#類別。

> [!IMPORTANT]
> 您必須使用`AwakeFromNib`，而不是另一個方法，例如`Initialize`，因為`AwakeFromNib`稱為_之後_OS 已經載入並具現化使用者介面，從.xib 檔案。 如果您嘗試存取標籤控制項.xib 檔案之前完全載入並具現化，您會收到`NullReferenceException`錯誤因為標籤控制項不會尚未建立。

接下來，新增下列部分類別`MainWindow`類別：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此程式碼將附加至您在 Xcode 和 Interface Builder 中建立，且會呼叫的每當使用者按一下按鈕的動作。

部分 UI 項目自動具有內建的動作，例如，預設的功能表列中的項目例如**開啟...** 功能表項目 (`openDocument:`)。 在  **Solution Pad**，按兩下**AppDelegate.cs**檔案，以開啟它進行編輯，並新增下列程式碼下方`DidFinishLaunching`方法：

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

索引鍵的線`[Export ("openDocument:")]`，它會告訴`NSMenu`， **AppDelegate**具有方法`void OpenDialog (NSObject sender)`回應`openDocument:`動作。

如需有關使用功能表的詳細資訊，請參閱我們[功能表](~/mac/user-interface/menu.md)文件。


## <a name="synchronizing-changes-with-xcode"></a>與 Xcode 同步處理變更

當您切換回 Visual Studio for Mac 從 Xcode 時，您已在 Xcode 中的任何變更將會自動與 Xamarin.Mac 專案同步。

如果您選取**MainWindow.designer.cs**中**Solution Pad**您可以看到如何我們的輸出和動作搭配完成我們C#的程式碼：

[![與 Xcode 同步處理變更](xib-images/sync01.png "與 Xcode 同步處理變更")](xib-images/sync01-large.png#lightbox)

請注意如何在兩個定義**MainWindow.designer.cs**檔案：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

中的定義貼齊**MainWindow.h**在 Xcode 中的檔案：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所見，Visual Studio for Mac 會接聽.h 檔案中，變更，並自動同步處理這些變更相應 **。 designer.cs**檔案，以將它們公開到您的應用程式。 您可能也注意**MainWindow.designer.cs**是部分類別中，以便 Visual Studio for Mac 不需修改**MainWindow.cs**這樣會覆寫我們對該類別的任何變更。

您通常會永遠不需要開啟**MainWindow.designer.cs** ，它此處所提供教育之用。

> [!IMPORTANT]
> 在大部分情況下，Visual Studio for Mac 會自動查看在 Xcode 中所做的任何變更，並它們同步處理到 Xamarin.Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。


## <a name="adding-a-new-window-to-a-project"></a>將新的視窗新增至專案

主要的文件視窗中，除了 Xamarin.Mac 應用程式可能需要其他類型的視窗顯示給使用者，例如喜好設定或偵測器面板。 您的專案中加入新的視窗時您應該一律使用**控制器的 Cocoa 視窗**選項，這樣會造成載入程序的視窗從.xib 檔案更容易。

若要加入新的視窗，執行下列作業：

1. 在  **Solution Pad**，以滑鼠右鍵按一下專案，然後選取**新增** > **新檔...**.
2. 在 [新增檔案] 對話方塊中，選取**Xamarin.Mac** > **與控制器的 Cocoa 視窗**:

    ![加入新的視窗控制器](xib-images/new01.png "新增新的視窗控制器")
3. 輸入 `PreferencesWindow` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下**PreferencesWindow.xib**以開啟它進行編輯介面產生器中的檔案：

    [![編輯在 Xcode 中的視窗](xib-images/new02.png "編輯在 Xcode 中的視窗")](xib-images/new02-large.png#lightbox)
5. 設計介面：

    [![設計 windows 版面配置](xib-images/new03.png "設計 windows 版面配置")](xib-images/new03-large.png#lightbox)
6. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

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

如果您執行程式碼，並選取**喜好設定...** 從**應用程式功能表**，視窗隨即出現：

![執行範例應用程式](xib-images/new04.png "執行範例應用程式")

如需有關在 Xamarin.Mac 應用程式使用 Windows 的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)文件。


## <a name="adding-a-new-view-to-a-project"></a>將新的檢視加入至專案

有些的時候時更輕鬆地設計您的視窗分成數個，更容易管理的.xib 檔案。 例如，選取工具列項目中的時，主視窗的內容切換**喜好設定 視窗**或在回應中的內容交換**來源清單**選取項目。

將新的檢視加入至您的專案時您應該一律使用**控制器的 Cocoa 檢視**選項，這樣會造成載入程序的檢視從.xib 檔案更容易。

若要新增新的檢視，請執行下列作業：

1. 在  **Solution Pad**，以滑鼠右鍵按一下專案，然後選取**新增** > **新檔...**.
2. 在 [新增檔案] 對話方塊中，選取**Xamarin.Mac** > **具有控制器的 Cocoa 檢視**:

    ![加入新的檢視](xib-images/view01.png "加入新的檢視")
3. 輸入 `SubviewTable` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下**SubviewTable.xib**檔案以開啟它進行 Interface Builder 中編輯和設計使用者介面：

    [![設計在 Xcode 中新的檢視](xib-images/view02.png "設計在 Xcode 中新的檢視")](xib-images/view02-large.png#lightbox)
5. 連接任何必要的動作與出口。
6. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

接著編輯**SubviewTable.cs** ，並新增下列程式碼**AwakeFromNib**來填入新的檢視，當它載入時的檔案：

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

將列舉新增至專案，以追蹤哪些檢視目前正在顯示。 例如， **SubviewType.cs**:

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

編輯.xib 檔案會使用檢視，並顯示它的視窗。 新增**的自訂檢視**，做為容器檢視一旦載入到記憶體中的C#程式碼，並將它公開到稱為 「 輸出`ViewContainer`:

[![建立所需的輸出](xib-images/view03.png "建立必要的輸出")](xib-images/view03-large.png#lightbox)

儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

接著，編輯.cs 檔案中將會顯示新的檢視之視窗 (例如**MainWindow.cs**)，並新增下列程式碼：

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

當我們需要以顯示新的檢視從.xib 檔案載入視窗的容器中 (**的自訂檢視**上方新增)，移除任何現有的檢視，然後再將它交換新的帳戶，此程式碼會處理。 它會查看它在您已經擁有檢視顯示，因此它會移除它從畫面。 接下來會採用已傳遞的檢視中 （如從檢視控制器載入） 調整大小以便容納在內容區域，並將其加入顯示的內容。

若要顯示新的檢視，請使用下列程式碼：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

這會建立新檢視的 [檢視控制器]，顯示的新執行個體、 設定它的型別 （如加入至專案的列舉所指定），並使用`DisplaySubview`方法新增至視窗的類別，以實際顯示的檢視。 例如: 

[![執行範例應用程式](xib-images/view04.png "執行範例應用程式")](xib-images/view04-large.png#lightbox)

如需有關在 Xamarin.Mac 應用程式使用 Windows 的詳細資訊，請參閱我們[Windows](~/mac/user-interface/window.md)並[對話](~/mac/user-interface/dialog.md)文件。


## <a name="summary"></a>總結

本文所深入了解使用 Xamarin.Mac 應用程式中的.xib 檔案。 我們可了解不同類型和.xib 檔案的用途，建立您的應用程式使用者介面、 如何建立和維護.xib 檔案在 Xcode 的介面產生器及如何使用.xib 檔案中C#程式碼。


## <a name="related-links"></a>相關連結

- [MacImages (範例)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [對話方塊](~/mac/user-interface/dialog.md)
- [使用映像](~/mac/app-fundamentals/image.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
