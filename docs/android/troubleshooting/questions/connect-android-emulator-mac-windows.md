---
title: 從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 336fb2ae1f1619994b11b630a6cb6726f17d0758
ms.sourcegitcommit: 6d347e1d7641ac1d2b389fb1dc7a6882a08f7c00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851518"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>從 Windows VM 連線到在 Mac 上執行的 Android 模擬器是否可行？

若要從 Windows 虛擬機器連接至 Mac 上執行的 Android Emulator，請使用下列步驟：

> [!NOTE]
> 我們建議使用不包含 Google Play 商店的 Android Emulator。

1. 在 Mac 上啟動模擬器。

2. 終止 `adb` Mac 上的伺服器：

    ```bash
    adb kill-server
    ```

3. 請注意，模擬器會在回送網路介面上的2個 TCP 埠上接聽：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇數通訊埠是用來連接的通訊埠 `adb` 。 另請參閱 [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking) 。

4. _選項 1_：用 `nc` 來轉送在埠5555上外部接收的輸入 TCP 封包 (或您喜歡的任何其他埠) 回送介面上的奇數埠 (此範例) 的 **127.0.0.1 5555** ，並以另一種方式將輸出的封包轉送回來：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要 `nc` 命令仍在終端機視窗中執行，就會如預期般轉寄封包。 您可以在終端機視窗中輸入 Control-C，以在 `nc` 使用模擬器完成後結束命令。

     (選項1通常比選項2更簡單，特別是當 **系統偏好設定 > 安全性 & 隱私權 > 防火牆** 開啟時。 ) 

    _選項 2_：用 `pfctl` 來將 TCP 封包從埠 `5555` (或您喜歡的任何其他埠（您想要) 在  [共用網路](https://kb.parallels.com/en/4948) 介面上）重新導向至回送介面上的奇數埠 (`127.0.0.1:5555` 在此範例中) ：

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令會使用系統服務來設定埠轉送 `pf packet filter` 。 分行符號是很重要的。 複製貼上時，請務必將它們保持不變。 如果您使用的是並列的，您也需要調整 *vmnet8* 中的介面名稱。 `vmnet8`是 VMWare 融合中*共用網路*模式的特殊*NAT 裝置*名稱。 在中，適當的網路介面可能會 [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5. 從 Windows 電腦連接到模擬器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    以 Mac 的 IP 位址取代「ip-位址-mac」，例如，如下所示 `ifconfig vmnet8 | grep 'inet '` 。 如有需要，請將取代 `5555` 為您在步驟4中所需的其他埠\.  (注意：取得命令列存取的其中一種方式 `adb` 是在 Visual Studio 中透過 [**Tools > Android > Android Adb 命令提示**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) 字元。 ) 

### <a name="alternate-technique-using-ssh"></a>使用的替代技巧 `ssh`

如果您已在 Mac 上啟用 _遠端登入_ ，則可以使用 `ssh` 埠轉送來連接至模擬器。

1. 在 Windows 上安裝 SSH 用戶端。 其中一個選項是安裝 [Git For Windows](https://git-for-windows.github.io/)。 `ssh`然後，命令會出現在**Git Bash**命令提示字元中。

2. 遵循上述的步驟1-3 來啟動模擬器、終止  `adb` Mac 上的伺服器，以及識別模擬器埠。

3. 在 `ssh` windows 上執行，以在此範例中的本機埠之間設定雙向埠轉送 (在此範例 `localhost:15555` 中，) 和 Mac 回送介面上的奇數模擬器埠 (`127.0.0.1:5555` 在此範例中) ：

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    以所 `mac-username` 列的 Mac 使用者名稱取代 `whoami` 。 `ip-address-of-the-mac`以 Mac 的 IP 位址取代。

4. 使用 Windows 上的本機埠連接到模擬器：

    ```cmd
    C:\> adb connect localhost:15555
    ```

     (注意：取得命令列存取的一個簡單方法 `adb` 是[在 Visual Studio 中透過**Tools > Android > Android Adb 命令提示**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)字元。 ) 

請特別注意：如果您使用埠 `5555` 作為本機埠，則 `adb` 會認為模擬器是在本機 Windows 上執行。 這不會造成 Visual Studio 中的任何問題，但 Visual Studio for Mac 它會導致應用程式在啟動後立即結束。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>`adb -H`尚不支援使用的替代技巧

理論上，另一種方法是使用 `adb` 的內建功能來連線到 `adb` 遠端電腦上執行的伺服器 (如) 所示 [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325) 。
但 Xamarin. Android IDE 延伸模組目前未提供設定該選項的方式。

## <a name="contact-information"></a>連絡人資訊

本檔將討論目前的行為，從2016年3月起。 本檔中所述的技術不是適用于 Xamarin 的穩定測試套件的一部分，因此未來可能會中斷。

如果您注意到該技術已無法運作，或是您注意到檔中有任何其他錯誤，請隨時在下列論壇往來文章中新增討論： [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm) 。
感謝您！
