---
title: 在 iOS 上的 NavigationPage Bar 分隔符號
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台特定，它會隱藏分隔線和 NavigationPage 在導覽列底部的陰影。
ms.prod: xamarin
ms.assetid: 5A45748A-6779-4441-82F2-415BD68473B9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a0483a87270a1ca7a2b00f907d5561c865d03541
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209843"
---
# <a name="navigationpage-bar-separator-on-ios"></a>在 iOS 上的 NavigationPage Bar 分隔符號

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台特定隱藏的分隔線和位於底部的瀏覽列的陰影[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)。 它由在 XAML 中設定[ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty)可繫結的屬性，以`false`:

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

或者，它可以取用從 C# 使用 fluent API:

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

`NavigationPage.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `NavigationPage.SetHideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.SetHideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可用來控制是否會隱藏巡覽列分隔符號。 颾魤 ㄛ [ `NavigationPage.HideNavigationBarSeparator` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparator(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.NavigationPage}))方法可以用來傳回是否要隱藏導覽列分隔符號。

結果是瀏覽列分隔符號[ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)可以隱藏：

![](navigation-bar-separator-images/navigationpage-hideseparatorbar.png "隱藏 NavigationPage 導覽列")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
