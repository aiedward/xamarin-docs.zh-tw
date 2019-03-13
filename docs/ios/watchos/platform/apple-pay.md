---
title: Apple Pay 在 watchOS 中 Xamarin
description: 本文章涵蓋的增強功能 Apple 對 Apple Pay watchOS 3 以及如何在 Xamarin.iOS 中的 Apple Watch 實作它們。
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 354e03ee1e07ba99fcdeb05617bc65ed89f0e8c2
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103084"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay 在 watchOS 中 Xamarin

Apple 有了許多改進功能到 Apple Pay 在 watchOS 3，以新增應用程式內的付款的支援。 這可讓使用者安全地提供付款和連絡資訊，直接從 Apple Watch 支付實體產品和服務。


## <a name="about-apple-pay-enhancements"></a>Apple Pay 的增強功能

為上述 Stated，Apple 已對數個增強功能 Apple Pay watchOS 3 可供安全的付款，以及連絡資訊，直接從 Apple Watch 支付實體產品和服務中。 修改 「 PassKit 架構會提供這些增強功能。

使用 iOS 10 和 watchOS 3，數個新的 Api 已新增使用 iOS 和 watchOS 支援動態付款網路與新的沙箱測試環境。

## <a name="passkit-framework-enhancements"></a>PassKit 架構增強功能

在 iOS 10 中，「 PassKit 架構已擴充來支援 Apple Pay 之外`UIKit`，並允許以呈現來自其卡片，他們的應用程式內的卡片簽發者。 

### <a name="supporting-apple-pay-outside-of-uikit"></a>支援 Apple Pay UIKit 之外

藉由使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)並[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，應用程式可以支援所提供的相同功能[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)不用 UIKit。 雖然這個新的 API 是為了支援 Apple Pay Apple Watch 上 （且在特定的對應方式），則可以選擇性在其他情況下 （例如現有的應用程式）。 不過，Apple 建議儘速移至新 API 為 Apple Pay 的廣泛支援，在所有開發人員的應用程式提供單一的程式碼基底。 如需有關對應方式以及 Siri 整合，請參閱我們[SiriKit 簡介](~/ios/platform/sirikit/index.md)文件。

### <a name="presenting-issuer-cards-from-within-apps"></a>呈現應用程式中的簽發者分享

使用 iOS 10 和 watchOS 3，已新增新功能給 PassKit 架構可呈現他們自己的應用程式內的 從其付款卡片的卡片簽發者。 開發人員可以加入`PKPaymentButtonTypeInStore`UIButton，將會顯示卡片的 [Apple Pay] 按鈕的應用程式的使用者介面。

`PresentPaymentPass`方法[PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary)類別也可用來以程式設計方式顯示卡片。

## <a name="new-payment-network-support"></a>新的付款網路支援

新增 iOS 10 和 watchOS 3，應用程式可以自動支援新的 「 付款 」 網路可供使用時沒有在開發人員無須修改、 重新編譯應用程式並重新提交至 App Store。

新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法`PKPaymentNetwork`類別可讓應用程式，以在執行階段的使用者的裝置上可用的網路。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)已經擴充屬性，以做為引數的付款提供者的名稱。 使用這些方法，應用程式會自動可支援的付款提供者支援的任何網路。

如需詳細資訊，請參閱我們[Apple 用多少付多少的組態](~/ios/platform/apple-pay.md)與 Apple [Apple 用多少付多少指南](https://developer.apple.com/apple-pay/)。

## <a name="new-testing-environment"></a>新的測試環境

使用 iOS 10 和 watchOS 3，Apple 已引進新的測試環境，可讓開發人員佈建測試付款卡片，直接在 iOS 裝置上。 然後這個新的測試環境會將加密的測試付款資料回到應用程式中。

若要啟用新的測試環境，執行下列作業：

1. 在 iTunes Connect 中建立新的測試 iCloud 帳戶。
2. 登入新的測試帳戶的 iOS 裝置。
3. 設定測試中的應用程式所需的區域。
4. 使用其中一種從測試付款卡片[Apple 用多少付多少指南](https://developer.apple.com/apple-pay/)進行付款。

> [!NOTE]
> 藉由切換 iCloud 帳戶，則裝置將會自動切換到新的測試環境。 不過，Apple 仍**需要**會測試與實際的應用程式卡在生產環境之前提交到 iTunes App Store。

## <a name="summary"></a>總結

這篇文章已涵蓋的增強功能 Apple 對 Apple Pay watchOS 3 以及如何在 Xamarin.iOS 中實作它們。
