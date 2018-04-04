---
title: 核心 NFC
description: 使用 iOS 11 讀取附近欄位距離無線通訊 (NFC) 標記
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2016
ms.openlocfilehash: db0c417c1c79a988821a93b1cf20d94161f5c47a
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="core-nfc"></a>核心 NFC

_使用 iOS 11 讀取附近欄位距離無線通訊 (NFC) 標記_

CoreNFC 是一個新的架構中提供的存取權的 iOS 11_近距離無線通訊_讀取從應用程式內的標記 (NFC) 選項。 它適用於 iPhone 7，8，再加上，而 X 8 7 加號。

NFC 標記讀取器，在 iOS 裝置支援所有的 NFC 標記類型 1 至 5 包含_NFC 資料交換格式_(NDEF) 資訊。

有一些要注意的一些限制：

- CoreNFC 只支援讀取 （不是撰寫或格式） 的標記。
- 掃描標籤必須是使用者起始，並在 60 秒後的逾時。
- 應用程式必須顯示在前景進行掃描。
- CoreNFC 僅 （不在模擬器中） 上的實際裝置上進行測試。

此頁面描述用於 CoreNFC 所需的設定，並示範如何使用應用程式開發介面使用["TFCTagReader 」 範例程式碼](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)。

## <a name="configuration"></a>組態

若要啟用 CoreNFC，您必須在專案中設定三個項目：

- **Info.plist**隱私金鑰。
- **Entitlements.plist**項目。
- 使用的佈建設定檔**NFC 標記讀取**功能。

### <a name="infoplist"></a>Info.plist

新增**NFCReaderUsageDescription**隱私金鑰和所發生的掃描時，對使用者顯示的文字。 使用適用於您的應用程式訊息 （例如，說明掃描的目的）：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

您的應用程式必須要求**附近欄位通訊標記讀取**功能使用下列索引鍵/值配對中您**Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>佈建設定檔

建立新**應用程式識別碼**，並確定**NFC 標記讀取**核服務是：

[![開發人員入口網站的新應用程式識別碼頁面以選取 NFC 標記讀取](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

您應該再為此應用程式識別碼，建立新的佈建設定檔，然後下載並將它安裝在您開發 mac

## <a name="reading-a-tag"></a>讀取標記

一旦設定您的專案，加入`using CoreNFC;`加入頂端，檔案並遵循這三個步驟來實作 NFC 標記讀取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1.實作 `INFCNdefReaderSessionDelegate`

這個介面具有兩種方法來實作：

- `DidDetect` – 成功讀取標記時呼叫。
- `DidInvalidate` – 呼叫時就會發生錯誤或 60 秒的逾時為止。

#### <a name="diddetect"></a>DidDetect

在範例程式碼中，每個掃描的訊息加入至資料表檢視：

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

這個方法呼叫多次 （和陣列的訊息可能會傳入） 如果在工作階段允許多個標記讀取。 這使用第三個參數來設定`Start`方法 (詳見[步驟 2](#step2))。

#### <a name="didinvalidate"></a>DidInvalidate

多種原因所造成的發生失效：

- 掃描時發生錯誤。
- 應用程式不會在前景。
- 使用者選擇要取消掃描。
- 掃描已取消的應用程式。

下列程式碼會示範如何處理錯誤：

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

一旦工作階段已失效，則必須建立新的工作階段物件，才能再掃描一次。

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2.啟動 `NFCNdefReaderSession`

掃描的開頭應使用者要求，例如按下按鈕。
下列程式碼會建立並啟動掃描工作階段：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

參數`NFCNdefReaderSession`建構函式如下：

- `delegate` – 實作`INFCNdefReaderSessionDelegate`。 在範例程式碼中，委派中實作的資料表檢視控制站，因此`this`做為委派參數。
- `queue` – 佇列上處理的回呼。 它可以是`null`，在此情況下請務必使用`DispatchQueue.MainQueue`（如範例所示），更新使用者介面控制項時。
- `invalidateAfterFirstRead` – 當`true`，掃描後，第一個成功的掃描; 停止時`false`掃描將會繼續，而多個結果傳回，直到掃描遭到取消或 60 秒的逾時為止。


### <a name="3-cancel-the-scanning-session"></a>3.取消掃描工作階段

使用者可以取消掃描工作階段，透過系統提供的按鈕，在使用者介面：

![在掃描時取消按鈕](corenfc-images/scan-cancel-sml.png)

應用程式可以藉由呼叫以程式設計的方式取消掃描`InvalidateSession`方法：

```csharp
Session.InvalidateSession();
```

在這兩種情況下，委派的`DidInvalidate`會呼叫的方法。

## <a name="summary"></a>總結

CoreNFC 可讓您的應用程式從 NFC 標記讀取資料。 它支援讀取各種不同的標記格式 （NDEF 類型為 1 到 5），但不支援寫入，或格式。


## <a name="related-links"></a>相關連結

- [NFCTagReader （範例）](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [介紹核心 NFC (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/718/)
