---
title: IOS 上的 NavigationPage Bar 文字色彩模式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，控制 NavigationPage 上的狀態列文字色彩是否符合導覽列的亮度。
ms.prod: xamarin
ms.assetid: 03698A44-39F1-4030-9AF5-F10A6713828A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6aa435cebe1976897f8165d1e645179b1ca4aa9f
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940313"
---
# <a name="navigationpage-bar-text-color-mode-on-ios"></a>IOS 上的 NavigationPage Bar 文字色彩模式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個平臺特定的控制項，是否調整上的狀態列文字色彩， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 以符合導覽列的亮度。 它是在 XAML 中使用，方法是將 [`NavigationPage.StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.StatusBarTextColorModeProperty) 附加屬性設定為 [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) 列舉值：

```xaml
<FlyoutPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    x:Class="PlatformSpecifics.iOSStatusBarTextColorModePage">
    <FlyoutPage.Flyout>
        <ContentPage Title="Flyout Page Title" />
    </FlyoutPage.Flyout>
    <FlyoutPage.Detail>
        <NavigationPage BarBackgroundColor="Blue" BarTextColor="White"
                        ios:NavigationPage.StatusBarTextColorMode="MatchNavigationBarTextLuminosity">
            <x:Arguments>
                <ContentPage>
                    <Label Text="Slide the master page to see the status bar text color mode change." />
                </ContentPage>
            </x:Arguments>
        </NavigationPage>
    </FlyoutPage.Detail>
</FlyoutPage>

```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

IsPresentedChanged += (sender, e) =>
{
    var flyoutPage = sender as FlyoutPage;
    if (flyoutPage.IsPresented)
        ((Xamarin.Forms.NavigationPage)flyoutPage.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.DoNotAdjust);
    else
        ((Xamarin.Forms.NavigationPage)flyoutPage.Detail)
          .On<iOS>()
          .SetStatusBarTextColorMode(StatusBarTextColorMode.MatchNavigationBarTextLuminosity);
};
```

`NavigationPage.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `NavigationPage.SetStatusBarTextColorMode` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. SetStatusBarTextColorMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage}， Xamarin.Forms 。命名空間中的 PlatformConfiguration. iOSSpecific. StatusBarTextColorMode) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 會控制是否調整上的狀態列文字色彩， [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 以符合導覽列的亮度，並 [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) 提供兩個可能值的列舉：

- [`DoNotAdjust`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.DoNotAdjust) –表示不應調整狀態列文字色彩。
- [`MatchNavigationBarTextLuminosity`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode.MatchNavigationBarTextLuminosity) –表示狀態列文字色彩應該符合巡覽列的亮度。

此外，[ `GetStatusBarTextColorMode` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. GetStatusBarTextColorMode (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage} ) # A3 方法可以用來取得套用至之列舉的目前值 [`StatusBarTextColorMode`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.StatusBarTextColorMode) [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。

結果是可以調整上的狀態列文字色彩 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，以符合巡覽列的亮度。 在此範例中，當使用者在的和頁面之間切換時，狀態列文字色彩會變更 [`Flyout`](xref:Xamarin.Forms.FlyoutPage.Flyout) [`Detail`](xref:Xamarin.Forms.FlyoutPage.Detail) [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) ：

![狀態列文字色彩模式 Platform-Specific](status-bar-text-color-images/status-bar-text-color-mode.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
