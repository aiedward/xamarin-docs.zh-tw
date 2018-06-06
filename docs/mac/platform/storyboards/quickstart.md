---
title: 分鏡腳本中 Xamarin.Mac-快速入門
description: 本文件提供建置 macOS 分鏡腳本 Xamarin.Mac 中包含使用者介面的快速簡介。 說明如何建立 segue，並建立喜好設定 視窗。
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/02/2017
ms.openlocfilehash: 2bf91a51a55583e2ba8ca1fc09eb3dcd0d9986cf
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792567"
---
# <a name="storyboards-in-xamarinmac--quick-start"></a>分鏡腳本中 Xamarin.Mac-快速入門

為快速介紹使用腳本來定義 Xamarin.Mac 應用程式的使用者介面，讓我們開始新 Xamarin.Mac 專案。 選取 [Mac] > [應用程式][Cocoa 應用程式] > ，然後按一下 [下一步] 按鈕：

[![](quickstart-images/qs01.png "加入新的 Cocoa 應用程式")](quickstart-images/qs01.png#lightbox)

使用**應用程式名稱**的`MacStoryboard`按一下**下一步**按鈕：

[![](quickstart-images/qs02.png "設定應用程式名稱")](quickstart-images/qs02.png#lightbox)

使用預設**專案名稱**和**方案名稱**按一下**建立**按鈕：

[![](quickstart-images/qs03.png "專案和方案名稱")](quickstart-images/qs03.png#lightbox)

在**方案總管 中**，連按兩下`Main.storyboard`開啟 Xcode 的介面產生器中進行編輯的檔案：

[![](quickstart-images/qs04.png "編輯在 Xcode 中的分鏡腳本")](quickstart-images/qs04.png#lightbox)

您可以看到上方，分鏡腳本的預設值會定義應用程式的功能表列和與其其主視窗檢視控制器和檢視。 針對我們的範例應用程式，我們將會建立具有主要 UI_內容檢視_某一端和_偵測器檢視_中第二個。

若要這樣做，我們需要先移除預設檢視控制器和隨附的分鏡腳本的檢視所選取的是它中介面產生器及按**刪除**機碼：

[![](quickstart-images/qs05.png "移除預設檢視控制站")](quickstart-images/qs05.png#lightbox)

接著，輸入`split`到**篩選**區域中，選取垂直分割檢視控制器，並將它拖曳至_設計介面_:

[![](quickstart-images/qs06.png "搜尋的分割檢視控制器")](quickstart-images/qs06.png#lightbox)

請注意，控制器會自動包含兩個子檢視控制器 （和其相關的檢視），有線總至左邊和右邊的 分割檢視。 若要將繫結到它的父視窗的 [分割] 檢視，請按**控制項**索引鍵、 視窗控制站 （在視窗中控制站的框架中的藍色圓形） 上按一下和拖曳一條線來分割檢視控制器。 選取**視窗內容**從快顯視窗：

[![](quickstart-images/qs07.png "設定 windows 內容檢視")](quickstart-images/qs07.png#lightbox)

這會結合在一起使用 Segue 的兩個介面項目：

[![](quickstart-images/qs08.png "視窗與內容之間 Segue")](quickstart-images/qs08.png#lightbox)

我們想要將文字檢視放在左邊的分割檢視，並調整大小的視窗或 [分割] 檢視時，自動填滿可用的區域。 文字檢視拖曳至檢視控制器附加至分割檢視的頂端，按一下  **Pin**自動配置條件約束 （中的第二個圖示，在設計介面底部的權限）。

[![](quickstart-images/qs09.png "設定條件約束")](quickstart-images/qs09.png#lightbox)

我們將從這裡按一下所有四個**的 i 字形狀**周圍的周框的圖示上方的條件約束 Popover 方塊，然後都按一下**加入 4 條件約束**底端加入必要的條件約束 按鈕。

如果我們返回 Visual Studio for Mac，並執行專案，請注意，文字檢視自動調整大小以填滿視窗或分割分割檢視的左半部將會調整大小：

[![](quickstart-images/qs10.png "執行應用程式的範例")](quickstart-images/qs10.png#lightbox)

因為我們使用分割檢視的右邊為偵測器區域，我們想要擁有較小的大小，並允許它摺疊。 返回 Xcode，並編輯所選取的設計介面中，並按一下右側的 檢視**大小 Inspector**。 從這裡輸入**寬度**的`250`:

[![](quickstart-images/qs11.png "設定寬度")](quickstart-images/qs11.png#lightbox)

下一步選取分割項目代表右側，設定較高**持有優先順序**按一下**使用者可以摺疊**核取方塊：

[![](quickstart-images/qs12.png "編輯保存優先順序")](quickstart-images/qs12.png#lightbox)

如果我們返回 Visual Studio for Mac 並立即執行專案，請注意右側，即會保留它較小者為大小和視窗會調整大小：

[![](quickstart-images/qs13.png "執行應用程式的範例")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>定義呈現方式話題

我們即將配置做為選取的文字屬性偵測器的 [分割] 檢視的右邊。 我們將配置的偵測器 UI，將某些控制項拖曳到下方檢視中。 最後一個控制項，我們想要顯示讓使用者能夠從四個預設的字元樣式選取 popover。

我們會將按鈕加入到偵測器並檢視控制器設計介面。 我們會將大小調整的大小檢視控制器我們想要我們 Popover 並加入四個按鈕。 接下來我們將**控制項**偵測器 檢視中的按鈕上的索引鍵按一下並拖曳至表示我們 popover 檢視控制器：

[![](quickstart-images/qs14.png "若要建立新的 segue 拖曳")](quickstart-images/qs14.png#lightbox)

我們將從快顯功能表中，選取**Popover**: 

[![](quickstart-images/qs15.png "選取 segue 類型")](quickstart-images/qs15.png#lightbox)

最後，我們會在設計介面中選取 Segue，並設定**慣用邊緣**至**左**。 然後，我們將在其中拖曳中的行**錨點檢視**我們想要連接至 popover 按鈕：

[![](quickstart-images/qs16.png "若要建立新的 segue 拖曳")](quickstart-images/qs16.png#lightbox)

如果我們 for Mac 返回 Visual Studio 執行應用程式，然後按一下**無**中的偵測器 popover 按鈕將會顯示：

[![](quickstart-images/qs17.png "執行 segue 的範例")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>建立應用程式喜好設定

大部分的標準 macOS 應用程式提供_喜好設定對話方塊_，可讓使用者定義數個選項可以控制應用程式，例如外觀或使用者帳戶的各種層面。

若要定義標準的喜好設定對話方塊視窗，第一次拖曳到設計介面的索引標籤檢視控制器：

[![](quickstart-images/qs18.png "編輯在 Xcode 中的分鏡腳本")](quickstart-images/qs18.png#lightbox)

同樣地，這會自動變成使用兩個檢視控制器附加的子系。 例如起見，我們會將標籤加入會在其內部置中每個檢視：

[![](quickstart-images/qs19.png "設定條件約束")](quickstart-images/qs19.png#lightbox)

接下來，我們想要顯示喜好設定 視窗，當使用者選取**喜好設定...** 功能表項目。 從功能表列中，選取 喜好設定功能表項目，**控制項**金鑰按一下和拖曳一條線到我們的索引標籤檢視控制站：

[![](quickstart-images/qs20.png "若要建立 segue 拖曳")](quickstart-images/qs20.png#lightbox)

我們將從快顯視窗中，選取**強制回應**來顯示此視窗為強制回應對話方塊：

[![](quickstart-images/qs21.png "選取 segue 類型")](quickstart-images/qs21.png#lightbox)

如果我們儲存變更，回到 Visual Studio for Mac 上，執行應用程式，然後選取**喜好設定...** 功能表項目 對話方塊將會顯示我們新的喜好設定：

[![](quickstart-images/qs22.png "執行 segue 的範例")](quickstart-images/qs22.png#lightbox)

您可能會發現，這看起來不像標準 macOS 應用程式喜好設定對話方塊視窗。 若要修正此問題，包含 Xamarin.Mac 應用程式的兩個影像檔`Resources`資料夾中的**方案總管 中**並返回 Xcode 的介面產生器。

選取索引標籤檢視控制器並切換其**樣式**至**工具列**: 

[![](quickstart-images/qs23.png "設定索引標籤列樣式")](quickstart-images/qs23.png#lightbox)

選取每個索引標籤，然後為它提供**標籤**並選取其中一個來代表它的映像：

[![](quickstart-images/qs24.png "在 Xcode 中設定每個索引標籤")](quickstart-images/qs24.png#lightbox)

如果我們儲存變更，回到 Visual Studio for Mac 上，執行應用程式，然後選取**喜好設定...** 功能表項目，像標準 macOS 應用程式，現在顯示的對話方塊：

[![](quickstart-images/qs25.png "執行 [喜好設定] 視窗的範例")](quickstart-images/qs25.png#lightbox)

如需詳細資訊，請參閱我們[處理映像](~/mac/app-fundamentals/image.md)，[功能表](~/mac/user-interface/menu.md)， [Windows](~/mac/user-interface/window.md)和[對話方塊](~/mac/user-interface/dialog.md)文件。

## <a name="related-links"></a>相關連結

- [MacStoryboard （範例）](https://developer.xamarin.com/samples/mac/MacStoryboard/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用視窗](~/mac/user-interface/window.md)
- [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\)
- [簡介 Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
