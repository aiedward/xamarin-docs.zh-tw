---
title: "設定裝置以進行開發"
description: "本文章將討論如何設定 Android 裝置，並將它連接到電腦，以便裝置可用來執行和偵錯 Xamarin.Android 應用程式。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/21/2017
ms.openlocfilehash: 26a543cc096d96346cd4503bf4fa2382f8ca9b78
ms.sourcegitcommit: 8e722d72c5d1384889f70adb26c5675544897b1f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/15/2018
---
# <a name="set-up-device-for-development"></a>設定裝置以進行開發

_本文章將探討如何設定 Android 裝置，並將它連接到電腦，以便可以使用裝置來執行 Xamarin.Android 應用程式及對這些應用程式進行偵錯。_

現在，您可能已經看過您的絕佳新應用程式在 Android 模擬器上執行，而且想要看到它在閃亮的 Android 裝置上執行。 以下是將裝置連接到電腦以進行偵錯時涉及的步驟：

1.  **啟用裝置上的偵錯** - 預設無法在 Android 裝置上進行應用程式偵錯。

2.  **安裝 USB 驅動程式** - OS X 電腦不需要這個步驟。 Windows 電腦可能需要安裝 USB 驅動程式。

3.  **將裝置連接到電腦** - 最後一個步驟是透過 USB 或 WiFi 將裝置連接到電腦。

這些步驟每一個都會在下列各節中更詳細地說明。


## <a name="enable-debugging-on-the-device"></a>啟用裝置上的偵錯

您可使用任何 Android 裝置來測試 Android 應用程式。 不過，裝置必須先正確設定，才能進行偵錯。 所需的步驟會稍有不同，視裝置上執行的 Android 版本而定。


### <a name="android-40-to-android-41"></a>Android 4.0 到 Android 4.1

針對 Android 4.0.x 到 Android 4.1.x，偵錯的啟用步驟如下：

1.  移至 [設定] 畫面。
2.  選取 [開發人員選項]。
3.  取消核取 [USB 偵錯] 選項。

這個螢幕擷取畫面顯示執行 Android 4.0.3 的裝置上的 [開發人員選項] 畫面：

