---
title: "訂用帳戶和報表"
ms.topic: article
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 237a986d6db2fb6984e99c6265fbbc212b35a351
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="subscriptions-and-reporting"></a>訂用帳戶和報表

## <a name="about-non-renewing-subscriptions"></a>有關非更新訂用帳戶

非更新的訂閱適用於代表的時間限制，例如 （一週的瀏覽應用程式存取） 或資料保存的時間限制存取的服務之銷售的產品。   
   
   
   
 非更新訂用帳戶和其他產品類型之間的主要差異：

-  在 iTunes Connect 中的產品定義不包含該詞彙。 應用程式程式碼必須能夠推斷有效期間，從產品的識別碼。 
-  它們可以購買許多次 （例如在您可以使用的產品）。 需要應用程式管理的訂用帳戶詞彙/到期和更新，並避免使用者購買重疊的訂用帳戶。 
-  購買項目不會受到 StoreKit 還原函式。 如果訂用帳戶應可跨所有使用者的裝置，應用程式必須設計及實作這項功能搭配遠端伺服器。 應用程式也會負責備份訂閱狀態的情況下註冊裝置時備份然後還原從-備份。 
-  實作概觀
-  使用伺服器傳遞工作流程與管理與管理您可以使用的產品，通常應該實作非更新的訂閱。 


## <a name="about-free-subscriptions"></a>關於免費訂用帳戶

免費訂用帳戶可讓開發人員將可用的內容放入 Newsstand 應用程式 （它們不能在非 Newsstand 應用程式）。 一旦開始免費訂用帳戶會在所有使用者的裝置。 免費訂用帳戶永遠不過期。它們只會結束應用程式解除安裝時。

### <a name="implementation-overview"></a>實作概觀

免費訂用帳戶的行為更像是自動更新的訂閱。 應用程式必須具備在 iTunes Connect 中的免費訂用帳戶的產品可以 '購買'。 當購買的使用者，免費訂用帳戶購買像是自動更新的訂閱產品驗證。 可以還原免費訂用帳戶的交易。


## <a name="about-auto-renewable-subscriptions"></a>有關自動更新的訂閱

自動更新訂用帳戶主要用於 Newsstand 應用程式。 它們代表授與指定的一段時間，在 iTunes Connect （設定範圍從 7 天到 1 年期間） 中所設定的動態內容使用者存取的產品。 訂用帳戶，自動更新充電結尾的每個訂用期間，使用者的 Apple ID，除非使用者 opts 出。此產品類型適用於雜誌或新聞訂用帳戶，使用者可以獲得存取每個發行其訂用帳戶有效時的問題。

### <a name="implementation-overview"></a>實作概觀

自動更新的訂閱應該使用 Server-Delivered 產品工作流程來實作 (請參閱*回條確認和 Server-Delivered 產品*> 一節)。

#### <a name="shared-secret"></a>共用的密碼

在應用程式採購共用密碼必須使用 JSON 要求中，確認您的伺服器上的自動更新訂閱時。 共用的密碼是透過 iTunes Connect 建立/存取。

從 iTunes Connect 首頁選取**我的應用程式**:   
   
 [![](subscriptions-and-reporting-images/image2.png "選取 我的應用程式")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
選取的應用程式，然後按一下**應用程式內購買** 索引標籤：

[![](subscriptions-and-reporting-images/image6.png "在應用程式內購買 索引標籤上按一下")](subscriptions-and-reporting-images/image6.png#lightbox)

從頁面底部，選取**檢視，或產生共用的密碼**:
   
 [![](subscriptions-and-reporting-images/image40.png "選取檢視，或產生共用的密碼")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "產生共用的密碼")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 若要使用的共用的密碼，請將其併入 JSON 承載驗證未自動更新訂用帳戶，就像這樣的應用程式內購買回條時傳送至 Apple 的伺服器中：

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

回應的狀態 欄位將是零，如果購買有效的做為與其他產品類型。

#### <a name="downloading-items-after-the-initial-subscription-term"></a>初始訂閱期限之後下載的項目

訂閱產品的一部分，該程式碼應該經常檢查最新已知的回條針對 Apple 的伺服器。 如果訂用帳戶已自動-更新後的最後一個驗證，JSON 回應將包含通知所發生之交易的應用程式的其他欄位 （這應該延長訂用帳戶有效）。 JSON 回應會包含：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

如果狀態為零，訂用帳戶是否仍然有效，和其他欄位保存有效的資料。 如果狀態為 21006 訂閱已過期。 請參閱[確認自動更新的訂用收據](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)其他錯誤碼的文件。

#### <a name="restoring-auto-renewable-subscriptions"></a>還原可自動更新的訂閱

您會取得多個交易 – 原始購買交易加上每一段時間的訂用帳戶已更新的個別交易。 您要追蹤以了解有效期間為何的開始日期和條款。   
   
   
   
 SKPaymentTransaction 物件不包含訂用帳戶一詞，您應該針對每個詞彙使用不同的產品識別碼，並撰寫程式碼，可以推斷交易的訂用帳戶期間從購買日期。

#### <a name="testing-auto-renewal"></a>測試自動更新

若要讓您更輕鬆地測試訂用帳戶，在沙箱中測試時，會壓縮其持續時間。 1 週訂用帳戶更新每 3 分鐘，訂用帳戶更新的每個小時的 1 年。 訂用帳戶會自動更新時在沙箱中測試 6 次的最大值。

## <a name="reporting"></a>報告

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 提供：   
   
 **銷售和趨勢**– 下載的應用程式的顯示詳細資料、 更新和應用程式內購買。   
   
 **付款和財務報表**– 詳細說明您的應用程式以及已對您和您欠的多少的清單付款得到的平均收入。

銷售和趨勢報表範例如下所示：   

 [![](subscriptions-and-reporting-images/image42.png "銷售和趨勢報表範例")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 另外還有[**細明體連接行動**iOS 應用程式 （iTunes 連結）](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8)。
iPhone 螢幕擷取畫面的一些可用的統計資料如下所示：   
   
 [![](subscriptions-and-reporting-images/image43.png "可用的統計資料的某些 iPhone 螢幕擷取畫面")](subscriptions-and-reporting-images/image43.png#lightbox)
