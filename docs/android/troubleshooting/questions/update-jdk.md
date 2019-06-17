---
title: 如何更新 Java 開發套件 (JDK) 版本？
description: 這篇文章說明如何更新 Windows 和 mac 上的 Java Development Kit (JDK) 版本
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 290a7deef4fdc10163bdb09881382f011b0dcd32
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61154110"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java 開發套件 (JDK) 版本？

_這篇文章說明如何更新 Windows 和 mac 上的 Java Development Kit (JDK) 版本_

## <a name="overview"></a>總覽

Xamarin.Android 使用 Java Development Kit (JDK)，來與適用於建置 Android 應用程式和執行 Android designer 的 Android SDK 整合。 最新版的 Android SDK (API 24 和更新版本) 需要 JDK 8 (1.8)。 或者，您可以安裝[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。 Microsoft Mobile OpenJDK 最終會取代 Xamarin.Android 開發的 JDK 8。

若要更新 Microsoft Mobile OpenJDK，請參閱[Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md)。 若要更新至 JDK 8，請遵循下列步驟：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  從下載 JDK 8 (1.8) [Oracle 網站](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![螢幕擷取畫面的 jdk 下載 Oracle 網站上的頁面](update-jdk-images/image1.png)

2.  挑選 64 位元版本，以便呈現[自訂控制項](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols)Xamarin Android 設計工具中：

    ![選取 Windows x64 JDK 封裝，從 [JDK] 下載頁面下載](update-jdk-images/image2.png)

3.  執行.exe，然後安裝**開發工具**:

    ![安裝的 JDK 安裝程式中的開發工具](update-jdk-images/image3.png)

4.  開啟 Visual Studio，並更新**Java 開發套件位置**以指向新的 JDK 下**工具 > 選項 > Xamarin > Android 設定 > Java 開發套件位置**:

    [![在 [Android 設定] 頁面中的 JDK 路徑設定](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

請務必重新啟動 Visual Studio 之後更新的位置。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1.  從下載 JDK 8 (1.8) [Oracle 網站](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![螢幕擷取畫面的 jdk 下載 Oracle 網站上的頁面](update-jdk-images/image1.png)

2.  開啟.dmg 檔案，並執行.pkg 安裝程式：

    ![在 macOS 上執行的 JDK 安裝程式](update-jdk-images/image5.png)

Mac OS 將會自動設定新的 JDK 版本做為預設藉由更新 **/System/Library/Frameworks/JavaVM.framework/Versions/Current**。 您可以再確認**Java SDK (JDK)** 位置會設定為預期的預設值是 **/usr**下**Visual Studio for Mac > 喜好設定 > 專案 > SDK 的位置 >Android > 位置 > Java SDK (JDK) 位置**:

[![Android 的位置 索引標籤中設定 JDK 的位置](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

