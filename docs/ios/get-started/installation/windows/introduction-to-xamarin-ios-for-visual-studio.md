---
title: Xamarin.iOS for Visual Studio 簡介
description: 本文件描述如何使用 Visual Studio 來建置和測試 Xamarin iOS 應用程式。 並討論建立專案、執行和偵錯應用程式，並從 Windows 連線到 Mac 組建主機。
ms.prod: xamarin
ms.assetid: bf3c779f-959f-428d-babb-428f363f7e4e
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2018
ms.openlocfilehash: 5f2617272cfdc84fa2b835ce44919d2599a1dce6
ms.sourcegitcommit: d62732ce6f3f9d8dc929d72d4acac3e592cba073
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2019
ms.locfileid: "57197195"
---
# <a name="introduction-to-xamarinios-for-visual-studio"></a>Xamarin.iOS for Visual Studio 簡介

「適用於 Windows 的 Xamarin」可允許在 Visual Studio 內撰寫和測試 iOS 應用程式，並且會由已連上網路的 Mac 提供組建和部署服務。

本文涵蓋在每部電腦上安裝及設定 Xamarin.iOS 工具以使用 Visual Studio 來組建 iOS 應用程式的步驟。

在 Visual Studio 內進行 iOS 開發可提供一些優點：

-  為 iOS、Android 及 Windows 應用程式建立跨平台解決方案。
-  將慣用的 Visual Studio 工具 (例如 **Resharper** 和 **Team Foundation Server**) 用於所有跨平台專案，包括 iOS 原始程式碼。
-  既能與慣用的 IDE 搭配運作，又能利用 Xamarin.iOS 的所有 Apple API 繫結。

<a name="Requirements_and_Installation" />

## <a name="requirements-and-installation"></a>需求與安裝

在 Visual Studio 中進行 iOS 開發時，有幾個必須符合的需求。 如總覽中所簡述，必須要有一部 Mac 來編譯 IPA 檔案，且如果沒有 Apple 的憑證和程式碼簽署工具，便無法將應用程式部署至裝置。

有一些組態選項可供使用，因此您可以判斷哪個組態最符合您的開發需求。 以下列出這些組態：

