---
title: 在應用程式內購買基本概念和在 Xamarin.iOS 中的設定
description: 本文件說明 Xamarin.iOS、 規則、 設定和 iTunes Connect 的相關討論相關的資訊中的應用程式內購買。
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 267dac5b6aec263f1d8b69d81f34f732118c1802
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61406812"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>在應用程式內購買基本概念和在 Xamarin.iOS 中的設定

實作應用程式內購買項目需要利用 StoreKit API，在裝置上的應用程式。 StoreKit 管理與 Apple iTunes 伺服器，以取得產品資訊，並執行交易的所有通訊。 佈建設定檔必須由應用程式內購買，並在 iTunes Connect 中，必須輸入產品資訊。

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit 管理所有與 Apple 的通訊，此圖中所示")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

使用以提供應用程式內購買的應用程式存放區需要下列設定，並設定：

-  **iTunes Connect** – 設定銷售的產品，並設定沙箱使用者帳戶，以測試購買。 您必須也提供您的銀行和稅務資訊給 Apple 讓它們可以匯款代替您收集的資金。
-   **iOS 佈建入口網站**– 建立套件組合識別碼，並啟用您的應用程式的應用程式存放區存取。
-  **儲存套件**– 程式碼加入至您的應用程式，顯示產品、 購買產品和還原的交易。
-  **自訂程式碼**– 若要追蹤客戶的購物記錄，以及提供的產品或他們所購買的服務。 您可能也需要實作伺服器端程序，以驗證回條，如果您的產品是由從伺服器 （例如書籍和雜誌的問題） 下載的內容所組成。


有兩個套件存放區 」 伺服器環境 」:

-  **生產**– 與實際成本的交易。 只可透過存取提交和核准的 Apple 應用程式。 必須也檢閱並核准才可在實際執行環境中應用程式內購買產品。
-  **沙箱**-您的測試會發生。 產品會提供以下建立 （核准程序僅適用於生產環境） 的後面。 在沙箱中的交易要求來執行交易的測試使用者 (並非真正的 Apple Id)。

## <a name="in-app-purchase-rules"></a>在應用程式內購買規則

您無法接受您的應用程式內的付款數位產品或服務的其他形式，也不提及它們或請參閱您的使用者從應用程式中。 這表示，您無法接受信用卡或 PayPal 時應用程式內購買的最適當的付款機制。 購買應用程式外的數位產品的特殊案例，但對於使用，例如購買網站上特定 「 登入 」 相關聯的書籍，以及使用應用程式中的 [登入] 可讓使用者存取的應用程式的已購買的書籍。
提及或連結至外部購買的功能不允許操作這種方式的應用程式 – 開發人員必須傳達給使用者以其他方式 （也許是透過電子郵件行銷或一些其他的直接通道） 這項功能。

不過，因為您無法使用應用程式內購買之項目實體的產品，允許您的案例可以使用替代的付款機制 （例如。 信用卡、 paypal 等） 從應用程式內。

Apple 必須核准每個產品之前它會在銷售 – 名稱、 描述以及螢幕擷取畫面的 產品所需的檢閱。 產品檢閱時間都是與應用程式評論時相同。

您無法選擇任何價格為您的產品，您可能只會選取 [價格層]，Apple 支援每個國家/貨幣會有特定的值。 您不能有不同的價格層不同的市場。

## <a name="configuration"></a>組態

