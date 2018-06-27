---
title: Google Android 模擬器設定
description: Google Android 模擬器可在各種不同組態中執行，以模擬不同裝置。 本指南說明如何準備 Android 模擬器來測試您的應用程式。
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: e5ba2cc23ea9751ca60644d3eb5b7e3f31bbb6bb
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34732527"
---
# <a name="google-android-emulator-setup"></a>Google Android 模擬器設定

_本指南說明如何準備 Google Android 模擬器來測試您的應用程式。_


## <a name="overview"></a>總覽

Google Android 模擬器可在各種不同組態中執行，以模擬不同裝置。 每個組態都稱為_虛擬裝置_。 當您在模擬器上部署和測試您的應用程式時，您要選取模擬如 Nexus 或像素電話等實體 Android 裝置的預先設定或自訂虛擬裝置。

下列章節會描述如何加速 Google Android 模擬器以達到最大效能、如何使用 Android Device Manager 建立及自訂虛擬裝置，以及如何自訂虛擬裝置的設定檔內容。 此外，疑難排解一節會說明常見的設定問題和因應措施。

## <a name="sections"></a>章節

### <a name="hardware-acceleration-for-emulator-performanceandroidget-startedinstallationandroid-emulatorhardware-accelerationmd"></a>[硬體加速以提升模擬器效能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

如何準備您的電腦以達到最大的 Android 模擬器效能。
由於 Google Android 模擬器在沒有硬體加速的情況下可能非常緩慢；因此，建議您先在電腦上啟用硬體加速，再使用此模擬器。

### <a name="managing-virtual-devices-with-the-android-device-managerandroidget-startedinstallationandroid-emulatordevice-managermd"></a>[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)

如何使用 Android Device Manager 建立與自訂虛擬裝置。

### <a name="editing-android-virtual-device-propertiesandroidget-startedinstallationandroid-emulatordevice-propertiesmd"></a>[編輯 Android 虛擬裝置屬性](~/android/get-started/installation/android-emulator/device-properties.md)

如何使用 Android Device Manager 編輯 Android 虛擬裝置的設定檔內容。

### <a name="troubleshooting-emulator-setup-problemsandroidget-startedinstallationandroid-emulatortroubleshootingmd"></a>[針對模擬器設定問題進行疑難排解](~/android/get-started/installation/android-emulator/troubleshooting.md)

如何在設定 Android 模擬器時，診斷並更正可能會發生的 Android Device Manager 問題。


當您設定 Android 模擬器之後，請參閱[使用 Google Android 模擬器偵錯](~/android/deploy-test/debugging/android-sdk-emulator/index.md)，以了解如何啟動模擬器，並使用它來測試和偵錯應用程式的相關資訊。


> [!NOTE]
> 自 Android SDK Tools **26.0.1** 版起，Google 移除了對現有 AVD/SDK 管理員的支援，改而提供其新推出的 CLI (命令列介面) 工具。 由於此項取代變更；因此，現在會針對 Android Tools 26.0.1 和更新版本改用 Xamarin SDK/Device Manager，而非 Google SDK/Device Manager。 如需 Xamarin SDK Manager 的詳細資訊，請參閱 [Android SDK 設定](~/android/get-started/installation/android-sdk.md))。

