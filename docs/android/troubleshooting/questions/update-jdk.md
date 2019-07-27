---
title: 如何更新 Java 開發套件 (JDK) 版本？
description: 本文說明如何更新 Windows 和 Mac 上的 JAVA 開發工具組 (JDK) 版本。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 02768bfcae9da06d8fd86ada63dd2d463245d254
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510457"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java 開發套件 (JDK) 版本？

_本文說明如何更新 Windows 和 Mac 上的 JAVA 開發工具組 (JDK) 版本。_

## <a name="overview"></a>總覽

Xamarin 會使用 JAVA 開發工具組 (JDK) 來與建立 Android 應用程式和執行 Android designer 的 Android SDK 整合。 最新版的 Android SDK (API 24 和更新版本) 需要 JDK 8 (1.8)。 或者, 您也可以安裝[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。 Microsoft Mobile OpenJDK 最終會取代適用于 Xamarin. Android 開發的 JDK 8。

若要更新至 Microsoft Mobile OpenJDK, 請參閱[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。 若要更新為 JDK 8, 請遵循下列步驟:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  從[Oracle 網站](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下載 JDK 8 (1.8):

    ![Oracle 網站上 JDK 下載頁面的螢幕擷取畫面](update-jdk-images/image1.png)

2.  挑選64位版本, 以允許在 Xamarin Android 設計工具中呈現[自訂控制項](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols):

    ![從 JDK 下載頁面選取要下載的 Windows x64 JDK 套件](update-jdk-images/image2.png)

3.  執行 .exe 並安裝**開發工具**:

    ![在 JDK 安裝程式中安裝開發工具](update-jdk-images/image3.png)

4.  開啟 Visual Studio 並更新**JAVA 開發工具組位置**, 以指向 工具 > 選項 下的新 JDK, **> Xamarin > Android 設定 > JAVA 開發工具組位置**:

    [![[Android 設定] 頁面中 JDK 的路徑設定](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

更新位置之後, 請務必重新開機 Visual Studio。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  從[Oracle 網站](https://www.oracle.com/technetwork/java/javase/downloads/index.html)下載 JDK 8 (1.8):

    ![Oracle 網站上 JDK 下載頁面的螢幕擷取畫面](update-jdk-images/image1.png)

2.  開啟 dmg 檔案, 並執行 .pkg 安裝程式:

    ![在 macOS 上執行 JDK 安裝程式](update-jdk-images/image5.png)

Mac OS 會藉由更新 **/System/Library/Frameworks/JAVAVM.framework/Versions/Current**, 自動將新的 JDK 版本設定為預設值。 接著, 您可以再次檢查**JAVA sdk (jdk)** 位置是否已設定為預設的 **/Usr** **Visual Studio for Mac > 偏好 > 專案 > SDK 位置 > JAVA sdk (JDK) 位置 > Android > 位置**:

[![在 [Android 位置] 索引標籤中設定 JDK 位置](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

