---
title: 購買可取用產品在 Xamarin.iOS 中
description: 本文件說明在 Xamarin.iOS 中的可取用產品。 可取用產品是單次使用一些功能，例如遊戲中的貨幣。
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b55465a700974e0ce5ceb8893d96311d920e04ae
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123644"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>購買可取用產品在 Xamarin.iOS 中

可取用產品是最容易實作，因為沒有 'restore' 的需求。 它們可用於遊戲中貨幣或單次使用一種功能等產品項目。 使用者也可以重新購買可取用產品移轉和-移轉一次。

## <a name="built-in-product-delivery"></a>內建的產品推出

隨附的這份文件的程式碼範例示範內建的產品，產品識別碼是硬式編碼到應用程式，因為緊密結合的程式碼，'解除鎖定' 付款後的功能。 購買程序可以視覺化像這樣：   
   
[![購買程序視覺效果](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 基本工作流程是：   
   
 1. 應用程式會新增`SKPayment`至佇列。 如有必要，使用者會提示您輸入其 Apple ID，並要求您確認付款。   
   
 2. StoreKit 會將要求傳送至伺服器進行處理。   
   
 3. 交易完成時，伺服器會回應交易回條。   
   
 4. `SKPaymentTransactionObserver`子類別會收到回條，並加以處理。   
   
 5. 應用程式可讓產品 (藉由更新`NSUserDefaults`或其他一些機制)，然後呼叫 StoreKit 的`FinishTransaction`。

還有另一種工作流程 – *Server-Delivered 產品*– 也就是文件稍後所述 (請參閱節*收據驗證和 Server-Delivered 產品*)。

## <a name="consumable-products-example"></a>需求的可取用的產品範例

[InAppPurchaseSample 程式碼](https://developer.xamarin.com/samples/monotouch/StoreKit/)包含名為專案*消耗*實作基本遊戲中貨幣 （稱為 「 monkey 信用額度"）。 此範例示範如何實作兩個應用程式內購買產品，以允許使用者購買，許多 「 monkey 點數 」 他們希望的位置 – 在實際的應用程式，也會花費它們某種方式 ！   
   
   
   
 應用程式會顯示在這些螢幕擷取畫面-每次購買多個 「 monkey 點數 」 加入使用者的平衡：   
   
   
   
 [![每一次購買使用者平衡新增更多的 monkey 信用額度](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   
   
   
   
 StoreKit 和應用程式市集的自訂類別之間的互動如下所示：   
   
   
   
 [![StoreKit 和應用程式市集的自訂類別之間的互動](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

&nbsp;

### <a name="viewcontroller-methods"></a>ViewController 方法

除了屬性和擷取產品資訊所需的方法，將檢視控制器會需要接聽採購相關通知的其他通知觀察者。 這些只是`NSObjects`，將會註冊並移除`ViewWillAppear`和`ViewWillDisappear`分別。

```csharp
NSObject succeededObserver, failedObserver;
```

建構函式也會建立`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`)，接著會建立並註冊`SKPaymentTransactionObserver`( `CustomPaymentObserver`)。   
   
   
   
 處理應用程式內購買交易的第一個部分是處理按下按鈕，當使用者想要購買某樣東西，範例應用程式的下列程式碼所示：

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

   
   
 使用者介面的第二部分會處理通知，在交易成功，在此情況下藉由更新顯示的平衡：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

使用者介面的最後一個部分會顯示訊息，如果基於某些因素遭到取消交易。 在範例程式碼 [輸出] 視窗只會寫入一則訊息：

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

除了這些檢視控制器上的方法，可消耗的產品購買交易也需要程式碼上`SKProductsRequestDelegate`而`SKPaymentTransactionObserver`。

### <a name="inapppurchasemanager-methods"></a>InAppPurchaseManager 方法

範例程式碼會實作一個數字的購買相關的 InAppPurchaseManager 類別方法，包括`PurchaseProduct`方法，以建立`SKPayment`執行個體，並將它新增至佇列進行處理：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

新增付款至佇列是非同步作業。 StoreKit 處理交易，並將它傳送至 Apple 的伺服器時，應用程式重新取得控制項。 它現在是該 iOS 會確認使用者已登入至 App Store，並提示她的 Apple ID 和密碼，如有必要。   
   
   
   
 假設使用者已成功使用 App Store 進行驗證，並同意交易`SKPaymentTransactionObserver`會再收到 StoreKit 的回應，且在呼叫下列方法來完成交易，並完成它。

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

最後一個步驟是確定您通知 StoreKit，您便已成功地符合交易，藉由呼叫`FinishTransaction`:

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

一旦在產品推出，`SKPaymentQueue.DefaultQueue.FinishTransaction`必須呼叫以移除付款佇列中的交易。

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>SKPaymentTransactionObserver (CustomPaymentObserver) 方法

StoreKit 呼叫`UpdatedTransactions`方法，當它收到來自 Apple 的伺服器的回應，並將傳遞的陣列`SKPaymentTransaction`檢查您的程式碼的物件。 方法的每個交易中執行迴圈，並執行不同的函式會根據交易狀態 （如下所示）：

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

`CompleteTransaction`稍早在本章節中涵蓋方法 – 它會將儲存的採購單詳細資料，以`NSUserDefaults`完成具有 StoreKit 的交易，最後會通知來更新 UI。

### <a name="purchasing-multiple-products"></a>購買多個產品

如果是合理購買多個產品的應用程式中，使用`SKMutablePayment`類別，並設定 [數量] 欄位：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

處理已完成的交易的程式碼也必須查詢正確完成購買的數量屬性：

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

當使用者購買多個數量時，StoreKit 確認警示將會反映出數量、 單價和總價他們將需要支付，如下列螢幕擷取畫面所示：

[![確認購買](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>處理網路中斷

在應用程式內購買需要正常運作的網路連線的 StoreKit Apple 伺服器進行通訊。 如果無法使用的網路連線，然後應用程式內購買將無法使用。

### <a name="product-requests"></a>產品的要求

如果網路無法使用時進行`SKProductRequest`，則`RequestFailed`方法`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`) 會被呼叫，如下所示：

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

ViewController 然後接聽通知，並在 [購買] 按鈕會顯示訊息：

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

因為網路連線可能是暫時性的在行動裝置上，可能想要監視網路狀態使用 SystemConfiguration framework，應用程式，並再試一次，當網路連線可用。 請參閱 Apple 的或使用它。

### <a name="purchase-transactions"></a>購買交易

StoreKit 付款佇列會儲存和轉寄的採購單要求可能的話，因此取決於改變網路中斷的影響，會在購買程序期間的網路失敗時。   
   
   
   
 如果在交易期間，發生錯誤`SKPaymentTransactionObserver`子類別 ( `CustomPaymentObserver`) 會有`UpdatedTransactions`呼叫的方法和`SKPaymentTransaction`類別會處於 [失敗] 狀態。

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

即使交易失敗，`FinishTransaction`移除付款佇列中的交易，必須呼叫方法：

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

範例程式碼接著會傳送通知，以便 ViewController 可以顯示一則訊息。 應用程式應該不會顯示一個訊息，如果使用者已取消交易。 可能會發生其他錯誤碼包括：

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>處理限制

**設定 > 一般 > 限制**iOS 功能，可讓使用者鎖定其裝置的特定功能。   
   
   
   
 您可以查詢是否允許使用者透過應用程式內購買商品`SKPaymentQueue.CanMakePayments`方法。 如果傳回 false 的使用者無法存取應用程式內購買。 如果在嘗試購買 StoreKit 自動會向使用者顯示錯誤訊息。 藉由檢查此值您的應用程式可以改為隱藏 [購買] 按鈕，或採取其他動作來協助使用者。   
   
   
   
 在 `InAppPurchaseManager.cs`檔案`CanMakePayments`方法會包裝 StoreKit 函式，就像這樣：

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

若要測試這個方法，請使用**限制**若要停用 iOS 功能**應用程式內購買**:   
   
   
   
 [![使用 iOS 的 「 限制 」 功能停用應用程式內購買](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   
   
   
   
 此範例程式碼`ConsumableViewController`回應`CanMakePayments`傳回 false，藉由顯示**AppStore 停用**上停用的按鈕的文字。

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

應用程式看起來像這個 when**應用程式內購買**功能是限制 – 購買按鈕已停用。   
   
   
   
 [![應用程式看起來像這樣，當應用程式內購買項目功能是限制的購買按鈕已停用](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   
   
   
   

產品資訊仍可要求當`CanMakePayments`為 false，讓應用程式仍然可以擷取並顯示價格。 這表示如果我們移除了`CanMakePayments`但在嘗試購買時使用者會看到一則訊息，是作用中，從 [購買] 按鈕還是希望程式碼的核取所**不允許在應用程式內購買**（StoreKit 所產生付款佇列存取時）：   
   
   
   
 [![不允許在應用程式內購買](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   
   
   
   
 真實世界應用程式可能需要不同的方法，才能處理限制，例如完全隱藏按鈕，並可能提供更詳細的訊息比 StoreKit 自動顯示的警示。

