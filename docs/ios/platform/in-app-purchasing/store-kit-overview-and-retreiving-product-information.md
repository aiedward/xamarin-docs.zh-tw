---
title: 儲存組件概觀和擷取產品資訊
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: f4ecd2942a99f80854fd340be454f9d8fefa5a36
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="store-kit-overview-and-retrieving-product-information"></a>儲存組件概觀和擷取產品資訊

以下螢幕擷取畫面會顯示在應用程式內購買的使用者介面。
任何交易發生之前，應用程式必須擷取產品的價格和顯示的描述。 然後當使用者按**購買**，應用程式對 StoreKit 可管理的確認對話方塊和 Apple ID 登入進行要求。 假設，之後交易又成功，StoreKit 通知的應用程式程式碼中，必須儲存的交易結果以及讓使用者存取其購買。   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit 通知的應用程式程式碼必須儲存的交易結果並提供使用者存取他們購買")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>類別

實作在應用程式內購買需要來自 StoreKit 架構的下列類別：   
   
 **SKProductsRequest** – StoreKit 的要求核准的產品銷售 （應用程式存放區）。 可以設定以數字的產品識別碼。

-   **SKProductsRequestDelegate** – 宣告處理產品要求和回應的方法。 
-   **SKProductsResponse** – 寄回給委派 StoreKit （應用程式存放區）。 包含符合的產品識別碼會隨要求傳送 SKProducts。 
-   **SKProduct** – StoreKit （即您已在 iTunes Connect 中設定） 從擷取的產品。 包含產品，例如產品識別碼、 標題、 描述和價格的相關資訊。 
-   **SKPayment** – 建立具有產品識別碼新增至付款佇列，進行購買。 
-   **SKPaymentQueue** – 已排入佇列付款要求傳送至 Apple。 正在處理每個付款所引發通知。 
-   **SKPaymentTransaction** – 代表已完成的交易 （訂單要求已處理的應用程式市集並傳回到您的應用程式，透過 StoreKit）。 交易可能已購買、 還原或失敗。 
-   **SKPaymentTransactionObserver** – 回應 StoreKit 付款佇列所產生的事件的自訂子類別。 
-   **StoreKit 作業是非同步**– 啟動 SKProductRequest 或 SKPayment 加入佇列，將控制權傳回給您的程式碼之後。 StoreKit 會 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子類別上呼叫方法，從 Apple 伺服器接收資料時。 


下圖顯示各種 StoreKit 類別 （抽象類別必須實作應用程式中） 之間的關聯性：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "必須在應用程式中實作各種 StoreKit 類別抽象類別之間的關聯性")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 這些類別會在本文件稍後的更詳細地說明。

## <a name="testing"></a>測試

大部分 StoreKit 作業需要實際的裝置進行測試。 擷取產品資訊 （即 價格&amp;描述) 將適用於模擬器但採購，並還原作業會傳回錯誤 (例如 FailedTransaction 程式碼 = 5002 時發生未知的錯誤)。

注意： 在 iOS 模擬器中，StoreKit 無法運作。 在 iOS 模擬器中執行應用程式，當 StoreKit 會記錄警告，如果您的應用程式嘗試擷取付款佇列。 在實際裝置上必須完成測試存放區。   
   
   
   
 重要事項： 沒有登入您的測試帳戶，設定應用程式中。 您可以使用符號設定應用程式無法使用任何現有的 Apple ID 帳戶，則會提示您時，必須等到*中應用程式內購買序列內*登入使用測試 Apple id。   
   
   
   

如果您嘗試登入的測試帳戶的實際存放區，它會自動轉換成實際的 Apple id。 該帳戶將無法再使用於測試。

測試 StoreKit 程式碼，您必須登出規則 iTunes 測試帳戶的登入連結至測試存放區的特殊測試帳戶 （在 iTunes Connect 中建立）。 若要登出目前的帳戶，請造訪**設定 > iTunes App Store 和**如下所示：

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "若要登出目前的帳戶，請造訪設定 iTunes 和應用程式市集")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
然後使用測試帳戶登入*StoreKit 要求應用程式內時*:



