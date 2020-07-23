---
title: Android 上的 NavigationPage 列高度
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的來設定 NavigationPage 上的巡覽列高度。
ms.prod: xamarin
ms.assetid: C8A73B64-FE70-408A-A72E-8AF147F0C52C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5f5e6311c79a88a6018526a2e1c0c06065eefb32
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929736"
---
# <a name="navigationpage-bar-height-on-android"></a>Android 上的 NavigationPage 列高度

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定會設定上巡覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 將可系結 [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) 屬性設定為整數值，即可在 XAML 中使用它：

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

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

`NavigationPage.On<Android>`方法會指定此平臺特定只會在應用程式相容性 Android 上執行。 [ `NavigationPage.SetBarHeight` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. SetBarHeight （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。NavigationPage}，system.string）方法，在命名空間中， [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) 是用來設定上巡覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 。 此外，[ `NavigationPage.GetBarHeight` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. GetBarHeight （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。NavigationPage}））方法可以用來傳回中導覽列的高度 `NavigationPage` 。

結果是可以設定上巡覽列的高度 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) ：

![NavigationPage 巡覽列高度](navigationpage-bar-height-images/navigationpage-barheight.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
