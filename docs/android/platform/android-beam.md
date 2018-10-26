---
title: Android 資料交換
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 13a0a0d9c6a9d1d5f49020b1a8096f5e054d415c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114921"
---
# <a name="android-beam"></a>Android 資料交換

Android 資料交換是接近欄位距離無線通訊 (NFC) 技術，可讓透過 NFC 在接近時共用資訊的應用程式的 Android 4.0 中導入。

[![說明共用資訊的相近的兩個裝置的圖表](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android 資料交換的運作方式是在範圍內兩個裝置時，將訊息推入 NFC 透過。 裝置大約 4 公分彼此可以共用使用 Android 的資料交換的資料。 一個裝置上的活動會建立一則訊息，並指定活動 （或活動），可以將它推送對其進行處理。 當時指定的活動會在前景中的裝置是在範圍內，Android 可以將訊息推送至第二個裝置中。 在接收端的裝置，意圖會叫用包含訊息資料。

Android 支援兩種方式的設定與 Android 的資料交換的訊息：

-   `SetNdefPushMessage` -Android 可以起始之前，應用程式可以呼叫 SetNdefPushMessage 指定 NdefMessage 推送 NFC，以及會將它推送活動。 當應用程式正在使用中時，訊息不會變更，最適合使用這項機制。

-   `SetNdefPushMessageCallback` -Android 可以起始時，應用程式可以處理建立 NdefMessage 回呼。 此機制允許的訊息建立延遲到的裝置是在範圍內。 它支援的案例，訊息可能不盡相同，根據應用程式中的情況。


在任一情況下，將資料與 Android 的資料交換，傳送應用程式傳送`NdefMessage`，在數個資料封裝`NdefRecords`。 讓我們看看我們可以觸發 Android 資料交換前必須解決的關鍵點。 首先，我們將使用建立的回呼樣式`NdefMessage`。


## <a name="creating-a-message"></a>建立訊息

我們可以註冊使用的回呼`NfcAdapter`活動中`OnCreate`方法。 例如，假設`NfcAdapter`名為`mNfcAdapter`宣告為類別變數在活動中，我們可以撰寫下列程式碼，建立會建構訊息的回呼：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

活動，它會實作`NfcAdapter.ICreateNdefMessageCallback`，會傳遞至`SetNdefPushMessageCallback`上述方法。 系統起始 Android 資料交換時，會呼叫`CreateNdefMessage`，從活動可以建構`NdefMessage`，如下所示：

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

在接收端，系統會叫用目的`ActionNdefDiscovered`從中我們可以依下列方式擷取 NdefMessage 的動作：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

如需使用 Android 資料交換下, 面的螢幕擷取畫面中所示的完整程式碼範例[Android 可以示範](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)範例資源庫中。

[![從 Android 可以示範的範例螢幕擷取畫面](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>相關連結

- [Android 可以示範 （範例）](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [簡介 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
