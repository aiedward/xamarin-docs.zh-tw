---
title: iOS 中的使用者介面
description: 本文件所連結說明如何建置 Xamarin.iOS 應用程式中的使用者介面的指南。 連結的指南涵蓋的外觀 API，建立使用者介面物件、 版面配置選項等等。
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: efb88ada8a4b4c36dd49de137eb64acd63552968
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61382332"
---
# <a name="user-interfaces-in-ios"></a>iOS 中的使用者介面

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[外觀 API](introduction-to-the-appearance-api.md)

iOS 可讓使用者介面控制項來配置其佈景主題使用 UIAppearance Api 的許多視覺屬性。

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[建立使用者介面物件](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple 群組關聯的部分功能重構成 「 架構 」，這等同於 Xamarin.iOS 命名空間。 `UIKit` 是包含適用於 iOS 的所有使用者介面控制項的命名空間。

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[版面配置選項](~/ios/user-interface/ios-ui/layout-options.md)

有兩種不同的機制，來檢視已調整大小或旋轉時，控制配置：自動調整大小，並自動版面配置。

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[提供 Haptic 意見反應](~/ios/user-interface/ios-ui/haptic-feedback.md)

本文章涵蓋適用於 iOS 10 和如何在 Xamarin.iOS 中實作它們的 haptic 意見反應的新類型。

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[使用 UI 執行緒](~/ios/user-interface/ios-ui/ui-thread.md)

您的程式碼只應該要對使用者介面控制項，從主要 （或 UI） 執行緒。 （例如回呼或背景執行緒） 的不同執行緒發生的任何 UI 更新可能不取得轉譯至螢幕，或甚至可能會造成當機。




