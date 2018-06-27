---
title: 模擬器設定問題疑難排解
description: 本文說明如何診斷和因應使用 Android Device Manager 時可能發生的問題。
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/01/2018
ms.openlocfilehash: 4cbd7d7626d114856d6c68fe7bc9feb7c2a5abc2
ms.sourcegitcommit: a7febc19102209b21e0696256c324f366faa444e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2018
ms.locfileid: "34733418"
---
# <a name="troubleshooting-emulator-setup-problems"></a>模擬器設定問題疑難排解

_本文說明如何診斷和因應使用 Android Device Manager 設定 Android 模擬器時可能發生的問題。如需在執行 Android 模擬器時疑難排解問題的相關資訊，請參閱[Google Android 模擬器疑難排解](~/android/deploy-test/debugging/android-sdk-emulator/troubleshooting.md)。_

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="android-sdk-in-non-standard-location"></a>Android SDK 不在標準位置上

Android SDK 通常會安裝於下列位置：

**C:\\Program Files (x86)\\Android\\android-sdk**

如果 SDK 不是安裝在此位置，您在啟動 Android Device Manager 時可能會收到這個錯誤：

![Android SDK 執行個體錯誤](troubleshooting-images/win/01-sdk-error.png)

若要解決這個問題，請執行下列動作：

1. 從 Windows 桌面，瀏覽至 **C:\\Users\\*username*\\AppData\\Roaming\\XamarinDeviceManager**：

    ![Android Device Manager 記錄檔位置](troubleshooting-images/win/02-log-files.png)

2. 按兩下以開啟其中一個記錄檔，並找出 **Config file path**。 例如: 

    [![記錄檔中的 Config file path](troubleshooting-images/win/03-config-file-path-sml.png)](troubleshooting-images/win/03-config-file-path.png#lightbox)

3. 瀏覽至此位置，然後按兩下 **user.config** 加以開啟。 

4. 在 **user.config** 中，找出 **&lt;UserSettings&gt;** 元素，並在其中新增 **AndroidSdkPath** 屬性。 將此屬性設定為電腦上安裝 Android SDK 的路徑，並儲存檔案。 例如，如果 Android SDK 安裝於 **C:\\Programs\\Android\\SDK**，則 **&lt;UserSettings&gt;** 看起來如下：
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

完成對 **user.config** 的變更後，您應該就能啟動 Android Device Manager。

## <a name="snapshot-disables-wifi-on-android-oreo"></a>Android Oreo 上的快照功能會停用 WiFi

如果您有針對具有模擬 Wi-Fi 存取的 Android Oreo 設定 AVD，在使用快照功能後重新啟動 AVD 可能會造成 Wi-Fi 存取被停用。

若要解決這個問題：

1. 在 Android Device Manager 中選取 AVD。

2. 在其他選項功能表中，按一下 [在檔案總管中顯示]。

3. 瀏覽至 [快照] > [default_boot]。

4. 刪除 **snapshot.pb** 檔案：

    ![snapshot.pb 檔案的位置](troubleshooting-images/win/05-delete-snapshot.png)

5. 重新啟動 AVD。 

做出這些變更之後，AVD 將會重新啟動為允許 Wi-Fi 再次運作的狀態。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="snapshot-disables-wifi-on-android-oreo"></a>Android Oreo 上的快照功能會停用 WiFi

如果您有針對具有模擬 Wi-Fi 存取的 Android Oreo 設定 AVD，在使用快照功能後重新啟動 AVD 可能會造成 Wi-Fi 存取被停用。

若要解決這個問題：

1. 在 Android Device Manager 中選取 AVD。

2. 在其他選項功能表中，按一下 [在尋找工具中顯示]。

3. 瀏覽至 [快照] > [default_boot]。

4. 刪除 **snapshot.pb** 檔案：

    [![snapshot.pb 檔案的位置](troubleshooting-images/mac/02-delete-snapshot-sml.png)](troubleshooting-images/mac/02-delete-snapshot.png#lightbox)

5. 重新啟動 AVD。 

做出這些變更之後，AVD 將會重新啟動為允許 Wi-Fi 再次運作的狀態。


-----

## <a name="generating-a-bug-report"></a>產生問題報告

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

如果您發現 Android Device Manager 的問題且無法使用上述疑難排解提示來解決，請以滑鼠右鍵按一下標題列，然後選取 [產生 Bug 報告] 來提出問題報告：

[![建檔 Bug 報告的功能表項目位置](troubleshooting-images/win/04-bug-report-sml.png)](troubleshooting-images/win/04-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果您發現 Android Device Manager 的問題且無法使用上述疑難排解提示來解決，請按一下 [說明] > [產生問題報告] 來提出問題報告：

[![建檔 Bug 報告的功能表項目位置](troubleshooting-images/mac/01-bug-report-sml.png)](troubleshooting-images/mac/01-bug-report.png#lightbox)

-----
