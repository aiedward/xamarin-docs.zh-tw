---
title: 在 Xamarin 中 SiriKit
description: 本文說明如何在 Xamarin iOS 應用程式中使用 SiriKit，以提供可供使用者在 iOS 裝置上使用 Siri 存取的服務。
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: c50d02b4fc806a9ce466da450cfeb6afaa68df93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433698"
---
# <a name="sirikit-in-xamarinios"></a>在 Xamarin 中 SiriKit

_本文說明如何在 Xamarin iOS 應用程式中使用 SiriKit，以提供可供使用者在 iOS 裝置上使用 Siri 存取的服務。_

SiriKit 可讓 iOS 應用程式在 ios 裝置上使用 Siri 和地圖應用程式，使用應用程式擴充功能和新的 **意圖** 和 **意圖 UI** 架構，提供可供使用者存取的服務。

Siri 適用于 **領域**的概念、相關工作的已知動作群組。 應用程式與 Siri 之間的每個互動都必須屬於其中一個已知的服務網域，如下所示：

- 正在呼叫音訊或影片。
- 預約。
- 管理 workouts。
- 消息。
- 正在搜尋相片。
- 傳送或接收款項。

當使用者要求 Siri 牽涉到其中一個應用程式延伸模組的服務時，SiriKit 會傳送一個 **意圖** 物件給延伸模組，該物件會描述使用者的要求以及任何支援的資料。 然後，應用程式延伸模組會為指定的**意圖**產生適當的**回應**物件，詳述延伸模組如何處理要求。

## <a name="understanding-sirikit-concepts"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文涵蓋在 Xamarin iOS 應用程式中使用 SiriKit 時所需的重要概念。 其中涵蓋新的意圖和意圖 UI 擴充點，以及它們如何搭配應用程式和使用者詞彙來開啟要 Siri 的應用程式。

## <a name="implementing-sirikit"></a>[實作 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文涵蓋在 Xamarin iOS 應用程式中執行 SiriKit 支援所需的步驟。 在嘗試將 SiriKit 支援新增至應用程式之前，開發人員應該先閱讀上述的「瞭解 SiriKit 概念指南」，因為涵蓋成功執行所需的重要概念。

## <a name="related-links"></a>相關連結

- [ElizaChat 範例](/samples/xamarin/ios-samples/ios10-elizachat)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [意圖架構參考](https://developer.apple.com/reference/intents)
- [意圖 UI 架構參考](https://developer.apple.com/reference/intentsui)