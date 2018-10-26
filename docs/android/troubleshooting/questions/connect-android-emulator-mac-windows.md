---
title: 它可以連線至 Android 模擬器在 Mac 上從執行 Windows VM？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 6e66bf4edb4269aa0f3b765df4a08b78c128f763
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115629"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>它可以連線至 Android 模擬器在 Mac 上從執行 Windows VM？

若要連接至 Android 模擬器在 Mac 上執行，從 Windows 虛擬機器，使用下列步驟：

1.  在 mac 上啟動模擬器

2.  Kill`adb`在 Mac 上的伺服器：

    ```bash
    adb kill-server
    ```

3.  模擬器會接聽回送網路介面上的 2 個 TCP 連接埠附註：

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    奇數的連接埠是用來連接到`adb`。 另請參閱[ http://developer.android.com/tools/devices/emulator.html#emulatornetworking ](http://developer.android.com/tools/devices/emulator.html#emulatornetworking)。

4.  _選項 1_： 使用 [`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    要轉寄的輸入 TCP 封包上接收到外部連接埠 5555 （或是您想要的任何其他連接埠） 回送介面的奇數連接埠 (**127.0.0.1 5555**在此範例中)，並輸出封包轉送給回其他方式：

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    只要`nc`命令執行保持在終端機視窗中，將封包會轉送，如預期般運作。 您可以在結束終端機視窗中輸入 CONTROL-C`nc`命令完成後使用模擬器。

    (選項 1 會比第 2 個選項通常更容易尤其**系統喜好設定 > 安全性與隱私權 > 防火牆**已切換為開啟。) 

    _選項 2_： 使用 [`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    若要重新導向從連接埠的 TCP 封包`5555`（或任何其他您要的連接埠） 上[共用網路](http://kb.parallels.com/en/4948)介面，以在回送介面上的奇數的連接埠 (`127.0.0.1:5555`在此範例中):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    此命令會設定連接埠轉送使用`pf packet filter`系統服務。 分行符號很重要。 請務必讓它們保持不變時複製-貼。 您也必須調整的介面名稱*vmnet8*如果您使用 Parallels。 `vmnet8` 是特殊的名稱*NAT 裝置*for*共用網路*VMWare Fusion 中的模式。 在 Parallels 中適當的網路介面應該很[vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm)。

5.  從 Windows 電腦連線到模擬器：

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    取代"ip-位址---mac"Mac 的 IP 位址，例如為由列出`ifconfig vmnet8 | grep 'inet '`。 如有需要取代`5555`嗎步驟 4 中的連接埠\. (注意： 其中一種方式取得命令列的存取權`adb`是透過[**工具 > Android > Android Adb 命令提示字元**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) Visual Studio 中。)

### <a name="alternate-technique-using-ssh"></a>替代技術使用 `ssh`

如果您已啟用_遠端登入_在 Mac 上，則您可以使用`ssh`連接埠轉送來連接到模擬器。

1.  在 Windows 上安裝 SSH 用戶端。 其中一個選項是安裝[Git for Windows](https://git-for-windows.github.io/)。 `ssh`命令將可在**Git Bash**命令提示字元。

2.  遵循的步驟 1-3，從上方開始在模擬器中，kill`adb`伺服器在 Mac 上，並找出模擬器的連接埠。

3.  執行`ssh`設為在 Windows 上的本機連接埠之間的雙向連接埠轉送的 Windows 上 (`localhost:15555`在此範例中) 和奇數模擬器上的連接埠 Mac 的回送介面 (`127.0.0.1:5555`在此範例中):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    取代`mac-username`您 Mac 的使用者名稱所示`whoami`。 取代`ip-address-of-the-mac`mac 的 IP 位址

4.  連接到模擬器，在 Windows 上使用本機連接埠：

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (注意： 一項簡單方法取得命令列的存取權`adb`是透過[**工具 > Android > Android Adb 命令提示字元**Visual Studio 中](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)。)

小型注意： 如果您使用連接埠`5555`本機連接埠，`adb`會認為，Windows 上本機執行模擬器。 這不在 Visual Studio 中，會造成任何問題，但在 Visual Studio for Mac 中，會導致在啟動之後立即結束應用程式。

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>這項替代技術使用`adb -H`尚不支援

理論上，另一種方法是使用`adb`的內建的功能，可連接到`adb`遠端電腦上執行的伺服器 (請參閱範例[ http://stackoverflow.com/a/18551325 ](http://stackoverflow.com/a/18551325))。
但 Xamarin.Android IDE 延伸模組目前不提供設定該選項的方法。

## <a name="contact-information"></a>連絡資訊

本文件討論截至 2016 年 3 月，目前的行為。 這份文件中所述的技巧不是 Xamarin 的套件，穩定的測試套件的一部分，因此它可能在未來會中斷。

如果您注意到，此技術無法再運作方式，或如果您注意到文件中的任何其他錯誤，歡迎加入以下的論壇往來文章的討論： [ http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm ](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)。
謝謝！

