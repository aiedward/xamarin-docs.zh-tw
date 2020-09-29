---
title: 購買 Xamarin 中的可取用產品
description: 本檔說明 Xamarin 中的可取用產品。 取用產品是一項功能，例如遊戲中的貨幣。
ms.prod: xamarin
ms.assetid: E0CB4A0F-C3FA-3933-58A7-13246971D677
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: d6cc17d4a3a77487689357641999525a539b442f
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435525"
---
# <a name="purchasing-consumable-products-in-xamarinios"></a>購買 Xamarin 中的可取用產品

因為沒有「還原」需求，所以可採用最簡單的取用產品。 它們適用于遊戲中的貨幣或單一使用功能的產品。 使用者可以再次重新購買取用的產品。

## <a name="built-in-product-delivery"></a>內建產品傳遞

本檔所附的範例程式碼會示範內建產品-產品識別碼會硬式編碼在應用程式中，因為它們會緊密結合在付款之後「解除鎖定」功能的程式碼。 您可以將購買程式視覺化，如下所示：   
   
[![購買流程視覺效果](purchasing-consumable-products-images/image26.png)](purchasing-consumable-products-images/image26.png#lightbox)     
   
 基本工作流程為：   
   
 1. 應用程式會將加入 `SKPayment` 至佇列。 必要時，系統會提示使用者輸入其 Apple ID，並要求您確認付款。   
   
 2. StoreKit 會將要求傳送至伺服器進行處理。   
   
 3. 當交易完成時，伺服器會回應交易回條。   
   
 4. 子 `SKPaymentTransactionObserver` 類別會接收回條並進行處理。   
   
 5. 應用程式會藉由更新或其他) 的機制來啟用產品 (`NSUserDefaults` ，然後再呼叫 StoreKit 的 `FinishTransaction` 。

另外還有另一種類型的工作流程（ *伺服器提供的產品* ），本檔稍後會討論 (參閱 *收據驗證和伺服器提供的產品*) 一節。

## <a name="consumable-products-example"></a>可耗用產品範例

[InAppPurchaseSample 程式碼](/samples/xamarin/ios-samples/storekit)包含一個名為「」的專案，其會實作為「遊戲點數」的基本「遊戲中貨幣」 () 。 *Consumables* 此範例示範如何執行兩個應用程式內購買產品，讓使用者能夠依自己的意願購買許多「猴子點數」; 在實際的應用程式中，也有一些消費的方式！   

應用程式會顯示在這些螢幕擷取畫面中，每個購買會對使用者的餘額增加更多「猴子點數」：   

 [![每次購買都會增加更多的猴子點數給使用者餘額](purchasing-consumable-products-images/image27.png)](purchasing-consumable-products-images/image27.png#lightbox)   

自訂類別、StoreKit 和 App Store 之間的互動如下所示：   

 [![自訂類別、StoreKit 和 App Store 之間的互動](purchasing-consumable-products-images/image28.png)](purchasing-consumable-products-images/image28.png#lightbox)

### <a name="viewcontroller-methods"></a>ViewController 方法

除了抓取產品資訊所需的屬性和方法之外，view controller 還需要額外的通知觀察者來接聽購買相關的通知。 這些只會 `NSObjects` 在和中分別註冊和移除 `ViewWillAppear` `ViewWillDisappear` 。

```csharp
NSObject succeededObserver, failedObserver;
```

此函式也會建立子 `SKProductsRequestDelegate` 類別 ( `InAppPurchaseManager`) 接著建立並註冊 `SKPaymentTransactionObserver` ( `CustomPaymentObserver`) 。   

處理應用程式內購買交易的第一個部分，就是在使用者想要購買某個東西時處理按鈕按下，如下列範例應用程式程式碼所示：

```csharp
buy5Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy5ProductId);
};
buy10Button.TouchUpInside += (sender, e) => {
   iap.PurchaseProduct (Buy10ProductId);
};
```

使用者介面的第二個部分是處理交易成功的通知，在此案例中，更新顯示的餘額：

```csharp
succeededObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionSucceededNotification,
(notification) => {
   balanceLabel.Text = CreditManager.Balance() + " monkey credits";
});
```

如果因為某些原因而取消交易，則使用者介面的最後部分會顯示訊息。 在範例程式碼中，訊息只會寫入至 [輸出] 視窗：

```csharp
failedObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerTransactionFailedNotification,
(notification) => {
   Console.WriteLine ("Transaction Failed");
});
```

除了 view controller 上的這些方法之外，取用產品購買交易也需要和上的程式碼 `SKProductsRequestDelegate` `SKPaymentTransactionObserver` 。

### <a name="inapppurchasemanager-methods"></a>InAppPurchaseManager 方法

範例程式碼會在 InAppPurchaseManager 類別上執行一些購買相關的方法，包括 `PurchaseProduct` 建立實例的方法， `SKPayment` 並將它新增至佇列進行處理：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKPayment payment = SKPayment.PaymentWithProduct (appStoreProductId);
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

將付款新增至佇列是非同步作業。 應用程式會在 StoreKit 處理交易時重新獲得控制權，並將其傳送至 Apple 的伺服器。 此時 iOS 會確認使用者是否已登入 App Store，並在必要時提示她輸入 Apple ID 和密碼。   

假設使用者成功驗證 App Store，並同意交易，則 `SKPaymentTransactionObserver` 會接收 StoreKit 的回應，並呼叫下列方法來完成交易並完成交易。

```csharp
public void CompleteTransaction (SKPaymentTransaction transaction)
{
   var productId = transaction.Payment.ProductIdentifier;
   // Register the purchase, so it is remembered for next time
   PhotoFilterManager.Purchase(productId);
   FinishTransaction(transaction, true);
}
```

最後一個步驟是確定您已藉由呼叫下列方法，通知 StoreKit 您已成功完成交易 `FinishTransaction` ：

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

一旦交付產品之後， `SKPaymentQueue.DefaultQueue.FinishTransaction` 必須呼叫以從付款佇列中移除交易。

### <a name="skpaymenttransactionobserver-custompaymentobserver-methods"></a>SKPaymentTransactionObserver (CustomPaymentObserver) 方法

`UpdatedTransactions`當 StoreKit 收到來自 Apple 伺服器的回應時，會呼叫方法，並傳遞物件的陣列 `SKPaymentTransaction` 供您的程式碼檢查。 方法會在每一筆交易中執行迴圈，並根據交易狀態 (執行不同的函數，如下所示) ：

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

本節稍 `CompleteTransaction` 早所述的方法，它會將購買詳細資料儲存至 `NSUserDefaults` ，使用 StoreKit 完成交易，最後通知 UI 更新。

### <a name="purchasing-multiple-products"></a>購買多個產品

如果您的應用程式有合理的購買多項產品，請使用 `SKMutablePayment` 類別並設定 Quantity 欄位：

```csharp
public void PurchaseProduct(string appStoreProductId)
{
   SKMutablePayment payment = SKMutablePayment.PaymentWithProduct (appStoreProductId);
   payment.Quantity = 4; // hardcoded as an example
   SKPaymentQueue.DefaultQueue.AddPayment (payment);
}
```

處理已完成之交易的程式碼也必須查詢 Quantity 屬性，才能正確地完成購買：

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

當使用者購買多個數量時，StoreKit 確認警示會反映數量、單價和收取的總價格，如下列螢幕擷取畫面所示：

[![確認購買](purchasing-consumable-products-images/image30.png)](purchasing-consumable-products-images/image30.png#lightbox)

## <a name="handling-network-outages"></a>處理網路中斷

應用程式內購買需要可運作的網路連線，才能讓 StoreKit 與 Apple 的伺服器通訊。 如果網路連線無法使用，則應用程式內購買將無法使用。

### <a name="product-requests"></a>產品要求

如果在進行時無法使用網路 `SKProductRequest` ，則 `RequestFailed` 會呼叫子類別的方法 `SKProductsRequestDelegate` ( `InAppPurchaseManager`) ，如下所示：

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

ViewController 接著會接聽通知，並在 [購買] 按鈕中顯示一則訊息：

```csharp
requestObserver = NSNotificationCenter.DefaultCenter.AddObserver (InAppPurchaseManager.InAppPurchaseManagerRequestFailedNotification,
(notification) => {
   Console.WriteLine ("Request Failed");
   buy5Button.SetTitle ("Network down?", UIControlState.Disabled);
   buy10Button.SetTitle ("Network down?", UIControlState.Disabled);
});
```

由於網路連線在行動裝置上可能是暫時性的，因此應用程式可能會想要使用 SystemConfiguration 架構來監視網路狀態，並在有網路連線可用時重新嘗試。 請參閱 Apple 的或使用它的。

### <a name="purchase-transactions"></a>購買交易

StoreKit 付款佇列會在可能的情況下儲存和轉寄購買要求，因此，網路中斷的影響會隨著在購買程式期間網路失敗的時間而不同。   

如果在交易期間發生錯誤，子 `SKPaymentTransactionObserver` 類別 ( `CustomPaymentObserver`) 將會 `UpdatedTransactions` 呼叫方法，而 `SKPaymentTransaction` 類別將會處於失敗狀態。

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

`FailedTransaction`方法會偵測錯誤是否由使用者取消所造成，如下所示：

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

即使交易失敗，也 `FinishTransaction` 必須呼叫方法以從付款佇列中移除交易：

```csharp
SKPaymentQueue.DefaultQueue.FinishTransaction(transaction);
```

然後，範例程式碼會傳送通知，讓 ViewController 可以顯示訊息。 如果使用者取消交易，應用程式不應該顯示額外的訊息。 可能會發生的其他錯誤代碼包括：

```csharp
FailedTransaction Code=0 Cannot connect to iTunes Store
FailedTransaction Code=5002 An unknown error has occurred
FailedTransaction Code=5020 Forget Your Password?
Applications may detect and respond to specific error codes, or handle them in the same way.
```

## <a name="handling-restrictions"></a>處理限制

IOS **> 一般 > 限制** 功能的設定，可讓使用者鎖定其裝置的某些功能。   

您可以查詢是否允許使用者透過方法進行應用程式內購買 `SKPaymentQueue.CanMakePayments` 。 如果傳回 false，則使用者無法存取應用程式內購買。 如果嘗試購買，StoreKit 會自動向使用者顯示錯誤訊息。 藉由檢查此值，您的應用程式可以改為隱藏 [購買] 按鈕，或採取其他動作來協助使用者。   

在檔案中 `InAppPurchaseManager.cs` ， `CanMakePayments` 方法會包裝 StoreKit 函式，如下所示：

```csharp
public bool CanMakePayments()
{
   return SKPaymentQueue.CanMakePayments;
}
```

若要測試此方法，請使用 iOS 的 **限制** 功能來停 **用應用程式內購買**：   

 [![使用 iOS 的限制功能來停用應用程式內購買](purchasing-consumable-products-images/image31.png)](purchasing-consumable-products-images/image31.png#lightbox)   

此範例程式碼會 `ConsumableViewController` `CanMakePayments` 在停用的按鈕上顯示 **AppStore 停用** 文字，以回應傳回 false。

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

當 **應用程式內購買** 功能受到限制時，應用程式看起來像這樣-已停用 [購買] 按鈕。   

 [![當應用程式內購買功能受限時，應用程式看起來會像是停用購買按鈕](purchasing-consumable-products-images/image32.png)](purchasing-consumable-products-images/image32.png#lightbox)   

當為 false 時，仍然可以要求產品資訊 `CanMakePayments` ，讓應用程式仍然可以取出並顯示價格。 這表示，如果我們 `CanMakePayments` 從程式碼中移除檢查，則購買按鈕仍會處於使用中狀態，不過當嘗試購買時，使用者會看到一則訊息，指出 (**在** 存取付款佇列時由 StoreKit 產生) ：   

 [![不允許應用程式內購買](purchasing-consumable-products-images/image33.png)](purchasing-consumable-products-images/image33.png#lightbox)   

實際的應用程式可能會採用不同的方法來處理限制，例如完全隱藏按鈕，而且可能會比 StoreKit 自動顯示的警示更詳細地提供更詳細的訊息。