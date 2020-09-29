---
title: 設定 Visual Studio for iOS 開發
description: 本文描述如何設定 Visual Studio 2019 for Xamarin.iOS 開發 。 尤其著重討論如何設定已安裝的 Xamarin.iOS 版本、iOS 工具列以及 [方案平台] 下拉式功能表。
ms.prod: xamarin
ms.assetid: 22D82244-890D-4325-B3CC-C0AC49130BCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 0f57c10ea263a3dcf882d2cf75a57105e7f7d0d4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434528"
---
# <a name="configuring-visual-studio-for-ios-development"></a>設定 Visual Studio for iOS 開發

_本文描述 Visual Studio 的各種 Xamarin.iOS 組態選項。_

## <a name="using-matching-xamarinios-versions"></a>使用相符的 Xamarin.iOS 版本

Visual Studio 2019 或 Visual Studio 2017 必須使用在 Mac 組建主機上安裝的相同 Xamarin.iOS 版本。 確保此條件成立：

- 如果您使用 Visual Studio 2019 或 Visual Studio 2017，請選取 Visual Studio for Mac 中的**穩定**更新通道。

- 如果您使用 Visual Studio 2019 Preview，請選取 Visual Studio for Mac 中的 [Alpha]**** 更新通道。

> [!NOTE]
> 從 [Visual Studio 2017 15.6 版](/visualstudio/releasenotes/vs2017-relnotes#automatic-macos-provisioning) 開始，Visual Studio 2017 會自動偵測 Mac 組建主機是否使用與 Windows 相同的 Xamarin.iOS 版本。 如果版本不符，則 Visual Studio 2017 會提供以在 Mac 組建主機上遠端安裝正確的版本。 如需詳細資訊，請參閱[與 Mac 配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)指南的[自動 Mac 佈建](~/ios/get-started/installation/windows/connecting-to-mac/index.md#automatic-mac-provisioning)小節。

## <a name="ios-toolbar"></a>iOS 工具列

在 Visual Studio 2019 或 Visual Studio 2017 中開啟 iOS 專案時，應該會顯示 iOS 工具列。  它預設會包含可用於 Xamarin.iOS 開發的四個按鈕：

![Visual Studio 2019 的 iOS 工具列](config-options-images/ios-toolbar.png)

- **與 Mac 配對**：開啟 [與 Mac 配對] 對話方塊。 在 Visual Studio 2019 或 Visual Studio 2017 中開啟 iOS 專案時啟用。
- **顯示 iOS 模擬器**：在 Mac 組建主機上，將 iOS 模擬器顯示在最上層。 在 Visual Studio 2019 或 Visual Studio 2017 中開啟 iOS 專案時啟用。
- **裝置記錄**：啟動可讓您檢查裝置記錄的視窗。 在 Visual Studio 2019 或 Visual Studio 2017 中開啟 iOS 專案時啟用。
- **顯示組建伺服器上的 IPA 檔案**：在 Mac 組建主機上開啟視窗，並顯示應用程式之 .ipa 檔案的位置。 在完成建立 .ipa 的組建之後啟用。

如果此工具列未出現，則請在 Visual Studio 2019 或 Visual Studio 2017 中開啟 [檢視]**** 功能表，然後選擇 [工具列] > [iOS]****：

![啟用 iOS 工具列](config-options-images/ios-toolbar-enable.png "啟用 iOS 工具列")

## <a name="solution-platforms-drop-down-menu"></a>方案平台下拉式功能表

[方案平台]**** 下拉式功能表可讓您選擇下一個組建要以實體裝置還是模擬器為目標。

確定此下拉式功能表出現在 [標準] 工具列上：

- 在 Visual Studio 2019 或 Visual Studio 2017 中，按一下 [標準] 工具列右邊緣的向下箭號。
- 選擇 [新增或移除按鈕]**** 
- 確定已核取 [方案平台]**** 項目：

![啟用 [方案平臺] 下拉式功能表](config-options-images/solution-platforms-enable.png "啟用 [方案平臺] 下拉式功能表")

開啟 iOS 專案時，[標準]**** 和 [iOS]**** 工具列現在應該類似下列螢幕擷取畫面：

![標準和 iOS 工具列](config-options-images/toolbars.png "標準和 iOS 工具列")