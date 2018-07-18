---
title: System.Exception AMDeviceNotificationSubscribe 傳回...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 257e0c14de3c23825b6abe6601c25438db81c58e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776478"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System.Exception AMDeviceNotificationSubscribe 傳回...

> [!IMPORTANT]
> 這個問題已經解決 Xamarin 的最新版的。 但是，若發生此問題，軟體的最新版本，請檔案[新 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本資訊和完整建置記錄檔輸出。


## <a name="fix"></a>修正

1.  Kill`usbmuxd`處理，讓系統將會重新啟動它：

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  如果無法解決問題，重新啟動 mac

## <a name="error-message"></a>錯誤訊息

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

當您第一次啟動 Visual Studio for Mac，或在錯誤對話方塊中會出現此訊息`mtbserver.log`Xamarin.iOS 組建主機應用程式中的檔案 (**Xamarin.iOS 組建主機 > 檢視建置主應用程式記錄檔**)。

請注意，這不常發生的問題。 Visual Studio 無法連接到 Mac 組建主機，如果有較有可能會出現在其他錯誤`mtbserver.log`檔案。

### <a name="errors-in-systemlog"></a>System.log 中的錯誤

在某些情況下面兩個錯誤可能也會出現訊息中重複`/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>其他資訊

在錯誤的根本原因的一項猜測值是 OS X 系統服務負責 reporting iOS 裝置和模擬器資訊可以在少數情況下，輸入非預期的狀態。 Xamarin 無法在此狀態的系統服務與正確互動。 重新開機會重新啟動系統服務，並解決問題。

根據從錯誤`system.log`會出現此問題可能會與 Bonjour (`mDNSResponder`)。 切換不同的 WiFi 網路似乎增加遇到問題的機會。

## <a name="references"></a>參考

*   [Bug 11789-MonoTouch.MobileDevice.MobileDeviceException: AMDeviceNotificationSubscribe 傳回： 0xe8000063 [解析 NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
