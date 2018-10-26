---
title: 在 Xamarin.iOS 中 SiriKit
description: 本文說明如何在 Xamarin.iOS 應用程式中使用 SiriKit，提供 iOS 裝置上使用 Siri 使用者可以存取的服務。
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f7cbb3f7d9e448947ec8163a8660616910e750f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117521"
---
# <a name="sirikit-in-xamarinios"></a>在 Xamarin.iOS 中 SiriKit

_本文說明如何在 Xamarin.iOS 應用程式中使用 SiriKit，提供 iOS 裝置上使用 Siri 使用者可以存取的服務。_

新增至 iOS 10，SiriKit 可讓 iOS 應用程式提供使用應用程式擴充功能和新的 iOS 裝置上使用 Siri 和對應的應用程式的使用者可以存取的服務**意圖**並**Intents UI**架構。

使用 Siri 的運作方式的概念**網域**，群組知道相關工作的動作。 每個互動，應用程式使用 Siri 必須分為其已知的服務網域，如下所示：

- 音訊或視訊通話。
- 預約好好體驗吧。
- 管理健身。
- 訊息傳遞。
- 正在搜尋相片。
- 傳送或接收付款。

當使用者提出的 Siri 要求牽涉到的其中一個應用程式擴充功能的服務時，SiriKit 傳送延伸模組**意圖**物件，描述使用者的要求，以及任何支援的資料。 應用程式擴充功能則會產生適當**回應**物件的給定**意圖**，詳細說明如何擴充功能來處理要求。

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文涵蓋在 Xamarin.iOS 應用程式中使用的 SiriKit 需要的重要概念。 它涵蓋了新的意圖和 Intents UI 延伸模組點和如何使用應用程式和使用者的詞彙，以開啟應用程式以使用 Siri。

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[實作 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文涵蓋在 Xamarin.iOS 應用程式中實作 SiriKit 支援所需的步驟。 開發人員應該閱讀上述的了解 SiriKit 概念指南，然後再嘗試新增應用程式，做為概念都涵蓋索引鍵，就必須成功實作 SiriKit 支援。





## <a name="related-links"></a>相關連結

- [ElizaChat 範例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Intents Framework 參考](https://developer.apple.com/reference/intents)
- [Intents UI 架構參考](https://developer.apple.com/reference/intentsui)
