---
title: StoreKit 概觀與在 Xamarin.iOS 中的擷取產品資訊
description: 本文件提供 StoreKit 概觀。 它會描述類別搭配 StoreKit 測試 StoreKit 互動、 顯示銷售的產品、 處理無效的產品，並顯示當地語系化的價格。
ms.prod: xamarin
ms.assetid: FC21192E-6325-4389-C060-E92DBB5EBD87
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 0dcda2e4fd1ca7773668a0a6fdf46e01f2f0841d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61366909"
---
# <a name="storekit-overview-and-retrieving-product-info-in-xamarinios"></a>StoreKit 概觀與在 Xamarin.iOS 中的擷取產品資訊

以下螢幕擷取畫面顯示的應用程式內購買的使用者介面。
任何交易發生之前，應用程式必須擷取產品的價格和顯示的描述。 然後當使用者按下**購買**，應用程式會對 StoreKit 用來管理 Apple ID 登入與確認對話方塊中的要求。 假設，之後交易又成功，StoreKit 通知的應用程式程式碼中，這必須交易結果儲存，並提供存取權及其購買的使用者。   

   
 [![](store-kit-overview-and-retreiving-product-information-images/image14.png "StoreKit 通知應用程式程式碼，其必須儲存的交易結果，且使用者提供存取權及其購買")](store-kit-overview-and-retreiving-product-information-images/image14.png#lightbox)

## <a name="classes"></a>類別

您需要從 StoreKit framework 的下列類別，才能實作應用程式內購買項目：   
   
 **SKProductsRequest** – StoreKit 的要求核准的產品銷售 （應用程式存放區）。 可以設定以產品識別碼的數字。

-   **SKProductsRequestDelegate** – 宣告方法，以處理產品要求和回應。 
-   **SKProductsResponse** – 從 StoreKit (App Store) 傳送回給委派。 包含符合 Id 與要求中傳送此產品 SKProducts。 
-   **SKProduct** – StoreKit （即您已在 iTunes Connect 中設定） 從擷取的產品。 包含的產品，例如產品識別碼、 標題、 描述和價格的相關資訊。 
-   **SKPayment** – 建立產品識別碼，並加入付款佇列進行購買。 
-   **SKPaymentQueue** – 已排入佇列付款要求傳送至 Apple。 因為正在處理每個付款，會觸發通知。 
-   **SKPaymentTransaction** – 代表已完成的交易 （採購單要求已處理的應用程式市集並傳送回您的應用程式，透過 StoreKit）。 交易可能購買、 還原或失敗。 
-   **SKPaymentTransactionObserver** – StoreKit 付款佇列所產生的事件回應的自訂子類別。 
-   **StoreKit 的作業是非同步**– 之後啟動 SKProductRequest 或 SKPayment 新增至佇列，將控制項傳回給您的程式碼。 StoreKit 會在您的 SKProductsRequestDelegate 或 SKPaymentTransactionObserver 子類別上呼叫方法，從 Apple 伺服器接收資料時。 


下圖顯示各種 StoreKit 類別 （在您的應用程式必須實作抽象類別） 之間的關聯性：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image15.png "在應用程式必須實作各種的 StoreKit 類別抽象類別之間的關聯性")](store-kit-overview-and-retreiving-product-information-images/image15.png#lightbox)   
   
   
   
 本文件稍後的更詳細地說明這些類別。

## <a name="testing"></a>測試

StoreKit 的大部分作業需要實際的裝置進行測試。 正在擷取產品資訊 （即 價格&amp;描述) 可在模擬器而購買和還原作業會傳回錯誤 (例如 FailedTransaction 程式碼 = 5002 發生未知的錯誤)。

注意:StoreKit iOS 模擬器中無法運作。 在 iOS 模擬器中執行您的應用程式，當 StoreKit 會記錄警告，如果您的應用程式嘗試擷取付款佇列。 必須在實際裝置上完成測試存放區。   
   
   
   
 重要事項：不使用登入您的測試帳戶，在 [設定] 應用程式。 您可以使用設定應用程式簽署，無法使用任何現有的 Apple ID 帳戶，然後在提示時，您必須等到*在您的應用程式內購買序列*使用測試 Apple ID 登入   
   
   
   

如果您嘗試登入測試帳戶的實際存放區，它會自動轉換成實際的 Apple id。 該帳戶將不再可用於測試。

