---
title: 在 Android 上 VisualElement 舊版色彩模式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用可停用舊版色彩模式的 Android 平臺特定 Xamarin.Forms 。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0e4300354588e5055e58251caa547a56c751fd94
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86936067"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>在 Android 上 VisualElement 舊版色彩模式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

有些 Xamarin.Forms views 功能是舊版色彩模式。 在此模式中，當 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) 視圖的屬性設定為時 `false` ，此視圖會覆寫使用者所設定的色彩，其預設原生色彩為停用狀態。 為了回溯相容性，這種舊版色彩模式仍然是支援的視圖的預設行為。

此 Android 平臺特定會停用此舊版色彩模式，讓使用者在視圖上設定的色彩即使停用視圖也會維持不變。 它會在 XAML 中使用，方法是將 [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) 附加屬性設定為 `false` ：

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                android:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. VisualElement. SetIsLegacyColorModeEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。VisualElement}，system.string）方法，在命名空間中， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來控制是否停用舊版色彩模式。 此外，[ `VisualElement.GetIsLegacyColorModeEnabled` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. VisualElement. GetIsLegacyColorModeEnabled （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。VisualElement}））方法可用來傳回舊版色彩模式是否已停用。

結果是可以停用舊版色彩模式，讓使用者在視圖上設定的色彩即使停用視圖也會保留：

![舊版色彩模式已停用](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> 在 [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) 視圖上設定時，會完全忽略舊版色彩模式。 如需視覺狀態的詳細資訊，請參閱[ Xamarin.Forms 視覺狀態管理員](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
