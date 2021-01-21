---
title: Xamarin 中的深色模式
description: 深色模式是適用于淺色和深色主題的全新全系統選項。 iOS 使用者現在可以選擇主題或允許 iOS 動態變更外觀。
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: 77f852914e41b636901348a0490d2d5927b044f0
ms.sourcegitcommit: e27e29c14b783263e063baaa65d4eecb8dd31f57
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98628783"
---
# <a name="dark-mode-in-xamarinios"></a>Xamarin 中的深色模式

深色模式是淺色和深色主題的全系統選項。 iOS 使用者現在可以選擇主題，或允許 iOS 根據環境和當日時間動態變更外觀。

本檔介紹深色模式，以及在 iOS 13 應用程式中支援深色模式。

## <a name="requirements"></a>規格需求

深色模式需要 iOS 13 和 Xcode 11、Xamarin. iOS 12.99，以及 Visual Studio 2019 或 Visual Studio 2019 for Mac （具有 Xcode 11 支援）。

## <a name="turning-on-dark-mode"></a>開啟深色模式

Apple 提供 iOS 13 中的開發人員功能表，可在深色和淺色模式之間切換。 在 iOS 13 模擬器中，開啟 [ **設定** ] 並選擇 [ **開發人員** ] 區段，然後將滾動至 **深色外觀** 切換。 此變更將會反映在整個模擬器環境中：

![開啟深色模式](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>淺色和深色模式的資產

Visual Studio 中的資產目錄現在支援每種面板模式的選擇性影像和色彩：通用、深色和淺色。 以這種方式定義影像和色彩時，iOS 會自動選擇適當的影像和色彩。

在 iOS 專案中開啟 **>appicon** 檔案，並新增映射集。 請注意，您可以指定任何目標解析度的通用、深色和淺色影像。 在下方的螢幕擷取畫面中，有一個深色的影像，以及名稱為 "MicrosoftLogo" 的淺色：

![淺色和深色模式的資產](dark-mode-images/LightAndDark_AssetCatalog2.png)

**>appicon** 也包含 **BackgroundColor** 和 **TitleColor** 的專案，其為色彩定義。 這些色彩現在可依名稱提供，供整個應用程式使用。 **BackgroundColor** 已指派給視圖的背景，並將 **TitleColor** 指派給標籤，如下列螢幕擷取畫面所示：

![螢幕擷取畫面會以淺色和深色模式顯示具有不同背景和標題色彩的行動裝置。](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>動態系統色彩

Apple 引進了新的語義色彩，可根據新的深色模式設定動態調整其外觀。

## <a name="summary"></a>[摘要]

本文引進適用于 iOS 的深色模式，並使用資產目錄為每個模式指定影像和色彩。

## <a name="related-links"></a>相關連結

- [深色模式設計指導方針](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [語義色彩](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
