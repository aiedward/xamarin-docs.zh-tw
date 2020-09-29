---
title: Xamarin 中的核心 NFC
description: 本檔說明如何使用 iOS 11 中引進的 Api，在 Xamarin 中讀取近距離的通訊標記。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: davidortinau
ms.author: daortin
ms.date: 09/25/2017
ms.openlocfilehash: 17f5caa7849b076fd8ac2b7a22459fbf8fed1c9d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432794"
---
# <a name="core-nfc-in-xamarinios"></a>Xamarin 中的核心 NFC

_使用 iOS 11 讀取近乎現場的通訊 (NFC) 標記_

CoreNFC 是 iOS 11 中的新架構，可讓您存取 _近_ 距離無線通訊 (NFC) 收音機，以讀取應用程式內的標記。 CoreNFC 適用于 iPhone 7、iPhone 7 Plus、iPhone 8、iPhone 8 Plus、iPhone X、iPhone XS 和 iPhone 11 模型 (但 iPhone 6 和 iPhone 6 Plus 模型都有 NFC 付款功能，因此不支援 CoreNFC) 。

IOS 裝置中的 NFC 標記讀取器支援包含 _Nfc 資料交換格式_ 的所有 nfc 標記類型1到5， (NDEF) 資訊。

有一些要注意的限制：

- CoreNFC 只支援標記讀取 (不會) 寫入或格式化。
- 標記掃描必須是使用者起始的，且在60秒後的超時時間。
- 應用程式必須顯示在前景，才能進行掃描。
- CoreNFC 只能在實際裝置上進行測試， (不在模擬器) 上。

此頁面說明使用 CoreNFC 所需的設定，並示範如何使用「 [NFCTagReader」範例程式碼](/samples/xamarin/ios-samples/ios11-nfctagreader)來使用 API。

## <a name="configuration"></a>設定

若要啟用 CoreNFC，您必須在專案中設定三個專案：

- Plist 隱私權金鑰的**資訊。**
- **Plist**專案。
- 具有 **NFC 標記讀取** 功能的布建設定檔。

### <a name="infoplist"></a>Info.plist

新增 **NFCReaderUsageDescription** 的隱私權金鑰和文字，在掃描進行時對使用者顯示。 使用適用于您應用程式的訊息 (例如，說明掃描) 的用途：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

您的應用程式必須使用您權利中的下列索引鍵/值組，要求近距離無線 **通信標記讀取** 功能 **。 plist**：

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>佈建設定檔

建立新的 **應用程式識別碼** ，並確定 **NFC 標記讀取** 服務為核取：

[![開發人員入口網站新的應用程式識別碼頁面，其中已選取 NFC 標記](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

接著，您應該為此應用程式識別碼建立新的布建設定檔，然後將其下載並安裝在您的開發 Mac 上。

## <a name="reading-a-tag"></a>讀取標記

設定您的專案之後，請新增 `using CoreNFC;` 至檔案頂端，並遵循下列三個步驟來執行 NFC 標記讀取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1. 執行 `INFCNdefReaderSessionDelegate`

介面有兩個要執行的方法：

- `DidDetect` –成功讀取標記時呼叫。
- `DidInvalidate` –發生錯誤時呼叫，或達到60秒的超時時間。

#### <a name="diddetect"></a>DidDetect

在範例程式碼中，每個掃描的訊息都會新增至資料表視圖：

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

您可以多次呼叫這個方法 (如果會話允許多個標記讀取，則可以將訊息陣列傳入) 。 這是使用方法的第三個參數來設定， `Start` ([步驟 2](#step2)) 中所述。

#### <a name="didinvalidate"></a>DidInvalidate

有許多原因可能會發生失效：

- 掃描時發生錯誤。
- 應用程式不在前景中。
- 使用者選擇取消掃描。
- 應用程式已取消掃描。

下列程式碼顯示如何處理錯誤：

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

一旦會話失效，就必須建立新的會話物件才能再次掃描。

<a name="step2"></a>

### <a name="2-start-an-nfcndefreadersession"></a>2. 啟動 `NFCNdefReaderSession`

掃描應從使用者要求開始，例如按下按鈕。
下列程式碼會建立並啟動掃描會話：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

此函式的參數如下 `NFCNdefReaderSession` ：

- `delegate` –的實 `INFCNdefReaderSessionDelegate` 。 在範例程式碼中，委派會在資料表視圖控制器中執行，因此 `this` 會當做委派參數使用。
- `queue` –回呼處理的佇列。 它可以是 `null` ，在這種情況下，請務必在 `DispatchQueue.MainQueue` 更新使用者介面控制項時使用， (如範例) 所示。
- `invalidateAfterFirstRead` –何時 `true` ，掃描會在第一次成功掃描之後停止; `false` 掃描將繼續，並傳回多個結果，直到掃描取消或達到60秒的超時為止。

### <a name="3-cancel-the-scanning-session"></a>3. 取消掃描會話

使用者可以透過使用者介面中系統提供的按鈕來取消掃描會話：

![掃描時取消按鈕](corenfc-images/scan-cancel-sml.png)

應用程式可以藉由呼叫方法，以程式設計方式取消掃描 `InvalidateSession` ：

```csharp
Session.InvalidateSession();
```

在這兩種情況下， `DidInvalidate` 都會呼叫委派的方法。

## <a name="summary"></a>摘要

CoreNFC 可讓您的應用程式從 NFC 標記讀取資料。 它支援讀取各種標記格式 (NDEF 類型1到 5) ，但不支援寫入或格式化。

## <a name="related-links"></a>相關連結

- [NFCTagReader (範例) ](/samples/xamarin/ios-samples/ios11-nfctagreader)
- [介紹核心 NFC (WWDC)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/718/)