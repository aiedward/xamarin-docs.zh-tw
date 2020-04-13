---
title: Xamarin.安卓和JAVA開發工具組9
description: 本文介紹如何解決 Xamarin.Android 中的 Java 開發工具組 (JDK) 9 或更高版本錯誤。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 2ea7c9b9f900bc339d183c2f5b317792ebec5232
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026837"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.安卓和JAVA開發工具組9或更高版本

_本文介紹如何解決 Xamarin.Android 中的 Java 開發工具組 (JDK) 9 或更高版本錯誤。_

## <a name="overview"></a>概觀

Xamarin.Android 使用 Java 開發工具套件 (JDK) 與 Android SDK 整合,用於建構 Android 應用並運行 Android 設計器。 最新版本的 Android SDK(API 24 及更高版本)需要 JDK 8 (1.8) 或微軟行動開放 JDK 預覽版。 **由於 Google 提供的 Android SDK 工具尚未與 JDK 9 相容,因此 Xamarin.Android 不適用於 JDK 9 或更高版本。**

## <a name="jdk-errors"></a>JDK 錯誤

如果您嘗試建構具有 JDK 版本晚於 JDK 8 的 Xamarin.Android 專案,則會收到一個顯式錯誤,指示不支援此版本的 JDK。 例如：

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors
```

要解決這些錯誤,必須安裝 JDK 8 (1.8),如[如何更新 JAVA 開發工具組 (JDK) 版本?](~/android/troubleshooting/questions/update-jdk.md)
或者,您可以安裝[微軟移動開放JDK預覽](~/android/get-started/installation/openjdk.md)微軟移動開放JDK最終將取代JDK 8用於Xamarin.Android開發。

## <a name="checking-the-jdk-version"></a>檢查 JDK 版本

通過輸入以下命令(JDK`bin`目錄必須位於`PATH`您的中),可以檢查已安裝的 Java 版本。

```shell
java -version
```

如果安裝了 JDK 9,您將看到如下消息:

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果安裝了 JDK 9 或更高版本,則必須安裝 Java JDK 8 (1.8) 或 Microsoft 行動開放 JDK 預覽。 有關如何安裝 JDK 8 的資訊,請參閱[如何更新 JAVA 開發工具組 (JDK) 版本?](~/android/troubleshooting/questions/update-jdk.md) 有關如何安裝微軟移動開放JDK的資訊,請參閱[微軟移動開放JDK預覽](~/android/get-started/installation/openjdk.md)。

請注意,您不必卸載更高版本的 JDK;因此,您不必卸載該版本的 JDK。但是,您必須確保 Xamarin 使用的是 JDK 8,而不是後來的 JDK 版本。 在視覺化工作室中,按下 **「工具>選項> Xamarin > Android 設定**。 如果**Java 開發工具套件位置**未設定為 JDK 8 位置(如**\\C:\\程式\\檔 Java jdk1.8.0_111),** 請按下「**更改」** 並將其設定為安裝 JDK 8 的位置。 在 Mac 視覺化工作室中,導航到 **「首選項>>」計畫「> Android > Java SDK (JDK) > 中的 SDK 位置,** 然後單擊「**瀏覽**」以更新此路徑。

## <a name="known-issues-with-jdk-9"></a>JDK 9 的已知問題

### <a name="apksigner"></a>阿普克塞納

apksigner 和 JDK 9`apksigner.bat`存在已知問題`apksigner.jar`,`-Djava.ext.dirs`其中檔調`-classpath`用 了 JDK 9 期望的 與 建議使用 JDK 8 (1.8)。 有關如何安裝 JDK 8 的資訊,請參閱[如何更新 JAVA 開發工具組 (JDK) 版本?](~/android/troubleshooting/questions/update-jdk.md)

如果已安裝 JDK 9,請確保以下路徑未設定`PATH`在環境 變數上,因為它仍將指向 JDK 9: `C:\ProgramData\Oracle\Java\javapath`。 刪除後,`java-version`在命令列應顯示 JDK 8。
