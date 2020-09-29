---
title: 在 Xamarin 中 Apple Pay
description: 本指南探討如何設定要與 Apple Pay 搭配使用的 Xamarin iOS 環境，以透過您的應用程式來支付實體貨物，例如食物、娛樂和成員資格。 其中包含必要識別碼、憑證和權利的資訊。
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 51a18042bea820221f450d8d7ceedf3f69359a6d
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431625"
---
# <a name="apple-pay-in-xamarinios"></a>在 Xamarin 中 Apple Pay

_本指南探討如何設定要與 Apple Pay 搭配使用的 Xamarin iOS 環境，以透過您的應用程式來支付實體貨物，例如食物、娛樂和成員資格。其中包含必要識別碼、憑證和權利的資訊。_

Apple Pay 是與 iOS 8 一起導入，可讓使用者透過其 iOS 裝置支付實體貨物，例如食物、娛樂和成員資格。 它適用于 iPhone 6 和 iPhone 6 Plus，也可以與店內購買的 Apple Watch 配對。 在 iPhone 上使用時，它會使用 Touch ID 來確認及授權交易給使用者的信用卡或轉帳卡。

## <a name="requirements"></a>需求

Apple Pay 只能在 iOS 8 和更新版本中使用，因此至少需要 Xcode 6。

下列專案也需要將 Apple Pay 整合到您的應用程式中：

- 付款處理器平臺
- 商家識別碼
- Apple Pay 憑證
- Apple Pay 權利

本檔將更詳細地查看這些專案。

## <a name="differences-between-apple-pay-and-iap"></a>Apple Pay 與 IAP 之間的差異

Apple Pay 和 *應用程式內購買* (IAP) 的主要差異，與他們銷售的產品有關。 *實體* 商品會透過 Apple Pay 銷售;食物、便利設施和實體娛樂 (例如電影票證) 都是這種情況的範例。 相反地，IAP 會銷售 *虛擬* 貨物（例如高階或額外內容）和訂用帳戶–請考慮串流服務的額外月份，或遊戲中的額外生活。

使用的架構也是重要的差異; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) 用於 Apple Pay，而 [StoreKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) 則會提供適用于 IAP 的 framework API。

有了 Apple Pay，Apple 就會 [陳述](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) 它「不會向使用者、商家或開發人員收取使用 Apple Pay 的款項」。 相較之下，IAP 對於每筆交易具有30% 的費用。 此外，有了 Apple Pay，交易完全不會通過 Apple，而是透過付款平臺。

## <a name="using-a-payment-processor-platform"></a>使用付款處理器平臺

Apple Pay 的其中一個基礎部分是付款的處理。 雖然您可以自行進行這項作業，但它需要大量的密碼編譯知識，如 Apple 的 [付款處理指南](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)中所述。
另一方面，付款處理平臺會為您處理這些作業，讓您專注于建立您的應用程式。

有兩個選項包括：

