---
title: Xamarin 中的深色模式
description: 深色模式是適用于淺色與深色主題的全新全系統選項。 iOS 使用者現在可以選擇主題, 或允許 iOS 動態變更外觀。
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: d21afcc7d7b130528e9cceac47840acd49b91f59
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129970"
---
# <a name="dark-mode-in-xamarinios"></a>Xamarin 中的深色模式

![此 API 目前為預覽狀態](~/media/shared/preview.png)

深色模式是適用于淺色與深色主題的全系統選項。 iOS 使用者現在可以選擇 [主題] 或 [允許 iOS], 根據環境和一天的時間動態變更外觀。

本檔介紹 iOS 13 應用程式中的深色模式和支援深色模式。

## <a name="requirements"></a>需求

深色模式需要 iOS 13 和 Xcode 11、Xamarin 12.99 和 Visual Studio 2019 或 Visual Studio 2019 for Mac, 含 Xcode 11 支援。

## <a name="turning-on-dark-mode"></a>開啟深色模式

Apple 在 iOS 13 中提供開發人員功能表, 以便在深色和淺色模式之間切換。 在 iOS 13 模擬器中開啟 [**設定**] 並選擇 [**開發人員**] 區段, 然後流覽至 [**深色外觀**] 開關。 此變更會反映在整個模擬器環境中:

![開啟深色模式](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>適用于淺色和深色模式的資產

Visual Studio 中的資產目錄現在支援每種面板模式的選擇性影像和色彩:通用、深色和淺色。 以這種方式定義您的影像和色彩時, iOS 會自動選擇適當的影像和色彩。

在您的 iOS 專案中開啟**assets.xcassets**檔案, 並新增新的映射集。 請注意, 您可以在任何目標解析度中指定通用、深色和淺色影像。 在下面的螢幕擷取畫面中, 有深色的影像, 以及名稱為 "MicrosoftLogo" 的 light:

![適用于淺色和深色模式的資產](dark-mode-images/LightAndDark_AssetCatalog2.png)

**Assets.xcassets**也包含**BackgroundColor**和**TitleColor**的專案, 也就是色彩定義。 這些色彩現在會依名稱提供, 以便在整個應用程式中使用。 **BackgroundColor**已指派給視圖的背景, 而**TitleColor**為標籤, 如下列螢幕擷取畫面所示:

![適用于淺色和深色模式的資產](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>動態系統色彩

Apple 引進了新的語義色彩, 可以根據新的深色模式設定, 動態地調整其外觀。

## <a name="summary"></a>總結

本文引進了適用于 iOS 的深色模式, 並使用資產目錄來指定每個模式的影像和色彩。

## <a name="related-links"></a>相關連結

- [深色模式設計方針](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [語義色彩](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
