---
title: 多處理序偵錯
ms.prod: xamarin
ms.assetid: 852F8AB1-F9E2-4126-9C8A-12500315C599
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/24/2017
ms.openlocfilehash: ec2203b668365290fc9df78e63b401fb71ead7e6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="multi-process-debugging"></a>多處理序偵錯

以 Visual Studio for Mac 開發的現代解決方案有多個以不同平台為目標的專案是極普遍的。 例如，解決方案可能會有須仰賴 Web 服務專案提供之資料的行動應用程式專案。 在開發此解決方案時，開發人員可能需要同時執行兩個專案，以對錯誤進行疑難排解。 自 [Xamarin Cycle 9 版](https://releases.xamarin.com/stable-release-cycle-9/)開始，Visual Studio for Mac 現在能對多個同時執行的處理序進行偵錯。 這樣能夠設定中斷點、檢查變數以及檢視一個以上執行中專案的執行緒。 這也稱為「多處理序偵錯」。 

本指南會討論一些對 Visual Studio for Mac 所做的變更，以 支援對多處理序進行偵錯、如何設定解決方案以對多處理序進行偵錯，以及如何使用 Visual Studio for Mac 來附加至現有處理序。

## <a name="requirements"></a>需求

對多處理序進行偵錯需要 Visual Studio for Mac。

## <a name="ide-changes"></a>IDE 變更

為了協助開發人員進行多處理序偵錯，Visual Studio for Mac 已對其使用者介面做出一些變更。 Visual Studio for Mac 已更新**偵錯工具列**，並在 [解決方案選項] 資料夾中新增 [解決方案組態] 區段。 此外，[執行緒面板] 現在會顯示執行中的處理序與每個處理序的執行緒。 Visual Studio for Mac 也會顯示多個偵錯面板，每個處理序都有一個，以進行 [應用程式輸出] 之類的特定事項。

### <a name="solution-configurations"></a>方案組態

根據預設，Visual Studio for Mac 會在偵錯工具列的 [解決方案組態] 區域中顯示個別專案。 當偵錯工作階段開始時，這就是 Visual Studio for Mac 會啟動並附加偵錯工具的專案。

若要在 Visual Studio for Mac 中啟動多處理序並針對多處理序進行偵錯，就必須建立「解決方案組態」。 解決方案組態會描述按一下 [開始] 按鈕或按下 &#8984;&#8617; (**Cmd-Enter**) 啟動偵錯工作階段時應包括解決方案中的那些專案。 下列螢幕擷取畫面是有多個解決方案組態的 Visual Studio for Mac 解決方案範例：

![](multi-process-debugging-images/mpd01-xs.png "有多個解決方案組態的解決方案")

### <a name="parts-of-the-debug-toolbar"></a>偵錯工具列的構成元素

偵錯工具列已變更為允許透過快顯功能表選取解決方案組態。 這個螢幕擷取畫面顯示偵錯工具列的構成元素：

![](multi-process-debugging-images/mpd02-xs.png "偵錯工具列的構成元素")

1. **解決方案組態** - 您能夠按一下偵錯工具列中的解決方案組態，然後從快顯功能表中選取組態來設定解決方案組態：

    ![](multi-process-debugging-images/mpd03-xs.png "含有解決方案組態的範例快顯功能表")

2. **建置目標** - 這可以識別專案的建置目標。 這和舊版的 Visual Studio for Mac 相同。
3. **裝置目標** - 這可以選取將要執行解決方案的裝置。 您能用來識別每個專案的個別裝置或模擬器：

    ![](multi-process-debugging-images/mpd04-xs.png "顯示專案之裝置的快顯功能表")

### <a name="multiple-debug-pads"></a>多個偵錯面板

啟動多個解決方案組態時，有些 Visual Studio for Mac 面板會出現多次，每個處理序都有一個。 例如，下列螢幕擷取畫面顯示執行兩個專案的解決方案有兩個 [應用程式輸出] 面板：

![](multi-process-debugging-images/mpd05-xs.png "解決方案組態的輸出面板")

### <a name="multiple-processes-and-the-active-thread"></a>多處理序和使用中執行緒

在一個處理序中遇到中斷點時，該處理序會暫停執行，而其他處理序則繼續執行。 在只有一個處理序的情況下，Visual Studio for Mac 能以一組面板輕鬆顯示執行緒、區域變數、應用程式輸出等的資訊。 然而，當多個處理序有多個中斷點並可能有多個執行緒時，偵錯工作階段嘗試一次顯示所有執行緒 (與處理序) 的所有資訊，肯定會讓開發人員應接不暇。

為了解決此問題，Visual Studio for Mac 一次只會顯示一個執行緒的資訊，這稱為「使用中執行緒」。 於中斷點暫停的第一個執行緒，即視為「使用中執行緒」。 使用中執行緒就是開發人員關注的執行緒。 您可對使用中執行緒發出 [不進入函式] &#8679;&#8984;O (Shift-Cmd-O) 等偵錯命令。

**執行緒面板**會顯示解決方案組態中正在檢查的所有處理序與執行緒資訊，並提供使用中執行緒為何的視覺提示：

![](multi-process-debugging-images/mpd06-xs.png "解決方案組態的執行緒面板")

執行緒是依據裝載執行緒的處理序來分組。 使用中執行緒的專案名稱與識別碼會以粗體文字顯示，而且巡覽邊中的使用中執行緒旁邊會顯示向右箭頭。 在上一個螢幕擷取畫面中，**處理序識別碼 48703** (**FirstProject**) 中的**執行緒 #1** 就是使用中執行緒。

對多處理序進行偵錯時，能夠使用 [執行緒面板] 來切換使用中執行緒，以查看該處理序的偵錯資訊。 若要切換使用中執行緒，請在 [執行緒面板] 中選取想要的執行緒，然後按兩下它。

#### <a name="stepping-through-code-when-multiple-projects-are-stopped"></a>當多個專案都停止時逐步執行程式碼

當兩個 (或多個) 專案有中斷點時，Visual Studio for Mac 會將兩個處理序都暫停。 只有在使用中執行緒中的程式碼才能進行**不進入函式**。 在範圍變更讓偵錯工具能夠切換使用中執行緒的焦點之後，另一個處理序才會暫停。 例如，請考量 Visual Studio for Mac 對兩個專案進行偵錯的下列螢幕擷取畫面：

![](multi-process-debugging-images/mpd09-xs.png  "Visual Studio for Mac 對兩個專案進行偵錯")

在此螢幕擷取畫面中，每個解決方案都有自己的中斷點。 開始偵錯之後，所遇到的第一個中斷點是 **SecondProject** 中 `MainClass` 的**第 10 行**。 因為這兩個專案都有中斷點，所以會中止每一個處理序。 遇到中斷點之後，每次引動**不進入函式**都會造成 Visual Studio for Mac 不進入使用中執行緒中的程式碼。

只限使用中執行緒可不進入函式，因此 Visual Studio for Mac 會一次逐步執行一行程式碼，而另一個處理序依然處於暫停狀態。

以上一個螢幕擷取畫面為例，當 `for` 迴圈完成後，Visual Studio for Mac 會允許 **FirstProject** 執行，直到遇到 `MainClass` 中**第 11 行**的中斷點。 針對每個**不進入函式**命令，偵錯工具會在 **FirstProject** 中逐行前進，直到 Visual Studio for Mac 的內部啟發式演算法將使用中執行緒切換回 **SecondProject**。

如果只有其中一個專案設定中斷點，則只會暫停那個處理序。 另一個專案會繼續執行，直到開發人員予以暫停或加入中斷點為止。

### <a name="pausing-and-resuming-a-processes"></a>暫停和繼續處理序

以滑鼠右鍵按一下處理系，然後從快顯功能表選取 [暫停] 或 [繼續]，即可暫停或繼續處理序：

![](multi-process-debugging-images/mpd08-xs.png "在執行緒面板中暫停或繼續")

偵錯工具列的外觀會根據正在進行偵錯之專案的狀態而變更。 當多個專案正在執行時，偵錯工具列會在至少有一個專案在執行中且有一個專案處於暫停之處，顯示 [暫停] 和 [繼續] 按鈕：

![](multi-process-debugging-images/mpd07-xs.png  "偵錯工具列")

按一下 [偵錯工具列] 中的 [暫停] 按鈕，可將正在偵錯的所有處理序暫停，而按一下 [繼續] 按鈕則可讓所有暫停的處理序繼續執行。

### <a name="debugging-a-second-project"></a>對第二個專案進行偵錯

在由 Visual Studio for Mac 啟動第一個專案之後，您也能夠對第二個專案進行偵錯。 第一個專案啟動之後，*以滑鼠右鍵按一下 [Solution Pad] 中的專案，然後選取 [Start Debugging Item] \(開始針對項目進行偵錯\)：

![](multi-process-debugging-images/mpd13-xs.png  "Start Debugging Item (開始針對項目進行偵錯)")

## <a name="creating-a-solution-configuration"></a>建立解決方案組態

以 [開始] 按鈕起始偵錯工作階段時，「解決方案組態」會告知 Visual Studio for Mac 要執行哪項專案。 每個方案都能有一個以上的解決方案組態。 這可讓您在對專案進行偵錯時，指定要執行哪些專案。

在 Xamaring Studio 中建立新的解決方案組態：

1. 在 Visual Studio for Mac 中，開啟 [解決方案選項] 對話方塊，然後選取 [執行] > [組態]：

    ![](multi-process-debugging-images/mpd10-xs.png "[解決方案選項] 對話方塊中的 [解決方案組態]")

2. 按一下 [新增] 按鈕，輸入新解決方案組態的名稱，然後按一下 [建立]。 新的解決方案組態會出現在 [組態] 視窗中：

    ![](multi-process-debugging-images/mpd11-xs.png  "為新的解決方案組態命名")

3. 選取組態清單中的新執行組態。 [解決方案選項] 對話方塊會顯示解決方案中的每個專案。 選取應在偵錯工作階段啟動時啟動的每個專案：

    ![](multi-process-debugging-images/mpd12-xs.png "選取要開始的專案")

現在 **MultipleProjects** 解決方案組態會出現在 [Debug toolbar] \(偵錯工具列\) 中，讓開發人員能夠同時對兩個專案進行偵錯。

## <a name="summary"></a>總結

本指南討論如何對 Visual Studio for Mac 中的多處理序進行偵錯。 文中涵蓋一些為了支援多處理序偵錯的 IDE 變更，並描述一些相關的行為。

## <a name="related-links"></a>相關連結

- [Xamarin Cycle 9 版本資訊](https://releases.xamarin.com/stable-release-cycle-9/)
