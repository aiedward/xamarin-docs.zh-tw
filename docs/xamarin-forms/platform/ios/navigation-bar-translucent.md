---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: be7e95df0ac247c5cd97c1715e4af67b48275180
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128111"
---
# <a name="navigationpage-bar-translucency-on-ios"></a>IOS 上的 NavigationPage Bar 半透明度

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定是用來變更上導覽列的透明度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ，並藉由將 [`NavigationPage.IsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) 附加屬性設定為值，在 XAML 中使用 `boolean` ：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

`NavigationPage.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `NavigationPage.EnableTranslucentNavigationBar` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. EnableTranslucentNavigationBar （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。NavigationPage}））方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來使導覽列變成半透明。 此外， [`NavigationPage`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.iOSSpecific` 也有一個 [ `DisableTranslucentNavigationBar` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. DisableTranslucentNavigationBar （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。NavigationPage}））方法，可將導覽列還原為其預設狀態，並將 [ `SetIsNavigationBarTranslucent` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. SetIsNavigationBarTranslucent （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。NavigationPage}，system.string）方法，可以藉由呼叫 [ `IsNavigationBarTranslucent` ] （x：，用來切換導覽列透明度 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. IsNavigationBarTranslucent （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。NavigationPage}））方法：

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

結果是可以變更導覽列的透明度：

![](navigation-bar-translucent-images/translucent-navigation-bar.png "Translucent Navigation Bar Platform-Specific")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
