---
title: 如何更新 Java 開發套件 (JDK) 版本？
description: 本文演示如何在 Windows 和 Mac 上更新 Java 開發工具套件 (JDK) 版本。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019522"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java 開發套件 (JDK) 版本？

_本文演示如何在 Windows 和 Mac 上更新 Java 開發工具套件 (JDK) 版本。_

## <a name="overview"></a>概觀

Xamarin.Android 使用 Java 開發工具套件 (JDK) 與 Android SDK 整合,用於建構 Android 應用並運行 Android 設計器。 最新版本的 Android SDK(API 24 及更高版本)需要 JDK 8 (1.8)。 或者,您可以安裝[微軟移動開放JDK預覽](~/android/get-started/installation/openjdk.md)。 微軟行動開放JDK最終將取代JDK 8用於Xamarin.Android開發。

要更新至微軟移動開放JDK,請參閱[微軟行動開放JDK預覽](~/android/get-started/installation/openjdk.md)。 要更新至 JDK 8,請按照以下步驟操作:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. 從[Oracle 網站](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下載 JDK 8 (1.8):

    ![甲骨文網站上JDK下載頁面的螢幕截圖](update-jdk-images/image1.png)

2. 選擇 64 位元以允許在 Xamarin Android 設計器中呈現[自訂控制件](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols):

    ![選擇 Windows x64 JDK 套件從 JDK 下載頁面下載](update-jdk-images/image2.png)

3. 執行 .exe 並安裝**開發工具**:

    ![在 JDK 安裝程式中安裝開發工具](update-jdk-images/image3.png)

4. 開啟視覺化工作室並更新**JAVA 開發工具套件位置**,以指向「工具>選項」下的新 JDK **> Xamarin > Android 設定> Java 開發工具套件位置**:

    [![Android 設定頁中 JDK 的路徑設定](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

請務必在更新位置後重新啟動 Visual Studio。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

1. 從[Oracle 網站](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下載 JDK 8 (1.8):

    ![甲骨文網站上JDK下載頁面的螢幕截圖](update-jdk-images/image1.png)

2. 開啟 .dmg 檔案並執行 .pkg 安裝程式:

    ![在 macOS 上執行 JDK 安裝程式](update-jdk-images/image5.png)

Mac OS 將透過更新 **/系統/庫/框架/JavaVM.framework/版本/當前**,自動將新的 JDK 版本設置為預設值。 然後,您可以仔細檢查**JAVA SDK (JDK)** 位置是否設置為 Mac > 首選項 >项目 > SDK > 位置在**Android > 位置> Java SDK 位置(JDK) 位置**)的 Visual Studio 下的預期預設 **/usr:**

[![在「Android 位置」選項卡中設定 JDK 位置](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
