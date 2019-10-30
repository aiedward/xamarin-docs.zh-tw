---
title: 從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 2c1f571efb9ec3fb726912eb1e30496bc51fe26e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026992"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？

若要從 Windows 虛擬機器連接到在 Mac 上執行的 Android Emulator，請使用下列步驟：

1. 在 Mac 上啟動模擬器。

2. 終止 Mac 上的 `adb` 伺服器：

    ```bash
    adb kill-server
    ```

3. 請注意，模擬器會接聽回送網路介面上的2個 TCP 通訊埠：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇數通訊埠是用來連接到 `adb`。 另請參閱[https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4. _選項 1_：使用[`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    將埠5555（或您想要的任何其他埠）外部接收的輸入 TCP 封包轉送到回送介面上的奇數埠（在此範例中為**127.0.0.1 5555** ），並將輸出封包轉送回另一種方式：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要 `nc` 命令會在終端機視窗中保持執行狀態，封包就會如預期般轉送。 您可以在終端機視窗中輸入 Control-C，以在使用模擬器完成後結束 `nc` 命令。

    （選項1通常比選項2更簡單，特別是如果**系統喜好設定 > 安全性 & 隱私權 > 防火牆**已開啟）。 

    _選項 2_：使用[`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    若要將 TCP 封包從埠 `5555` （或您[喜歡的任何](https://kb.parallels.com/en/4948)其他埠）重新導向至回送介面上的奇數埠（在此範例中為`127.0.0.1:5555`）：

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令會使用 `pf packet filter` 系統服務來設定埠轉送。 分行符號很重要。 複製貼入時，請務必保持不變。 如果您使用的是「平行」，您也必須從*vmnet8*調整介面名稱。 `vmnet8` 是 VMWare 融合中*共用網路*模式的特殊*NAT 裝置*名稱。 類似的網路介面可能[vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5. 從 Windows 機器連接到模擬器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    以 Mac 的 IP 位址取代「ip-位址-mac」，例如，如 `ifconfig vmnet8 | grep 'inet '`所列出。 如有需要，請將 `5555` 取代為您在步驟4中所需的其他埠\. （注意：取得 `adb` 命令列存取的一種方式是透過 Visual Studio 中的 [[**工具] > android > Android Adb 命令提示**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)字元）。

### <a name="alternate-technique-using-ssh"></a>使用 `ssh` 的替代技術

如果您已在 Mac 上啟用_遠端登入_，則可以使用 `ssh` 埠轉送來連接至模擬器。

1. 在 Windows 上安裝 SSH 用戶端。 其中一個選項是安裝[Git For Windows](https://git-for-windows.github.io/)。 然後，`ssh` 命令會在**Git Bash**命令提示字元中提供。

2. 遵循上述步驟1-3 來啟動模擬器、終止 Mac 上的 `adb` 伺服器，以及識別模擬器埠。

3. 在 Windows 上執行 `ssh`，以在 Windows 上的本機埠（在此範例中為`localhost:15555`）與 Mac 回送介面上的奇數模擬器埠（在此範例中為`127.0.0.1:5555`）上設定雙向埠轉送：

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    以 `whoami`列出的 Mac 使用者名稱取代 `mac-username`。 將 `ip-address-of-the-mac` 取代為 Mac 的 IP 位址。

4. 使用 Windows 上的本機埠連接到模擬器：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    （注意：取得 `adb` 命令列存取的簡單方式是透過[Visual Studio 中的**工具 > Android > Android Adb 命令提示**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)字元）。

請注意：如果您對本機埠使用通訊埠 `5555`，`adb` 會認為模擬器是在 Windows 本機上執行。 這不會造成 Visual Studio 中的任何問題，但在 Visual Studio for Mac 會導致應用程式在啟動後立即結束。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>尚不支援使用 `adb -H` 的替代技術

理論上，另一種方法是使用 `adb`的內建功能，連接到在遠端電腦上執行的 `adb` 伺服器（請參閱例如[https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)）。
但 Xamarin Android IDE 擴充功能目前並未提供設定該選項的方法。

## <a name="contact-information"></a>連絡人資訊

本檔討論2016年3月的目前行為。 本檔中所述的技術不是適用于 Xamarin 的穩定測試套件的一部分，因此未來可能會中斷。

如果您注意到此技術已不再運作，或您注意到檔中有任何其他錯誤，歡迎您加入下列論壇往來文章的討論： [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
謝謝！
