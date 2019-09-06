---
title: 在 Xamarin 的 watchOS 上 Apple Pay
description: 本文涵蓋 Apple 在 watchOS 3 中 Apple Pay 的增強功能，以及如何在適用于 Apple Watch 的 Xamarin 中執行。
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 7180ccd03429e3a23633744bae35f35b4d9a6678
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292223"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>在 Xamarin 的 watchOS 上 Apple Pay

Apple 已對 watchOS 3 中的 Apple Pay 進行了幾項增強，以增加應用程式內付款的支援。 這可讓使用者安全地提供付款和連絡人資訊，直接從 Apple Watch 支付實體貨物和服務的費用。


## <a name="about-apple-pay-enhancements"></a>關於 Apple Pay 增強功能

如上所述，Apple 已對 watchOS 3 中的 Apple Pay 進行了幾項增強，可讓安全的付款和連絡人資訊直接從 Apple Watch 支付實體貨物和服務的費用。 這些增強功能是由 PassKit 架構的修改所提供。

在 iOS 10 和 watchOS 3 中，已新增數個可與 iOS 和 watchOS 搭配使用的新 Api，以支援動態付款網路和新的沙箱測試環境。

## <a name="passkit-framework-enhancements"></a>PassKit Framework 增強功能

在 iOS 10 中，PassKit 架構已擴充為支援以外的`UIKit` Apple Pay，讓卡片簽發者可以從其應用程式中呈現其卡片。 

### <a name="supporting-apple-pay-outside-of-uikit"></a>支援 UIKit 以外的 Apple Pay

藉由使用[PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller)和[PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate)，應用程式可以支援[PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller)所提供的相同功能，而不需使用 UIKit。 雖然這個新的 API 是支援 Apple Watch 上的 Apple Pay （也是在特定的意圖），但在其他情況（例如現有的應用程式）中是選擇性的。 不過，Apple 建議您儘快移至新的 API，以透過單一程式碼基底，在所有開發人員的應用程式中提供廣泛的 Apple Pay 支援。 如需意圖和 Siri 整合的詳細資訊，請參閱我們[的 SiriKit 簡介](~/ios/platform/sirikit/index.md)檔。

### <a name="presenting-issuer-cards-from-within-apps"></a>從應用程式中呈現簽發者卡片

在 iOS 10 和 watchOS 3 中，新功能已新增至 PassKit 架構，讓卡片簽發者可以從自己的應用程式中呈現其付款卡。 開發人員可以將`PKPaymentButtonTypeInStore` UIButton 新增至應用程式的使用者介面，以顯示卡片的 Apple Pay 按鈕。

[PKPassLibrary 類別](https://developer.apple.com/reference/passkit/pkpasslibrary)的方法也可以用來以程式設計方式顯示`PresentPaymentPass`卡片。

## <a name="new-payment-network-support"></a>新的付款網路支援

IOS 10 和 watchOS 3 的新手，應用程式可以在沒有開發人員需要修改、重新編譯應用程式並重新提交至 App Store 時，自動支援新的付款網路。

`PKPaymentNetwork`類別的新[AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks)方法可讓應用程式在執行時間探索使用者裝置上可用的網路。 此外， [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks)屬性已擴充為接受付款提供者的名稱做為引數。 使用這些方法，應用程式可以自動支援付款提供者支援的任何網路。

如需詳細資訊，請參閱我們的[Apple Pay](~/ios/platform/apple-pay.md)設定和 Apple 的[Apple Pay 指南](https://developer.apple.com/apple-pay/)。

## <a name="new-testing-environment"></a>新的測試環境

在 iOS 10 和 watchOS 3 中，Apple 引進了新的測試環境，可讓開發人員直接在 iOS 裝置上布建測試付款卡。 這個新的測試環境接著會將加密的測試付款資料傳回給應用程式。

若要啟用新的測試環境，請執行下列動作：

1. 在 iTunes Connect 中建立新的測試 iCloud 帳戶。
2. 使用新的測試帳戶登入 iOS 裝置。
3. 設定要在其中測試應用程式的所需區域。
4. 使用[Apple Pay 指南](https://developer.apple.com/apple-pay/)中的其中一個測試付款卡來進行付款。

> [!NOTE]
> 藉由切換 iCloud 帳戶，裝置會自動切換到新的測試環境。 不過，在提交至 iTunes App Store 之前，Apple 仍然**需要**在生產環境中使用實際卡片來測試應用程式。

## <a name="summary"></a>總結

本文涵蓋了 Apple 對 watchOS 3 中的 Apple Pay 的增強功能，以及如何在 Xamarin 中執行。