[![開發人員選項](set-up-device-for-development-images/developer-options-sml.png)](set-up-device-for-development-images/developer-options.png#lightbox)


### <a name="android-42-and-higher"></a>Android 4.2 和更新版本

從 Android 4.2 和更高版本開始，[開發人員選項] 預設為隱藏。 若要使用，請移至 [設定] > [關於手機]，然後點選 [版本號碼] 項目七次，以顯示 [開發人員選項] 索引標籤：

[![版本號碼項目](set-up-device-for-development-images/about-phone-sml.png)](set-up-device-for-development-images/about-phone.png#lightbox)

一旦在 [設定] > [系統] 下可使用 [開發人員選項] 索引標籤，請開啟以顯示開發人員設定：

[![開發人員設定畫面](set-up-device-for-development-images/developer3.png)](set-up-device-for-development-images/developer3.png#lightbox)

這是要啟用開發人員選項的地方，例如 USB 偵錯，以及保持清醒模式。


## <a name="install-usb-drivers"></a>安裝 USB 驅動程式

OS X 不需要這個步驟。只要用 USB 纜線將裝置連接到 Mac。

可能需要安裝某些額外的驅動程式，Windows 電腦才能辨識透過 USB 連接的 Android 裝置。

> [!NOTE]
> 這些是設定 Google Nexus 裝置的步驟，僅供參考。 特定裝置的步驟可能有所差異，但會遵循類似的模式。 如有任何問題，請在網際網路上搜尋您的裝置。

在 **[Android SDK 安裝路徑] \tools** 目錄執行 **android.bat** 應用程式。 Xamarin.Android 安裝程式預設會將 Android SDK 放在 Windows 電腦上的下列位置：

    C:\Users\[username]\AppData\Local\Android\android-sdk


### <a name="download-the-usb-drivers"></a>下載 USB 驅動程式

Google Nexus 裝置 (不包括 Galaxy Nexus) 需要 Google USB 驅動程式。 Galaxy Nexus 的驅動程式[由 Samsung 散佈](http://www.samsung.com/us/support/downloads/)。
所有其他 Android 裝置都應使用[來自其各自製造商的 USB 驅動程式](http://developer.android.com/tools/extras/oem-usb.html#Drivers)。

安裝 **Google USB 驅動程式**套件，方法是啟動 Android SDK 管理員，然後展開 [Extras] 資料夾，如下列螢幕擷取畫面中所見：

[![選取 Google USB 驅動程式套件](set-up-device-for-development-images/usbdriverpackage.png)](set-up-device-for-development-images/usbdriverpackage.png#lightbox)

核取 [Google USB 驅動程式] 方塊，然後按一下 [安裝] 按鈕。
驅動程式檔案會下載到下列位置：

    [Android SDK install path]\extras\google\usb\_driver

Xamarin.Android 安裝的預設路徑是：

    C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver



### <a name="installing-the-usb-driver"></a>安裝 USB 驅動程式

USB 驅動程式下載之後，就必須安裝它們。
在 Windows 7 上安裝驅動程式：

1.  使用 USB 纜線將裝置連接到電腦。

2.  從桌面或是 Windows 檔案總管以滑鼠右鍵按一下 [電腦]，然後選取 [管理]。

3.  選取左窗格的 [裝置]。

4.  在右窗格找出並展開 [其他裝置]。

5.  以滑鼠右鍵按一下裝置名稱，然後選取 [更新驅動程式軟體]。
    這會啟動 [硬體更新精靈]。

6.  選取 [瀏覽電腦上的驅動程式軟體] 然後按一下 [下一步]。

7.  按一下 [瀏覽] 並找出 USB 驅動程式資料夾 (Google USB 驅動程式位於 **[Android SDK 安裝路徑]\extras\google\usb_driver**)。

8.  按一下 [下一步] 以安裝驅動程式。


### <a name="installing-unverified-drivers-in-windows-8"></a>在 Windows 8 中安裝未驗證的驅動程式

若要在 Windows 8 中安裝未驗證的驅動程式，可能需要額外的步驟。 下列步驟描述如何安裝 Google Nexus 的驅動程式：

1.  **存取 Windows 8 進階開機選項** - 這個步驟包含重新啟動電腦以存取進階開機選項。 啟動命令列提示字元，並使用下列命令重新啟動電腦：

        shutdown.exe /r /o

2.  **連接裝置** - 將裝置連接到電腦

3.  **啟動裝置管理員** - 執行 **devmgmt.msc**；您應該會看到列出您的裝置，並且其上有黃色三角形。

4.  **安裝裝置驅動程式** - 安裝裝置驅動程式，如上面所述。



## <a name="connect-the-device-to-the-computer"></a>將裝置連接到電腦

最後一個步驟是將裝置連接到電腦。 執行此作業的方法有兩種：

-   **USB 纜線** - 這是最簡單且最常見的方法。 只要將 USB 纜線插入到裝置，然後插入到電腦。

-   **WiFi** - 使用 WiFi 可以將 Android 裝置連接到電腦，而不需使用 USB 纜線。 這項技術需要多一點工夫，但當沒有 USB 纜線或裝置遠離 USB 纜線時可能很有用。 下一節將討論透過 WiFi 連接。


### <a name="connecting-over-wifi"></a>透過 WiFi 連接

[Android Debug Bridge](http://developer.android.com/tools/help/adb.html) (*ADB*) 預設已設定為透過 USB 與 Android 裝置進行通訊。 可以重新設定為使用 TCP/IP，而不是 USB。 若要這樣做，裝置和電腦都必須在相同的 WiFi 網路上。 若要設定環境以透過 WiFi 進行偵錯，請從命令列發出下列步驟：

1.  判斷 Android 裝置的 IP 位址。 找出 IP 位址的其中一種方式，是到 [設定] > [Wi-Fi] 下，然後點選裝置連接到的 WiFi 網路。 這樣將會出現設定畫面，顯示網路連線的資訊，類似於以下螢幕擷取畫面中所見：

    ![IP 位址](set-up-device-for-development-images/ip-settings.png)

    在某些版本的 Android 上，IP 位址不會在列出該處，但可以改在 [設定] > [關於手機] > [狀態] 下找到。

2.  透過 USB 將 Android 裝置連接到您的電腦。

3.  接下來，重新啟動 ADB 以讓它在連接埠 5555 上使用 TCP。 從命令提示字元，輸入下列命令：

        adb tcpip 5555

    發出此命令之後，您的電腦將無法接聽透過 USB 連接的裝置。

4.  拔除將您的裝置連接到電腦的那條 USB 纜線。

5.  設定 ADB，讓它透過您在上述步驟 1 中所指定的連接埠連接到 Android 裝置：

        adb connect 192.168.1.28:5555

    此命令完成後，Android 裝置會透過 WiFi 連接到電腦。

當您完成透過 WiFi 進行偵錯時，可以使用下列命令將 ADB 重設回 USB 模式：

    adb usb

可以要求 ADB 列出連接到電腦的裝置。 不論裝置連接的方式為何，您都可以在命令提示字元發出下列命令以查看連接的裝置：

    adb devices


## <a name="summary"></a>總結

本文討論如何藉由啟用裝置上的偵錯，設定 Android 裝置以進行開發。 它也介紹如何使用 USB 或 WiFi 將裝置連接到電腦。


## <a name="related-links"></a>相關連結

- [Android Debug Bridge](http://developer.android.com/tools/help/adb.html) \(英文\)
- [使用硬體裝置](http://developer.android.com/tools/device.html) \(英文\)
- [Samsung 驅動程式下載](http://www.samsung.com/us/support/downloads/) \(英文\)
- [OEM USB 驅動程式](http://developer.android.com/tools/extras/oem-usb.html#Drivers) \(英文\)
- [Google USB Driver](http://developer.android.com/sdk/win-usb.html) \(英文\)
- [XDA 開發人員：Windows 8 - ADB/fastboot 驅動程式問題已解決](http://forum.xda-developers.com/showthread.php?t=1583801) \(英文\)
