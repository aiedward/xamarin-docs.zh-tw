---
title: iOS 12 簡介
description: 本檔提供一些 iOS 12 Api 的高階說明，其中 Xamarin 的預覽版本會提供C#系結。
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/08/2018
ms.openlocfilehash: b3f3db32e87d83ea4e076d439df3342e5ca2ed50
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70284628"
---
# <a name="introduction-to-ios-12"></a>iOS 12 簡介

本檔提供一些 iOS 12 Api 的高階說明，其中 Xamarin 的預覽版本會提供C#系結。

若要開始使用 Xamarin 建立 iOS 12 應用程式，請參閱[快速入門手冊](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit 是 iOS 隨附的增強型現實架構。 ARKit 2 可讓多個使用者在增強的現實場景中彼此互動，讓您可以將物件保存在空間中並于稍後返回，並提供2D 影像辨識和追蹤，以及3D 物件辨識。 iOS 12 也提供 AR 快速外觀，這是在您的應用程式中呈現 usdz AR 模型的方式。

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Siri 快捷方式](siri-shortcuts.md)

Siri 快捷方式可讓開發人員更深入地整合其應用程式與 Siri。 使用 Siri 快捷方式，使用者可以使用語音命令來開啟內容或起始背景工作，也可以透過 Siri 在鎖定畫面上建議的快捷方式來起始這些相同的工作。

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

核心 ML 2 透過模型量化和彈性模型來減少應用程式大小、利用新的批次預測 API 改善應用程式效能，並使用自訂模型來支援機器學習服務的進展。

## <a name="notification-improvementsnotificationsindexmd"></a>[通知改良功能](notifications/index.md)

在 iOS 12 中，分組的通知可讓您在應用程式或執行緒相關的群組中顯示使用者通知。 摘要文字提供有關通知群組的進一步資訊。

IOS 12 中的通知內容延伸允許自訂使用者介面和動態動作按鈕。

## <a name="natural-language-frameworknatural-languagemd"></a>[自然語言架構](natural-language.md)

自然語言架構可讓應用程式執行各種類型的語言分析。 例如，它可以識別語音的各個部分，並判斷以文字塊表示的語言。

## <a name="vision-frameworkiosplatformintroduction-to-ios11visionmd"></a>[願景架構](~/ios/platform/introduction-to-ios11/vision.md)

視覺架構包含改良的臉部偵測器，可偵測各種方向的臉部。 此外，要求修訂也可以選取特定的視覺架構演算法修訂。

## <a name="photo-and-video-apis"></a>相片和影片 Api

在 iOS 12 中，直向分割 API 會傳回直向效果遮罩–從直向影像背景描寫前景的線性遮罩，適用于建立各種影像效果。 iOS 12 也可以使用 TrueDepth 攝影機的深度資料來進行即時影片效果。

## <a name="passwords"></a>密碼

iOS 12 可讓使用者和開發人員更輕鬆地使用密碼：

- 密碼自動填入和自動化強式密碼可讓您在註冊並登入應用程式時，自動產生、儲存和使用 iOS 應用程式中的強式密碼。
- 安全性程式碼自動填入可讓您使用以 SMS 為基礎的驗證碼，而不需要手動剪切和貼上或記住需求。
- `ASWebAuthenticationSession`類別會簡化使用同盟驗證服務的程式。
- 自動填滿認證提供者擴充功能可讓協力廠商密碼應用程式提供使用者名稱和密碼給登入欄位。

## <a name="healthkit-updates"></a>HealthKit 更新

iOS 11.3 引進了[健康情況記錄](https://www.apple.com/healthcare/health-records/)，可讓使用者從各種醫療保健機構下載其健康情況記錄資訊，並在其 iOS 裝置上觀看。 iOS 12 新增了可讓協力廠商應用程式安全地存取此資料的 Api。

## <a name="imessage-app-presentation-contexts"></a>iMessage 應用程式展示內容

在 iOS 12 中，iMessage apps 支援呈現內容，可讓應用程式以一般 iMessage 應用程式或在相片或影片效果的內容中執行。

## <a name="network-framework"></a>網路架構

網路架構是 iOS 應用程式中常用`URLSession`之 api 的基礎，現在以獨立架構的形式提供，可讓您更輕鬆地使用 TCP、UDP、TLS、IPv4/IPv6 等。

## <a name="carplay"></a>了 carplay

在 iOS 12 中，協力廠商應用程式可以使用新的了 carplay 架構，在了 carplay 中傳遞地圖和輪流導覽指示。

## <a name="deprecations"></a>棄用功能

在 iOS 12 中，Apple 已淘汰：

- OpenGL ES，[鼓勵開發人員](https://developer.apple.com/ios/whats-new/)採用裸機。
- [`UIWebView`](xref:UIKit.UIWebView)，[改為`WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。
