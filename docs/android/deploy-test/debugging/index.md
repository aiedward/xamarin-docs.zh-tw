---
title: 偵錯
description: 如何針對您的 Xamarin.Android 應用程式進行測試和偵錯
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/16/2018
ms.openlocfilehash: 429a369ddcd11829920f9fb932a737d1a53cec10
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="debugging"></a>偵錯

## <a name="debugging-overview"></a>偵錯概觀

開發 Android 應用程式需要在實體硬體上或使用模擬器來執行應用程式。 使用硬體是最佳方式，但並不一定是最務實的做法。 在許多情況下，使用以下所述的其中一種模擬器來模擬 Android 硬體，會是較簡單且更符合成本效益的方式。


### <a name="android-sdk-emulatorandroiddeploy-testdebuggingandroid-sdk-emulatorindexmd"></a>[Android SDK 模擬器](~/android/deploy-test/debugging/android-sdk-emulator/index.md) \(英文\)

這些文章說明如何使用 Android SDK 隨附的預設模擬器。 此模擬器適用於 Visual Studio for Windows 和 Visual Studio for Mac。

### <a name="visual-studio-android-emulatorandroiddeploy-testdebuggingvisual-studio-android-emulatormd"></a>[Visual Studio Android 模擬器](~/android/deploy-test/debugging/visual-studio-android-emulator.md)

這篇文章說明如何使用 Visual Studio 2015 內建的 Android 模擬器，來針對您的 Xamarin.Android 應用程式進行偵錯和測試。 如果您使用 Visual Studio 2015 且不需要自訂裝置設定檔，此模擬器就會是您的理想選擇。

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[在裝置上進行偵錯](~/android/deploy-test/debugging/debug-on-device.md)

這篇文章說明如何設定實體 Android 裝置，以便直接從 Visual Studio 或 Visual Studio for Mac 將 Xamarin.Android 應用程式部署至該實體裝置。

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)

開發人員所使用的其中一個非常常見的應用程式偵錯技巧就是使用 `Console.WriteLine`。 不過，在 Android 這類行動平台上並沒有主控台。 Android 裝置會提供一個記錄檔，您在撰寫應用程式時可能會需要用到此記錄檔。 由於擷取此記錄檔時所輸入命令的緣故，因此這個記錄檔有時稱為 **logcat**。 本文說明如何使用 **logcat**。

> [!WARNING]
> 請注意，**Xamarin Android Player** 已被取代。 如需詳細資訊，請參閱[這篇部落格文章中的公告](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/) \(英文\)。
