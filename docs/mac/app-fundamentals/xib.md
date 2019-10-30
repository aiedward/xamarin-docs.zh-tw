---
title: Xamarin. Mac 中的 xib 檔案
description: 本文涵蓋如何使用在 Xcode 的 Interface Builder 中建立的 xib 檔案，來建立和維護 Xamarin. Mac 應用程式的使用者介面。
ms.prod: xamarin
ms.assetid: 6AF3D216-448D-4B2D-9026-74E4FFF5923A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 6d40dd3cc994ef8ab21ffb9658f226d36cd97913
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021777"
---
# <a name="xib-files-in-xamarinmac"></a>Xamarin. Mac 中的 xib 檔案

_本文涵蓋如何使用在 Xcode 的 Interface Builder 中建立的 xib 檔案，來建立和維護 Xamarin. Mac 應用程式的使用者介面。_

> [!NOTE]
> 建立 Xamarin 應用程式的使用者介面時，慣用的方法是使用分鏡腳本。 本檔集已保留，以供歷程記錄之用，以及使用舊版的 Xamarin 專案。 如需詳細資訊，請參閱我們的分鏡指令檔[簡介](~/mac/platform/storyboards/index.md)。

## <a name="overview"></a>總覽

在 Xamarin. C# Mac 應用程式中使用和 .net 時，您可以存取開發人員在*目標-C*和*Xcode*中工作的相同使用者介面元素和工具。 因為 Xamarin 會直接與 Xcode 整合，所以您可以使用 Xcode 的_Interface Builder_來建立和維護使用者介面（或選擇直接在程式碼中C#建立）。

MacOS 會使用 xib 檔案來定義應用程式使用者介面的元素（例如功能表、視窗、視圖、標籤、文字欄位），並以圖形方式在 Xcode 的 Interface Builder 中建立和維護。

