---
title: App 內購買
description: 數位產品和服務使用存放區套件應用程式開發介面，可以販售 iOS 應用程式。 建立和管理在 iTunes Connect 入口網站中的產品。 Apple 管理交易處理，並核准所有產品之前它們可以銷售，以及對 （目前 30%) 的每個交易的費用。 Apple 要求您使用應用程式中的任何數位銷售應用程式內購買，但無法用於實體的貨品或非數位服務的銷售。 可能會遭到拒絕時，會提供替代的付款選項數位產品和服務的應用程式。 本文件說明如何設定應用程式使用市集套件，並提供最常見的應用程式內購買案例的 Xamarin.iOS 範例。
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 7a8dec6051caeba55c45df29c085ecfcddd160d2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="in-app-purchasing"></a>App 內購買

_數位產品和服務使用存放區套件應用程式開發介面，可以販售 iOS 應用程式。建立和管理在 iTunes Connect 入口網站中的產品。Apple 管理交易處理，並核准所有產品之前它們可以銷售，以及對 （目前 30%) 的每個交易的費用。Apple 要求您使用應用程式中的任何數位銷售應用程式內購買，但無法用於實體的貨品或非數位服務的銷售。可能會遭到拒絕時，會提供替代的付款選項數位產品和服務的應用程式。本文件說明如何設定應用程式使用市集套件，並提供最常見的應用程式內購買案例的 Xamarin.iOS 範例。_


iOS 應用程式可以銷售數位產品或服務使用 StoreKit – 一組 Api 提供與 Apple 伺服器進行通訊的 iOS 進行財務交易與使用者透過其 Apple id。 StoreKit Api 關心主要是進行交易，擷取產品資訊-沒有使用者介面元件。 實作應用程式內購買的應用程式必須建置自己的使用者介面，並追蹤所購買的項目自訂程式碼，以提供必要的產品或服務給使用者。

提供在應用程式內購買的功能需要幾個步驟：

-  **設定您的應用程式**– 應用程式的佈建設定檔必須正確設定。
-  **建立產品**– 在 iTunes Connect 入口網站，則必須建立產品描述和價格。
-  **實作 StoreKit** – 依據正在銷售產品的類型必須實作 StoreKit API。
-  **建立使用者介面，以及產品本身**– 產品必須實作，包括追蹤每項採購和備份/還原它們適當的機制。
-  **監視 sales 和接收資金**– 使用 iTunes Connect 所提供的資訊來監控的銷售趨勢、 追蹤您的收益。


本文件說明如何完成提供應用程式內購買使用 Xamarin.iOS 所有這些步驟。


## <a name="requirements"></a>需求

若要支援應用程式內購買中，您必須使用 Xamarin.iOS 5.0 或更新版本 Xcode 7 和更新版本。

## <a name="contents"></a>內容

 * [App 內購買基本概念和設定](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

 * [StoreKit 概觀與擷取產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

 * [購買可取用產品](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

 * [購買非取用產品](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

 * [交易和驗證](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

 * [訂用帳戶與報表](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)


## <a name="summary"></a>總結

這篇文章已導入的應用程式內購買的概念、 概述如何設定您的應用程式利用.net framework 和呈現使用 Xamarin.iOS 範例。 它已涵蓋：

-  **iOS 佈建入口網站**– 指導方針啟用應用程式內購買的功能。
-  **iTunes Connect** -設定要在您的應用程式 」 銷售的產品。
-  **儲存套件**– 說明用來建置應用程式內購買功能的類別。
-  **您購買的應用程式程式碼撰寫**– 如何建置應用程式內購買到 Xamarin.iOS 應用程式的範例。
-  **報告**– 透過 iTunes Connect 中可用的統計資料的概觀。


## <a name="related-links"></a>相關連結

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [在應用程式內購買程式設計指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect 開發人員指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [存放區套件 Framework 參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [問與答的應用程式內購買的產品識別碼](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [在應用程式內購買技術提示](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [第一個應用程式市集提交](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [應用程式存放區資源中心](https://developer.apple.com/appstore/index.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [管理您的應用程式](https://developer.apple.com/appstore/resources/managing/index.html)
