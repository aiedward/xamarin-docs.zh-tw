---
title: 在 Xamarin.iOS 中的 Apple Pay
description: 本指南將探索設定 Xamarin.iOS 環境以搭配 Apple Pay 支付實體產品，例如食物、 娛樂與透過您的應用程式的成員資格。 它包含必要的識別項、 憑證和權利的詳細資訊。
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2017
ms.openlocfilehash: b971029ff3b2b1e8f5e63233d1d754c44b0e3309
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110091"
---
# <a name="apple-pay-in-xamarinios"></a>在 Xamarin.iOS 中的 Apple Pay

_本指南將探索設定 Xamarin.iOS 環境以搭配 Apple Pay 支付實體產品，例如食物、 娛樂與透過您的應用程式的成員資格。它包含必要的識別項、 憑證和權利的詳細資訊。_

Apple Pay 引進了與 iOS 8 中，讓使用者支付實體產品，例如食物、 娛樂與透過其 iOS 裝置的成員資格。 它是適用於 iPhone 6 和 iPhone 6 加號，並也可以與在市集購買的 Apple Watch 配對。 在 iPhone 上使用時，它會使用 Touch ID，來確認和授權使用者的信用卡或轉帳卡的交易。

## <a name="requirements"></a>需求

Apple Pay 才可用在 iOS 8 和更新版本，並因此需要 Xcode 6 的最小值。

下列項目也是需要將整合到您的應用程式的 Apple Pay 的：

 - 付款處理器平台
 - 商家識別碼
 - Apple Pay 的憑證
 - Apple Pay 權利

本文將探討更多詳細資料中的項目。

## <a name="differences-between-apple-pay-and-iap"></a>Apple Pay 和 IAP 之間的差異

Apple Pay 的主要差異及*應用程式內購買*(IAP)，屬於銷售的產品。 *實體*商品售出透過 Apple Pay; 食物、 住宿及實體 （例如杜比劇院效果票證） 的娛樂，都是這個範例。 相反地，IAP 銷售*虛擬*商品，例如進階或額外的內容，以及訂用帳戶 – 考慮串流服務，或額外的生活在遊戲中的其他幾個月。

使用的架構也是主要的差異;[PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)是用於 Apple Pay，雖然[StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) IAP 提供的架構 API。

使用 Apple Pay，Apple[狀態](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf)，它 「 [不] 不另行收費使用者、 商家或開發人員若要使用 Apple Pay 的付款 」。 相較之下，IAP 會有 30%費用為每個交易。 此外，使用 Apple Pay，交易不會經過 Apple 根本，相反地，它會經歷付款平台。

## <a name="using-a-payment-processor-platform"></a>使用付款處理器平台

Apple Pay 的基本組件的其中一個是處理付款。 雖然您可以自行完成此作業，它需要相當了解密碼編譯
- 在 Apple 的所述[付款處理指南](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)。
付款處理平台，相反地，會處理您可讓您專注於建置您的應用程式執行這些作業。

兩個選項包括：

