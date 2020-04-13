---
title: 設定裝置以進行開發
description: 本文章將討論如何設定 Android 裝置，並將它連接到電腦，以便裝置可用來執行和偵錯 Xamarin.Android 應用程式。
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 72e0a2adc79796b3df7b6fb4eca62448f1a1a7a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510727"
---
# <a name="set-up-device-for-development"></a>設定裝置以進行開發

_本文說明如何設定 Android 裝置並將其連線至電腦，以便使用該裝置來執行 Xamarin.Android 應用程式並對這些應用程式進行偵錯。_

在 Android 模擬器上測試後，建議您在 Android 裝置上查看並測試您的應用程式。 您需要啟用除錯並將裝置連接到電腦。

這些步驟每一個都會在下列各節中更詳細地說明。

## <a name="enable-debugging-on-the-device"></a>啟用裝置上的偵錯

必須啟用設備進行調試才能測試 Android 應用程式。 自版本 4.2 以來,Android 上的開發人員選項一直隱藏,啟用它們可能因 Android 版本而異。

### <a name="android-90"></a>安卓 9.0+

對於 Android 9.0 及更高版本,調試通過以下步驟啟用:

1. 移至 [設定]**** 畫面。
2. 選擇 **「關於電話**」。。
3. 點擊 **'產生次數**'7次,**直到您現在是開發人員!** 可見。

### <a name="android-80-and-android-81"></a>安卓 8.0 和安卓 8.1

1. 轉到 **"設置"** 螢幕。
2. 選擇**系統**。
3. 選擇 **"關於電話"**
4. 點擊 **'產生次數**'7次,**直到您現在是開發人員!** 可見。

### <a name="android-71-and-lower"></a>安卓 7.1 和更低

1. 轉到 **"設置"** 螢幕。
2. 選擇 **「關於電話**」。。
3. 點擊 **'產生次數**'7次,**直到您現在是開發人員!** 可見。

