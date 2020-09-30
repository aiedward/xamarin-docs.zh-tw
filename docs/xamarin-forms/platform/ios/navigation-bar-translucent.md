---
title: IOS 上的 NavigationPage Bar 半透明度
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，在 NavigationPage 中變更導覽列的透明度。
ms.prod: xamarin
ms.assetid: 1150941B-56DB-4781-BE36-A4C4F9F2C500
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 99e163364161287a8506bfc741d737edfaf88e4c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556759"
---
# <a name="navigationpage-bar-translucency-on-ios"></a>IOS 上的 NavigationPage Bar 半透明度

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定用來變更上導覽列的透明度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，並且會透過將 [`NavigationPage.IsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) 附加屬性設定為值，在 XAML 中使用 `boolean` ：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `NavigationPage.EnableTranslucentNavigationBar` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. EnableTranslucentNavigationBar (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage} ) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來讓巡覽列成為半透明的。 此外， [`NavigationPage`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 也有 [ `DisableTranslucentNavigationBar` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. DisableTranslucentNavigationBar (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage} ) # A3 方法會將巡覽列還原為其預設狀態，而 [ `SetIsNavigationBarTranslucent` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. SetIsNavigationBarTranslucent (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage}、system.string) # A7 方法，可透過呼叫 [ `IsNavigationBarTranslucent` ] (x：，用來切換巡覽列透明度 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. IsNavigationBarTranslucent (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage} ) # A11 方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

結果是可以變更導覽列的透明度：

![半透明導覽列平臺特定](navigation-bar-translucent-images/translucent-navigation-bar.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)