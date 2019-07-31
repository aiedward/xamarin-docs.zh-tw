---
title: Xamarin 中的應用程式內購買
description: 本檔說明如何使用 StoreKit Api 銷售數位產品和服務。 它會連結到討論設定、取用產品、非可耗用產品、交易、訂閱等等的指南。
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 2320aa9a611a44d654bcbae18bb2664797054e50
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647913"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Xamarin 中的應用程式內購買

iOS 應用程式可以使用 StoreKit 來銷售數位產品或服務– iOS 所提供的一組 Api, 可與 Apple 的伺服器通訊, 透過其 Apple ID 與使用者進行財務交易。 StoreKit Api 主要是與抓取產品資訊並進行交易相關, 而不是使用者介面元件。 執行應用程式內購買的應用程式必須建立自己的使用者介面, 並以自訂程式碼追蹤購買的專案, 為使用者提供必要的產品或服務。

提供應用程式內購買功能需要幾個步驟:

-  設定**應用**程式–應用程式的布建設定檔必須正確設定。
-  **建立產品**–您必須在 iTunes Connect 入口網站中建立產品描述和價格。
-  **執行 StoreKit** – StoreKit API 必須根據所銷售的產品類型來執行。
-  **建立使用者介面和產品本身**–必須實行產品, 包括追蹤每項購買和備份/還原的機制 (如果適用的話)。
-  **監視銷售和接收資金**–使用 iTunes Connect 提供的資訊來監視銷售趨勢並追蹤您的收入。

本檔說明如何完成所有這些步驟, 以使用 Xamarin 來提供應用程式內購買。

## <a name="requirements"></a>需求

若要支援應用程式內購買, 您必須使用包含 Xcode 7 和更新版本的 Xamarin. iOS 5.0 或更新版本。

## <a name="contents"></a>內容

* [App 內購買基本概念和設定](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

* [StoreKit 總覽並獲取產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

* [購買可取用產品](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

* [購買非取用產品](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

* [交易和驗證](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

* [訂用帳戶與報表](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>總結

本文已介紹應用程式內購買的概念, 並概述如何設定您的應用程式, 以利用 Xamarin. iOS 來使用它和呈現的範例。 其中涵蓋:

-  **iOS 布建入口網站**–啟用應用程式內購買功能的指導方針。
-  **ITunes Connect** –設定要在您的應用程式中銷售的產品。
-  **商店套件**–用來建立應用程式內購買功能之類別的說明。
-  **編碼您的應用程式以進行購買**–範例說明如何將應用程式內建購買至 Xamarin iOS 應用程式。
-  **報告**–概述透過 iTunes Connect 提供的統計資料。


## <a name="related-links"></a>相關連結

- [InAppPurchaseSample](https://docs.microsoft.com/en-us/samples/xamarin/ios-samples/storekit/)
- [在應用程式購買程式設計指南中](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect 開發人員指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Store 套件架構參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [應用程式內購買產品識別碼問 &](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [應用程式內購買技術提示](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [您的第一個 App Store 提交](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [App Store 資源中心](https://developer.apple.com/appstore/index.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [管理您的應用程式](https://developer.apple.com/appstore/resources/managing/index.html)
