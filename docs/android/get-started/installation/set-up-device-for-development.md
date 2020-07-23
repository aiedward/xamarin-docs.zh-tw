---
title: 設定裝置以進行開發
description: 本文章將討論如何設定 Android 裝置，並將它連接到電腦，以便裝置可用來執行和偵錯 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 0b0bfc650ffa271a7616d7c6e6a436fafa2664c8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86932596"
---
# <a name="set-up-device-for-development"></a>設定裝置以進行開發

_本文說明如何設定 Android 裝置並將其連線至電腦，以便使用該裝置來執行 Xamarin.Android 應用程式並對這些應用程式進行偵錯。_

在 Android 模擬器上測試後，建議您在 Android 裝置上查看並測試您的應用程式。 您必須啟用 [偵測]，並將裝置連線到電腦。

這些步驟每一個都會在下列各節中更詳細地說明。

## <a name="enable-debugging-on-the-device"></a>啟用裝置上的偵錯

必須啟用裝置的偵錯工具，才能測試 Android 應用程式。 自4.2 版起，預設會隱藏 Android 上的開發人員選項，而且根據 Android 版本而定，啟用它們會有所不同。

### <a name="android-90"></a>Android 9.0 +

若為 Android 9.0 和更新版本，請遵循下列步驟來啟用偵錯工具：

1. 移至 [設定]**** 畫面。
2. 選取 [**關於電話**]。
3. 請使用**組建編號**7 次 **，直到您現在成為開發人員！** 為可見。

### <a name="android-80-and-android-81"></a>Android 8.0 和 Android 8。1

1. 移至 [**設定**] 畫面。
2. 選取 [**系統**]。
3. 選取 [**關於電話**]
4. 請使用**組建編號**7 次 **，直到您現在成為開發人員！** 為可見。

### <a name="android-71-and-lower"></a>Android 7.1 和更低版本

1. 移至 [**設定**] 畫面。
2. 選取 [**關於電話**]。
3. 請使用**組建編號**7 次 **，直到您現在成為開發人員！** 為可見。

