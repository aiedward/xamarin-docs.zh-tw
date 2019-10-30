---
title: Xamarin. Android 和 JAVA 開發工具組9
description: 本文說明如何解決 Xamarin 中的 JAVA 開發工具組（JDK）9或更新版本的錯誤。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026837"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin. Android 和 JAVA 開發工具組9或更新版本

_本文說明如何解決 Xamarin 中的 JAVA 開發工具組（JDK）9或更新版本的錯誤。_

## <a name="overview"></a>總覽

Xamarin 會使用 JAVA 開發工具組（JDK）來與建立 Android 應用程式和執行 Android designer 的 Android SDK 整合。 最新版的 Android SDK （API 24 和更新版本）需要 JDK 8 （1.8）或 Microsoft Mobile OpenJDK Preview。 **因為 Google 提供的 Android SDK 工具尚未與 JDK 9 相容，所以 Xamarin 無法搭配 JDK 9 或更新版本使用。**

## <a name="jdk-errors"></a>JDK 錯誤

如果您嘗試使用 jdk 8 之後的 JDK 版本來建立 Xamarin Android 專案，您會收到明確錯誤，指出不支援這個版本的 JDK。 例如:

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

若要解決這些錯誤，您必須依照[如何? 更新 JAVA 開發工具組（JDK）版本？](~/android/troubleshooting/questions/update-jdk.md)中的說明，安裝 JDK 8 （1.8）。
或者，您也可以安裝[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) 。 Microsoft mobile OpenJDK 最終會取代適用于 Xamarin. Android 開發的 JDK 8。

## <a name="checking-the-jdk-version"></a>檢查 JDK 版本

您可以藉由輸入下列命令來檢查您已安裝的 JAVA 版本（JDK `bin` 目錄必須在您的 `PATH`中）：

```shell
java -version
```

如果已安裝 JDK 9，您會看到如下的訊息：

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果已安裝 JDK 9 或更新版本，您必須安裝 JAVA JDK 8 （1.8）或 Microsoft Mobile OpenJDK Preview。 如需有關如何安裝 JDK 8 的詳細資訊，請參閱[如何? 更新 JAVA 開發工具組（JDK）版本？](~/android/troubleshooting/questions/update-jdk.md)。 如需有關如何安裝 Microsoft Mobile OpenJDK 的詳細資訊，請參閱[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。

請注意，您不需要卸載較新版本的 JDK;不過，您必須確定 Xamarin 使用 JDK 8，而不是較新的 JDK 版本。 在 Visual Studio 中，按一下 [工具] [ **> 選項] > [Xamarin > Android 設定**]。 如果**JAVA 開發工具組位置**未設定為 jdk 8 位置（例如**C：\\Program Files\\JAVA\\JDK 1.8.0 _111**），請按一下 [**變更**]，並將它設定為安裝 JDK 8 的位置。 在 Visual Studio for Mac 中，流覽至 喜好設定  **> 專案 > Sdk 位置 > Android > JAVA SDK （JDK）** ，然後按一下**流覽**以更新此路徑

## <a name="known-issues-with-jdk-9"></a>JDK 9 的已知問題

### <a name="apksigner"></a>apksigner

Apksigner 和 JDK 9 有一個已知的問題，其中 `apksigner.bat` 檔案會使用 `-Djava.ext.dirs` 來叫用 `apksigner.jar`，而不是 JDK 9 所預期的 `-classpath`。 建議使用 JDK 8 （1.8）。 如需有關如何安裝 JDK 8 的詳細資訊，請參閱[如何? 更新 JAVA 開發工具組（JDK）版本？](~/android/troubleshooting/questions/update-jdk.md)

如果您已安裝 JDK 9，請確定您的 `PATH` 環境變數上未設定下列路徑，因為它仍然會指向 JDK 9： `C:\ProgramData\Oracle\Java\javapath`。 移除之後，在命令列 `java-version` 應該會顯示 JDK 8。
