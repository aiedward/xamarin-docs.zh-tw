---
title: 交易和在 Xamarin.iOS 中的驗證
description: 本文件說明如何允許對於過去購買的產品在 Xamarin.iOS 應用程式中還原。 它也會討論如何保護購買和伺服器提供的產品。
ms.prod: xamarin
ms.assetid: 84EDD2B9-3FAA-B3C7-F5E8-C1E5645B7C77
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 83f5fd233c004271169a4d00d0a65e70aa925b95
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369085"
---
# <a name="transactions-and-verification-in-xamarinios"></a>交易和在 Xamarin.iOS 中的驗證

## <a name="restoring-past-transactions"></a>還原過去的交易

如果您的應用程式支援且可還原之產品類型，您必須包含某些使用者介面項目，可讓使用者還原這些購買項目。
這項功能可讓客戶將產品新增到其他裝置，或還原至相同裝置的產品之後被清除, 或移除並重新安裝應用程式。 下列的產品類型且可還原之各項：

-  非取用產品
-  自動更新的訂用帳戶
-  免費訂用帳戶

還原程序應該更新記錄您保留以滿足您的產品在裝置上。 客戶可以選擇在任何時間，在任何裝置上還原。 還原程序重新傳送所有先前的交易，該使用者;應用程式程式碼接著必須決定要使用該資訊 （例如，檢查如果已經有該裝置上購買的記錄，並不是，建立 購買的記錄與啟用使用者的產品） 所採取的動作。

### <a name="implementing-restore"></a>實作還原

使用者介面**還原** 按鈕會呼叫下列方法，這會在觸發 RestoreCompletedTransactions `SKPaymentQueue`。

```csharp
public void Restore()
{
   // theObserver will be notified of when the restored transactions start arriving <- AppStore
   SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions();
}
```

StoreKit 將還原要求傳送至 Apple 的伺服器以非同步的方式。   
   
因為`CustomPaymentObserver`註冊為交易的觀察者 」，它會接收訊息時，Apple 的伺服器回應。 回應會包含這位使用者曾 （跨所有裝置） 執行此應用程式中的所有交易。 程式碼執行迴圈每一筆交易中，偵測到，還原狀態，然後呼叫`UpdatedTransactions`方法來處理它，如下所示：

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

如果使用者沒有可還原的產品`UpdatedTransactions`就不會呼叫。   
   
最簡單可行的程式碼，以還原給定的交易，在此範例中會執行相同的動作，做為當購買發生，不同之處在於`OriginalTransaction`屬性用來存取產品識別碼：

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

更複雜的實作可能會檢查其他`transaction.OriginalTransaction`屬性，例如原始日期和回條的數目。 這項資訊很有用 （例如訂用帳戶） 的某些產品類型。

#### <a name="restore-completion"></a>還原完成

`CustomPaymentObserver`有兩個額外的方法會呼叫 StoreKit 完成還原程序之後 （不論成功或失敗），如下所示：

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

在範例中這些方法不執行任何動作，不過實際的應用程式可以選擇實作的訊息給使用者或一些其他功能。

## <a name="securing-purchases"></a>保護購買項目

在這兩個範例文件使用`NSUserDefaults`追蹤購買項目：   
   
 **消耗**– 點數購買 '平衡' 是簡單`NSUserDefaults`會隨著每個購買的整數值。   
   
 **非消耗**-每次相片篩選購買中索引鍵 / 值組的形式儲存`NSUserDefaults`。

使用`NSUserDefaults`會讓範例程式碼保持簡單，但不提供非常安全的解決方案，因為它可能會就技術上而言意識的使用者，可以更新 （略過的付款機制） 的設定。   
   
注意:真實世界應用程式應該採用安全的機制，以便儲存購買並不受限於使用者竄改的內容。 這可能是加密和/或其他技術，包括遠端伺服器驗證。   
   
 機制也應該設計為充分利用內建的備份和復原功能的 iOS、 iTunes 和 iCloud。 這可確保使用者還原備份之後其先前的購買項目將會立即提供。   
   
請參閱 Apple 的安全編碼指南如需特定 iOS 的指引。

## <a name="receipt-verification-and-server-delivered-products"></a>接收驗證和伺服器提供的產品

到目前為止此文件中的範例皆僅為應用程式直接與應用程式存放區伺服器通訊以進行購買交易，解除鎖定功能或已編碼至應用程式的功能。   
   
Apple 允許購買另一部伺服器，很適合用來驗證要求傳遞購買的數位內容之前所要獨立驗證的回條來提供額外購買的安全性層級 (例如數位書籍或magazine)。   
   
 **內建的產品**– 這份文件中的範例，例如所購買的產品則是隨附於應用程式的功能形式存在。 在應用程式內購買可讓使用者存取的功能。
產品識別碼是硬式編碼。   
   
 **伺服器傳遞產品**-產品包含可下載的內容，直到成功的交易造成要下載的內容儲存在遠端伺服器上。
範例可能包括書籍或雜誌的問題。 產品識別碼通常被來自外部伺服器 （其中也裝載產品內容）。 應用程式必須實作以穩健方式的記錄交易時完成，因此，如果內容下載失敗可能會重試不令人困惑使用者。

### <a name="server-delivered-products"></a>伺服器提供的產品

某些產品的內容，例如書籍和雜誌 （或甚至是遊戲的層級） 需要購買程序期間，從遠端伺服器下載。 這表示額外的伺服器是儲存和傳遞所需的產品內容在購買之後。

