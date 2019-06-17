---
title: Xamarin.Mac-快速入門中的分鏡腳本
description: 本文件提供建置 macOS 與 xamarin.mac 的分鏡腳本的使用者介面的快速入門簡介。 說明如何建立 segue，並建立喜好設定 視窗。
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: 7f7d23a01a3c3c6567d6bab45d0abbfb078fb512
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61033474"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Xamarin.Mac-快速入門中的分鏡腳本

為快速介紹使用分鏡腳本來定義 Xamarin.Mac 應用程式的使用者介面，讓我們開始新的 Xamarin.Mac 專案。 選取 [Mac] > [應用程式][Cocoa 應用程式] > ，然後按一下 [下一步] 按鈕：

[![](quickstart-images/qs01.png "新增新的 Cocoa 應用程式")](quickstart-images/qs01.png#lightbox)

使用**應用程式名稱**的`MacStoryboard`然後按一下**下一步**按鈕：

[![](quickstart-images/qs02.png "設定應用程式名稱")](quickstart-images/qs02.png#lightbox)

使用預設**專案名稱**並**方案名稱**然後按一下**建立**按鈕：

[![](quickstart-images/qs03.png "專案和方案名稱")](quickstart-images/qs03.png#lightbox)

在 [**方案總管] 中**，連按兩下`Main.storyboard`檔案將它開啟以在 Xcode 的 Interface Builder 中編輯：

[![](quickstart-images/qs04.png "編輯在 Xcode 中的分鏡腳本")](quickstart-images/qs04.png#lightbox)

如上文所見，預設分鏡腳本會定義應用程式的功能表列和其主視窗與檢視控制器和檢視。 我們的範例應用程式，我們將會建立具有主要 UI_內容檢視_一邊並_Inspector 檢視_中第二個。

若要這樣做，我們需要先移除預設的檢視控制器和隨附的分鏡腳本的檢視所選取的是它在介面產生器及按下**刪除**金鑰：

[![](quickstart-images/qs05.png "移除預設檢視控制器")](quickstart-images/qs05.png#lightbox)

接下來，輸入`split`成**篩選**區域中，選取 垂直分割檢視控制器，並將它拖曳到_設計介面_:

[![](quickstart-images/qs06.png "搜尋 分割檢視控制器")](quickstart-images/qs06.png#lightbox)

請注意，控制器會自動包含兩個子檢視控制器 （和其相關的檢視），有線-最多的左邊和右邊的 [分割] 檢視。 若要將繫結至其父視窗的 分割 檢視，請按**控制**索引鍵、 按一下 視窗控制器 （視窗控制器的框架中的藍色圓形） 和將一條線拖曳至 分割檢視控制器。 選取 **視窗內容**從快顯視窗：

[![](quickstart-images/qs07.png "設定 windows 內容檢視")](quickstart-images/qs07.png#lightbox)

這會結合在一起使用 Segue 的兩個介面項目：

[![](quickstart-images/qs08.png "視窗與內容之間的 Segue")](quickstart-images/qs08.png#lightbox)

我們想要在分割檢視的左側放置文字檢視，並讓它自動填滿可用的區域，當視窗或 [分割] 檢視會調整大小。 文字檢視拖曳至檢視控制器會附加至分割檢視的頂端，按一下**Pin**自動版面配置條件約束 （從設計介面底部右邊的第二個圖示）。

[![](quickstart-images/qs09.png "設定條件約束")](quickstart-images/qs09.png#lightbox)

從這裡再按一下 [所有四個**i 字形狀**周圍的周框的圖示方塊上方的條件約束 Popover，然後都按一下**加入 4 個條件約束**底部新增必要的條件約束] 按鈕。

如果我們回到 Visual Studio for Mac，並執行專案，請注意，文字檢視自動調整大小以填滿視窗的 [分割] 檢視的左下的方，或分割而調整大小：

[![](quickstart-images/qs10.png "舉例來說，執行的應用程式")](quickstart-images/qs10.png#lightbox)

因為我們要做為偵測器區域使用右手邊的分割檢視，我們想要擁有較小的大小，並允許它摺疊。 返回 Xcode，然後編輯在設計介面中選取它，然後按一下右側的 檢視**大小偵測器**。 從這裡開始輸入**寬度**的`250`:

[![](quickstart-images/qs11.png "設定寬度")](quickstart-images/qs11.png#lightbox)

下一步 選取分割項目，表示右側中，設定較高**保留的優先順序**然後按一下**使用者可以摺疊**核取方塊：

[![](quickstart-images/qs12.png "編輯保存優先順序")](quickstart-images/qs12.png#lightbox)

如果我們回到 Visual Studio for Mac，並立即執行專案，請注意，右邊則會保留它是較小大小和視窗調整大小時：

[![](quickstart-images/qs13.png "舉例來說，執行的應用程式")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>定義呈現方式的 Segue

我們將版面配置做為選取的文字屬性偵測器的 [分割] 檢視的右邊。 我們會將一些控制項到下方檢視拖曳至配置偵測器的 UI。 最後一個控制項中，我們想要顯示 popover，可讓使用者選取從四個預設的字元樣式。

我們將新增按鈕，來偵測器和一個檢視控制器，到設計介面。 我們會將大小調整大小的檢視控制器我們想要我們 Popover，並將四個按鈕加入到它。 我們將在接下來**控制**金鑰-按一下 偵測器 檢視中的按鈕，並將拖曳至 檢視控制器，以代表我們 popover:

[![](quickstart-images/qs14.png "拖曳以建立新的 segue")](quickstart-images/qs14.png#lightbox)

我們將從快顯功能表中，選取**Popover**: 

[![](quickstart-images/qs15.png "選取 segue 類型")](quickstart-images/qs15.png#lightbox)

最後，我們將在設計介面中選取 Segue 並設定**慣用 Edge**要**左**。 然後，我們將從一行**錨點檢視**至我們想要附加至 popover 按鈕：

[![](quickstart-images/qs16.png "拖曳以建立新的 segue")](quickstart-images/qs16.png#lightbox)

如果我們回到 Visual Studio for Mac 中，執行應用程式，然後按一下**無**Inspector popover 中的按鈕將會顯示：

[![](quickstart-images/qs17.png "舉例來說，執行的 segue")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>建立應用程式喜好設定

大部分的標準 macOS 應用程式提供_喜好設定對話方塊_，可讓使用者定義數個選項可以控制應用程式，例如外觀或使用者帳戶的各種層面。

若要定義標準的喜好設定對話方塊視窗，第一次拖曳到設計介面的索引標籤檢視控制器：

[![](quickstart-images/qs18.png "編輯在 Xcode 中的分鏡腳本")](quickstart-images/qs18.png#lightbox)

同樣地，這將會自動由兩個檢視控制器附加的子系。 例如起見，我們會加入一個標籤會在其內部置中每個檢視：

[![](quickstart-images/qs19.png "設定條件約束")](quickstart-images/qs19.png#lightbox)

接下來，我們要顯示 [喜好設定] 視窗，當使用者選取**喜好設定...** 功能表項目。 從功能表列中，選取 [喜好設定] 功能表項目中，**控制**金鑰按一下，並將一條線拖曳至我們的索引標籤檢視控制器：

[![](quickstart-images/qs20.png "若要建立的 segue 拖曳")](quickstart-images/qs20.png#lightbox)

我們將從快顯視窗中，選取**強制回應**來顯示此視窗為強制回應對話方塊：

[![](quickstart-images/qs21.png "選取 segue 類型")](quickstart-images/qs21.png#lightbox)

如果我們將儲存的變更，回到 Visual Studio for Mac，請執行應用程式，並選取**喜好設定...** 功能表項目，我們新的喜好設定，就會顯示對話方塊：

[![](quickstart-images/qs22.png "舉例來說，執行的 segue")](quickstart-images/qs22.png#lightbox)

您可能會注意到，這看起來不像標準的 macOS 應用程式喜好設定對話方塊視窗。 若要修正此問題，包括兩個映像檔案在 Xamarin.Mac 應用程式之`Resources`中的資料夾**方案總管] 中**並返回 [Xcode 的 Interface Builder。

選取索引標籤檢視控制器並切換其**樣式**要**工具列**: 

[![](quickstart-images/qs23.png "設定索引標籤列樣式")](quickstart-images/qs23.png#lightbox)

選取每個索引標籤，並為它提供**標籤**，然後選取其中一個映像，來表示：

[![](quickstart-images/qs24.png "在 Xcode 中設定每個索引標籤")](quickstart-images/qs24.png#lightbox)

如果我們將儲存的變更，回到 Visual Studio for Mac，請執行應用程式，並選取**喜好設定...** 功能表項目，例如標準 macOS 應用程式，現在顯示的對話方塊：

[![](quickstart-images/qs25.png "舉例來說，執行 [喜好設定] 視窗")](quickstart-images/qs25.png#lightbox)

如需詳細資訊，請參閱我們[處理映像](~/mac/app-fundamentals/image.md)，[功能表](~/mac/user-interface/menu.md)， [Windows](~/mac/user-interface/window.md)並[對話方塊](~/mac/user-interface/dialog.md)文件。

## <a name="related-links"></a>相關連結

- [MacStoryboard （範例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [Windows 的簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
