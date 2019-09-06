---
title: 在 Xamarin 中 SiriKit
description: 本文說明如何在 Xamarin iOS 應用程式中使用 SiriKit，以提供可讓使用者在 iOS 裝置上使用 Siri 來存取的服務。
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: e3ef6dd857760d722fe84f98250b8db9e398ea2e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287176"
---
# <a name="sirikit-in-xamarinios"></a>在 Xamarin 中 SiriKit

_本文說明如何在 Xamarin iOS 應用程式中使用 SiriKit，以提供可讓使用者在 iOS 裝置上使用 Siri 來存取的服務。_

IOS 10 的新功能，SiriKit 可讓 iOS 應用程式使用應用程式延伸模組和新的**意圖**和**意圖 UI**架構，在 Ios 裝置上透過 Siri 和 Maps 應用程式，提供使用者可存取的服務。

Siri 適用于**網域**的概念、相關工作的已知動作群組。 應用程式與 Siri 之間的每個互動都必須屬於其中一個已知的服務網域，如下所示：

- 音訊或影片通話。
- 預約。
- 管理 workouts。
- 關鍵.
- 正在搜尋相片。
- 傳送或接收付款。

當使用者提出包含其中一個應用程式延伸模組服務的 Siri 要求時，SiriKit 會將描述使用者要求的**意圖**物件以及任何支援的資料傳送給此延伸模組。 然後，應用程式延伸模組會針對指定的**意圖**產生適當的**回應**物件，詳述延伸模組如何處理要求。

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文涵蓋在 Xamarin iOS 應用程式中使用 SiriKit 時所需的重要概念。 其中涵蓋新的意圖和意圖 UI 擴充點，以及它們如何使用應用程式和使用者詞彙來開啟要 Siri 的應用程式。

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[實作 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文涵蓋在 Xamarin iOS 應用程式中執行 SiriKit 支援所需的步驟。 開發人員在嘗試將 SiriKit 支援新增至應用程式之前，應該先閱讀瞭解 SiriKit 概念指南，因為已涵蓋成功執行所需的重要概念。





## <a name="related-links"></a>相關連結

- [ElizaChat 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意圖架構參考](https://developer.apple.com/reference/intents)
- [意圖 UI 架構參考](https://developer.apple.com/reference/intentsui)
