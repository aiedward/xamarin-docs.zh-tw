---
title: Xamarin 中的分鏡腳本-快速入門
description: 本檔提供在 Xamarin. Mac 中使用分鏡腳本建立 macOS 使用者介面的快速入門簡介。 其中說明如何建立 segue 並建立喜好設定視窗。
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 2b8fede37354fd8a899a14c0710bf46e5a82b86a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026209"
---
# <a name="storyboards-in-xamarinmac-quick-start"></a>Xamarin 中的分鏡腳本-快速入門

為了快速介紹如何使用分鏡腳本來定義 Xamarin 應用程式的使用者介面，讓我們開始新的 Xamarin. Mac 專案。 選取 [Mac] > [應用程式][Cocoa 應用程式] > ，然後按一下 [下一步] 按鈕：

[![](quickstart-images/qs01.png "Adding a new Cocoa App")](quickstart-images/qs01.png#lightbox)

使用 `MacStoryboard` 的**應用程式名稱**，然後按 [**下一步]** 按鈕：

[![](quickstart-images/qs02.png "Setting the App Name")](quickstart-images/qs02.png#lightbox)

使用預設的 [**專案名稱**] 和 [**方案名稱**]，然後按一下 [**建立**] 按鈕：

[![](quickstart-images/qs03.png "The project and solution names")](quickstart-images/qs03.png#lightbox)

在 **方案總管**中，按兩下 `Main.storyboard` 檔案，將它開啟，以在 Xcode 的 Interface Builder 中進行編輯：

[![](quickstart-images/qs04.png "Editing the storyboard in Xcode")](quickstart-images/qs04.png#lightbox)

如您所見，預設分鏡腳本會同時定義應用程式的功能表列和其主視窗和 it 視圖控制器和視圖。 針對我們的範例應用程式，我們將建立一個並排_顯示主要內容視圖_的 UI，以及第二個的偵測_器視圖_。

若要這樣做，我們必須先在 Interface Builder 中選取並按下**Delete**鍵，以移除分鏡腳本隨附的預設 view Controller 和 view：

[![](quickstart-images/qs05.png "Removing the default view controller")](quickstart-images/qs05.png#lightbox)

接下來，在**篩選**區域中輸入 `split`，選取垂直分割視圖控制器並將它拖曳至  _Design Surface_：

[![](quickstart-images/qs06.png "Searching for the split view controller")](quickstart-images/qs06.png#lightbox)

請注意，控制器會自動包含兩個子視圖控制器（及其相關的視圖），並將其連接到分割視圖的左右側邊。 若要將分割視圖系結至它的父視窗，請按下**Control**鍵，按一下視窗控制器（視窗控制器框架中的藍色圓圈），然後將線條拖曳至分割視圖控制器。 從快顯選取 [**視窗內容]** ：

[![](quickstart-images/qs07.png "Setting the windows content view")](quickstart-images/qs07.png#lightbox)

這會使用 Segue 將兩個介面元素結合在一起：

[![](quickstart-images/qs08.png "The Segue between the window and the content")](quickstart-images/qs08.png#lightbox)

我們想要將文字視圖放在分割視圖的左邊，讓它在視窗或分割視圖調整大小時，自動填滿可用的區域。 將 [文字] 視圖拖曳至附加至 [分割] 視圖的頂端視圖控制器上，然後按一下 [**釘**選自動設定條件約束] （位於 Design Surface 底部右側的第二個圖示）。

[![](quickstart-images/qs09.png "Configuring the constraints")](quickstart-images/qs09.png#lightbox)

在這裡，我們將按一下 [條件約束] Popover 頂端的周框方塊周圍的四個**I**括弧圖示，然後按一下底部的 [**加入4個條件約束**] 按鈕，以新增必要的條件約束。

如果我們返回 Visual Studio for Mac 並執行專案，請注意，當視窗或分割調整大小時，文字視圖會自動調整大小以填滿分割視圖的左邊：

[![](quickstart-images/qs10.png "An example of the app running")](quickstart-images/qs10.png#lightbox)

因為我們要使用分割視圖的右手邊做為偵測器區域，所以我們想要讓它具有較小的大小，並允許折迭。 返回 Xcode，然後在 Design Surface 中選取並按一下 大小 偵測**器**，以編輯右側的視圖。 從這裡輸入 `250`的**寬度**：

[![](quickstart-images/qs11.png "Setting the width")](quickstart-images/qs11.png#lightbox)

接下來，選取代表右側的分割專案，設定較高的**保留優先順序**，然後按一下 [**使用者可以**折迭] 核取方塊：

[![](quickstart-images/qs12.png "Editing the holding priority")](quickstart-images/qs12.png#lightbox)

如果我們返回 Visual Studio for Mac 並立即執行專案，請注意，右側會保留較小的大小，而且會調整視窗大小：

[![](quickstart-images/qs13.png "An example of the app running")](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue" />

## <a name="defining-a-presentation-segue"></a>定義簡報 Segue

我們將配置分割視圖的右手邊，做為所選文字屬性的偵測器。 我們會將一些控制項拖曳至底部的視圖，以配置偵測器的 UI。 針對最後一個控制項，我們想要顯示可讓使用者從四個預設字元樣式中選取的 popover。

我們會將按鈕新增至偵測器，並在 Design Surface 中加入視圖控制器。 我們會調整 View Controller 的大小，使其成為我們想要 Popover 的大小，並在其中加上四個按鈕。 接下來，我們將**控制**[偵測器] 視圖中的 [按鍵] 按鈕，並將其拖曳至代表我們 Popover 的 View Controller：

[![](quickstart-images/qs14.png "Dragging to create a new segue")](quickstart-images/qs14.png#lightbox)

在快顯功能表中，我們會選取 [ **Popover**]： 

[![](quickstart-images/qs15.png "Selecting the segue type")](quickstart-images/qs15.png#lightbox)

最後，我們將在 Design Surface 中選取 Segue，並將**慣用的邊緣**設定為**Left**。 然後，我們會從 [**錨點] 視圖**將線條拖曳至我們想要附加 Popover 的按鈕：

[![](quickstart-images/qs16.png "Dragging to create a new segue")](quickstart-images/qs16.png#lightbox)

如果我們返回 Visual Studio for Mac，請執行應用程式，並按一下 [偵測器] 中的 [**無**] 按鈕，將會顯示 popover：

[![](quickstart-images/qs17.png "An example of the segue running")](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences" />

## <a name="creating-app-preferences"></a>建立應用程式喜好設定

大部分標準 macOS 應用程式都會提供_喜好設定對話方塊_，讓使用者定義數個選項來控制應用程式的各個層面，例如外觀或使用者帳戶。

若要定義標準喜好設定對話方塊視窗，請先將 索引標籤視圖控制器 拖曳至 Design Surface：

[![](quickstart-images/qs18.png "Editing the storyboard in Xcode")](quickstart-images/qs18.png#lightbox)

同樣地，這會自動隨附兩個子視圖控制器。 例如，我們會將標籤新增至將在其中置中的每個視圖：

[![](quickstart-images/qs19.png "Setting the constraints")](quickstart-images/qs19.png#lightbox)

接下來，我們想要在使用者選取 [**喜好設定 ...** ] 功能表項目時顯示 [喜好設定] 視窗。 從功能表列中，選取 [喜好設定] 功能表項目，按住**ctrl**鍵並將線條拖曳到索引標籤視圖控制器：

[![](quickstart-images/qs20.png "Dragging to create a segue")](quickstart-images/qs20.png#lightbox)

從快顯中，我們會**選取 [** 強制回應]，以強制回應對話方塊顯示此視窗：

[![](quickstart-images/qs21.png "Selecting the segue type")](quickstart-images/qs21.png#lightbox)

如果我們儲存變更，請返回 Visual Studio for Mac、執行應用程式，然後選取 [**喜好設定 ...** ] 功能表項目，就會顯示新的 [喜好設定] 對話方塊：

[![](quickstart-images/qs22.png "An example of the segue running")](quickstart-images/qs22.png#lightbox)

您可能會注意到，這看起來不像標準的 macOS 應用程式喜好設定交談視窗。 若要修正此問題，請在**方案總管**的 Xamarin 應用程式的 `Resources` 資料夾中包含兩個影像檔，然後返回 Xcode 的 Interface Builder。

選取 [索引標籤視圖控制器]，並將其**樣式**切換至**工具列**： 

[![](quickstart-images/qs23.png "Setting the tab bar style")](quickstart-images/qs23.png#lightbox)

選取每個索引**標籤**並為其命名，然後選取其中一個影像來代表它：

[![](quickstart-images/qs24.png "Configuring each tab in Xcode")](quickstart-images/qs24.png#lightbox)

如果我們儲存變更，請返回 Visual Studio for Mac、執行應用程式，然後選取 [**喜好設定 ...** ] 功能表項目，對話方塊現在會顯示為標準 macOS 應用程式：

[![](quickstart-images/qs25.png "An example of the running preferences window")](quickstart-images/qs25.png#lightbox)

如需詳細資訊，請參閱我們[使用影像](~/mac/app-fundamentals/image.md)、[功能表](~/mac/user-interface/menu.md)、[視窗](~/mac/user-interface/window.md)和[對話方塊](~/mac/user-interface/dialog.md)檔。

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [macOS 人性化介面指導方針](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/) \(英文\)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