- **等量磁碟區**-在註冊[Stripe.com](https://stripe.com/)存取其 Api。

- **JudoPay** -查看他們[github 上的 Xamarin 範例程式碼](https://github.com/Judopay/Xamarin-Sample-App)，並在註冊[JudoPay.com](https://www.judopay.com/)。

## <a name="provisioning-for-apple-pay"></a>Apple Pay 的佈建

設定應用程式以使用 Apple Pay 需要 Apple 開發人員入口網站和應用程式內的安裝程式。 有幾個成功佈建您的應用程式，Apple pay 的應遵循的步驟：

1. 建立商家識別碼：
    - 請依照下列步驟[這裡](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. 建立具有 Apply Pay 功能的應用程式識別碼，並新增商家：
    - 請依照下列步驟[這裡](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. 商家識別碼產生憑證：
    - 請依照下列步驟[這裡](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. 產生的新建立的應用程式識別碼佈建設定檔：
    - 請依照下列步驟[這裡](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. 新增 Apple Pay 權利：
    - 選取 Apple pay 權利詳述[此處](~/ios/deploy-test/provisioning/entitlements.md)，或手動加入的檔案中的索引鍵/值組[這裡](~/ios/deploy-test/provisioning/entitlements.md)

## <a name="working-with-apple-pay"></a>使用 Apple Pay

Apple 有了許多改進功能到 Apple Pay 在 iOS 10 可讓使用者做出安全的付款，從網站，並透過與 Siri 與地圖互動。

使用 iOS 10，數個新的 Api 已新增使用 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。

### <a name="apple-pay-website-integration"></a>Apple Pay 網站整合

新增至 iOS 10，開發人員可以將 Apple Pay 直接納入其網站使用**ApplePay JS**。 瀏覽使用 Safari 的網站，在 iOS 或 macOS 中的使用者可以藉由驗證他們的 iPhone 或 Apple Watch 上的交易進行與 Apple Pay 的付款。 如需詳細資訊，請參閱 Apple [ApplePay JP Framework 參考](https://developer.apple.com/reference/applepayjs)。

### <a name="passkit-framework-enhancements"></a>PassKit 架構增強功能

在 iOS 10 中，「 PassKit 架構已擴充來支援 Apple Pay 之外`UIKit`，並允許呈現自己分享他們的應用程式內的卡片簽發者。


#### <a name="supporting-apple-pay-outside-of-uikit"></a>支援 Apple Pay UIKit 之外

藉由使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)並[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，應用程式可以支援所提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)不用 UIKit。 雖然這個新的 API 是為了支援 Apple Pay Apple Watch 上 （且在特定的對應方式），則可以選擇性在其他情況下 （例如現有的應用程式）。 不過，Apple 建議儘速移至新 API 為 Apple Pay 的廣泛支援，在所有開發人員的應用程式提供單一的程式碼基底。 如需有關對應方式以及 Siri 整合，請參閱我們[SiriKit 簡介](~/ios/platform/sirikit/index.md)文件。

#### <a name="presenting-issuer-cards-from-within-apps"></a>呈現應用程式中的簽發者分享

Ios 10、 PassKit 架構，讓呈現其從自己的應用程式內的卡片的卡片簽發者已新增新功能。 開發人員可以加入`PKPaymentButtonTypeInStore`UIButton，將會顯示卡片的 [Apple Pay] 按鈕的應用程式的使用者介面。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)類別也可用來以程式設計方式顯示卡片。

### <a name="new-payment-network-support"></a>新的付款網路支援

新增至 iOS 10，應用程式可以自動支援新的 「 付款 」 網路可供使用時沒有在開發人員無須修改、 重新編譯應用程式並重新提交至 App Store。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法`PKPaymentNetwork`類別可讓應用程式，以在執行階段的使用者的裝置上可用的網路。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)已經擴充屬性，以做為引數的付款提供者的名稱。 使用這些方法，應用程式會自動可支援的付款提供者支援的任何網路。

如需詳細資訊，請參閱我們[Apple 用多少付多少的組態](~/ios/platform/apple-pay.md)與 Apple [Apple 用多少付多少指南](https://developer.apple.com/apple-pay/)。

### <a name="new-testing-environment"></a>新的測試環境

使用 iOS 10，Apple 已引進新的測試環境，可讓開發人員佈建測試付款卡片，直接在 iOS 裝置上。 然後這個新的測試環境會將加密的測試付款資料回到應用程式中。

若要啟用新的測試環境，執行下列作業：

1. 在 iTunes Connect 中建立新的測試 iCloud 帳戶。
2. 登入新的測試帳戶的 iOS 裝置。
3. 設定測試中的應用程式所需的區域。
4. 使用其中一種從測試付款卡片[Apple 用多少付多少指南](https://developer.apple.com/apple-pay/)進行付款。

> [!IMPORTANT]
> 藉由切換 iCloud 帳戶，則裝置將會自動切換到新的測試環境。 不過，Apple 仍**需要**會測試與實際的應用程式卡在生產環境之前提交到 iTunes App Store。

## <a name="summary"></a>總結

在本文中，我們會探討使用應用程式內的 Apple Pay 所需的不同項目。 我們探討了如何建立 Merchant ID，以及如何使用內**Entitlements.plist**，它必須以手動方式修改。

## <a name="related-links"></a>相關連結

- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [PassKit 簡介](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [雜貨店 （範例）](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
