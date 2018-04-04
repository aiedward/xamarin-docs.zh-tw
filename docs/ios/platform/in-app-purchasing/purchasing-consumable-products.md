---
title: 您可以使用產品的購買
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 5c2c84c044ff41cced2c97e414502faff45341ec
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="purchasing-consumable-products"></a>您可以使用產品的購買

您可以使用的產品是最簡單的方式實作，因為沒有 'restore' 的需求。 它們可用於產品，例如遊戲中貨幣或單次使用一項功能。 使用者重新可以一次購買您可以使用產品 over 和-移轉。

## <a name="built-in-product-delivery"></a>內建產品傳遞

隨附的這份文件的程式碼範例會示範內建的產品，產品識別碼是在應用程式的硬式編碼，因為它們會緊密結合 '解除鎖定' payment 之後的 「 功能的程式碼。 採購程序可以視覺化像這樣：   
   
[![採購程序視覺效果](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 基本工作流程是︰   
   
   
   
 1. 應用程式將`SKPayment`至佇列。 如果需要使用者將會提示您輸入其 Apple ID，並要求確認付款。   
   
 2. StoreKit 將要求傳送至伺服器進行處理。   
   
 3. 交易完成時，伺服器會回應交易回條。   
   
 4. `SKPaymentTransactionObserver`收到回條子類別，然後加以處理。   
   
 5. 應用程式可讓產品 (藉由更新`NSUserDefaults`或其他一些機制)，然後再呼叫 StoreKit 的`FinishTransaction`。

沒有其他類型的工作流程 – *Server-Delivered 產品*– 也就是在文件稍後討論 (請參閱節*回條確認和 Server-Delivered 產品*)。

## <a name="consumable-products-example"></a>您可以使用的產品範例

[InAppPurchaseSample 程式碼](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含名為專案*消耗*實作基本遊戲中貨幣 （稱為 「 猴子信用額度"）。 此範例示範如何實作兩個應用程式內購買產品，以允許使用者購買許多"猴子信用額度 」 在他們希望的位置 – 實際的應用程式中，也會花費它們的方法，因為 ！   
   
   
   
 這些螢幕擷取畫面會顯示應用程式 – 每項採購將多個 「 猴子信用額度"加入至使用者的平衡：   
   
   
   
 [![每項採購新增更多猴子信用額度使用者平衡](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 StoreKit 和應用程式存放區的自訂類別之間的互動看起來像這樣：   
   
   
   
 [![StoreKit 和應用程式存放區的自訂類別之間的互動](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>ViewController 方法

屬性和擷取產品資訊所需的方法，除了檢視控制器還需要其他通知觀察器，以接聽採購相關通知。 這些只是`NSObjects`，將註冊並移除`ViewWillAppear`和`ViewWillDisappear`分別。

```csharp
NSObject succeededObserver, failedObserver;
```

建構函式也會建立`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`)，接著建立並登錄`SKPaymentTransactionObserver`( `CustomPaymentObserver`)。   
   
   
   
 處理應用程式內購買交易的第一個部分是處理按鈕按下，當使用者希望購買某樣東西，範例應用程式的下列程式碼所示：

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 使用者介面的第二部分會處理通知的交易成功，在此情況下藉由更新顯示的平衡：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

如果因故取消交易，則使用者介面的最後一個部分會顯示訊息。 在範例程式碼 [輸出] 視窗只會寫入一則訊息：

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

除了檢視控制器上的這些方法，您可以使用的產品購買交易也需要的程式碼上`SKProductsRequestDelegate`和`SKPaymentTransactionObserver`。

### <a name="inapppurchasemanager-methods"></a>InAppPurchaseManager 方法

範例程式碼會實作一個數字的購買相關 InAppPurchaseManager 類別方法，包括`PurchaseProduct`建立方法`SKPayment`執行個體，並將它加入處理佇列：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

加入佇列的付款是非同步作業。 StoreKit 處理交易，並將它傳送至 Apple 的伺服器時，應用程式重新取得控制。 它現在是該 iOS 會確認使用者登入應用程式市集，並提示輸入其 Apple ID 和密碼所需。   
   
   
   
 假設使用者已成功向應用程式市集，並同意交易`SKPaymentTransactionObserver`會收到 StoreKit 的回應，並呼叫下列方法來完成交易和最終處理它。

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

最後一個步驟是確定您通知 StoreKit，您已成功呼叫符合交易，請確定`FinishTransaction`:

```csharp
public void FinishTransaction(SKPaymentTransaction transaction, bool wasSuccessful)
{
   // remove the transaction from the payment queue.
   SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);  // THIS IS IMPORTANT - LET'S APPLE KNOW WE'RE DONE !!!!
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {transaction},new NSObject[] {new NSString("transaction")});
       if (wasSuccessful) {
           // send out a notification that we've finished the transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionSucceededNotification, this, userInfo);
       } else {
           // send out a notification for the failed transaction
           NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerTransactionFailedNotification, this, userInfo);
       }
   }
}
```

傳送產品，一旦`SKPaymentQueue.DefaultQueue.FinishTransaction`必須呼叫付款佇列中移除交易。

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>SKPaymentTransactionObserver (CustomPaymentObserver) 方法

StoreKit 呼叫`UpdatedTransactions`方法，當它收到來自 Apple 的伺服器的回應，並將陣列傳遞`SKPaymentTransaction`檢查您的程式碼的物件。 方法執行迴圈，每一筆交易，並執行 （如此處所示） 為基礎的交易狀態的不同函數：

```csharp
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
           default:
               break;
       }
   }
}
```

`CompleteTransaction`本節稍早涵蓋方法 – 它會將採購單詳細資料，以儲存`NSUserDefaults`終結 StoreKit 具有的交易，最後通知更新 UI。

### <a name="purchasing-multiple-products"></a>購買多個產品

如果是合理購買多個產品的應用程式中，使用`SKMutablePayment`類別並設定 [數量] 欄位：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

處理已完成的交易，程式碼也必須查詢正常完成購買的數量屬性：

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   var qty = transaction.Payment.Quantity;
   if (productId == ConsumableViewController.Buy5ProductId)
       CreditManager.Add(5 * qty);
   else if (productId == ConsumableViewController.Buy10ProductId)
       CreditManager.Add(10 * qty);
   else
       Console.WriteLine ("Shouldn't happen, there are only two products");
   FinishTransaction(transaction, true);
}
```

在使用者購買多個數量，StoreKit 確認警示將會反映出數量、 單價和總價他們將需要支付費用，如下列螢幕擷取畫面所示：

[![確認購買](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>處理網路中斷

在應用程式內購買需要 StoreKit 與 Apple 伺服器進行的工作網路連線。 如果無法使用網路連線，然後應用程式內購買將無法使用。

### <a name="product-requests"></a>產品要求

如果網路無法使用時進行`SKProductRequest`、`RequestFailed`方法`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`) 會被呼叫，如下所示：

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   using (var pool = new NSAutoreleasePool()) {
       NSDictionary userInfo = NSDictionary.FromObjectsAndKeys(new NSObject[] {error},new NSObject[] {new NSString("error")});
       // send out a notification for the failed transaction
       NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerRequestFailedNotification, this, userInfo);
   }
}
```

然後 ViewController 會接聽通知，並顯示訊息，以購買按鈕：

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

因為網路連線可能是暫時性的行動裝置上，應用程式可能會想要監視使用 SystemConfiguration framework 中，網路狀態，並再試一次，網路連線可用時。 請參閱 Apple，或使用它。

### <a name="purchase-transactions"></a>購買交易

StoreKit 付款佇列會儲存和轉寄的訂單要求的話，請讓網路中斷的影響而異取決於在採購程序期間的網路失敗時。   
   
   
   
 如果在交易期間，會發生錯誤`SKPaymentTransactionObserver`子類別 ( `CustomPaymentObserver`) 會有`UpdatedTransactions`方法呼叫和`SKPaymentTransaction`類別將為 「 失敗 」 狀態。

```csharp
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
           default:
               break;
       }
   }
}
```

`FailedTransaction`方法可偵測的錯誤是否是因為使用者取消作業，如下所示：

```csharp
public void FailedTransaction (SKPaymentTransaction transaction)
{
   //SKErrorPaymentCancelled == 2
   if (transaction.Error.Code == 2) // user cancelled
       Console.WriteLine("User CANCELLED FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   else // error!
       Console.WriteLine("FailedTransaction Code=" + transaction.Error.Code + " " + transaction.Error.LocalizedDescription);
   FinishTransaction(transaction,false);
}
```

即使交易失敗，`FinishTransaction`必須呼叫方法來移除付款佇列的交易：

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

範例程式碼，以便 ViewController 可以顯示一則訊息，然後傳送通知。 應用程式不應該顯示另一個訊息，如果使用者已取消交易。 可能會發生其他錯誤碼包括：

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>處理限制

**設定 > 一般 > 限制**iOS 的功能可讓使用者鎖定其裝置的特定功能。   
   
   
   
 您可以查詢是否允許使用者在應用程式內購買，透過`SKPaymentQueue.CanMakePayments`方法。 如果傳回 false 的使用者無法存取應用程式內購買。 如果嘗試購買 StoreKit 自動會向使用者顯示錯誤訊息。 藉由檢查此值應用程式可以改為隱藏 [購買] 按鈕，或採取其他動作可協助使用者。   
   
   
   
 在`InAppPurchaseManager.cs`檔案`CanMakePayments`方法會包裝 StoreKit 函式，如下：

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

若要測試此方法，請使用**限制**功能停用 iOS**應用程式內購買**:   
   
   
   
 [![使用 iOS 的限制功能停用應用程式內購買](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 此範例程式碼`ConsumableViewController`反應`CanMakePayments`傳回 false，藉由顯示**AppStore 停用**上已停用按鈕的文字。

```csharp
// only if we can make payments, request the prices
if (iap.CanMakePayments()) {
   // now go get prices, if we don't have them already
   if (!pricesLoaded)
       iap.RequestProductData(products); // async request via StoreKit -> App Store
} else {
   // can't make payments (purchases turned off in Settings?)
   // the buttons are disabled by default, and only enabled when prices are retrieved
   buy5Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
   buy10Button.SetTitle ("AppStore disabled", UIControlState.Disabled);
}
```

應用程式看起來像時機**應用程式內購買**功能是限制 – 購買按鈕已停用。   
   
   
   
 [![應用程式看起來像這樣，當功能是在應用程式購買限制的購買按鈕已停用](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

產品資訊仍然可以要求時機`CanMakePayments`為 false，所以應用程式仍可以擷取及顯示價格。 這表示如果我們移除了`CanMakePayments`核取，還是希望採購按鈕的程式碼在作用中，不過當您嘗試在購買時使用者會看到一則訊息，**不允許在應用程式內購買**（由 StoreKit 所產生付款佇列存取時）：   
   
   
   
 [![不允許在應用程式內購買](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 實際的應用程式可能需要不同的方法，才能處理限制，例如完全隱藏按鈕，並可能提供更詳細的訊息比 StoreKit 自動顯示的警示。

