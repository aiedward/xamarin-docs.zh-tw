---
title: 對於 Android SDK 工具所做的變更
description: Android SDK 會如何管理已安裝的應用程式開發介面層級和 Avd 的變更。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/02/2018
ms.openlocfilehash: b5de9d673a348ddd4b939ae387257f835b37117a
ms.sourcegitcommit: c9ebf456e1c6924956bedb13f4ea78ff09f7b1a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
---
# <a name="changes-to-the-android-sdk-tooling"></a>對於 Android SDK 工具所做的變更

_Android SDK 會如何管理已安裝的應用程式開發介面層級和 Avd 的變更。_

## <a name="changes-to-android-sdk-tooling"></a>Android SDK 工具的變更

在最新版 android SDK 工具的詳細資訊，Google 已移除現有的 AVD 和 SDK 管理員改用新的 CLI （命令列介面） 工具。 **Android**程式已被移除，並用於 Mac 和 Visual Studio 的 Xamarin 的舊版本的 Visual Studio 中的 Google GUI （圖形化使用者介面） 管理員將無法再運作舊版本 25.2.5 Android SDK 工具。 例如，嘗試使用**android**透過命令列程式會導致錯誤訊息如下所示：

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

下列各節說明如何管理的 Android SDK 和 Android 虛擬裝置使用 Android SDK 25.3.0 和更新版本。

### <a name="ui-tools"></a>UI 工具

Visual Studio 和 Visual Studio for Mac 現在提供已停止的 Google gui 管理員 Xamarin 取代項目：

-   若要下載 Android SDK 工具、 平台，以及其他元件所需的開發 Xamarin.Android 應用程式，使用[Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md)而不是傳統 Google SDK Manager。

-   若要建立及設定 Android 虛擬裝置，使用[Xamarin Android 裝置管理員](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)而不是傳統 Google 模擬器管理員。

這些工具的功能相當於 Google GUI 為基礎所取代的管理員。

### <a name="cli-tools"></a>CLI 工具

或者，您可以使用 CLI 工具來管理和更新您的模擬器和 Android SDK。 下列程式現在會構成 Android SDK 工具的命令列介面：

#### <a name="sdkmanager"></a>sdkmanager

**中加入：** Android SDK 工具 25.2.3 (2016 年 11 月) 及更高版本。

新程式可以呼叫**sdkmanager**中**工具/bin**您的 Android SDK 的資料夾。 此工具用來維護 Android SDK，在命令列。 如需有關使用此工具的詳細資訊，請參閱[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**中加入：** Android SDK 工具 25.3.0 (年 3 月，2017) 及更高版本。

新程式可以呼叫**avdmanager**中**工具/bin**您的 Android SDK 的資料夾。 此工具用來維護 Avd Google Android 模擬器。 如需有關使用此工具的詳細資訊，請參閱[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降級

您可以降級您**Android SDK 工具**版本所安裝的 Android sdk，從舊版[Android 開發人員網站](https://developer.android.com/studio/index.html)。

### <a name="using-the-old-gui"></a>使用舊的 GUI

您仍然可以使用原始的 GUI 執行**android**程式內您**工具**只要您位於資料夾**Android SDK 工具**版本**25.2.5**或更低。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [Android 裝置管理員](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)
- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
