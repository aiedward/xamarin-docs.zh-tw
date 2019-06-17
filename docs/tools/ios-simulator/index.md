---
title: 適用於 Windows 的遠端 iOS 模擬器
description: 遠端 iOS 模擬器的 Windows 可讓您測試您的應用程式，在 iOS 模擬器與 Visual Studio 2019 的 Windows 中顯示。
ms.prod: xamarin
ms.assetid: 63c50190-7e54-4140-a30d-1a0e577c47d7
author: lobrien
ms.author: laobri
ms.date: 04/02/2019
---

# <a name="remoted-ios-simulator-for-windows"></a>適用於 Windows 的遠端 iOS 模擬器

遠端 iOS 模擬器的 Windows 可讓您測試您的應用程式，在 iOS 模擬器與 Visual Studio 2019 和 Visual Studio 2017 的 Windows 中顯示。

[![在 Windows 上執行的 iOS 模擬器](images/hero-sml.png "在 Windows 上執行的 iOS 模擬器")](images/hero.png#lightbox)

## <a name="getting-started"></a>使用者入門

遠端 iOS 模擬器的 Windows 會自動安裝 Visual Studio 2019 和 Visual Studio 2017 中的 Xamarin 的一部分。 若要使用它，請遵循下列步驟：

1. [至 Mac 組建主機配對 Visual 2019](~/ios/get-started/installation/windows/connecting-to-mac/index.md)。
2. 在 Visual Studio 中開始偵錯 iOS 或 tvOS 專案。 遠端 iOS 模擬器的 Windows 會出現在 Windows 電腦上。

監看式[這段影片](deploy.md)的逐步指南。

## <a name="simulator-window"></a>模擬器視窗

在模擬器 視窗頂端工具列會包含數個實用的按鈕：

- **家用**– 模擬 iOS 裝置上的 [首頁] 按鈕。
- **鎖定**– 鎖定模擬器 （撥動以解除鎖定）。
- **螢幕擷取畫面**– 儲存模擬器的螢幕擷取畫面 (儲存在**Pictures\Xamarin\iOS 模擬器\\**)。
- [**設定**](#settings) – 顯示鍵盤、 位置和其他設定。
- [**其他選項**](#other-options) -會顯示各種模擬器選項，例如旋轉、 搖晃手勢和 [Touch id]。

    [![iOS 模擬器對應範例](images/maps-app-sml.png "iOS 模擬器對應範例")](images/maps-app.png#lightbox)

## <a name="settings"></a>設定

按一下工具列的齒輪圖示開啟**設定**視窗：

[![iOS 模擬器設定](images/settings-sml.png "iOS 模擬器設定")](images/settings.png#lightbox)

這些設定可讓您啟用硬體鍵盤，請選擇 裝置應該的位置 （靜態和移動位置同時支援） 的報表，啟用 Touch ID，以及重設的內容和設定模擬器。

## <a name="other-options"></a>其他選項

工具列的省略符號按鈕會顯示其他選項，例如旋轉、 搖晃手勢和 重新啟動。 這些相同的選項可視為清單，用滑鼠右鍵按一下模擬器 視窗中的任何位置：

[![iOS 模擬器中的其他設定](images/more-sml.png "iOS 模擬器中的其他設定")](images/more.png#lightbox)

## <a name="touchscreen-support"></a>觸控式螢幕支援

現今大部分的 Windows 電腦有觸控式螢幕。 由於遠端 iOS 模擬器的 Windows 支援觸控互動，您可以測試您的應用程式使用相同的捏合、 撥動和您使用實體 iOS 裝置的多指觸控手勢。

同樣地，遠端 iOS 模擬器的 Windows 會將 Windows 手寫筆輸入視為 Apple 鉛筆輸入。

## <a name="disabling-the-remoted-ios-simulator-for-windows"></a>停用遠端 iOS 模擬器的 Windows

若要停用遠端 iOS 模擬器的 Windows，請瀏覽至**工具 > 選項 > Xamarin > iOS 設定**並取消核取**遠端 Simulator 到 Windows**。

[![核取方塊以使用模擬器](images/options-sml.png "核取方塊以使用模擬器")](images/options.png#lightbox)

停用這個選項，偵錯會開啟已連線的 Mac 上的 iOS 模擬器組建主機。
