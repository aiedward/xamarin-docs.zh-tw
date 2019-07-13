---
title: tvOS 10 簡介
description: 這篇文章會介紹的所有新的及修改 Api 和 tvOS 10 中可用的功能適用於 Xamarin.tvOS 開發人員。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6bad05f62fa9168818aa9a2e73cc1e2830af03b3
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830027"
---
# <a name="introduction-to-tvos-10"></a>tvOS 10 簡介

_這篇文章會介紹的所有新的及修改 Api 和 tvOS 10 中可用的功能適用於 Xamarin.tvOS 開發人員。_

使用 tvOS 的新 Api 和服務，可讓開發人員建立新的類別目錄的應用程式和功能，有包含 10 SDK Apple。 

如需有關 tvOS 10 的詳細資訊，請參閱 Apple [tvOS 應用程式 +](https://developer.apple.com/tvos/)文件。

## <a name="whats-new-in-tvos-10"></a>新功能 tvOS 10

Apple 已加入數個新的 Api 和服務，在 tvOS 10 以及現有功能，包括許多增強功能：

## <a name="new-user-interface-styles"></a>新的使用者介面樣式

tvOS 10 現在支援暗色調 」 和 「 Light 使用者介面佈景主題，所有的內建 UIKit 控制項將會自動調整，根據使用者的喜好設定。

當建立及實作新的自訂 UI 控制項，應該使用開發人員[UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection)類別，以配合使用者的選取的佈景主題。

如需詳細資訊，請參閱我們[新的使用者介面樣式](~/ios/tvos/platform/user-interface-styles.md)文件。

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性與隱私權 tvOS 10 可協助開發人員改進其應用程式的安全性，並確保使用者的隱私權的數個增強功能。

如此一來，在 watchOS 3 （或更新版本） 上執行的應用程式必須以靜態方式會宣告輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊的意圖其`Info.plist`說明為什麼應用程式想要存取使用者的檔案。

因為 tvOS 10 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南以取得詳細的資訊。

## <a name="video-subscriber-account"></a>影片的訂閱者帳戶

新增 tvOS 10 影片訂閱者帳戶的架構允許應用程式該支援驗證串流或點播視訊來向他們的有線電視或衛星電視提供者供使用者使用單一登入體驗。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>寬色域

tvOS 10 延伸的延伸範圍像素格式和整個系統，包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬色域圖色彩空間的支援。 藉由提供整個圖形堆疊在這種行為，進一步降低與寬的色彩顯示裝置的支援。

此外，`UIKit`已經過修改才能在新擴充**sRGB** colorspace，方便您混合使用中而不會顯著的效能遺失的寬色域色階的色彩。

使用各種色彩時，Apple 提供以下最佳作法：

- `UIColor` 現在會使用 sRGB 色彩空間，並將無法再將值`0.0`至`1.0`範圍。 如果應用程式依賴舊版的 clamp 行為時，它必須針對 tvOS 10 進行修改。
- 如果應用程式執行的自訂轉譯`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)類別，以指定要使用的擴充範圍或標準範圍的格式。
- 當使用低層級的 API，例如核心圖形或金屬提供映像處理時，應用程式應該使用的延伸的範圍的色彩空間和像素格式支援 16 位元浮點值。 必要時，應用程式必須以手動方式將色彩元件值。
- Core 圖形、 Core 映像和金屬效能著色器都有提供兩個色彩空間之間進行轉換的新方法。

若要深入了解，請參閱我們[寬色域簡介](~/ios/platform/wide-color.md)指南。

## <a name="newly-available-existing-frameworks"></a>新的、 可用的現有架構

IOS （和不 tvOS） 可用的數個架構已有可用的 tvOS 10 例如：

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- 相片
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>其他的架構變更

除了主要的架構變更和上面所列的內容，Apple 已變更許多其他的次要 framework 了 tvOS 10。

若要深入了解，請參閱我們[額外的架構變更](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>已被取代的 API

TvOS 10 的已被取代任何 Api 或架構。 請參閱 Apple [tvOS 10 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html)的 API 修改的文件的完整清單。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [TvOS 10 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