[![Android 9.0 上的開發人員選項畫面](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>確認已啟用 USB 偵錯工具

在您的裝置上啟用開發人員模式之後，您必須確定裝置上已啟用 USB 偵錯工具。 這也會根據 Android 版本而有所不同。

### <a name="android-90"></a>Android 9.0 +

流覽至 [**設定] > [系統 > Advanced > 開發人員選項**]，並啟用 [ **USB 調試**]。

### <a name="android-80-and-android-81"></a>Android 8.0 和 Android 8。1

流覽至 [**設定] > [系統 > 開發人員選項**]，並啟用 [ **USB 調試**]。

### <a name="android-71-and-lower"></a>Android 7.1 和更低版本

流覽至 [**設定] > [開發人員選項**]，並啟用 [ **USB 調試**]。

一旦在 [設定] > [系統]**** 下可使用 [開發人員選項]**** 索引標籤，請開啟以顯示開發人員設定：

[![Android 9.0 上的開發人員選項畫面](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

這是要啟用開發人員選項的地方，例如 USB 偵錯，以及保持清醒模式。

## <a name="connect-the-device-to-the-computer"></a>將裝置連接到電腦

最後一個步驟是將裝置連接到電腦。 最簡單且最可靠的方法是透過 USB 來執行此動作。

如果您之前未使用電腦來進行前的偵測，將會收到提示，讓您信任裝置上的電腦。 您也可以在每次連線裝置時，核取 [從此**電腦一律允許**]，以避免要求此提示。

![Google USB](set-up-device-for-development-images/trust-computer-for-usb-debugging.png)

## <a name="alternate-connection-via-wifi"></a>透過 Wifi 的替代連接

您可以透過 WiFi 將 Android 裝置連接到電腦，而不需要使用 USB 纜線。 這項技術需要更多工作，但當裝置太遠而無法透過纜線持續插入時，可能會很有用。 

### <a name="connecting-over-wifi"></a>透過 WiFi 連接

[Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) 預設已設定為透過 USB 與 Android 裝置進行通訊。 可以將其重新設定為使用 TCP/IP，而不是 USB。 若要這樣做，裝置和電腦都必須在相同的 WiFi 網路上。 若要設定您的環境以透過 WiFi 進行的偵錯工具，請從命令列完成下列步驟：

1. 判斷 Android 裝置的 IP 位址。 找出 IP 位址的方法之一，就是在**Network & internet > wi-fi 的 [> 設定**] 底下尋找，然後在裝置所連線的 WiFi 網路上按一下，然後按一下 [ **Advanced**]。 這會開啟一個下拉式清單，顯示網路連線的相關資訊，類似于以下螢幕擷取畫面所示：

    [![IP 位址](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    在某些版本的 Android 上，IP 位址不會列在該處，但可以在 [**設定] > [關於電話 > 狀態**] 下找到。

2. 透過 USB 將 Android 裝置連接到您的電腦。

3. 接下來，重新啟動 ADB 以讓它在連接埠 5555 上使用 TCP。 從命令提示字元，輸入下列命令：

    ```command
    adb tcpip 5555
    ```

    發出此命令之後，您的電腦將無法接聽透過 USB 連接的裝置。

4. 拔除將您的裝置連接到電腦的那條 USB 纜線。

5. 設定 ADB，讓它透過您在上述步驟 1 中所指定的連接埠連接到 Android 裝置：

    ```command
    adb connect 192.168.1.28:5555
    ```

    此命令完成之後，Android 裝置會透過 WiFi 連線到電腦。

    當您透過 WiFi 完成偵錯工具時，可以使用下列命令將 ADB 重設回 USB 模式：
    
    ```command
    adb usb
    ```
    
    您可以要求 ADB 列出已連線到電腦的裝置。 不論裝置連接的方式為何，您都可以在命令提示字元發出下列命令以查看連接的裝置：
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>疑難排解

在某些情況下，您可能會發現您的裝置無法連接到電腦。 在此情況下，您可能會想要確認是否已安裝 USB 驅動程式。

## <a name="install-usb-drivers"></a>安裝 USB 驅動程式

MacOS 不需要這個步驟;只要使用 USB 纜線將裝置連接到 Mac 即可。

可能需要安裝某些額外的驅動程式，Windows 電腦才能辨識透過 USB 連接的 Android 裝置。

> [!NOTE]
> 這些是設定 Google Nexus 裝置的步驟，僅供參考。 特定裝置的步驟可能有所差異，但會遵循類似的模式。 如有任何問題，請在網際網路上搜尋您的裝置。

在 **[Android SDK 安裝路徑] \tools** 目錄執行 **android.bat** 應用程式。 Xamarin.Android 安裝程式預設會將 Android SDK 放在 Windows 電腦上的下列位置：

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>下載 USB 驅動程式

Google Nexus 裝置 (不包括 Galaxy Nexus) 需要 Google USB 驅動程式。 Galaxy Nexus 的驅動程式[由 Samsung 散佈](https://www.samsung.com/us/support/downloads/)。
所有其他 Android 裝置都應使用[來自其各自製造商的 USB 驅動程式](https://developer.android.com/tools/extras/oem-usb.html#Drivers)。

安裝 **Google USB 驅動程式**套件，方法是啟動 Android SDK 管理員，然後展開 [Extras]**** 資料夾，如下列螢幕擷取畫面中所見：

![已選取 Google USB 驅動程式](set-up-device-for-development-images/google-usb-driver.png)

勾選 [ **GOOGLE USB 驅動程式**] 方塊，然後按一下 [套用**變更**] 按鈕。
驅動程式檔案會下載到下列位置：

`[Android SDK install path]\extras\google\usb\_driver`

Xamarin.Android 安裝的預設路徑是：

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>安裝 USB 驅動程式

USB 驅動程式下載之後，就必須安裝它們。
在 Windows 7 上安裝驅動程式：

1. 使用 USB 纜線將裝置連接到電腦。

2. 在桌面或 Windows Explorer 中的電腦上按一下滑鼠右鍵，然後選取 [**管理**]。

3. 在左側窗格中，選取 [裝置]  。

4. 在右窗格找出並展開 [其他裝置]****。

5. 以滑鼠右鍵按一下裝置名稱，然後選取 [更新驅動程式軟體]****。
    這會啟動 [硬體更新精靈]。

6. 選取 **[流覽電腦上的驅動程式軟體]** ，然後按 **[下一步]** 。

7. 按一下 [**流覽]** 並找出 USB 驅動程式資料夾（Google USB 驅動程式位於 **[Android SDK 安裝路徑] \extras\google\ usb_driver**）。

8. 按一下 [下一步]**** 以安裝驅動程式。

## <a name="summary"></a>總結

本文討論如何藉由啟用裝置上的偵錯，設定 Android 裝置以進行開發。 它也介紹如何使用 USB 或 WiFi 將裝置連接到電腦。

## <a name="related-links"></a>相關連結

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [使用硬體裝置](https://developer.android.com/tools/device.html) \(英文\)
- [Samsung 驅動程式下載](https://www.samsung.com/us/support/downloads/) \(英文\)
- [OEM USB 驅動程式](https://developer.android.com/tools/extras/oem-usb.html#Drivers) \(英文\)
- [Google USB Driver](https://developer.android.com/sdk/win-usb.html) \(英文\)
- [XDA 開發人員：Windows 8 - ADB/fastboot 驅動程式問題已解決](https://forum.xda-developers.com/showthread.php?t=1583801) \(英文\)
