---
title: Xamarin 中的核心 NFC
description: 本檔說明如何使用 iOS 11 中引進的 Api，在 Xamarin 中讀取近距離無線通訊標記。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: conceptdev
ms.author: crdun
ms.date: 09/25/2017
ms.openlocfilehash: 6da32e22fbdb3b5b7d96d7ee93c2f25bba84cd78
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286617"
---
# <a name="core-nfc-in-xamarinios"></a>Xamarin 中的核心 NFC

_使用 iOS 11 讀取近距離無線通訊（NFC）標記_

CoreNFC 是 iOS 11 中的新架構，可讓您存取_近_距離無線通訊（NFC）廣播，以讀取應用程式內的標記。 其適用于 iPhone 7、7加、8、8 Plus 和 X。

IOS 裝置中的 NFC 標記讀取器支援所有 NFC 標記類型1到5，其中包含_NFC 資料交換格式_（NDEF）資訊。

有一些限制需要注意：

- CoreNFC 只支援標記讀取（不寫入或格式化）。
- 標記掃描必須是使用者起始的，而且在60秒之後就會超時。
- 應用程式必須在前景中顯示以進行掃描。
- CoreNFC 只能在實際裝置（而不是在模擬器上）上進行測試。

此頁面說明使用 CoreNFC 所需的設定，並說明如何使用「 [NFCTagReader」範例程式碼](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader)來使用 API。

## <a name="configuration"></a>組態

若要啟用 CoreNFC，您必須在專案中設定三個專案：

- **Plist**隱私權金鑰。
- **Plist**專案。
- 具有**NFC 標記讀取**功能的布建設定檔。

### <a name="infoplist"></a>Info.plist

新增**NFCReaderUsageDescription**的隱私權金鑰和文字，這會在掃描進行時顯示給使用者。 使用適用于您應用程式的訊息（例如，說明掃描的用途）：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

您的應用程式必須使用您權利中的下列索引鍵/值組，要求**近端欄位通訊標記讀取**功能 **。 plist**：

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>佈建設定檔

建立新的**應用程式識別碼**，並確定**NFC 標記讀取**服務已核取：

[![已選取 NFC 標記的開發人員入口網站新應用程式識別碼頁面](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

接著，您應該為此應用程式識別碼建立新的布建設定檔，然後將它下載並安裝在您的開發 Mac 上。

## <a name="reading-a-tag"></a>讀取標記

一旦設定您的專案，請`using CoreNFC;`將新增至檔案頂端，並遵循下列三個步驟來執行 NFC 標記讀取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1.實作`INFCNdefReaderSessionDelegate`

介面有兩個要執行的方法：

- `DidDetect`–在成功讀取標記時呼叫。
- `DidInvalidate`–在發生錯誤或達到60秒的超時時呼叫。

#### <a name="diddetect"></a>DidDetect

在範例程式碼中，每個掃描的訊息都會加入至資料表視圖：

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

如果會話允許多個標記讀取，則可以多次呼叫此方法（以及可傳入的訊息陣列）。 這是使用`Start`方法的第三個參數來設定（如[步驟 2](#step2)中所述）。

#### <a name="didinvalidate"></a>DidInvalidate

有一些原因會導致失效：

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

會話失效之後，必須建立新的會話物件才能再次掃描。

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2.啟動`NFCNdefReaderSession`

掃描應該以使用者要求開始，例如按下按鈕。
下列程式碼會建立並啟動掃描會話：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

此`NFCNdefReaderSession`函數的參數如下所示：

- `delegate`–的執行`INFCNdefReaderSessionDelegate`。 在範例程式碼中，委派是在資料表視圖控制器中執行，因此`this`會當做委派參數使用。
- `queue`–回呼處理所在的佇列。 它可以是`null`，在這種情況下，請務必`DispatchQueue.MainQueue`在更新使用者介面控制項時使用（如範例所示）。
- `invalidateAfterFirstRead`-若為`false` ，掃描會在第一次成功掃描後停止;當掃描會繼續，並傳回多個結果，直到掃描取消或達到60秒的`true`超時值為止。


### <a name="3-cancel-the-scanning-session"></a>3.取消掃描會話

使用者可以透過使用者介面中系統提供的按鈕來取消掃描會話：

![掃描時取消按鈕](corenfc-images/scan-cancel-sml.png)

應用程式可以藉由呼叫`InvalidateSession`方法，以程式設計方式取消掃描：

```csharp
Session.InvalidateSession();
```

在這兩種情況下， `DidInvalidate`都會呼叫委派的方法。

## <a name="summary"></a>總結

CoreNFC 可讓您的應用程式從 NFC 標記讀取資料。 它支援讀取各種標記格式（NDEF 類型1到5），但不支援寫入或格式化。


## <a name="related-links"></a>相關連結

- [NFCTagReader （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-nfctagreader)
- [核心 NFC 簡介（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/718/)