若要建立測試使用者，在 iTunes Connect 中的按一下**使用者和角色**主頁面上。

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "若要建立測試使用者，在 iTunes Connect 按一下使用者和角色的主頁面上")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

選取**沙箱測試人員**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "選取沙箱測試人員")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

現有使用者的清單隨即顯示。 您可以將新使用者加入或刪除現有的記錄。 並不會在入口網站 （目前） 可讓您檢視或編輯現有的測試使用者，所以建議您保持良好的記錄，每個測試使用者所建立 （特別是在您指派的密碼）。 一旦您刪除的測試使用者電子郵件地址不得重複使用另一個測試帳戶。  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "會顯示現有使用者的清單")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 新的測試使用者有類似的屬性，以實際的 Apple ID （例如名稱、 密碼、 秘密提示問題和解答）。 保留在此處輸入的所有詳細資料的記錄。 **選取 iTunes Store**欄位將會決定哪些貨幣，並使用應用程式內購買的語言時已登入之使用者的身分。

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "選取 iTunes Store 欄位將會決定使用者的貨幣和應用程式內購買行為的語言")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>擷取產品資訊

銷售應用程式內購買產品的第一個步驟將它顯示成： 從顯示的應用程式存放區擷取目前的價格和描述。   
   
   
   
 不論哪一種產品的應用程式銷售 （取用，非取用或類型的訂用帳戶），擷取產品資訊。 顯示的程序都相同。 本文章的 InAppPurchaseSample 程式碼包含名為專案*消耗*來示範如何擷取實際執行環境資訊顯示。 它會顯示如何：

-  建立實作`SKProductsRequestDelegate`並實作`ReceivedResponse`抽象方法。 範例程式碼會呼叫`InAppPurchaseManager`類別。 
-  請看看是否允許付款 StoreKit (使用`SKPaymentQueue.CanMakePayments`)。 
-  具現化`SKProductsRequest`具有已定義在 iTunes Connect 中的產品識別碼。 這是範例`InAppPurchaseManager.RequestProductData`方法。 
-  在上呼叫 Start 方法`SKProductsRequest`。 這會觸發的非同步呼叫的應用程式存放區的伺服器。 委派 ( `InAppPurchaseManager` ) 將會呼叫後的結果。 
-  委派 ( `InAppPurchaseManager` )`ReceivedResponse`從 App Store （產品價格和描述或無效的產品相關的訊息） 傳回的資料更新 UI 的方法。 

整體的互動如下所示 ( **StoreKit**是內建到 iOS、 和**App Store**代表 Apple 的伺服器):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "擷取產品資訊的圖形")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>顯示產品資訊範例

[InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *消耗*範例程式碼示範如何擷取產品資訊的方式。 此範例的主畫面會顯示從應用程式存放區擷取這兩項產品資訊：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "主畫面會顯示從應用程式存放區擷取的資訊產品")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 以下更詳細說明擷取並顯示產品資訊的範例程式碼。


#### <a name="viewcontroller-methods"></a>ViewController 方法

`ConsumableViewController`類別會管理適用於產品 Id 為硬式編碼的類別中的兩個產品價格的顯示。

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

在類別層級也必須有 NSObject 宣告，將用來安裝`NSNotificationCenter`觀察者：

```csharp
NSObject priceObserver;
```

在 ViewWillAppear 方法觀察者會建立，並使用預設通知中心指派：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 在結尾`ViewWillAppear`方法，請呼叫`RequestProductData`方法以啟始 StoreKit 要求。 之後此要求，StoreKit 會以非同步方式連絡 Apple 的伺服器，以取得資訊，以及送回您的應用程式。 這藉由`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`)，會在下一節中說明。

```csharp
iap.RequestProductData(products);
```

