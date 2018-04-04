---
title: Apple Pay
description: 本指南中，瀏覽設定 Xamarin.iOS 環境搭配 Apple Pay 支付實體的貨物，例如食物、 娛樂與透過您的應用程式的成員資格。 它包含必要的識別項，憑證與權利的詳細資訊。
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: fc7c247e5edcdc25d53c34c922801a5497b8c367
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="apple-pay"></a>Apple Pay

_本指南中，瀏覽設定 Xamarin.iOS 環境搭配 Apple Pay 支付實體的貨物，例如食物、 娛樂與透過您的應用程式的成員資格。它包含必要的識別項，憑證與權利的詳細資訊。_


Apple Pay 引進了與 iOS 8，讓使用者支付實體的貨物，例如食物、 娛樂及透過其 iOS 裝置的成員資格。 它是適用於 iPhone 6 和 iPhone 6 加號，及也能與 Apple Watch 存放區中購買的配對。 在 iPhone 上使用時，它會使用 Touch ID 的方式來確認和授權使用者的信用卡或扣款卡交易。


## <a name="requirements"></a>需求

Apple Pay 只有使用中 iOS 8 和更高版本，且因此需要 Xcode 6 的最小值。

下列項目也是整合到您的應用程式的 Apple Pay 必要的：

 - 付款處理器平台
 - 零售商的識別項
 - Apple Pay 的憑證
 - Apple Pay 權利

本文將探討更多詳細資料中的項目。

## <a name="differences-between-apple-pay-and-iap"></a>Apple Pay 和 iap 營收差異

Apple Pay 的主要差異和*應用程式內購買*（iap 營收） 都屬於與其銷售的產品。 *實體*貨物在銷售透過 Apple Pay; 食物、 住宿及實體娛樂 （例如劇院票證） 都是這個範例。 相反地，iap 營收銷售*虛擬*貨物，例如 premium 或額外的內容，以及訂閱 – 考慮串流服務，或在遊戲中的額外生活的其他幾個月。

使用架構也是主要的差異。[PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)是用於 Apple Pay，雖然[StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)提供應用程式開發介面的架構，iap 營收。

