---
title: 在應用程式內購買基本知識和設定 Xamarin.iOS
description: 本文件說明 Xamarin.iOS，討論相關資訊的相關規則、 組態和 iTunes Connect 中的應用程式內購買。
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 9ded160ad4b31346c400e63d739a3dc21f6304d3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787239"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>在應用程式內購買基本知識和設定 Xamarin.iOS

實作在應用程式內購買需要利用 StoreKit API，在裝置上的應用程式。 StoreKit 管理所有與 Apple iTunes 伺服器以取得產品資訊並執行交易的通訊。 佈建設定檔必須設定應用程式內購買，並在 iTunes Connect 中必須輸入產品資訊。

 [![](in-app-purchase-basics-and-configuration-images/image1.png "這個圖表中所示，StoreKit 管理所有與 Apple 的通訊")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

使用以提供應用程式內購買的應用程式存放區需要下列安裝及設定：

-  **iTunes Connect** -設定要銷售的產品，並設定沙箱使用者帳戶，來測試購買。 您必須也提供您的銀行與稅務資訊給 Apple 讓它們可以 remit 收集代表您的款項。
-   **iOS 佈建入口網站**– 建立套件組合識別碼，並啟用您的應用程式的應用程式存放區存取。
-  **儲存套件**– 顯示產品、 購買的產品和還原交易的應用程式中加入程式碼。
-  **自訂程式碼**– 若要追蹤的客戶購物記錄，以及提供的產品或服務已購買。 您可能也需要實作伺服器端處理程序驗證回條，如果您的產品所組成 （例如書籍和雜誌問題） 的伺服器從下載的內容。


有兩個存放區套件 」 伺服器環境 」:

-  **生產**– 實際金額的交易。 只可透過存取提交並核准的 Apple 應用程式。 在應用程式內購買的產品也要檢閱並核准它們才可供在實際執行環境。
-  **沙箱**– 在您的測試中發生。 產品所提供的以下 （核准程序僅適用於生產環境） 的建立後立即。 在沙箱中的交易都需要測試使用者 (不是真正的 Apple Id) 來執行交易。

## <a name="in-app-purchase-rules"></a>在應用程式內購買規則

您無法接受其他形式的付款數位產品或服務應用程式內，也不提到它們或參考您的使用者從應用程式中。 這表示，您無法接受信用卡或 PayPal 時應用程式內購買的最適當的付款機制。 特殊的情況下，購買應用程式外的數位產品，但對於使用，例如購買網站上特定 「 登入 」 相關聯的活頁簿，以及使用應用程式中的 「 登入 」 可讓使用者存取應用程式中購買的書籍。
應用程式運作，如此一來，不允許有提及，或連結至外部購買功能-開發人員必須進行這項功能其使用者 （也許是透過電子郵件行銷或其他直接的通道） 的其他方式來通訊。

不過，因為您無法使用應用程式內購買之項目實體的貨物，此時您可以使用替代付款機制 （例如 信用卡、 paypal 等） 的應用程式內。

Apple 必須核准每個產品之前它就會在銷售 – name、 description 與螢幕擷取畫面的 產品需要檢閱。 產品檢閱時間都與應用程式檢閱相同。

您無法選擇任何價格為您的產品，您只可以選取的 '價格層' Apple 支援每個國家/地區/貨幣具有特定值。 您不能有不同的價格層不同市場。

## <a name="configuration"></a>組態

寫入任何應用程式內購買程式碼之前，您必須執行一些設定工作，在 iTunes Connect 中的 ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 和 iOS 佈建入口網站 ( [developer.apple.com/iOS](http://developer.apple.com/iOS))。

這三個步驟之前撰寫任何程式碼應該就完成：

-  **Apple 開發人員帳戶**– 送出您的銀行和課稅資訊給 Apple。
-  **iOS 佈建入口網站**– 確保您的應用程式具有有效的應用程式識別碼 (不包含星號萬用字元 * 中它) 並在購買應用程式啟用。
-  **iTunes Connect 應用程式管理**– 將產品加入您的應用程式。


### <a name="apple-developer-account"></a>Apple 開發人員帳戶

建立和分配給可用的應用程式需要在組態設定很少[iTunes Connect](https://itunesconnect.apple.com)，但是銷售付費的應用程式或應用程式內購買要求您提供 Apple 銀行與課稅資訊。 按一下**合約、 稅金和銀行**從主功能表如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image2.png "按一下 在協議、 稅金和銀行從主功能表")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

開發人員帳戶應擁有**iOS 付費的應用程式**合約作用中，此螢幕擷取畫面所示：

 [![](in-app-purchase-basics-and-configuration-images/image3.png "應有付費的應用程式作用中合約的 iOS 開發人員帳戶。")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

您不能測試任何 StoreKit 功能，直到您有**iOS 付費的應用程式**合約： StoreKit 呼叫程式碼中的將會失敗 Apple 已處理完之前您**合約、 稅金和銀行**資訊。

### <a name="ios-provisioning-portal"></a>iOS 佈建入口網站

新的應用程式中所設**應用程式識別碼**區段**iOS 佈建入口網站**。 若要建立新的應用程式識別碼，請移至[的 ios 佈建入口網站的會員中心](https://developer.apple.com/membercenter/index.action)，瀏覽至**憑證、 識別碼和設定檔**> 一節的入口網站，然後按一下**的識別項**下*的 iOS 應用程式*。 然後，按一下"+"在上方的權限產生新的應用程式識別碼。


建立新的表單**應用程式識別碼**

 看起來像這樣：

 [![](in-app-purchase-basics-and-configuration-images/image4.png "表單的表單建立新的應用程式識別碼")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

輸入的內容適用於*描述*，因此您可以輕鬆地識別此清單中的應用程式識別碼。 如*應用程式識別碼首碼*，選取 小組的識別碼。

#### <a name="bundle-identifierapp-id-suffix-format"></a>套件組合識別碼/應用程式識別碼後置詞格式

您可以使用您想要的任何字串您**配套識別碼**（只要是在您的帳戶中是唯一的），不過 Apple 建議您遵循反向 DNS 格式而非使用任意字串。 本文章的範例應用程式套件組合識別碼，使用 com.xamarin.storekit.testing，不過它會為要使用的識別碼，就像 my_store_example （即使 Apple 不建議使用） 也一樣有效。

> [!IMPORTANT]
> Apple 也允許加入結尾的萬用字元星號**配套識別碼**使單一的應用程式識別碼可用於多個應用程式，不過_萬用字元應用程式識別碼不能用於 AppPurchase_. 範例可能 com.xamarin.* 萬用字元配套識別碼。

#### <a name="enabling-app-services"></a>啟用應用程式服務

請注意，**中應用程式內購買**將會自動啟用服務 清單中：

 [![](in-app-purchase-basics-and-configuration-images/image5.png "在 [服務] 清單中將自動啟用應用程式內購買")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>佈建設定檔

通常，選取您已設定應用程式內購買的應用程式識別碼，請建立開發和生產環境佈建的設定檔。 請參閱[iOS 裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)和[發行的應用程式市集](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)輔助線，如需詳細資訊。

## <a name="itunes-connect"></a>iTunes Connect

按一下**我的應用程式**在 iTunes Connect 來建立或編輯的 iOS 應用程式項目中。 應用程式的 [概觀] 頁面將如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image6.png "應用程式的 [概觀] 頁面")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

按一下**應用程式內購買**來建立或編輯您的產品銷售。 這個螢幕擷取畫面顯示已加入數個產品範例應用程式：

 [![](in-app-purchase-basics-and-configuration-images/image7.png "有幾項產品已經加入範例應用程式")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

將新的產品的程序有兩個步驟：

1.   選擇的產品類型： [![](in-app-purchase-basics-and-configuration-images/image8.png "選擇的產品類型")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   輸入產品的屬性，包括產品識別碼、 定價層和當地語系化的描述： [![](in-app-purchase-basics-and-configuration-images/image9.png "輸入的產品屬性")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

每個應用程式內購買的產品所需的欄位如下所示：


### <a name="reference-name"></a>參考名稱

參考名稱不會顯示給使用者。它僅供內部使用，並在 iTunes Connect 中，才會出現。

### <a name="product-id-format"></a>產品識別碼格式

產品識別碼只能包含英數字元 (A-Z、 a-z、 0-9)、 底線 (_) 和句號 （.） 字元。 雖然您可以使用任何字串，您的識別項，Apple 建議反向 DNS 格式。 例如，範例應用程式會使用這個套件組合識別碼：

 `com.xamarin.storekit.testing`

因此來識別應用程式內購買產品的慣例會，如下所示：

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

不會強制此命名慣例，只是建議可幫助您管理您的產品。 此外，儘管有遵循相同的反向 DNS 慣例，產品識別碼是*無關*到配套識別碼且不需要即可開始使用相同的字串。 仍是有效的 （即使 Apple 不建議） 使用像 photo_product_greyscale 的識別項。

產品識別碼不會看到您的使用者，但它用來參考您的應用程式程式碼中的產品。

### <a name="product-type"></a>產品類型

有五種類型的應用程式內購買產品，您可以提供：

1.  **您可以使用**– 項 '使用向上'，例如，播放程式，可以縮短對遊戲中貨幣。 如果使用者沒有備份/還原或否則他們的裝置重新整理，您可以使用的交易不會不取得還原也 （這會有效地讓播放程式重複相同的優點）。 應用程式程式碼必須確定在交易完成時，立即提供 '您可以使用項目'。
1.  **非可使用**– 使用者 '擁有' 一次購買產品，例如數位雜誌問題或遊戲的層級。
1.  **自動更新的訂閱**– 只要像真實世界雜誌訂用帳戶，訂用期間結束 Apple 自動再次付費客戶並擴充訂用帳戶的詞彙不限次數，或直到客戶明確取消它。 這是 Newsstand 應用程式的慣用的付款方式 （事實上，應用程式必須支援此付款方式 Newsstand 發佈核准）。
1.  **免費訂用帳戶**– 只可以提供在 Newsstand 啟用應用程式，並讓他們的裝置上的客戶存取訂用帳戶的內容。 免費訂用帳戶永遠不過期。
1.  **非更新訂用帳戶**– 應用於賣出時間限制存取，來提供靜態內容，例如一個月的相片封存的存取。


 *這份文件目前涵蓋只能使用第一個兩個產品類型 （可使用和非可使用）。*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>價格層

應用程式存放區不會讓您選擇任意的價格的產品，– Apple 提供您可以從選擇的固定的價格層。 價格固定的每個貨幣和 Apple 保留來調整相對的價格 （例如，在特定的貨幣與美國貨幣之間的相對外幣匯率速率持續變更） 後的權限。

Apple 提供價格矩陣，以協助您選取正確的層次，針對您想要貨幣/價格。 價格矩陣 (年 8 月 2012) 的摘錄，如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image10.png "摘錄自價格矩陣 2012 年 8 月")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

在撰寫 (2013) 時有從 USD 87 層以 USD 999.99 0.99。 定價矩陣顯示價格，您的客戶將付費和也的量，您會收到來自 Apple – 這是其 30%的電量小於以及任何本機稅金這些需要收集 （請注意，在美國和加拿大地區的銷售，接收 99 的 c p 70 c 範例roduct，而澳洲的銷售接收僅 63 c，因為 '貨物&amp;服務稅' levied 銷售價格上)。

在任何時間，包括排程的價格變更才會生效，在未來的日期可以更新您的產品價格。 這個螢幕擷取畫面顯示新增的未來日期的價格變更的方式 – 價格會被暫時從變成第 1 層第 3 層年 9 月的月只：

 [![](in-app-purchase-basics-and-configuration-images/image11.png "其中價格已經暫時變更從第 1 層到第 3 層年 9 月的月份只未來日期的價格變動")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>不支援免費產品

雖然 Apple 已提供 Newsstand 應用程式的特殊免費訂用帳戶選項，但它不可以針對任何其他應用程式內購買類型設定為零 （免費） 的價格。 雖然您可以編輯 (亦即。 較低) 的促銷價格，您不能在應用程式內購買 「 免費 」 透過 iTunes Connect。

### <a name="localization"></a>當地語系化

在 iTunes Connect 中，您可以輸入任意數目的受支援的語言不同的名稱和描述文字。 每一種語言可以加入/編輯中透過快顯視窗：

 [![](in-app-purchase-basics-and-configuration-images/image12.png "每一種語言可以加入/編輯中透過快顯視窗")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 當您在應用程式中顯示產品資訊時，當地語系化的文字是可供您透過 StoreKit 顯示。 也必須以顯示正確的符號和十進位格式 – 此格式文件稍後涵蓋當地語系化貨幣顯示。

### <a name="app-store-review"></a>檢閱應用程式市集

相同應用程式 – 每個產品檢閱由 Apple 才能移上銷售資料。 產品不適當的內容中的名稱或描述，可能會遭到拒絕或 Apple 可能會決定您所選擇的不正確的產品類型 （例如。 您已建立書籍或雜誌問題，但使用的可消耗的產品類型）。 產品評論可能需要長達應用程式檢視。

第一次應用程式送出應用程式內購買啟用 （不論它是新的應用程式或功能已經加入至現有） 您也必須選擇與它送出某些產品。 在 iTunes Connect 入口網站會提示您這樣做，請如下列螢幕擷取畫面所示：

 [![](in-app-purchase-basics-and-configuration-images/image13.png "在 iTunes Connect 入口網站會提示您送出某些產品以及")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 應用程式與應用程式內購買項目將檢閱在一起，使其所有核准一次 （讓該應用程式不會傳送到存放區中沒有任何已核准的產品 ！）。

核准您的第一個版本中應用程式內購買功能之後，您可以新增其他產品，並將它們送出供隨時檢閱。 您也可以選擇要提交新的版本特定的應用程式內購買的產品，以及使用**版本詳細資料**頁面如提示所示。

請參閱[應用程式市集審查指導方針](https://developer.apple.com/appstore/guidelines.html)如需詳細資訊。

 [組件 2-存放區組件概觀和擷取產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