#### <a name="getting-prices-for-server-delivered-products"></a>取得伺服器提供產品的價格

因為遠端傳遞產品，它還有一段時間加入更多產品 （而不需要更新應用程式程式碼），例如新增更多的書籍或雜誌的新問題。 使應用程式可以探索這些新聞產品，並顯示給使用者，應該儲存額外的伺服器，並提供這項資訊。   
   
[![](transactions-and-verification-images/image38.png "取得伺服器提供產品的價格")](transactions-and-verification-images/image38.png#lightbox)   
   
1. 產品資訊必須儲存在多個位置： 在您的伺服器上，並在 iTunes Connect 中。 此外，每個產品都有與其相關聯的內容檔案。 成功的購買之後，將會傳遞這些檔案。   
   
2. 當使用者想要購買產品時，應用程式必須判斷哪些產品現已推出。 這項資訊可能會快取，但應該傳遞從遠端伺服器儲存之產品的主要清單。   
   
3. 伺服器會傳回一份来剖析的應用程式的產品識別碼。   
   
4. 接著，應用程式判斷其中一個擷取的價格和描述傳送 storekit 這些產品識別碼。   
   
5. StoreKit 會將產品 Id 的清單傳送至 Apple 的伺服器。   
   
6. ITunes 伺服器回應的有效的產品資訊 （「 描述 」 和 「 目前價格 」）。   
   
7. 應用程式的`SKProductsRequestDelegate`顯示的產品資訊傳遞給使用者。

#### <a name="purchasing-server-delivered-products"></a>購買伺服器提供的產品

因為遠端伺服器需要驗證內容的要求有效的某種方式 (亦即。 已支付)，一起傳遞回條資訊來進行驗證。 遠端伺服器轉送該資料至 iTunes 進行驗證，而如果成功，則在對應用程式的回應中包含產品內容。   
   
 [![](transactions-and-verification-images/image39.png "購買伺服器提供的產品")](transactions-and-verification-images/image39.png#lightbox)   
   
1. 應用程式會新增`SKPayment`至佇列。 如有必要，使用者會提示您輸入其 Apple ID，並要求您確認付款。   
   
2. StoreKit 會將要求傳送至伺服器進行處理。   
   
3. 交易完成時，伺服器會回應交易回條。   
   
4. `SKPaymentTransactionObserver`子類別會收到回條，並加以處理。 因為必須從伺服器下載產品，應用程式會起始遠端伺服器的網路要求。   
   
5. 下載要求會伴隨的接收資料，以便在遠端伺服器可以驗證它已被授權存取內容。 應用程式的網路用戶端等候此要求的回應。   
   
6. 當伺服器收到要求的內容時，它會剖析出回條的資料，並傳送要求，直接 iTunes 伺服器以確認回條是有效的交易。 伺服器應使用某些邏輯來判斷是否要將要求傳送至生產或沙箱的 URL。 Apple 建議一律使用生產環境 URL，並切換至沙箱，如果您收到狀態 21007 （沙箱回條傳送到實際執行伺服器）。 請參閱 Apple 的[收據驗證程式設計指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)如需詳細資訊。
   
7. iTunes 會檢查回條，並傳回狀態零，它是否有效。   
   
8. 伺服器等候 iTunes 的回應。 如果收到有效的回應，程式碼應該找出相關聯的產品內容檔案，以便納入應用程式的回應。   
  
9. 應用程式會接收並剖析的回應，裝置的檔案系統中儲存的產品內容。   
   
10. 應用程式可讓產品，然後再呼叫 StoreKit 的`FinishTransaction`。 應用程式可能然後選擇性地顯示購買的內容 （例如，購買的書籍或雜誌的問題顯示第一個頁面）。

針對非常大型產品內容檔案的替代實作可能涉及只儲存在步驟 9 中交易的回條，以便可以快速地完成交易，並提供使用者下載實際的產品內容的使用者介面在稍後的時間。 後續的下載要求可以重新傳送預存的回條，存取所需的產品的內容檔案。

### <a name="writing-server-side-receipt-verification-code"></a>撰寫伺服器端接收驗證碼

驗證伺服器端程式碼中的回條可以透過簡單 HTTP POST 要求/回應包含步驟 #5 到 #8 中的工作流程圖表。   
   
擷取`SKPaymentTansaction.TransactionReceipt`應用程式中的屬性。 這是要傳送至 iTunes 進行驗證 （步驟 5） 需要的資料。

Base64 編碼的交易接收資料 （無論是在步驟 #5 或 6）。

建立簡單的 JSON 承載，像這樣：

```csharp
{
   "receipt-data" : "(base-64 encoded receipt here)"
}
```

HTTP POST 至 JSON [ https://buy.itunes.apple.com/verifyReceipt ](https://buy.itunes.apple.com/verifyReceipt)用於生產環境或[ https://sandbox.itunes.apple.com/verifyReceipt ](https://sandbox.itunes.apple.com/verifyReceipt)進行測試。   
   
 JSON 回應會包含下列索引鍵：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt repeated here) }
}
```

狀態為零則表示有效的回條。 您的伺服器可以繼續完成購買的產品內容。 接收索引鍵包含相同的屬性的 JSON 字典`SKPaymentTransaction`收到的應用程式，因此伺服器程式碼可以查詢來擷取資訊，例如購買的數量和 product_id 這個字典的物件。

請參閱 Apple[收據驗證程式設計指南](https://developer.apple.com/library/archive/releasenotes/General/ValidateAppStoreReceipt/Introduction.html)文件的其他資訊。
