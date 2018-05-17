---
title: Android Emulator 安裝
description: 本節描述如何準備 Google Android 模擬器來測試您的應用程式。 其中說明如何加速模擬器以達到最大效能，並示範如何使用模擬器管理員來建立和自訂虛擬裝置。
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/25/2018
ms.openlocfilehash: 215e298068b7a3a23b2e469e923f172c8303bbcb
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
---
# <a name="android-emulator-setup"></a>Android Emulator 安裝

_本節描述如何準備 Google Android 模擬器來測試您的應用程式。_ 其中說明如何加速模擬器以達到最大效能，並示範如何使用模擬器管理員來建立和自訂虛擬裝置。


## <a name="overview"></a>總覽

Google Android SDK 模擬器可在各種不同組態中執行，以模擬不同裝置。 這其中每一種組態都會建立為「虛擬裝置」。 在本指南中，您將了解如何加速 Android Emulator 以提升效能，並使用 Xamarin Android Emulator 管理員或舊版 Google 模擬器管理員來建立虛擬裝置。


> [!NOTE]
> 自 Android SDK Tools **26.0.1** 版起，Google 移除了對現有 AVD/SDK 管理員的支援，改而提供其新推出的 CLI (命令列介面) 工具。 由於此項取代變更，因此，現在會針對 Android 工具 26.0.1 和更新版本改用 Xamarin SDK/裝置管理員，而非 Google SDK/模擬器管理  (如需 Xamarin SDK Manager 的詳細資訊，請參閱 [Android SDK 安裝](~/android/get-started/installation/android-sdk.md))。


## <a name="sections"></a>章節

### <a name="hardware-accelerationandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[硬體加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

如何準備您的電腦以達到最大的 Google Android 模擬器效能。 由於 Google Android 模擬器在沒有硬體加速的情況下可能非常緩慢；因此，建議您先在電腦上啟用硬體加速，然後再使用 Google Android 模擬器。

### <a name="xamarin-android-device-managerandroidget-startedinstallationandroid-emulatorxamarin-device-managermd"></a>[Xamarin Android 裝置管理員](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)

如何使用 Xamarin Android 裝置管理員，來建立和自訂 Google Android 模擬器虛擬裝置。 **Xamarin Android 裝置管理員** (目前處於預覽狀態) 旨在取代舊版 Google 模擬器管理員。 如果您的目標是 Android Oreo 8.0 或更新版本，就必須使用 Xamarin Android 裝置管理員，而非 Google 模擬器管理員。

### <a name="google-emulator-managerandroidget-startedinstallationandroid-emulatorgoogle-emulator-managermd"></a>[Google 模擬器管理員](~/android/get-started/installation/android-emulator/google-emulator-manager.md)

如何使用舊版 Google 模擬器管理員，來建立和自訂 Google Android 模擬器虛擬裝置。 只要保持在 Android SDK 工具 25.2.5 版或更低版本，您就能使用原本的 Google 模擬器管理員繼續執行 Google Android Emulator。

當您設定 Android SDK 模擬器之後，請參閱 [Google Android 模擬器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)，以了解如何啟動模擬器，並使用它來測試和偵錯應用程式的相關資訊。
