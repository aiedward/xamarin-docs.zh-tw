---
title: Android 上的 NavigationPage Bar 高度
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以在 NavigationPage 上設定巡覽列的高度。
ms.prod: xamarin
ms.assetid: C8A73B64-FE70-408A-A72E-8AF147F0C52C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4704625955cc1b487efe00f41d3d9b9e2a41413b
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367084"
---
# <a name="navigationpage-bar-height-on-android"></a>Android 上的 NavigationPage Bar 高度

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定會設定上導覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 它是在 XAML 中使用，方法是將可系結 [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) 屬性設定為整數值：

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

`NavigationPage.On<Android>`方法指定此平臺特定的只會在應用程式相容性 Android 上執行。 [ `NavigationPage.SetBarHeight` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. SetBarHeight (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。NavigationPage} 是命名空間中的}、system.string) # A3 方法， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) 可用來設定上導覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 此外，[ `NavigationPage.GetBarHeight` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. GetBarHeight (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。NavigationPage} ) # A3 方法可以用來傳回中巡覽列的高度 `NavigationPage` 。

結果是可以設定上巡覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ：

![NavigationPage 巡覽列高度](navigationpage-bar-height-images/navigationpage-barheight.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)