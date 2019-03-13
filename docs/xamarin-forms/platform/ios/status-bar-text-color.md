---
title: 在 iOS 上 NavigationPage 列文字的色彩模式
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定的控制列 NavigationPage 上的文字色彩的狀態是否符合導覽列的明暗度。
ms.prod: xamarin
ms.assetid: 03698A44-39F1-4030-9AF5-F10A6713828A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 39db74f8df8d8d3f62ff53b91e17f93f38c44bf0
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209093"
---
# <a name="navigationpage-bar-text-color-mode-on-ios"></a>在 iOS 上 NavigationPage 列文字的色彩模式

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此平台特定的控制項上是否色彩的狀態列文字[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整成符合要求的導覽列的明暗度。 它由在 XAML 中設定[ `NavigationPage.StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty)附加屬性的值[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)列舉型別：

```xaml
<MasterDetailPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <MasterDetailPage.Master>
        <ContentPage Title="Master Page Title" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>

```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var mdp = sender as MasterDetailPage;
    if (mdp.IsPresented)
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)mdp.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.SetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中，控制項上是否色彩的狀態列文字[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)調整以符合亮度的導覽列中，使用[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)提供兩個可能值的列舉型別：

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) – 表示不應該調整的狀態列文字色彩。
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) – 表示狀態列的文字色彩應符合的導覽列的明暗度。

颾魤 ㄛ [ `GetStatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.GetStatusBarTextColorMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法可用來擷取目前的值[ `StatusBarTextColorMode` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode)套用至列舉型別[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage).

結果是在狀態列上的文字色彩[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以調整以符合導覽列的明暗度。 在此範例中，狀態列的文字色彩變更為使用者之間的切換[ `Master` ](xref:Xamarin.Forms.MasterDetailPage.Master)和[ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail)頁面[ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

![](status-bar-text-color-images/status-bar-text-color-mode.png "狀態列文字的色彩模式平台專屬")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
