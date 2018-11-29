---
title: 使用 XAML 樣式的樣式設定 Xamarin.Forms 應用程式
description: 本指南說明如何藉由使用 XAML 樣式自訂 Xamarin.Forms 應用程式的外觀。
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/17/2016
ms.openlocfilehash: 2607298bdc0842f60a1d1a3299bed61bbea925a1
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459859"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>使用 XAML 樣式的樣式設定 Xamarin.Forms 應用程式

## <a name="introductionintroductionmd"></a>[簡介](introduction.md)

Xamarin.Forms 應用程式通常會包含多個具有相同的外觀的控制項。 設定每個個別控制項的外觀可能重複又容易出錯。 相反地，樣式可以建立群組並設定上的控制項類型的屬性可用來自訂控制項外觀。

## <a name="explicit-stylesexplicitmd"></a>[明確樣式](explicit.md)

*明確*樣式是藉由設定選擇性地套用到控制項及其[ `Style` ](xref:Xamarin.Forms.VisualElement.Style)屬性。

## <a name="implicit-stylesimplicitmd"></a>[隱含樣式](implicit.md)

*隱含*樣式是使用相同的所有控制項的其中一個[ `TargetType` ](xref:Xamarin.Forms.Style.TargetType)，而不需要每個控制項，以參考樣式。

## <a name="global-stylesapplicationmd"></a>[全域樣式](application.md)

樣式可供全域藉由將它們新增至應用程式的[ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)。 這有助於避免跨頁面或控制項的樣式重複。

## <a name="style-inheritanceinheritancemd"></a>[樣式繼承](inheritance.md)

若要減少重複，並啟用重複使用其他樣式可以繼承樣式。

## <a name="dynamic-stylesdynamicmd"></a>[動態樣式](dynamic.md)

樣式，請勿回應屬性的變更和應用程式的持續期間保持不變。 不過，應用程式可以使用動態資源回應在執行階段動態的樣式變更。

## <a name="device-stylesdevicemd"></a>[裝置樣式](device.md)

Xamarin.Forms 包含六*動態*樣式，又稱為*裝置*樣式，在[ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles)類別。 所有六個樣式可以套用至[ `Label` ](xref:Xamarin.Forms.Label)只執行個體。