[![執行中應用程式的範例](xib-images/intro01.png "執行中應用程式的範例")](xib-images/intro01-large.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用 xib 檔案的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，因為它涵蓋了我們將在本文中使用的重要概念和技巧。

您可能想要看一下[Xamarin 內部](~/mac/internals/how-it-works.md)檔的「 C# [公開C#類別/方法到目標-C](~/mac/internals/how-it-works.md) 」一節，它會說明用來將類別連線到目標-c 的 `Register` 和 `Export` 屬性物件和 UI 元素。

## <a name="introduction-to-xcode-and-interface-builder"></a>Xcode 和 Interface Builder 簡介

在 Xcode 的過程中，Apple 已建立稱為 Interface Builder 的工具，讓您在設計工具中以視覺化方式建立使用者介面。 Xamarin 將流暢與 Interface Builder 整合，可讓您使用與目標 C 使用者相同的工具來建立 UI。

### <a name="components-of-xcode"></a>Xcode 的元件

當您從 Visual Studio for Mac 開啟 Xcode 中的 xib 檔案時，它會開啟左側的 [**專案導覽器**]、中間的 [**介面**階層] 和 [**介面編輯器**]，以及右邊的 [**屬性 & 公用程式**] 區段：

[![Xcode UI 的元件](xib-images/xcode03.png "Xcode UI 的元件")](xib-images/xcode03-large.png#lightbox)

讓我們看一下這些 Xcode 章節的功能，以及如何使用它們來建立 Xamarin 應用程式的介面。

#### <a name="project-navigation"></a>專案導覽

當您在 Xcode 中開啟 xib 檔案進行編輯時，Visual Studio for Mac 會在背景建立 Xcode 專案檔，以在其本身與 Xcode 之間傳達變更。 之後，當您從 Xcode 切換回 Visual Studio for Mac 時，對此專案所做的任何變更都會 Visual Studio for Mac，與您的 Xamarin. Mac 專案同步處理。

[**專案流覽**] 區段可讓您在組成這個_填充碼_Xcode 專案的所有檔案之間流覽。 一般來說，您只會對這份清單中的 xib 檔案感興趣，例如**MainMenu. xib**和**mainwindow.xaml. xib**。

#### <a name="interface-hierarchy"></a>介面階層架構

[**介面**階層架構] 區段可讓您輕鬆地存取使用者介面的數個重要屬性，例如其**預留位置**和主**視窗**。 您也可以使用此區段來存取組成使用者介面的個別元素（views），並藉由在階層內拖曳它們來調整其嵌套方式。

#### <a name="interface-editor"></a>介面編輯器

[**介面編輯器**] 區段提供以圖形方式配置使用者介面的表面。 您會從 [**屬性] & [公用程式**] 區段的 [連結**庫**] 區段拖曳元素，以建立您的設計。 當您將使用者介面專案（views）新增至設計介面時，這些專案會以它們出現在 [**介面編輯器**] 中的順序加入至 [**介面**階層] 區段。

#### <a name="properties--utilities"></a>& 公用程式的屬性

[內容 **& 公用程式**] 區段分成兩個主要區段，我們將使用這些屬性 **（也**稱為偵測器）和連結**庫**：

![屬性偵測器](xib-images/xcode04.png "屬性偵測器")

這個區段一開始幾乎是空的，不過，如果您在 [**介面編輯器**] 或 [**介面**階層架構] 中選取元素，[**屬性**] 區段將會填入指定專案和屬性的相關資訊，您可以微調.

在 [屬性] 區段內，共有 8 個不同的「偵測器索引標籤」，如下圖所示：

[![所有偵測器的總覽](xib-images/xcode05.png "所有偵測器的總覽")](xib-images/xcode05-large.png#lightbox)

這些索引標籤從左到右分別是：

- **檔案偵測器** – 檔案偵測器顯示檔案資訊，例如正在編輯之 Xib 檔案的檔案名稱和位置。
- **快速說明** – [快速說明] 索引標籤提供內容相關的說明，以在 Xcode 中選取的項目為基礎。
- **身分識別偵測器** – 身分識別偵測器提供關於所選控制項/檢視的相關資訊。
- **屬性**偵測器–屬性偵測器可讓您自訂所選控制項/視圖的各種屬性。
- **大小**偵測器– [大小] 偵測器可讓您控制所選控制項/視圖的大小和調整行為。
- **連接偵測器**–連接偵測器會顯示所選控制項的輸出和動作連接。 我們只會在一段時間內檢查輸出和動作。
- 系結偵測**器–系結偵測器可**讓您設定控制項，使其值自動系結至資料模型。
- **視圖效果**偵測器– [視圖效果] 偵測器可讓您指定控制項的效果，例如動畫。

在 [連結**庫**] 區段中，您可以找到要放置在設計工具中的控制項和物件，以圖形方式建立您的使用者介面：

![程式庫偵測器的範例](xib-images/xcode06.png "程式庫偵測器的範例")

現在您已熟悉 Xcode IDE 和 Interface Builder，讓我們來看看如何使用它來建立使用者介面。

## <a name="creating-and-maintaining-windows-in-xcode"></a>在 Xcode 中建立和維護視窗

建立 Xamarin 應用程式使用者介面的慣用方法是使用分鏡腳本（如需詳細資訊，請參閱我們的分鏡指令檔[簡介](~/mac/platform/storyboards/index.md)），如此一來，在 Xamarin 中啟動的任何新專案就會使用分鏡腳本。預設.

若要切換至使用以 xib 為基礎的 UI，請執行下列動作：

1. 開啟 Visual Studio for Mac 並啟動新的 Xamarin. Mac 專案。
2. 在  **Solution Pad**中，以滑鼠右鍵按一下專案，然後選取 **加入** > **新增檔案 ...**
3. 選取 [ **Mac**  > **Windows 控制器**]：

    ![加入新的視窗控制器](xib-images/setup00.png "加入新的視窗控制器")
4. 輸入 `MainWindow` 作為 [名稱]，然後按一下 [**新增**] 按鈕：

    ![加入新的主視窗](xib-images/setup01.png "加入新的主視窗")
5. 再次以滑鼠右鍵按一下專案，然後選取 [**加入** > **新增檔案 ...** ]
6. 選取  **Mac**  > **主功能表**：

    ![加入新的主功能表](xib-images/setup02.png "加入新的主功能表")
7. 將名稱保留 `MainMenu`，然後按一下 [**新增**] 按鈕。
8. 在  **Solution Pad**選取**主要**的分鏡腳本檔案，按一下滑鼠右鍵，然後選取 **移除**：

    ![選取主要分鏡腳本](xib-images/setup03.png "選取主要分鏡腳本")
9. 在 [移除] 對話方塊中，按一下 [**刪除**] 按鈕：

    ![確認刪除](xib-images/setup04.png "確認刪除")
10. 在  **Solution Pad**中，按兩下**plist**檔案以開啟它進行編輯。
11. 從**主要介面**下拉式清單中選取 [`MainMenu`]：

    [![設定主功能表](xib-images/setup05.png "設定主功能表")](xib-images/setup05-large.png#lightbox)
12. 在  **Solution Pad**中，按兩下**xib**檔案，以在 Xcode 的 Interface Builder 中將其開啟以進行編輯。
13. 在 [程式庫] 偵測**器**的 [搜尋] 欄位中輸入 `object`，然後將新的**物件**拖曳到設計介面上：

    [![編輯主功能表](xib-images/setup06.png "編輯主功能表")](xib-images/setup06-large.png#lightbox)
14. 在 [身分**識別偵測器**] 中，輸入**類別**的 `AppDelegate`：

    [![選取應用程式委派](xib-images/setup07.png "選取應用程式委派")](xib-images/setup07-large.png#lightbox)
15. 從**介面**階層中選取檔案**的擁有**者，切換到**連接偵測器**，然後從委派將一行拖曳至剛新增至專案的 `AppDelegate`**物件**：

    [![正在連線應用程式委派](xib-images/setup08.png "正在連線應用程式委派")](xib-images/setup08-large.png#lightbox)
16. 儲存變更並返回 Visual Studio for Mac。

所有這些變更都備妥之後，請編輯**AppDelegate.cs**檔案，使其看起來如下所示：

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

現在，應用程式的主視窗會在新增視窗控制器時，自動包含在專案中的 xib 檔案中定義 **。** 若要編輯您的 windows 設計，請在  **Solution Pad**中，按兩下**mainwindow.xaml xib**檔案：

![選取 Mainwindow.xaml xib 檔案](xib-images/edit01.png "選取 Mainwindow.xaml xib 檔案")

這會在 Xcode 的 Interface Builder 中開啟視窗設計：

[![編輯 Mainwindow.xaml。 xib](xib-images/edit02.png "編輯 Mainwindow.xaml。 xib")](xib-images/edit02-large.png#lightbox)

### <a name="standard-window-workflow"></a>標準視窗工作流程

針對您在 Xamarin. Mac 應用程式中建立及使用的任何視窗，程式基本上是相同的：

1. 針對不是自動新增至專案的新視窗，請將新的視窗定義加入至專案。
2. 按兩下 xib 檔案以開啟視窗設計，以在 Xcode 的 Interface Builder 中進行編輯。
3. 在 [**屬性偵測器**] 和 [**大小偵測器**] 中設定任何必要的視窗屬性。
4. 拖曳所需的控制項，以建立您的介面，並在**屬性偵測器**中進行設定。
5. 使用 [**大小**] 偵測器來處理 UI 元素的調整大小。
6. 透過輸出和動作，將視窗C#的 UI 元素公開至程式碼。
7. 儲存您的變更，並切換回 Visual Studio for Mac 以與 Xcode 同步。

### <a name="designing-a-window-layout"></a>設計視窗版面配置

在「介面產生器」中配置使用者介面的程式，基本上與您新增的每個元素都相同：

1. 在 [連結**庫偵測器**] 中尋找所需的控制項，並將它拖曳至 [**介面編輯器**] 並放置它。
2. 在**屬性偵測器**中設定任何必要的視窗屬性。
3. 使用 [**大小**] 偵測器來處理 UI 元素的調整大小。
4. 如果您使用的是自訂類別，請在身分**識別偵測器**中設定它。
5. 透過輸出和動作將C# UI 元素公開至程式碼。
6. 儲存您的變更，並切換回 Visual Studio for Mac 以與 Xcode 同步。

例如：

1. 在 Xcode 中，從 [程式庫] 區段拖曳 [按鈕]：

    [![從程式庫選取按鈕](xib-images/xcode07.png "從程式庫選取按鈕")](xib-images/xcode07-large.png#lightbox)
2. 將 [按鈕] 放到 [**介面編輯器**] 中的**視窗**上：

    [![在視窗中加入按鈕](xib-images/xcode08.png "在視窗中加入按鈕")](xib-images/xcode08-large.png#lightbox)
3. 按一下 [屬性偵測器] 中的 [標題] 屬性，將按鈕的標題變更為 `Click Me`：

    ![設定按鈕屬性](xib-images/xcode09.png "設定按鈕屬性")
4. 從 [程式庫] 區段拖曳 [標籤]：

    [![選取媒體櫃中的標籤](xib-images/xcode10.png "選取媒體櫃中的標籤")](xib-images/xcode10-large.png#lightbox)
5. 將標籤拖曳到 [介面編輯器] 中 [視窗] 上的按鈕旁：

    [![將標籤加入至視窗](xib-images/xcode11.png "將標籤加入至視窗")](xib-images/xcode11-large.png#lightbox)
6. 抓住標籤上的右側控點，將它拖曳到視窗邊緣附近為止：

    [![調整標籤大小](xib-images/xcode12.png "調整標籤大小")](xib-images/xcode12-large.png#lightbox)
7. 在 [**介面編輯器**] 中仍選取標籤時，切換至 [大小] 偵測**器**：

    ![選取 [大小] 偵測器](xib-images/xcode13.png "選取 [大小] 偵測器")
8. 在 [**自動調整大小**] 方塊中，按一下右邊的**暗紅色括弧**和中央的**暗紅色水準箭**號：

    ![編輯自動調整大小屬性](xib-images/xcode14.png "編輯自動調整大小屬性")
9. 這可確保在執行中的應用程式中調整視窗大小時，標籤會延展以擴大和縮小。 [**自動調整大小**方塊] 方塊的左上**角和左方**，會告訴標籤停留在其指定的 X 和 Y 位置。
10. 將您的變更儲存至使用者介面

當您調整大小和移動控制項時，您應該已經注意到 Interface Builder 會提供有用的貼齊提示，這些是以[OS X 人力介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)為基礎。 這些指導方針可協助您建立高品質的應用程式，以熟悉 Mac 使用者的外觀與風格。

如果您查看 [**介面**階層] 區段，請注意如何顯示構成使用者介面之元素的版面配置和階層：

![選取介面階層架構中的元素](xib-images/xcode15.png "選取介面階層架構中的元素")

您可以從這裡選取要編輯的專案，或視需要拖曳來重新排列 UI 元素。 例如，如果 UI 專案是由另一個元素所涵蓋，您可以將它拖曳到清單底部，使其成為視窗的最上層專案。

如需在 Xamarin. Mac 應用程式中使用 Windows 的詳細資訊，請參閱我們的[windows](~/mac/user-interface/window.md)檔。

## <a name="exposing-ui-elements-to-c-code"></a>將 UI 元素公開C#給程式碼

當您完成在 Interface Builder 中配置使用者介面的外觀與風格之後，您必須公開 UI 的元素，才能從C#程式碼存取這些專案。 若要這麼做，您將使用動作和輸出。

### <a name="setting-a-custom-main-window-controller"></a>設定自訂主視窗控制器

為了能夠建立輸出和動作將 UI 元素公開給C#程式碼，Xamarin 應用程式必須使用自訂的視窗控制器。

請執行下列動作：

1. 在 Xcode 的 Interface Builder 中開啟應用程式的腳本。
2. 選取 Design Surface 中的 [`NSWindowController`]。
3. 切換至 [身分**識別偵測器**] 視圖，並輸入 `WindowController` 做為**類別名稱**：

    [![編輯類別名稱](xib-images/windowcontroller01.png "編輯類別名稱")](xib-images/windowcontroller01-large.png#lightbox)
4. 儲存您的變更，並返回 Visual Studio for Mac 以進行同步處理。
5. 在 Visual Studio for Mac 的**Solution Pad**中，會將**WindowController.cs**檔案新增至您的專案：

    ![中的新類別名稱 Visual Studio for Mac](xib-images/windowcontroller02.png "中的新類別名稱 Visual Studio for Mac")
6. 在 Xcode 的 Interface Builder 中重新開啟分鏡腳本。
7. **WindowController**將可供使用：

    [![Xcode 中的相符 .h 檔案](xib-images/windowcontroller03.png "Xcode 中的相符 .h 檔案")](xib-images/windowcontroller03-large.png#lightbox)

### <a name="outlets-and-actions"></a>輸出和動作

那麼，輸出和動作是什麼？ 在傳統的 .NET 使用者介面程式設計中，使用者介面的控制項在其加入時會自動公開為屬性。 在 Mac 中的運作方式則不同，只將控制項加入至檢視是無法讓程式碼存取的。 開發人員必須明確地將 UI 元素公開到程式碼。 為了達到此目的，Apple 提供兩個選項：

- **輸出** – 輸出類似屬性。 如果您將控制項連接到某個插座，它會透過屬性公開至您的程式碼，因此您可以執行類似附加事件處理常式、對其呼叫方法等動作。
- **動作** – 動作類似 WPF 中的命令模式。 例如，在控制項上執行動作時，例如按一下按鈕，控制項就會自動在您的程式碼中呼叫方法。 動作既強大又方便，因為您可以將許多控制項連接到相同的動作。

在 Xcode 中，輸出和動作會透過*控制拖曳*直接新增至程式碼中。 更明確地說，這表示要建立輸出或動作，您可以選擇要新增插座或動作的控制項專案，按住鍵盤上的 [**控制**] 按鈕，然後直接將該控制項拖曳到您的程式碼中。

若是 Xamarin 的開發人員，這表示您將拖曳至目標-C stub 檔案，這些檔案會對應C#至您要在其中建立輸出或動作的檔案。 Visual Studio for Mac 建立了一個名為**mainwindow.xaml**的檔案，做為它產生來使用 Interface Builder 的填充碼 Xcode 專案的一部分：

[![Xcode 中的 .h 檔案範例](xib-images/xcode16.png "Xcode 中的 .h 檔案範例")](xib-images/xcode16-large.png#lightbox)

當建立新的 `NSWindow` 時，這個 stub .h 檔案會反映自動新增至 Xamarin. Mac 專案的**MainWindow.designer.cs** 。 此檔案將用來同步處理 Interface Builder 所做的變更，而我們將在這裡建立您的輸出和動作，讓 UI 元素公開C#至程式碼。

#### <a name="adding-an-outlet"></a>新增插座

有了對輸出和動作的基本瞭解，讓我們來看看如何建立一個要向程式C#代碼公開 UI 元素的輸出。

請執行下列動作：

1. 在 Xcode 中，於畫面較靠近右上角按一下 [雙圓形] 按鈕以開啟 [助理編輯器]：

    [![選取 [助理編輯器]](xib-images/outlet01.png "選取 [助理編輯器]")](xib-images/outlet01-large.png#lightbox)
2. Xcode 會切換成分割檢視模式，一邊是 [介面編輯器]，另一邊則是 [程式碼編輯器]。
3. 請注意，Xcode 已自動在程式**代碼編輯器**中挑選**MainWindowController** ，這是不正確的。 如果您記得我們對上述輸出和動作的討論，我們必須選取**mainwindow.xaml** 。
4. 在程式**代碼編輯器**的頂端，按一下 [**自動] 連結**，然後選取 [ **mainwindow.xaml** ] 檔案：

    [![選取正確的 .h 檔案](xib-images/outlet02.png "選取正確的 .h 檔案")](xib-images/outlet02-large.png#lightbox)
5. Xcode 現在應該已選取正確的檔案：

    [![已選取正確的檔案](xib-images/outlet03.png "已選取正確的檔案")](xib-images/outlet03-large.png#lightbox)
6. **最後一個步驟很重要 ！** 如果您未選取正確的檔案，您將無法建立輸出和動作，或將它們公開到錯誤的類別C#！
7. 在 [**介面編輯器**] 中，按住鍵盤上的**Control**鍵，然後在 [程式碼編輯器] 中，將上面所建立的標籤拖曳到 `@interface MainWindow : NSWindow { }` 的程式碼下方：

    [![拖曳以建立新的插座](xib-images/outlet04.png "拖曳以建立新的插座")](xib-images/outlet04-large.png#lightbox)
8. 隨即顯示對話方塊。 將 [連線]**保留設定為 [輸出**]，並輸入 `ClickedLabel` 作為 [**名稱**]：

    [![設定插座屬性](xib-images/outlet05.png "設定插座屬性")](xib-images/outlet05-large.png#lightbox)
9. 按一下 [連線 **]** 按鈕以建立插座：

    ![已完成的輸出](xib-images/outlet06.png "已完成的輸出")
10. 將變更儲存到檔案。

#### <a name="adding-an-action"></a>新增動作

接下來，讓我們看看如何建立動作，以向您的程式C#代碼公開使用者與 UI 元素的互動。

請執行下列動作：

1. 請確定我們仍在 [**助理編輯器**] 中，而且 [程式**代碼編輯器**] 中顯示了**mainwindow.xaml**檔案。
2. 在 [**介面編輯器**] 中，按住鍵盤上的**Control**鍵，然後在 [程式碼編輯器] 的正下方，按一下您所建立的按鈕，如下 `@property (assign) IBOutlet NSTextField *ClickedLabel;` 所示：

    [![拖曳以建立動作](xib-images/action01.png "拖曳以建立動作")](xib-images/action01-large.png#lightbox)
3. **將 [連線**類型] 變更為 [動作]：

    [![選取動作類型](xib-images/action02.png "選取動作類型")](xib-images/action02-large.png#lightbox)
4. 輸入 `ClickedButton` 作為**名稱**：

    [![正在設定動作](xib-images/action03.png "正在設定動作")](xib-images/action03-large.png#lightbox)
5. 按一下 [連線 **]** 按鈕以建立動作：

    ![完成的動作](xib-images/action04.png "完成的動作")
6. 將變更儲存到檔案。

當您的使用者介面已上線並公開至C#程式碼時，請切換回 Visual Studio for Mac，讓它同步處理 Xcode 和 Interface Builder 的變更。

### <a name="writing-the-code"></a>撰寫程式碼

當您建立使用者介面，並透過輸出和動作將其 UI 專案公開給程式碼時，您就可以開始撰寫程式碼，讓您的程式變得更生動。 例如，在  **Solution Pad**中按兩下**MainWindow.cs**檔案進行編輯：

[![MainWindow.cs 檔案](xib-images/code01.png "MainWindow.cs 檔案")](xib-images/code01-large.png#lightbox)

並將下列程式碼新增至 `MainWindow` 類別，以使用您先前建立的範例輸出：

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

請注意，當您在 Xcode C#中建立其插座時，您在 Xcode 中指派它的直接名稱會存取 `NSLabel`，在此情況下，它會被稱為 `ClickedLabel`。 您可以使用與任何一般C#類別相同的方式，存取公開物件的任何方法或屬性。

> [!IMPORTANT]
> 您需要使用 `AwakeFromNib`，而不是其他方法（例如 `Initialize`），因為在 OS 載入並從 xib 檔案具現化使用者介面_之後_，會呼叫 `AwakeFromNib`。 如果您在 xib 檔案完全載入並具現化之前嘗試存取標籤控制項，您會收到 `NullReferenceException` 錯誤，因為 label 控制項尚未建立。

接下來，將下列部分類別新增至 `MainWindow` 類別：

```csharp
partial void ClickedButton (Foundation.NSObject sender) {

    // Update counter and label
    ClickedLabel.StringValue = string.Format("The button has been clicked {0} time{1}.",++numberOfTimesClicked, (numberOfTimesClicked < 2) ? "" : "s");
}
```

此程式碼會附加至您在 Xcode 和 Interface Builder 中建立的動作，並會在使用者按一下按鈕時呼叫。

某些 UI 元素會自動具有內建動作，例如預設功能表列中的專案，例如 [**開啟 ...** ] 功能表項目（`openDocument:`）。 在  **Solution Pad**中，按兩下**AppDelegate.cs**檔案以開啟它進行編輯，然後將下列程式碼新增至 `DidFinishLaunching` 方法：

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

這裡的金鑰行是 `[Export ("openDocument:")]`，它會告訴 `NSMenu` **AppDelegate**有一個方法 `void OpenDialog (NSObject sender)` 回應 `openDocument:` 動作。

如需有關使用功能表的詳細資訊，請參閱我們的[功能表](~/mac/user-interface/menu.md)檔。

## <a name="synchronizing-changes-with-xcode"></a>與 Xcode 同步處理變更

當您從 Xcode 切換回 Visual Studio for Mac 時，您在 Xcode 中所做的任何變更都會自動與您的 Xamarin. Mac 專案同步處理。

如果您在**Solution Pad**中選取 [ C# **MainWindow.designer.cs** ]，就可以看到我們的程式碼中的輸出和動作如何連接：

[![與 Xcode 同步處理變更](xib-images/sync01.png "與 Xcode 同步處理變更")](xib-images/sync01-large.png#lightbox)

請注意**MainWindow.designer.cs**檔案中的兩個定義：

```csharp
[Outlet]
AppKit.NSTextField ClickedLabel { get; set; }

[Action ("ClickedButton:")]
partial void ClickedButton (Foundation.NSObject sender);
```

在 Xcode 中，使用**mainwindow.xaml**中的定義來對齊：

```objc
@property (assign) IBOutlet NSTextField *ClickedLabel;
- (IBAction)ClickedButton:(id)sender;
```

如您所見，Visual Studio for Mac 會接聽 .h 檔案的變更，然後自動同步處理個別**designer.cs**檔案中的變更，以將它們公開給您的應用程式。 您可能也會注意到**MainWindow.designer.cs**是部分類別，因此 Visual Studio for Mac 不需要修改**MainWindow.cs** ，這會覆寫對類別所做的任何變更。

您通常不需要自行開啟**MainWindow.designer.cs** ，而只是為了教育目的而在此呈現。

> [!IMPORTANT]
> 在大多數情況下，Visual Studio for Mac 會自動查看在 Xcode 中所做的任何變更，並將它們同步處理至您的 Xamarin. Mac 專案。 在關閉的情況下，同步處理不會自動發生，請切換回 Xcode，然後再次回到 Visual Studio for Mac。 這通常會啟動同步處理週期。

## <a name="adding-a-new-window-to-a-project"></a>將新視窗加入至專案

除了主文件視窗外，Xamarin. Mac 應用程式可能需要向使用者顯示其他類型的 windows，例如喜好設定或偵測器面板。 將新視窗加入至專案時，您應該一律使用 [ **Cocoa] 視窗搭配 [控制器]** 選項，因為這會讓從 xib 檔案載入視窗的程式變得更容易。

若要加入新的視窗，請執行下列動作：

1. 在  **Solution Pad**中，以滑鼠右鍵按一下專案，然後選取 **加入** > **新增檔案 ...**
2. 在 [新增檔案] 對話方塊中，選取 [ **Xamarin**  > **Cocoa] 視窗，其中包含控制器**：

    ![加入新的視窗控制器](xib-images/new01.png "加入新的視窗控制器")
3. 輸入 `PreferencesWindow` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下  **PreferencesWindow xib**檔案以在 Interface Builder 中開啟它進行編輯：

    [![在 Xcode 中編輯視窗](xib-images/new02.png "在 Xcode 中編輯視窗")](xib-images/new02-large.png#lightbox)
5. 設計您的介面：

    [![設計 windows 版面配置](xib-images/new03.png "設計 windows 版面配置")](xib-images/new03-large.png#lightbox)
6. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

將下列程式碼新增至**AppDelegate.cs** ，以顯示您的新視窗：

```csharp
[Export("applicationPreferences:")]
void ShowPreferences (NSObject sender)
{
    var preferences = new PreferencesWindowController ();
    preferences.Window.MakeKeyAndOrderFront (this);
}
```

[`var preferences = new PreferencesWindowController ();`] 行會建立視窗控制器的新實例，它會從 xib 檔案載入視窗並擴大它。 [`preferences.Window.MakeKeyAndOrderFront (this);`] 行會向使用者顯示新視窗。

如果您執行程式碼，並從 [**應用程式] 功能表**中選取 [**喜好設定 ...** ]，將會顯示視窗：

![執行範例應用程式](xib-images/new04.png "執行範例應用程式")

如需在 Xamarin. Mac 應用程式中使用 Windows 的詳細資訊，請參閱我們的[windows](~/mac/user-interface/window.md)檔。

## <a name="adding-a-new-view-to-a-project"></a>將新的視圖加入至專案

有時候，將視窗的設計細分成幾個更容易管理的 xib 檔案， 例如，如同在 [**喜好設定] 視窗**中選取工具列專案或交換內容以回應**來源清單**選擇時，切換出主視窗的內容。

將新的視圖加入至專案時，您應該一律使用 [ **Cocoa View With Controller** ] 選項，因為這會讓從 xib 檔案載入視圖的程式變得更容易。

若要新增新的視圖，請執行下列動作：

1. 在  **Solution Pad**中，以滑鼠右鍵按一下專案，然後選取 **加入** > **新增檔案 ...**
2. 在 [新增檔案] 對話方塊中，選取 [ **Xamarin** ]  > **Cocoa View with Controller**：

    ![加入新的視圖](xib-images/view01.png "加入新的視圖")
3. 輸入 `SubviewTable` 作為 [名稱]，然後按一下 [新增] 按鈕。
4. 按兩下  **SubviewTable xib**檔案，在 Interface Builder 中開啟它進行編輯，並設計使用者介面：

    [![在 Xcode 中設計新的視圖](xib-images/view02.png "在 Xcode 中設計新的視圖")](xib-images/view02-large.png#lightbox)
5. 連接任何必要的動作和輸出。
6. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接著，編輯**SubviewTable.cs** ，並將下列程式碼新增至**AwakeFromNib**檔案，以在載入時填入新的視圖：

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

編輯將耗用此視圖並顯示它的視窗 xib 檔案。 新增**自訂視圖**，做為視圖的容器，一旦程式C#代碼將其載入記憶體中，並將它公開至稱為 `ViewContainer` 的插座：

[![建立所需的插座](xib-images/view03.png "建立所需的插座")](xib-images/view03-large.png#lightbox)

儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

接著，編輯將顯示新視圖之視窗的 .cs 檔案（例如， **MainWindow.cs**），並新增下列程式碼：

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

當我們需要在視窗的容器（如上新增的**自訂視圖**）中，顯示從 xib 檔案載入的新視圖時，此程式碼會處理移除任何現有的視圖，並將其交換為新的。 它會查看您已顯示的視圖，如果是，則會將它從螢幕中移除。 接下來，它會將已經傳入的視圖（從視圖控制器載入）調整成符合內容區域，並將其新增至要顯示的內容。

若要顯示新的視圖，請使用下列程式碼：

```csharp
DisplaySubview(new SubviewTableController(), SubviewType.TableView);
```

這會建立視圖控制器的新實例，以顯示新的視圖、設定其類型（由加入至專案的列舉所指定），並使用加入至視窗類別的 `DisplaySubview` 方法，實際顯示視圖。 例如:

[![執行範例應用程式](xib-images/view04.png "執行範例應用程式")](xib-images/view04-large.png#lightbox)

如需在 Xamarin. Mac 應用程式中使用 Windows 的詳細資訊，請參閱我們的[Windows](~/mac/user-interface/window.md)和[對話方塊](~/mac/user-interface/dialog.md)檔。

## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用 xib 檔案。 我們看到了 xib 檔案的不同類型和用途，以建立您應用程式的使用者介面、如何建立和維護 Xcode 的 Interface Builder 中的 xib 檔案，以及如何在程式碼中C#使用 xib 檔案。

## <a name="related-links"></a>相關連結

- [MacImages (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [Menus](~/mac/user-interface/menu.md)
- [對話方塊](~/mac/user-interface/dialog.md)
- [使用影像](~/mac/app-fundamentals/image.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/) \(英文\)
