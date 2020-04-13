---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: aab121ed5f811baf38eed48cf891ccdf076eaf44
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291955"
---
# <a name="android-beam"></a>Android Beam

Android 光束是 Android 4.0 中引入的近場通信 (NFC) 技術,允許應用程式在近距離通過 NFC 共用資訊。

[![圖說明兩個裝置在近距離分享資訊](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android 光束的工作原理是,當兩台設備在範圍內時,將消息推送到 NFC 上。 彼此距離約 4 釐米的設備可以使用 Android 光束共享數據。 一台設備上的活動將創建一條消息,並指定可以處理推送消息的活動(或活動)。 當指定的活動在前臺且設備處於範圍內時,Android 光束會將消息推送到第二個設備。 在接收設備上,將調用包含消息數據的意向。

Android 支援使用 Android 光束設定消息的兩種方法:

- `SetNdefPushMessage`- 在 Android 光束啟動之前,應用程式可以調用 SetNdefPushMessage 來指定 NdefMessage 以推送 NFC 以及推動它的活動。 當應用程式正在使用時消息不更改時,最好使用此機制。

- `SetNdefPushMessageCallback`- 啟動 Android 光束時,應用程式可以處理回調以創建 NdefMessage。 此機制允許延遲消息創建,直到設備在範圍內。 它支援消息可能因應用程式中發生的情況而異的情況。

在這兩種情況下,要使用 Android 光束發送數據,`NdefMessage`應用程式都會將數據打包`NdefRecords`到多個 中。 讓我們來看看在觸發 Android 光束之前必須解決的要點。 首先,我們將使用建立 回檔`NdefMessage`樣式 。

## <a name="creating-a-message"></a>建立訊息

我們可以在活動`NfcAdapter``OnCreate`的方法中註冊回調。 例如,假設命名`NfcAdapter``mNfcAdapter`在活動中聲明為類變數,我們可以編寫以下代碼來創建將構造消息的回調:

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

實現活動`NfcAdapter.ICreateNdefMessageCallback`將傳遞給`SetNdefPushMessageCallback`上述 方法。 啟動 Android 光束時,系統將`CreateNdefMessage`調用 ,活動可以從中構造`NdefMessage`如下所示 :

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```

## <a name="receiving-a-message"></a>接收訊息

在接收端,系統調用意圖與`ActionNdefDiscovered`操作,我們可以從中提取 NdefMessage 如下:

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

有關使用 Android 光束的完整代碼範例(如下圖所示所示)請參閱範例庫中的[Android 光束示範](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)。

[![來自 Android 光束示範的範例螢幕截圖](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>相關連結

- [安卓光束演示(示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
