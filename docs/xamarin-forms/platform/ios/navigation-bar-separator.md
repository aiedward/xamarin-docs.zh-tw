---
title: IOS 上的 NavigationPage Bar 分隔符號
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，隱藏 NavigationPage 上巡覽列底部的分隔線和陰影。
ms.prod: xamarin
ms.assetid: 5A45748A-6779-4441-82F2-415BD68473B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 44bd1ca75220d0aefe2f26bf0e3ba1afcde93150
ms.sourcegitcommit: 40a56bbc1e038a9181101580ad18a4584edb5ab0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "93372349"
---
# <a name="navigationpage-bar-separator-on-ios"></a>IOS 上的 NavigationPage Bar 分隔符號

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會隱藏位於上巡覽列底部的分隔線和陰影 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 它是在 XAML 中使用，方法是將可系結 [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) 屬性設定為 `false` ：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

`NavigationPage.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `NavigationPage.SetHideNavigationBarSeparator` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. SetHideNavigationBarSeparator (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage} 是命名空間中的}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制是否隱藏導覽列分隔符號。 此外，[ `NavigationPage.HideNavigationBarSeparator` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. HideNavigationBarSeparator (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。NavigationPage} ) # A3 方法可以用來傳回是否隱藏導覽列分隔符號。

結果是可以隱藏上的導覽列分隔符號 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ：

![隱藏 NavigationPage 巡覽列](navigation-bar-separator-images/navigationpage-hideseparatorbar.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)