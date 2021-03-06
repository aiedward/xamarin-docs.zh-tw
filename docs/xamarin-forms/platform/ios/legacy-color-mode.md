---
title: 在 iOS 上 VisualElement 舊版色彩模式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用停用舊版色彩模式的 iOS 平臺特定 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 60FFBA67-6E06-439B-A5EB-8C808285E2CD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d10b48399fd0457bbf9cf63b4a57e17bce8f0f8f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372713"
---
# <a name="visualelement-legacy-color-mode-on-ios"></a>在 iOS 上 VisualElement 舊版色彩模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

某些 Xamarin.Forms 視圖功能是舊版色彩模式。 在此模式中，當 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 視圖的屬性設定為時， `false` 此視圖會覆寫使用者所設定的色彩，其預設原生色彩為 [已停用] 狀態。 為了回溯相容性，這種舊版色彩模式會維持支援的視圖預設行為。

這個 iOS 平臺特定會停用上的舊版色彩模式 [`VisualElement`](xref:Xamarin.Forms.VisualElement) ，如此一來，使用者所設定的色彩也會維持停用狀態。 它是在 XAML 中使用，方法是將 [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) 附加屬性設定為 `false` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. SetIsLegacyColorModeEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} 是命名空間中的}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制是否停用舊版色彩模式。 此外，[ `VisualElement.GetIsLegacyColorModeEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. VisualElement. GetIsLegacyColorModeEnabled (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。VisualElement} ) # A3 方法可用來傳回舊版色彩模式是否已停用。

結果是可以停用舊版色彩模式，如此一來，使用者所設定的色彩也會在停用視圖時維持原狀：

![舊版色彩模式已停用](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> 在 [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) 視圖上設定時，會完全忽略舊版色彩模式。 如需視覺狀態的詳細資訊，請參閱 [ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)