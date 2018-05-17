---
title: 適用於 Windows 的遠端 iOS 模擬器
description: 遠端 iOS 模擬器 for Windows 可讓您測試您的應用程式在 iOS 模擬器與 Visual Studio 2017 一起視窗中顯示。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: topgenorth
ms.author: toopge
ms.date: 05/11/2018
ms.openlocfilehash: b07cc24e63f4aa3ce4451e3bdb5819f1df1058c6
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2018
---
# <a name="remoted-ios-simulator-for-windows"></a>適用於 Windows 的遠端 iOS 模擬器

遠端 iOS 模擬器 for Windows 可讓您測試您的應用程式在 iOS 模擬器與 Visual Studio 2017 一起視窗中顯示。

[![](ios-simulator-images/hero-sml.png "iOS 模擬器在 Windows 上執行")](ios-simulator-images/hero.png#lightbox)

## <a name="getting-started"></a>使用者入門

遠端 iOS 模擬器 for Windows 會自動安裝的 Visual Studio 2017 Xamarin 的一部分。 若要使用它，請遵循下列步驟：

1. [建立 Mac 的主機配對 Visual 2017](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在 Visual Studio 2017，開始偵錯 iOS 或 tvOS 專案。 Windows 遠端 iOS 模擬器會出現在您的 Windows 電腦。

## <a name="simulator-window"></a>模擬器視窗

在模擬器 視窗頂端工具列包含有用的按鈕數目：

- **家用**– 模擬 iOS 裝置上的 [首頁] 按鈕
- **鎖定**– 鎖定模擬器 （撥動以解除鎖定）
- **螢幕擷取畫面**– 儲存模擬器的螢幕擷取畫面
- [**設定**](#settings) – 顯示鍵盤、 位置和其他設定
- [**其他選項**](#other-options) – 會開啟不同的模擬器選項，例如旋轉和搖晃手勢

    [![](ios-simulator-images/maps-app-sml.png "iOS 模擬器對應範例")](ios-simulator-images/maps-app.png#lightbox)

## <a name="settings"></a>設定

按一下工具列的齒輪圖示開啟**設定**視窗：

[![](ios-simulator-images/settings-sml.png "iOS 模擬器設定")](ios-simulator-images/settings.png#lightbox)

這些設定可讓您啟用硬體鍵盤，請選擇裝置必須的位置 （靜態和移動位置同時支援） 的報表，請啟用 Touch ID，以及重設內容及設定適用於模擬器。

## <a name="other-options"></a>其他選項

工具列的省略符號按鈕會顯示其他選項，例如旋轉、 搖晃手勢，和重新開機。 這些相同的選項都可視為清單上按一下滑鼠右鍵在模擬器 視窗中的任何位置：

[![](ios-simulator-images/more-sml.png "iOS 模擬器中的其他設定")](ios-simulator-images/more.png#lightbox)

## <a name="touchscreen-support"></a>觸控螢幕支援

大多數最新型的 Windows 電腦有觸控式螢幕。 由於遠端 iOS 模擬器 for Windows 支援觸控的互動，您可以測試您的應用程式相同的縮小、 撥動和您使用實體 iOS 裝置的多重指觸控手勢。

同樣地，遠端 iOS 模擬器 for Windows 會將 Windows 手寫筆輸入視為 Apple 鉛筆輸入。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>停用遠端 iOS 模擬器 for Windows

若要停用遠端 iOS 模擬器的 Windows，請導覽至**工具 > 選項 > Xamarin > iOS 設定**並取消核取**遠端 windows 模擬器**。

[![](ios-simulator-images/options-sml.png "核取方塊以使用模擬器")](ios-simulator-images/options.png#lightbox)

停用這個選項，偵錯會開啟連接的 Mac 上的 iOS 模擬器建置的主機。