使用 Apple Pay，Apple[狀態](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf)[，它"不] 會收費使用者、 商家或開發人員若要使用 Apple Pay 付款 」。 相較之下，iap 營收會有 30%的電量，每一筆交易。 此外，與 Apple Pay，交易不會進出 Apple 根本，相反地，它將經歷付款平台。


## <a name="using-a-payment-processor-platform"></a>使用付款處理器平台

處理付款的其中一個 Apple Pay 的基本部分。 雖然可以自行，需要密碼編譯的重要的資訊
- Apple 中所詳述[處理付款指南](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)。
相反地，付款處理平台，會處理這些作業的您，讓您專注於建置應用程式。

包含兩個選項：

- **等量磁碟區**-註冊[Stripe.com](https://stripe.com/)存取其應用程式開發介面。

- **JudoPay** -簽出其[github 上的 Xamarin 範例程式碼](https://github.com/Judopay/Xamarin-Sample-App)，並在註冊[JudoPay.com](https://www.judopay.com/)。


## <a name="provisioning-for-apple-pay"></a>Apple Pay 佈建

設定應用程式使用 Apple Pay 需要 Apple 開發人員入口網站和應用程式中的設定。 有幾個成功佈建您的 Apple pay 的應用程式，應遵循的步驟：

1. 建立商家識別碼：
    - 請依照[這裡](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)
2. 建立應用程式識別碼之適用於付費功能，並加入商店：
    - 請依照[這裡](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)
3. 商店識別碼以產生憑證：
    - 請依照[這裡](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)
4. 產生新建立的應用程式識別碼與佈建設定檔：
    - 請依照[這裡](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)
5. 新增 Apple Pay 權利：
    - 選取所述的 Apple pay 權利[這裡](~/ios/deploy-test/provisioning/entitlements.md)，或手動從檔案新增的索引鍵/值組[這裡](~/ios/deploy-test/provisioning/entitlements.md)


## <a name="working-with-apple-pay"></a>使用 Apple Pay

Apple 已中 iOS 10 可讓使用者從網站，並透過 Siri 與地圖互動的安全付款，以進行的 Apple Pay 數個增強功能。

使用 iOS 10，數個新的應用程式開發介面已加入使用 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。


### <a name="apple-pay-website-integration"></a>Apple Pay 網站整合

新增 ios 10，開發人員可以將 Apple Pay 直接合併其網站使用**ApplePay JS**。 使用者瀏覽與 Safari 中 iOS 或 macOS 的網站可以讓付款與 Apple Pay 驗證其 iPhone 或 Apple Watch 上的交易。 如需詳細資訊，請參閱 Apple [ApplePay JP Framework 參考](https://developer.apple.com/reference/applepayjs)。

### <a name="passkit-framework-enhancements"></a>PassKit 架構增強功能

在 iOS 10 中，PassKit 架構已擴充，能夠支援 Apple Pay 之外`UIKit`和允許呈現自己的應用程式內自己張卡片的卡片簽發者。


#### <a name="supporting-apple-pay-outside-of-uikit"></a>支援 Apple Pay UIKit 之外

使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，應用程式可以支援所提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)不用 UIKit。 雖然這個新的 API 是為了支援 Apple Pay Apple Watch 上 （和特定的對應方式），則可以在其他情況下 （例如，現有的應用程式） 選擇性。 不過，Apple 建議儘速移至新的 API 為 Apple Pay 的廣泛支援，所有開發人員的應用程式提供單一的程式碼基底。 如需有關對應方式和 Siri 整合，請參閱我們[簡介 SiriKit](~/ios/platform/sirikit/index.md)文件。

#### <a name="presenting-issuer-cards-from-within-apps"></a>呈現從應用程式中的簽發者卡

使用 iOS 10，已經 PassKit 架構可讓卡簽發者來呈現其卡從自己的應用程式內加入新功能。 開發人員可以加入`PKPaymentButtonTypeInStore`UIButton 應用程式的使用者介面將會顯示卡的 Apple Pay 按鈕。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)類別也可用來以程式設計方式顯示卡片。

### <a name="new-payment-network-support"></a>新的付款網路支援

新增 ios 10，應用程式可以自動支援新的付款網路可供使用時不需要修改、 重新編譯應用程式並再重新送出的應用程式市集的開發人員。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法`PKPaymentNetwork`類別可讓應用程式，以在執行階段的使用者的裝置上的可用的網路。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)已經擴充屬性，以取得做為引數的付款提供者的名稱。 使用這些方法，應用程式會自動可以支援任何的付款提供者支援的網路。

如需詳細資訊，請參閱我們[Apple 支付組態](~/ios/platform/apple-pay.md)與 Apple [Apple 支付指南](https://developer.apple.com/apple-pay/)。

### <a name="new-testing-environment"></a>新的測試環境

使用 iOS 10，Apple 引進了新的測試環境，可讓開發人員佈建 iOS 裝置上直接測試付款卡。 這個新的測試環境再傳回加密的測試用戶付款方式資料應用程式。

若要啟用新的測試環境，執行下列作業：

1. 在 iTunes Connect 中建立新的測試 iCloud 帳戶。
2. 登入新的測試帳戶的 iOS 裝置。
3. 設定所要測試應用程式中的區域。
4. 使用其中一種測試的付款卡片，從[Apple 支付指南](https://developer.apple.com/apple-pay/)進行付款。

> [!IMPORTANT]
> 藉由切換 iCloud 帳戶，則裝置將會自動切換到新的測試環境。 不過，Apple 仍**需要**應用程式，以測試與實際卡在生產環境之前提交至 iTunes App Store。

## <a name="summary"></a>總結

在本文中，我們可以探索內所要使用 Apple Pay 您的應用程式所需的不同項目。 我們探討了如何建立一個商店識別碼，以及如何用在**Entitlements.plist**，而且需要以手動方式修改。


## <a name="related-links"></a>相關連結

- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [PassKit 簡介](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [雜貨店 （範例）](https://developer.xamarin.com/samples/monotouch/ios9/Emporium/)
