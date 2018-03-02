---
title: "在 Windows 上安裝 Xamarin.iOS"
description: "本文說明如何設定 Xamarin.iOS for Visual Studio。 其中涵蓋 Visual Studio 之 Xamarin 擴充功能的安裝程序，並探討如何連線到安裝在 Mac 上的 Apple SDK。"
ms.topic: article
ms.prod: xamarin
ms.assetid: abf85d3e-a365-44a2-b1a4-6c572c7f76dd
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/29/2017
ms.openlocfilehash: e3416b0551bafcd7ab87b21eafbe56b733f359a4
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="installing-xamarinios-on-windows"></a>在 Windows 上安裝 Xamarin.iOS

_本文說明如何設定 Xamarin.iOS for Visual Studio。其中涵蓋適用於 Visual Studio 之 Xamarin 擴充功能的安裝程序，並探討如何連線到安裝在 Mac 上的 Apple SDK。_

## <a name="overview"></a>總覽

Xamarin.iOS for Visual Studio 可允許在 Windows 電腦上撰寫和測試 iOS 應用程式，並且會由已連上網路的 Mac 提供組建和部署服務。

在 Visual Studio 內進行 iOS 開發可提供下列功能：

- 為 iOS、Android 及 Windows 應用程式建立跨平台解決方案。
- 將 Visual Studio 工具 (例如 *Resharper* 和 *Team Foundation Server*) 用於所有跨平台專案，包括 iOS 原始程式碼。
- 既能與慣用的 IDE 搭配運作，又能利用 Xamarin.iOS 的所有 Apple API 繫結

Xamarin.iOS for Visual Studio 支援的組態包括 Visual Studio 在 Mac 上的 Windows 虛擬機器 (使用 Parallels 或 VMWare) 內執行，或是位於 Mac 所在相同網路中可看見的個別電腦上。 不論哪一種組態最適合您，Visual Studio 都會使用 SSH 以立即且安全的方式連線到 Mac。

本文涵蓋在 Mac 和 Windows 電腦上安裝及設定 Xamarin.iOS 工具的步驟，以及連線到 Mac 主機以便讓開發人員使用 Visual Studio 來進行 Xamarin.iOS 應用程式組建、偵錯及部署的步驟。

下圖顯示一個簡單的 Xamarin.iOS 開發工作流程概觀：

[![Xamarin.iOS 開發工作流程](images/xma2.png)](images/xma2.png)

> [!IMPORTANT]
>  Visual Studio 實際上會啟動個別的 MSBuild 處理序來建置專案。 此處理序會建立與 Mac 的新連線，也就是說當 Visual Studio 進行組建時，實際上有兩個從 Windows 到 Mac 的 SSH 連線。 從[命令列](~/ios/get-started/installation/windows/connecting-to-mac/index.md)進行組建只會建立一個 MSBuild 處理序。 為了簡單起見，此圖表的所有連線都只由一個箭號代表。

## <a name="requirements"></a>需求

Xamarin.iOS for Visual Studio 實現卓越的技術：不僅可讓開發人員在 Windows 電腦上使用 Visual Studio IDE 來建立、組建 iOS 應用程式，還可針對這些應用程式進行偵錯。 它無法單獨完成這些工作 – 在沒有 Apple 編譯器的情況下，無法建立 iOS 應用程式，而在沒有 Apple 憑證及程式碼簽署工具的情況下，也無法部署這些應用程式。 這意謂著 Xamarin.iOS for Visual Studio 安裝必須連線到已連上網路的 Mac OS X 電腦，才能執行這些工作。 設定好之後，Xamarin 的工具將會儘可能讓程序無縫接軌。


<a name="system-requirements">

### <a name="system-requirements"></a>系統需求

系統需求包括：

### <a name="windows"></a>Windows

1. Windows 7 或更新版本。

2. Visual Studio 2015 Professional 或更新版本

    a. 如果您有 Enterprise 版授權，您將必須安裝 Visual Studio Enterprise。

3. Xamarin for Visual Studio。

Xamarin 工具無法與 Visual Studio Express 版搭配使用，因為缺少外掛程式支援。 Visual Studio Community 支援 Xamarin。

### <a name="mac"></a>Mac

1. 執行 macOS Sierra (10.12) 或更新版本 (但建議使用最新穩定版) 的 Mac。