為測試 StoreKit 的程式碼中，您可以必須登出您的一般 iTunes 測試帳戶和登入使用特殊的測試帳戶 （在 iTunes Connect 中建立） 連結到測試存放區。 若要登出目前的帳戶，請造訪**設定 > iTunes App Store 和**如下所示：

 [![](store-kit-overview-and-retreiving-product-information-images/image16.png "若要登出目前的帳戶，請造訪設定 iTunes 與 App Store")](store-kit-overview-and-retreiving-product-information-images/image16.png#lightbox)
 
然後使用測試帳戶登入*StoreKit 要求應用程式內時*:



若要建立測試使用者，在 iTunes Connect 中的按一下**使用者和角色**的主頁面上。

 [![](store-kit-overview-and-retreiving-product-information-images/image17.png "若要建立測試使用者在 iTunes Connect 按一下使用者和角色的主頁面上")](store-kit-overview-and-retreiving-product-information-images/image17.png#lightbox)

選取**沙箱測試人員**

 [![](store-kit-overview-and-retreiving-product-information-images/image18.png "選取 沙箱測試人員")](store-kit-overview-and-retreiving-product-information-images/image18.png#lightbox)

現有使用者的清單隨即顯示。 您可以新增新的使用者，或刪除現有的記錄。 在入口網站並不會 （目前） 可讓您檢視或編輯現有的測試使用者，因此建議您保持良好的記錄，每個測試使用者而建立 （尤其是在您指派的密碼）。 一旦您刪除的測試使用者電子郵件地址不得重複使用另一個測試帳戶。  
   
 [![](store-kit-overview-and-retreiving-product-information-images/image19.png "會顯示現有的使用者清單")](store-kit-overview-and-retreiving-product-information-images/image19.png#lightbox)   
   
 新的測試使用者有類似的屬性，以實際的 Apple ID （例如名稱、 密碼、 祕密問題及回答）。 追蹤記錄的 在此處輸入的所有詳細資料。 **選取 iTunes Store**欄位將會決定哪一個貨幣，並使用應用程式內購買的語言時已登入以該使用者。

 [![](store-kit-overview-and-retreiving-product-information-images/image20.png "選取 iTunes Store 欄位將會決定使用者的貨幣和語言進行其應用程式內購買")](store-kit-overview-and-retreiving-product-information-images/image20.png#lightbox)

## <a name="retrieving-product-information"></a>正在擷取產品資訊

銷售應用程式內購買產品的第一個步驟將它顯示： 顯示應用程式存放區中擷取目前的價格和描述。   
   
   
   
 不論哪一種產品應用程式銷售 （取用，非取用或類型的訂用帳戶），擷取顯示的產品資訊的程序都相同。 本文所附的 InAppPurchaseSample 程式碼包含名為的專案*消耗*，示範如何擷取顯示的實際執行環境資訊。 它會示範如何：

-  建立實作`SKProductsRequestDelegate`並實作`ReceivedResponse`抽象方法。 範例程式碼會將此稱為`InAppPurchaseManager`類別。 
-  請看看是否允許付款 StoreKit (使用`SKPaymentQueue.CanMakePayments`)。 
-  具現化`SKProductsRequest`具有已定義在 iTunes Connect 中的產品識別碼。 這會在此範例的`InAppPurchaseManager.RequestProductData`方法。 
-  在呼叫 Start 方法`SKProductsRequest`。 這會觸發應用程式存放區伺服器的非同步呼叫。 委派 ( `InAppPurchaseManager` ) 將會呼叫後的結果。 
-  委派的 ( `InAppPurchaseManager` )`ReceivedResponse`方法會傳回從 App Store （產品價格和描述或不正確的產品相關的訊息） 的資料以更新 UI。 

整體的互動看起來像這樣 ( **StoreKit**內建於 iOS，而**App Store**代表 Apple 的伺服器):

 [![](store-kit-overview-and-retreiving-product-information-images/image21.png "正在擷取產品資訊的圖形")](store-kit-overview-and-retreiving-product-information-images/image21.png#lightbox)

### <a name="displaying-product-information-example"></a>顯示產品資訊範例

[InAppPurchaseSample](https://developer.xamarin.com/samples/monotouch/StoreKit/) *消耗*範例程式碼示範如何擷取產品資訊的方式。 此範例的主畫面會顯示從應用程式存放區擷取這兩項產品資訊：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image23.png "主畫面會顯示從應用程式存放區擷取的資訊產品")](store-kit-overview-and-retreiving-product-information-images/image23.png#lightbox)   
   
   
   
 擷取並顯示產品資訊的範例程式碼會在下面詳細說明。


#### <a name="viewcontroller-methods"></a>ViewController 方法

`ConsumableViewController`類別會管理的產品 Id 為硬式編碼在類別中的兩個產品價格的顯示方式。

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

ViewWillAppear 方法中會建立觀察者，並將其指派使用預設的通知中心中：

```csharp
priceObserver = NSNotificationCenter.DefaultCenter.AddObserver (
  InAppPurchaseManager.InAppPurchaseManagerProductsFetchedNotification,
(notification) => {
   // display code goes here, to handle the response from the App Store
}
```

   
   
 在結尾`ViewWillAppear`方法中，呼叫`RequestProductData`; 方法來啟始 StoreKit 要求。 一旦發出此要求，StoreKit 將以非同步方式與 Apple 的伺服器，以取得的資訊，並將其饋送回到您的應用程式。 這所達成`SKProductsRequestDelegate`子類別 ( `InAppPurchaseManager`)，會在下一節中說明。

```csharp
iap.RequestProductData(products);
```

顯示的價格和描述的程式碼只要從 SKProduct 擷取資訊，並將它指派給 UIKit 控制項 (請注意，我們會顯示`LocalizedTitle`和`LocalizedDescription`– StoreKit 會自動解析正確的文字和基礎價格使用者的帳戶設定）。 下列程式碼位於前面所建立的通知：

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

最後，`ViewWillDisappear`方法應該確保移除觀察者：

```csharp
NSNotificationCenter.DefaultCenter.RemoveObserver (priceObserver);
```

#### <a name="skproductrequestdelegate-inapppurchasemanager-methods"></a>SKProductRequestDelegate (InAppPurchaseManager) 方法

`RequestProductData`應用程式想要擷取產品價格和其他資訊時，會呼叫方法。 它會剖析成正確的資料類型的產品識別碼集合，然後建立`SKProductsRequest`具有相關資訊。 呼叫 Start 方法，會導致對 Apple 伺服器的網路要求。 要求會以非同步方式執行，並呼叫`ReceivedResponse`方法的委派時順利完成。

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

iOS 會自動將要求路由至 App Store，根據應用程式以 – 執行哪些佈建設定檔的 「 沙箱 」 或 'production' 版本讓您開發或測試您的應用程式時要求將能夠存取每個產品在 iTunes Connect （即使尚未已送出或由 Apple 核准） 中設定。 當您的應用程式在生產環境中，StoreKit 要求只會傳回的資訊**Approved**產品。   
   
   
   
 `ReceivedResponse` Apple 的伺服器回應的資料之後會呼叫覆寫的方法。 因為這在背景中進行呼叫，所以程式碼應該剖析有效的資料，並使用通知將產品資訊傳送至任何 ViewControllers '接聽' 該通知。 有效的產品資訊的收集和傳送通知的程式碼如下所示：

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

雖然未顯示在圖表中，`RequestFailed`應該也會覆寫方法，以便您可以提供意見給使用者，萬一應用程式存放區伺服器無法連線 （或其他某些錯誤，就會發生）。 範例程式碼只會寫入主控台，但實際的應用程式可能會選擇進行查詢以`error.Code`屬性及實作自訂行為 （例如使用者警示）。

```csharp
public override void RequestFailed (SKRequest request, NSError error)
{
   Console.WriteLine (" ** InAppPurchaseManager RequestFailed() " + error.LocalizedDescription);
}
```

此螢幕擷取畫面顯示範例應用程式之後立即載入 （當產品不未提供任何資訊）：

 [![](store-kit-overview-and-retreiving-product-information-images/image24.png "範例應用程式之後立即載入時不未提供任何產品資訊")](store-kit-overview-and-retreiving-product-information-images/image24.png#lightbox)

## <a name="invalid-products"></a>無效的產品

`SKProductsRequest`也可能會傳回無效的產品識別碼的清單。 無效的產品通常會傳回下列其中一項：   
   
   
   
 **輸入產品識別碼**– 接受只有效的產品識別碼。   
   
 **產品尚未核准**– 測試時，請清除銷售的所有產品應該都傳回`SKProductsRequest`; 不過，在生產環境中會都傳回僅已核准的產品。   
   
 **應用程式識別碼不是明確**– 萬用字元應用程式識別碼 （將以星號） 不允許應用程式內購買。   
   
 **不正確的佈建設定檔**– 如果您進行變更 （例如啟用應用程式內購買），在佈建入口網站中的應用程式組態，請記得重新產生，並建置應用程式時，請使用正確的佈建設定檔。   
   
 **iOS 付費應用程式合約不是就地**– StoreKit 功能將完全無法運作除非您在 Apple 開發人員帳戶的有效合約。   
   
 **二進位檔位於已拒絕狀態**– 如果有先前提交的二進位檔中 （無論是使用 App Store 團隊，或由開發人員） 已拒絕狀態則 StoreKit 功能將無法運作。

`ReceivedResponse`方法的範例程式碼會輸出到主控台無效的產品：

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

價格層之間所有的國際應用程式存放區指定每項產品的特定價格。 若要確保每個貨幣價格會正確顯示，使用下列的擴充方法 (定義於`SKProductExtension.cs`) 而不是 [價格] 屬性，每個`SKProduct`:

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

使用兩個不同的 iTunes 測試帳戶 （一個用於美國的存放區），另一個用於日文的存放區會導致下列的螢幕擷取畫面：   
   
   
   
 [![](store-kit-overview-and-retreiving-product-information-images/image25.png "兩個不同的 iTunes 測試帳戶的顯示語言特定的結果")](store-kit-overview-and-retreiving-product-information-images/image25.png#lightbox)   
   
   
   
 請注意，存放區會影響用來提供產品資訊和價格貨幣，而標籤和其他當地語系化的內容，會影響裝置的語言設定的語言。   
   
   
   
 您應該記得，若要使用不同的存放區測試帳戶，您必須**登出**中**設定 > iTunes App Store 和**並重新啟動應用程式以使用不同的帳戶登入。 若要變更裝置的語言，請移至**設定 > 一般 > 國際 > 語言**。