[![Android 9.0 上的開發人員選項螢幕](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>驗證 USB 除錯是否已開啟

在設備上啟用開發人員模式後,必須確保在設備上啟用 USB 調試。 這也因Android版本而異。

### <a name="android-90"></a>安卓 9.0+

導覽到**設定>系統>進階>開發人員選項**,並啟用**USB 除錯**。

### <a name="android-80-and-android-81"></a>安卓 8.0 和安卓 8.1

導覽到**設定>系統>開發人員選項**,並啟用**USB 除錯**。

### <a name="android-71-and-lower"></a>安卓 7.1 和更低

導覽**到 「設定>開發人員選項**,並啟用**USB 除錯**。

一旦在 [設定] > [系統]**** 下可使用 [開發人員選項]**** 索引標籤，請開啟以顯示開發人員設定：

[![Android 9.0 上的開發人員選項螢幕](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

這是要啟用開發人員選項的地方，例如 USB 偵錯，以及保持清醒模式。

## <a name="connect-the-device-to-the-computer"></a>將裝置連線到電腦

最後一個步驟是將裝置連接到電腦。 最簡單、最可靠的方法是通過 USB 執行此操作。

如果您以前沒有將其用於調試,您將收到一條提示,用於信任設備上的計算機。 您還可以選擇 **「始終允許」**, 以防止每次連線裝置時都需要此提示。

![](set-up-device-for-development-images/trust-computer-for-usb-debugging.png "Google USB")

## <a name="alternate-connection-via-wifi"></a>透過 Wifi 進行備用連線

無需使用 USB 電纜即可透過 WiFi 將 Android 裝置連接到電腦。 此技術需要付出更多努力,但當設備離電腦太遠,無法通過電纜持續插入時,該技術可能很有用。 

### <a name="connecting-over-wifi"></a>透過 WiFi 連接

[Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) 預設已設定為透過 USB 與 Android 裝置進行通訊。 可以將其重新設定為使用 TCP/IP，而不是 USB。 若要這樣做，裝置和電腦都必須在相同的 WiFi 網路上。 要設定環境以透過 WiFi 進行除錯,請完成命令列中的以下步驟:

1. 判斷 Android 裝置的 IP 位址。 找出 IP 位址的方法是檢視 **「設定 >网络&網際網路> Wi-Fi」下的設定網路**,然後點擊裝置連接到的 WiFi 網路,然後點擊**進階**。 這將開啟下拉清單,顯示有關網路連接的資訊,類似於下面的螢幕截圖中所示:

    [![IP 位址](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    在某些版本的Android上,IP位址不會在那裡列出,但可以在 **"關於手機>狀態的設置>** 找到。

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

    完成此命令後,Android 設備將通過WiFi連接到電腦。

    以 WiFi 完成除錯後,可以使用以下指令將 ADB 重置回 USB 模式:
    
    ```command
    adb usb
    ```
    
    可以請求亞行列出連接到計算機的設備。 不論裝置連接的方式為何，您都可以在命令提示字元發出下列命令以查看連接的裝置：
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>疑難排解

在某些情況下,您可能會發現您的裝置無法連接到電腦。 在這種情況下,您可能需要驗證是否安裝了 USB 驅動程式。

## <a name="install-usb-drivers"></a>安裝 USB 驅動程式

此步驟對於 macOS 來說不是必需的;因此,此步驟不需要。只需使用 USB 電纜將設備連接到 Mac。

可能需要安裝某些額外的驅動程式，Windows 電腦才能辨識透過 USB 連接的 Android 裝置。

> [!NOTE]
> 這些是設定 Google Nexus 裝置的步驟，僅供參考。 特定裝置的步驟可能有所差異，但會遵循類似的模式。 如有任何問題，請在網際網路上搜尋您的裝置。

在 **[Android SDK 安裝路徑] \tools** 目錄執行 **android.bat** 應用程式。 Xamarin.Android 安裝程式預設會將 Android SDK 放在 Windows 電腦上的下列位置：

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>下載 USB 驅動程式

Google Nexus 裝置 (不包括 Galaxy Nexus) 需要 Google USB 驅動程式。 Galaxy Nexus 的驅動程式[由 Samsung 散佈](https://www.samsung.com/us/support/downloads/)。
所有其他 Android 裝置都應使用[來自其各自製造商的 USB 驅動程式](https://developer.android.com/tools/extras/oem-usb.html#Drivers)。

安裝 **Google USB 驅動程式**套件，方法是啟動 Android SDK 管理員，然後展開 [Extras]**** 資料夾，如下列螢幕擷取畫面中所見：

![](set-up-device-for-development-images/google-usb-driver.png "Google USB driver selected")

選中**Google USB 驅動程式**框,然後按下「**應用更改」** 按鈕。
驅動程式檔案會下載到下列位置：

`[Android SDK install path]\extras\google\usb\_driver`

Xamarin.Android 安裝的預設路徑是：

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>安裝 USB 驅動程式

USB 驅動程式下載之後，就必須安裝它們。
在 Windows 7 上安裝驅動程式：

1. 使用 USB 纜線將裝置連接到電腦。

2. 右鍵按下桌面或 Windows 資源管理員中的電腦,然後選擇 **「管理**」。

3. 在左側窗格中，選取 [裝置]****。

4. 在右窗格找出並展開 [其他裝置]****。

5. 以滑鼠右鍵按一下裝置名稱，然後選取 [更新驅動程式軟體]****。
    這會啟動 [硬體更新精靈]。

6. 選擇 **「瀏覽我的電腦以取得驅動程式軟體**」,然後按下「**下一步**」 。

7. 按下 **「瀏覽」** 並找到 USB 驅動程式資料夾(Google USB 驅動程式位於 **[Android SDK 安裝路徑][額外\google]usb_driver)。**

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
