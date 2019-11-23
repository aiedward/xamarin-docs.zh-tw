---
title: Android 10 功能
description: 如何開始使用 Xamarin 開發適用于 Android 10 的應用程式。
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: c19c9e5bd279824ea2d3e4e9f88857388f786a2c
ms.sourcegitcommit: b11dc46a9ba23483195e923de88cbef173730087
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612274"
---
# <a name="android-10-with-xamarin"></a>使用 Xamarin 的 Android 10

_如何開始使用 Xamarin 開發適用于 Android 10 的應用程式。_

Android 10 現在可從 Google 取得。 此版本提供一些新功能和 Api，而且其中有許多都需要在最新的 Android 裝置上利用新的硬體功能。

![Android 10 標誌](~/android/platform/android-10-images/android10_black.png)

本文的結構是協助您開始開發適用于 Android 10 的 Xamarin Android 應用程式。 其中說明如何安裝必要的更新、設定 SDK，以及準備模擬器或裝置進行測試。 它也提供 Android 10 的新功能概述，並提供範例原始程式碼，說明如何使用一些重要的 Android 10 功能。

Xamarin. Android 10.0 提供 Android 10 的支援。 如需 Android 10 的 Xamarin Android 支援的詳細資訊，請參閱《 [android 10.0 版本資訊》](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0)。

## <a name="requirements"></a>需求

下列清單是在以 Xamarin 為基礎的應用程式中使用 Android 10 功能的必要條件：

- 建議使用**Visual Studio** Visual Studio 2019。 在 [Windows update] 上，Visual Studio 2019 16.3 版或更新版本。 在 macOS 上，更新為 Visual Studio 2019 （適用于 Mac 8.3 版或更新版本）。
- **Xamarin** . android 10.0 或更新版本必須與 Visual Studio 一起安裝（Xamarin. android 會在 Windows 上自動安裝為**使用 .net**的行動裝置開發工作負載，並安裝為**Visual Studio for Mac 安裝程式**的一部分）
- **JAVA 開發人員套件**-Xamarin. Android 10.0 開發需要 JDK 8。 Microsoft 的 OpenJDK 散發會自動安裝為 Visual Studio 的一部分。
- **Android SDK** Android SDK API 29 必須透過 Android SDK 管理員進行安裝。

## <a name="get-started"></a>開始使用

若要開始使用 Xamarin 開發 Android 10 應用程式，您必須先下載並安裝最新的工具和 SDK 套件，才能建立您的第一個 Android 10 專案：

1. **建議使用 Visual Studio 2019**。 更新為 Visual Studio 2019 16.3 或更新版本。 如果您使用 Visual Studio for Mac 2019，請更新為 Visual Studio 2019 （適用于 Mac 8.3 或更新版本）。
2. 透過 SDK 管理員安裝**Android 10 （API 29）** 套件和工具。
    - Android 10 （API 29） SDK 平臺
    - Android 10 （API 29）系統映射
    - Android SDK 組建工具 29.0.0 +
    - Android SDK 平臺工具 29.0.0 +
    - Android Emulator 29.0.0 +
3. 建立以 Android 10.0 為目標的新 Xamarin Android 專案。
4. 設定用於測試 Android 10 應用程式的模擬器或裝置。

以下說明每個步驟：

### <a name="update-visual-studio"></a>更新 Visual Studio 2017

建議您使用 Visual Studio 2019 來建立使用 Xamarin 的 Android 10 應用程式。

