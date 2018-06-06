---
title: 在 Xamarin.iOS SiriKit
description: 這篇文章會示範如何使用 SiriKit Xamarin.iOS 應用程式中提供的 iOS 裝置上使用 Siri 使用者可以存取的服務。
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 584b694c83d6c66d6e79e1030b2e682cefb64e6a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788048"
---
# <a name="sirikit-in-xamarinios"></a>在 Xamarin.iOS SiriKit

_這篇文章會示範如何使用 SiriKit Xamarin.iOS 應用程式中提供的 iOS 裝置上使用 Siri 使用者可以存取的服務。_

新增 ios 10 SiriKit 可讓 iOS 應用程式提供給使用者使用應用程式擴充功能和新的 iOS 裝置上使用 Siri 和對應的應用程式存取服務**對應方式**和**目的 UI**架構。

Siri 搭配的概念**網域**，群組的知道相關工作的動作。 每個應用程式具有 Siri 的互動必須歸類到其中一個其已知的服務網域，如下所示：

- 音訊或視訊電話。
- 預約的賽車。
- 管理健身。
- 訊息。
- 搜尋相片。
- 傳送或接收付款。

當使用者提出要求的 Siri 涉及其中一個應用程式擴充功能的服務時，SiriKit 傳送副檔名**意圖**物件，描述使用者的要求，以及任何支援的資料。 應用程式擴充功能會產生適當**回應**物件指定**意圖**，詳細說明如何擴充功能來處理要求。

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[了解 SiriKit 概念](~/ios/platform/sirikit/understanding-sirikit.md)

本文涵蓋將需要使用 SiriKit Xamarin.iOS 應用程式中的重要概念。 它涵蓋了新的對應方式和對應方式 UI 的擴充點，以及它們如何與應用程式和使用者詞彙，以開啟應用程式使用 Siri。

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[實作 SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

本文涵蓋 Xamarin.iOS 應用程式中實作 SiriKit 支援所需的步驟。 開發人員應該閱讀上方的了解 SiriKit 概念指南，然後再嘗試加入應用程式，做為概念涵蓋索引鍵，就會成功實作需要 SiriKit 支援。





## <a name="related-links"></a>相關連結

- [ElizaChat 範例](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [SiriKit 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [對應方式 Framework 參考](https://developer.apple.com/reference/intents)
- [對應 UI Framework 參考](https://developer.apple.com/reference/intentsui)
