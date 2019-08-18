---
title: 對於 Android SDK 工具所做的變更
description: 變更 Android SDK 如何管理已安裝的 API 層級和 Avd。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 5446f8e7bbdf2a5f625852bb61637be392bc6bc2
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523209"
---
# <a name="changes-to-the-android-sdk-tooling"></a>對於 Android SDK 工具所做的變更

_變更 Android SDK 如何管理已安裝的 API 層級和 Avd。_

## <a name="changes-to-android-sdk-tooling"></a>Android SDK 工具的變更

在最新版本的 Android SDK Tools 中, Google 已移除現有的 AVD 和 SDK 管理員, 以改用新的 CLI (命令列介面) 工具。 **Android**程式已移除, 而 Visual Studio for Mac 中的 Google GUI (圖形化使用者介面) 管理員和 Xamarin 的舊版 Visual Studio Tools 將無法再使用超過 Android SDK Tools 的版本25.2.5。 例如, 嘗試透過命令列使用**android**程式會產生如下的錯誤訊息:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

下列各節說明如何使用 Android SDK 25.3.0 和更新版本來管理 Android SDK 和 Android 虛擬裝置。

### <a name="ui-tools"></a>UI 工具

Visual Studio 和 Visual Studio for Mac 現在為已淘汰的 Google GUI 型管理員提供 Xamarin 取代:

- 若要下載開發 Xamarin Android 應用程式所需的 Android SDK 工具、平臺和其他元件, 請使用[Xamarin Android SDK 管理員](~/android/get-started/installation/android-sdk.md), 而不是舊版的 Google SDK 管理員。

- 若要建立和設定 Android 虛擬裝置, 請使用[Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) , 而不是舊版 Google 模擬器管理員。

這些工具在功能上相當於其所取代的 Google GUI 型管理員。

### <a name="cli-tools"></a>CLI 工具

或者, 您可以使用 CLI 工具來管理及更新您的模擬器和 Android SDK。 下列程式現在會為 Android SDK 工具建立命令列介面:

#### <a name="sdkmanager"></a>sdkmanager

**已在中新增:** Android SDK Tools 25.2.3 版 (2016 年11月) 和更高版本。

Android SDK 的**tools/bin**資料夾中有一個稱為**sdkmanager**的新程式。 此工具可用來維護命令列上的 Android SDK。 如需使用此工具的詳細資訊, 請參閱[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**已在中新增:** Android SDK Tools 25.3.0 (3 月、2017) 和更高版本。

Android SDK 的**tools/bin**資料夾中有一個稱為**avdmanager**的新程式。 此工具可用來維護 Android Emulator 的 Avd。 如需使用此工具的詳細資訊, 請參閱[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降級

您可以從[Android 開發人員網站](https://developer.android.com/studio/index.html)安裝舊版的 Android SDK, 以降級**Android SDK Tools**版本。

### <a name="using-the-old-gui"></a>使用舊版 GUI

只要您使用的是**Android SDK Tools** **25.2.5**或更低版本, 您仍然可以透過在 [**工具**] 資料夾內執行**android**程式來使用原始 GUI。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)
- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
