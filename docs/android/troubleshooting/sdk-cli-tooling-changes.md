---
title: 對於 Android SDK 工具所做的變更
description: 更改 Android SDK 如何管理已安裝的 API 級別和 AVD。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 4c559a76d7354fd957d065717ef14d91591d1be0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019501"
---
# <a name="changes-to-the-android-sdk-tooling"></a>對於 Android SDK 工具所做的變更

_更改 Android SDK 如何管理已安裝的 API 級別和 AVD。_

## <a name="changes-to-android-sdk-tooling"></a>對 Android SDK 工具的變更

在最新版本的 Android SDK 工具中,Google 刪除了現有的 AVD 和 SDK 管理器,轉而採用新的 CLI(命令列介面)工具。 **Android**程式已被刪除,Visual Studio 中的 Google GUI(圖形使用者介面)管理器適用於 Mac 和 Xamarin 的較舊版本的 Visual Studio 工具將不再工作,超過 Android SDK 工具版本 25.2.5。 例如,嘗試通過命令行使用**android**程式將導致錯誤消息,如下所示:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

以下各節說明如何使用 Android SDK 25.3.0 及更高版本管理 Android SDK 和 Android 虛擬設備。

### <a name="ui-tools"></a>UI 工具

Mac 的 Visual Studio 和 Visual Studio 現在為停產的基於 Google GUI 的管理員提供 Xamarin 取代:

- 要下載開發 Xamarin.Android 應用所需的 Android SDK 工具、平臺和其他元件,請使用[Xamarin Android SDK 管理器](~/android/get-started/installation/android-sdk.md)而不是傳統的 Google SDK 管理器。

- 要創建和配置 Android 虛擬裝置,請使用[Android 裝置管理員](~/android/get-started/installation/android-emulator/device-manager.md),而不是傳統的 Google 模擬器管理器。

這些工具在功能上相當於他們所取代的基於 Google GUI 的管理器。

### <a name="cli-tools"></a>CLI 工具

或者,您可以使用 CLI 工具來管理和更新模擬器和 Android SDK。 以下應用程式現在構成 Android SDK 工具的命令列介面:

#### <a name="sdkmanager"></a>sdkmanager

**新增於:** Android SDK 工具 25.2.3 (2016 年 11 月) 及以上。

在 Android SDK**的工具/ bin**資料夾中有一個名為**sdkmanager**的新程式。 此工具用於在命令行維護 Android SDK。 有關使用此工具的詳細資訊,請參閱[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**新增於:** Android SDK 工具 25.3.0 (2017 年 3 月) 及以上。

在 Android SDK**的工具/ bin**資料夾中有一個名為**avdmanager**的新程式。 此工具用於維護 Android 模擬器的 AVD。 有關使用此工具的詳細資訊,請參閱[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降級

您可以通過從[Android 開發人員網站](https://developer.android.com/studio/index.html)安裝 Android SDK 的早期版本來降級**Android SDK**版本。

### <a name="using-the-old-gui"></a>使用舊介面介面

您仍然可以使用原始 GUI,透過在**工具**資料夾中運行**Android**程式,只要您位於 Android **SDK 工具**版本**25.2.5**或更低。

## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [安卓設備管理員](~/android/get-started/installation/android-emulator/device-manager.md)
- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
