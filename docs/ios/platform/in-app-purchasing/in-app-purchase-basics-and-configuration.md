---
title: Xamarin 中的應用程式內購買基本概念和設定
description: 本檔說明在 Xamarin 中的應用程式內購買, 討論規則、設定和 iTunes Connect 的相關資訊。
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 4c8e08c5393bed1f96baa7c1bced85eed3d2d0c6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527911"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Xamarin 中的應用程式內購買基本概念和設定

若要執行應用程式內購買, 應用程式必須在裝置上使用 StoreKit API。 StoreKit 會管理與 Apple iTunes 伺服器的所有通訊, 以取得產品資訊並執行交易。 必須針對應用程式內購買設定布建設定檔, 而且必須在 iTunes Connect 中輸入產品資訊。

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit 會管理與 Apple 的所有通訊, 如下圖所示")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

使用 App Store 來提供應用程式內購買需要下列安裝和設定:

- **ITunes Connect** –將產品設定為銷售和設定沙箱使用者帳戶, 以測試購買。 您也必須提供您的銀行和稅務資訊給 Apple, 讓他們可以代表您所收集的資金。
- **iOS 布建入口網站**–建立套件組合識別碼, 並啟用應用程式的 app Store 存取。
- **商店套件**–將程式碼新增至您的應用程式, 以顯示產品、購買產品和還原交易。
- **自訂程式碼**–用來追蹤客戶所進行的購買, 並提供他們所購買的產品或服務。 如果您的產品是由從伺服器下載的內容 (例如書籍和雜誌問題) 所組成, 您也可能需要執行伺服器端程式來驗證收據。


有兩個存放套件「伺服器環境」:

- **生產**–實際費用的交易。 只能透過 Apple 提交及核准的應用程式存取。 應用程式內購買產品在生產環境中可供使用之前, 也必須先經過審查和核准。
- **沙箱**–發生測試的位置。 產品會在建立之後立即提供 (核准程式僅適用于生產環境)。 沙箱中的交易需要測試使用者 (不是真實的 Apple Id) 來執行交易。

## <a name="in-app-purchase-rules"></a>應用程式內購買規則

您不能在應用程式內接受數位產品或服務的其他付款形式, 也不能在應用程式中提及或參考您的使用者。 這表示當應用程式內購買是最適當的付款機制時, 您無法接受信用卡或 PayPal。 在應用程式外購買數位產品, 但在應用程式中使用的特殊案例, 例如在與特定「登入」相關聯的網站上購買書籍, 並在應用程式中使用該「登入」, 可讓使用者存取購買的書籍。
以這種方式運作的應用程式不允許提及或連結至外部購買功能–開發人員必須以其他方式 (可能是透過電子郵件行銷或其他直接頻道) 與使用者溝通這項功能。

不過, 由於您無法使用實體商品的應用程式內購買, 因此, 您可以使用替代的付款機制 (例如, 信用卡, PayPal)。

Apple 必須先核准每個產品, 才會上線–需要「產品」的名稱、描述和螢幕擷取畫面才能進行審查。 產品審核時間與應用程式評論相同。

您不能選擇產品的任何價格–您只能選取在 Apple 支援的每個國家/地區具有特定值的「價格層」。 不同市場中不能有不同的定價層。

## <a name="configuration"></a>組態

