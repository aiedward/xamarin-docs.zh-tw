---
title: 對於 Android SDK 工具所做的變更
description: 已安裝的 API 層級和 Avd，Android SDK 的管理方式的變更。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112802"
---
# <a name="changes-to-the-android-sdk-tooling"></a>對於 Android SDK 工具所做的變更

_已安裝的 API 層級和 Avd，Android SDK 的管理方式的變更。_

## <a name="changes-to-android-sdk-tooling"></a>Android SDK 工具的變更

最新版的 Android SDK Tools 的詳細資訊，Google 已移除現有的 AVD 和 SDK 管理員，改用新的 CLI （命令列介面） 工具。 **Android**程式已被移除，並在 Visual Studio for Mac 和舊版的 Visual Studio Tools for Xamarin Google GUI （圖形化使用者介面） 管理員將無法再運作 25.2.5 版以後的 Android SDK Tools。 例如，嘗試使用**android**透過命令列程式將會導致錯誤訊息如下所示：

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

下列各節將說明如何管理的 Android SDK 和 Android 虛擬裝置，使用 Android SDK 25.3.0 和更新版本。

### <a name="ui-tools"></a>UI 工具

Visual Studio 和 Visual Studio for Mac 現在提供 Xamarin Google GUI 為基礎的已停用管理員取代：

-   若要下載 Android SDK 工具、 平台，以及開發 Xamarin.Android 應用程式所需的其他元件，使用[Xamarin Android SDK 管理員](~/android/get-started/installation/android-sdk.md)而不是舊版 Google SDK 管理員。

-   若要建立及設定 Android 虛擬裝置，使用[Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)而不是舊版 Google 模擬器管理員。

這些工具的功能等同於 Google GUI 為基礎所取代的管理員。

### <a name="cli-tools"></a>CLI 工具

或者，您可以使用 CLI 工具來管理和更新您的 Android SDK 和模擬器。 下列的程式現在會構成 Android SDK 工具的命令列介面：

#### <a name="sdkmanager"></a>sdkmanager

**已新增至：** Android SDK Tools 25.2.3 (2016 年 11 月) 和更新版本。

沒有新的程式，稱為**sdkmanager**中**工具/bin**您的 Android SDK 資料夾。 此工具用來維護 Android SDK，在命令列。 如需使用此工具的詳細資訊，請參閱[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**已新增至：** Android SDK Tools 25.3.0 (年 3 月，2017) 和更新版本。

沒有新的程式，稱為**avdmanager**中**工具/bin**您的 Android SDK 資料夾。 此工具用來維護 Android 模擬器的 Avd。 如需使用此工具的詳細資訊，請參閱[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降級

您可以降級您**Android SDK Tools**版本安裝舊版的 Android SDK，透過[Android 開發人員網站](https://developer.android.com/studio/index.html)。

### <a name="using-the-old-gui"></a>使用舊 GUI

您仍然可以執行使用原始的 GUI **android**程式內您**工具**只要您位於資料夾**Android SDK Tools**版本**25.2.5**或更低。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md)
- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
