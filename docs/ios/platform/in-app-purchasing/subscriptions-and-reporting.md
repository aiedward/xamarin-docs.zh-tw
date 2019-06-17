---
title: 訂用帳戶和 Xamarin.iOS 中的報告
description: 本文件說明非續約訂用帳戶，免費訂用帳戶，自動更新的訂用帳戶，以及報告這些項目上使用 iTunes Connect。
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4e63894cb862db3b5b5a1e7a2bebd79160c311a9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61367313"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>訂用帳戶和 Xamarin.iOS 中的報告

## <a name="about-non-renewing-subscriptions"></a>關於非續約訂用帳戶

非更新訂用帳戶用於表示服務具有時間限制，例如 （一週瀏覽應用程式存取） 或時間有限的存取權的資料封存的銷售的產品。   
   
非續約訂用帳戶和其他產品類型之間的主要差異：

-  在 iTunes Connect 中的產品定義不包含詞彙。 應用程式程式碼必須能夠推斷有效期間，從產品的識別碼。 
-  他們可以購買多次 （例如可取用的產品）。 需要應用程式管理的訂用帳戶詞彙/到期和更新，並避免使用者購買重疊的訂用帳戶。 
-  StoreKit 還原函式不支援購買項目。 如果訂用帳戶應該是可跨所有使用者的裝置，應用程式必須設計及實作這項功能搭配遠端伺服器。 應用程式也會負責備份的情況下的訂用帳戶狀態的裝置時備份然後還原從-備份。 
-  實作概觀
-  使用伺服器傳遞工作流程和管理方式跟可取用產品，通常應該實作非續約訂用帳戶。 


## <a name="about-free-subscriptions"></a>關於免費訂用帳戶

免費訂用帳戶可讓開發人員的免費內容置於 Newsstand （它們不能在非 Newsstand 應用程式） 的應用程式。 一旦啟動免費訂用帳戶將可在所有使用者裝置上。 免費訂用帳戶永遠不會過期;它們只能在應用程式解除安裝時結束。

### <a name="implementation-overview"></a>實作概觀

免費訂用帳戶的行為更像自動更新的訂用帳戶。 應用程式必須在 iTunes Connect 中的免費訂用帳戶產品可供 'purchase'。 當使用者所購買，免費訂用帳戶購買像是自動更新的訂用帳戶產品驗證。 可以還原免費訂用帳戶的交易。


## <a name="about-auto-renewable-subscriptions"></a>關於自動採用可續訂訂用帳戶

自動更新的訂用帳戶主要用於 Newsstand 應用程式。 它們代表授與使用者存取動態內容的一段指定時間，這已在 iTunes Connect （設定範圍從 7 天到 1 年期） 中的產品。 訂用帳戶，自動更新之每個訂用帳戶，結束使用者的 Apple ID 收費，除非使用者選擇外。此產品類型適用於雜誌或新聞訂用帳戶，其中使用者會取得每一期發行其訂用帳戶有效時的存取。

### <a name="implementation-overview"></a>實作概觀

自動更新的訂用帳戶應該使用 Server-Delivered 產品工作流程來實作 (請參閱*收據驗證和 Server-Delivered 產品*區段)。

#### <a name="shared-secret"></a>共用祕密

驗證您的伺服器上的自動更新訂用帳戶時，必須 JSON 要求中使用應用程式內購買共用密碼。 共用的密碼是透過 iTunes Connect 建立/存取。

從 iTunes Connect 首頁上選取**My Apps**:   
   
 [![](subscriptions-and-reporting-images/image2.png "選取 [我的應用程式]")](subscriptions-and-reporting-images/image2.png#lightbox)  
 
選取應用程式，然後按一下**應用程式內購買** 索引標籤：

[![](subscriptions-and-reporting-images/image6.png "按一下 [應用程式內購買] 索引標籤")](subscriptions-and-reporting-images/image6.png#lightbox)

從頁面底部，選取**檢視，或產生共用的密碼**:
   
 [![](subscriptions-and-reporting-images/image40.png "選取 [檢視]，或產生共用祕密")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "產生共用祕密")](subscriptions-and-reporting-images/image41.png#lightbox)   
   
   
   
 若要使用的共用的密碼，請將它包含在傳送至 Apple 的伺服器驗證的自動更新的訂用帳戶，就像這樣的應用程式內購買收據時的 JSON 承載：

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

回應的 [狀態] 欄位將是零，如果購買有效的做為其他產品類型。

#### <a name="downloading-items-after-the-initial-subscription-term"></a>在初始的訂閱期限後下載的項目

在訂用帳戶產品提供，程式碼應該經常確認最新已知回條針對 Apple 的伺服器。 如果訂用帳戶已自動更新自前一次驗證，JSON 回應會包含通知之交易的發生應用程式的其他欄位 （這應該延長訂用帳戶有效）。 將會包含 JSON 回應：

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

如果狀態為零，訂用帳戶是否仍然有效，以及其他欄位保留有效的資料。 如果狀態是 21006 訂用帳戶已過期。 請參閱[確認自動更新的訂用帳戶接收](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)其他錯誤碼的文件。

#### <a name="restoring-auto-renewable-subscriptions"></a>還原自動採用可續訂訂用帳戶

您會取得多個交易 – 原始的購買交易加上個別的交易，每一段時間的訂用帳戶已更新。 您要追蹤的開始日期和詞彙，以了解有效期間為何。   
   
   
   
 SKPaymentTransaction 物件不包含訂用帳戶一詞，您應該使用不同的產品識別碼，每個詞彙，並撰寫程式碼，可以進行推斷的交易從購買日期起的訂用帳戶期間。

#### <a name="testing-auto-renewal"></a>測試自動更新

若要讓您更輕鬆地測試訂用帳戶，在沙箱中測試時，會壓縮其持續時間。 1 週的訂用帳戶續約每隔 3 分鐘，訂用帳戶續約每隔一小時的 1 年。 訂用帳戶將自動續約 6 次，在沙箱中測試時的最大值。

## <a name="reporting"></a>報告

iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 提供：   
   
 **銷售與趨勢**– 顯示詳細資料，應用程式的下載、 更新和應用程式內購買項目。   
   
 **付款和財務報表**– 由您的應用程式以及已對您和您營業稅多少的清單付款所獲得的平均收入將詳細說明。

銷售和趨勢報表範例如下所示：   

 [![](subscriptions-and-reporting-images/image42.png "銷售和趨勢報表範例")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 另外還有[**細明體連線的行動**iOS 應用程式 （iTunes 連結）](http://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8)。
iPhone 的一些可用的統計資料的螢幕擷取畫面如下所示：   
   
 [![](subscriptions-and-reporting-images/image43.png "iPhone 的一些可用的統計資料的螢幕擷取畫面")](subscriptions-and-reporting-images/image43.png#lightbox)
