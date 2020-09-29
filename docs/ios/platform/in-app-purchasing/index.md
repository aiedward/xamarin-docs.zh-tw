---
title: Xamarin 中的應用程式內購買
description: 本檔說明如何使用 StoreKit Api 銷售數位產品和服務。 它會連結到討論設定、取用產品、非取用產品、交易、訂用帳戶等的指南。
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 7e061f12e45e6d60c1772fc8699ffdb1f1b1d730
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91430396"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Xamarin 中的應用程式內購買

iOS 應用程式可以使用 StoreKit （iOS 所提供的一組 Api）來銷售數位產品或服務，這組 Api 會與 Apple 的伺服器通訊，以透過其 Apple ID 與使用者進行財務交易。 StoreKit Api 主要是要處理產品資訊和執行交易，而不是使用者介面元件。 執行應用程式內購買的應用程式必須建立自己的使用者介面，並使用自訂程式碼來追蹤購買的專案，以提供所需的產品或服務給使用者。

提供應用程式內購買功能需要多個步驟：

- 設定**您的應用**程式–應用程式的布建設定檔必須正確設定。
- 您必須在 iTunes Connect 入口網站中**建立產品說明**和價格。
- **執行 StoreKit** – StoreKit API 必須根據銷售的產品類型來實行。
- **建立使用者介面和產品本身** –必須實行這些產品，包括追蹤每個購買的機制，以及在適當的情況下進行備份/還原。
- **監視銷售和接收資金** –使用 iTunes Connect 提供的資訊來監視銷售趨勢，並追蹤您的收入。

本檔說明如何完成上述所有步驟，以使用 Xamarin 提供應用程式內購買。

## <a name="requirements"></a>需求

若要支援應用程式內購買，您必須使用適用于 Xcode 7 和更新版本的 Xamarin. iOS 5.0 或更新版本。

## <a name="contents"></a>目錄

- [App 內購買基本概念和設定](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

- [StoreKit 總覽和取出產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

- [購買消耗性產品](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

- [購買非消耗性產品](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

- [交易和驗證](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

- [訂閱與報表](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>摘要

本文介紹了應用程式內購買的概念，並概述如何設定您的應用程式以利用它來利用它，並使用 Xamarin 呈現範例。 其中涵蓋：

- **iOS 布建入口網站** -啟用應用程式內購買功能的指導方針。
- **ITunes Connect** –設定要在您的應用程式中銷售的產品。
- **Store 套件** –用來建立應用程式內購買功能之類別的說明。
- **為您的應用程式撰寫程式碼，** 範例說明如何將應用程式內購買組建到 Xamarin ios 應用程式。
- **報告** -概要說明可透過 iTunes Connect 取得的統計資料。

## <a name="related-links"></a>相關連結

- [InAppPurchaseSample](/samples/xamarin/ios-samples/storekit/)
- [在應用程式採購程式設計指南中](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect 開發人員指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [商店套件架構參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [應用程式內購買產品識別碼 Q&A](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [應用程式內購買技術注意事項](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [您的第一個 App Store 提交](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [管理您的應用程式](https://developer.apple.com/appstore/resources/managing/index.html)