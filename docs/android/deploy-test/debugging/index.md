---
title: 除錯 Xamarin.Android 應用程式上的裝置和模擬器
description: 如何針對您的 Xamarin.Android 應用程式進行測試和偵錯
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 3b3fa14ec81bd4f06322197b7140654f9086ce73
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75556479"
---
# <a name="debug-xamarinandroid-apps"></a>調試 Xamarin.安卓應用程式

本節討論如何偵錯裝置或模擬器上的 Xamarin.Android 應用程式。

## <a name="debugging-overview"></a>偵錯概觀

開發 Android 應用程式需要在實體硬體上或使用模擬器來執行應用程式。 使用硬體是最佳方式，但並不一定是最務實的做法。 在許多情況下，使用以下所述的其中一種模擬器來模擬 Android 硬體，會是較簡單且更符合成本效益的方式。

### <a name="debugging-on-the-android-emulator"></a>[在 Android Emulator 上偵錯](~/android/deploy-test/debugging/debug-on-emulator.md)

本文說明如何從 Visual Studio 啟動 Android Emulator，並在虛擬裝置中啟動您的應用程式。

### <a name="debugging-on-a-device"></a>[在裝置上進行偵錯](~/android/deploy-test/debugging/debug-on-device.md)

本文演示如何配置物理 Android 設備,以便 Xamarin.Android 應用程式可以直接從 Visual Studio 或 Mac 視覺工作室部署到它。

### <a name="android-debug-log"></a>[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)

開發人員所使用的其中一個非常常見的應用程式偵錯技巧就是使用 `Console.WriteLine`。 不過，在 Android 這類行動平台上並沒有主控台。 Android 裝置會提供一個記錄檔，您在撰寫應用程式時可能會需要用到此記錄檔。 這有時稱為**logcat,** 因為鍵入來檢索它的命令。 本文說明如何使用 **logcat**。
