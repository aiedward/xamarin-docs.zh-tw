---
title: Apple Pay 上 watchOS Xamarin 中
description: 本文件涵蓋的增強功能蘋果對 Apple Pay watchOS 3 以及如何為 Apple Watch 中 Xamarin.iOS 實作它們。
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 75d660ad0699b6fac3b1ae43046f322f380872b3
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791071"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay 上 watchOS Xamarin 中

Apple 已新增支援在應用程式付款的 watchOS 3 中，以進行的 Apple Pay 數個增強功能。 這可讓使用者安全地提供付款，並連絡要直接從 Apple Watch 特別實體的產品和服務的資訊。


## <a name="about-apple-pay-enhancements"></a>Apple Pay 增強功能

為上述 Stated Apple 有數個增強功能中進行的 Apple Pay watchOS 3，允許安全的付款和連絡資訊直接從 Apple Watch 支付實體的產品和服務。 修改 PassKit framework 會提供這些增強功能。

使用 iOS 10 和 watchOS 3，數個新的應用程式開發介面已加入使用 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。

## <a name="passkit-framework-enhancements"></a>PassKit 架構增強功能

在 iOS 10 中，PassKit 架構已擴充，能夠支援 Apple Pay 之外`UIKit`和允許卡片來呈現其卡從自己的應用程式中的簽發者。 

### <a name="supporting-apple-pay-outside-of-uikit"></a>支援 Apple Pay UIKit 之外

使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，應用程式可以支援所提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)不用 UIKit。 雖然這個新的 API 是為了支援 Apple Pay Apple Watch 上 （和特定的對應方式），則可以在其他情況下 （例如，現有的應用程式） 選擇性。 不過，Apple 建議儘速移至新的 API 為 Apple Pay 的廣泛支援，所有開發人員的應用程式提供單一的程式碼基底。 如需有關對應方式和 Siri 整合，請參閱我們[簡介 SiriKit](~/ios/platform/sirikit/index.md)文件。

### <a name="presenting-issuer-cards-from-within-apps"></a>呈現從應用程式中的簽發者卡

與 iOS 10 watchOS 3，已加入新功能允許卡簽發者來呈現自己的應用程式內的 從其付款卡 PassKit framework。 開發人員可以加入`PKPaymentButtonTypeInStore`UIButton 應用程式的使用者介面將會顯示卡的 Apple Pay 按鈕。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)類別也可用來以程式設計方式顯示卡片。

## <a name="new-payment-network-support"></a>新的付款網路支援

新增 iOS 10 和 watchOS 3，應用程式可以自動支援新的付款網路可供使用時不需要修改、 重新編譯應用程式並再重新送出的應用程式市集的開發人員。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法`PKPaymentNetwork`類別可讓應用程式，以在執行階段的使用者的裝置上的可用的網路。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)已經擴充屬性，以取得做為引數的付款提供者的名稱。 使用這些方法，應用程式會自動可以支援任何的付款提供者支援的網路。

如需詳細資訊，請參閱我們[Apple 支付組態](~/ios/platform/apple-pay.md)與 Apple [Apple 支付指南](https://developer.apple.com/apple-pay/)。

## <a name="new-testing-environment"></a>新的測試環境

使用 iOS 10 和 watchOS 3，Apple 引進了新的測試環境，可讓開發人員佈建 iOS 裝置上直接測試付款卡。 這個新的測試環境再傳回加密的測試用戶付款方式資料應用程式。

若要啟用新的測試環境，執行下列作業：

1. 在 iTunes Connect 中建立新的測試 iCloud 帳戶。
2. 登入新的測試帳戶的 iOS 裝置。
3. 設定所要測試應用程式中的區域。
4. 使用其中一種測試的付款卡片，從[Apple 支付指南](https://developer.apple.com/apple-pay/)進行付款。

> [!NOTE]
> 藉由切換 iCloud 帳戶，則裝置將會自動切換到新的測試環境。 不過，Apple 仍**需要**應用程式，以測試與實際卡在生產環境之前提交至 iTunes App Store。

## <a name="summary"></a>總結

這篇文章已涵蓋增強功能蘋果對 Apple Pay watchOS 3 以及如何實作它們 Xamarin.iOS 中。
