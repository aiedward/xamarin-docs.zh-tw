---
title: 使用 iOS 設計工具建立使用者介面
description: 本檔說明如何使用 Xamarin Designer for iOS，利用分鏡腳本和 xib 檔案來建立應用程式的使用者介面。 它會連結到討論工具可用性的檔、其基本功能、可設計的控制項，以及提供其使用方式的逐步解說。
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/31/2018
ms.openlocfilehash: 3a58fc58b429a8a16437773289cd4d7286bbff81
ms.sourcegitcommit: d2aa3a8bf9a60b6708db55b10b0c6893c06d3256
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331477"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>使用 iOS 設計工具建立使用者介面

_Xamarin Designer for iOS 是適用于 iOS 分鏡腳本的視覺化設計工具，以及與 Visual Studio for Mac 和 Visual Studio 完全整合的 Interface Builder 格式。IOS 設計工具會維持與分鏡腳本和 xib 格式的完整相容性，如此一來，除了 Xcode 的 Interface Builder 之外，還可以在 Visual Studio for Mac 或 Visual Studio 中編輯檔案。此外，Xamarin Designer for iOS 也支援像是在設計階段于編輯器中轉譯的自訂控制項等 advanced 功能。_

> [!WARNING]
> 現在，建立 iOS 使用者介面的建議方式是直接在執行 Xcode 的 Mac 上執行。 您可以在 [這裡找到](~/ios/user-interface/ios-use-xcode.md)詳細資訊。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

[![Visual Studio for Mac 中的 iOS 設計工具](images/designer-vsmac-sml.png "iOS 設計工具")](images/designer-vsmac.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Visual Studio 中的 iOS 設計工具](images/designer-vs.png "iOS 設計工具")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>可用性

Xamarin Designer for iOS 可在 Windows 上的 Visual Studio for Mac 和 Visual Studio 2017 中取得。

這些指南假設您已經熟悉 [Xamarin 消費者入門指南](~/ios/get-started/index.md)中所涵蓋的內容。

## <a name="ios-designer-basics"></a>[iOS 設計工具基本概念](introduction.md)

本指南涵蓋 Xamarin iOS 設計工具的功能。 它涵蓋了設計工具的基本概念，並示範如何使用設計工具以視覺化方式配置控制項，以及如何編輯屬性。

## <a name="designable-controls-overview"></a>[設計的控制項總覽](ios-designable-controls-overview.md)

本指南深入探討自訂控制項、如何建立它們，以及它們必須符合才能在設計介面上呈現的需求。 此外，它也會示範如何使用可設計的控制項來偵測可能發生的常見問題。

## <a name="walkthrough---using-custom-controls-with-ios-designer"></a>[逐步解說-搭配 iOS 設計工具使用自訂控制項](ios-designable-controls-walkthrough.md)

本文提供逐步解說，說明如何建立自訂控制項，並在 iOS 設計工具中使用它。 它會示範如何在設計工具的 [工具箱] 中提供控制項，讓它可以拖放到視圖上。 此外，它也會示範如何執行控制項，以便在設計階段和執行時間適當地呈現，以及如何建立可在設計階段設定的屬性。

## <a name="auto-layout-with-the-xamarin-ios-designer"></a>[使用 Xamarin iOS 設計工具的自動版面配置](designer-auto-layout.md)

本指南介紹 ios 自動設定和 iOS 設計工具中可用的新條件約束工作流程。
