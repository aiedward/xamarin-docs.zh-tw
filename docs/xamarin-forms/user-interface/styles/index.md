---
title: "樣式"
description: "使用自訂外觀樣式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 934948579e5f3fb19c7afe49f4e86a1ef255b77f
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="styles"></a>樣式

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

Xamarin.Forms 應用程式通常會包含多個具有相同的外觀的控制項。 設定每個個別控制項的外觀可能重複而且容易產生錯誤。 相反地，樣式可以建立自訂控制項外觀的群組上的控制項類型所提供的設定屬性。

## <a name="explicit-stylesexplicitmd"></a>[明確的樣式](explicit.md)

*明確*樣式是藉由設定選擇性地套用到控制項及其[ `Style` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Style/)屬性。

## <a name="implicit-stylesimplicitmd"></a>[隱含的樣式](implicit.md)

*隱含*樣式是使用相同的所有控制項[ `TargetType` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.TargetType/)，而不需要每個控制項，來參考樣式。

## <a name="global-stylesapplicationmd"></a>[全域樣式](application.md)

樣式可供全域將它們新增至應用程式的[ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)。 這有助於避免跨頁或控制項的出現重複的樣式。

## <a name="style-inheritanceinheritancemd"></a>[樣式繼承](inheritance.md)

樣式可以繼承自其他樣式來減少重複，並允許重複使用。

## <a name="dynamic-stylesdynamicmd"></a>[動態樣式](dynamic.md)

樣式請勿回應屬性的變更，並針對應用程式的持續時間維持不變。 不過，應用程式可以藉由使用動態的資源回應在執行階段動態樣式變更。

## <a name="device-stylesdevicemd"></a>[裝置樣式](device.md)

Xamarin.Forms 包含六個*動態*樣式，稱為*裝置*樣式，在[ `Devices.Styles` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Device+Styles/)類別。 所有六個樣式套用至[ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)僅限執行個體。
