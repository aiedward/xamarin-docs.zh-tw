---
title: Android Emulator 安裝
description: Android Emulator 模擬器可在各種不同組態中執行，以模擬不同裝置。 本指南說明如何準備 Android Emulator 來測試您的應用程式。
ms.prod: xamarin
ms.assetid: 889963B7-F4DA-41D9-9B8D-B733BB71A329
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/31/2021
ms.openlocfilehash: 2a713b253489771075590b3686e21e9e25340002
ms.sourcegitcommit: 566a8839f3d3beafd7f8639fca471b425ca14997
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564345"
---
# <a name="android-emulator-setup"></a>Android Emulator 安裝

本指南說明如何準備 Android Emulator 來測試您的應用程式。

## <a name="overview"></a>概觀

Android Emulator 模擬器可在各種不同組態中執行，以模擬不同裝置。 每個組態都稱為 _虛擬裝置_。 當您在模擬器上部署和測試您的應用程式時，您要選取模擬如 Nexus 或像素電話等實體 Android 裝置的預先設定或自訂虛擬裝置。

下列章節說明如何加速 Android 模擬器以達到最大效能，如何使用 Android Device Manager 建立及自訂虛擬裝置，以及如何自訂虛擬裝置的設定檔內容。 此外，疑難排解一節會說明常見的模擬器問題和因應措施。

## <a name="sections"></a>區段

### <a name="hardware-acceleration-for-emulator-performance"></a>[硬體加速以提升模擬器效能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)

如何使用 HYPER-V 或 HAXM 虛擬化技術來準備電腦，以達到最大的 Android Emulator 效能。 由於 Android Emulator 在沒有硬體加速的情況下可能非常緩慢，因此建議您先在電腦上啟用硬體加速，再使用此模擬器。

### <a name="managing-virtual-devices-with-the-android-device-manager"></a>[使用 Android Device Manager 管理虛擬裝置](~/android/get-started/installation/android-emulator/device-manager.md)

如何使用 Android Device Manager 建立與自訂虛擬裝置。

### <a name="editing-android-virtual-device-properties"></a>[編輯 Android 虛擬裝置屬性](~/android/get-started/installation/android-emulator/device-properties.md)

如何使用 Android Device Manager 編輯虛擬裝置的設定檔內容。

### <a name="android-emulator-troubleshooting"></a>[Android Emulator 疑難排解](~/android/get-started/installation/android-emulator/troubleshooting.md)

本文說明執行 Android Emulator 時最常發生的警告訊息和問題，以及因應措施和秘訣。

> [!NOTE]
> 如果您使用具有 Apple 晶片（例如 M1）的 Mac，您將需要 [從 GitHub 安裝適用于 m1 preview 的 Android Emulator](https://github.com/google/android-emulator-m1-preview/releases)。

在您設定好 Android Emulator 之後，請參閱 [Debugging on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上偵錯)，以了解如何啟動模擬器，並用其測試和偵錯應用程式的相關資訊。

> [!NOTE]
> 自 Android SDK Tools **26.0.1** 版起，Google 移除了對現有 AVD/SDK 管理員的支援，改而提供其新推出的 CLI (命令列介面) 工具。 由於此項取代變更；因此，現在會針對 Android Tools 26.0.1 和更新版本改用 Xamarin SDK/Device Manager，而非 Google SDK/Device Manager。 如需 Xamarin SDK Manager 的詳細資訊，請參閱[設定 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md)。
