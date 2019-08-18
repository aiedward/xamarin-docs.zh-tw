---
title: Xamarin 中的訂閱和報告
description: 本檔說明非續約訂用帳戶、免費訂閱、自動可續訂的訂用帳戶, 以及使用 iTunes Connect 來報告這些專案。
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: ea016860bc30d9f6b70f41b85db09bf5544304ba
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528286"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Xamarin 中的訂閱和報告

## <a name="about-non-renewing-subscriptions"></a>關於非續約訂閱

非續訂訂閱適用于代表服務銷售時間限制的產品 (例如一周的流覽應用程式存取權, 或資料封存的限時存取)。   
   
非續約訂閱和其他產品類型之間的主要差異:

- ITunes Connect 中的產品定義不包含該詞彙。 應用程式代碼必須能夠從產品識別碼推斷有效期間。 
- 您可以購買多次 (例如可耗用的產品)。 需要應用程式來管理訂用帳戶期限/到期日和更新, 並防止使用者購買重迭的訂閱。 
- StoreKit Restore 函數不支援購買。 如果訂用帳戶在所有使用者的裝置上都可供使用, 則應用程式必須與遠端伺服器一起設計和執行這項功能。 應用程式也會負責備份裝置備份時的訂用帳戶狀態, 然後從備份進行還原。 
- 執行總覽
- 通常應該使用伺服器提供的工作流程和受控的取用產品來執行非續訂訂閱。 


## <a name="about-free-subscriptions"></a>關於免費訂閱

免費訂用帳戶可讓開發人員將免費內容放在 Newsstand apps 中 (不能用於非 Newsstand 應用程式)。 免費訂用帳戶啟動後, 即可在所有使用者的裝置上使用。 免費訂閱永不過期;它們只會在應用程式卸載時結束。

### <a name="implementation-overview"></a>執行總覽

免費訂閱的運作方式很像自動可續訂訂閱。 應用程式必須擁有免費的訂用帳戶產品, 才能在 iTunes Connect 中進行「購買」。 當使用者購買時, 免費的訂用帳戶購買應驗證為自動可續訂的訂用帳戶產品。 可以還原免費的訂用帳戶交易。


## <a name="about-auto-renewable-subscriptions"></a>關於自動可續訂訂閱

自動可續訂的訂用帳戶主要用於 Newsstand 應用程式。 其代表的產品會授與使用者在指定時間內的動態內容存取權, 這會在 iTunes Connect 中設定 (從7天到1年的期間)。 訂閱會自動更新, 除非使用者退出宣告, 否則會在每個訂用帳戶期間結束時, 對使用者的 Apple ID 收費。此產品類型適用于雜誌或新聞訂閱, 使用者可在其訂用帳戶有效時, 取得每個發行問題的存取權。

### <a name="implementation-overview"></a>執行總覽

自動可續訂訂閱應使用伺服器提供的產品工作流程來執行 (請參閱*收據驗證和伺服器提供的產品*一節)。

#### <a name="shared-secret"></a>共用密碼

在您的伺服器上驗證自動可續訂訂閱時, 必須在 JSON 要求中使用應用程式內購買共用密碼。 共用密碼是透過 iTunes Connect 建立/存取。

從 iTunes Connect 首頁選取 **我的應用程式**:   
   
 [![](subscriptions-and-reporting-images/image2.png "選取 [我的應用程式]")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
選取應用程式, 然後按一下 [**在應用程式內購買**] 索引標籤:

[![](subscriptions-and-reporting-images/image6.png "按一下 [應用程式內購買] 索引標籤")](subscriptions-and-reporting-images/image6.png#lightbox)

從頁面底部, 選取 [查看]**或 [產生共用密碼**]:
   
 [![](subscriptions-and-reporting-images/image40.png "選取 [查看] 或 [產生共用密碼]")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "產生共用密碼")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 若要使用共用密碼, 請在驗證自動可續訂訂用帳戶的應用程式內購買回條時, 將它包含在傳送至 Apple 伺服器的 JSON 承載中, 如下所示:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

如果購買有效, 回應的 [狀態] 欄位將會是零, 如同其他產品類型。

#### <a name="downloading-items-after-the-initial-subscription-term"></a>在初始訂閱期限之後下載專案

在傳遞訂閱產品時, 程式碼應該經常驗證對 Apple 伺服器的最新已知接收。 如果自上次驗證之後, 訂用帳戶已自動更新, JSON 回應會包含其他欄位, 以通知應用程式已發生交易 (這應該會擴充訂閱有效性)。 JSON 回應將包含:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

如果狀態為零, 則訂閱仍然有效, 且其他欄位會保存有效的資料。 如果狀態為 21006, 則訂用帳戶已過期。 如需其他錯誤碼, 請參閱[驗證自動可續訂的訂](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)用帳戶接收檔。

#### <a name="restoring-auto-renewable-subscriptions"></a>還原自動可續訂訂閱

您將會得到多個交易, 也就是原始的購買交易, 再加上訂閱更新的每一段時間的個別交易。 您需要追蹤開始日期和詞彙, 以瞭解有效期間。   
   
   
   
 SKPaymentTransaction 物件不包含訂用帳戶期限–您應該針對每個詞彙使用不同的產品識別碼, 並撰寫可從交易的購買日期推斷訂閱期間的程式碼。

#### <a name="testing-auto-renewal"></a>測試自動續約

為了讓您更輕鬆地測試訂閱, 在沙箱中測試時, 會壓縮其持續時間。 1周訂閱每3分鐘更新一次, 1 年訂閱每小時更新一次。 在沙箱中測試時, 訂用帳戶最多會自動更新6次。

## <a name="reporting"></a>報表

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 提供:   
   
 **銷售和趨勢**–顯示應用程式下載、更新和應用程式內購買的詳細資料。   
   
 **付款和財務報表**-詳述您的應用程式所獲得的收益, 以及列出已支付的款項和您所欠的金額。

範例銷售和趨勢報表如下所示:   

 [![](subscriptions-and-reporting-images/image42.png "範例銷售和趨勢報表")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 另外還有一個[ **[行楷] [連線] [** 行動 IOS 應用程式] (iTunes 連結)](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8)。
以下顯示一些可用統計資料的 iPhone 螢幕擷取畫面:   
   
 [![](subscriptions-and-reporting-images/image43.png "一些可用統計資料的 iPhone 螢幕擷取畫面")](subscriptions-and-reporting-images/image43.png#lightbox)