- 等**量：在** [Stripe.com](https://stripe.com/)上註冊以存取其 api。

- **JudoPay** -查看 [Github 上的 Xamarin 範例程式碼](https://github.com/Judopay/Xamarin-Sample-App)，並在 [JudoPay.com](https://www.judopay.com/)註冊。

## <a name="provisioning-for-apple-pay"></a>Apple Pay 的布建

設定應用程式以使用 Apple Pay 需要在 Apple 開發人員入口網站和您的應用程式中安裝。 您必須遵循幾個步驟，才能成功布建您的應用程式以進行 Apple 付款：

1. 建立商家識別碼：
    - 遵循[此處](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid)的步驟
2. 使用 [套用付費] 功能建立應用程式識別碼，並在其中新增商家：
    - 遵循[此處](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid)的步驟
3. 產生商家識別碼的憑證：
    - 遵循[此處](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate)的步驟
4. 使用新建立的應用程式識別碼來產生布建設定檔：
    - 遵循[此處](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning)的步驟
5. 新增 Apple Pay 權利：
    - 依[此處](~/ios/deploy-test/provisioning/entitlements.md)所述選取 Apple 付費權利，或從[這裡](~/ios/deploy-test/provisioning/entitlements.md)手動將索引鍵/值組新增至檔案

## <a name="working-with-apple-pay"></a>使用 Apple Pay

Apple 針對 iOS 10 中的 Apple Pay 進行了數項增強，可讓使用者從網站進行安全的付款，並透過 Siri 和地圖進行互動。

使用 iOS 10，新增了數個新的 Api，可與 iOS 和 watchOS 搭配使用，以支援動態付款網路和新的沙箱測試環境。

### <a name="apple-pay-website-integration"></a>Apple Pay 網站整合

開發人員可以使用 **APPLEPAY JS**，直接在其網站中 Apple Pay 加入 iOS 10 的新手。 在 iOS 或 macOS 中使用 Safari 流覽網站的使用者，可以透過在其 iPhone 或 Apple Watch 上驗證交易的方式來進行 Apple Pay 的付款。 如需詳細資訊，請參閱 Apple 的 [APPLEPAY JP 架構參考](https://developer.apple.com/reference/applepayjs)。

### <a name="passkit-framework-enhancements"></a>PassKit 架構的增強功能

在 iOS 10 中，PassKit 架構已擴充為支援以外的 Apple Pay， `UIKit` 可讓卡片簽發者在其應用程式內呈現自己的卡片。

#### <a name="supporting-apple-pay-outside-of-uikit"></a>支援 UIKit 以外的 Apple Pay

藉由使用 [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) 和 [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，應用程式可以支援 [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) 所提供的相同功能，而不需要使用 UIKit。 雖然需要這個新的 API，才能在 Apple Watch (和特定) 意圖中支援 Apple Pay，但在其他情況下是選擇性的， (例如現有的應用程式) 。 不過，Apple 建議您儘快移至新的 API，以單一程式碼基底，在所有開發人員的應用程式中提供廣泛的 Apple Pay 支援。 如需意圖和 Siri 整合的詳細資訊，請參閱 [SiriKit](~/ios/platform/sirikit/index.md) 檔的簡介。

#### <a name="presenting-issuer-cards-from-within-apps"></a>從應用程式內展示簽發者卡片

使用 iOS 10 時，PassKit 架構已新增新功能，可讓卡片簽發者從自己的應用程式中呈現其卡片。 開發人員可以將 `PKPaymentButtonTypeInStore` UIButton 新增至應用程式的使用者介面，以顯示卡片的 Apple Pay 按鈕。

`PresentPaymentPass` [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)類別的方法也可以用來以程式設計方式顯示該卡片。

### <a name="new-payment-network-support"></a>新的付款網路支援

IOS 10 的新功能，應用程式可以在不需要修改、重新編譯應用程式，並將其重新提交到 App Store 的情況下，自動支援新的付款網路。

類別的新 [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) 方法 `PKPaymentNetwork` 可讓應用程式在執行時間探索使用者裝置上可用的網路。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) 屬性已擴充為採用付款提供者的名稱作為引數。 使用這些方法，應用程式可以自動支援付款提供者支援的任何網路。

如需詳細資訊，請參閱我們的 [Apple Pay](~/ios/platform/apple-pay.md) 設定和 Apple 的 [Apple Pay 指南](https://developer.apple.com/apple-pay/)。

### <a name="new-testing-environment"></a>新的測試環境

使用 iOS 10，Apple 引進了新的測試環境，可讓開發人員直接在 iOS 裝置上布建測試付款卡。 然後，這個新的測試環境會將加密的測試付款資料傳回給應用程式。

若要啟用新的測試環境，請執行下列動作：

1. 在 iTunes Connect 中建立新的測試 iCloud 帳戶。
2. 使用新的測試帳戶登入 iOS 裝置。
3. 設定要測試應用程式的所需區域。
4. 使用 [Apple Pay 指南](https://developer.apple.com/apple-pay/) 中的其中一個測試付款卡進行付款。

> [!IMPORTANT]
> 藉由切換 iCloud 帳戶，裝置會自動切換至新的測試環境。 不過，Apple 仍 **要求** 應用程式在生產環境中以真實卡進行測試，然後再提交至 iTunes App Store。

## <a name="summary"></a>摘要

在本文中，我們探討了在應用程式中使用 Apple Pay 所需的不同專案。 我們探討了如何建立商家識別碼，以及如何在 **plist**中使用它，而這需要手動修改。

## <a name="related-links"></a>相關連結

- [App 內購買](~/ios/platform/in-app-purchasing/index.md)
- [PassKit 簡介](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (範例) ](/samples/xamarin/ios-samples/ios9-emporium)