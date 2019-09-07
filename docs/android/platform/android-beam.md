---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 4d294b7aee9d4a6c2118a5ff12968a7f95fb981c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70757863"
---
# <a name="android-beam"></a>Android Beam

Android 橫樑是 Android 4.0 中引進的近距離無線通訊（NFC）技術，可讓應用程式在接近鄰近的情況時，透過 NFC 分享資訊。

[![說明近近分享資訊中兩個裝置的圖表](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android 橫樑的運作方式是在兩個裝置都在範圍內時，透過 NFC 推播訊息。 彼此4cm 的裝置可以使用 Android 橫樑共用資料。 一個裝置上的活動會建立一則訊息，並指定可處理推送它的活動（或活動）。 當指定的活動位於前景且裝置在範圍內時，Android 橫樑會將訊息推送到第二個裝置。 在接收的裝置上，會叫用包含訊息資料的意圖。

Android 支援兩種使用 Android 橫樑設定訊息的方式：

- `SetNdefPushMessage`-在開始 Android 橫樑之前，應用程式可以呼叫 SetNdefPushMessage 來指定要推送到 NFC 的 NdefMessage，以及要推送它的活動。 當應用程式正在使用中時，如果訊息不會變更，最好使用這項機制。

- `SetNdefPushMessageCallback`-當 Android 橫樑起始時，應用程式可以處理回呼來建立 NdefMessage。 這項機制可讓訊息建立延遲，直到裝置在範圍內為止。 它支援訊息可能會根據應用程式中發生的情況而有所不同的案例。

在任一情況下，若要使用 Android 橫樑傳送資料，應用程式`NdefMessage`會傳送，並在數`NdefRecords`個中封裝資料。 我們來看一下必須解決才能觸發 Android 橫樑的重點。 首先，我們將使用建立`NdefMessage`的回呼樣式。

## <a name="creating-a-message"></a>建立訊息

我們可以`NfcAdapter`在活動的`OnCreate`方法中，向註冊回呼。 例如，假設名`NfcAdapter` `mNfcAdapter`為的會宣告為活動中的類別變數，我們可以撰寫下列程式碼來建立將會建立訊息的回呼：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

執行的活動`NfcAdapter.ICreateNdefMessageCallback`會傳遞`SetNdefPushMessageCallback`給上述方法。 當 Android 橫樑起始時，系統將會呼叫`CreateNdefMessage`，活動可以在其中`NdefMessage`建立，如下所示：

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

在接收端，系統會叫用具有`ActionNdefDiscovered`動作的意圖，我們可以在其中解壓縮 NdefMessage，如下所示：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

如需使用 Android 橫樑的完整程式碼範例（如下列螢幕擷取畫面所示），請參閱範例庫中的[Android 橫樑示範](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)。

[![Android 橫樑示範的範例螢幕擷取畫面](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>相關連結

- [Android 橫樑示範（範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/androidbeamdemo)
- [霜淇淋三明治簡介](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平臺](https://developer.android.com/sdk/android-4.0.html)
