---
title: "對於 Android SDK 工具所做的變更"
description: "Android SDK 會如何管理已安裝的應用程式開發介面層級和 Avd 的變更。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 69e9f08870a01c056951700978d07277af5edfa8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="changes-to-the-android-sdk-tooling"></a>對於 Android SDK 工具所做的變更

_Android SDK 會如何管理已安裝的應用程式開發介面層級和 Avd 的變更。_

## <a name="changes-to--android-sdk-tooling"></a>Android SDK 工具的變更

在現代版的 android SDK 工具，Google 已移除現有的 AVD 和 SDK 管理員 in favour of 新_命令列介面_(CLI) 工具。 前者**android**已經移除 windows 程式和過去的 Android SDK 工具版本，用於 Mac 和 Visual Studio 的 Xamarin 的舊版本的 Visual Studio 中的 GUI （圖形化使用者介面） 管理員就無法再運作。


![在 Visual Studio 中的 android IDE 功能表](sdk-cli-tooling-changes-images/android-ide-menu.png)

嘗試使用**android**透過命令列程式會導致錯誤訊息如下所示：

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

因此，您必須使用 CLI 工具來管理與更新您的模擬器和 Android SDK。

### <a name="cli-tools"></a>CLI 工具

下列程式現在會構成 Android SDK 工具的命令列介面：

#### <a name="sdkmanager"></a>sdkmanager

**中加入：** Android SDK 工具 25.2.3 (2016 年 11 月) 及更高版本。

新程式可以呼叫**sdkmanager**中**工具/bin**您的 Android SDK 的資料夾。 此工具用來維護 Android SDK，在命令列。 如需有關使用此工具的詳細資訊，請參閱[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**中加入：** Android SDK 工具 25.3.0 (年 3 月，2017) 及更高版本。

新程式可以呼叫**avdmanager**中**工具/bin**您的 Android SDK 的資料夾。 此工具用來維護 AVD Google Android 模擬器。 如需有關使用此工具的詳細資訊，請參閱[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降級

您可以降級您**Android SDK 工具**版本所安裝的 Android sdk，從舊版[Android 開發人員網站](https://developer.android.com/studio/index.html)。

### <a name="using-the-old-gui"></a>使用舊的 GUI

您仍然可以使用原始的 GUI 執行**android**程式內您**工具**只要您位於資料夾**Android SDK 工具**版本**25.2.5**或更低。


## <a name="related-links"></a>相關連結

- [Android SDK 安裝](~/android/get-started/installation/android-sdk.md)
- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
