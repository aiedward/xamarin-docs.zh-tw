---
title: 傳回了 System.Exception AMDeviceNotificationSubscribe ...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: b6d2931168132630233112b9515071ac7fd07843
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769741"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>傳回了 System.Exception AMDeviceNotificationSubscribe ...

> [!IMPORTANT]
> 此問題已在最新版本的 Xamarin 中解決。 不過，如果軟體的最新版本發生問題，請使用完整版本設定資訊和完整組建記錄檔輸出來提出[新的 bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) 。

## <a name="fix"></a>修正

1. `usbmuxd`終止進程，讓系統重新開機程式：

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2. 如果這樣做無法解決問題，請重新開機 Mac。

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

當您第一次啟動 Visual Studio for Mac 時，或在 Xamarin ios 組建主機應用程式`mtbserver.log` （**xamarin ios 組建主機 > View 組建主機記錄**檔）中的檔案中，此訊息可能會出現在錯誤對話方塊中。

請注意，這是不常見的問題。 如果 Visual Studio 連線到 Mac 組建主機時發生問題，檔案中`mtbserver.log`可能會出現其他錯誤。

### <a name="errors-in-systemlog"></a>System .log 中的錯誤

在某些情況下，下列兩個錯誤訊息可能也會`/var/log/system.log`在中重複出現：

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>其他資訊

在發生錯誤的根本原因時，其中一項猜測是負責報告 iOS 裝置和模擬器資訊的 OS X 系統服務，在罕見的情況下可能會進入非預期的狀態。 在此狀態下，Xamarin 無法與系統服務正常互動。 重新開機電腦會重新開機系統服務，並解決問題。

根據出現的錯誤`system.log` ，此問題可能與 Bonjour （`mDNSResponder`）相關。 在不同的 WiFi 網路之間進行變更似乎會增加遇到問題的機會。

## <a name="references"></a>參考

* [Bug 11789-MonoTouch. MobileDevice. Xamarin.macdev.mobiledeviceexception：AMDeviceNotificationSubscribe 傳回：0xe8000063 [已解析的 NORESPONSE]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