2. Xamarin iOS SDK。 這會在下載 Visual Studio for Mac 時安裝

3. Apple 的 Xcode IDE 和 iOS SDK (建議使用來自 Mac App Store 的最新穩定版本)。

**Windows 電腦必須要能夠透過網路連線到 Mac。**

<a name="appleaccount" />

### <a name="apple-developer-account"></a>Apple 開發人員帳戶

若要將應用程式部署至裝置，或將應用程式提交給 App Store，必須要有「Apple 開發人員」帳戶。 必須在已連上網路的 Mac 上建立及安裝相關的開發人員憑證和佈建設定檔，Xamarin.iOS for Visual Studio 才能運作。 如需了解取得開發憑證和佈建裝置的步驟，請參閱[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)一文。

<a name="features" />

## <a name="features"></a>功能 

Xamarin.iOS for Visual Studio 可讓您從 Windows 建立、編輯、組建及部署 Xamarin.iOS 專案。 這包含下列功能：

- 建立新的 iOS 專案。

- 編輯 iOS 專案，以及也包括 Xamarin.Android 和 UWP 專案在內的跨平台解決方案。

- 編譯 iOS 專案，以及也包括 Xamarin.Android 和 UWP 專案在內的跨平台解決方案。

- 使用「iOS 設計工具」的分鏡腳本和 .xib 支援。

- 進行 iOS 應用程式的部署和偵錯，其中應用程式本身是在模擬器中執行，或在已連線至 Mac 的裝置上執行。

- Windows 上的 iOS 模擬器 – 如需有關在 Windows 上使用 iOS 模擬器的詳細資訊，請參閱[這份指南](~/tools/ios-simulator.md)。

<a name="configuring" />

## <a name="configuring-your-mac"></a>設定 Mac


### <a name="installation"></a>安裝

若要在 Mac 主機上安裝 Xamarin.iOS 工具，您必須[安裝 Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/installation)。

安裝軟體之後，請依照下一節中的步驟在 macOS 上設定 Xamarin.iOS，以允許 Xamarin for Visual Studio 與其連線。

> [!IMPORTANT]
>  Windows 電腦必須與所連線的 Mac 使用相同的 Xamarin.iOS 版本。 確保此條件成立：
>
> - **Visual Studio 2015 和更早的版本**：確定您與 Visual Studio for Mac 位於相同的[更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)上。
>
> - **Visual Studio 2017 發行版本**：確定您位於 Visual Studio for Mac 的**穩定**通道上。
>
> - **Visual Studio 2017 預覽版本**：確定您位於 Visual Studio for Mac 的 **Alpha 版**通道上。

<a name="configuration" />


### <a name="configuration"></a>組態

若要存取 Visual Studio 的 Xamarin 擴充功能與 Mac 之間的通訊，您將必須在 Mac 上允許**遠端登入**。 請依照下列步驟來進行這項設定：

1. 開啟 *Spotlight* (同時按**Cmd 鍵和空格鍵**) 並搜尋**遠端登入**，然後選取 [共享] 結果項目。 這會開啟 [系統偏好設定] 的 [共享] 面板。

![Spotlight 的遠端登入搜尋](images/spotlight.png)

2. 勾選左側 [服務] 清單中的 [遠端登入] 選項，以允許 Xamarin for Visual Studio 連線到 Mac。

![勾選 [服務] 清單中的 [遠端登入] 選項](images/sharing.png)

3. 確定 [遠端登入] 已設定為允許 [所有使用者] 存取，或是右側清單之允許的使用者清單中包含您的 Mac 使用者名稱或群組。

如果 Mac 與 Visual Studio 位於相同的網路上，Visual Studio 現在應該就能探索到它。

> [!NOTE]
> 如果您的 macOS 防火牆已設定為預設封鎖已簽署的應用程式，則您可能需要允許 `mono-sgen` 接收傳入連線。 如果是這種情況，將會顯示警示對話方塊來提示您。

<a name="developersetup"/>

### <a name="ios-developer-setup"></a>iOS 開發人員設定

就 iOS 開發而言，為 Mac 電腦設定相關的簽署身分識別相當重要。 這可讓您正確地簽署應用程式，以便透過 App Store 或「臨機操作」散發這些應用程式。 請依照下列連結，取得有關設定 Mac 以使用 Xamarin 進行 iOS 開發的指示：

