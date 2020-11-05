---
title: IOS 上的主指示器可見度
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，在頁面上設定主指示器的可見度。
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 49fd8c563509d88b80fcb91b5e6298b8a5bb3363
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372297"
---
# <a name="home-indicator-visibility-on-ios"></a>IOS 上的主指示器可見度

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會設定上主指示器的可見度 [`Page`](xref:Xamarin.Forms.Page) 。 它是在 XAML 中使用，方法是將可系結 [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) 屬性設定為 `boolean` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

`Page.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetPrefersHomeIndicatorAutoHidden (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Page}、system.string) # A3 方法，在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中，控制 home 指標的可見度。 此外，[ `Page.PrefersHomeIndicatorAutoHidden` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. PrefersHomeIndicatorAutoHidden (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。Page} ) # A3 方法可以用來取得首頁指標的可見度。

結果是可以控制上主指示器的可見度 [`Page`](xref:Xamarin.Forms.Page) ：

![IOS 頁面上家庭指標可見度的螢幕擷取畫面](page-home-indicator-images/home-indicator-visibility.png "Page 首頁指標可見度")

> [!NOTE]
> 這個平臺特定的可套用至 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 、 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 、 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 物件。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)