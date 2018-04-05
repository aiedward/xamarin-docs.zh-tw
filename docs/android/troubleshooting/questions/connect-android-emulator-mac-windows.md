---
title: 它可以連接至 Android 模擬器在 Mac 上從執行 Windows VM？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 4afe2b9fab8eeebb3f0451379b8e3937cc8d8f55
ms.sourcegitcommit: 66807f8927d472fbfd0ff8bc77cea9b37e7b9a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2018
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>它可以連接至 Android 模擬器在 Mac 上從執行 Windows VM？

若要連接到 Google Android 模擬器在 Mac 上執行的 Windows 虛擬機器，請使用下列步驟：

1.  在 mac 上啟動模擬器

2.  Kill`adb`在 Mac 上的伺服器：

    ```bash
    adb kill-server
    ```

3.  請注意模擬器 2 的回送網路介面上的 TCP 連接埠上接聽：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇數的連接埠是用來連接到`adb`。 另請參閱[ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4.  _選項 1_： 使用[ `nc` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)向前輸入 TCP 封包外部連接埠上接收 5555 （或是您想要的任何其他連接埠） 到奇數連接埠上的回送介面 (**127.0.0.1 5555**在此範例中)，並輸出封包轉送給回另一種方式：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要`nc`命令執行維持在 Terminal 視窗中，如預期般，將會轉送封包。 您可以輸入控制項 C 以結束終端機視窗`nc`命令完成之後使用模擬器。

    (選項 1 通常會比來得容易選項 2，特別是當**系統偏好設定 > 安全性和隱私權 > 防火牆**電源已開啟。) 

    _選項 2_： 使用[ `pfctl` ](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)重新導向連接埠 TCP 封包`5555`（或任何您喜歡的連接埠） 上[共用網路](http://kb.parallels.com/en/4948)奇數的連接埠上的介面回送介面 (`127.0.0.1:5555`在此範例中):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令會設定連接埠轉送使用`pf packet filter`系統服務。 分行符號很重要。 請務必保留它們時複製-貼。 您也必須調整的介面名稱*vmnet8*如果您使用 Parallels。 `vmnet8` 是特殊的名稱*NAT 裝置*如*共用網路*VMWare 融合中的模式。 在 Parallels 中適當的網路介面很[vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5.  從 Windows 電腦連線到模擬器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    取代 」 ip 位址的--mac"與 Mac 的 IP 位址，例如做為所列出`ifconfig vmnet8 | grep 'inet '`。 如有需要取代`5555`與您喜歡步驟 4 中的連接埠\. (注意： 其中一種方式取得命令列存取`adb`是透過[**工具 > Android > Android Adb 命令提示字元**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) Visual Studio 中。)

### <a name="alternate-technique-using-ssh"></a>其他技術使用 `ssh`

如果您已啟用_遠端登入_在 Mac 上，您就可以使用`ssh`連接埠轉送來連接到模擬器。

1.  在 Windows 上安裝的 SSH 用戶端。 若要安裝的其中一個選項是[Git for Windows](https://git-for-windows.github.io/)。 `ssh`命令將可在**Git Bash**命令提示字元。

2.  遵循的步驟 1-3 上述若要啟動模擬器，請終止`adb`Mac 上的伺服器，並識別模擬器連接埠。

3.  執行`ssh`設定在 Windows 上的本機連接埠之間的雙向連接埠轉送的 Windows 上 (`localhost:15555`在此範例中) 和奇數模擬器上的連接埠 Mac 的回送介面 (`127.0.0.1:5555`在此範例中):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    取代`mac-username`具有您 Mac 使用者名稱，如下所列的`whoami`。 取代`ip-address-of-the-mac`與 mac 的 IP 位址

4.  連接到模擬器 Windows 上使用本機連接埠：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (注意： 一個簡便的方式取得命令列存取`adb`是透過[**工具 > Android > Android Adb 命令提示字元**Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。)

小型注意： 如果您使用連接埠`5555`本機連接埠，`adb`會認為模擬器在本機執行 Windows 上。 這不會在 Visual Studio 中，會造成任何問題，但它會導致在 Visual Studio for Mac 應用程式啟動後立即結束。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>使用替代技術`adb -H`尚不支援

理論上，另一種方法就是使用`adb`的內建功能可連接到`adb`遠端機器上執行伺服器 (請參閱例如[ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325))。
但 Xamarin.Android IDE 擴充功能目前不提供設定該選項的方法。

## <a name="contact-information"></a>連絡資訊

本文件討論自 2016 年 3 月，目前的行為。 這份文件中所述的技巧不屬於 xamarin，穩定的測試套件，讓它在未來將會中斷。

如果您注意到，則技術無法再運作，或如果您注意到文件中的任何其他錯誤，依需要加入，討論的下列論壇執行緒： [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
謝謝！