在撰寫任何應用程式內購買程式碼之前, 您必須先在 iTunes Connect ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 和 iOS 布建入口網站 ( [developer.apple.com/iOS](https://developer.apple.com/iOS)) 中進行一些設定工作。

在撰寫任何程式碼之前, 必須先完成這三個步驟:

- **Apple 開發人員帳戶**–將您的銀行和稅務資訊提交給 apple。
- **iOS 布建入口網站**–確保您的應用程式具有有效的應用程式識別碼 (不是具有星號 * 的萬用字元), 而且已啟用應用程式購買。
- **ITunes Connect 應用程式管理**–將產品新增至您的應用程式。


### <a name="apple-developer-account"></a>Apple 開發人員帳戶

建立和散發免費的應用程式需要在[ITunes Connect](https://itunesconnect.apple.com)中設定非常少, 不過若要銷售付費應用程式或應用程式內購買, 您必須為 Apple 提供銀行和稅務資訊。 按一下主功能表中的 [**協定]、[稅務和銀行],** 如下所示:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "按一下主功能表上的 [協定]、[稅務和銀行]")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

您的開發人員帳戶應具有作用中的**IOS 付費應用程式**合約, 如下列螢幕擷取畫面所示:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "您的開發人員帳戶應該有 iOS 付費應用程式合約生效")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

在您擁有**IOS 付費應用程式**合約之前, 您將無法測試任何 StoreKit 功能, 除非 Apple 已處理您的**合約、稅務和銀行**資訊, 否則您的程式碼中的 StoreKit 呼叫將會失敗。

### <a name="ios-provisioning-portal"></a>iOS 佈建入口網站

新的應用程式會在 IOS 布建**入口網站**的 [**應用程式識別碼**] 區段中設定。 若要建立新的應用程式識別碼, 請移至 iOS 布建[入口網站的成員中心](https://developer.apple.com/membercenter/index.action), 流覽至入口網站的 [**憑證、識別碼及設定檔**] 區段, 然後按一下 [ *iOS 應用程式*] 底下的 [**識別碼**]。 然後按一下右上方的 "+", 以產生新的應用程式識別碼。


用來建立新**應用程式識別碼**的表單

 如下所示:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "用來建立新應用程式識別碼的表單")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

輸入適用于*描述*的內容, 以便您可以在清單中輕鬆地識別此應用程式識別碼。 針對 [*應用程式識別碼] 前置*詞, 選取 [小組識別碼]。

#### <a name="bundle-identifierapp-id-suffix-format"></a>套件組合識別碼/應用程式識別碼尾碼格式

您可以使用您喜歡的任何字串作為套件組合**識別碼**(只要它在您的帳戶中是唯一的), 不過 Apple 建議您遵循反向 DNS 格式, 而不要使用任何任一字元串。 本文隨附的範例應用程式會使用 storekit 來測試套件組合識別碼, 不過, 使用類似 my_store_example 的識別碼 (即使 Apple 不建議它) 也會同樣有效。

> [!IMPORTANT]
> Apple 也允許將萬用字元星號新增至組合**識別碼**的結尾, 讓單一應用程式 id 可以用於多個應用程式, 但_萬用字元應用程式識別碼無法用於 AppPurchase_。 例如, 萬用字元套件組合識別碼可能是 .com. *

#### <a name="enabling-app-services"></a>啟用應用程式服務

請注意,**應用程式內購買**會在 [服務] 清單中自動啟用:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "應用程式內購買會自動在服務清單中啟用")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>佈建設定檔

如往常般建立開發和生產布建設定檔, 並選取您為應用程式內購買所設定的應用程式識別碼。 如需詳細資訊, 請參閱[IOS 裝置](~/ios/get-started/installation/device-provisioning/index.md)布建和[發佈至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)指南。

## <a name="itunes-connect"></a>iTunes Connect

按一下 iTunes Connect 中的 [**我的應用程式**], 以建立或編輯 iOS 應用程式專案。 [應用程式總覽] 頁面如下所示:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "應用程式總覽頁面")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

按一下 [**應用程式內購買**] 以建立或編輯您的產品以進行銷售。 此螢幕擷取畫面顯示已新增數個產品的範例應用程式:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "已新增數個產品的範例應用程式")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

加入新產品的程式有兩個步驟:

1. 選擇產品類型:[![](in-app-purchase-basics-and-configuration-images/image8.png "選擇產品類型")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2. 輸入產品的屬性, 包括產品識別碼、定價層和當地語系化的描述:[![](in-app-purchase-basics-and-configuration-images/image9.png "輸入 products 屬性")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

以下說明每個應用程式內購買產品所需的欄位:


### <a name="reference-name"></a>參考名稱

參考名稱不會對您的使用者顯示;僅供內部使用, 而且只會出現在 iTunes Connect 中。

### <a name="product-id-format"></a>產品識別碼格式

產品識別碼只能包含英數位元 (a-z、a-z、0-9)、底線 (_) 和句號 (.) 字元。 雖然您可以將任何字串用於識別碼, 但 Apple 會建議使用反向 DNS 格式。 例如, 範例應用程式會使用此配套識別碼:

 `com.xamarin.storekit.testing`

因此, 用來識別應用程式內購買產品的慣例如下所示:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

此命名慣例不會強制執行, 只是協助您管理產品的建議。 此外, 儘管遵循相同的反向 DNS 慣例, 產品識別碼並不會與套件組合識別碼*相關*, 而且也不需要以相同的字串開頭。 使用識別碼 (例如 photo_product_greyscale) 仍然有效 (即使 Apple 不建議這麼做)。

產品識別碼不會顯示給您的使用者, 但會用來參考應用程式代碼中的產品。

### <a name="product-type"></a>產品類型

有五種類型的應用程式內購買產品可供您提供:

1. 取用–「已用完」的專案, 例如玩家可以花費的遊戲中貨幣。 如果使用者執行備份/還原, 或以其他方式重新整理其裝置, 則也不會還原可取用的交易 (這會有效地讓玩家再次獲得相同的權益)。 當交易完成時, 應用程式代碼必須務必提供「可耗用專案」。
1. **非**取用–使用者「擁有」購買的產品, 例如數位雜誌問題或遊戲等級。
1. **自動可續訂訂閱**–就像是實際的雜誌訂用帳戶一樣, 在訂閱期間結束時, Apple 會再次自動向客戶收費並延長訂用帳戶期限, 永遠或直到客戶明確取消為止。 這是 Newsstand 應用程式的慣用付款條件 (事實上, 應用程式必須支援此付款方法以核准 Newsstand 散發)。
1. **免費訂**用帳戶–只能在啟用 Newsstand 的應用程式中提供, 並可讓客戶存取其所有裝置上的訂用帳戶內容。 免費訂閱永遠不會過期。
1. **非續約訂**用帳戶–應該用來銷售對靜態內容的限時存取, 例如一個月的相片封存存取權。


 *本檔目前僅涵蓋前兩個產品類型 (可耗用和非可耗用)。*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>定價層

App Store 不會讓您為您的產品選擇任意價格– Apple 提供您可以選擇的固定價格層。 價格是以每個貨幣來固定, 而 Apple 保留調整相對價格的權利 (例如, 在特定貨幣與美國元之間的相對外匯率持續變更之後)。

Apple 提供價格對照表, 協助您針對所需的貨幣/價格選取正確的層。 價格矩陣的摘錄 (2012 年8月) 如下所示:

 [![](in-app-purchase-basics-and-configuration-images/image10.png "2012年8月價格對照表的摘錄")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

在撰寫本文時 (2013 年6月), 有87層從美元0.99 到美元的999.99。 定價矩陣會顯示您的客戶將支付的價格, 以及您將從 Apple 收到的金額–這少於 30% 的費用, 也會收取任何需要收集的當地稅金 (請注意, 美國和加拿大賣方接收 70c 99c p 的範例產品, 而澳大利亞銷售人員只會收到 63c, 因為&amp; 「貨物服務稅務」會收取售價。

您產品的定價可以隨時更新, 包括在未來日期生效的排程價格變更。 此螢幕擷取畫面顯示如何新增未來的價格變更–從第1層到第3層的價格暫時變更為9月份:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "未來的價格變更, 其價格將從第1層暫時改為9月份的第3層")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>不支援的免費產品

雖然 Apple 已為 Newsstand apps 提供特殊的免費訂用帳戶選項, 但不可能為任何其他應用程式內購買類型設定零 (免費) 的價格。 雖然您可以編輯 (如較低) 銷售促銷的價格, 但無法透過 iTunes Connect 讓應用程式內購買「免費」。

### <a name="localization"></a>當地語系化

在 iTunes Connect 中, 您可以為任何數目的支援語言輸入不同的名稱和描述文字。 在中, 您可以透過快顯來新增/編輯每種語言:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "在中, 您可以透過快顯來新增/編輯每種語言")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 當您在應用程式中顯示產品資訊時, 可透過 StoreKit 顯示當地語系化的文字。 您也必須當地語系化貨幣顯示, 以顯示正確的符號和十進位格式–此格式會在本檔稍後討論。

### <a name="app-store-review"></a>App Store 審查

與應用程式相同– Apple 會先審查每個產品, 然後才可以開始使用。 產品可能會因為名稱或描述中不適當的內容而遭到拒絕, 或 Apple 可能會決定您選擇了錯誤的產品類型 (例如, 您已建立書籍或雜誌的問題, 但使用的是可取用的產品類型)。 只要應用程式審查, 產品評論就可以進行。

第一次提交應用程式時, 若已啟用應用程式內購買 (無論是新的應用程式, 或是已新增功能), 您也必須選擇要與它一起提交的產品。 ITunes Connect 入口網站會提示您執行這項操作, 如下列螢幕擷取畫面所示:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "ITunes Connect 入口網站也會提示您提交一些產品")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 應用程式和應用程式內購買將會一起審核, 讓他們一次獲得核准 (如此一來, 應用程式就不會進入存放區, 而不會有任何核准的產品!)。

當您的第一個版本具有應用程式內購買功能之後, 您可以隨時新增並提交進一步的產品, 以供日後審查。 您也可以選擇隨特定的應用程式內購買產品一起提交新版本, 並使用 [**版本詳細資料**] 頁面做為提示的建議。

如需詳細資訊, 請參閱[App Store 審查指導方針](https://developer.apple.com/appstore/guidelines.html)。

 [第2部分-商店套件總覽和檢索產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