如果您使用 Visual Studio 2019，請更新為 Visual Studio 2019 16.3 或更新版本（如需指示，請參閱[將 Visual Studio 2019 更新為最新版本](https://docs.microsoft.com/visualstudio/install/update-visual-studio)）。 在 macOS 上，更新為 Mac 8.3 或更新版本 Visual Studio 2019 （如需指示，請參閱將[Visual Studio 2019 For Mac 更新為最新版本](https://docs.microsoft.com/visualstudio/mac/update)）。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

若要使用 Xamarin 10.0 建立專案，您必須先使用 Android SDK 管理員來安裝適用于 Android 10 的 SDK 平臺 **（API 層級29）** 。

1. 啟動 SDK 管理員。 在 Visual Studio 中，按一下 **工具 > Android > Android SDK 管理員。** 在 Visual Studio for Mac 中，按一下 [**工具] > [SDK 管理員]。**
2. 在右下角，按一下齒輪圖示，然後選取 [存放庫] **> Google （不支援）：**

    ![Android SDK Manager 存放庫選取範圍](~/android/platform/android-10-images/sdkrepository.png)

3. 安裝**Android 10 SDK 平臺**套件（在 [**平臺**] 索引標籤中列為**Android SDK Platform 29** ）（如需有關使用 SDK 管理員的詳細資訊，請參閱[Android SDK 設定](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)）：

    ![Android SDK Manager 平臺 索引標籤](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>建立 Xamarin Android 專案

建立新的 Xamarin Android 專案。 如果您不熟悉使用 Xamarin 進行 Android 開發的新手，請參閱[Hello，Android](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index)以瞭解如何建立 Xamarin. android 專案。

當您建立 Android 專案時，您必須將版本設定設定為以 Android 10.0 或更新版本為目標。 例如，若要以 Android 10 的專案為目標，您必須將專案的目標 Android API 層級設定為**android 10.0 （API 29）** 。 這包括您的**目標 Framework 版本**和**目標 ANDROID SDK 版本**到 API 29 或更新版本。 如需設定 Android API 層級的詳細資訊，請參閱[瞭解 ANDROID Api 層級。](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)

![Xamarin. Android 目標架構](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>設定裝置或模擬器

如果您使用的是實體裝置（例如圖元），您可以前往電話設定中的 `System` > `System update` > `Check for update` 下載 Android 10 更新。 如果您想要將裝置閃爍，請參閱將[原廠映射](https://developers.google.com/android/images)或[OTA 影像](https://developers.google.com/android/ota)閃爍至您的裝置的指示。

如果您使用模擬器，請建立 API 層級29的虛擬裝置，並選取 x86 型映射。 如需使用 Android Device Manager 來建立和管理虛擬裝置的詳細資訊，請參閱[使用 Android Device Manager 管理虛擬裝置。](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager) 如需使用 Android Emulator 進行測試和偵測的詳細資訊，請參閱[Android Emulator 上的偵錯工具。](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>新功能

Android 10 引進了各種新功能。 其中一些新功能的目的是要利用最新的 Android 裝置所提供的新硬體功能，而有些則是設計來進一步增強 Android 使用者體驗：

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>使用 Android 10 功能和 Api 增強您的應用程式

接下來，當您準備好時，請深入探索 Android 10，並瞭解您可以使用的 [新功能和 api](https://developer.android.com/preview/api-overview.html) 。 以下是一些要開始使用的熱門功能。

以下是每個應用程式的建議功能：

- **深色主題：**  透過新增 [深色主題](https://developer.android.com/preview/features/darktheme) 或啟用 [強制深色](https://developer.android.com/preview/features/darktheme#force_dark)，為啟用全系統暗色調主題的使用者確保一致的體驗。

![深色佈景主題](~/android/platform/android-10-images/darktheme.png)

- **在 [您的應用程式中支援 ](https://developer.android.com/preview/features/gesturalnav)gestural 導覽** ，方法是繼續邊緣對邊緣，並確定您的自訂手勢是系統導覽手勢的互補功能。

![手勢導覽](~/android/platform/android-10-images/gesturenavigation.png)

- **針對 Foldables 優化：**  藉由 [優化 foldables](https://developer.android.com/preview/features/foldables)，在現今的創新裝置上提供順暢的邊緣對邊緣體驗。

![式](~/android/platform/android-10-images/foldable.png)

如果您的應用程式相關，建議使用下列功能：

- **更多互動式通知：**  如果您的通知包含訊息，請 [在通知中啟用建議的回復和動作](https://developer.android.com/preview/features#smart-suggestions) 以與使用者互動，並讓他們立即採取行動。
- **更好的生物識別：**  如果您使用生物識別驗證，請移至 [BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt)，這是在新式裝置上支援指紋驗證的慣用方式。
- 擴充**錄製：**  以支援字幕或遊戲錄製，請啟用 [音訊播放捕捉](https://developer.android.com/preview/features/playback-capture)。 這是觸及更多使用者並讓您的應用程式更容易存取的絕佳方式。
- **更好的編解碼器：** 媒體應用程式 ，請嘗試 [AV1](https://en.wikipedia.org/wiki/AV1) 以取得影片串流和 [HDR10 +](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) ，以取得高動態範圍的影片。 針對語音和音樂串流，您可以使用 [Opus](http://opus-codec.org/)編碼，而針對 musicians，則會提供 [原生的 MIDI API](https://developer.android.com/preview/features/midi) 。
- **更好的網路 api：**  如果您的應用程式透過 Wi-fi 管理 IoT 裝置，請嘗試新的 [網路連線 api](https://developer.android.com/preview/features#peer2peer) 以進行設定、下載或列印等功能。

這些只是 Android 10 中許多新功能和 Api 的其中幾個。 若要查看所有專案，請造訪 [適用于開發人員的 Android 10 網站](https://developer.android.com/about/versions/10/highlights)。

## <a name="behavior-changes"></a>行為變更

當目標 Android 版本設定為 API 層級29時，有數個平臺變更 cann 會影響應用程式的行為，即使您未執行上述的新功能也一樣。 下列清單是這些變更的簡短摘要：

- [為確保應用程式穩定性和相容性，android 平臺現在會限制您的應用程式可在 Android 10 中使用的非 SDK 介面](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions)。
- [共用記憶體已變更](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory)。
- [Android 執行時間 & AOT 正確性](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat)。
- [全螢幕意圖的許可權必須要求 `USE_FULL_SCREEN_INTENT`](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents)。
- [支援 foldables](https://developer.android.com/about/versions/10/behavior-changes-10#foldables)。

## <a name="summary"></a>摘要

本文介紹了 Android 10，並說明如何安裝和設定最新的 Xamarin android 開發工具和套件。 其中提供 Android 10 中可用的主要功能總覽。 其中包含 API 檔和 Android 開發人員主題的連結，可協助您開始建立適用于 Android 10 的應用程式。 它也會反白顯示可能影響現有應用程式的最重要 Android 10 行為變更。

## <a name="related-links"></a>相關連結

- [Android 10](https://developer.android.com/about/versions/10)
