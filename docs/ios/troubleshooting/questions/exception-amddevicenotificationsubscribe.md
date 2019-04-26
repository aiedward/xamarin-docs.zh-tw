---
title: 傳回了 System.Exception AMDeviceNotificationSubscribe ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4fb0712366422e8810a2db60d40c3b85d9f4cd82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421940"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>傳回了 System.Exception AMDeviceNotificationSubscribe ...

> [!IMPORTANT]
> 這個問題已經解決最新版 Xamarin。 不過，如果最新版本的軟體，就會發生問題，請指導，申請[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md)您完整的版本控制資訊和完整建置記錄檔輸出。


## <a name="fix"></a>修正

1.  Kill`usbmuxd`處理，因此系統會重新啟動它：

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  如果這樣無法解決問題，重新啟動 mac。

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

當您第一次啟動 Visual Studio for Mac，或在 [錯誤] 對話方塊中會出現此訊息`mtbserver.log`Xamarin.iOS Build Host 應用程式中的檔案 (**Xamarin.iOS Build Host > 檢視建置主應用程式記錄檔**)。

請注意，這是常見的問題。 如果 Visual Studio 無法連接到 Mac 組建主機，還有更有可能會出現在其他錯誤`mtbserver.log`檔案。

### <a name="errors-in-systemlog"></a>在 [system.log] 中的錯誤

在某些情況下下列兩個錯誤訊息可能也會出現中重複`/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>其他資訊

一個猜出錯誤的根本原因是 OS X 系統服務負責報告 iOS 裝置和模擬器的資訊可在極少數的情況下，輸入未預期的狀態。 Xamarin 無法與系統服務，在此狀態下正確互動。 重新啟動電腦時，會重新啟動系統服務，並可解決問題。

根據從錯誤`system.log`它會出現此問題可能與 Bonjour (`mDNSResponder`)。 切換不同的 WiFi 網路似乎會提高遇到問題的機會。

## <a name="references"></a>參考

*   [Bug 11789-MonoTouch.MobileDevice.MobileDeviceException:AMDeviceNotificationSubscribe 傳回：0XE8000063 [解析 NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
