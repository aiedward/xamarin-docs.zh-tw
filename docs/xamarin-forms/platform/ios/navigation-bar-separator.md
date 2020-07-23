---
title: IOS 上的 NavigationPage Bar 分隔符號
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來隱藏 NavigationPage 上巡覽列底部的分隔線和陰影。
ms.prod: xamarin
ms.assetid: 5A45748A-6779-4441-82F2-415BD68473B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c74f9bd17f8da5cd41a55f8b35a66e21df81a8d2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931179"
---
# <a name="navigationpage-bar-separator-on-ios"></a>IOS 上的 NavigationPage Bar 分隔符號

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會隱藏位於上巡覽列底部的分隔線和陰影 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 將可系結屬性設定為，即可在 XAML 中使用它 [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) `false` ：

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`NavigationPage.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `NavigationPage.SetHideNavigationBarSeparator` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. SetHideNavigationBarSeparator （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。NavigationPage}，system.string）方法，在命名空間中， [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制是否隱藏導覽列分隔符號。 此外，[ `NavigationPage.HideNavigationBarSeparator` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. NavigationPage. HideNavigationBarSeparator （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。NavigationPage}））方法可以用來傳回是否隱藏導覽列分隔符號。

結果是，上的巡覽列分隔符號 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 可以隱藏起來：

![NavigationPage 巡覽列已隱藏](navigation-bar-separator-images/navigationpage-hideseparatorbar.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
