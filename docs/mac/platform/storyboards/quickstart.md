---
title: Xamarin 中的分鏡腳本–快速入門
description: 本檔提供在 Xamarin 中使用分鏡腳本建立 macOS 使用者介面的快速入門簡介。 它會說明如何建立 segue 並建立 [喜好設定] 視窗。
ms.prod: xamarin
ms.assetid: 20719B5D-8147-4E8A-A23C-8D575C7ACCEE
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 39e8e63c7612df95123e1e32edbfb3a8c28ffe37
ms.sourcegitcommit: 513feb0e07558766e3de4a898e53d56b27c20559
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697705"
---
# <a name="storyboards-in-xamarinmac--quick-start"></a>Xamarin 中的分鏡腳本–快速入門

在使用分鏡腳本定義 Xamarin 的消費者介面的快速簡介中，讓我們開始新的 Xamarin 專案。 選取 **Mac**  >  **應用** 程式  >  **Cocoa 應用程式**，然後按 [**下一步]** 按鈕：

[![新增 Cocoa 應用程式](quickstart-images/qs01.png)](quickstart-images/qs01.png#lightbox)

使用的 **應用程式名稱** `MacStoryboard` ，然後按 [ **下一步]** 按鈕：

[![設定應用程式名稱](quickstart-images/qs02.png)](quickstart-images/qs02.png#lightbox)

使用預設的 **專案名稱** 和 **方案名稱** ，然後按一下 [ **建立** ] 按鈕：

[![專案和方案名稱](quickstart-images/qs03.png)](quickstart-images/qs03.png#lightbox)

在 [ **方案總管** 中，按兩下檔案 `Main.storyboard` 將其開啟，以在 Xcode 的 Interface Builder 中進行編輯：

[![編輯 Xcode 中的分鏡腳本 Interface Builder。](quickstart-images/qs04.png)](quickstart-images/qs04.png#lightbox)

如您在上面所見，預設的腳本會使用它來定義應用程式的功能表列和其主視窗，以及它的 View Controller 和 View。 在我們的範例應用程式中，我們將建立在一端有主要 _內容視圖_ 的 UI，以及第二個視窗中的偵測 _器視圖_ 。

若要這樣做，我們必須先在 Interface Builder 中選取，然後按下 **Delete** 鍵，以移除分鏡腳本所附的預設 view 控制器和 view：

[![移除預設視圖控制器](quickstart-images/qs05.png)](quickstart-images/qs05.png#lightbox)

接下來，在 `split` [ **篩選** ] 區域中，選取 [垂直分割視圖控制器]，並將它拖曳至 _Design Surface_：

[![搜尋分割視圖控制器](quickstart-images/qs06.png)](quickstart-images/qs06.png#lightbox)

請注意，控制器會自動包含兩個子視圖控制器 (與其相關的視圖) 、連接到分割視圖的左右側邊。 若要將分割視圖系結至其父視窗，請按下 **ctrl** 鍵，然後按一下視窗控制器 (視窗控制器框架中的藍色圓圈) 然後將線條拖曳至 [分割視圖控制器]。 從快顯視窗中選取 **視窗內容** ：

[![設定 windows 內容視圖](quickstart-images/qs07.png)](quickstart-images/qs07.png#lightbox)

這會將兩個介面元素與 Segue 結合在一起：

[![視窗與內容之間的 Segue](quickstart-images/qs08.png)](quickstart-images/qs08.png#lightbox)

我們想要在分割視圖的左側放置文字視圖，並讓它在調整視窗或分割視圖大小時，自動填滿可用的區域。 將 [文字] 視圖拖曳至附加至 [分割] 視圖的上方視圖控制器，然後按一下 [ **釘** 選自動設定條件約束] (Design Surface) 底部右邊的第二個圖示。

[![設定條件約束](quickstart-images/qs09.png)](quickstart-images/qs09.png#lightbox)

在這裡，我們要在 [條件約束] Popover 頂端的周框方塊周圍按一下全部四個 **方塊，然後** 按一下底部的 [ **加入4個條件約束** ] 按鈕以新增必要的條件約束。

如果我們返回 Visual Studio for Mac 並執行專案，請注意文字視圖會自動調整大小，以在視窗中填滿分割視圖的左側，或調整分割的大小：

[![應用程式執行的範例，在視窗的左窗格中顯示文字。](quickstart-images/qs10.png)](quickstart-images/qs10.png#lightbox)

因為我們要使用分割區的右手邊做為偵測區，所以我們希望它有較小的大小，並允許它折迭。 返回 [Xcode]，並在 [Design Surface 中選取，然後按一下 [大小] 偵測 **器**，以編輯右側的視圖。 從這裡輸入 **寬度** `250` ：

[![設定寬度](quickstart-images/qs11.png)](quickstart-images/qs11.png#lightbox)

接下來，選取代表右邊的分割專案、設定較高的 **保留優先順序** ，然後按一下 [ **使用者可以** 折迭] 核取方塊：

[![編輯保留優先順序](quickstart-images/qs12.png)](quickstart-images/qs12.png#lightbox)

如果我們回到 Visual Studio for Mac 並立即執行專案，請注意，右邊會保持較小的大小，而且視窗會調整大小：

[![應用程式執行的範例，在視窗的較大左窗格中顯示文字。](quickstart-images/qs13.png)](quickstart-images/qs13.png#lightbox)

<a name="Defining-a-Presentation-Segue"></a>

## <a name="defining-a-presentation-segue"></a>定義簡報 Segue

我們將配置分割視圖的右手邊，作為所選取文字屬性的偵測器。 我們會將一些控制項拖曳至底端視圖，以配置偵測器的 UI。 在最後一個控制項中，我們想要顯示可讓使用者從四個預設字元樣式中選取的 popover。

我們會將按鈕新增至偵測器，並將 View Controller 新增至 Design Surface。 我們會將視圖控制器的大小調整為我們想要 Popover 的大小，然後在其中新增四個按鈕。 接下來，我們將在 [偵測器] 視圖的按鈕上 **控制** 按鍵，然後將它拖曳到代表我們 Popover 的 View Controller：

[![拖曳以在 View Controller 中建立新的 segue。](quickstart-images/qs14.png)](quickstart-images/qs14.png#lightbox)

在快顯功能表中，我們會選取 **Popover**： 

[![從 View 控制器選取 popover segue 類型。](quickstart-images/qs15.png)](quickstart-images/qs15.png#lightbox)

最後，我們會在 Design Surface 中選取 [Segue]，並將 **慣用的 Edge** 設定為 [ **左**]。 然後，我們會將一行從 **錨點視圖** 拖曳至我們想要附加 Popover 的按鈕：

[![藉由將錨點視圖附加至按鈕來拖曳以建立新的 segue。](quickstart-images/qs16.png)](quickstart-images/qs16.png#lightbox)

如果我們返回 Visual Studio for Mac，請執行應用程式，並按一下偵測器中的 [ **無** ] 按鈕，將會顯示 popover：

[![執行 segue 的範例，其中顯示 popover。](quickstart-images/qs17.png)](quickstart-images/qs17.png#lightbox)

<a name="Creating-App-Preferences"></a>

## <a name="creating-app-preferences"></a>正在建立應用程式喜好設定

大部分標準 macOS 應用程式都會提供 _喜好設定對話方塊_ ，可讓使用者定義數個選項來控制應用程式的各個層面，例如外觀或使用者帳戶。

若要定義標準喜好設定對話方塊視窗，請先將 [索引標籤視圖控制器] 拖曳至 Design Surface：

[![先將 [索引標籤視圖控制器] 拖曳至 Design Surface，以編輯 Xcode 中的分鏡腳本。](quickstart-images/qs18.png)](quickstart-images/qs18.png#lightbox)

同樣地，這將會自動隨附兩個子視圖控制器。 例如，我們會將標籤新增至將在其中中心內的每個視圖：

[![設定條件約束](quickstart-images/qs19.png)](quickstart-images/qs19.png#lightbox)

接下來，我們想要在使用者選取 [ **喜好** 設定] 功能表項目時，顯示 [喜好設定] 視窗。 從功能表列中，選取 [喜好設定] 功能表項目、 **控制** 按鍵，然後將線條拖曳至索引標籤視圖控制器：

[![拖曳以建立 segue](quickstart-images/qs20.png)](quickstart-images/qs20.png#lightbox)

在快顯視窗中，我們會 **選取 [** 強制回應]，將此視窗顯示為強制回應對話方塊：

[![從 [動作 Segue] 功能表中選取強制回應 segue 類型。](quickstart-images/qs21.png)](quickstart-images/qs21.png#lightbox)

如果我們儲存變更，請返回 Visual Studio for Mac，執行應用程式並選取 [ **喜好設定 ...** ] 功能表項目，將會顯示新的 [喜好設定] 對話方塊：

[![Segue 的範例，其中顯示 [新增喜好設定] 對話方塊。](quickstart-images/qs22.png)](quickstart-images/qs22.png#lightbox)

您可能會注意到，這看起來不像標準的 macOS 應用程式喜好設定對話方塊視窗。 若要修正此問題，請在方案總管中的 Xamarin 應用程式資料夾中包含兩個影像檔案， `Resources` 並返回 Xcode 的 Interface Builder。 

選取 [索引標籤視圖控制器]，然後將其 **樣式** 切換為 **工具列**： 

[![設定索引標籤列樣式](quickstart-images/qs23.png)](quickstart-images/qs23.png#lightbox)

選取每個索引標籤，並為其 **加上標籤** ，然後選取其中一個影像來代表它：

[![設定 Xcode 中的每個索引標籤](quickstart-images/qs24.png)](quickstart-images/qs24.png#lightbox)

如果我們儲存變更，請返回 Visual Studio for Mac，執行應用程式並選取 [ **喜好設定 ...** ] 功能表項目，對話方塊現在會顯示為標準的 macOS 應用程式：

[![執行喜好設定視窗的範例](quickstart-images/qs25.png)](quickstart-images/qs25.png#lightbox)

如需詳細資訊，請參閱使用 [影像](~/mac/app-fundamentals/image.md)、 [功能表](~/mac/user-interface/menu.md)、 [視窗](~/mac/user-interface/window.md) 和 [對話方塊](~/mac/user-interface/dialog.md) 檔。

## <a name="related-links"></a>相關連結

- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [macOS Human Interface Guidelines (人性化介面指導方針)](https://developer.apple.com/design/human-interface-guidelines/macos/overview/themes/)
- [Windows 簡介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
