---
title: 使用 iOS 設計工具建立使用者介面
description: 本檔說明如何使用 Xamarin Designer for iOS 來建立應用程式的使用者介面，其中包含分鏡腳本和 xib 檔案。 它會連結到討論工具可用性的檔、其基本功能、可設計的控制項，並提供其用法的逐步解說。
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/31/2018
ms.openlocfilehash: 157e16da2c524029c29e767cd6b3e5eb550a2389
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021754"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>使用 iOS 設計工具建立使用者介面

_Xamarin Designer for iOS 是 iOS 分鏡腳本的視覺化設計工具，以及與 Visual Studio for Mac 和 Visual Studio 完全整合的 Interface Builder 格式。IOS 設計工具維持與分鏡腳本和 xib 格式的完全相容性，因此除了 Xcode 的 Interface Builder，檔案也可以在 Visual Studio for Mac 或 Visual Studio 中進行編輯。此外，Xamarin Designer for iOS 支援先進的功能，例如編輯器中設計階段所呈現的自訂控制項。_

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![Visual Studio for Mac 中的 iOS 設計工具](images/designer-vsmac-sml.png "IOS 設計工具")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Visual Studio 中的 iOS 設計工具](images/designer-vs.png "IOS 設計工具")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>可用性

Xamarin Designer for iOS 適用于 Visual Studio for Mac 和 Windows 上 Visual Studio 2017 中。

這些指南假設您已熟悉[Xamarin iOS 消費者入門指南](~/ios/get-started/index.md)中所涵蓋的內容。

## <a name="ios-designer-basicsintroductionmd"></a>[iOS 設計工具基本概念](introduction.md)

本指南涵蓋 Xamarin iOS 設計工具的功能。 其中包含設計工具的基本概念，並示範如何使用設計工具，以視覺化方式配置控制項，以及如何編輯屬性。

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[設計的控制項總覽](ios-designable-controls-overview.md)

本指南深入探討自訂控制項、其建立方式，以及必須滿足哪些需求，才能在設計介面上呈現。 此外，它也會示範如何在使用可設計的控制項時，針對可能發生的常見問題進行偵錯工具。

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[逐步解說-搭配 iOS 設計工具使用自訂控制項](ios-designable-controls-walkthrough.md)

本文提供逐步解說，示範如何建立自訂控制項，並在 iOS 設計工具中使用它。 它會示範如何在設計工具的 [工具箱] 中提供控制項，讓它可以拖放到視圖上。 此外，它也會示範如何執行控制項，以便在設計階段和執行時間中正確轉譯，以及如何建立可在設計階段設定的屬性。

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[使用 Xamarin iOS 設計工具進行自動版面配置](designer-auto-layout.md)

本指南介紹 ios 自動設定和 iOS 設計工具中提供的新條件約束工作流程。
