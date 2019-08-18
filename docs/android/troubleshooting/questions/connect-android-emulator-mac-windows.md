---
title: 從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 8537e075c4ac1dba8362ec9ce5fc77ee0935503c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523296"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？

若要從 Windows 虛擬機器連接到在 Mac 上執行的 Android Emulator, 請使用下列步驟:

1. 在 Mac 上啟動模擬器。

2. 在 Mac 上終止伺服器:`adb`

    ```bash
    adb kill-server
    ```

3. 請注意, 模擬器會接聽回送網路介面上的2個 TCP 通訊埠:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇數通訊埠是用來連接到`adb`的。 另請參閱。 [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)

4. _選項 1_:使用[`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    將埠 5555 (或您想要的任何其他埠) 外部接收的輸入 TCP 封包轉送到回送介面上的奇數埠 (在此範例中為**127.0.0.1 5555** ), 並將輸出封包轉送回另一種方式:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    `nc`只要命令在終端機視窗中保持執行狀態, 封包就會如預期般轉送。 您可以在終端機視窗中輸入 Control-C, 以`nc`在使用模擬器完成後結束命令。

    (選項1通常比選項2更簡單, 特別是如果**系統喜好設定 > 安全性 & 隱私權 > 防火牆**已開啟)。 

    _選項 2_:使用[`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    若要將[共用網路](http://kb.parallels.com/en/4948)介面`5555`上的 TCP 封包從埠 (或您喜歡的任何其他埠) 重新導向至回送介面上的`127.0.0.1:5555`奇數埠 (在此範例中為):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令會使用`pf packet filter`系統服務來設定埠轉送。 分行符號很重要。 複製貼入時, 請務必保持不變。 如果您使用的是「平行」, 您也必須從*vmnet8*調整介面名稱。 `vmnet8`這是 VMWare 融合中*共用網路*模式的特殊*NAT 裝置*名稱。 類似的網路介面可能[vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5. 從 Windows 機器連接到模擬器:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    以 Mac 的 IP 位址取代「ip-address-mac」, 例如由`ifconfig vmnet8 | grep 'inet '`列出。 如有需要, `5555`請將取代為您在步驟4中所需的其他埠\. (注意: 取得命令列存取`adb`的一種方式, 就是透過 Visual Studio 中 android [**Adb 命令提示字元的工具 > >** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) 。)

### <a name="alternate-technique-using-ssh"></a>使用的替代技術`ssh`

如果您已在 Mac 上啟用_遠端登入_, 則可以使用`ssh`埠轉送來連接至模擬器。

1. 在 Windows 上安裝 SSH 用戶端。 其中一個選項是安裝[Git For Windows](https://git-for-windows.github.io/)。 接著, 命令會在**Git Bash**命令提示字元中提供。 `ssh`

2. 遵循上述步驟1-3 來啟動模擬器、在 Mac 上終止`adb`伺服器, 並識別模擬器埠。

3. 在`ssh` windows 上執行, 在 windows 上的本機埠 (`localhost:15555`在此範例中為) 和 Mac 回送介面上的奇數模擬器埠 (`127.0.0.1:5555`在此範例中為) 上設定雙向埠轉送:

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    以`mac-username`所列出的`whoami`Mac 使用者名稱取代。 將`ip-address-of-the-mac`取代為 Mac 的 IP 位址。

4. 使用 Windows 上的本機埠連接到模擬器:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (注意: 取得命令列存取`adb`的一種簡單方法, 就是[在 Visual Studio 中透過 android **> android Adb 命令提示字元的工具 >** ](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat))。

請特別注意: 如果您針對本機`5555`埠使用埠, `adb`將會認為模擬器是在 Windows 本機上執行。 這不會造成 Visual Studio 中的任何問題, 但在 Visual Studio for Mac 會導致應用程式在啟動後立即結束。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>尚不支援`adb -H`使用的替代技術

理論上, 另一種方法是使用`adb`的內建功能來連接到遠端電腦`adb`上執行的伺服器 (例如[https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325), 請參閱)。
但 Xamarin Android IDE 擴充功能目前並未提供設定該選項的方法。

## <a name="contact-information"></a>連絡人資訊

本檔討論2016年3月的目前行為。 本檔中所述的技術不是適用于 Xamarin 的穩定測試套件的一部分, 因此未來可能會中斷。

如果您注意到此技術已不再運作, 或您注意到檔中有任何其他錯誤, 歡迎您加入下列論壇往來文章的討論: [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
謝謝！

