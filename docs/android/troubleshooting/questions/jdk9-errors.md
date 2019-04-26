---
title: Xamarin.Android 和 Java Development Kit 9
description: 這篇文章說明如何解決 Java Development Kit (JDK) 9 或更新版本在 Xamarin.Android 中的錯誤。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: d8c64ff79367d93e282edd9534ffb98f5bb90c93
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153317"
---
# <a name="xamarinandroid-and-java-development-kit-9-or-later"></a>Xamarin.Android 和 Java Development Kit 9 或更新版本

_這篇文章說明如何解決 Java Development Kit (JDK) 9 或更新版本在 Xamarin.Android 中的錯誤。_


## <a name="overview"></a>總覽

Xamarin.Android 使用 Java Development Kit (JDK)，來與適用於建置 Android 應用程式和執行 Android designer 的 Android SDK 整合。 最新版的 Android SDK (API 24 和更新版本) 需要 JDK 8 (1.8) 或 Microsoft 行動 OpenJDK Preview。 **因為 Google 的 Android SDK 工具尚未與不相容 JDK 9，Xamarin.Android 無法使用 JDK 9 或更新版本。**

## <a name="jdk-errors"></a>JDK 錯誤

如果您嘗試建置晚於 JDK 8 的 jdk 版本的 Xamarin.Android 專案時，就會明確指出，不支援這個版本的 JDK 的錯誤。 例如: 

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

若要解決這些錯誤，您必須安裝 JDK 8 (1.8) 中所述[如何更新 Java Development Kit (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)。
或者，您可以安裝[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md) Microsoft Mobile OpenJDK 最終會取代 Xamarin.Android 開發的 JDK 8。


## <a name="checking-the-jdk-version"></a>正在檢查 JDK 版本

您可以查看您已輸入下列命令來安裝的 Java 版本 (JDK`bin`目錄必須位於您`PATH`):

```shell
java -version
```

如果安裝 JDK 9，您會看到類似下列訊息：

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果已安裝 JDK 9 或更新版本，您必須安裝 Java JDK 8 (1.8) 或 Microsoft 行動 OpenJDK Preview。 如需有關如何安裝 JDK 8 的資訊，請參閱 <<c0> [ 如何更新 Java Development Kit (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)。 如需如何安裝 Microsoft Mobile OpenJDK 資訊，請參閱[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。

請注意，您不需要解除安裝更新版本的 JDK;不過，您必須確定 JDK 8，而不是更新版本的 JDK 版本，使用 Xamarin。 在 Visual Studio 中，按一下**工具 > 選項 > Xamarin > Android 設定**。 如果**Java 開發套件位置**未設定為 JDK 8 位置 (例如**c:\\Program Files\\Java\\jdk1.8.0_111**)，按一下 **變更**並將它設定為 JDK 8 安裝所在的位置。 在 Visual Studio for Mac 中，瀏覽至**喜好設定 > 專案 > SDK 的位置 > Android > Java SDK (JDK)** 然後按一下**瀏覽**來更新此路徑。

## <a name="known-issues-with-jdk-9"></a>JDK 9 的已知的問題

### <a name="apksigner"></a>apksigner

Apksigner 與 JDK 9 中已知的問題`apksigner.bat`檔會叫用`apksigner.jar`具有`-Djava.ext.dirs`而不是`-classpath`必須 JDK 9。 建議使用 JDK 8 (1.8)。 如需有關如何安裝 JDK 8 的資訊，請參閱[如何更新 Java Development Kit (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)

如果您已安裝 JDK 9，請確定下列路徑上不會設定您`PATH`環境變數，因為它仍然會指向 JDK 9: `C:\ProgramData\Oracle\Java\javapath`。 移除之後,`java-version`在命令列應該顯示 JDK 8。
