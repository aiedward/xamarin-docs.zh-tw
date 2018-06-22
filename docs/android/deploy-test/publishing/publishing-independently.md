---
title: 獨立發行
ms.prod: xamarin
ms.assetid: 6FB4DEF2-01AD-C5FE-0950-CE1BF088A9C6
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/21/2017
ms.openlocfilehash: f7ba0620a4639ff62e2d75d7cf8f02fcc01faac5
ms.sourcegitcommit: c9ebf456e1c6924956bedb13f4ea78ff09f7b1a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/04/2018
ms.locfileid: "33113469"
---
# <a name="publishing-independently"></a>獨立發行

您可以在不使用任何現有 Android 市集的情況下發行應用程式。 本節將說明這些其他發行方法，以及 Xamarin.Android 的授權層級。


## <a name="xamarin-licensing"></a>Xamarin 授權

有四個可用於 Xamarin.Android 應用程式之開發、部署及散發的授權：

-   **Visual Studio Community** &ndash; 適用於使用 Windows 的學生、小型團隊及 OSS 開發人員。

-   **Visual Studio Professional** &ndash; 適用於個人開發人員或小型團隊 (僅限 Windows)。 此授權會提供標準或雲端訂用帳戶、額外 Xamarin University 內容的存取權，且沒有任何使用限制。

-   **Visual Studio Enterprise** &ndash; 適用於任何規模的團隊 (僅限 Windows)。 此授權包含企業功能、標準或雲端訂用帳戶。

請瀏覽 [visualstudio.com](https://www.visualstudio.com/xamarin/) 來下載 Community 版，或深入了解如何購買 Professional 和 Enterprise 版。


## <a name="allow-installation-from-unknown-sources"></a>允許從未知來源進行安裝

Android 會防止使用者下載及安裝來自 Google Play.以外位置的應用程式。 若要允許從非市集來源進行安裝，使用者必須先在裝置上啟用 [不明的來源] 設定，才能嘗試安裝應用程式。 這項設定可能位於 [設定] > [系統安全] 底下，如下圖所示：

[![](publishing-independently-images/settings.png)](publishing-independently-images/settings.png#lightbox)


> [!IMPORTANT]
> 有些網路提供者可能會防止安裝來自不明來源的應用程式，不論此設定為何。



## <a name="publishing-by-e-mail"></a>透過電子郵件發行

將發行 APK 附加在電子郵件中，是一個將應用程式散發給使用者的快速便利方式。 當使用者在 Android 架構裝置上開啟該電子郵件時，Android 會辨識出該 APK 附件並顯示 [安裝] 按鈕，如下圖所示：

[![附件的 [安裝] 按鈕](publishing-independently-images/publishing-via-email.png)](publishing-independently-images/publishing-via-email.png#lightbox)

雖然透過電子郵件散發相當簡單，但在防範盜版或未經授權散發方面所能提供的防護相當少。 在應用程式的接收者很少，且受信任不會散發該應用程式的情況下，最適合使用此散發方式。


## <a name="publishing-by-web"></a>透過 Web 發行

您可以透過 Web 伺服器散發應用程式。 做法是將應用程式上傳到 Web 伺服器，然後將下載連結提供給使用者。 當 Android 架構裝置瀏覽至連結，然後下載應用程式時，該應用程式會在下載完成時自動安裝。


## <a name="manually-installing-an-apk"></a>手動安裝 APK

手動安裝是安裝應用程式的第三個選項。 手動安裝應用程式：

1.   **將 APK 複本散發給使用者** &ndash; 例如，可以透過 CD 或 USB 快閃磁碟機散發此複本。
1.   **(使用者) 將應用程式安裝在 Android 裝置上**  &ndash; 使用命令列 *Android Debug Bridge* (**adb**) 工具。 **adb** 是一個多功能的命令列工具，可啟用與模擬器執行個體或 Android 架構裝置的通訊。 Android SDK 包含 **adb**；您可以在 **<sdk>/platform-tools/** 目錄中找到此工具。

Android 裝置必須透過 USB 纜線，才能連接至電腦。
Windows 可能也需要手機廠商所提供的額外 USB 驅動程式，才可供 **adb** 辨識。 這些額外 USB 驅動程式的安裝指示不在本文件的討論範圍內。

在發出任何 **adb** 命令之前，知道已連接哪些模擬器執行個體或裝置 (如果有的話) 會相當有幫助。 您可以使用 `devices` 命令來查看所連接裝置的清單，如以下程式碼片段所示範：

```shell
$ adb devices
List of devices attached
        0149B2EC03012005device
```

確認所連接的裝置之後，便可使用 **adb** 來發出 `install` 命令：

```shell
$ adb install <path-to-apk>
```

以下程式碼片段說明將應用程式安裝至所連接裝置的範例：

```shell
$ adb install helloworld.apk
3772 KB/s (3013594 bytes in 0.780s)
        pkg: /data/local/tmp/helloworld.apk
Success
```

如果已經安裝應用程式，則無法使用 `adb install` 來安裝 APK 而將會回報失敗，如以下範例所示：

```shell
$ adb install helloworld.apk
4037 KB/s (3013594 bytes in 0.728s)
        pkg: /data/local/tmp/helloworld.apk
Failure [INSTALL_FAILED_ALREADY_EXISTS]
```

您將需要將應用程式自裝置中解除安裝。 首先，請發出 `adb uninstall` 命令：

```shell
adb uninstall <package_name>
```

以下程式碼片段是將應用程式解除安裝的範例：

```shell
$ adb uninstall mono.samples.helloworld
Success
```
