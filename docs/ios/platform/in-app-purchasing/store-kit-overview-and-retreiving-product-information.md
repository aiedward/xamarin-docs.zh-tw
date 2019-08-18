---
title: StoreKit 總覽和在 Xamarin 中取出產品資訊
description: 本檔提供 StoreKit 的總覽。 其中描述與 StoreKit 搭配使用的類別、測試 StoreKit 互動、顯示銷售產品、處理無效產品, 以及顯示當地語系化價格。
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 64ad867dca0bbbf27d39b69dc7a1acba73728ca2
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527794"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>StoreKit 總覽和在 Xamarin 中取出產品資訊

應用程式內購買的使用者介面如下列螢幕擷取畫面所示。
在進行任何交易之前, 應用程式必須先取得產品的價格和顯示的描述。 然後, 當使用者按下 [**購買**] 時, 應用程式會向 StoreKit 提出要求, 以管理確認對話方塊和 Apple ID 登入。 假設交易成功, StoreKit 會通知應用程式代碼, 這必須儲存交易結果, 並為使用者提供其購買的存取權。   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit 會通知應用程式代碼, 這必須儲存交易結果, 並為使用者提供其購買的存取權")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>類別

若要執行應用程式內購買, 需要 StoreKit 架構中的下列類別:   
   
 **SKProductsRequest** –針對核准的產品要銷售的 StoreKit 要求 (App Store)。 可以使用多個產品識別碼來設定。

- **SKProductsRequestDelegate** –宣告處理產品要求和回應的方法。 
- **SKProductsResponse** –從 StoreKit (App Store) 傳送回委派。 包含符合要求所傳送之產品識別碼的 SKProducts。 
- **SKProduct** –從 StoreKit (您在 iTunes Connect 中設定) 取出的產品。 包含產品的相關資訊, 例如產品識別碼、標題、描述和價格。 
- **SKPayment** –使用產品識別碼建立, 並新增至付款佇列以執行購買。 
- **SKPaymentQueue** –要傳送給 Apple 的佇列付款要求。 由於每個付款處理的結果, 都會觸發通知。 
- **SKPaymentTransaction** –代表已完成的交易 (已由 App Store 處理並透過 StoreKit 傳送回您的應用程式的購買要求)。 交易可能已購買、還原或失敗。 
- **SKPaymentTransactionObserver** –回應 StoreKit 付款佇列所產生之事件的自訂子類別。 
- **StoreKit 作業是非同步**的–啟動 SKProductRequest 之後, 或將 SKPayment 新增至佇列之後, 控制權就會傳回給您的程式碼。 StoreKit 會在您的 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子類別接收來自 Apple 伺服器的資料時, 呼叫方法。 


下圖顯示各種 StoreKit 類別之間的關聯性 (必須在您的應用程式中實作為抽象類別):   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "各種 StoreKit 類別之間的關聯性必須在應用程式中實作為抽象類別")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 本檔稍後會更詳細說明這些類別。

## <a name="testing"></a>測試

大部分的 StoreKit 作業都需要實際的裝置進行測試。 正在抓取產品資訊 (ie)。 價格&amp;描述) 會在模擬器中運作, 但是購買和還原作業將會傳回錯誤 (例如 FailedTransaction 碼 = 5002 發生未知的錯誤)。

注意:StoreKit 不會在 iOS 模擬器中運作。 當您的應用程式在 iOS 模擬器中執行時, 如果您的應用程式嘗試取出付款佇列, StoreKit 會記錄警告。 測試存放區必須在實際裝置上完成。   
   
   
   
 重要事項：請勿在 [設定] 應用程式中使用您的測試帳戶登入。 您可以使用 [設定] 應用程式來登出任何現有的 Apple ID 帳戶, 然後您必須等到*應用程式內購買順序中*出現提示, 才能使用測試 Apple ID 登入。   
   
   
   

如果您嘗試使用測試帳戶來登入實際的存放區, 它會自動轉換成實際的 Apple ID。 該帳戶將無法再供測試之用。

若要測試 StoreKit 程式碼, 您必須登出一般 iTunes 測試帳戶, 並使用與測試存放區連結的特殊測試帳戶 (在 iTunes Connect 中建立) 來登入。 若要登出目前的帳戶, 請造訪 **> iTunes 和 App Store 的設定**, 如下所示:

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "若要登出目前的帳戶, 請造訪設定 iTunes 和 App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
然後, 在*您的應用程式中 StoreKit 要求時*, 使用測試帳戶登入:



若要在 iTunes Connect 中建立測試使用者, 請按一下主頁面上的 [**使用者和角色**]。

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "若要在 iTunes Connect 中建立測試使用者, 請按一下主頁面上的 [使用者和角色]")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

選取**沙箱測試人員**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "選取沙箱測試人員")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

隨即顯示現有使用者的清單。 您可以加入新的使用者, 或刪除現有的記錄。 入口網站不會使用 (目前) 讓您查看或編輯現有的測試使用者, 因此建議您針對每個建立的測試使用者 (尤其是您指派的密碼) 保留良好的記錄。 一旦您刪除測試使用者, 電子郵件地址就無法重複用於另一個測試帳戶。  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "隨即顯示現有使用者的清單")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 新的測試使用者對真實的 Apple ID 具有類似的屬性 (例如名稱、密碼、秘密問題和答案)。 保留此處輸入之所有詳細資料的記錄。 [**選取 ITunes Store** ] 欄位會決定在以該使用者身分登入時, 應用程式內購買會使用的貨幣和語言。

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "[選取 iTunes Store] 欄位將決定使用者在應用程式內購買的貨幣和語言")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>正在抓取產品資訊

銷售應用程式內購買產品的第一個步驟是顯示它: 從 App Store 中取得目前的價格和描述, 以供顯示。   
   
   
   
 無論應用程式銷售的產品類型為何 (可取用、非取用或類型的訂用帳戶), 取得產品資訊以供顯示的流程都相同。 本文隨附的 InAppPurchaseSample 程式碼包含名為「*耗材*」的專案, 示範如何取出實際執行資訊以供顯示。 它會顯示如何:

- 建立的`SKProductsRequestDelegate`執行, 並`ReceivedResponse`執行抽象方法。 範例程式碼會呼叫這個`InAppPurchaseManager`類別。 
- 請使用 StoreKit 查看是否允許付款 (使用`SKPaymentQueue.CanMakePayments` )。 
- `SKProductsRequest`使用已在 iTunes Connect 中定義的產品識別碼來具現化。 這會在範例的`InAppPurchaseManager.RequestProductData`方法中完成。 
- 在上`SKProductsRequest`呼叫 Start 方法。 這會觸發對 App Store 伺服器的非同步呼叫。 將會呼叫`InAppPurchaseManager`委派 (), 並傳回結果。 
- 委派的 ( `InAppPurchaseManager` ) `ReceivedResponse`方法會以 App Store 傳回的資料來更新 UI (產品價格 & 描述, 或有關無效產品的訊息)。 

整體互動看起來像這樣 ( **StoreKit**已內建至 iOS, 而**App Store**代表 Apple 的伺服器):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "正在抓取產品資訊圖表")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>顯示產品資訊範例

[InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)的*耗材*範例程式碼會示範如何取得產品資訊。 範例的主畫面會顯示從 App Store 抓取之兩項產品的資訊:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "主畫面會顯示從 App Store 取得的資訊產品")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 下面將更詳細說明用來抓取和顯示產品資訊的範例程式碼。


#### <a name="viewcontroller-methods"></a>ViewController 方法

`ConsumableViewController`類別會管理兩個產品的價格顯示, 其產品識別碼會在類別中硬式編碼。

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

在類別層級, 也應該有一個宣告的 NSObject, 將用來設定`NSNotificationCenter`觀察者:

```csharp
NSObject priceObserver;
```

在 Viewwillappear: 每當方法中, 會使用預設的通知中心來建立和指派觀察者:

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 在`ViewWillAppear`方法的結尾, `RequestProductData`呼叫方法以起始 StoreKit 要求。 提出此要求之後, StoreKit 會以非同步方式連線到 Apple 的伺服器以取得資訊, 並將它送回您的應用程式。 這是由`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`) 達成, 會在下一節中說明。

```csharp
iap.RequestProductData(products);
```

顯示價格和描述的程式碼只會從 SKProduct 中抓取資訊, 並將其指派給 UIKit 控制項 (請注意, 我們`LocalizedTitle`會`LocalizedDescription`顯示和– StoreKit 會自動解析正確的文字和價格, 依據使用者的帳戶設定)。 下列程式碼屬於我們先前建立的通知:

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

最後, `ViewWillDisappear`方法應該確定已移除觀察者:

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

