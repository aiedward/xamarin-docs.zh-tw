---
title: 使用 Xamarin 建立使用者介面
description: 本檔說明如何在 Xamarin iOS 應用程式中建立使用者介面。 它提供 iOS 設計工具、分鏡腳本、一般 iOS 介面概念，以及 iOS 使用者介面控制項的指南連結。
ms.prod: xamarin
ms.assetid: 2B3E45FA-C30F-D708-0E8F-3EE02BD1A867
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: 35188f2480ba531b5b90113121ced70c2c732d75
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602290"
---
# <a name="building-user-interfaces-with-xamarinios"></a>使用 Xamarin 建立使用者介面

## <a name="storyboards"></a>[Storyboard](~/ios/user-interface/storyboards/index.md)

分鏡腳本是應用程式外觀和流程的視覺標記法。 Visual Studio for Mac 可讓您與 Xcode 介面產生器互動，以視覺化方式設計您的應用程式畫面，以及使用 c # 存取 views、控制器和 segue，以提供更多控制。 

## <a name="ios-designer"></a>[iOS 設計工具](~/ios/user-interface/designer/index.md)

> [!WARNING]
> IOS 設計工具在 Visual Studio 2019 16.8 版和 Visual Studio 2019 for Mac 版本8.8 中已淘汰，並已在 Visual Studio 2019 版本16.9 和 Visual Studio for Mac 版本8.9 中移除。
> 建立 iOS 使用者介面的建議方式是直接在執行 Xcode 的 Mac 上執行。 如需詳細資訊，請參閱 [使用 Xcode 設計使用者介面](~/ios/user-interface/storyboards/index.md)。 

我們已建立適用于 iOS 分鏡腳本格式的設計工具，其已完全整合到 Visual Studio for Mac 中。 IOS 設計工具會維持與分鏡腳本格式的完整相容性，以便在 Xcode 或 Visual Studio for Mac 中編輯檔案。 此外，編輯器支援先進的功能，例如在設計階段于編輯器中呈現的自訂控制項。

## <a name="user-interface-in-ios"></a>[iOS 中的使用者介面](~/ios/user-interface/ios-ui/index.md)

涵蓋在 Xamarin iOS 應用程式中使用 iOS 使用者介面，包括：外觀 API、建立使用者介面物件、配置選項、提供 Haptic 意見反應，以及使用 UI 執行緒。

## <a name="user-interface-controls"></a>[使用者介面控制項](~/ios/user-interface/controls/index.md)

Xamarin 會公開 Apple 提供的所有原生使用者介面物件。 您可以使用 iOS 設計工具、Xcode 的 Interface Builder 或以程式設計的方式，輕鬆地將這些應用程式新增至 Xamarin 應用程式。 無論您選擇哪一種方法，Xamarin 都會以 c # 公開所有的使用者介面物件屬性和方法。
