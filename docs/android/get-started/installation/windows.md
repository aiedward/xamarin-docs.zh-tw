---
title: Windows 安裝
description: 本指南說明在 Windows 上安裝 Xamarin.Android for Visual Studio 的步驟，並說明如何設定 Xamarin.Android 以建置您的第一個 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 0caea0c31275a7459286c143cc92ca90731dd93b
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304111"
---
# <a name="windows-installation"></a>Windows 安裝

_本指南說明在 Windows 上安裝 Xamarin.Android for Visual Studio 的步驟，並說明如何設定 Xamarin.Android 以建置您的第一個 Xamarin.Android 應用程式。_

## <a name="overview"></a>概觀

因為所有 Visual Studio 版本現在都免費隨附 Xamarin，不需要另外授權，您可以使用 Visual Studio 安裝程式來下載和安裝 Xamarin.Android 工具。
（不再需要舊版 Xamarin 所需的手動安裝和授權步驟。）在本指南中，您將瞭解下列各項：

- 如何設定 Java Development Kit、Android SDK 以及 Android NDK 的自訂位置。

- 如何啟動 Android SDK 管理員來下載並安裝其他 Android SDK 元件。

- 如何準備用於偵錯和測試的 Android 裝置或模擬器。

- 如何建立第一個 Xamarin.Android 應用程式專案。

本指南結束時，您將擁有可運行的 Xamarin.Android 安裝並整合至 Visual Studio，而且您將可以開始建置第一個 Xamarin.Android 應用程式。

## <a name="installation"></a>安裝

如需在 Windows 上安裝 Xamarin 以便搭配 Visual Studio 使用的詳細資訊，請參閱 [Windows 安裝](~/get-started/installation/windows.md)指南。

## <a name="configuration"></a>組態

Xamarin.Android 使用 Java Development Kit (JDK) 和 Android SDK 來建置應用程式。 在安裝期間，Visual Studio 安裝程式會將這些工具放在它們的預設位置中，並以適當的路徑組態來設定開發環境。 您可以檢視和變更這些位置，方法是依序按一下 [工具] > [選項] > [Xamarin] > [Android 設定]：

![[Xamarin Android 設定] 對話方塊的螢幕擷取畫面](windows-images/07-settings.png)

對大多數使用者來說，這些預設位置不需進一步變更即可運作。 不過，您可能想要使用這些工具的自訂位置來設定 Visual Studio (例如，如果您已在不同的位置安裝 Java JDK、 Android SDK 或 NDK)。 按一下您想要變更之路徑旁的 [變更]，然後巡覽至新位置。