-  使用 Mac 作為您的主要開發電腦，然後執行已安裝 Visual Studio 的 Windows 虛擬機器。 建議您使用 VM 軟體，例如 [Parallels](http://www.parallels.com/products/desktop/) \(英文\) 或 [VMWare](http://www.vmware.com/products/fusion/) \(英文\)。
-  僅將 Mac 當作組建主機使用。 在此案例中，它會連線至與已安裝[必要](~/get-started/installation/windows.md#installation)工具之 Windows 電腦相同的網路。

在上述任一情況中，您都應該依照下列步驟進行操作：

- [安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)
- [在 Windows 上安裝 Xamarin 工具](~/get-started/installation/windows.md)

## <a name="connecting-to-the-mac"></a>連線到 Mac

若要將 Visual Studio 連線至 Mac 組建主機，請遵循[與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南中的指示。

## <a name="visual-studio-toolbar-overview"></a>Visual Studio 工具列概觀

Xamarin iOS for Visual Studio 會在標準工具列和新的 iOS 工具列中新增項目。
以下說明這些工具列的功能。

### <a name="standard-toolbar"></a>標準工具列

以紅色圈起的是與 Xamarin iOS 開發相關的控制項：

 [![](introduction-to-xamarin-ios-for-visual-studio-images/03.png "以紅色圈起的是與 Xamarin iOS 開發相關的控制項")](introduction-to-xamarin-ios-for-visual-studio-images/03.png#lightbox "以紅色圈起的是與 Xamarin iOS 開發相關的控制項")

-  **開始** - 開始在選取的平台上進行應用程式偵錯或執行。 必須要有已連線的 Mac (請參閱 iOS 工具列中的狀態指標)。
-  **方案組態** – 可讓您選取要使用的組態 (例如 [偵錯]、[發行])。
-  **方案平台** - 可讓您選取 [iPhone] 或 [iPhoneSimulator] 來進行部署。

### <a name="ios-toolbar"></a>iOS 工具列

Visual Studio 中的 iOS 工具列在每個版本的 Visual Studio 中看起來都相似。 這些全部顯示如下：

[![](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png "iOS 工具列")](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png#lightbox)

以下說明每個項目：

-  **Mac Agent/連線管理員** – 顯示 [Xamarin Mac Agent] 對話方塊。 正在連線時，此圖示會顯示成「橘色」，已連線時，則會顯示成「綠色」。
-  **顯示 iOS 模擬器** – 將 [iOS 模擬器] 視窗顯示在 Mac 的最上層。
-  **顯示組建伺服器上的 IPA 檔案** – 將 Mac 上的 Finder 開啟在應用程式 IPA 輸出檔的位置。

## <a name="ios-output-options"></a>iOS 輸出選項

### <a name="output-window"></a>輸出視窗

[輸出] 窗格中有選項，您可以檢視來探索組建、部署及連線訊息與錯誤。

下面的螢幕擷取畫面顯示可用的輸出視窗，這些可能會依據您的專案類型而有所不同：

[![](introduction-to-xamarin-ios-for-visual-studio-images/output-sml.png "可用的輸出視窗")](introduction-to-xamarin-ios-for-visual-studio-images/output-large.png#lightbox)

- **Xamarin** – 這包含僅與 Xamarin 相關的資訊，例如與 Mac 的連線和啟用狀態。

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output3-sml.png "僅與 Xamarin 相關的資訊，例如與 Mac 的連線和啟用狀態")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

- **Xamarin 診斷** – 這會顯示 Xamarin 專案的更多相關詳細資訊，例如與 Android 的互動。

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output4-sml.png "Xamarin 專案的相關詳細資訊")](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

其他預設的 Visual Studio [輸出] 窗格 (例如 [偵錯] 和 [建置]) 在 [輸出] 檢視內仍有提供，可用於「偵錯輸出」和「MSBuild 輸出」：

-  **偵錯**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output2-sml.png "偵錯輸出")](introduction-to-xamarin-ios-for-visual-studio-images/output2-large.png#lightbox)

- **建置**和**建置順序**

    [![](introduction-to-xamarin-ios-for-visual-studio-images/output1-sml.png "MSBuild 輸出")](introduction-to-xamarin-ios-for-visual-studio-images/output1-large.png#lightbox)

## <a name="ios-project-properties"></a>iOS 專案屬性

若要存取 Visual Studio 的「專案屬性」，請在專案名稱上按一下滑鼠右鍵，然後選取操作功能表中的 [屬性]。 這將可讓您設定 iOS 應用程式，如以下螢幕擷取畫面所示：

 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosproperties.png "設定 iOS 應用程式")

-  iOS 套件組合簽署 – 連線到 Mac 以填入程式碼簽署身分識別和佈建設定檔：

 ![](introduction-to-xamarin-ios-for-visual-studio-images/bundlesigning.png "填入程式碼簽署身分識別和佈建設定檔")

-  iOS IPA 選項 – IPA 檔案將會儲存在 Mac 的檔案系統上：

 ![](introduction-to-xamarin-ios-for-visual-studio-images/ipaoptions.png "iOS IPA 選項")

-  iOS 執行選項 – 設定額外的參數：

 ![](introduction-to-xamarin-ios-for-visual-studio-images/iosrunoptions.png "iOS 執行選項")

## <a name="creating-a-new-project-for-ios-applications"></a>為 iOS 應用程式建立新專案

從 Visual Studio 內建立新 iOS 專案的方式與建立任何其他專案類型相同。 選取 [檔案] > [新增專案] 將會開啟以下所示的對話方塊，其中顯示一些可供建立新 iOS 專案的專案類型：

![建立新專案](introduction-to-xamarin-ios-for-visual-studio-images/newproject.w157.png)

選取 [iOS 應用程式 (Xamarin)] 會顯示下列用於建立新 Xamarin.iOS 應用程式的範本：

![選取 iOS 應用程式的範本](introduction-to-xamarin-ios-for-visual-studio-images/newproject-2.w157.png)

您可以在 Visual Studio 中使用「iOS 設計工具」來編輯分鏡腳本和 .xib 檔案。 若要建立「分鏡腳本」，請選擇其中一個「分鏡腳本」範本。 這會在 [方案總管] 中產生一個 **Main.storyboard** 檔案，如以下螢幕擷取畫面所示：

![[方案總管] 中的 Main.storyboard 檔案](introduction-to-xamarin-ios-for-visual-studio-images/solution-explorer-new.w157.png)

若要開始建立或編輯您的「分鏡腳本」，請按兩下 `Main.storyboard` 以在「iOS 設計工具」中開啟它：

![](introduction-to-xamarin-ios-for-visual-studio-images/iosdesigner.png "iOS 設計工具中的 Main.storyboard")

若要將物件新增到您的檢視中，請使用 [工具箱] 窗格將項目拖放到您的「設計介面」上。 您可以選取 [檢視] > [工具箱] 來新增 [工具箱] (如果尚未新增)。 您可以使用 [屬性] 窗格來修改物件屬性、調整其版面配置，以及建立事件，如下所示：

![](introduction-to-xamarin-ios-for-visual-studio-images/properties.png "[屬性] 窗格")

 如需有關使用 iOS 設計工具的詳細資訊，請參閱[設計工具](~/ios/user-interface/designer/index.md)指南。

## <a name="running--debugging-ios-applications"></a>執行 iOS 應用程式和進行偵錯

### <a name="device-logging"></a>裝置記錄

在 Visual Studio 2017 中，已整合 Android 和 iOS 記錄台。

Visual Studio 的新 [裝置記錄檔] 工具視窗可允許顯示 Android 和 iOS 裝置的記錄檔。 透過執行下列任何一個命令，即可顯示此記錄檔：

- [檢視] -> [其他視窗] -> [裝置記錄檔]
- [工具] > [iOS] > [裝置記錄檔]
- iOS 工具列 > [裝置記錄檔]

顯示工具視窗之後，使用者便可從裝置下拉式清單中選取實體裝置。 選取某個裝置時，記錄會自動新增到表格中。 在裝置之間切換會將裝置記錄停止後再啟動。

若要讓裝置出現在下拉式方塊中，必須載入 iOS 專案。 此外，針對 iOS，Visual Studio 必須[連線到 Mac 伺服器](~/ios/get-started/installation/windows/connecting-to-mac/index.md)，才能探索已連線到 Mac 的 iOS 裝置。

此工具視窗會提供：記錄項目表、可選取裝置的下拉式清單、清除記錄項目的方式、搜尋方塊，以及播放/停止/暫停按鈕。

### <a name="set-debugging-stops"></a>設定偵錯停止點

您可以在應用程式中的任何一點設定中斷點，以向偵錯工具發出訊號來暫時停止程式執行。 若要在您的 Visual Studio 中設定中斷點，請按一下您想要中斷之程式碼行號旁邊的編輯器邊界區域：

![](introduction-to-xamarin-ios-for-visual-studio-images/image18.png "設定偵錯點")

開始偵錯，然後使用模擬器或裝置將應用程式巡覽至中斷點。 達到中斷點時，將會醒目提示程式碼行並啟用 Visual Studio 的一般偵錯行為：您可以逐步執行、不進入或跳離程式碼、檢查區域變數，或是使用「即時運算視窗」。

以下螢幕擷取畫面顯示在 OS X 上使用 Parallels 於 Visual Studio 旁邊執行的「iOS 模擬器」：

![](introduction-to-xamarin-ios-for-visual-studio-images/image19.png "此螢幕擷取畫面顯示在 OS X 上使用 Parallels 於 Visual Studio 旁邊執行的「iOS 模擬器」")

### <a name="examine-local-variables"></a>檢查區域變數

![](introduction-to-xamarin-ios-for-visual-studio-images/image20.png "使用偵錯功能來檢查區域變數")

## <a name="summary"></a>總結

本文說明了如何使用 Xamarin iOS for Visual Studio。 其中列出了各種可用來從 Visual Studio 內建立、組建及測試 iOS 應用程式的功能，並且逐步解說如何組建簡單的 iOS 應用程式及對其進行偵錯。

## <a name="related-links"></a>相關連結

- [Xamarin.iOS 安裝](~/ios/get-started/installation/windows/index.md)
- [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)
- [以程式碼建立 iOS UI](~/ios/app-fundamentals/ios-code-only.md)
- [使用 XMA 將 Mac 連線到您的 Visual Studio 環境 (影片)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent) \(英文\)
