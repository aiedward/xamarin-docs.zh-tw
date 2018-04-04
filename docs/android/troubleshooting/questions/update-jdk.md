---
title: 如何更新 Java Development Kit (JDK) 版本？
description: 本文說明如何更新在 Windows 和 mac 上的 Java Development Kit (JDK) 版本
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: dcfc5e406e60ac72fb1ca1e9cfb0395d17074b2c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>如何更新 Java Development Kit (JDK) 版本？

_本文說明如何更新在 Windows 和 mac 上的 Java Development Kit (JDK) 版本_

## <a name="overview"></a>總覽

Xamarin.Android 使用 Java Development Kit (JDK) 來搭配 Android SDK 建置 Android 應用程式和執行 Android 的設計工具整合。 最新版的 Android SDK (API 24 和更新版本) 需要 JDK 8 (1.8)。 如果您有尚未更新為 JDK 8，請遵循下列步驟來安裝及啟用它：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1.  從下載 JDK 8 (1.8) [Oracle 網站](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![螢幕擷取畫面的 jdk 下載 Oracle 網站上的頁面](update-jdk-images/image1.png)

2.  挑選要允許呈現的 64 位元版本[自訂控制項](https://developer.xamarin.com/releases/vs/xamarin.vs_4/xamarin.vs_4.2/#androiddesignercustomcontrols)Xamarin Android 設計工具中：

    ![選取要從 JDK 下載頁面下載的 Windows x64 JDK 封裝](update-jdk-images/image2.png)

3.  執行.exe，然後安裝**開發工具**:

    ![安裝的 JDK 安裝程式中的開發工具](update-jdk-images/image3.png)

4.  開啟 Visual Studio 和更新**Java Development Kit 位置**以指向新的 JDK 下**工具 > 選項 > Xamarin > Android 設定 > Java Development Kit 位置 > 變更**:

    ![在 IDE [Android 選項] 設定頁面 jdk 路徑設定](update-jdk-images/image4.png)

請務必重新啟動 Visual Studio 之後更新的位置。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1.  從下載 JDK 8 (1.8) [Oracle 網站](http://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![螢幕擷取畫面的 jdk 下載 Oracle 網站上的頁面](update-jdk-images/image1.png)

2.  開啟.dmg 檔案並執行.pkg 安裝程式：

    ![MacOS 上執行的 JDK 安裝程式](update-jdk-images/image5.png)

Mac OS 將會自動設定新的 JDK 版本做為預設藉由更新**/System/Library/Frameworks/JavaVM.framework/Versions/Current**。 您可以接著請仔細檢查， **Java SDK (JDK)**位置會設定為預期的預設值是**libodbc**下**Visual Studio for Mac > 喜好設定 > 專案 > SDK 位置 >Android > Java SDK (JDK) > 位置**:

![在 [Android SDK 的位置] 頁面中設定 JDK 位置](update-jdk-images/image6.png)

-----

