---
title: TvOS 10 簡介
description: 本文介紹的所有新的及修改應用程式開發介面和功能中 tvOS 10 Xamarin.tvOS 開發人員。
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 642a851cbfc0450ee8f5f5c4d798c40778e3d3dd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-tvos-10"></a>TvOS 10 簡介

_本文介紹的所有新的及修改應用程式開發介面和功能中 tvOS 10 Xamarin.tvOS 開發人員。_

與新 tvOS 10 SDK Apple 已包含新的 Api 和服務，可讓開發人員建立新的類別目錄的應用程式和功能。 

如需有關 tvOS 10 的詳細資訊，請參閱 Apple [tvOS + 應用程式](https://developer.apple.com/tvos/)文件。

## <a name="whats-new-in-tvos-10"></a>新功能 tvOS 10

Apple 已加入 tvOS 10 以及強化現有功能，包括數個新的 Api 和服務：

## <a name="new-user-interface-styles"></a>新的使用者介面樣式

tvOS 10 現在支援所有內建 UIKit 控制項將會自動同時深色和淺色使用者介面主題會隨著調整，依據使用者的喜好設定。

當建立及實作新的自訂 UI 控制項，開發人員應該使用[UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection)適應使用者選取的佈景主題的類別。

如需詳細資訊，請參閱我們[新的使用者介面樣式](~/ios/tvos/platform/user-interface-styles.md)文件。

## <a name="security-and-privacy-enhancements"></a>安全性和隱私權增強功能

Apple 已對安全性和隱私權可協助開發人員改善其應用程式的安全性，並確保使用者的隱私權 tvOS 10 中的數個增強功能。

如此一來，watchOS 3 （或更新版本） 上執行的應用程式必須以靜態方式宣告試圖輸入一或多個隱私權特定中索引鍵來存取特定功能或使用者資訊及其`Info.plist`解釋為什麼應用程式想要存取的使用者的檔案。

因為 tvOS 10 與 iOS 10 共用這些變更，請參閱我們的 iOS 10[安全性和隱私權增強功能](~/ios/app-fundamentals/security-privacy.md)指南以取得詳細的資訊。

## <a name="video-subscriber-account"></a>視訊的訂閱者帳戶

新 tvos 10，此視訊訂閱者帳戶架構可讓應用程式的支援驗證的資料流或 video-on-demand 向他們的有線電視或衛星電視提供者的使用者使用單一登入體驗。

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>寬的色彩

tvOS 10 擴充的擴充範圍像素格式和整個系統中包括架構，例如核心圖形、 Core 映像、 裸機和 AVFoundation 寬一系列色彩空間的支援。 藉由提供整個圖形堆疊此行為，是進一步降低與寬的色彩顯示裝置的支援。

此外，`UIKit`已修改才能在新擴充**sRGB** colorspace，輕鬆地混合中不會顯著的效能遺失寬色彩色階的色彩。

Apple 時使用寬的色彩，請提供下列最佳作法：

 - `UIColor` 現在使用 sRGB 色彩空間，而且將不再 clamp 值`0.0`至`1.0`範圍。 如果應用程式依賴之前有縮短時間差行為時，它將需要修改 tvos 10。
 - 如果應用程式執行的自訂轉譯`UIImages`，使用新[UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer)類別來指定要使用的擴充範圍或標準範圍的格式。
 - 當使用例如核心圖形或金屬低階 API 提供映像處理時，應用程式應該使用的擴充的範圍的色彩空間和像素格式支援 16 位元浮點值。 在需要時，應用程式必須以手動方式 clamp 色彩元件值。
 - 核心圖形、 Core 映像和金屬效能著色器所有提供的兩個色彩空間之間進行轉換的新方法。

若要進一步了解，請參閱我們[簡介寬色彩](~/ios/platform/wide-color.md)指南。

## <a name="newly-available-existing-frameworks"></a>新的、 可用的現有架構

IOS （和不 tvOS） 上可用的數個架構已提供 tvos 10 例如：

 - ExternalAccessory
 - HomeKit
 - MultipeerConnectivity
 - 相片
 - ReplayKit
 - UserNotification

## <a name="additional-framework-changes"></a>其他架構變更

主要架構變更和加入上面所列的項目，除了 Apple 進行了許多其他次要架構變更 tvOS 10 中。

若要進一步了解，請參閱我們[其他架構變更](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md)指南。

## <a name="deprecated-apis"></a>已被取代的 API

沒有應用程式開發介面或架構中已被取代的 tvOS 10。 請參閱 Apple [tvOS 10 API 差異](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html)的應用程式開發介面修改文件的完整清單。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [在 tvOS 10 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
