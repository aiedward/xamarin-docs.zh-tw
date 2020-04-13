---
title: Microsoft 的 Mobile OpenJDK 散發
description: 設定和疑難排解 Microsoft OpenJDK 的散發，以進行行動開發的逐步指南。
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: a24edbc10d529878092b474df7f186d14049d5e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "60955069"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Microsoft 的 Mobile OpenJDK 散發

_本指南介紹了切換到 OpenJDK 內部分發的步驟。此分發用於移動開發。_

## <a name="overview"></a>概觀

從 Visual Studio 15.9 與 Visual Studio for Mac 7.7 開始，Visual Studio Tools for Xamarin 已從 Oracle 的 JDK 移轉至**僅適用於 Android 開發的輕量型 OpenJDK 版本**。 因為 Oracle 即將於 2019 年停止 JDK 8 商業散發的支援，但 JDK 8 是所有 Android 開發都需要的元件，所以這是有必要的移轉。

此移轉的優點包括：

- 您將一律擁有適用於 Android 開發的 OpenJDK 版本。

- 下載 Oracle JDK 9 或更新版本並不會影響開發體驗。

- 降低下載大小與磁碟使用量。

- 再也不需要處理第三方伺服器問題與安裝程式問題。

為讓您更快地移轉到改良的體驗，我們提供 Microsoft Mobile OpenJDK 散發的組建，供您在 Windows 與 Mac 上測試。 安裝程式如下所述，而且您可以隨時還原為 Oracle JDK。

## <a name="download"></a>下載

如果您在 Windows 上餘 Visual Studio 安裝程式中選取 Android SDK 套件，就會自動為您安裝 Mobile OpenJDK 散發。

在 Mac 上，會在新安裝的 Android 工作負載內一併安裝 Mobile OpenJDK。 若您是現有的 Visual Studio for Mac 使用者，就會提示您在更新中選擇加以安裝。 IDE 會提示您移至新的 JDK，並切換為在下次重新啟動時使用它。

## <a name="troubleshooting"></a>疑難排解

如果您在 Mac 或 Windows 上進行設定時遇到問題，可參考下列步驟進行手動設定：

檢查 OpenJDK 是否安裝在電腦上的正確位置：

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.x**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.x**

將 IDE 指向新的 JDK：

- **Mac** &ndash; 按一下 [工具] > [SDK 管理員] > [位置]****，並將 [Java SDK (JDK) 的位置]**** 變更為完整的 OpenJDK 安裝路徑。 在下列範例中，會將此路徑設為 **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**，但您的版本可能較新。

![在 Mac 上設定 Microsoft Mobile OpenJDK 散發的 JDK 路徑](openjdk-images/vsm.png)

- **Windows** &ndash; 按一下 [工具] > [選項] > [Xamarin] > [Android 設定]****，並將 [Java 開發套件位置]**** 變更為完整的 OpenJDK 安裝路徑。 在下列範例中，會將此路徑設為 **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**，但您的版本可能較新：

![在 Windows 上設定 Microsoft Mobile OpenJDK 散發的 JDK 路徑](openjdk-images/vs.png)

## <a name="known-issues"></a>已知問題

### <a name="package-openjdkv1regkeyversion18025chipx64-failed-to-install"></a>無法安裝套件 'OpenJDKV1.RegKey,version=1.8.0.25,chip=x64'

這對某些公司環境來說可能是個問題。 OpenJDK 已經在電腦上，請遵循[上述的疑難排解步驟](#troubleshooting)將您的 IDE 指向正確位置。 您可在[此處](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html)追蹤問題的狀態。

## <a name="summary"></a>總結

您已在本文中學到如何設定 IDE 來使用 Microsoft 的 Mobile OpenJDK 散發，以及如何在遇到問題時進行疑難排解。
