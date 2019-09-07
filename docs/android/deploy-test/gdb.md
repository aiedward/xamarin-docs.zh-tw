---
title: GDB
ms.prod: xamarin
ms.assetid: CD0BE462-FA38-4881-B481-82AD05B3B8FE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 0599b2374addf461e59948a1926de06e6e1e746a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754049"
---
# <a name="gdb"></a>GDB

## <a name="overview"></a>總覽

Xamarin.Android 4.10 引進了透過 `_Gdb` MSBuild 目標來使用 `gdb` 的部分支援。 

> [!NOTE]
> `gdb` 援需要安裝 Android NDK。

有三種方式可以使用 `gdb`：

1. [啟用 Fast Deployment 的偵錯組建](#Debug_Builds_with_Fast_Deployment)。
1. [停用 Fast Deployment 的偵錯組建](#Debug_Builds_without_Fast_Deployment)。
1. [發行組建](#Release_Builds)。

當發生錯誤時，請參閱[疑難排解](#Troubleshooting)一節。

<a name="Debug_Builds_with_Fast_Deployment" />

### <a name="debug-builds-with-fast-deployment"></a>啟用 Fast Deployment 的偵錯組建

當啟用 Fast Deployment 來建置及部署偵錯組件時，可使用 `_Gdb` MSBuild 目標來附加 `gdb`。

首先，安裝應用程式。 這可透過 IDE 進行，或透過命令列：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:Install *.csproj
```

其次，執行 `_Gdb` 目標。 在執行結束時，便會印出 `gdb` 命令列：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
...
```

`_Gdb` 目標會啟動在您 `AndroidManifest.xml` 檔案中宣告的任意啟動活動。 若要明確指定要執行的活動，請使用 `RunActivity` MSBuild 屬性。 此時不支援啟動服務及其他 Android 建構。

`_Gdb` 目標會建立一個 `gdb-symbols` 目錄，並將您目標 `/system/lib` 及 `$APPDIR/lib` 目錄中的內容複製到該目錄。

> [!NOTE]
> `gdb-symbols` 目錄中的內容會繫結至您部署到的 Android 目標，並且不會在您變更目標時自動取代。 (請將此視為一個 Bug。)若您變更了 Android 目標裝置，您必須手動刪除此目錄。

最後，複製產生的 `gdb` 命令並在您的殼層中執行它：

```bash
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) bt
#0  0x40082e84 in nanosleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#1  0x4008ffe6 in sleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#2  0x74e46240 in ?? ()
#3  0x74e46240 in ?? ()
(gdb) c
```

<a name="Debug_Builds_without_Fast_Deployment" />

## <a name="debug-builds-without-fast-deployment"></a>停用 Fast Deployment 的偵錯組建

*啟用* Fast Deployment 的偵錯組建會藉由將 Android NDK 的 `gdbserver` 程式複製到 Fast Deployment `.__override__` 目錄來執行。 當停用 Fast Deployment 時，此目錄便不存在。

有兩種因應措施：

- 設定 `debug.mono.log` 系統屬性，以建立 `.__override__` 目錄。
- 在您的 `.apk` 中包含 `gdbserver`。

### <a name="setting-the-debugmonolog-system-property"></a>設定 `debug.mono.log` 系統屬性

若要設定 `debug.mono.log` 系統屬性，請使用 `adb` 命令：

```bash
$ adb shell setprop debug.mono.log gc
```

設定系統屬性之後，請執行 `_Gdb` 目標及印出的 `gdb` 命令，如同在啟用 Fast Deployment 的偵錯組建中所進行之操作：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

### <a name="including-gdbserver-in-your-app"></a>在您的應用程式中包含 `gdbserver`

若要在您的應用程式中包含 `gdbserver`：

1. 在您的 Android NDK 中尋找 `gdbserver` (通常會在 **$ANDROID\_NDK\_PATH/prebuilt/android-arm/gdbserver/gdbserver** 中)，並將其複製到您的專案目錄。

2. 將 `gdbserver` 重新命名為 **libs/armeabi-v7a/libgdbserver.so**。

3. 使用 `AndroidNativeLibrary` 的**建置動作**將 **libs/armeabi-v7a/libgdbserver.so** 新增到您的專案。

4. 重建並重新安裝您的應用程式。

在重新安裝應用程式之後，請執行 `_Gdb` 目標及印出的 `gdb` 命令，如同在啟用 Fast Deployment 的偵錯組建中所進行之操作：

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

<a name="Release_Builds" />

## <a name="release-builds"></a>發行的組建

`gdb` 支援需要三個項目：

1. `INTERNET` 權限。
2. 啟用應用程式偵錯。
3. 可存取的 `gdbserver`。

根據預設，偵錯應用程式中會啟用 `INTERNET` 權限。 若您的應用程式中尚未存在，您可以藉由編輯 **Properties/AndroidManifest.xml** 或編輯[專案屬性](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)來新增它。

應用程式偵錯可藉由將 [ApplicationAttribute.Debugging](xref:Android.App.ApplicationAttribute.Debuggable) 自訂屬性內容設為 `true`，或編輯 **Properties/AndroidManifest.xml** 並將 `//application/@android:debuggable` 屬性設為 `true` 來啟用：

```xml
<application android:label="Example.Name.Here" android:debuggable="true">
```

您可藉由遵循[停用 Fast Deployment 的偵錯組建](#Debug_Builds_without_Fast_Deployment)一節來提供可存取的 `gdbserver`。

一個缺點：`_Gdb` MSBuild 目標會終止任何先前在執行的應用程式執行個體。 這在 Android v4.0 之前的目標上無法運作。

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>疑難排解

### <a name="mono_pmip-doesnt-work"></a>`mono_pmip` 無法運作

`mono_pmip` 功能 (用於[取得受控堆疊框架](https://www.mono-project.com/docs/debug+profile/debug/#debugging-with-gdb)上很有用) 會從 `libmonosgen-2.0.so` 中匯出，是 `_Gdb` 目標目前無法下拉的項目。 (這將會在未來的版本中修正。)

若要啟用呼叫位於 `libmonosgen-2.0.so` 中的函式，請從目標裝置將其複製到 `gdb-symbols` 目錄：

```bash
$ adb pull /data/data/Mono.Android.DebugRuntime/lib/libmonosgen-2.0.so Project/gdb-symbols
```

然後重新啟動您的偵錯工作階段。

### <a name="bus-error-10-when-running-the-gdb-command"></a>執行  命令時發生匯流排錯誤：10

當 `gdb` 命令發生 `"Bus error: 10"` 命令錯誤時，請重新啟動 Android 裝置。

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
Bus error: 10
$
```

### <a name="no-stack-trace-after-attach"></a>在附加之後沒有堆疊追蹤

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
(gdb) bt
No stack.
```

這通常表示 `gdb-symbols` 目錄的內容並未與您的 Android 目標同步。 (您是否變更了您的 Android 目標？)

請刪除 `gdb-symbols` 目錄並再試一次。
