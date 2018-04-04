---
title: Xamarin.Android 及 JDK 9
description: 本文說明如何在 Java Development Kit (JDK) 9 中解決錯誤 Xamarin.Android。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7DCF0985-F77D-4A68-AC54-10C9846E189A
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 8857823884447f22b7bc5535f43369671d3285bc
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinandroid-and-jdk-9"></a>Xamarin.Android 及 JDK 9

_本文說明如何在 Java Development Kit (JDK) 9 中解決錯誤 Xamarin.Android。_


## <a name="overview"></a>總覽

Xamarin.Android 使用 Java Development Kit (JDK) 來搭配 Android SDK 建置 Android 應用程式和執行 Android 的設計工具整合。 最新版的 Android SDK (API 24 和更新版本) 需要 JDK 8 (1.8)。 **Google Android SDK 工具還不與 JDK 9 相容，因為 Xamarin.Android 不適用於 JDK 9。**

## <a name="jdk-9-errors"></a>JDK 9 錯誤

如果您嘗試使用已安裝的 JDK 9 建置 Xamarin.Android 專案時，就會明確指出不支援 JDK 9 的錯誤。

```shell
Building with JDK Version `9.0.4` is not supported. Please install JDK version `1.8.0`. See https://aka.ms/xamarin/jdk9-errors  
```

若要解決這些錯誤，您必須安裝 JDK 8 (1.8) 中所述[如何更新 Java Development Kit (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)。


## <a name="checking-the-jdk-version"></a>正在檢查的 JDK 版本

您可以查看您已輸入下列命令安裝的 Java 版本 (JDK`bin`目錄必須位於您`PATH`):

```shell
java -version
```

如果已安裝的 JDK 9，您會看到類似下列訊息：

```shell
java version "9.0.4"
Java(TM) SE Runtime Environment (build 9.0.4+11)
Java HotSpot(TM) 64-Bit Server VM (build 9.0.4+11, mixed mode)
```

如果已安裝 JDK 9，您必須安裝 Java JDK 8 (1.8)。 如需如何安裝 JDK 8 資訊，請參閱[如何更新 Java Development Kit (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)

## <a name="known-issues-with-jdk-9"></a>JDK 9 的已知的問題

### <a name="apksigner"></a>apksigner

中的已知問題與 apksigner JDK 9`apksigner.bat`檔案會叫用`apksigner.jar`與`-Djava.ext.dirs`而不是`-classpath`JDK 9 預期。 建議使用的 JDK 8 (1.8)。 如需如何安裝 JDK 8 資訊，請參閱[如何更新 Java Development Kit (JDK) 版本？](~/android/troubleshooting/questions/update-jdk.md)

解除安裝之後 JDK 9，請確定下列路徑上不會設定您`PATH`環境變數，因為它仍然會指向 JDK 9: `C:\ProgramData\Oracle\Java\javapath`。 移除它之後,`java -version`在命令列應該顯示 JDK 8。
