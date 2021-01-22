---
title: 在 Xamarin 中 xib 檔案
description: 本文涵蓋使用在 Xcode 的 Interface Builder 中建立的 xib 檔案，以建立和維護 Xamarin 應用程式的使用者介面。
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: ec1005f3956498f1525c65b5d505f2f899aee2e2
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697666"
---
# <a name="xib-files-in-xamarinmac"></a>在 Xamarin 中 xib 檔案

_本文涵蓋使用在 Xcode 的 Interface Builder 中建立的 xib 檔案，以建立和維護 Xamarin 應用程式的使用者介面。_

> [!NOTE]
> 建立 Xamarin 應用程式使用者介面的慣用方式是使用分鏡腳本。 基於歷史原因，以及使用舊版 Xamarin 專案，這項檔已留在原處。 如需詳細資訊，請參閱分鏡指令檔的 [簡介](~/mac/platform/storyboards/index.md) 。

## <a name="overview"></a>概觀

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *c* 和 *Xcode* 中運作的相同使用者介面元素和工具。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的 _Interface Builder_ 來建立和維護您的使用者介面 (或選擇性地在 c # 程式碼) 中直接建立它們。

MacOS 會使用 xib 檔案來定義應用程式使用者介面的元素 (例如，在 Xcode 的 Interface Builder 中，以圖形方式建立和維護的功能表、視窗、視圖、標籤、文字欄位) 。

[![執行中應用程式的範例](xib-images/intro01.png "執行中應用程式的範例")](xib-images/intro01-large.png#lightbox)

在本文中，我們將討論在 Xamarin 應用程式中使用 xib 檔案的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，因為它涵蓋了我們將在本文中使用的重要概念和技術。

您可能會想要看看如何將 [c # 類別/方法公開到](~/mac/internals/how-it-works.md) [Xamarin 內部](~/mac/internals/how-it-works.md) 檔的目標 c 區段，它會說明 `Register` `Export` 用來將 C # 類別連接到目標 c 物件和 UI 元素的和屬性。

## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 簡介

在 Xcode 的過程中，Apple 建立了一個稱為 Interface Builder 的工具，可讓您在設計工具中以視覺化方式建立消費者介面。 Xamarin 與 Interface Builder 整合了流暢，可讓您使用與目標 C 使用者相同的工具來建立您的 UI。

### <a name="components-of-xcode"></a>Xcode 的元件

當您從 Visual Studio for Mac 開啟 Xcode 中的 xib 檔案時，它會以左側的 [ **專案導覽器** ]、中間的 [ **介面** 階層] 和 [ **介面編輯器** ]，以及右邊的 [ **公用程式] & [公用程式** ] 區段開啟：

[![Xcode UI 的元件](xib-images/xcode03.png "Xcode UI 的元件")](xib-images/xcode03-large.png#lightbox)

讓我們來看看這些 Xcode 章節的功用，以及如何使用它們來建立適用于 Xamarin 應用程式的介面。

#### <a name="project-navigation"></a>專案導覽

當您在 Xcode 中開啟 xib 檔案以進行編輯時，Visual Studio for Mac 會在背景中建立 Xcode 專案檔，以在其本身與 Xcode 之間傳達變更。 稍後，當您從 Xcode 切換回 Visual Studio for Mac 時，對此專案所做的任何變更都會 Visual Studio for Mac，與您的 Xamarin. Mac 專案進行同步處理。

[ **專案導覽** ] 區段可讓您在組成此 _填充碼_ Xcode 專案的所有檔案之間流覽。 一般而言，您只會對這份清單中的 xib 檔案感興趣，例如 **MainMenu. xib** 和 **MainWindow. xib**。

#### <a name="interface-hierarchy"></a>介面階層

[ **介面** 階層] 區段可讓您輕鬆地存取消費者介面的數個索引鍵屬性，例如其 **預留位置** 和主 **視窗**。 您也可以使用此區段來存取組成使用者介面的個別專案 (views) ，並藉由在階層內拖曳它們來調整其嵌套方式。

#### <a name="interface-editor"></a>介面編輯器

[ **介面編輯器** ] 區段提供您以圖形方式將消費者介面進行版面配置的表面。 您將從 [**屬性 & 公用程式**] 區段的 [連結 **庫**] 區段中拖曳元素，以建立您的設計。 當您在設計介面中加入使用者介面專案 (views) 時，它們就會依照出現在 **介面編輯器** 中的順序加入至 [**介面** 階層] 區段。

#### <a name="properties--utilities"></a>屬性 & 公用程式

[內容 **& 公用程式** ] 區段分為兩個主要區段，我們將使用這些 **屬性** (偵測器) 和連結 **庫**：

![屬性偵測器](xib-images/xcode04.png "屬性偵測器")

此區段一開始幾乎是空的，不過，如果您在 [ **介面編輯器** ] 或 [ **介面**] 階層中選取專案，[ **屬性** ] 區段將會填入可供您調整之指定專案和屬性的相關資訊。

在 [屬性] 區段內，共有 8 個不同的「偵測器索引標籤」，如下圖所示：

[![所有偵測器的總覽](xib-images/xcode05.png "所有偵測器的概觀")](xib-images/xcode05-large.png#lightbox)

這些索引標籤從左到右分別是：

- **檔案偵測器** – 檔案偵測器顯示檔案資訊，例如正在編輯之 Xib 檔案的檔案名稱和位置。
- **快速說明** – [快速說明] 索引標籤提供內容相關的說明，以在 Xcode 中選取的項目為基礎。
- **身分識別偵測器** – 身分識別偵測器提供關於所選控制項/檢視的相關資訊。
- **屬性偵測器** –屬性偵測器可讓您自訂所選控制項/視圖的各種屬性。
- **大小偵測器** –大小偵測器可讓您控制所選控制項/視圖的大小和調整大小行為。
- **連接偵測器** –連接偵測器會顯示所選控制項的輸出和動作連接。 我們只會在稍後檢查輸出和動作。
- 系結偵測 **器-系結偵測器可** 讓您設定控制項，使其值自動系結至資料模型。
- **視圖效果偵測器** ：視圖效果偵測器可讓您指定控制項的效果，例如動畫。

在 [連結 **庫** ] 區段中，您可以找到要放入設計工具中的控制項和物件，以圖形方式建立使用者介面：

![程式庫偵測器的範例](xib-images/xcode06.png "程式庫偵測器的範例")

現在您已熟悉 Xcode IDE 和 Interface Builder，讓我們來看看如何使用它來建立使用者介面。

## <a name="creating-and-maintaining-windows-in-xcode"></a>在 Xcode 中建立和維護視窗

建立 Xamarin 應用程式消費者介面的慣用方法是使用分鏡腳本 (如需詳細資訊，請參閱我們的分鏡指令檔 [簡介](~/mac/platform/storyboards/index.md)) ，如此一來，在 Xamarin 中啟動的任何新專案預設都會使用分鏡腳本。

若要切換到使用以 xib 為基礎的 UI，請執行下列動作：

1. 開啟 Visual Studio for Mac 並啟動新的 Xamarin 專案。
2. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新** 檔案]。
3. 選取 **Mac**  >  **Windows 控制器**：

    ![新增視窗控制器](xib-images/setup00.png "新增視窗控制器")
4. 輸入 `MainWindow` 名稱，然後按一下 [ **新增** ] 按鈕：

    ![加入新的主視窗](xib-images/setup01.png "加入新的主視窗")
5. 再以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新** 檔案]。
6. 選取 **Mac**  >  **主功能表**：

    ![加入新的主功能表](xib-images/setup02.png "加入新的主功能表")
7. 將名稱保留為 `MainMenu` ，然後按一下 [ **新增** ] 按鈕。
8. 在 **Solution Pad** 選取 **主要** 的分鏡腳本檔案，以滑鼠右鍵按一下並選取 [ **移除**：

    ![選取主要分鏡腳本](xib-images/setup03.png "選取主要分鏡腳本")
9. 在 [移除] 對話方塊中，按一下 [ **刪除** ] 按鈕：

    ![確認刪除](xib-images/setup04.png "確認刪除")
10. 在 [ **Solution Pad** 中，按兩下 **plist** 檔案以開啟它進行編輯。
11. `MainMenu`從 **主要介面** 下拉式清單中選取：

    [![設定主功能表](xib-images/setup05.png "設定主功能表")](xib-images/setup05-large.png#lightbox)
12. 在 [ **Solution Pad** 中，按兩下 **MainMenu xib** 檔案，以開啟該檔案以在 Xcode 的 Interface Builder 中進行編輯。
13. 在 [連結 **庫偵測器**] 中，于 `object` [搜尋] 欄位中輸入，然後將新的 **物件** 拖曳至設計介面：

    [![編輯主功能表](xib-images/setup06.png "編輯主功能表")](xib-images/setup06-large.png#lightbox)
14. 在 [身分 **識別偵測器**] 中，輸入 `AppDelegate` **類別** 的：

    [![選取應用程式委派](xib-images/setup07.png "選取應用程式委派")](xib-images/setup07-large.png#lightbox)
15. 從 **介面** 階層選取檔案 **的擁有** 者，切換至 [**連接偵測器**]，然後從委派將一行拖曳至 `AppDelegate` 剛剛新增至專案的 **物件**：

    [![連接應用程式委派](xib-images/setup08.png "連接應用程式委派")](xib-images/setup08-large.png#lightbox)
16. 儲存變更並返回 Visual Studio for Mac。

完成這些變更後，請編輯 **AppDelegate.cs** 檔案，使其看起來如下所示：

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

現在應用程式的主視窗會在新增視窗控制器時，自動包含在專案中的 **xib** 檔案中定義。 若要編輯您的 windows 設計，請在 [ **Solution Pad** 中，按兩下 **MainWindow xib** 檔案：

![選取 MainWindow xib 檔案](xib-images/edit01.png "選取 MainWindow xib 檔案")

這會開啟 Xcode Interface Builder 中的視窗設計：

[![編輯 MainWindow. xib](xib-images/edit02.png "編輯 MainWindow. xib")](xib-images/edit02-large.png#lightbox)

### <a name="standard-window-workflow"></a>標準時間範圍工作流程

針對您在 Xamarin. Mac 應用程式中建立及使用的任何視窗，此程式基本上相同：

1. 針對不是自動新增至專案的新視窗，請在專案中加入新的視窗定義。
2. 按兩下 xib 檔案以開啟在 Xcode 的 Interface Builder 中編輯的視窗設計。
3. 在 **屬性偵測器** 和 **大小偵測器** 中設定任何必要的視窗屬性。
4. 拖曳建立您的介面所需的控制項，並在屬性偵測 **器** 中進行設定。
5. 使用 **大小偵測器** 來處理 UI 元素的調整大小。
6. 透過輸出和動作，將視窗的 UI 元素公開到 c # 程式碼。
7. 儲存您的變更並切換回 Visual Studio for Mac，以與 Xcode 同步。

### <a name="designing-a-window-layout"></a>設計視窗版面配置

在 Interface builder 中配置消費者介面的程式基本上是針對您新增的每個元素都相同：

1. 在 [連結 **庫偵測器** ] 中尋找所需的控制項，並將它拖曳至 **介面編輯器** 並放置。
2. 在 **屬性偵測器** 中設定任何必要的視窗屬性。
3. 使用 **大小偵測器** 來處理 UI 元素的調整大小。
4. 如果您使用自訂類別，請在身分識別偵測 **器** 中進行設定。
5. 透過輸出和動作，將 UI 元素公開至 c # 程式碼。
6. 儲存您的變更並切換回 Visual Studio for Mac，以與 Xcode 同步。

例如：

1. 在 Xcode 中，從 [程式庫] 區段拖曳 [按鈕]：

    [![從程式庫選取按鈕](xib-images/xcode07.png "從程式庫選取按鈕")](xib-images/xcode07-large.png#lightbox)
2. 將按鈕放在 **介面編輯器** 中的 **視窗** 上：

    [![將按鈕加入至視窗](xib-images/xcode08.png "將按鈕加入至視窗")](xib-images/xcode08-large.png#lightbox)
3. 按一下 [屬性偵測器] 中的 [標題] 屬性，將按鈕的標題變更為 `Click Me`：

    ![設定按鈕屬性](xib-images/xcode09.png "設定按鈕屬性")
4. 從 [程式庫] 區段拖曳 [標籤]：

    [![選取文件庫中的標籤](xib-images/xcode10.png "選取文件庫中的標籤")](xib-images/xcode10-large.png#lightbox)
5. 將標籤拖曳到 [介面編輯器] 中 [視窗] 上的按鈕旁：

    [![將標籤加入至視窗](xib-images/xcode11.png "將標籤加入至視窗")](xib-images/xcode11-large.png#lightbox)
6. 抓住標籤上的右側控點，將它拖曳到視窗邊緣附近為止：

    [![調整標籤大小](xib-images/xcode12.png "調整標籤大小")](xib-images/xcode12-large.png#lightbox)
7. 在 **介面編輯器** 中仍選取標籤的情況下，切換至 **大小偵測器**：

    ![選取大小檢查](xib-images/xcode13.png "選取大小檢查")
8. 在 [ **自動調整大小** ] 方塊中，按一下右邊的 **暗紅色括弧** 和中央的 **暗紅色水準箭** 號：

    ![編輯自動調整大小屬性](xib-images/xcode14.png "編輯自動調整大小屬性")
9. 這可確保在執行中的應用程式中調整視窗大小時，標籤會延展以放大和縮小。 **自動調整大小 box** 方塊的 **紅色括弧** 和左上角，可讓標籤卡在指定的 X 和 Y 位置。
10. 將您的變更儲存至消費者介面

當您調整控制項的大小和移動時，您應該注意到 Interface Builder 會提供以 [OS X 人類介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)為基礎的實用嵌入式管理提示。 這些指導方針可協助您建立高品質的應用程式，以熟悉 Mac 使用者的外觀和風格。

如果您查看 [ **介面** 階層] 區段，請注意組成使用者介面之元素的版面配置和階層會如何顯示：

![在 [介面階層] 中選取元素](xib-images/xcode15.png "在 [介面階層] 中選取元素")

您可以從這裡選取要編輯的專案，或視需要拖曳以重新排列 UI 元素。 例如，如果 UI 元素是由另一個專案所涵蓋，您可以將它拖曳到清單底部，使其成為視窗中最上層的專案。

如需在 Xamarin 應用程式中使用 Windows 的詳細資訊，請參閱我們的 [windows](~/mac/user-interface/window.md) 檔。

## <a name="exposing-ui-elements-to-c-code"></a>將 UI 元素公開給 c # 程式碼

當您在 Interface Builder 中完成使用者介面的外觀和操作後，您必須公開 UI 的元素，以便從 c # 程式碼存取它們。 若要這樣做，您將使用動作和輸出。

### <a name="setting-a-custom-main-window-controller"></a>設定自訂主視窗控制器

為了能夠建立輸出和動作，以將 UI 元素公開給 c # 程式碼，Xamarin 應用程式必須使用自訂視窗控制器。

執行下列動作：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的分鏡腳本。
2. 選取 `NSWindowController` Design Surface 中的。
3. 切換至身分 **識別偵測器** 視圖，並輸入 `WindowController` 為 **類別名稱**：

    [![編輯類別名稱](xib-images/windowcontroller01.png "編輯類別名稱")](xib-images/windowcontroller01-large.png#lightbox)
4. 儲存您的變更，並返回 Visual Studio for Mac 進行同步處理。
5. **WindowController.cs** 檔案將會新增至您在 Visual Studio for Mac 中 **Solution Pad** 的專案中：

    ![Visual Studio for Mac 中的新類別名稱](xib-images/windowcontroller02.png "Visual Studio for Mac 中的新類別名稱")
6. 在 Xcode 的 Interface Builder 中重新開啟分鏡腳本。
7. **WindowController .h** 檔案將可供使用：

    [![Xcode 中的相符 .h 檔案](xib-images/windowcontroller03.png "Xcode 中的相符 .h 檔案")](xib-images/windowcontroller03-large.png#lightbox)

### <a name="outlets-and-actions"></a>輸出和動作

什麼是輸出和動作？ 在傳統的 .NET 使用者介面程式設計中，使用者介面的控制項在其加入時會自動公開為屬性。 在 Mac 中的運作方式則不同，只將控制項加入至檢視是無法讓程式碼存取的。 開發人員必須明確地將 UI 元素公開到程式碼。 為了達成此目的，Apple 提供兩個選項：

- **輸出** – 輸出類似屬性。 如果您將控制項連接到一個輸出，它會透過屬性公開給您的程式碼，因此您可以進行附加事件處理常式、對其呼叫方法等專案。
- **動作** – 動作類似 WPF 中的命令模式。 例如，在控制項上執行動作時，例如按一下按鈕，控制項將會自動在您的程式碼中呼叫方法。 動作功能強大且方便，因為您可以將許多控制項連接至相同的動作。

在 Xcode 中，會透過 *控制項拖曳*，直接在程式碼中加入輸出和動作。 更具體來說，這表示若要建立輸出或動作，您可以選擇要新增輸出的控制項元素、按住鍵盤上的 **控制項** 按鈕，然後直接將該控制項拖曳到您的程式碼中。

若是 Xamarin 的開發人員，這表示您可以拖曳至目標 C 存根檔案，這些檔案會對應至您想要在其中建立輸出或動作的 c # 檔案。 Visual Studio for Mac 在其產生的填充碼 Xcode 專案中建立一個名為 **MainWindow** 的檔案，以使用 Interface Builder：

[![Xcode 中的 .h 檔案範例](xib-images/xcode16.png "Xcode 中的 .h 檔案範例")](xib-images/xcode16-large.png#lightbox)

這個存根檔會在建立新的時，鏡像自動新增至 Xamarin 專案的 **MainWindow.designer.cs** `NSWindow` 。 此檔案將用來同步處理 Interface Builder 所做的變更，我們將在這裡建立您的輸出和動作，以便將 UI 元素公開給 c # 程式碼。

#### <a name="adding-an-outlet"></a>新增輸出

透過對輸出和動作的基本瞭解，讓我們看看如何建立輸出，以將 UI 專案公開給您的 c # 程式碼。

執行下列動作：

1. 在 Xcode 中，於畫面較靠近右上角按一下 [雙圓形] 按鈕以開啟 [助理編輯器]：

    [![選取 [助理編輯器]](xib-images/outlet01.png "選取 [助理編輯器]")](xib-images/outlet01-large.png#lightbox)
2. Xcode 會切換成分割檢視模式，一邊是 [介面編輯器]，另一邊則是 [程式碼編輯器]。
3. 請注意，Xcode 已在程式 **代碼編輯器** 中自動挑選 **MainWindowController** 檔，這是不正確的。 如果您記得討論上述的輸出和動作，我們必須選取 **MainWindow** 。
4. 在程式 **代碼編輯器** 的頂端，按一下 [ **自動] 連結** ，然後選取 **MainWindow .h** 檔案：

    [![選取正確的 .h 檔案](xib-images/outlet02.png "選取正確的 .h 檔案")](xib-images/outlet02-large.png#lightbox)
5. Xcode 現在應該已選取正確的檔案：

    [![已選取正確的檔案](xib-images/outlet03.png "已選取正確的檔案")](xib-images/outlet03-large.png#lightbox)
6. **最後一個步驟很重要 ！** 如果您未選取正確的檔案，您將無法建立輸出和動作，也不會在 c # 中公開給錯誤的類別！
7. 在 [ **介面編輯器**] 中，按住鍵盤上的 **控制** 鍵，然後按一下 [將上面建立的標籤拖曳到程式碼編輯器] 的程式 `@interface MainWindow : NSWindow { }` 代碼下方：

    [![拖曳以建立新的輸出](xib-images/outlet04.png "拖曳以建立新的輸出")](xib-images/outlet04-large.png#lightbox)
8. 隨即顯示對話方塊。 將 **連接** 設定為 [輸出]，並輸入 `ClickedLabel` **名稱**：

    [![設定輸出屬性](xib-images/outlet05.png "設定輸出屬性")](xib-images/outlet05-large.png#lightbox)
9. 按一下 [連線 **]** 按鈕以建立輸出：

    ![完成的輸出](xib-images/outlet06.png "完成的輸出")
10. 將變更儲存至檔案。

#### <a name="adding-an-action"></a>新增動作

接下來，讓我們看看如何建立動作，以將使用者與 UI 元素的互動公開給您的 c # 程式碼。

執行下列動作：

1. 請確定我們仍在 [ **助理編輯器** ] 中，而且 **MainWindow** 檔會顯示在 [程式 **代碼編輯器**] 中。
2. 在 [ **介面編輯器**] 中，按住鍵盤上的 **控制** 鍵，然後按一下 [將上面建立的按鈕拖曳到程式碼編輯器] 的程式碼正下方 `@property (assign) IBOutlet NSTextField *ClickedLabel;` ：

    [![拖曳以建立動作](xib-images/action01.png "拖曳以建立動作")](xib-images/action01-large.png#lightbox)
3. 將 **連接** 類型變更為 [動作]：

    [![選取動作類型](xib-images/action02.png "選取動作類型")](xib-images/action02-large.png#lightbox)
4. 輸入 `ClickedButton` 作為 **名稱**：

    [![設定動作](xib-images/action03.png "設定動作")](xib-images/action03-large.png#lightbox)
5. 按一下 [連線 **]** 按鈕以建立動作：

    ![已完成的動作](xib-images/action04.png "已完成的動作")
6. 將變更儲存至檔案。

當您的消費者介面連接並公開至 c # 程式碼時，切換回 Visual Studio for Mac，讓它同步處理 Xcode 和 Interface Builder 的變更。

### <a name="writing-the-code"></a>撰寫程式碼

隨著您的消費者介面建立，並透過輸出和動作將其 UI 元素公開到程式碼，您就可以開始撰寫程式碼，將您的程式帶入生活。 例如，在 **Solution Pad** 中按兩下以開啟 **MainWindow.cs** 檔案進行編輯：

[![MainWindow.cs 檔案](xib-images/code01.png "MainWindow.cs 檔案")](xib-images/code01-large.png#lightbox)

並將下列程式碼加入至 `MainWindow` 類別，以使用您先前建立的範例輸出：

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

請注意， `NSLabel` 當您在 Xcode 中建立其輸出時，以 c # 存取的直接名稱會以 c # 存取，在此案例中，它會被呼叫 `ClickedLabel` 。 您可以使用與任何一般 c # 類別相同的方式，來存取公開物件的任何方法或屬性。

> [!IMPORTANT]
> 您必須使用 `AwakeFromNib` ，而不是另一個方法（例如 `Initialize` ），因為 `AwakeFromNib` 會在作業系統載入並具現化 xib 檔案的消費者介面 _之後_ 呼叫。 如果您在 xib 檔案已完全載入並具現化之前，嘗試存取標籤控制項，您會收到 `NullReferenceException` 錯誤，因為尚未建立標籤控制項。

接下來，將下列部分類別新增至 `MainWindow` 類別：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此程式碼會附加至您在 Xcode 和 Interface Builder 中建立的動作，並在使用者按一下按鈕時呼叫。

某些 UI 元素會自動有內建的動作，例如，預設功能表列中的專案，例如 [ **開啟 ...** ] 功能表項目 (`openDocument:`) 。 在 [ **Solution Pad** 中，按兩下 **AppDelegate.cs** 檔案以開啟它進行編輯，並將下列程式碼新增至 `DidFinishLaunching` 方法下方：

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

此處的關鍵行是 `[Export ("openDocument:")]` ，它會告訴 `NSMenu` **AppDelegate** 有一個方法可 `void OpenDialog (NSObject sender)` 回應 `openDocument:` 動作。

如需使用功能表的詳細資訊，請參閱我們的 [功能表](~/mac/user-interface/menu.md) 檔。

## <a name="synchronizing-changes-with-xcode"></a>與 Xcode 同步處理變更

當您從 Xcode 切換回 Visual Studio for Mac 時，您在 Xcode 中所做的任何變更都會自動與您的 Xamarin. Mac 專案同步處理。

如果您在 **Solution Pad** 中選取 [ **MainWindow.designer.cs** ]，您將能夠看到我們的 c # 程式碼中的輸出口和動作如何連接：

[![使用 Xcode 同步處理變更](xib-images/sync01.png "與 Xcode 同步處理變更")](xib-images/sync01-large.png#lightbox)

請注意 **MainWindow.designer.cs** 檔中的兩個定義：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

在 Xcode 中使用 **MainWindow .h** 檔案中的定義來對齊：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所見，Visual Studio for Mac 會接聽 .h 檔案的變更，然後自動同步處理各自 **designer.cs** 檔案中的變更，以將它們公開給您的應用程式。 您也可能會注意到 **MainWindow.designer.cs** 是部分類別，因此 Visual Studio for Mac 不需要修改 **MainWindow.cs** ，這會覆寫我們對類別所做的任何變更。

您通常不需要自行開啟 **MainWindow.designer.cs** ，它只會提供給教育用途。

> [!IMPORTANT]
> 在大多數情況下，Visual Studio for Mac 會自動查看在 Xcode 中所做的任何變更，並將它們同步處理至您的 Xamarin. Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。

## <a name="adding-a-new-window-to-a-project"></a>將新視窗加入至專案

除了主文件視窗之外，Xamarin 應用程式可能需要向使用者顯示其他類型的視窗，例如喜好設定或偵測器面板。 將新視窗加入至專案時，您應該一律使用 [ **Cocoa] 視窗搭配 [控制器]** 選項，因為這樣會讓從 xib 檔案載入視窗的程式更容易。

若要加入新的視窗，請執行下列動作：

1. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新** 檔案]。
2. 在 [新增檔案] 對話方塊中，選取 [ **Xamarin**  >  **] Cocoa 視窗與控制器**：

    ![新增視窗控制器](xib-images/new01.png "新增視窗控制器")
3. 輸入 `PreferencesWindow` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下 **PreferencesWindow xib** 檔案以開啟它，以在 Interface Builder 中編輯：

    [![在 Xcode 中編輯視窗](xib-images/new02.png "在 Xcode 中編輯視窗")](xib-images/new02-large.png#lightbox)
5. 設計您的介面：

    [![設計 windows 版面配置](xib-images/new03.png "設計 windows 版面配置")](xib-images/new03-large.png#lightbox)
6. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

將下列程式碼新增至 **AppDelegate.cs** ，以顯示新的視窗：

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

這 `var preferences = new PreferencesWindowController ();` 一行會建立視窗控制器的新實例，此實例會從 xib 檔案載入視窗並擴大它。 這 `preferences.Window.MakeKeyAndOrderFront (this);` 一行會向使用者顯示新的視窗。

如果您執行程式碼，並從 [**應用程式] 功能表** 中選取 [**喜好設定 ...** ]，將會顯示視窗：

![螢幕擷取畫面顯示 [應用程式] 功能表中顯示的 [喜好設定] 視窗。](xib-images/new04.png "執行範例應用程式")

如需在 Xamarin 應用程式中使用 Windows 的詳細資訊，請參閱我們的 [windows](~/mac/user-interface/window.md) 檔。

## <a name="adding-a-new-view-to-a-project"></a>將新的視圖新增至專案

有時候，將視窗的設計細分為數個更容易管理的 xib 檔案會比較容易。 例如，像是在 [ **喜好設定] 視窗** 中選取工具列專案時切換主視窗的內容，或在回應 **來源清單** 選取專案時交換內容。

當您將新的視圖新增至專案時，您應該一律使用 [ **Cocoa View With Controller** ] 選項，因為這樣會讓從 xib 檔案載入視圖的程式更容易。

若要加入新的視圖，請執行下列動作：

1. 在 [ **Solution Pad** 中，以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新** 檔案]。
2. 在 [新增檔案] 對話方塊中，選取 [ **Xamarin. Mac**  >  **Cocoa View with Controller**：

    ![加入新的視圖](xib-images/view01.png "加入新的視圖")
3. 輸入 `SubviewTable` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下 **SubviewTable** 檔案以開啟它，以在 Interface Builder 中編輯並設計消費者介面：

    [![在 Xcode 中設計新的視圖](xib-images/view02.png "在 Xcode 中設計新的視圖")](xib-images/view02-large.png#lightbox)
5. 連接任何必要的動作和輸出。
6. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接著，請編輯 **SubviewTable.cs** ，並將下列程式碼新增至 **AwakeFromNib** 檔案，以在載入新的視圖時加以填入：

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

將列舉新增至專案，以追蹤目前正在顯示的視圖。 例如， **SubviewType.cs**：

```csharp
public enum SubviewType
{
    None,
    TableView,
    OutlineView,
    ImageView
}
```

編輯視窗的 xib 檔案，此檔案將取用視圖並顯示。 新增 **自訂的視圖** ，以在使用 c # 程式碼將其載入至記憶體後作為視圖的容器，並將其公開至稱為的輸出 `ViewContainer` ：

[![建立必要的輸出](xib-images/view03.png "建立必要的輸出")](xib-images/view03-large.png#lightbox)

儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

接下來，編輯將顯示新視圖 (視窗的 .cs 檔案，例如， **MainWindow.cs**) 並加入下列程式碼：

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

當我們需要在視窗的容器中顯示從 xib 檔案載入的新視圖時 () 中新增的 **自訂視圖** 時，此程式碼會處理移除任何現有的視圖，並將其換出新的視圖。 它看起來會看到您已經顯示一個視圖，如果有，就會從螢幕中移除它。 接下來，它會將已傳入 (的視圖從 View Controller 載入，) 調整其大小以符合內容區域，並將其新增至要顯示的內容。

若要顯示新的視圖，請使用下列程式碼：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

這會為要顯示的新視圖建立視圖控制器的新實例，並將其類型 (設定為加入至專案的列舉所指定) ，並使用 `DisplaySubview` 加入至視窗類別的方法來實際顯示視圖。 例如：

[![螢幕擷取畫面顯示 [使用影像] 視窗中選取的資料表視圖。](xib-images/view04.png "執行範例應用程式")](xib-images/view04-large.png#lightbox)

如需在 Xamarin 應用程式中使用 Windows 的詳細資訊，請參閱我們的 [windows](~/mac/user-interface/window.md) 和 [對話](~/mac/user-interface/dialog.md) 檔。

## <a name="summary"></a>[摘要]

本文詳細說明如何在 Xamarin 應用程式中使用 xib 檔案。 我們看到 xib 檔案的不同類型和用途，可建立您應用程式的消費者介面、如何建立和維護 Xcode 的 Interface Builder 中的 xib 檔案，以及如何使用 c # 程式碼中的 xib 檔案。

## <a name="related-links"></a>相關連結

- [MacImages (範例)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [功能表](~/mac/user-interface/menu.md)
- [對話方塊](~/mac/user-interface/dialog.md)
- [使用影像](~/mac/app-fundamentals/image.md)
- [macOS Human Interface Guidelines (人性化介面指導方針)](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)