Xamarin.Android 會使用 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) \(英文\)，如果您正在針對 API 層級 24 或更高層級進行開發，則需要此項 (JDK 8 也支援早於 24 的 API 層級)。 如果您是針對 API 層級 23 或更早的層級進行開發，則可繼續使用 [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

### <a name="android-sdk-manager"></a>Android SDK Manager

Android 使用多個 Android API 層級設定來判斷您的應用程式在各種版本 Android 之間的相容性 (如需 Android API 層級的詳細資訊，請參閱[解 Android API 層級](~/android/app-fundamentals/android-api-levels.md))。
根據您的目標是哪些 Android API 層級，您可能需要下載並安裝其他 Android SDK 元件。 此外，您可能需要安裝 Android SDK 中提供的選擇性工具和模擬器映像。 若要這樣做，請使用 Android SDK 管理員。 您可以按一下 [工具] > [Android] > [Android SDK 管理員] 來啟動 **Android SDK 管理員**：

[![如何啟動 Android SDK 管理員](windows-images/08-sdk-manager-sml.png)](windows-images/08-sdk-manager.png#lightbox)

Visual Studio 預設會安裝 Google Android SDK 管理員：

![Google Android SDK 管理員的螢幕擷取畫面範例](windows-images/09-google-sdk-manager.png)

您可以使用 Google Android SDK 管理員來安裝最高達 25.2.3 版的 Android SDK 工具套件版本。 不過，如果您需要使用較新版的 Android SDK Tools 套件，必須安裝適用於 Visual Studio 的 Xamarin Android SDK 管理員外掛程式 (可從 Visual Studio Marketplace 取得)。 這是必要的，因為 Google 的獨立 SDK 管理員已在 Android SDK Tools 套件的 25.2.3 版中被取代。 

如需使用 Xamarin Android SDK 管理員的詳細資訊，請參閱 [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)。

### <a name="android-emulator"></a>Android Emulator

[Android Emulator](https://developer.android.com/studio/run/emulator) 是開發和測試 Xamarin.Android 應用程式的實用工具。 例如，平板電腦等實體裝置在開發期間可能無法立即使用，或開發人員可能想要先在電腦上執行某些整合測試，再認可程式碼。

在電腦上模擬 Android 裝置包含下列元件：

- **Google Android Emulator** &ndash; 這是以[qemu-img convert](https://www.qemu.org/)為基礎的模擬器，它會建立在開發人員工作站上執行的虛擬化裝置。
- _模擬器_**映射 &ndash; 模擬器映射是**一種範本，或是要虛擬化之硬體和作業系統的規格。 例如，一個模擬器映像會識別在已安裝 Google Play 服務之 Android 7.0 上執行的 Nexus 5X 硬體需求。 另一個模擬器映像可能專門針對執行 Android 6.0 的特定 10 吋平板電腦。
- Android**虛擬裝置（AVD）** &ndash; _android 虛擬裝置_是從模擬器映射建立的模擬 Android 裝置。 執行和測試 Android 應用程式時，Xamarin.Android 會啟動 Android 模擬器 (啟動特定的 AVD)、安裝 APK，然後執行應用程式。

使用針對 x86 架構最佳化的特殊模擬器映像以及兩種虛擬化技術其中之一，在 x86 電腦上執行開發工作時可顯著改善效能：

1. Microsoft 的 Hyper-V &ndash; 適用於執行 Windows 10 2018 年 4 月更新或更新版本的電腦。
2. Intel 的 Hardware Accelerated Execution Manager (HAXM) &ndash; 適用於執行 OS X、macOS 或舊版 Windows 的 x86 電腦。

如需 Android Emulator、Hyper-V 和 HAXM 的詳細資訊，請參閱[硬體加速以提升模擬器效能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)指南。

> [!NOTE]
> 在 Windows 10 2018 年 4 月更新以前的 Windows 版本上，HAXM 與 Hyper-V 不相容。 在本例中，需要[停用 Hyper-V](~/android/get-started/installation/android-emulator/troubleshooting.md#disable-hyperv) 或使用沒有最佳化 x86 的較慢模擬器映像。

<a name="device" />

### <a name="android-device"></a>Android 裝置

如果您有實體 Android 裝置要用於測試，這是設定它以供開發使用的好時機。 請參閱[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)來設定 Android 裝置進行開發，然後將它連線至您的電腦以便執行及偵錯 Xamarin.Android 應用程式。

## <a name="create-an-application"></a>建立應用程式

既然您已安裝 Xamarin.Android，您可以啟動 Visual Studio 建立新的專案。 按一下 [檔案] > [新增] > [專案]，開始建立您的應用程式：

![如何建立新的專案](windows-images/10-new-project.png)

在 [新增專案] 對話方塊中，選取 [範本] 下的 [Android]，然後在右窗格中按一下 [Android 應用程式]。 輸入您的應用程式名稱 (在以下的螢幕擷取畫面中，應用程式稱為 **MyApp**)，然後按一下 [確定]：

[![[新增專案] 對話方塊的螢幕擷取畫面，建立空白的 Android 應用程式](windows-images/11-first-app-sml.w157.png)](windows-images/11-first-app.w157.png#lightbox)

就這麼簡單！ 現在您已經準備好使用 Xamarin.Android 來建立 Android 應用程式！

## <a name="summary"></a>摘要

在本文中，您學到如何在 Windows 上設定和安裝 Xamarin.Android 平台、如何 (選擇性地) 使用自訂 Java JDK 和 Android SDK 安裝位置來設定 Visual Studio、如何啟動 SDK 管理員來安裝其他 Android SDK 元件、如何設定 Android 裝置或模擬器，以及如何開始建置您的第一個應用程式。

下一個步驟是查看 [Hello, Android](~/android/get-started/hello-android/index.md) 教學課程，解如何建立可運作的 Xamarin.Android 應用程式。

## <a name="related-links"></a>相關連結

- [下載 Visual Studio](https://visualstudio.microsoft.com/vs/)
- [安裝 Visual Studio Tools for Xamarin](~/get-started/installation/windows.md)
- [系統需求](~/cross-platform/get-started/requirements.md)
- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [Android Emulator 安裝](~/android/get-started/installation/android-emulator/index.md)
- [設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)
- [Run Apps on the Android Emulator](https://developer.android.com/studio/run/emulator#Requirements) (在 Android 模擬器上執行應用程式)
