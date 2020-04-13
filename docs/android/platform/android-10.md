---
title: 安卓 10 功能
description: 如何使用 Xamarin.Android 開始開發 Android 10 的應用程式。
ms.assetid: B3342772-FB88-4B7F-BC15-8BC78EED749E
author: JonDouglas
ms.author: jodou
ms.date: 09/17/2019
ms.openlocfilehash: c19c9e5bd279824ea2d3e4e9f88857388f786a2c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73612274"
---
# <a name="android-10-with-xamarin"></a>安卓 10 與 Xamarin

_如何使用 Xamarin.Android 開始開發 Android 10 的應用程式。_

Android 10 現已從 Google 獲得。 此版本中提供了許多新功能和 API,其中許多功能和 API 都是利用最新 Android 設備中的新硬體功能所必需的。

![安卓 10 徽標](~/android/platform/android-10-images/android10_black.png)

本文的結構是説明您開始開發 Xamarin.Android 應用程式,用於 Android 10。 它說明瞭如何安裝必要的更新、配置 SDK 以及準備模擬器或設備進行測試。 它還概述了 Android 10 中的新功能,並提供了示例原始程式碼,說明如何使用一些關鍵的 Android 10 功能。

Xamarin.Android 10.0 為安卓 10 提供支援。 有關 Xamarin.Android 支援 Android 10 的更多資訊,請參閱[Xamarin.Android 10.0 發行說明](https://docs.microsoft.com/xamarin/android/release-notes/10/10.0)。

## <a name="requirements"></a>需求

在基於 Xamarin 的應用中使用 Android 10 功能需要以下清單:

- **視覺工作室**- 視覺工作室2019是推薦。 在 Windows 上更新到 Visual Studio 2019 版本 16.3 或更高版本。 在 macOS 上,更新至 Visual Studio 2019 的 Mac 版本 8.3 或更高版本。
- **Xamarin.Android** - Xamarin.Android 10.0 或更高版本必須安裝到 Visual Studio(Xamarin.Android 自動安裝為**Windows 上的行動開發.NET**工作負載的一部分,並作為**Mac 安裝程式的視覺化工作室**的一部分安裝)
- **Java 開發人員工具組**- Xamarin.Android 10.0 開發需要 JDK 8。 微軟的 OpenJDK 的分發會自動作為可視化工作室的一部分安裝。
- **Android SDK** - Android SDK API 29 必須透過 Android SDK 管理器安裝。

## <a name="get-started"></a>開始使用

要開始使用 Xamarin.Android 開發 Android 10 應用,您必須下載並安裝最新的工具和 SDK 套件,然後才能建立第一個 Android 10 專案:

1. **視覺工作室 2019 是建議**. 更新至 Visual Studio 2019 版本 16.3 或更高版本。 如果您使用的是 Mac 2019 的 Visual Studio,請更新至 Visual Studio 2019 的 Mac 版本 8.3 或更高版本。
2. 通過 SDK 管理器安裝**Android 10 (API 29)** 套件和工具。
    - Android 10 (API 29) SDK 平台
    - 安卓 10 (API 29) 系統映射
    - Android SDK 建譯工具 29.0.0*
    - Android SDK 平臺工具 29.0.0+
    - 安卓模擬器 29.0.0+
3. 創建一個新的 Xamarin.Android 專案,目標是 Android 10.0。
4. 配置用於測試 Android 10 應用的模擬器或設備。

這些步驟如下:

### <a name="update-visual-studio"></a>更新 Visual Studio 2017

Visual Studio 2019 建議使用 Xamarin 構建 Android 10 應用程式。

如果您使用的是 Visual Studio 2019,請更新至 Visual Studio 2019 版本 16.3 或更高版本(有關說明,請參閱[將 Visual Studio 2019 更新到最新版本](https://docs.microsoft.com/visualstudio/install/update-visual-studio))。 在 macOS 上,針對 Mac 8.3 或更高版本更新至 Visual Studio 2019(有關說明,請參閱[將 Mac 的 2019 更新 Visual Studio 2019 到最新版本](https://docs.microsoft.com/visualstudio/mac/update))。

### <a name="install-the-android-sdk"></a>安裝 Android SDK

要使用 Xamarin.Android 10.0 創建項目,必須首先使用 Android SDK 管理器安裝 Android 10 的 SDK 平臺 **(API 級別 29)。**

1. 啟動 SDK 管理員。 在可視化工作室中,按一下 **「工具> Android > Android SDK 管理器。** 在 Mac 可視化工作室中,按一下 **「工具> SDK 管理器。**
2. 在右下角,按下齒輪圖示並選擇**Google >存储库(不支援):**

    ![Android SDK 管理員儲存函式庫選擇](~/android/platform/android-10-images/sdkrepository.png)

3. 安裝**Android 10 SDK 平台**包,這些包在**平台**選項卡中列為 Android SDK**平臺 29(** 有關使用 SDK 管理員的詳細資訊,請參閱[Android SDK 設定](https://docs.microsoft.com/xamarin/android/get-started/installation/android-sdk)):

    ![Android SDK 管理員平台選項卡](~/android/platform/android-10-images/sdkplatforms.png)

### <a name="create-a-xamarinandroid-project"></a>建立 Xamarin.Android 專案

創建新的 Xamarin.安卓專案。 如果您是使用 Xamarin 的 Android 開發的新使用者,請參閱[Hello,Android,](https://docs.microsoft.com/xamarin/android/get-started/hello-android/index)瞭解如何創建 Xamarin.Android 專案。

創建 Android 專案時,必須將版本設置配置為以 Android 10.0 或更高版本為目標。 例如,要針對 Android 10 的項目,必須將專案的目標 Android API 級別配置為**Android 10.0 (API 29)。** 這包括**目標框架版本**和目標 Android **SDK 版本**到 API 29 或更高版本。 有關配置 Android API 級別的詳細資訊,請參閱[瞭解 Android API 級別。](https://docs.microsoft.com/xamarin/android/app-fundamentals/android-api-levels)

![Xamarin.安卓目標框架](~/android/platform/android-10-images/targetframework.png)

### <a name="configure-a-device-or-emulator"></a>設定裝置或模擬器

如果您正在使用物理設備(如圖元),`System` > `System update` > `Check for update`則可以通過轉到手機設置下載 Android 10 更新。 如果您希望快閃記憶體設備,請參閱有關向設備閃爍[出廠映射](https://developers.google.com/android/images)或[OTA 映射](https://developers.google.com/android/ota)的說明。

如果使用模擬器,請為 API 級別 29 創建虛擬裝置,然後選擇基於 x86 的圖像。 有關使用 Android 裝置管理員創建和管理虛擬裝置的資訊,請參閱使用[Android 設備管理員管理虛擬設備。](https://docs.microsoft.com/xamarin/android/get-started/installation/android-emulator/device-manager) 有關使用 Android 模擬器進行測試和調試的資訊,請參閱 Android[模擬器上的調試。](https://docs.microsoft.com/xamarin/android/deploy-test/debugging/debug-on-emulator)

## <a name="new-features"></a>新功能

Android 10 引入了多種新功能。 其中一些新功能旨在利用最新的 Android 裝置提供的新硬體功能,而其他功能旨在進一步增強 Android 用戶體驗:

## <a name="enhance-your-app-with-android-10-features-and-apis"></a>使用 Android 10 功能與 API 增強你的應用程式

接下來,當您準備好時,深入瞭解 Android 10 並瞭解您可以使用的 [新功能和 API。](https://developer.android.com/preview/api-overview.html)   以下是一些需要開始使用的頂級功能。

每個應用都推薦使用這些功能:

- **黑暗主題:** 透過新增 [深色主題](https://developer.android.com/preview/features/darktheme) 或啟用 [「力黑暗」](https://developer.android.com/preview/features/darktheme#force_dark)確保支援系統範圍的黑暗主題的用戶獲得一致的體驗。

![深色佈景主題](~/android/platform/android-10-images/darktheme.png)

- **透過 [gestural navigation](https://developer.android.com/preview/features/gesturalnav)** 邊緣到邊緣並確保自訂手勢與系統導航手勢相輔相成,支援應用中的手勢導航 。

![手勢導航](~/android/platform/android-10-images/gesturenavigation.png)

- **優化可摺疊功能:** 透過 [最佳式可摺疊裝置](https://developer.android.com/preview/features/foldables),在當今的創新裝置上提供無縫、邊緣到邊緣的體驗。

![折疊](~/android/platform/android-10-images/foldable.png)

如果與您的應用相關,建議使用這些功能:

- **更多互動式通知:** 如果通知包含消息,請 在 [通知中啟用建議的答覆和操作](https://developer.android.com/preview/features#smart-suggestions),以吸引使用者,並讓他們立即採取行動。
- **更好的生物識別技術:** 如果您使用生物識別技術,則轉到 [生物識別提示](https://developer.android.com/reference/androidx/biometric/BiometricPrompt),這是在現代設備上支援指紋識別的首選方法。
- **豐富錄製:** 要支援字幕或遊戲錄製,啟用 [音效播放捕捉](https://developer.android.com/preview/features/playback-capture)。 這是聯繫更多使用者並使應用更易於訪問的好方法。
- **更好的編解碼器:** 對於媒體應用,請嘗試 [AV1](https://en.wikipedia.org/wiki/AV1) 視頻流和 [HDR10+](https://en.wikipedia.org/wiki/High-dynamic-range_video#HDR10+) 用於高動態範圍視頻。 對於語音和音樂流,您可以使用 [Opus](http://opus-codec.org/)編碼,對於音樂家,可以使用 [本機 MIDI API。](https://developer.android.com/preview/features/midi)  
- **更好的網路 API:** 如果應用透過 Wi-Fi 管理 IoT 裝置,請嘗試新的 [網路連接 API](https://developer.android.com/preview/features#peer2peer) 以執行配置、下載或列印等功能。

這些只是Android 10中許多新功能和API中的一些。 要查看所有這些,請造 [訪 Android 10 網站為開發人員](https://developer.android.com/about/versions/10/highlights)。

## <a name="behavior-changes"></a>行為變更

當目標 Android 版本設置為 API 級別 29 時,即使您未實現上述新功能,也會影響應用的行為。 以下清單是這些變更的簡要摘要:

- [為了確保應用的穩定性和相容性,Android 平台現在限制你的應用程式在 Android 10 中可以使用的非 SDK 介面](https://developer.android.com/about/versions/10/behavior-changes-10#non-sdk-restrictions)。
- [分享記憶體已變更](https://developer.android.com/about/versions/10/behavior-changes-10#shared-memory)。
- [Android 執行時& AOT 正確性](https://developer.android.com/about/versions/10/behavior-changes-10#system-only-oat)。
- [全螢幕意圖的權限必須`USE_FULL_SCREEN_INTENT`請求](https://developer.android.com/about/versions/10/behavior-changes-10#full-screen-intents)。
- [支援可折疊性](https://developer.android.com/about/versions/10/behavior-changes-10#foldables)。

## <a name="summary"></a>總結

本文介紹了Android 10,並解釋了如何使用Android 10安裝和配置Xamarin.Android開發的最新工具和軟體包。 它提供了 Android 10 中提供的主要功能的概述。 它包括指向 API 文件和 Android 開發人員主題的連結,可説明您開始為 Android 10 創建應用。 它還強調了可能影響現有應用的最重要 Android 10 行為更改。

## <a name="related-links"></a>相關連結

- [Android 10](https://developer.android.com/about/versions/10)
