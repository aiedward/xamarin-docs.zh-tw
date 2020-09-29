---
title: Android Beam
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/06/2017
ms.openlocfilehash: 8132be3e709e6cfe519934ce6c16ae16ac7054c6
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454217"
---
# <a name="android-beam"></a>Android Beam

Android 橫樑是在 Android 4.0 中引進的近距離無線通訊 (NFC) 技術，可讓應用程式在接近時透過 NFC 共用資訊。

[![說明近距離共用資訊中兩個裝置的圖表](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

當兩個裝置在範圍內時，Android 橫樑的運作方式是透過 NFC 推播訊息。 彼此4cm 的裝置可以使用 Android 橫樑共用資料。 一部裝置上的活動會建立訊息，並指定活動 (或可處理推送它的活動) 。 當指定的活動在前景，且裝置在範圍內時，Android 橫樑會將訊息推送至第二個裝置。 在接收的裝置上，會叫用包含訊息資料的意圖。

Android 支援兩種使用 Android 橫樑設定訊息的方式：

- `SetNdefPushMessage` -在起始 Android 橫樑之前，應用程式可以呼叫 SetNdefPushMessage 來指定要在 NFC 中推送的 NdefMessage，以及推送它的活動。 當應用程式正在使用中時，當訊息未變更時，最適合使用此機制。

- `SetNdefPushMessageCallback` -當啟動 Android 橫樑時，應用程式可以處理回呼來建立 NdefMessage。 這項機制可讓訊息建立延遲，直到裝置在範圍內為止。 它支援的案例可能會根據應用程式中的情況而有所不同。

在任一種情況下，若要使用 Android 橫樑傳送資料，應用程式會傳送，並將 `NdefMessage` 資料封裝在數個 `NdefRecords` 。 讓我們來看看在可以觸發 Android 橫樑之前必須先解決的重點。 首先，我們將使用建立的回呼樣式 `NdefMessage` 。

## <a name="creating-a-message"></a>建立訊息

我們可以 `NfcAdapter` 在活動的方法中向註冊回呼 `OnCreate` 。 例如，假設在 `NfcAdapter` 活動中將名稱宣告為 `mNfcAdapter` 類別變數，我們可以撰寫下列程式碼來建立將會建立訊息的回呼：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

實作為的活動 `NfcAdapter.ICreateNdefMessageCallback` 會傳遞至 `SetNdefPushMessageCallback` 上述方法。 當 Android 橫樑起始時，系統會呼叫 `CreateNdefMessage` ，活動可以在其中建立， `NdefMessage` 如下所示：

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

在接收端，系統 `ActionNdefDiscovered` 會使用動作叫用意圖，我們可以從這裡將 NdefMessage 解壓縮，如下所示：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

如需使用 Android 橫樑的完整程式碼範例（如以下螢幕擷取畫面所示），請參閱範例庫中的 [Android 橫樑示範](/samples/xamarin/monodroid-samples/androidbeamdemo) 。

[![Android 橫樑示範的範例螢幕擷取畫面](android-beam-images/24.png)](android-beam-images/24.png#lightbox)

## <a name="related-links"></a>相關連結

- [Android 橫樑示範 (範例) ](/samples/xamarin/monodroid-samples/androidbeamdemo)