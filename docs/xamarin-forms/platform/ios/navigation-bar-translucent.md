---
title: IOS 上的 NavigationPage Bar 半透明度
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來變更 NavigationPage 中巡覽列的透明度。
ms.prod: xamarin
ms.assetid: 1150941B-56DB-4781-BE36-A4C4F9F2C500
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d30e1f86e2ef22250b9e25e2352501b6f2c05ade
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939135"
---
# <a name="navigationpage-bar-translucency-on-ios"></a>IOS 上的 NavigationPage Bar 半透明度

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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

![半透明的導覽列平臺特定](navigation-bar-translucent-images/translucent-navigation-bar.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
