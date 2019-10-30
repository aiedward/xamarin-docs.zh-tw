---
title: 在 Xamarin 中的交易和驗證
description: 本檔說明如何允許在 Xamarin iOS 應用程式中還原過去的購買專案。 它也會討論保護購買和伺服器交付產品的方式。
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d92de14dc42f7c20a1f25b6454623c7ad4441e8a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032291"
---
# <a name="transactions-and-verification-in-xamarinios"></a>在 Xamarin 中的交易和驗證

## <a name="restoring-past-transactions"></a>還原過去的交易

如果您的應用程式支援可還原的產品類型，您必須包含某些使用者介面元素，才能讓使用者還原這些購買專案。
此功能可讓客戶將產品新增至其他裝置，或在抹除清除或移除並重新安裝應用程式之後，將產品還原至相同的裝置。 以下是可還原的產品類型：

- 非可耗用產品
- 自動可續訂訂閱
- 免費訂閱

還原程式應該會更新您在裝置上保留的記錄，以滿足您的產品。 客戶可以在任何裝置上隨時選擇還原。 還原程式會重新傳送該使用者所有先前的交易;接著，應用程式代碼必須決定要對該資訊採取什麼動作（例如，檢查裝置上是否已有該購買的記錄，如果沒有，則建立購買的記錄並為使用者啟用產品）。

### <a name="implementing-restore"></a>執行還原

[使用者介面**還原**] 按鈕會呼叫下列方法，這會在 `SKPaymentQueue`上觸發 RestoreCompletedTransactions。

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit 會以非同步方式將還原要求傳送至 Apple 的伺服器。   
   
因為 `CustomPaymentObserver` 會註冊為交易觀察者，所以當 Apple 的伺服器回應時，就會收到訊息。 回應會包含此使用者曾在此應用程式中執行的所有交易（橫跨其所有裝置）。 程式碼會逐一查看每個交易，偵測還原狀態並呼叫 `UpdatedTransactions` 方法來處理它，如下所示：

```csharp
// called when the transaction status is updated
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
   foreach (SKPaymentTransaction transaction in transactions)
   {
       switch (transaction.TransactionState)
       {
       case SKPaymentTransactionState.Purchased:
          theManager.CompleteTransaction(transaction);
           break;
       case SKPaymentTransactionState.Failed:
          theManager.FailedTransaction(transaction);
           break;
       case SKPaymentTransactionState.Restored:
           theManager.RestoreTransaction(transaction);
           break;
default:
           break;
       }
   }
}
```

如果使用者沒有可還原的產品，則不會呼叫 `UpdatedTransactions`。   
   
在範例中還原指定交易最簡單的可能程式碼，會執行與購買時相同的動作，但 `OriginalTransaction` 屬性是用來存取產品識別碼：

```csharp
public void RestoreTransaction (SKPaymentTransaction transaction)
{
   // Restored Transactions always have an 'original transaction' attached
   var productId = transaction.OriginalTransaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId); // it's as though it was purchased again
   FinishTransaction(transaction, true);
}
```

更複雜的執行可能會檢查其他 `transaction.OriginalTransaction` 屬性，例如原始日期和收據號碼。 此資訊適用于某些產品類型（例如訂用帳戶）。

#### <a name="restore-completion"></a>還原完成

`CustomPaymentObserver` 有兩個其他方法，會在還原程式完成（成功或失敗）時由 StoreKit 呼叫，如下所示：

```csharp
public override void PaymentQueueRestoreCompletedTransactionsFinished (SKPaymentQueue queue)
{
   Console.WriteLine(" ** RESTORE Finished ");
}
public override void RestoreCompletedTransactionsFailedWithError (SKPaymentQueue queue, NSError error)
{
   Console.WriteLine(" ** RESTORE FailedWithError " + error.LocalizedDescription);
}
```

在範例中，這些方法不會執行任何動作，但實際的應用程式可能會選擇對使用者或其他功能執行訊息。

## <a name="securing-purchases"></a>保護購買

本檔中的兩個範例使用 `NSUserDefaults` 來追蹤購買：   
   
 **耗材**–信用額度購買的「餘額」是簡單的 `NSUserDefaults` 整數值，會隨著每次購買而增加。   
   
 **非耗材**–每個相片篩選購買都會以索引鍵/值組的形式儲存在 `NSUserDefaults`中。

使用 `NSUserDefaults` 可讓範例程式碼變得簡單，但不提供非常安全的解決方案，因為技術上意識的使用者可能會更新設定（略過付款機制）。   
   
注意：實際應用程式應該採用安全的機制，來儲存不受使用者篡改的已購買內容。 這可能牽涉到加密及（或）其他技術，包括遠端伺服器驗證。   
   
 此機制也應該設計成利用 iOS、iTunes 和 iCloud 的內建備份和復原功能。 這可確保在使用者還原備份之後，其先前的購買將會立即可用。   
   
如需更多 iOS 特定指導方針，請參閱 Apple 的安全程式碼撰寫指南。

## <a name="receipt-verification-and-server-delivered-products"></a>收據驗證和伺服器提供的產品

到目前為止，本檔中的範例只包含應用程式直接與 App Store 伺服器通訊，以進行購買交易，以解除鎖定應用程式中已編碼的功能或功能。   
   
Apple 藉由允許另一部伺服器獨立驗證購買收據，提供額外的購買安全性等級，這對於在購買時傳遞數位內容（例如數位書籍或雜誌）。   
   
 **內建產品**-如同本檔中的範例，所購買的產品會以應用程式隨附的功能形式存在。 應用程式內購買可讓使用者存取功能。
產品識別碼已硬式編碼。   
   
 **伺服器提供的產品**–產品包含儲存在遠端伺服器上的可下載內容，直到成功的交易導致下載內容為止。
