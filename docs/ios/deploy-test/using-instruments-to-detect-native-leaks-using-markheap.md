---
title: 使用 Instruments 分析 Xamarin.iOS 應用程式
description: 本文件描述如何使用 Apple 的 Instruments 應用程式分析已安裝在裝置或模擬器上的 Xamarin.iOS 應用程式。
ms.prod: xamarin
ms.assetid: 70A8CAC8-20C2-655B-37C3-ACF9EA7874D8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 25129d532af0b146afedf28865649ffc9e38ee17
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785608"
---
# <a name="profiling-xamarinios-applications-with-instruments"></a>使用 Instruments 分析 Xamarin.iOS 應用程式

Xcode **Instruments** 是一種工具，可用來分析裝置上或模擬器中的 Xamarin.iOS 應用程式。 Mono 使用其 Just-in-Time 模型來編譯程式碼，因此 Instruments 也不會解譯此類資料，因此，可能很難從使用 Instruments 的模擬器型應用程式處理輸出。
因此，本指南將著重於如何使用開發人員應用程式解譯本文件中的 Instruments 輸出。

## <a name="requirements"></a>需求

Xcode Instruments 只能在 Mac 上執行。

## <a name="opening-the-instruments-app"></a>開啟 Instruments 應用程式

選取裝置，並執行 Instruments 應用程式：

1.  在 Visual Studio for Mac 中開啟 Xamarin.iOS 專案。
2.  選取 [偵錯|iPhone] 設定。
3.  將 iOS 裝置連接到電腦。
4.  在 [執行] 功能表中，選取 [上傳至裝置]。 現在將會建置應用程式，並上傳至裝置。
5.  在 [工具] 功能表中，選取 [啟動 Instruments]。


Instruments 現在將會開啟，並顯示下列對話方塊：

 [![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png "選擇分析範本")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments1.png#lightbox)

按一下以選取 [配置] 範本。 其他範本也有效，但是本文僅討論 [配置] 分析範本。

接下來，使用視窗頂端的功能表選取裝置和應用程式：

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png "選取裝置及應用程式")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments2.png#lightbox)

在視窗頂端的功能表中，應該選取了 iOS 裝置，而且旁邊應該選取了要分析的應用程式 (上方螢幕擷取畫面中的 **MemoryDemo**)。

如果裝置未列在功能表底下，請檢查 Visual Studio for Mac 中的 [主控台] 上是否有應用程式部署至裝置時可能會顯示的錯誤訊息。 此外，請確定已經透過 Xcode Organizer 佈建用於開發的裝置。

按一下 [選擇] 按鈕，下一個畫面應該就會出現：

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png "分析介面")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments3.png#lightbox)

按一下 [錄製] 按鈕 (左上方的紅色圓形)，即可開始分析。

下列螢幕擷取畫面顯示使用 **Instruments** 進行分析的範例：

[![](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png "使用 Instruments 執行分析的範例")](using-instruments-to-detect-native-leaks-using-markheap-images/instruments4.png#lightbox)

## <a name="summary"></a>總結

本指南說明如何啟動 Xcode Instruments 以便從 Visual Studio for Mac 內監視 iOS 應用程式。 如需如何使用 Instruments 診斷記憶體問題的範例，請繼續進行 [Instruments 逐步解說](~/ios/deploy-test/walkthrough-apples-instrument.md)。

## <a name="related-links"></a>相關連結

- [Instruments 逐步解說](~/ios/deploy-test/walkthrough-apples-instrument.md)
- [Xamarin.iOS 記憶體回收](https://krumelur.me/2015/04/27/xamarin-ios-the-garbage-collector-and-me/)
