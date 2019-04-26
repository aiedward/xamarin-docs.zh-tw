---
title: 建置使用者介面時，iOS 設計工具
description: 本文件說明如何使用適用於 iOS 的 Xamarin 設計工具來建置應用程式的使用者介面，分鏡腳本和.xib 檔案。 它會連結至文件，其中討論工具的可用性、 基本功能，可設計的控制項，並提供其用法的逐步解說。
ms.prod: xamarin
ms.assetid: E35EFB69-EBBA-40E3-ADBE-CB8016F17127
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/31/2018
ms.openlocfilehash: 7c6529c539ab502fb6c13226acd18a57f8f6d58e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378663"
---
# <a name="building-user-interfaces-with-the-ios-designer"></a>建置使用者介面時，iOS 設計工具

_適用於 iOS 的 Xamarin 設計工具是適用於 iOS 分鏡腳本和 Interface Builder 的格式完全整合與 Visual Studio for Mac 和 Visual Studio 的視覺化設計工具。IOS 設計工具會維護的分鏡腳本和.xib 格式，完全相容，讓您可以在 Visual Studio for Mac 或 Visual Studio 除了 Xcode 的 Interface Builder 中編輯檔案。此外，適用於 iOS 的 Xamarin 設計工具支援進階的功能，例如在設計階段編輯器中呈現的自訂控制項。_

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![iOS 設計工具在 Visual Studio for Mac](images/designer-vsmac-sml.png "iOS 設計工具")](images/designer-vsmac.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![iOS 設計工具在 Visual Studio](images/designer-vs.png "iOS 設計工具")](images/designer-vs.png#lightbox)

-----

## <a name="availability"></a>可用性

在 Visual Studio for Mac 和 Windows 上的 Visual Studio 2017 中使用 Xamarin iOS 設計工具。

這些指南假設您熟悉內容涵蓋[Xamarin.iOS 快速入門引導](~/ios/get-started/index.md)。

## <a name="ios-designer-basicsintroductionmd"></a>[iOS 設計工具基本概念](introduction.md)

本指南涵蓋的 Xamarin iOS 設計工具的功能。 它涵蓋了設計工具的基本概念，顯示如何使用設計工具以視覺化方式配置控制項，以及如何編輯內容。

## <a name="designable-controls-overviewios-designable-controls-overviewmd"></a>[可設計的控制項概觀](ios-designable-controls-overview.md)

深入了解自訂控制項，在本指南將說明如何建立和需求必須符合要呈現在設計介面上。 此外，它會示範如何偵錯時使用可設計的控制項可能會發生的常見問題。

## <a name="walkthrough---using-custom-controls-with-ios-designerios-designable-controls-walkthroughmd"></a>[逐步解說-使用 iOS 設計工具中的自訂控制項](ios-designable-controls-walkthrough.md)

本文將逐步解說示範如何建立自訂控制項，並在 iOS 設計工具中使用它。 它示範如何讓控制項設計工具的工具箱中可用，因此它可以拖/放至檢視。 此外，它會顯示如何實作控制項，讓它正確呈現在設計階段和執行階段，以及如何建立可以在設計階段設定的屬性。

## <a name="auto-layout-with-the-xamarin-ios-designerdesigner-auto-layoutmd"></a>[使用 Xamarin iOS 設計工具的自動版面配置](designer-auto-layout.md)

本指南會介紹 iOS 自動版面配置 」 和 「 iOS 設計工具中可用的新條件約束 」 工作流程。
