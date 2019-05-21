---
title: 在 Android 上的 VisualElement 舊版色彩模式
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 Android 平台特定，停用的 Xamarin.Forms 舊版的色彩模式。
ms.prod: xamarin
ms.assetid: 37D95A2D-74AC-488A-B903-2BDD799EAA5C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: e22d857ab3b254d101d044bf32a9f0b6312a3ce3
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926588"
---
# <a name="visualelement-legacy-color-mode-on-android"></a>在 Android 上的 VisualElement 舊版色彩模式

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Xamarin.Forms 檢視的一些功能的舊版的色彩模式。 在此模式中，當[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)檢視的屬性設定為`false`，檢視將會覆寫設定的使用者停用狀態的預設原生色彩的色彩。 針對回溯相容性，這個舊版的色彩模式仍受支援的檢視表的預設行為。

此 Android 平台特定停用這個舊版的色彩模式，以便即使檢視已停用，仍能由使用者在檢視上所設定的色彩。 它由在 XAML 中設定[ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.IsLegacyColorModeEnabledProperty) ; 附加屬性`false`:

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

_legacyColorModeDisabledButton.On<Android>().SetIsLegacyColorModeEnabled(false);
```

`VisualElement.On<Android>`方法可讓您指定這個平台專屬只會在 Android 上執行。 [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)命名空間，可用來控制是否已停用舊版的色彩模式。 颾魤 ㄛ [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.VisualElement}))方法可以用來傳回是否要停用舊版的色彩模式。

結果是，就可以停用舊版的色彩模式，以便使用者在檢視上所設定的色彩甚至保持停用檢視時：

![](legacy-color-mode-images/legacy-color-mode-disabled.png "已停用舊版的色彩模式")

> [!NOTE]
> 設定時[ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup)檢視上的舊版的色彩模式會完全忽略。 如需有關視覺狀態的詳細資訊，請參閱[Xamarin.Forms Visual State Manager](~/xamarin-forms/user-interface/visual-state-manager.md)。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
