---
title: "交易和驗證"
ms.topic: article
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 21f8c6738c00d5738c02962ee95b415e3855d740
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="transactions-and-verification"></a>交易和驗證

## <a name="restoring-past-transactions"></a>還原過去的交易

如果您的應用程式支援且可還原之產品類型，您必須包含可讓使用者還原這些採購項某些使用者介面項目。
這項功能可讓客戶將產品加入其他裝置，或還原至相同裝置的產品之後清除要移除或重新安裝應用程式。 下列產品類型且可還原之項目：

-  非可使用產品
-  自動更新的訂閱
-  免費訂用帳戶


還原程序應該更新記錄您保留以滿足您的產品在裝置上。 客戶可以選擇在任何時間，在任何裝置上還原。 還原程序重新傳送所有先前的交易，該使用者。應用程式程式碼接著必須決定要使用該資訊 （例如，檢查如果已經在裝置上，該購買的記錄，並不是，建立的購買記錄與啟用使用者的產品） 中採取的動作。

### <a name="implementing-restore"></a>實作還原

使用者介面**還原**按鈕會呼叫下列方法，在觸發程序 RestoreCompletedTransactions `SKPaymentQueue`。

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit 會還原要求傳送至 Apple 的伺服器以非同步的方式。   
   
   
   
 因為`CustomPaymentObserver`註冊成交易觀察器，它會接收訊息時 Apple 的伺服器回應。 回應會包含這位使用者執行曾執行此應用程式中 （跨所有裝置） 的所有交易。 程式碼執行迴圈每一筆交易中，偵測到還原狀態並呼叫`UpdatedTransactions`方法來處理它，如下所示：

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

如果沒有可還原之產品的使用者，`UpdatedTransactions`則不會呼叫。   
   
   
   
 最簡單可能的程式碼，還原指定的交易，在此範例中的功能相同的動作為購買何時會發生，不同處在於`OriginalTransaction`屬性用來存取 產品識別碼：

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

更複雜的實作可能會檢查其他`transaction.OriginalTransaction`屬性，例如原始日期和回條的數目。 這項資訊會很有用 （例如訂閱） 的某些產品類型。

#### <a name="restore-completion"></a>還原完成

`CustomPaymentObserver`有兩個額外的方法會先呼叫 StoreKit 還原程序完成之後 （成功或失敗），如下所示：

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

在範例中這些方法不會執行任何動作，不過實際的應用程式也可以選擇實作的訊息給使用者或其他特定功能。

## <a name="securing-purchases"></a>保護購買項目

在這兩個範例文件使用`NSUserDefaults`追蹤購買項目：   
   
 **消耗**– 信用卡購買 '平衡' 是簡單`NSUserDefaults`會隨著每項採購的整數值。   
   
 **非消耗**– 每個相片篩選購買儲存中的索引鍵-值配對為`NSUserDefaults`。

使用`NSUserDefaults`保留範例程式碼的簡單，但不提供非常安全的解決方案，因為它可能是技術重視的使用者，可以更新 （略過付款機制） 的設定。   
   
附註： 實際的應用程式應該採用的安全機制來儲存購買不受限制使用者竄改的內容。 這可能會加密及/或其他技術，包括遠端伺服器驗證。   
   
 機制也應該設計成利用內建的備份和復原功能，iOS、 iTunes 和 icloud 的功能中。 這可確保使用者會還原備份之後先前購買行為將會立即可用。   
   
   
 請參閱 Apple 的安全編碼指南 》 以取得更多的 iOS 特定指導方針。

## <a name="receipt-verification-and-server-delivered-products"></a>回條驗證和伺服器傳遞產品

到目前為止此文件中的範例僅有組成應用程式直接與應用程式存放區伺服器通訊以進行購買交易，解除鎖定功能或已編碼至應用程式的功能。   
   
   
   
 Apple 允許獨立驗證另一部伺服器，可以用來驗證要求傳遞一部分購買的數位內容之前所購買回條來提供額外的採購安全性層級 (例如數位書籍或magazine)。   
   
   
   
 **內建產品**– 這份文件中的範例，例如所購買的產品是隨附於應用程式的功能。 在應用程式內購買可讓使用者存取此功能。