- [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md?ide=vs)

設定好 Mac 之後，接著即可設定 Windows 電腦。

<a name="windowsinstallation">

## <a name="windows-installation"></a>Windows 安裝

Xamarin 可以隨著 Visual Studio 2017 或 2015 安裝一起安裝。 若要安裝適用於 Xamarin 的 Visual Studio 工具，請參閱 [Windows 安裝](~/cross-platform/get-started/installation/windows.md)指南。

## <a name="installation-complete"></a>安裝完成

安裝程序完成之後，仍然還需要進行一些步驟，才能讓所有項目運作：

- [將 Visual Studio 連線到 Mac](#connectingtomac) – Visual Studio 必須先連線到 Mac 組建主機，才能組建 Xamarin.iOS 專案。
- [設定 Visual Studio 工具列](#Visual_Studio_Toolbar_Overview) – 這將可讓您在 Visual Studio 中輕鬆存取 Xamarin.iOS 功能。

<a name="connectingtomac" /> 

### <a name="connecting-to-the-mac"></a>連線到 Mac

Xamarin.iOS for Visual Studio 會透過電腦間的 SSH 連線，建立與 Mac 組建主機的連線。 如需有關此連線的詳細資訊，請參閱[連線到 Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) 指南。

若要連線到 Mac，請依照下列步驟進行操作：

- 瀏覽至 [工具] > [選項]，然後在 [Xamarin] 底下，選取 [iOS 設定]：

  [ ![[iOS 設定] 畫面](images/image2.png)](images/image2.png)

- 如果已正確地[設定](#configuration) Mac 來允許**遠端登入**，您應該就能在清單中選取您的 Mac：

  [![[遠端主機] 對話方塊](images/xma3.png)](images/xma3.png)

- 這會提示您輸入 Mac 主機的系統管理認證：

  [![登入對話方塊](images/xma4.png)](images/xma4.png)

- 當您已連線時，電腦名稱的旁邊會顯示 [連接成功] 圖示：

  [![電腦名稱旁邊顯示 [連接成功] 圖示的 [遠端主機] 對話方塊](images/image6.png)](images/image6.png)

每次您啟動 Visual Studio 時，都會重新連線。

<a name="toolbar" />

## <a name="visual-studio-toolbar-configuration"></a>Visual Studio 工具列組態

當 iOS 專案開啟時，預設會顯示 iOS 工具列而無須設定。

在 iOS 工具列沒有出現的情況下，可以使用下列步驟。

若要設定此工具列，請先開啟 [檢視] > [工具列] 功能表，然後確定已選取 [iOS] 項目。 請選擇此螢幕擷取畫面中顯示的功能表項目，應該勾選此項目來指示顯示工具列：

[![選擇 [工具列] > [iOS]](images/image31.png)](images/image31.png)

### <a name="visual-studio-2015"></a>Visual Studio 2015

在 Visual Studio 2017 之前的版本中，可能需要將 [方案平台] 按鈕新增至標準工具列。 這樣可允許在偵錯時選取「iOS 裝置」或「iOS 模擬器」。 請依照下列指示來進行這項設定

按一下標準列右邊的功能表按鈕：

- 選擇 [新增或移除按鈕]
- 選取 [方案平台]

[![選取 [方案平台]](images/image35.png)](images/image35.png)

**標準**和 **iOS** 工具列現在應該會像這個螢幕擷取畫面：

[![標準和 iOS 工具列現在應該會像這個螢幕擷取畫面](images/image36.png)](images/image36.png)

完成工具列組態之後，您即可開始使用 Xamarin.iOS for Visual Studio。

## <a name="summary"></a>總結

本文提供了安裝、設定及使用 Xamarin.iOS for Visual Studio 的逐步指南。

其中涵蓋了在 Windows 和 Mac OS X 上安裝及設定先決條件工具。


## <a name="related-links"></a>相關連結

- [安裝](~/cross-platform/get-started/installation/windows.md)
- [裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)
- [Xamarin.iOS for Visual Studio 簡介](~/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio.md)
- [使用 XMA 將 Mac 連線到您的 Visual Studio 環境 (影片)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent) \(英文\)
