---
title: iOS 中的使用者介面
description: 本檔連結的指南會說明如何在 Xamarin iOS 應用程式中建立使用者介面。 連結的指南涵蓋外觀 API、建立使用者介面物件、版面配置選項等等。
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 325898b3c934e25ae1610a3437f787476dcd22cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003333"
---
# <a name="user-interfaces-in-ios"></a>iOS 中的使用者介面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[外觀 API](introduction-to-the-appearance-api.md)

iOS allows many visual attributes of the user interface controls to be themed using the UIAppearance APIs.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[建立使用者介面物件](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple groups related pieces of functionality into “frameworks” which equate to Xamarin.iOS namespaces. `UIKit` is the namespace that contains all the user interface controls for iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[版面配置選項](~/ios/user-interface/ios-ui/layout-options.md)

There are two different mechanisms for controlling the layout when a view is resized or rotated: Autosizing and Autolayout.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

This article covers the new types of haptic feedback available in iOS 10 and how to implement them in Xamarin.iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)

Your code should only make changes to user interface controls from the main (or UI) thread. Any UI updates that occur on a different thread (such as a callback or background thread) may not get rendered to the screen, or could even cause a crash.