產品識別碼是硬式編碼。   
   
 **伺服器傳送產品**– 產品組成可下載直到成功的交易造成要下載的內容儲存在遠端伺服器的內容。
範例可能包括書籍或雜誌問題。 產品識別碼通常被來自外部伺服器 （其中也裝載產品內容）。 應用程式必須實作穩固地錄製當交易完成時，以便在內容下載失敗可重試而不會混淆使用者。

### <a name="server-delivered-products"></a>伺服器傳送的產品

某些產品的內容，例如書籍和雜誌 （或甚至遊戲的層級） 需要採購程序期間，從遠端伺服器下載。 這表示儲存和傳遞產品內容，它購買之後需要額外的伺服器。

#### <a name="getting-prices-for-server-delivered-products"></a>取得伺服器傳遞產品的價格

因為會從遠端傳送產品，所以也可能經過一段時間加入更多產品 （而不需要更新應用程式程式碼），例如新增更多的書籍或雜誌的新問題。 讓應用程式可以探索這些新聞產品，並顯示給使用者，應該儲存額外的伺服器，並傳遞這項資訊。   
   
   
   
 [![](transactions-and-verification-images/image38.png "取得伺服器傳遞產品的價格")](transactions-and-verification-images/image38.png#lightbox)   
   
   
   
 1. 產品資訊必須儲存在多個位置： 在您的伺服器，在 iTunes Connect 中。 此外，每個產品都有與其相關聯的內容檔案。 這些檔案會傳遞成功購買之後。   
   
 2. 當使用者想要購買產品時，應用程式必須判斷哪些產品所提供的。 這項資訊可能會快取，但應該傳遞主產品清單儲存在遠端伺服器。   
   
 3. 伺服器會傳回要剖析的應用程式的產品識別碼的清單。   
   
 4. 接著，應用程式會決定哪些要擷取的價格和描述傳送至 StoreKit 這些產品識別碼。   
   
 5. StoreKit 會將產品 Id 的清單傳送至 Apple 的伺服器。   
   
 6. ITunes 伺服器回應的有效的產品資訊 （「 描述 」 和 「 目前的價格 」）。   
   
 7. 應用程式的`SKProductsRequestDelegate`顯示的產品資訊傳遞給使用者。

#### <a name="purchasing-server-delivered-products"></a>購買伺服器傳遞產品

因為遠端伺服器需要驗證內容的要求有效的某些方面 (ie。 付費的部份)，隨著傳遞回條資訊進行驗證。 遠端伺服器轉送至 iTunes 驗證該資料，而如果成功的話，包含產品內容回應至應用程式中。   
   
 [![](transactions-and-verification-images/image39.png "購買伺服器傳遞產品")](transactions-and-verification-images/image39.png#lightbox)   
   
 1. 應用程式將`SKPayment`至佇列。 如果需要使用者將會提示您輸入其 Apple ID，並要求確認付款。   
   
 2. StoreKit 將要求傳送至伺服器進行處理。   
   
 3. 交易完成時，伺服器會回應交易回條。   
   
 4. `SKPaymentTransactionObserver`收到回條子類別，然後加以處理。 因為必須從伺服器下載產品，應用程式會起始遠端伺服器的網路要求。   
   
 5. 下載要求會伴隨的回條的資料，以便在遠端伺服器可以確認它已獲授權存取內容。 應用程式的網路用戶端等候此要求的回應。   
   
 6. 當伺服器收到的要求內容時，它會剖析出回條的資料，並傳送要求，直接 iTunes 伺服器以確認回條為有效的交易。 伺服器應該使用某個邏輯來判斷是否要將要求傳送至生產環境或沙箱 URL。 Apple 建議一律使用生產 URL，並切換至沙箱，如果狀態： 21007 （沙箱回條傳送到實際執行伺服器）-是指[技術附註 2259年常見問題集 #16](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)。   
   
 7. 將檢查回條 iTunes，並傳回狀態零，如果它是有效。   
   
 8. 伺服器等候 iTunes' 回應。 如果收到有效的回應，該程式碼應該找出要在應用程式的回應中包含的內容檔案相關聯的產品。   
   
 9. 應用程式接收並剖析的回應，將產品內容儲存至裝置的檔案系統。   
   
 10. 應用程式可讓產品，然後再呼叫 StoreKit 的`FinishTransaction`。 應用程式可能再選擇性地顯示購買的內容 （例如，購買的書籍或雜誌問題顯示第一個頁面）。

只要將儲存交易回條步驟 #9，讓交易可以迅速完成，並提供使用者下載的產品的實際內容的使用者介面，可能牽涉到替代的實作非常大的產品內容檔案在稍後的圖示。 後續的下載要求，可以將預存回條來存取必要的產品的內容檔案重新傳送。

### <a name="writing-server-side-receipt-verification-code"></a>撰寫伺服器端收到的驗證程式碼

驗證伺服器端程式碼中的回條可以使用簡易 HTTP POST 要求/回應包含步驟 #5 到工作流程圖表中的 #8。   
   
   
   
 擷取`SKPaymentTansaction.TransactionReceipt`應用程式中的屬性。 這是要傳送至 iTunes 進行驗證 （步驟 5） 需要的資料。

Base64 編碼的交易接收資料 （無論是在步驟 5 或 #6）。

建立簡單的 JSON 裝載如下：

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST 以 JSON [https://buy.itunes.apple.com/verifyReceipt](https://buy.itunes.apple.com/verifyReceipt)生產環境或[https://sandbox.itunes.apple.com/verifyReceipt](https://sandbox.itunes.apple.com/verifyReceipt)進行測試。   
   
 JSON 回應會包含下列機碼：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

狀態為零則表示有效的回條。 您的伺服器可以繼續滿足所購買的產品內容。 回條索引鍵包含相同的內容的 JSON 字典`SKPaymentTransaction`伺服端程式碼才能查詢此字典來擷取資訊，例如 product_id 及購買數量的應用程式時，收到的物件。

請參閱 Apple[驗證存放區的回條](https://developer.apple.com/library/ios/#documentation/NetworkingInternet/Conceptual/StoreKitGuide/VerifyingStoreReceipts/VerifyingStoreReceipts.html#//apple_ref/doc/uid/TP40008267-CH104-SW1)文件的其他資訊。

#### <a name="using-aspnet"></a>使用 ASP.NET

C# 開發人員沒有實用的開放原始碼專案，稱為[APNS 清晰](https://github.com/Redth/APNS-Sharp)其中包含 ASP.NET 中的回條的運作方式的驗證碼。 特別是，`Receipt.cs`和`ReceiptVerification.cs`檔案[ `Jdsoft.Apple.AppStore` ](https://github.com/Redth/APNS-Sharp/tree/master/JdSoft.Apple.AppStore)目錄可以加入至.NET 網站，以輕鬆地實作步驟 #6 到 #8 Server-Delivered 產品工作流程圖。   
   
   
   
 與 iTunes 伺服器通訊使用 JSON，輕鬆地在 C# 中處理。   
   
   
   
 請參閱 Apple 在應用程式內購買[回條驗證](https://developer.apple.com/library/ios/#releasenotes/StoreKit/IAP_ReceiptValidation/_index.html)文件的詳細資訊，如何確認回條是否有效。

### <a name="3rd-party-receipt-verification"></a>第 3 個合作對象收到的驗證

有提供回條確認 （及其他項目） 而非建立您自己的伺服器，您可以使用的平台的公司。 Xamarin 背書不保證這些服務。它們是只要此處提到的參考。

#### <a name="urban-airship"></a>都市 Airship

都市 Airship 提供數種不同的後端服務，包括驗證和推播通知的回條的 iOS 應用程式。   
   
 [http://urbanairship.com/products/in-app-purchase/](http://urbanairship.com/products/in-app-purchase/)



