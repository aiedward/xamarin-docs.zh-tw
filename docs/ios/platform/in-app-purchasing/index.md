---
title: 應用程式內購買在 Xamarin.iOS 中
description: 本文件說明如何成功銷售數位產品和服務使用 StoreKit Api。 它會連結到討論組態、 可取用產品、 非取用產品、 交易、 訂用帳戶，以及更多的指南。
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: b4165f9e6c3088a41d6bd746b1912a64570c553a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832561"
---
# <a name="in-app-purchasing-in-xamarinios"></a>應用程式內購買在 Xamarin.iOS 中

iOS 應用程式可以銷售數位產品或服務使用 StoreKit – 一組以進行使用者透過其 Apple id。 與財務交易 Apple 伺服器進行通訊的 iOS 所提供的 Api StoreKit Api 會擷取產品資訊和進行交易與 – 沒有任何使用者介面元件。 實作應用程式內購買的應用程式必須建置自己的使用者介面，並追蹤自訂程式碼，以提供必要的產品或服務給使用者的購買項目。

提供應用程式內購買功能，需要幾個步驟：

-  **設定您的應用程式**– 應用程式的佈建設定檔必須設定正確。
-  **建立產品**– 在 iTunes Connect 入口網站中，則必須建立產品描述和價格。
-  **實作 StoreKit** – StoreKit API 必須根據銷售的產品類型實作。
-  **建置使用者介面及本身之產品**– 產品必須實作，包括追蹤每一次購買和備份/還原它們適當的機制。
-  **監視銷售和接收資金**– 使用 iTunes Connect 所提供的資訊來監視銷售趨勢，並追蹤您的收益。

本文件說明如何完成上述所有步驟，提供應用程式內購買使用 Xamarin.iOS。

## <a name="requirements"></a>需求

若要支援應用程式內購買中，您必須使用 Xamarin.iOS 5.0 或更新版本與 Xcode 7 和更新版本。

## <a name="contents"></a>內容

* [App 內購買基本概念和設定](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

* [StoreKit 概觀與擷取產品資訊](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

* [購買可取用產品](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

* [購買非取用產品](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

* [交易和驗證](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

* [訂用帳戶與報表](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>總結

這篇文章已導入的應用程式內購買的概念，說明如何設定您的應用程式，以利用它，並使用 Xamarin.iOS 範例。 它涵蓋：

-  **iOS 佈建入口網站**– 指導方針，以啟用應用程式內購買的功能。
-  **iTunes Connect** -設定要在您的應用程式內銷售的產品。
-  **儲存套件**– 說明用來建置應用程式內購買功能的類別。
-  **您購買的應用程式程式碼撰寫**– 範例，示範如何在 Xamarin.iOS 應用程式中建置應用程式內購買。
-  **報告**– 可透過 iTunes Connect 的統計資料的概觀。


## <a name="related-links"></a>相關連結

- [InAppPurchaseSample](https://developer.xamarin.com/samples/StoreKit/)
- [在應用程式內購買程式設計指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [iTunes Connect 開發人員指南](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [儲存套件的 Framework 參考](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [問與答的應用程式內購買的產品識別碼](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [在應用程式內購買的技術提示](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [您的第一個應用程式市集提交](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [應用程式市集資源中心](https://developer.apple.com/appstore/index.html)
- [App Store 提交提示](https://developer.apple.com/appstore/resources/submission/tips.html)
- [App Store 審查指導方針](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [管理您的應用程式](https://developer.apple.com/appstore/resources/managing/index.html)
