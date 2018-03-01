---
title: "Android 資料交換"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/06/2017
ms.openlocfilehash: bea8480c66a2ecf499375636c98511ca55ce7693
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="android-beam"></a>Android 資料交換

Android 資料交換是可讓透過時近 NFC 共用資訊的應用程式的 Android 4 的新附近欄位通訊 (NFC) 技術。

[![圖表說明兩個裝置進行近共用資訊](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png)

Android 資料交換的運作方式是在範圍內兩個裝置時，將訊息推入透過 NFC。 裝置彼此大約 4 cm 可以共用使用 Android 的資料交換的資料。 一部裝置上的活動建立訊息，並指定活動 （或活動），可以將它推送來處理。 當指定的活動會在前景和裝置都在範圍內時，Android 資料交換將訊息推送到第二個裝置。 在接收裝置上，意圖會叫用包含訊息資料。

Android 支援兩種設定 Android 資料交換訊息的方式：

-   `SetNdefPushMessage` Android 資料交換起始之前，應用程式可以呼叫來指定透過 NFC，並會將它推送活動推播 NdefMessage SetNdefPushMessage。 當應用程式正在使用中時，訊息不會變更時，最適合使用這項機制。

-   `SetNdefPushMessageCallback` -當起始 Android 資料交換時，應用程式可以處理建立 NdefMessage 回呼。 此機制可讓訊息建立延遲到裝置是在範圍內。 它支援的案例有所不同訊息為基礎的應用程式中的情況。


在任一情況下，將資料與 Android 的資料交換，傳送應用程式傳送`NdefMessage`，封裝在幾個資料`NdefRecords`。 讓我們看看我們可以觸發 Android 資料交換前必須解決的關鍵點。 首先，我們將會使用建立的回呼樣式`NdefMessage`。

<a name="Creating_a_Message" />

## <a name="creating-a-message"></a>建立訊息

我們可以註冊的回呼`NfcAdapter`中活動的`OnCreate`方法。 例如，假設`NfcAdapter`名為`mNfcAdapter`宣告為類別變數在活動中，我們可以撰寫下列程式碼來建立會建構訊息的回呼：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

活動，它會實作`NfcAdapter.ICreateNdefMessageCallback`，傳遞至`SetNdefPushMessageCallback`上述方法。 系統起始 Android 資料交換時，會呼叫`CreateNdefMessage`，從活動可以建構`NdefMessage`如下所示：

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

<a name="Receiving_a_Message" />

## <a name="receiving-a-message"></a>接收訊息

在接收端，系統會叫用與意圖`ActionNdefDiscovered`動作，從中可擷取 NdefMessage，如下所示：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

使用 Android 資料交換，顯示在下列螢幕擷取畫面中執行的完整程式碼範例請參閱[Android 資料交換示範](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)範例庫中。

[![從 Android 資料交換示範範例螢幕擷取畫面](android-beam-images/24.png)](android-beam-images/24.png)



## <a name="related-links"></a>相關連結

- [Android 資料交換示範 （範例）](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