當應用程式想要取得產品價格和其他資訊時, 會呼叫方法。`RequestProductData` 它會將產品識別碼的集合剖析成正確的資料類型, 然後`SKProductsRequest`使用該資訊建立。 呼叫 Start 方法會導致向 Apple 伺服器發出網路要求。 要求會以非同步方式執行, 並`ReceivedResponse`在成功完成時呼叫委派的方法。

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

iOS 會根據應用程式所執行的布建設定檔, 自動將要求路由至 App Store 的「沙箱」或「生產」版本, 因此當您開發或測試您的應用程式時, 要求將會擁有每項產品的存取權在 iTunes Connect 中設定 (即使是 Apple 尚未提交或核准的連線)。 當您的應用程式在生產環境中時, StoreKit 要求只會傳回**已核准**產品的資訊。   
   
   
   
 在`ReceivedResponse` Apple 的伺服器回應資料之後, 會呼叫覆寫的方法。 因為這是在背景中呼叫, 所以程式碼應剖析有效的資料, 並使用通知將產品資訊傳送到任何為該通知「接聽」的 ViewControllers。 收集有效產品資訊並傳送通知的程式碼如下所示:

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

雖然圖表中沒有顯示, `RequestFailed`但也應該覆寫方法, 以便在無法連線到應用程式存放區伺服器 (或發生其他錯誤) 時, 為使用者提供一些意見反應。 範例程式碼只會寫入至主控台, 但實際的應用程式可能會選取查詢`error.Code`屬性並執行自訂行為 (例如, 對使用者發出警示)。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

這個螢幕擷取畫面會在載入後立即顯示範例應用程式 (沒有可用的產品資訊時):

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "未提供任何產品資訊時, 在載入後立即進行的範例應用程式")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>不正確產品

`SKProductsRequest`可能也會傳回無效產品識別碼的清單。 通常會傳回不正確產品, 因為下列其中一項:   
   
   
   
 **產品識別碼輸入錯誤**-只接受有效的產品識別碼。   
   
 **產品尚未核准**–進行測試時, 所有針對銷售清除的產品都應該由`SKProductsRequest`傳回; 不過, 在生產環境中, 只會傳回已核准的產品。   
   
 **應用程式識別碼不是明確**的–萬用字元應用程式識別碼 (含星號) 不允許應用程式內購買。   
   
 **不正確**的布建設定檔–如果您在布建入口網站中對應用程式設定進行變更 (例如, 啟用應用程式內購買), 請記得在建立應用程式時重新產生並使用正確的布建設定檔。   
   
 **IOS 付費應用程式合約不**存在–除非您的 Apple 開發人員帳戶有有效的合約, 否則 StoreKit 功能將完全不適用。   
   
 **二進位檔處於拒絕狀態**–如果先前提交的二進位檔處於已拒絕狀態 (由 App Store 小組或開發人員), 則 StoreKit 功能將無法使用。

範例`ReceivedResponse`程式碼中的方法會將不正確產品輸出到主控台:

```csharp
public override void ReceivedResponse (SKProductsRequest request, SKProductsResponse response)
{
   // code removed for clarity
   foreach (string invalidProductId in response.InvalidProducts) {
       Console.WriteLine("Invalid product id: " + invalidProductId );
   }
}
```

## <a name="displaying-localized-prices"></a>顯示當地語系化價格

價位層會為所有國際應用程式商店中的每個產品指定特定價格。 為確保每個貨幣都能正確顯示價格, 請使用下列擴充方法 (定義`SKProductExtension.cs`于中), 而不是每`SKProduct`一個的價格屬性:

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

設定按鈕標題的程式碼會使用擴充方法, 如下所示:

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

使用兩個不同的 iTunes 測試帳戶 (一個用於美國商店, 另一個用於日文商店) 會產生下列螢幕擷取畫面:   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "兩個不同的 iTunes 測試帳戶, 顯示語言特定的結果")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 請注意, 存放區會影響用於產品資訊和價格貨幣的語言, 而裝置的語言設定則會影響標籤和其他當地語系化的內容。   
   
   
   
 回想一下, 若要使用不同的存放區測試帳戶, 您必須**登出** **> ITunes 和 App Store**中的設定, 然後重新開機應用程式以使用不同的帳戶登入。 若要變更裝置的語言, 請移至 **[設定] > [一般] > [國際 > 語言**]。

