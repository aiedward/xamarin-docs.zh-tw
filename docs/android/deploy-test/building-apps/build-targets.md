---
title: 建置目標
description: 本檔將列出 Xamarin. Android 組建程式中所有支援的目標。
ms.prod: xamarin
ms.assetid: 17DE89FF-F316-4620-B865-EF6E0963A29C
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/17/2020
ms.openlocfilehash: 4482e6c4bfe2a6952d59d896b7c79ca82432b42b
ms.sourcegitcommit: 38496cfd4d30fd40a011011f303a31de639bd699
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91247258"
---
# <a name="build-targets"></a>建置目標

下列組建目標是針對 Xamarin. Android 專案所定義。

## <a name="build"></a>Build

在專案和所有相依性中建立原始程式碼。

此目標不 *會*) 建立 Android 套件 (`.apk` 。
若要建立 Android 封裝，請使用 [SignAndroidPackage](#signandroidpackage) 目標， *或* 在建立時將 [' $ (AndroidBuildApplicationPackage) ](~/android/deploy-test/building-apps/build-properties.md#androidbuildapplicationpackage) 屬性設定為 True：

```shell
msbuild /p:AndroidBuildApplicationPackage=True App.sln
```

## <a name="buildandstartaotprofiling"></a>BuildAndStartAotProfiling

使用內嵌的 AOT profiler 來建立應用程式、將分析工具的 TCP 埠設定為 [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport) ，並啟動預設活動。

預設 TCP 埠為 `9999` 。

已在 Xamarin. Android 10.2 中新增。

## <a name="clean"></a>Clean

移除組建進程所產生的所有檔案。

## <a name="finishaotprofiling"></a>FinishAotProfiling

必須在[BuildAndStartAotProfiling](#buildandstartaotprofiling)目標*之後*呼叫。

從裝置或模擬器透過 TCP 埠收集 AOT profiler 資料 [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport)
並將它們寫入至 [`$(AndroidAotCustomProfilePath)`](~/android/deploy-test/building-apps/build-properties.md#androidaotcustomprofilepath) 。

埠和自訂設定檔的預設值為 `9999` 和 `custom.aprof` 。

若要將其他選項傳遞至 `aprofutil` ，請在 [`$(AProfUtilExtraOptions)`](~/android/deploy-test/building-apps/build-properties.md#aprofutilextraoptions)
屬性中找到的值而定。

這相當於：

```shell
aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
```

已在 Xamarin. Android 10.2 中新增。

## <a name="install"></a>安裝

[建立、簽署](#signandroidpackage)Android 套件，並將其安裝到預設裝置或虛擬裝置上。

[`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget)屬性會指定 android 套件可能安裝或移除的 android 目標裝置。

```bash
# Install package onto emulator via -e
# Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
```

## <a name="signandroidpackage"></a>SignAndroidPackage

建立並簽署 Android 套件 (`.apk`) 檔。

與 `/p:Configuration=Release` 搭配使用來產生獨立的「發行」套件。

## <a name="startandroidactivity"></a>StartAndroidActivity

啟動裝置上的預設活動或執行中的模擬器。

若要啟動不同的活動，請將 [`$(AndroidLaunchActivity)`](~/android/deploy-test/building-apps/build-properties.md#androidlaunchactivity)
屬性至活動名稱。

這相當於：

```shell
adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)
```

已在 Xamarin. Android 10.2 中新增。

## <a name="stopandroidpackage"></a>StopAndroidPackage

完全停止裝置上的應用程式封裝或執行中的模擬器。

這相當於：

```shell
adb shell am force-stop @PACKAGE_NAME@
```

已在 Xamarin. Android 10.2 中新增。

## <a name="uninstall"></a>解除安裝

從預設裝置或虛擬裝置卸載 Android 套件。

[`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget)屬性會指定 android 套件可能安裝或移除的 android 目標裝置。

## <a name="updateandroidresources"></a>UpdateAndroidResources

更新檔案 `Resource.designer.cs` 。

當專案新增資源時，IDE 通常會呼叫此目標。
