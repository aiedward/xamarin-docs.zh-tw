---
title: 在 iOS 上的主要指標可見性
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 這篇文章說明如何使用 iOS 平台專屬的設定頁面上的主要指示器的可見性。
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: c0d6717cd8f89344be7df3dc3ec687a5f1b375f4
ms.sourcegitcommit: 482aef652bdaa440561252b6a1a1c0a40583cd32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2019
ms.locfileid: "65971620"
---
# <a name="home-indicator-visibility-on-ios"></a>在 iOS 上的主要指標可見性

[![下載範例](~/media/shared/download.png)下載範例](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

此 iOS 平台特定設定的可見性的主要指示器[ `Page` ](xref:Xamarin.Forms.Page)。 它由在 XAML 中設定[ `Page.PrefersHomeIndicatorAutoHidden` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty)可繫結的屬性，以`boolean`:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

`Page.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 [ `Page.SetPrefersHomeIndicatorAutoHidden` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.SetPrefersHomeIndicatorAutoHidden(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Page},System.Boolean))方法，請在[ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間，可控制主指標的可見性。 颾魤 ㄛ [ `Page.PrefersHomeIndicatorAutoHidden` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHidden(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Page}))方法可用來擷取主指標的可見性。

結果是上的主要指示器的可見度[ `Page` ](xref:Xamarin.Forms.Page)可以控制：

![在 [iOS] 頁面上的主要指標可見性的螢幕擷取畫面](page-home-indicator-images/home-indicator-visibility.png "頁面主指標的可見性")

> [!NOTE]
> 此平台專屬可以套用至[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)， [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage)， [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage)，以及[ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage)物件。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
