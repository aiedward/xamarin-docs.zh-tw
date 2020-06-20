---
title: Android 上的 TabbedPage 頁面輕刷
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以在 TabbedPage 的頁面之間以水準手指手勢進行輕量。
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61c0137788303363769fdec80a16542e2d8bea5e
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128605"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Android 上的 TabbedPage 頁面輕刷

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來在中的頁面之間以水準手指手勢來啟用輕量 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它會在 XAML 中使用，方法是將 [`TabbedPage.IsSwipePagingEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `TabbedPage.SetIsSwipePagingEnabled` ] （X： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled （ Xamarin.Forms 。BindableObject，system.string）方法，在 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 命名空間中，是用來在的頁面之間啟用輕刷 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 此外， `TabbedPage` 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` 也有一個 [ `EnableSwipePaging` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}））方法，可啟用此平臺特定的和 [ `DisableSwipePaging` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging （ Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration. Android、 Xamarin.Forms 。TabbedPage}））方法，可停用此平臺特定。 [`TabbedPage.OffscreenPageLimit`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty)附加屬性和 [ `SetOffscreenPageLimit` ] （x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit （ Xamarin.Forms 。BindableObject，system.string）方法可用來設定在目前頁面的任一端，應保留為閒置狀態的頁面數目。

結果是會啟用透過所顯示之頁面的滑動分頁功能 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
