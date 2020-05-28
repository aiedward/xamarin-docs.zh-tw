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
ms.openlocfilehash: e76684ffb293380c283153c35c907acc50e40aab
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128072"
---
# <a name="home-indicator-visibility-on-ios"></a>IOS 上的主指示器可見度

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定會設定上主指示器的可見度 [`Page`](xref:Xamarin.Forms.Page) 。 將可系結屬性設定為，即可在 XAML 中使用它 [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) `boolean` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

`Page.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 [ `Page.SetPrefersHomeIndicatorAutoHidden` ] （X： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetPrefersHomeIndicatorAutoHidden （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Page}，system.string）方法，在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中，控制主指示器的可見度。 此外，[ `Page.PrefersHomeIndicatorAutoHidden` ] （x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. PrefersHomeIndicatorAutoHidden （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. iOS、 Xamarin.Forms 。Page}））方法，可以用來抓取主指示器的可見度。

結果是可以控制上的主指示器可見度 [`Page`](xref:Xamarin.Forms.Page) ：

![IOS 頁面上主指示器可見度的螢幕擷取畫面](page-home-indicator-images/home-indicator-visibility.png "Page 首頁指標可見度")

> [!NOTE]
> 此平臺特定可以套用至 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 、 [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) 、 [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) 和 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 物件。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
