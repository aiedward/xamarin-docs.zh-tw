---
title: Windows 安裝
description: 本指南說明在 Windows 上安裝 Xamarin.Android for Visual Studio 的步驟，並說明如何設定 Xamarin.Android 以建置您的第一個 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 1cd9a4977aad3f3bd8d8a4e51871698a54f75eb8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="windows-installation"></a>Windows 安裝

_本指南說明在 Windows 上安裝 Xamarin.Android for Visual Studio 的步驟，並說明如何設定 Xamarin.Android 以建置您的第一個 Xamarin.Android 應用程式。_


## <a name="overview"></a>總覽

因為所有 Visual Studio 版本現在都免費隨附 Xamarin，不需要另外授權，您可以使用 Visual Studio 安裝程式來下載和安裝 Xamarin.Android 工具。
(較早版本的 Xamarin.Android 所需的手動安裝與授權步驟已不再需要。)本指南中，您將學會下列作業：

-   如何設定 Java Development Kit、Android SDK 以及 Android NDK 的自訂位置。

-   如何啟動 Android SDK 管理員來下載並安裝其他 Android SDK 元件。

-   如何準備偵錯和測試用的 Android 裝置或模擬器。

-   如何建立第一個 Xamarin.Android 應用程式專案。

本指南結束時，您將擁有可運行的 Xamarin.Android 安裝並整合至 Visual Studio，而且您將可以開始建置第一個 Xamarin.Android 應用程式。

## <a name="installation"></a>安裝

如需在 Windows 上安裝 Xamarin 以便搭配 Visual Studio 使用的詳細資訊，請參閱 [Windows 安裝](~/cross-platform/get-started/installation/windows.md)指南。


## <a name="configuration"></a>組態

Xamarin.Android 使用 Java Development Kit (JDK) 和 Android SDK 來建置應用程式。 在安裝期間，Visual Studio 安裝程式會將這些工具放在它們的預設位置中，並以適當的路徑組態來設定開發環境。 您可以檢視和變更這些位置，方法是依序按一下 [工具] > [選項] > [Xamarin] > [Android 設定]：

![[Xamarin Android 設定] 對話方塊的螢幕擷取畫面](windows-images/07-settings.png)

對大多數使用者來說，這些預設位置不需進一步變更即可運作。 不過，您可能想要使用這些工具的自訂位置來設定 Visual Studio (例如，如果您已在不同的位置安裝 Java JDK、 Android SDK 或 NDK)。 按一下您想要變更之路徑旁的 [變更]，然後巡覽至新位置。

Xamarin.Android 會使用 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，如果您正在針對 API 層級 24 或更高層級進行開發，則需要此項 (JDK 8 也支援早於 24 的 API 層級)。 如果您是針對 API 層級 23 或更早的層級進行開發，則可繼續使用 [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

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


### <a name="android-emulator"></a>Android 模擬器

如果您沒有實體 Android 裝置要用於測試，可以使用 Android 模擬器來測試應用程式。 如需 Google Android 模擬器的詳細資訊，請參閱 [Android SDK Emulator](~/android/deploy-test/debugging/android-sdk-emulator/index.md)。

Google Android 模擬器使用 Intel HAXM (Hardware Accelerated Execution Manager)，它可能會與其他模擬器所使用的虛擬化技術相衝突。 三種主要的虛擬化技術如下：

-   **Hyper-V** (Visual Studio Emulator for Android 及 Windows Phone 模擬器所使用) 

-   **Virtual Box** (Genymotion 所使用)

-   **Intel HAXM** (Google Android SDK 模擬器所使用) 

由於開發電腦的 CPU 一次只支援一種虛擬化技術，因此最好在開發電腦上只使用一種技術。

<a name="device" />

### <a name="android-device"></a>Android 裝置

如果您有實體 Android 裝置要用於測試，這是設定它以供開發使用的好時機。 請參閱[設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)來設定 Android 裝置進行開發，然後將它連線至您的電腦以便執行及偵錯 Xamarin.Android 應用程式。


## <a name="create-an-application"></a>建立應用程式

既然您已安裝 Xamarin.Android，您可以啟動 Visual Studio 建立新的專案。 按一下 [檔案] > [新增] > [專案]，開始建立您的應用程式：

![如何建立新的專案](windows-images/10-new-project.png)

在 [新增專案] 對話方塊中，選取 [範本] 下的 [Android]，然後在右窗格中按一下 [空白的應用程式 (Android)]。 輸入您的應用程式名稱 (在以下的螢幕擷取畫面中，應用程式稱為 **MyApp**)，然後按一下 [確定]：

[![[新增專案] 對話方塊的螢幕擷取畫面，建立空白的 Android 應用程式](windows-images/11-first-app-sml.png)](windows-images/11-first-app.png#lightbox)

就這麼容易！ 現在您已經準備好使用 Xamarin.Android 來建立 Android 應用程式！


## <a name="summary"></a>總結

在本文中，您學到如何在 Windows 上設定和安裝 Xamarin.Android 平台、如何 (選擇性地) 使用自訂 Java JDK 和 Android SDK 安裝位置來設定 Visual Studio、如何啟動 SDK 管理員來安裝其他 Android SDK 元件、如何設定 Android 裝置或模擬器，以及如何開始建置您的第一個應用程式。

下一個步驟是查看 [Hello, Android](~/android/get-started/hello-android/index.md) 教學課程，解如何建立可運作的 Xamarin.Android 應用程式。


## <a name="related-links"></a>相關連結

- [下載 Visual Studio](https://www.visualstudio.com/vs/)
- [安裝 Visual Studio Tools for Xamarin](~/cross-platform/get-started/installation/windows.md)
- [系統需求](~/cross-platform/get-started/requirements.md)
- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [Android SDK 模擬器](~/android/get-started/installation/android-emulator/index.md) \(英文\)
- [設定裝置以進行開發](~/android/get-started/installation/set-up-device-for-development.md)