撰寫任何應用程式內購買的程式碼之前，您必須執行一些設定工作，在 iTunes Connect 中的 ( [itunesconnect.apple.com](http://itunesconnect.apple.com)) 和 iOS 佈建入口網站 ( [developer.apple.com/iOS](https://developer.apple.com/iOS))。

撰寫任何程式碼之前應該完成這三個步驟：

-  **Apple 開發人員帳戶**– 提交給 Apple 銀行和稅務資訊。
-  **iOS 佈建入口網站**-請確定您的應用程式具有有效的應用程式識別碼 (不包含星號萬用字元 * 中)，並在購買應用程式啟用。
-  **iTunes Connect 應用程式管理**– 將產品加入至您的應用程式。


### <a name="apple-developer-account"></a>Apple 開發人員帳戶

建置和發佈免費的應用程式需要在組態設定很少[iTunes Connect](https://itunesconnect.apple.com)，但銷售付費應用程式或應用程式內購買要求您提供 Apple 銀行和稅務資訊。 按一下 **合約、 稅務和銀行**從主功能表如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image2.png "按一下 合約、 稅務和銀行從主功能表上")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

您的開發人員帳戶應有**iOS 付費應用程式**合約生效時，此螢幕擷取畫面所示：

 [![](in-app-purchase-basics-and-configuration-images/image3.png "應有付費應用程式合約作用中的 iOS 開發人員帳戶。")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

您不能測試任何 StoreKit 功能，直到處理完**iOS 付費應用程式**合約： StoreKit 呼叫程式碼中的將會失敗 Apple 已處理完之前您**合約、 稅金和銀行**資訊。

### <a name="ios-provisioning-portal"></a>iOS 佈建入口網站

新的應用程式中已設定**應用程式識別碼**一節**iOS 佈建入口網站**。 若要建立新的應用程式識別碼，請前往[Member Center 的 iOS 佈建入口網站](https://developer.apple.com/membercenter/index.action)，瀏覽至**憑證、 識別碼和設定檔**入口網站中，然後按一下 上的一節**識別碼**底下*iOS 應用程式*。 然後，按一下 [+] 頂端的權限產生新的應用程式識別碼。


建立新的表單**應用程式識別碼**

 看起來像這樣：

 [![](in-app-purchase-basics-and-configuration-images/image4.png "表單的表單建立新的應用程式識別碼")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

輸入適合*描述*，因此您可以輕鬆地識別此清單中的應用程式識別碼。 針對*應用程式識別碼的前置詞*，選取 「 小組識別碼 」。

#### <a name="bundle-identifierapp-id-suffix-format"></a>套件組合識別碼/應用程式識別碼後置詞格式

您可以使用您喜歡的任何字串您**套件組合識別碼**（只要它是在您的帳戶中是唯一的），然而，Apple 建議您遵循的反向 DNS 格式而不使用任何任意字串。 本文所附的範例應用程式套件組合識別碼，使用 com.xamarin.storekit.testing，不過，若要使用的識別碼，例如 my_store_example （即使 Apple 不建議採用） 同樣有效。

> [!IMPORTANT]
> Apple 也允許加入結尾的萬用字元星號**套件組合識別碼**以便單一的應用程式識別碼可用於多個應用程式，不過_萬用字元應用程式識別碼不能用於 AppPurchase_. 範例可能 com.xamarin.* 萬用字元套件組合識別碼。

#### <a name="enabling-app-services"></a>啟用應用程式服務

請注意，**應用程式內購買**將會自動啟用服務 清單中：

 [![](in-app-purchase-basics-and-configuration-images/image5.png "在 [服務] 清單中將自動啟用應用程式內購買")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>佈建設定檔

建立開發和生產佈建設定檔，您通常會選取您已設定應用程式內購買的應用程式識別碼。 請參閱[iOS 裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)並[發佈至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)輔助線，如需詳細資訊。

## <a name="itunes-connect"></a>iTunes Connect

按一下  **My Apps**在 iTunes Connect，以建立或編輯的 iOS 應用程式項目中。 應用程式的 [概觀] 頁面將如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image6.png "應用程式的 [概觀] 頁面")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

按一下 **應用程式內購買**建立或編輯您的產品銷售。 此螢幕擷取畫面顯示範例應用程式有幾項產品已經新增：

 [![](in-app-purchase-basics-and-configuration-images/image7.png "有幾項產品已經新增範例應用程式")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

新增新產品的程序有兩個步驟：

1.   選擇產品類型：[![](in-app-purchase-basics-and-configuration-images/image8.png "選擇產品類型")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2.   輸入產品的屬性，包括產品識別碼、 定價層和當地語系化的描述：[![](in-app-purchase-basics-and-configuration-images/image9.png "輸入的產品屬性")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

每個應用程式內購買產品所需的欄位如下所示：


### <a name="reference-name"></a>參考名稱

參考名稱不會顯示給使用者;它僅供內部使用，並在 iTunes Connect 中，才會出現。

### <a name="product-id-format"></a>產品識別碼格式

產品識別碼只能包含英數字元 (A-Z、 a-z、 0-9)、 底線 (_) 和句號 （.） 字元。 雖然您可以使用任何字串，您的識別項，Apple 建議的反向 DNS 格式。 例如，範例應用程式會使用這個套件組合識別碼：

 `com.xamarin.storekit.testing`

因此若要找出應用程式內購買產品的慣例會，如下所示：

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

不會強制執行此命名慣例，只是為了協助您管理您的產品建議。 此外，儘管遵循相同的反向 DNS 慣例，產品識別碼都*無關*到套件組合識別碼且不需要以相同的字串為開頭。 此外，您仍然可以有效使用像是 photo_product_greyscale 的識別項 （即使 Apple 不建議）。

產品識別碼不會顯示給使用者，但它用來參考您的應用程式程式碼中的產品。

### <a name="product-type"></a>產品類型

有五種類型的應用程式內購買產品，您可以提供：

1.  **需求的可取用**– 項目 '使用向上'，例如遊戲中播放程式可以花費的貨幣。 如果使用者進行備份/還原，否則他們的裝置重新整理需求的可取用的交易不會不取得一併還原 （這可有效地讓播放程式上一次相同的優點）。 應用程式程式碼必須確定提供可取用的 item'，因為在交易完成。
1.  **非取用**– 使用者 '擁有' 一次購買產品，例如數位雜誌問題或遊戲的層級。
1.  **自動更新的訂用帳戶**– 就像真實世界的雜誌訂閱，訂閱期間的結尾 Apple 自動再次費用的客戶並擴充訂用帳戶一詞，下去，或直到客戶明確取消它。 這是 Newsstand 應用程式的慣用的付款方式 （事實上，應用程式必須支援這種付款方法 Newsstand 發佈核准）。
1.  **免費訂用帳戶**– 才會提供在 Newsstand 啟用應用程式，並讓其所有裝置上的客戶存取訂用帳戶內容。 免費訂用帳戶 」 永不過期。
1.  **非更新訂用帳戶**– 應該用來銷售時間有限的存取權等靜態內容使用，一個月的相片封存權限。


 *本文件目前涵蓋只有前兩個產品類型 （可使用和非取用）。*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>價格層

應用程式存放區不會讓您選擇以任意的價格為您的產品，Apple 提供您可以選擇的固定的價格層。 固定在每個貨幣的價格和 Apple 保留的權限 （例如，在特定的貨幣與美元之間的相對外幣匯率速率持續變更） 後調整相對的價格。

Apple 提供價格矩陣，以協助您選取正確的層，您想要的貨幣/價格。 價格矩陣 (年 8 月 2012) 的摘錄，如下所示：

 [![](in-app-purchase-basics-and-configuration-images/image10.png "摘錄自 2012 年 8 月的價格矩陣")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

在撰寫 (2013) 的階段中，有 87 層從美元 USD 999.99 到 0.99。 定價矩陣會顯示的價格，您的客戶將付和也的量，您會收到來自 Apple – 這是小於其 30%費用以及任何當地稅金這些項目才能收集 （請注意，在美國和加拿大地區的銷售者會收到 99 的 c p 70 c 範例roduct，而澳洲的銷售者會收到僅 63 c，因為 '貨物&amp;服務稅' levied 的銷售價格)。

可以更新您的產品價格，在任何時間，包括排程的價格會在未來的日期上生效的變更。 此螢幕擷取畫面顯示如何加入的未來日期的價格變更 – 價格會被暫時變更從第 1 層到第 3 層九月的月刊只有：

 [![](in-app-purchase-basics-and-configuration-images/image11.png "其中價格會被暫時變更從第 1 層到第 3 層九月的月刊只未來日期的價格變更")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>不支援的免費產品

Apple 提供的特殊 Newsstand 應用程式的免費訂用帳戶選項，雖然它不可以針對任何其他應用程式內購買類型設定為零 （免費） 的價格。 雖然您可以編輯 (亦即。 較低) 的促銷價格，您不能透過 iTunes Connect 的 「 免費 」 的應用程式內購買。

### <a name="localization"></a>當地語系化

在 iTunes Connect 中您可以為任何數目的受支援的語言輸入不同的名稱和描述文字。 每一種語言可以新增/編輯中透過快顯視窗：

 [![](in-app-purchase-basics-and-configuration-images/image12.png "每一種語言可以新增/編輯中透過快顯視窗")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 當您在應用程式中顯示產品資訊時，就有一個當地語系化的文字可供您透過 StoreKit 顯示。 也必須以顯示正確的符號和十進位格式 – 此格式本文件稍後涵蓋當地語系化貨幣顯示。

### <a name="app-store-review"></a>應用程式市集審查

相同應用程式 – 每個產品檢閱由 Apple 才允許前往-銷售。 產品不適當內容的名稱或描述，可能會遭到拒絕或 Apple 可能會決定您所選擇的錯誤的產品類型 （例如。 您已建立書籍或雜誌的問題，但使用可取用的產品類型）。 產品檢閱可能需要長達應用程式審查。

第一次應用程式提交應用程式內購買已啟用 （不論它是新的應用程式，或功能已新增至現有） 您也必須選擇要提交與它的某些產品。 在 iTunes Connect 入口網站會提示您這樣做，請在此螢幕擷取畫面所示：

 [![](in-app-purchase-basics-and-configuration-images/image13.png "在 iTunes Connect 入口網站會提示您送出某些產品以及")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 應用程式和應用程式內購買項目將檢閱在一起，使它們全都在 （讓該應用程式不會傳送至存放區沒有任何已核准的產品 ！） 取得一次核准。

已核准您第一次的版本和應用程式內購買功能之後，您可以新增其他的產品，並將它們提交檢閱在任何時間。 您也可以選擇要提交新的版本，以及特定的應用程式內購買的產品，使用**版本的詳細資訊**頁面提示所示。

請參閱[App Store 審核指南](https://developer.apple.com/appstore/guidelines.html)如需詳細資訊。

 [第 2 部分-存放區概觀與擷取產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
