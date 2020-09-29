---
title: StoreKit 總覽和在 Xamarin 中取出產品資訊
description: 本檔提供 StoreKit 的總覽。 它描述與 StoreKit 搭配使用的類別、測試 StoreKit 互動、顯示銷售的產品、處理不正確產品，以及顯示當地語系化的價格。
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: b7bfa98f84210c921790989c60a7bda21b7c6bcd
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435543"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>StoreKit 總覽和在 Xamarin 中取出產品資訊

應用程式內購買的使用者介面會顯示在下列螢幕擷取畫面中。
在進行任何交易之前，應用程式必須先取得產品的價格和描述，以顯示。 然後當使用者按下 [ **購買**] 時，應用程式會對 StoreKit 提出要求，以管理確認對話方塊和 Apple ID 登入。 假設交易接著成功，StoreKit 會通知應用程式程式碼，此程式碼必須儲存交易結果，並讓使用者能夠存取其購買。   

 [![StoreKit 會通知應用程式程式碼，此程式碼必須儲存交易結果並提供使用者購買的存取權](store-kit-overview-and-retreiving-product-information-images/image14.png)](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>類別

執行應用程式內購買需要來自 StoreKit framework 的下列類別：   

 **SKProductsRequest** –針對核准的產品進行 StoreKit 的要求，以 (App Store) 銷售。 可以使用一些產品識別碼進行設定。

- **SKProductsRequestDelegate** –宣告處理產品要求和回應的方法。
- **SKProductsResponse** –從 StoreKit 送回委派 (App Store) 。 包含符合要求所傳送之產品識別碼的 SKProducts。
- **SKProduct** –從您在 iTunes Connect) 中設定的 StoreKit (取出的產品。 包含產品的相關資訊，例如產品識別碼、標題、描述和價格。
- **SKPayment** –使用產品識別碼建立，並新增至付款佇列以執行購買。
- **SKPaymentQueue** –已排入佇列的付款要求以傳送給 Apple。 每項付款處理的結果都會觸發通知。
- **SKPaymentTransaction** –代表已完成的交易 (已由 App Store 處理，並透過 StoreKit 傳送回您的應用程式) 的購買要求。 您可以購買、還原或失敗交易。
- **SKPaymentTransactionObserver** –可回應 StoreKit 付款佇列所產生之事件的自訂子類別。
- **StoreKit 作業是非同步** 的– SKProductRequest 啟動或 SKPayment 加入至佇列之後，控制權會傳回給您的程式碼。 StoreKit 會在您的 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子類別上呼叫方法，以接收來自 Apple 伺服器的資料。

下圖顯示各種 StoreKit 類別之間的關聯性 (抽象類別必須在您的應用程式) 中執行：   

 [![不同 StoreKit 類別之間的關聯性必須在應用程式中實作為抽象類別](store-kit-overview-and-retreiving-product-information-images/image15.png)](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   

本檔稍後會更詳細地說明這些類別。

## <a name="testing"></a>測試

大部分的 StoreKit 作業都需要真實的裝置來進行測試。 正在 (ie 中抓取產品資訊。 價格 &amp; 描述) 可在模擬器中運作，但是購買和還原作業會傳回錯誤 (例如 FailedTransaction Code = 5002 發生未知的錯誤) 。

注意： StoreKit 無法在 iOS 模擬器中運作。 在 iOS 模擬器中執行應用程式時，如果您的應用程式嘗試取得付款佇列，StoreKit 會記錄警告。 測試存放區必須在實際裝置上完成。   

重要：請勿在設定應用程式中使用您的測試帳戶登入。 您可以使用 [設定] 應用程式登出任何現有的 Apple ID 帳戶，然後您必須 *在應用程式內購買順序中* 等待提示，以使用測試 Apple ID 登入。   

如果您嘗試使用測試帳戶登入實際存放區，則會自動將它轉換成實際的 Apple ID。 該帳戶將不再可用來進行測試。

若要測試 StoreKit 程式碼，您必須登出一般 iTunes 測試帳戶，並使用特殊測試帳戶登入， (在與測試存放區連結的 iTunes Connect) 中建立。 若要登出目前的帳戶，請造訪 **> iTunes 和 App Store 的設定，** 如下所示：

 [![若要登出目前的帳戶，請造訪設定 iTunes 和 App Store](store-kit-overview-and-retreiving-product-information-images/image16.png)](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)

然後，在 *您的應用程式中 StoreKit 要求時*，使用測試帳戶登入：

若要在 iTunes Connect 中建立測試使用者，請按一下主頁面上的 **使用者和角色** 。

 [![若要在 iTunes Connect 中建立測試使用者，請按一下主頁面上的使用者和角色](store-kit-overview-and-retreiving-product-information-images/image17.png)](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

選取 **沙箱測試人員**

 [![選取沙箱測試人員](store-kit-overview-and-retreiving-product-information-images/image18.png)](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

現有使用者的清單隨即顯示。 您可以新增使用者或刪除現有的記錄。 入口網站目前未 () 可讓您查看或編輯現有的測試使用者，因此建議您保留所建立每個測試使用者的良好記錄， (特別是您指派) 的密碼。 一旦您刪除測試使用者，電子郵件地址就無法再用於另一個測試帳戶。  

 [![現有使用者的清單隨即顯示。](store-kit-overview-and-retreiving-product-information-images/image19.png)](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   

 新的測試使用者與真正的 Apple ID 具有類似的屬性 (例如姓名、密碼、秘密問題和解答) 。 保留此處輸入的所有詳細資料記錄。 [ **選取 ITunes 商店** ] 欄位將決定以該使用者身分登入時，應用程式內購買所使用的貨幣和語言。

 [![選取的 iTunes 商店欄位將會決定使用者在應用程式內購買的貨幣和語言](store-kit-overview-and-retreiving-product-information-images/image20.png)](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>正在抓取產品資訊

銷售應用程式內購買產品的第一個步驟是顯示：從 App Store 抓取目前的價格和描述，以顯示。   

無論應用程式 (取用、非取用或) 訂用帳戶類型所銷售的產品類型為何，取得產品資訊以進行顯示的程式都相同。 本文隨附的 InAppPurchaseSample 程式碼包含一個名為 *補充* 專案的專案，示範如何取出生產資訊以供顯示。 它會顯示如何：

- 建立的執行  `SKProductsRequestDelegate` ，並執行  `ReceivedResponse` 抽象方法。 範例程式碼會呼叫這個  `InAppPurchaseManager` 類別。
- 請查看 StoreKit，查看是否允許使用 )  (款項  `SKPaymentQueue.CanMakePayments` 。
- `SKProductsRequest`使用 ITunes Connect 中定義的產品識別碼來具現化。 這是在範例的方法中完成  `InAppPurchaseManager.RequestProductData` 。
- 在上呼叫 Start 方法  `SKProductsRequest` 。 這會觸發對 App Store 伺服器的非同步呼叫。 委派 ( `InAppPurchaseManager` ) 會和結果一併呼叫。
- 委派的 ( `InAppPurchaseManager` )  `ReceivedResponse` 方法會以 App Store (產品價格 & 描述所傳回的資料來更新 UI，或) 無效產品的相關訊息。

整體互動看起來像這 ( **StoreKit** 內建于 iOS，而 **App Store** 代表 Apple 的伺服器) ：

 [![正在抓取產品資訊圖](store-kit-overview-and-retreiving-product-information-images/image21.png)](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>顯示產品資訊範例

[InAppPurchaseSample](/samples/xamarin/ios-samples/storekit) *耗材*範例-程式碼示範如何取出產品資訊。 範例的主畫面會顯示從 App Store 取出的兩項產品資訊：   

 [![主畫面會顯示從 App Store 取出的資訊產品](store-kit-overview-and-retreiving-product-information-images/image23.png)](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   

以下將更詳細地說明用來取得和顯示產品資訊的範例程式碼。

#### <a name="viewcontroller-methods"></a>ViewController 方法

此 `ConsumableViewController` 類別會管理兩項產品的價格顯示，這些產品的產品識別碼是在類別中硬式編碼。

```csharp
public static string Buy5ProductId = "com.xamarin.storekit.testing.consume5credits",
   Buy10ProductId = "com.xamarin.storekit.testing.consume10credits";
List<string> products;
InAppPurchaseManager iap;
public ConsumableViewController () : base()
{
   // two products for sale on this page
   products = new List<string>() {Buy5ProductId, Buy10ProductId};
   iap = new InAppPurchaseManager();
}
```

在類別層級，您也應該將用來設定觀察者的 NSObject 宣告為 `NSNotificationCenter` ：

```csharp
NSObject priceObserver;
```

在 ViewWillAppear 方法中，會使用預設的通知中心建立並指派觀察者：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

在方法的結尾 `ViewWillAppear` ，呼叫 `RequestProductData` 方法以起始 StoreKit 要求。 提出此要求之後，StoreKit 將會以非同步方式聯絡 Apple 的伺服器以取得資訊，並將其送回您的應用程式。 這是由下一 `SKProductsRequestDelegate` 節所說明的子類別 () 來達成 `InAppPurchaseManager` 。

```csharp
iap.RequestProductData(products);
```

顯示價格和描述的程式碼只會抓取 SKProduct 中的資訊，並將其指派給 UIKit 控制項 (請注意，我們顯示 `LocalizedTitle` 和 `LocalizedDescription` – StoreKit 會根據使用者的帳戶設定) 自動解析正確的文字和價格。 下列程式碼屬於我們在上面建立的通知：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
   var info = notification.UserInfo;
   if (info.ContainsKey(NSBuy5ProductId)) {
       var product = (SKProduct) info.ObjectForKey(NSBuy5ProductId);
       buy5Button.Enabled = true;
       buy5Title.Text = product.LocalizedTitle;
       buy5Description.Text = product.LocalizedDescription;
       buy5Button.SetTitle("Buy " + product.Price, UIControlState.Normal); // price display should be localized
   }
}
```

最後，此 `ViewWillDisappear` 方法應確定已移除觀察者：

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

`RequestProductData`當應用程式希望取得產品價格和其他資訊時，就會呼叫方法。 它會將產品識別碼的集合剖析成正確的資料類型，然後 `SKProductsRequest` 使用該資訊建立。 呼叫 Start 方法會導致向 Apple 伺服器發出網路要求。 要求會以非同步方式執行，並 `ReceivedResponse` 在成功完成時呼叫委派的方法。

```csharp
public void RequestProductData (List<string> productIds)
{
   var array = new NSString[productIds.Count];
   for (var i = 0; i < productIds.Count; i++) {
       array[i] = new NSString(productIds[i]);
   }
   NSSet productIdentifiers = NSSet.MakeNSObjectSet<NSString>(array);
   productsRequest = new SKProductsRequest(productIdentifiers);
   productsRequest.Delegate = this; // for SKProductsRequestDelegate.ReceivedResponse
   productsRequest.Start();
}
```

iOS 會根據應用程式執行所在的布建設定檔，自動將要求路由至 App Store 的「沙箱」或「生產」版本，因此當您開發或測試應用程式時，要求將可存取 iTunes Connect 中設定的每項產品 (即使是 Apple) 尚未提交或核准的產品。 當您的應用程式在生產環境中時，StoreKit 要求只會傳回 **已核准** 產品的資訊。   

在 `ReceivedResponse` Apple 的伺服器回應資料之後，會呼叫覆寫的方法。 因為這是在背景中呼叫的，所以程式碼應該剖析有效的資料，並使用通知將產品資訊傳送至該通知的任何「正在接聽」 >viewcontroller。 收集有效產品資訊和傳送通知的程式碼如下所示：

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   SKProduct[] products = response.Products;
   NSDictionary userInfo = null;
   if (products.Length > 0) {
       NSObject[] productIdsArray = new NSObject[response.Products.Length];
       NSObject[] productsArray = new NSObject[response.Products.Length];
       for (int i = 0; i < response.Products.Length; i++) {
           productIdsArray[i] = new NSString(response.Products[i].ProductIdentifier);
           productsArray[i] = response.Products[i];
       }
       userInfo = NSDictionary.FromObjectsAndKeys (productsArray, productIdsArray);
   }
   NSNotificationCenter.DefaultCenter.PostNotificationName (InAppPurchaseManagerProductsFetchedNotification, this, userInfo);
}
```

雖然圖中未顯示，但 `RequestFailed` 也應該覆寫此方法，以便在 App Store 伺服器無法連線 (或) 發生其他錯誤時，為使用者提供一些意見反應。 範例程式碼只會寫入到主控台，但實際的應用程式可能會選取查詢以進行屬性的查詢， `error.Code` 以及執行自訂行為 (例如使用者) 的警示。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

此螢幕擷取畫面會在載入 (但未提供任何產品資訊) 時立即顯示範例應用程式：

 [![未提供任何產品資訊時，立即載入範例應用程式](store-kit-overview-and-retreiving-product-information-images/image24.png)](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>產品無效

`SKProductsRequest`可能也會傳回無效產品識別碼的清單。 因為下列其中一項，通常會傳回不正確產品：   

**產品識別碼輸入錯誤** -只接受有效的產品識別碼。   

 **產品尚未經過核准** –在測試期間，所有已清除以進行銷售的產品都應該由傳回 `SKProductsRequest` ; 不過，在生產環境中，只會傳回核准的產品。   

 **應用程式識別碼不是明確** 的-萬用字元應用程式識別碼 (有星號) 不允許應用程式內購買。   

 布建**設定檔不正確**–如果您在布建入口網站中變更您的應用程式設定 (例如啟用應用程式內購買) ，請記得在建立應用程式時重新產生並使用正確的布建設定檔。   

 **IOS 付費應用程式合約未就緒** -除非您的 Apple 開發人員帳戶有有效的合約，否則 StoreKit 功能將無法運作。   

 二進位檔處於「**已拒絕」狀態**-如果先前已將「已拒絕」狀態的二進位檔案 (由 App Store 小組或開發人員所提交) 則 StoreKit 功能將無法運作。

`ReceivedResponse`範例程式碼中的方法會將不正確產品輸出到主控台：

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>顯示當地語系化的價格

定價層會針對所有國際應用程式商店中的每個產品指定特定價格。 為確保每個貨幣的價格都正確顯示，請使用下列擴充方法 (在) 中定義， `SKProductExtension.cs` 而不是每個貨幣的價格屬性 `SKProduct` ：

```csharp
public static class SKProductExtension {
   public static string LocalizedPrice (this SKProduct product)
   {
       var formatter = new NSNumberFormatter ();
       formatter.FormatterBehavior = NSNumberFormatterBehavior.Version_10_4;  
       formatter.NumberStyle = NSNumberFormatterStyle.Currency;
       formatter.Locale = product.PriceLocale;
       var formattedString = formatter.StringFromNumber(product.Price);
       return formattedString;
   }
}
```

設定按鈕標題的程式碼會使用如下的擴充方法：

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

使用兩個不同的 iTunes 測試帳戶 (一個用於美國商店，另一個用於日文商店) 會導致下列螢幕擷取畫面：   

 [![兩個不同的 iTunes 測試帳戶，顯示特定語言的結果](store-kit-overview-and-retreiving-product-information-images/image25.png)](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   

請注意，存放區會影響產品資訊和價格貨幣所使用的語言，而裝置的語言設定會影響標籤和其他當地語系化的內容。   

回想一下，若要使用不同的商店測試帳戶**Sign Out** ，您必須登出 **> iTunes 的設定，並 App Store**並重新啟動應用程式，以使用不同的帳戶登入。 若要變更裝置的語言，請移至 **[設定] > 一般 > 國際 > 語言**。