顯示的價格和描述的程式碼只從 SKProduct 擷取資訊，並將它指派給 UIKit 控制項 (請注意，我們會顯示`LocalizedTitle`和`LocalizedDescription`– StoreKit 自動解析正確的文字和根據價格使用者的帳戶設定）。 下列程式碼所屬中前面所建立的通知：

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

最後，`ViewWillDisappear`方法應確保會移除觀察者：

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

`RequestProductData`呼叫方法時，應用程式想要擷取產品價格和其他資訊。 它會剖析成正確的資料類型的產品識別碼集合，然後建立`SKProductsRequest`具有相關資訊。 呼叫 Start 方法會導致 Apple 的伺服器必須對網路要求。 要求將會以非同步方式執行，並呼叫`ReceivedResponse`方法已成功完成時的委派。

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

iOS 會自動路由傳送要求至 「 沙箱 」 或 「 生產 」 版本的應用程式市集，根據應用程式正以 – 哪些佈建設定檔，要求您開發或測試您的應用程式時將會有每個產品的存取在 iTunes Connect （即使是尚未已送出或由 Apple 核准） 中設定。 在生產環境中應用程式時，StoreKit 要求只會傳回資訊**Approved**產品。   
   
   
   
 `ReceivedResponse` Apple 的伺服器與資料所作的反應之後呼叫覆寫的方法。 這在背景呼叫，因為程式碼應該剖析出有效的資料，並使用的產品資訊傳送至任何 ViewControllers '接聽' 通知的通知。 收集有效的產品資訊和傳送通知的程式碼如下所示：

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

雖然未顯示在圖表中，`RequestFailed`應該也覆寫方法，讓您可以提供意見反應給使用者，萬一應用程式存放區的伺服器無法連線到 （或其他錯誤，就會發生）。 範例程式碼只會寫入主控台中，但實際的應用程式可能會選擇查詢`error.Code`屬性和實作自訂行為 （例如使用者警示）。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

這個螢幕擷取畫面顯示範例應用程式之後立即載入 （當產品不未提供任何資訊）：

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "在載入時不未提供任何產品資訊之後，立即以範例應用程式")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>無效的產品

`SKProductsRequest`也可能會傳回無效的產品識別碼的清單。 無效的產品通常會傳回下列其中一項：   
   
   
   
 **輸入產品識別碼**– 接受只有有效的產品識別碼。   
   
 **產品未核准**– 測試時，請清除銷售的所有產品應都傳回`SKProductsRequest`; 不過，只有已核准的產品會都傳回在生產環境中。   
   
 **應用程式識別碼並未明確**– 萬用字元應用程式識別碼 （具有星號） 不允許應用程式內購買。   
   
 **不正確的佈建設定檔**– 如果您進行變更 （例如啟用應用程式內購買），在佈建入口網站中的應用程式組態，請記得要重新產生，並建置應用程式時，請使用正確的佈建設定檔。   
   
 **iOS 付費的應用程式合約不是就地**– StoreKit 功能將無法完全除非有有效的合約，為您的 Apple 開發人員帳戶。   
   
 **二進位檔位於已拒絕狀態**– 則 StoreKit 功能無法使用，如果已拒絕狀態 （無論是由應用程式市集小組，或由開發人員） 先前提出的二進位檔。

`ReceivedResponse`方法的範例程式碼輸出到主控台無效的產品：

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

價格層之間所有的國際應用程式存放區指定每項產品的特定價格。 若要確保每個貨幣能正確顯示的價格，使用下列的擴充方法 (定義於`SKProductExtension.cs`) 而不是每個價格屬性`SKProduct`:

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

設定按鈕的標題的程式碼會使用擴充方法，就像這樣：

```csharp
string Buy = "Buy {0}"; // or a localizable string
buy5Button.SetTitle(String.Format(Buy, product.LocalizedPrice()), UIControlState.Normal);
```

使用兩個不同 iTunes 測試帳戶 （一個在美國的存放區），另一個用於日文的存放區會導致下列螢幕擷取畫面：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "兩個不同 iTunes 測試帳戶的顯示語言特定的結果")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 請注意，在存放區會影響裝置的語言設定會影響標籤和其他當地語系化的內容時，會將產品資訊和價格貨幣，使用的語言。   
   
   
   
 回收，若要使用不同的存放區測試帳戶，您必須**登出**中**設定 > iTunes App Store 和**，然後重新啟動應用程式以使用不同的帳戶登入。 若要變更裝置的語言，請移至**設定 > 一般 > 國際 > 語言**。

