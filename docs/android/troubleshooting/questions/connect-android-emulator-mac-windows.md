---
title: 從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 49d1eea60f766f4cb61484a6e441506cf8f046ff
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291562"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？

要連接到從 Windows 虛擬機器在 Mac 上執行的 Android 模擬程式,請使用以下步驟:

1. 在 Mac 上啟動模擬器。

2. 在`adb`Mac 上殺死伺服器:

    ```bash
    adb kill-server
    ```

3. 請注意,模擬器正在迴圈回網介面上的 2 個 TCP 連接埠上偵聽:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇數埠是用於連接到`adb`的埠。 另請參閱[https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4. _選項 1:_ 用於`nc`將連接埠 5555(或您喜歡的任何其他埠)上外部接收的入站 TCP 資料包轉發到環回介面上的奇數埠(本例中**為 127.0.0.1 5555),** 並另行將出站數據包轉發:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要`nc`命令在終端視窗中保持運行,數據包將按預期轉發。 您可以在終端視窗中鍵入 Control-C,在使用模擬器`nc`完成後退出命令。

    (選項 1 通常比選項 2 更容易,尤其是在**打開防火牆>防火牆>安全&隱私**時。

    _選項 2_ `pfctl` : 用於將`127.0.0.1:5555`[分享網路](https://kb.parallels.com/en/4948)介面上的連接埠`5555`(或您喜歡的任何其他埠)重定向到環回介面上的奇數連接埠(請顯示例中):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令使用`pf packet filter`系統服務設置埠轉發。 換行符很重要。 複製粘貼時,請確保保持其完整。 如果使用 Parallels,還需要從*vmnet8*調整介面名稱。 `vmnet8`是 VMWare 融合中*共用網路*模式的特殊*NAT 設備*的名稱。 並行中的相應網路介面可能是[vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5. 從 Windows 電腦連接到模擬器:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    將「mac ip 位址」替換為 Mac 的`ifconfig vmnet8 | grep 'inet '`IP 位址,例如, 如果需要,`5555`請取代為步驟 4 中其他喜歡的連接埠\. (注意:獲得命令列存取的一種`adb`方法 是透過[**工具>Android>Android在視覺工作室中的Android Adb命令提示**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。

### <a name="alternate-technique-using-ssh"></a>使用備用技術`ssh`

如果您在 Mac 上啟用了_遠端登錄_`ssh`,則可以使用連接埠轉接連接到模擬器。

1. 在 Windows 上安裝 SSH 用戶端。 選項是安裝[Git 的 Windows](https://git-for-windows.github.io/)。 然後`ssh`,該命令將在 Git **Bash**命令提示符中可用。

2. 按照上面的步驟 1-3 啟動模擬器,在`adb`Mac 上 殺死伺服器,並標識模擬器埠。

3. 在`ssh`Windows 上執行以在 Windows 上的`localhost:15555`本地端埠( 在此範例) 和 Mac 回環介面上的奇數模擬器連接埠之間設定`127.0.0.1:5555`雙向連接埠轉送( 請您範例中為):

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    替換為`mac-username`您列`whoami`出的 Mac 使用者名稱。 替換為`ip-address-of-the-mac`Mac 的 IP 位址。

4. 使用 Windows 上的本地埠連接到模擬器:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (注意:獲得命令行訪問的`adb`一個簡單方法是通過[**工具>Android>Android Adb命令提示在**視覺工作室](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。)

一個小警告:如果您對本地埠`5555`使用連接埠,`adb`則認為模擬器在 Windows 上本地運行。 這不會在 Visual Studio 中造成任何麻煩,但在 Mac 的 Visual Studio 中,它會導致應用程式在啟動後立即退出。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>不支援使用`adb -H`的替代技術

從理論上講,另一種方法是使用`adb`內置功能連接到在遠端電腦上運行`adb`的 伺服器[https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)(例如 ,請參閱 )。
但是 Xamarin.Android IDE 擴展目前無法提供配置該選項的方法。

## <a name="contact-information"></a>連絡人資訊

本文件討論截至 2016 年 3 月的當前行為。 本文檔中描述的技術不是 Xamarin 穩定測試套件的一部分,因此將來可能會中斷。

如果您注意到此技術不再有效,或者您注意到文件中的任何其他錯誤,請隨時新增到以下論壇主題的討論: [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
感謝您！
