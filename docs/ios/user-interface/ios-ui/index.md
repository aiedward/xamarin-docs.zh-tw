---
title: "在 iOS 中的使用者介面"
description: "涵蓋使用 Xamarin.iOS 應用程式中的 iOS 使用者介面。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 2B3E45FA-C30F-D708-0E8F-3EE02BD1A867
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 831ddfff7e05c391472b280095564f90528369ff
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="user-interface-in-ios"></a>在 iOS 中的使用者介面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[應用程式開發介面的外觀](introduction-to-the-appearance-api.md)

iOS 可讓使用者介面控制項設定佈景主題使用 UIAppearance Api 的許多視覺屬性。

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[建立使用者介面物件](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple 群組關聯的部分功能"架構"，這等同於 Xamarin.iOS 命名空間。 `UIKit` 是包含適用於 iOS 的所有使用者介面控制項的命名空間。

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[版面配置選項](~/ios/user-interface/ios-ui/layout-options.md)

有兩個不同的機制，可檢視已調整大小或旋轉時，控制配置： 自動調整大小和自動版面配置。

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

本文涵蓋 haptic 意見反應 iOS 10 以及如何實作它們 Xamarin.iOS 中可用的新的類型。

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)

您的程式碼應該只會提供對使用者介面控制項，從主要 （或 UI） 執行緒。 在不同的執行緒 （例如回呼或背景的執行緒） 進行任何 UI 更新可能就無法取得轉譯至螢幕，或甚至會損毀。




