---
title: IOS 12 簡介
description: 本文件提供的 Xamarin 預覽版本提供 C# 繫結的某些 iOS 12 Api 的高層級描述。
ms.prod: xamarin
ms.assetid: 99EA7090-315D-493C-87D3-26AB73D9E1A9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/08/2018
ms.openlocfilehash: 99f2b98614c2b8d558dd8744b31a62b787fc955c
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233831"
---
# <a name="introduction-to-ios-12"></a>IOS 12 簡介

本文件提供的 Xamarin 預覽版本提供 C# 繫結的某些 iOS 12 Api 的高層級描述。

若要開始建置使用 Xamarin iOS 12 應用程式，請參閱[入門指南](get-started.md)

## <a name="arkit-2arkit2md"></a>[ARKit 2](arkit2.md)

ARKit 是隨附於 iOS 擴增的實境架構。 ARKit 2 可讓多位使用者彼此互動以擴增的實境場景，讓您能夠保存空間中的物件，並在稍後的時間，傳回它們，並提供 2D 影像辨識，以及追蹤和 3D 物件辨識。 iOS 12 也提供 AR 快速尋找，呈現 usdz AR 模型，在您的應用程式的方式。

## <a name="siri-shortcutssiri-shortcutsmd"></a>[Siri 快速鍵](siri-shortcuts.md)

Siri 快速鍵可讓開發人員更深入整合 Siri 他們的應用程式。 使用 Siri 快速鍵，使用者可以使用語音命令來開啟內容或啟動背景工作，或它們可以起始這些相同的工作，透過 Siri 建議在鎖定畫面的捷徑。

## <a name="core-ml-2coremlmd"></a>[Core ML 2](coreml.md)

核心 ML 2 可減少應用程式的大小，透過模型量化和彈性的模型、 改善與新的批次預測 API 的應用程式效能和使用自訂的模型來支援在 machine learning 中的進階功能。

## <a name="notification-improvementsnotificationsindexmd"></a>[通知改良](notifications/index.md)

在 iOS 12 中，群組的通知可讓應用程式或執行緒相關的群組中存在的使用者通知。 摘要的文字會進一步提供通知群組的相關資訊。

在 iOS 12 中的通知內容延伸模組可讓自訂使用者介面和動態動作按鈕。

## <a name="natural-language-frameworknatural-languagemd"></a>[自然語言架構](natural-language.md)

自然語言架構可讓應用程式，以執行各種類型的語言分析。 比方說，它可以識別部分，並判斷由文字區塊的語言。

## <a name="vision-framework"></a>視覺架構

視覺架構包含可以在不同的方向，偵測臉改良的臉部偵測器。 此外，要求修訂可以選取特定的目標 framework 演算法修訂。

## <a name="photo-and-video-apis"></a>相片與影片 Api

在 iOS 12 中，直向分割 API 會傳回直向效果草蓆 – 線性的遮罩，描述前景與背景的直向映像，並可用於建立映像的各種效果。 iOS 12 也可讓它可以用於即時視訊效果的深度 TrueDepth 相機中的資料。

## <a name="passwords"></a>密碼

iOS 12 簡化了使用者和開發人員使用的密碼：

- 密碼自動填入和自動的強式密碼使您能夠自動產生、 儲存和使用強式密碼註冊和登入應用程式時的 iOS 應用程式中。
- 安全性程式碼自動填入可讓您能夠使用 SMS 為基礎的驗證碼，而不需要手動剪下和貼上或記住的需求。
- `ASWebAuthenticationSession`類別簡化使用同盟的驗證服務的程序。
- 自動填滿認證提供者擴充可讓您可提供使用者名稱和密碼來登入欄位的第三方密碼應用程式。

## <a name="healthkit-updates"></a>HealthKit 更新

導入的 iOS 11.3[醫療記錄](https://www.apple.com/healthcare/health-records/)，可讓使用者下載其健全狀況記錄從各種醫療保健機構的資訊，並在其 iOS 裝置上檢視。 iOS 12 加入 Api，可讓協力廠商應用程式，安全地存取此資料。

## <a name="imessage-app-presentation-contexts"></a>iMessage 應用程式的簡報內容

在 iOS 12、 iMessage 應用程式支援簡報內容，允許做為一般 iMessage 應用程式或相片或視訊效果的內容中執行的應用程式。

## <a name="network-framework"></a>網路架構

網路架構的網路堆疊基礎`URLSession`iOS 應用程式中常用的 Api 現推出作為獨立架構，讓您輕鬆地使用 TCP、 UDP、 TLS、 IPv4/IPv6，和更多功能。

## <a name="carplay"></a>CarPlay

在 iOS 12 中，第三方應用程式只要將地圖和開啟所開啟的瀏覽指示 CarPlay 藉由使用新的 CarPlay 架構。

## <a name="deprecations"></a>棄用功能

使用 iOS 12，Apple 已取代：

- OpenGL ES[鼓勵開發人員](https://developer.apple.com/ios/whats-new/)採用裸機。
- [`UIWebView`](xref:UIKit.UIWebView)[的益處`WKWebView` ](https://developer.apple.com/documentation/webkit/wkwebview?language=objc)。

## <a name="related-links"></a>相關連結

- [準備開始使用適用於 iOS (Apple) 12](https://developer.apple.com/ios/)
