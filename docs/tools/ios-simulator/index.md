---
title: 適用於 Windows 的遠端 iOS 模擬器
description: Windows 的遠端 iOS 模擬器允許您在 Windows 中與 Visual Studio 2019 一起顯示的 iOS 模擬器上測試應用。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: davidortinau
ms.author: daortin
ms.date: 04/26/2019
ms.openlocfilehash: d5898f9c6ee30eb1f12bf6480b93a609e762e6ea
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886589"
---
# <a name="remoted-ios-simulator-for-windows"></a>適用於 Windows 的遠端 iOS 模擬器

Windows 的遠端 iOS 模擬器允許您在 Windows 中顯示的 iOS 模擬器上測試應用,同時顯示 Visual Studio 2019 和 Visual Studio 2017。

[![在 Windows 上執行的 iOS 模擬器](images/hero-sml.png "在 Windows 上執行的 iOS 模擬器")](images/hero.png#lightbox)

## <a name="getting-started"></a>開始使用

Windows 的遠端 iOS 模擬器作為 Xamarin 在 Visual Studio 2019 和 Visual Studio 2017 中的一部分自動安裝。 要使用它,請按照以下步驟操作:

1. [將 Visual 2019 與 Mac 建構主機配對](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在可視化工作室中,開始調試 iOS 或 tvOS 專案。 Windows 的遠端 iOS 模擬器將顯示在 Windows 電腦上。

觀看[此視頻](deploy.md),獲得分步指南。

## <a name="simulator-window"></a>模擬器視窗

模擬器視窗頂部的工具列包含許多有用的按鈕:

- **主頁**= 類比 iOS 裝置上的主頁按鈕。
- **鎖定**+ 鎖定模擬器(輕掃以解鎖)。
- **螢幕截圖**– 儲存模擬器的螢幕截圖(儲存在**圖片\\_Xamarin_iOS模擬器**中)。
- [**設定**](#settings)= 顯示鍵盤、位置和其他設定。
- [**其他選項**](#other-options)– 提供各種模擬器選項,如旋轉、搖搖手勢和觸控 ID。

    [![iOS 模擬器地圖範例](images/maps-app-sml.png "iOS 模擬器地圖範例")](images/maps-app.png#lightbox)

## <a name="settings"></a>設定

點選工具列的齒輪圖示將開啟 **「設定」** 視窗:

[![iOS 模擬器設定](images/settings-sml.png "iOS 模擬器設定")](images/settings.png#lightbox)

這些設置允許您啟用硬體鍵盤、選擇設備應報告的位置(同時支援靜態和行動位置)、啟用Touch ID以及重置模擬器的內容和設置。

## <a name="other-options"></a>其他選項

工具列的省略號按鈕顯示其他選項,如旋轉、搖動手勢和重新啟動。 以右鍵按下模擬器視窗中的任意位置,可以將這些相同的選項視為清單:

[![iOS 模擬器附加設定](images/more-sml.png "iOS 模擬器附加設定")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>觸控式螢幕支援

大多數現代 Windows 計算機都有觸控式螢幕。 由於 Windows 的遠端 iOS 模擬器支援觸控互動,因此可以使用與物理 iOS 裝置相同的捏合、輕掃和多指觸控手勢來測試應用。

同樣,Windows 的遠端 iOS 模擬器將 Windows 手寫筆輸入視為 Apple 鉛筆輸入。

## <a name="sound-handling"></a>音效處理

模擬器播放的聲音將來自主機 Mac 的揚聲器。
在 Windows 電腦上聽不到 iOS 聲音。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>關閉 Windows 的遠端 iOS 模擬器

要關閉 Windows 的遠端 iOS 模擬器,請瀏覽到**工具>選項> Xamarin > iOS 設定**,並取消選取**遠端模擬器到 Windows**。

[![勾選方塊用於使用模擬器](images/options-sml.png "勾選方塊用於使用模擬器")](images/options.png#lightbox)

禁用此選項後,除錯將打開連接的 Mac 建構主機上的 iOS 模擬器。

## <a name="troubleshooting"></a>疑難排解

如果您在遠端 iOS 模擬器中遇到問題,您可以在以下位置查看日誌:

- **Mac** |`~/Library/Logs/Xamarin/Simulator.Server`
- **視窗**|`%LOCALAPPDATA%\Xamarin\Logs\Xamarin.Simulator`

如果在[Visual Studio 中報告問題](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio),則附加這些日誌可能會有所説明(有選項可以保持上載的私密性)。
