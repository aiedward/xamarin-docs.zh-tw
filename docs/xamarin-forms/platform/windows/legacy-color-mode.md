---
title: VisualElement 舊版 Windows 上的色彩模式
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Windows 平台特定，停用的 Xamarin.Forms 舊版的色彩模式。
ms.prod: xamarin
ms.assetid: B8759309-07C7-4DCA-A18A-C1A198A7951B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 64b6d104319722ba56eb01628090c5ee042d5b11
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60855316"
---
# <a name="visualelement-legacy-color-mode-on-windows"></a>VisualElement 舊版 Windows 上的色彩模式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Xamarin.Forms 檢視的一些功能的舊版的色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 針對回溯相容性，這個舊版的色彩模式仍受支援的檢視表的預設行為。

此通用 Windows 平台特定平台-停用這個舊版的色彩模式，以便使用者在檢視上的色彩設定維持即使檢視已停用。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) ; 附加屬性`false`:

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Windows>`方法可讓您指定這個平台專屬只會在 Windows 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 颾魤 ㄛ [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement}))方法可以用來傳回是否要停用舊版的色彩模式。

結果是，就可以停用舊版的色彩模式，以便使用者在檢視上所設定的色彩甚至保持停用檢視時：

![](legacy-color-mode-images/legacy-color-mode-disabled.png "已停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上的舊版的色彩模式會完全忽略。 如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
