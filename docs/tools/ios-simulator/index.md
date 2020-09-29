---
title: 適用於 Windows 的遠端 iOS 模擬器
description: 適用于 Windows 的遠端 iOS 模擬器可讓您在 Windows 中的 iOS 模擬器上測試您的應用程式，以及 Visual Studio 2019。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: 826fb2bbfb202507bf4ffe062004fb0137e19176
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456661"
---
# <a name="remoted-ios-simulator-for-windows"></a>適用於 Windows 的遠端 iOS 模擬器

適用于 Windows 的遠端 iOS 模擬器可讓您在 Windows 中顯示的 iOS 模擬器上測試您的應用程式，Visual Studio 2019 和 Visual Studio 2017。

[![在 Windows 上執行的 iOS 模擬器](images/hero-sml.png "在 Windows 上執行的 iOS 模擬器")](images/hero.png#lightbox)

## <a name="getting-started"></a>開始使用

適用于 Windows 的遠端 iOS 模擬器會自動安裝為 Visual Studio 2019 和 Visual Studio 2017 中 Xamarin 的一部分。 若要使用它，請遵循下列步驟：

1. [將 Visual Studio 2019 與 Mac 組建主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在 Visual Studio 中，開始對 iOS 或 tvOS 專案進行偵錯工具。 適用于 Windows 的遠端 iOS 模擬器將會出現在您的 Windows 電腦上。

觀看 [這段影片](deploy.md) 以取得逐步指南。

## <a name="simulator-window"></a>模擬器視窗

模擬器視窗頂端的工具列包含一些實用的按鈕：

- **首頁** –模擬 iOS 裝置上的 [首頁] 按鈕。
- **鎖定** –鎖定模擬器 (滑動以解除鎖定) 。
- **螢幕擷取畫面**–儲存模擬器 (儲存在** \\ Pictures\Xamarin\iOS**模擬器) 的螢幕擷取畫面。
- [**設定**](#settings) –顯示鍵盤、位置和其他設定。
- [**其他選項**](#other-options) –會顯示各種模擬器選項，例如旋轉、搖動手勢和 Touch ID。

    [![iOS 模擬器對應範例](images/maps-app-sml.png "iOS 模擬器對應範例")](images/maps-app.png#lightbox)

## <a name="settings"></a>設定

按一下工具列的齒輪圖示會開啟 [ **設定** ] 視窗：

[![iOS 模擬器設定](images/settings-sml.png "iOS 模擬器設定")](images/settings.png#lightbox)

這些設定可讓您啟用硬體鍵盤、選擇裝置應回報的位置 (靜態和移動位置都支援) 、啟用觸控識別碼，以及重設模擬器的內容和設定。

## <a name="other-options"></a>其他選項

工具列的省略號按鈕會顯示其他選項，例如旋轉、搖動手勢和重新開機。 以滑鼠右鍵按一下模擬器視窗中的任何位置，即可將這些相同的選項視為清單：

[![iOS 模擬器其他設定](images/more-sml.png "iOS 模擬器其他設定")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>觸控式螢幕支援

大部分新式 Windows 電腦都有觸控畫面。 由於適用于 Windows 的遠端 iOS 模擬器支援觸控互動，您可以使用與實體 iOS 裝置搭配使用的相同縮小、滑動和多手指觸控手勢來測試您的應用程式。

同樣地，適用于 Windows 的遠端 iOS 模擬器會將 Windows 手寫筆輸入視為 Apple 鉛筆輸入。

## <a name="sound-handling"></a>音效處理

模擬器播放的聲音將來自主機 Mac 的喇叭。
Windows 電腦上沒有聽到 iOS 音效。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>停用 Windows 的遠端 iOS 模擬器

若要停用 Windows 的遠端 iOS 模擬器，請流覽至 [ **工具] > 選項 > Xamarin > IOS 設定** ，並取消核取 [ **對 windows 的遠端**模擬器]。

[![使用模擬器的核取方塊](images/options-sml.png "使用模擬器的核取方塊")](images/options.png#lightbox)

停用此選項時，會在連線的 Mac 組建主機上開啟 iOS 模擬器。

## <a name="troubleshooting"></a>疑難排解

如果您在遠端 iOS 模擬器中遇到問題，您可以在下列位置中查看記錄：

- **Mac** – `~/Library/Logs/Xamarin/Simulator.Server`
- **Windows** – `%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

如果您 [在 Visual Studio 中回報問題](/visualstudio/ide/how-to-report-a-problem-with-visual-studio)，附加這些記錄檔可能會很有説明 (有一些選項可讓上傳私用) 。