---
title: 偵測裝置和模擬器上的 Xamarin.Android
description: 如何針對您的 Xamarin.Android 應用程式進行測試和偵錯
ms.prod: xamarin
ms.assetid: A355A471-8195-4391-93FE-0000BCB17923
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: ab070d1f5f2400ee5daa12013953cf8c6beff082
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279771"
---
# <a name="debugging"></a>偵錯

本節討論如何偵錯裝置或模擬器上的 Xamarin.Android 應用程式。

## <a name="debugging-overview"></a>偵錯概觀

開發 Android 應用程式需要在實體硬體上或使用模擬器來執行應用程式。 使用硬體是最佳方式，但並不一定是最務實的做法。 在許多情況下，使用以下所述的其中一種模擬器來模擬 Android 硬體，會是較簡單且更符合成本效益的方式。

### <a name="debugging-on-the-android-emulatorandroiddeploy-testdebuggingdebug-on-emulatormd"></a>[Run Apps on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上執行應用程式)

本文說明如何從 Visual Studio 啟動 Android Emulator，並在虛擬裝置中啟動您的應用程式。

### <a name="debugging-on-a-deviceandroiddeploy-testdebuggingdebug-on-devicemd"></a>[在裝置上進行偵錯](~/android/deploy-test/debugging/debug-on-device.md)

這篇文章說明如何設定實體 Android 裝置，以便直接從 Visual Studio 或 Visual Studio for Mac 將 Xamarin.Android 應用程式部署至該實體裝置。

### <a name="android-debug-logandroiddeploy-testdebuggingandroid-debug-logmd"></a>[Android 偵錯記錄檔](~/android/deploy-test/debugging/android-debug-log.md)

開發人員所使用的其中一個非常常見的應用程式偵錯技巧就是使用 `Console.WriteLine`。 不過，在 Android 這類行動平台上並沒有主控台。 Android 裝置會提供一個記錄檔，您在撰寫應用程式時可能會需要用到此記錄檔。 由於擷取此記錄檔時所輸入命令的緣故，因此這個記錄檔有時稱為 **logcat**。 本文說明如何使用 **logcat**。

> [!WARNING]
> 請注意，**Xamarin Android Player** 已被取代。 如需詳細資訊，請參閱[這篇部落格文章中的公告](https://blog.xamarin.com/live-from-dotnetconf-cycle-7-xamarin-studio-6-and-more/) \(英文\)。 此外，**Visual Studio Android Emulator** 已取代為 Visual Studio 2017 Android Emulator。
