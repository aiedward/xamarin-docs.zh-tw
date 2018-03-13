---
title: "遠端 iOS 模擬器 （適用於 Windows)"
description: "在 Windows 上的 Visual Studio 中的測試及偵錯 iOS 應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 04/07/2017
ms.openlocfilehash: 0ea6528ce3523ab5d829342e99ccf35ca3d69aa2
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>遠端 iOS 模擬器 （適用於 Windows)

_在 Windows 上的 Visual Studio 中的測試及偵錯 iOS 應用程式_

[![](ios-simulator-images/hero-sml.png "iOS 模擬器在 Windows 上執行")](ios-simulator-images/hero.png#lightbox)

## <a name="download-and-install"></a>下載並安裝

下載[installer](https://dl.xamarin.com/xamarin-simulator/Xamarin.Simulator.Installer.msi)並安裝 Windows 電腦上。 應該已安裝 for Xamarin visual Studio Tools。

> [!NOTE]
> 使用遠端 iOS 模擬器需要 Visual Studio 搭配 Xamarin 的安裝網路的 Mac。

## <a name="getting-started"></a>快速入門

若要使用遠端 iOS 模擬器：

1. 請確定 Visual Studio 啟動遠端 iOS 模擬器之前，至少一次連接到您的 Mac。
2. 確保應用程式的 iOS 或 tvOS**啟始專案**並開始偵錯。

您可以停用遠端 iOS 模擬器**工具 > 選項 > Xamarin > iOS 設定**取消核取方塊**遠端 windows 模擬器**如下所示：

[![](ios-simulator-images/options-sml.png "核取方塊以使用模擬器")](ios-simulator-images/options.png#lightbox)

IOS 模擬器，然後會開啟連接的 Mac 電腦上。 勾選此選項可開啟遠端 iOS 模擬器。

## <a name="features"></a>功能

在遠端 iOS 模擬器為您提供測試和偵錯 iOS 應用程式，完全由 Windows 上的 Visual Studio 模擬器上的方法。

### <a name="simulator-window"></a>模擬器視窗

視窗工具列包含數個按鈕與模擬器互動：

- **家用**– 模擬裝置上的 [首頁] 按鈕。
- **鎖定**– 鎖定的模擬器 （可以撥動解除鎖定）。
- **螢幕擷取畫面**– 將模擬器的螢幕擷取畫面儲存至磁碟。
- [**設定**](#settings) – 設定鍵盤和位置。
 - 其他[**選項**](#options) – 各種不同的模擬器選項可用，例如旋轉、 搖晃，或叫用在模擬器中的其他狀態。 有些選項模糊，當從出現在工具列上，或以滑鼠右鍵按一下視窗上的省略符號圖示就可以存取它們。

    [![](ios-simulator-images/maps-app-sml.png "iOS 模擬器對應範例")](ios-simulator-images/maps-app.png#lightbox)


### <a name="settings"></a>設定

「 齒輪 」 圖示會開啟**設定**視窗：

[![](ios-simulator-images/settings-sml.png "iOS 模擬器設定")](ios-simulator-images/settings.png#lightbox)

這可讓您啟用硬體鍵盤上的模擬器，並選擇哪些位置會回報給裝置 （包括靜態位置或其他移動的位置選項）。



### <a name="other-options"></a>其他選項

若要檢視所有可用的模擬器，例如旋轉、 觸發搖晃手勢，並重新啟動模擬器中的選項 [模擬器] 視窗中按一下滑鼠右鍵：

[![](ios-simulator-images/more-sml.png "iOS 模擬器中的其他設定")](ios-simulator-images/more.png#lightbox)

### <a name="touchscreen-support"></a>觸控螢幕支援

大多數最新型的 Windows 電腦有觸控式螢幕，並遠端 iOS 模擬器可讓您接觸模擬器視窗來測試 iOS 應用程式中的使用者互動。

這包括就撥動，以及多個單指觸控手勢-先前無法只可輕鬆地測試實體裝置的項目。

Windows 中的手寫筆支援也會轉譯到 Apple 鉛筆模擬器上的輸入。

<!--
<a name="knownissues" />

# Known Issues

 - Apple Watch devices may show in the Visual Studio device list, but are not yet supported.
 - Launching in **Release** mode may also start Apple’s simulator on the networked Mac.
 - Closing the remote iOS Simulator on Windows will not immediately stop debugging in Visual Studio. Stop debugging manually from the menu or the red button.
 - Opening too many different simulators simultaneously will produce unexpected results.
 - Exception of type `Foundation.NSErrorException` may be thrown while launching Simulators. Workaround is to kill csproxy (server process) on the Mac host and re-deploy to the simulator.
 - Performance may be slower when using Xcode 8
-->
