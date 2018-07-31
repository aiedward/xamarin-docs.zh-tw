---
title: 在 Xamarin.iOS 中的核心 NFC
description: 本文件說明如何在 Xamarin.iOS 使用於 iOS 11 推出的 Api 中的欄位通訊標記附近讀取。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/25/2017
ms.openlocfilehash: 1381a4564f93fd091f181949454df3f06b31ae6b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350829"
---
# <a name="core-nfc-in-xamarinios"></a>在 Xamarin.iOS 中的核心 NFC

_使用 iOS 11 讀取附近欄位距離無線通訊 (NFC) 標記_

CoreNFC 是新的架構，在 iOS 11，提供存取權_近距離無線通訊_讀取從應用程式內的標記 (NFC) 選項。 它適用於 iPhone 7、 8、 8 Plus，而 X 7 Plus。

IOS 裝置的 NFC 標籤讀取器支援所有的 NFC 標記類型 1 至 5 包含_NFC 資料交換格式_(NDEF) 資訊。

有一些要注意的限制：

- CoreNFC 只支援讀取 （不是撰寫或格式化） 的標記。
- 標記掃描必須由使用者啟始，和在 60 秒後的逾時。
- 應用程式必須顯示在前景讓掃描。
- CoreNFC 僅 （不在模擬器中） 上的實際裝置上進行測試。

此頁面說明使用 CoreNFC 所需的設定，並示範如何使用 API 使用[程式碼範例 」 TFCTagReader"](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)。

## <a name="configuration"></a>組態

若要啟用 CoreNFC，您必須在專案中設定三個項目：

- **Info.plist**隱私金鑰。
- **Entitlements.plist**項目。
- 使用的佈建設定檔**NFC 標籤讀取**功能。

### <a name="infoplist"></a>Info.plist

新增**NFCReaderUsageDescription**隱私金鑰和所進行的掃描時，會將對使用者顯示的文字。 使用適用於您的應用程式訊息 （例如，說明掃描的用途）：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

您的應用程式必須要求**靠近欄位通訊標籤讀取**功能使用下列索引鍵/值配對中您**Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>佈建設定檔

建立新**應用程式識別碼**，並確定**NFC 標籤讀取**已核服務：

[![開發人員入口網站的新應用程式識別碼頁面與選取的 NFC 標籤讀取](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

您應該再為此應用程式識別碼，建立新的佈建設定檔然後下載並將它安裝在您的開發 mac。

## <a name="reading-a-tag"></a>讀取標籤

一旦設定您的專案，加入`using CoreNFC;`頂端檔案並遵循下列三個步驟，來實作 NFC 標籤讀取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1.實作 `INFCNdefReaderSessionDelegate`

這個介面具有兩種方法可以實作：

- `DidDetect` – 成功讀取標記時呼叫。
- `DidInvalidate` -呼叫時就會發生錯誤或 60 秒的逾時為止。

#### <a name="diddetect"></a>DidDetect

在範例程式碼中，每個掃描的訊息新增至資料表檢視：

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

這個方法可能會多次呼叫 （和陣列的訊息可能會傳入） 如果工作階段可讓多個標記讀取。 這會設定使用的第三個參數`Start`方法 (中所述[步驟 2](#step2))。

#### <a name="didinvalidate"></a>DidInvalidate

有許多原因可能會造成失效：

- 在掃描時，發生錯誤。
- 應用程式不再是在前景中。
- 使用者選擇取消掃描。
- 掃描已取消應用程式。

下列程式碼示範如何處理錯誤：

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

之後已經失效的工作階段，就必須建立新的工作階段物件來掃描一次。

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2.啟動 `NFCNdefReaderSession`

掃描的開頭應為使用者要求，例如按下按鈕。
下列程式碼會建立並啟動掃描工作階段：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

參數`NFCNdefReaderSession`建構函式如下所示：

- `delegate` – 實作`INFCNdefReaderSessionDelegate`。 在範例程式碼中，委派中實作資料表檢視控制器，因此`this`做為委派參數。
- `queue` – 佇列上處理回呼。 它可以是`null`，在此情況下請務必使用`DispatchQueue.MainQueue`更新使用者介面控制項 （如範例所示） 時。
- `invalidateAfterFirstRead` – 當`true`，掃描後，第一次成功掃描; 停止時`false`掃描將會繼續，直到掃描已取消，或達到 60 秒的逾時為止，會傳回多個結果。


### <a name="3-cancel-the-scanning-session"></a>3.取消掃描工作階段

使用者可以取消掃描工作階段，透過系統提供的按鈕，在使用者介面：

![在掃描時的 [取消] 按鈕](corenfc-images/scan-cancel-sml.png)

應用程式可以藉由呼叫以程式設計方式取消掃描`InvalidateSession`方法：

```csharp
Session.InvalidateSession();
```

在這兩種情況下，委派的`DidInvalidate`會呼叫方法。

## <a name="summary"></a>總結

CoreNFC 可讓您的應用程式從 NFC 標籤讀取資料。 它支援讀取各種標記格式 （NDEF 類型為 1 到 5），但不支援寫入，或格式。


## <a name="related-links"></a>相關連結

- [NFCTagReader （範例）](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [簡介 Core NFC (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/718/)
