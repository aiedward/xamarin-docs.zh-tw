---
title: Windows 上的 VisualElement 舊版色彩模式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用停用舊版色彩模式的 Windows 平臺特定 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: B8759309-07C7-4DCA-A18A-C1A198A7951B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3bcbee04edbeebf3949673a53edde4200856f5c6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84132107"
---
# <a name="visualelement-legacy-color-mode-on-windows"></a>Windows 上的 VisualElement 舊版色彩模式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

有些 Xamarin.Forms views 功能是舊版色彩模式。 在此模式中，當 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 視圖的屬性設定為時 `false` ，此視圖會覆寫使用者所設定的色彩，其預設原生色彩為停用狀態。 為了回溯相容性，這種舊版色彩模式仍然是支援的視圖的預設行為。

此通用 Windows 平臺平臺特定的會停用此舊版色彩模式，讓使用者在視圖上設定的色彩即使在停用視圖時仍會保留。 它會在 XAML 中使用，方法是將 [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) 附加屬性設定為 `false` ：

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Windows>`方法會指定這個平臺特定的只會在 Windows 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. SetIsLegacyColorModeEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}，system.string）方法，在命名空間中， [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) 可用來控制是否停用舊版色彩模式。 此外，[ `VisualElement.GetIsLegacyColorModeEnabled` ] （x： Xamarin.Forms 。PlatformConfiguration. WindowsSpecific. VisualElement. GetIsLegacyColorModeEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration。 Windows， Xamarin.Forms 。VisualElement}））方法可用來傳回舊版色彩模式是否已停用。

結果是可以停用舊版色彩模式，讓使用者在視圖上設定的色彩即使停用視圖也會保留：

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Legacy color mode disabled")

> [!NOTE]
> 在 [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) 視圖上設定時，會完全忽略舊版色彩模式。 如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
