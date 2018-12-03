---
title: Microsoft 的 Mobile OpenJDK 散發
description: 設定和疑難排解 Microsoft OpenJDK 的散發，以進行行動開發的逐步指南。
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: aad88ad883dea1e0430a047a71a2c191195efffe
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459898"
---
# <a name="microsofts-mobile-openjdk-distribution"></a>Microsoft 的 Mobile OpenJDK 散發

_本指南會描述切換至 OpenJDK 內部散發的步驟。此散發適用於行動開發。_

## <a name="overview"></a>總覽

從 Visual Studio 15.9 與 Visual Studio for Mac 7.7 開始，Visual Studio Tools for Xamarin 已從 Oracle 的 JDK 移轉至**僅適用於 Android 開發的輕量型 OpenJDK 版本**。 因為 Oracle 即將於 2019 年停止 JDK 8 商業散發的支援，但 JDK 8 是所有 Android 開發都需要的元件，所以這是有必要的移轉。

此移轉的優點包括：

- 您將一律擁有適用於 Android 開發的 OpenJDK 版本。

- 下載 Oracle JDK 9 或更新版本並不會影響開發體驗。

- 降低下載大小與磁碟使用量。

- 再也不需要處理第三方伺服器問題與安裝程式問題。

為讓您更快地移轉到改良的體驗，我們提供 Microsoft Mobile OpenJDK 散發的組建，供您在 Windows 與 Mac 上測試。 安裝程式如下所述，而且您可以隨時還原為 Oracle JDK。

## <a name="download"></a>下載

如果您在 Windows 上餘 Visual Studio 安裝程式中選取 Android SDK 套件，就會自動為您安裝 Mobile OpenJDK 散發。 散發會包含在對 15.9 的更新內，提供給選取 Android SDK 的使用者們。

在 Mac 上，會在新安裝的 Android 工作負載內一併安裝 Mobile OpenJDK。 若您是現有的 Visual Studio for Mac 使用者，將會收到提示，要求您在對 7.7 的更新中選擇加以安裝。 IDE 會提示您移至新的 JDK，並切換為在下次重新啟動時使用它。

## <a name="troubleshooting"></a>疑難排解

如果您在 Mac 或 Windows 上進行設定時遇到問題，可參考下列步驟進行手動設定：

檢查 OpenJDK 是否安裝在電腦上的正確位置：

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

如果此路徑為空白，您可以下載以下其中一個套件：

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

將 IDE 指向新的 JDK：

- **Mac** &ndash; 按一下 [工具] > [SDK 管理員] > [位置]，並將 [Java SDK (JDK) 的位置] 變更為完整的 OpenJDK 安裝路徑。 在下列範例中，此路徑設定為 **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**。

![在 Mac 上設定 Microsoft Mobile OpenJDK 散發的 JDK 路徑](openjdk-images/vsm.png)

- **Windows** &ndash; 按一下 [工具] > [選項] > [Xamarin] > [Android 設定]，並將 [Java 開發套件位置] 變更為完整的 OpenJDK 安裝路徑。 在下列範例中，此路徑設定為 **C:\\Program Files\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**：

![在 Windows 上設定 Microsoft Mobile OpenJDK 散發的 JDK 路徑](openjdk-images/vs.png)

## <a name="known-issues"></a>已知問題

### <a name="package-openjdkv1regkeyversion1809chipx64-failed-to-install"></a>無法安裝套件 'OpenJDKV1.RegKey,version=1.8.0.9,chip=x64'

這對某些公司環境來說可能是個問題。 OpenJDK 已經在電腦上，請遵循[上述的疑難排解步驟](#troubleshooting)將您的 IDE 指向正確位置。 您可在[此處](https://developercommunity.visualstudio.com/content/problem/382549/packageidopenjdkv1regkeypackageactioninstallreturn.html)追蹤問題的狀態。

### <a name="unknown-update-type-zip-when-upgrading-visual-studio-for-mac-to-77"></a>將 Visual Studio for Mac 升級至 7.7 時發生「未知的更新類型: zip」

從舊版 Visual Studio for Mac 升級至 7.7 時，可能會發生此問題。 因應措施：在更新程式中取消選取 [OpenJDK] 並安裝其餘更新，然後再次檢查更新來取得 OpenJDK 套件。 若問題仍未解決，您可以遵循[上述的疑難排解步驟](#troubleshooting)來手動安裝 OpenJDK。 如果您遇到此問題，請附上記錄將 Bug 歸檔。

## <a name="summary"></a>總結

您已在本文中學到如何設定 IDE 來使用 Microsoft 的 Mobile OpenJDK 散發，以及如何在遇到問題時進行疑難排解。
