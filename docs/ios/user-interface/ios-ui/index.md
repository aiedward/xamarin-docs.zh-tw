---
title: iOS 中的使用者介面
description: 本檔連結的指南會說明如何在 Xamarin iOS 應用程式中建立使用者介面。 連結的指南涵蓋外觀 API、建立使用者介面物件、版面配置選項等等。
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 106cadc6fef43c06597cf1e3b846bd48cecb6fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287057"
---
# <a name="user-interfaces-in-ios"></a>iOS 中的使用者介面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[外觀 API](introduction-to-the-appearance-api.md)

iOS 可讓使用者介面控制項的許多視覺屬性，使用 UIAppearance Api 來進行主題。

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[建立使用者介面物件](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple 將相關的功能片段分組為「架構」，這會等同于 Xamarin. iOS 命名空間。 `UIKit`是包含所有 iOS 使用者介面控制項的命名空間。

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[版面配置選項](~/ios/user-interface/ios-ui/layout-options.md)

有兩種不同的機制，可在調整視圖大小或旋轉時控制版面配置：自動調整大小和自動版式。

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

本文涵蓋 iOS 10 中提供的新類型 haptic 意見反應，以及如何在 Xamarin 中執行。

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)

您的程式碼應該只會對主要（或 UI）執行緒變更使用者介面控制項。 在不同的執行緒上發生的任何 UI 更新（例如回呼或背景執行緒）可能不會轉譯至螢幕，或甚至會造成當機。




