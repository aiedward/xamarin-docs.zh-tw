---
title: tvOS 10 簡介
description: 本文介紹適用于 Xamarin. tvOS 開發人員的 tvOS 10 中所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 5d59646f111e5ac3f50de7319f8d5065492c9942
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434904"
---
# <a name="introduction-to-tvos-10"></a>tvOS 10 簡介

_本文介紹適用于 Xamarin. tvOS 開發人員的 tvOS 10 中所有新的和修改過的 Api 和功能。_

有了新的 tvOS 10 SDK，Apple 已包含新的 Api 和服務，可讓開發人員建立新類別的應用程式和功能。 

如需 tvOS 10 的詳細資訊，請參閱 Apple 的 [tvOS + 應用程式](https://developer.apple.com/tvos/) 檔。

## <a name="whats-new-in-tvos-10"></a>TvOS 10 的新功能

Apple 已在 tvOS 10 中新增數個新的 Api 和服務，以及現有功能的許多增強功能，包括：

## <a name="new-user-interface-styles"></a>新的消費者介面樣式

tvOS 10 現在支援深色和淺色消費者介面主題，所有的內建 UIKit 控制項都將根據使用者的喜好設定自動調整。

建立和執行新的自訂 UI 控制項時，開發人員應該使用 [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) 類別來調整使用者選取的主題。

如需詳細資訊，請參閱 [新的消費者介面樣式](~/ios/tvos/platform/user-interface-styles.md) 檔。

## <a name="security-and-privacy-enhancements"></a>安全性與隱私權的增強功能

Apple 已對 tvOS 10 中的安全性和隱私權進行了許多增強，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。

因此，在 watchOS 3 (或更新) 版本上執行的應用程式，必須以靜態方式宣告其意圖，方法是在其檔案中輸入一或多個隱私權特定金鑰 `Info.plist` ，向使用者說明應用程式希望取得存取權的原因。

由於 tvOS 10 與 iOS 10 共用這些變更，因此如需詳細資訊，請參閱我們的 iOS 10 [安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md) 指南。

## <a name="video-subscriber-account"></a>影片訂閱者帳戶

影片訂閱者帳戶架構是 tvOS 10 的新功能，可讓支援已驗證串流或隨選影片的應用程式使用使用者的單一登入體驗來驗證其纜線或衛星電視提供者。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>寬色域

tvOS 10 可延伸擴充範圍像素格式的支援，以及整個系統中的寬範圍色彩空間，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這項行為，可進一步分階段減緩支援彩色顯示器的裝置。

此外，已 `UIKit` 修改為在新的擴充 **sRGB** colorspace 中工作，讓您更輕鬆地混合寬色彩 gamuts 中的色彩，而不會大幅降低效能。

Apple 在使用寬色彩時提供下列最佳作法：

- `UIColor` 現在使用 sRGB 色彩空間，而且不會再將值放入 `0.0` 至的 `1.0` 範圍。 如果應用程式依賴先前的夾具行為，則必須針對 tvOS 10 修改它。
- 如果應用程式執行的自訂轉譯 `UIImages` ，請使用新的 [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) 類別來指定延伸範圍或標準範圍格式的使用。
- 使用低層級的 API （例如核心圖形或金屬）來提供影像處理時，應用程式應該使用支援16位浮點值的擴充範圍色彩空間和像素格式。 若有需要，應用程式必須手動將色彩元件值的夾具。
- 核心圖形、核心影像和金屬效能著色器全都提供在兩個色彩空間之間轉換的新方法。

若要深入瞭解，請參閱我們的 [寬色彩指南簡介](~/ios/platform/wide-color.md) 。

## <a name="newly-available-existing-frameworks"></a>新推出的現有架構

有幾個可在 iOS (而非 tvOS) 上使用的架構，可供 tvOS 10 使用，例如：

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- 相片
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增專案，Apple 在 tvOS 10 中進行了許多其他的次要架構變更。

若要深入瞭解，請參閱我們的 [其他架構變更](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) 指南。

## <a name="deprecated-apis"></a>已被取代的 API

TvOS 10 尚未取代任何 Api 或架構。 如需完整的 API 修改清單，請參閱 Apple 的 [tvOS 10 Api 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) 檔。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)