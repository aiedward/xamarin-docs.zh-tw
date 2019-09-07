---
title: tvOS 10 簡介
description: 本文介紹 tvOS 10 for Xamarin. tvOS 開發人員所提供的所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 8c338f8a5b2f1d41b1ea0f61778a1c14eb84ce08
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769148"
---
# <a name="introduction-to-tvos-10"></a>tvOS 10 簡介

_本文介紹 tvOS 10 for Xamarin. tvOS 開發人員所提供的所有新的和修改過的 Api 和功能。_

新的 tvOS 10 SDK Apple 已包含新的 Api 和服務，可讓開發人員建立新類別的應用程式和功能。 

如需 tvOS 10 的詳細資訊，請參閱 Apple 的[tvOS + 應用程式](https://developer.apple.com/tvos/)檔。

## <a name="whats-new-in-tvos-10"></a>TvOS 10 的新功能

Apple 在 tvOS 10 中新增了數個新的 Api 和服務，以及現有功能的許多增強功能，包括：

## <a name="new-user-interface-styles"></a>新的使用者介面樣式

tvOS 10 現在支援深色和淺色使用者介面主題，所有的內建 UIKit 控制項都會根據使用者的喜好設定自動調整。

建立和執行新的自訂 UI 控制項時，開發人員應該使用[UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection)類別來調整使用者所選取的主題。

如需詳細資訊，請參閱我們的[新使用者介面樣式](~/ios/tvos/platform/user-interface-styles.md)檔。

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 在 tvOS 10 的安全性和隱私權方面做了幾項增強，可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權。

因此，在 watchOS 3 （或更新版本）上執行的應用程式必須在其`Info.plist`檔案中輸入一或多個隱私權特定金鑰，以靜態方式宣告其意圖，以存取特定功能或使用者資訊，並向使用者說明應用程式想要取得存取權的原因。

由於 tvOS 10 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南，以取得詳細資訊。

## <a name="video-subscriber-account"></a>影片訂閱者帳戶

Video 訂閱者帳戶架構是 tvOS 10 的新功能，可讓支援已驗證串流或隨選影片的應用程式使用使用者的單一登入體驗，向其纜線或衛星電視提供者進行驗證。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>寬色域

tvOS 10 延伸了擴充範圍像素格式的支援，以及整個系統的寬範圍色彩空間，包括核心圖形、核心影像、金屬和 AVFoundation 等架構。 在整個圖形堆疊中提供這種行為，即可進一步分階段減緩具有寬色彩顯示的裝置支援。

此外， `UIKit`已修改為在新的擴充的**sRGB** colorspace 中工作，讓您更輕鬆地混合寬色彩 gamuts 中的色彩，而不會大幅降低效能。

使用寬色彩時，Apple 提供下列最佳作法：

- `UIColor`現在會使用 sRGB 色彩空間，而不會再將值設`0.0`為`1.0` to 範圍。 如果應用程式依賴先前的夾具行為，就必須修改 tvOS 10。
- 如果應用程式執行的自訂`UIImages`轉譯，請使用新的[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)類別來指定擴充範圍或標準範圍格式的使用。
- 當使用低層級的 API （例如核心圖形或金屬）來提供影像處理時，應用程式應該使用支援16位浮點值的擴充範圍色彩空間和像素格式。 必要時，應用程式必須手動將色彩元件值設為 [夾具]。
- 核心圖形、核心影像和金屬效能著色器全都提供了新的方法，可在兩個色彩空間之間進行轉換。

若要深入瞭解，請參閱我們[的寬色彩指南簡介](~/ios/platform/wide-color.md)。

## <a name="newly-available-existing-frameworks"></a>新推出的現有架構

IOS （而非 tvOS）上提供的數個架構已提供給 tvOS 10，例如：

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- 相片
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>其他架構變更

除了上面所列的主要架構變更和新增功能之外，Apple 也在 tvOS 10 中進行了許多其他的次要架構變更。

若要深入瞭解，請參閱我們的[其他架構變更](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>已被取代的 API

TvOS 10 不會取代任何 Api 或架構。 如需 API 修改的完整清單，請參閱 Apple 的[tvOS 10 Api 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html)檔。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