範例可能包括書籍或雜誌的問題。 產品識別碼通常來自外部伺服器（也就是產品內容的主控位置）。 當交易完成時，應用程式必須執行一種健全的記錄方式，如此一來，如果內容下載失敗，則可能會重新嘗試，而不會讓使用者混淆。

### <a name="server-delivered-products"></a>伺服器提供的產品

某些產品的內容，例如書籍和雜誌（或甚至是遊戲層級），必須在購買過程中從遠端伺服器下載。 這表示在購買產品內容之後，需要額外的伺服器來儲存及傳遞。

#### <a name="getting-prices-for-server-delivered-products"></a>取得伺服器交付產品的價格

由於產品已從遠端傳遞，因此您也可以在一段時間內新增更多產品（不需更新應用程式代碼），例如新增更多書籍或雜誌的新問題。 為了讓應用程式能夠探索這些新聞產品並向使用者顯示，其他伺服器應該儲存並傳遞這項資訊。   
   
[![](transactions-and-verification-images/image38.png "Getting Prices for Server-Delivered Products")](transactions-and-verification-images/image38.png#lightbox)   
   
1. 產品資訊必須儲存在多個位置：在您的伺服器上和 iTunes Connect。 此外，每個產品都有相關聯的內容檔案。 這些檔案會在成功購買後傳遞。   
   
2. 當使用者想要購買產品時，應用程式必須判斷有哪些產品可供使用。 這項資訊可以快取，但應從儲存主要產品清單的遠端伺服器傳遞。   
   
3. 伺服器會傳回要剖析之應用程式的產品識別碼清單。   
   
4. 然後，應用程式會決定要將哪些產品識別碼傳送給 StoreKit，以取得價格和描述。   
   
5. StoreKit 會將產品識別碼清單傳送至 Apple 的伺服器。   
   
6. ITunes 伺服器會以有效的產品資訊（描述和目前價格）來回應。   
   
7. 應用程式的 `SKProductsRequestDelegate` 會將顯示的產品資訊傳遞給使用者。

#### <a name="purchasing-server-delivered-products"></a>購買伺服器提供的產品

由於遠端伺服器需要某種方式來驗證內容要求是否有效（即已針對付費），因此會傳遞回條資訊以進行驗證。 遠端伺服器會將該資料轉送到 iTunes 進行驗證，如果成功，則會在應用程式的回應中包含產品內容。   
   
 [![](transactions-and-verification-images/image39.png "Purchasing Server-Delivered Products")](transactions-and-verification-images/image39.png#lightbox)   
   
1. 應用程式會將 `SKPayment` 新增至佇列。 如有需要，系統會提示使用者輸入其 Apple ID，並要求您確認付款。   
   
2. StoreKit 會將要求傳送至伺服器以進行處理。   
   
3. 當交易完成時，伺服器會以交易回條回應。   
   
4. `SKPaymentTransactionObserver` 子類別會接收回條並加以處理。 因為產品必須從伺服器下載，所以應用程式會對遠端伺服器起始網路要求。   
   
5. 下載要求會伴隨回條資料，讓遠端伺服器可以驗證它是否已獲授權存取內容。 應用程式的網路用戶端會等候此要求的回應。   
   
6. 當伺服器收到內容要求時，它會剖析回條資料，並將要求直接傳送至 iTunes 伺服器，以確認回條是否為有效的交易。 伺服器應該使用某些邏輯來判斷是否要將要求傳送至生產或沙箱 URL。 Apple 建議一律使用生產 URL，並在您的接收狀態21007（沙箱回條已傳送至實際伺服器）時切換至沙箱。 如需詳細資訊，請參閱 Apple 的[收據驗證程式設計指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)。
   
7. iTunes 會檢查回條並傳回零的狀態（如果有效）。   
   
8. 伺服器會等候 iTunes 的回應。 如果它收到有效的回應，程式碼應該會找出相關聯的產品內容檔案，以包含在應用程式的回應中。   
  
9. 應用程式會接收並剖析回應，並將產品內容儲存至裝置的檔案系統。   
   
10. 應用程式會啟用產品，然後呼叫 StoreKit 的 `FinishTransaction`。 然後，應用程式可能會選擇性地顯示已購買的內容（例如，顯示所購買書籍或雜誌問題的第一頁）。

非常大型產品內容檔案的替代執行方式，可能只需要將交易回條儲存在步驟 #9，讓交易能夠快速完成，並提供使用者介面讓使用者下載實際的產品內容稍後的時間。 後續下載要求可以重新傳送儲存的回條，以存取所需的產品內容檔案。

### <a name="writing-server-side-receipt-verification-code"></a>撰寫伺服器端接收驗證碼

在伺服器端程式碼中驗證回條可以使用簡單的 HTTP POST 要求/回應來完成，其中包含在工作流程圖表中 #8 的步驟 #5。   
   
將應用程式中的 `SKPaymentTansaction.TransactionReceipt` 屬性解壓縮。 這是需要傳送至 iTunes 以進行驗證的資料（步驟 #5）。

將交易回條資料（在步驟 #5 或 #6）編碼為 Base64。

建立簡單的 JSON 承載，如下所示：

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP 會將 JSON 張貼到[https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt) ，以供生產或[https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt)進行測試。   
   
 JSON 回應將包含下列索引鍵：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

狀態為零表示有效的回條。 您的伺服器可以繼續完成所購買產品的內容。 回條金鑰包含 JSON 字典，其屬性與應用程式所接收的 `SKPaymentTransaction` 物件相同，因此伺服器程式碼可以查詢此字典來抓取資訊，例如 product_id 和購買數量。

如需其他資訊，請參閱 Apple 的[收據驗證程式設計指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html)檔。
