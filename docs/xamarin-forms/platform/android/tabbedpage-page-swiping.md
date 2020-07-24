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
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 0043d90e631c19a55b766a877a9cd30316f14650
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997373"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Android 上的 TabbedPage 頁面輕刷

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 Android 平臺特定是用來在中的頁面之間以水準手指手勢來啟用輕量 [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) 。 它會在 XAML 中使用，方法是將 [`TabbedPage.IsSwipePagingEnabled`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`方法會指定此平臺特定僅在 Android 上執行。 [ `TabbedPage.SetIsSwipePagingEnabled` ] （X： :::no-loc(Xamarin.Forms)::: 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled （ :::no-loc(Xamarin.Forms)::: 。BindableObject，system.string）方法，在 [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) 命名空間中，是用來在的頁面之間啟用輕刷 [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) 。 此外， `TabbedPage` 命名空間中的類別 `:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific` 也有一個 [ `EnableSwipePaging` ] （x： :::no-loc(Xamarin.Forms)::: 。PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging （ :::no-loc(Xamarin.Forms)::: 。IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: 。PlatformConfiguration. Android、 :::no-loc(Xamarin.Forms)::: 。TabbedPage}））方法，可啟用此平臺特定的和 [ `DisableSwipePaging` ] （x： :::no-loc(Xamarin.Forms)::: 。PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging （ :::no-loc(Xamarin.Forms)::: 。IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: 。PlatformConfiguration. Android、 :::no-loc(Xamarin.Forms)::: 。TabbedPage}））方法，可停用此平臺特定。 [`TabbedPage.OffscreenPageLimit`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty)附加屬性和 [ `SetOffscreenPageLimit` ] （x： :::no-loc(Xamarin.Forms)::: 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit （ :::no-loc(Xamarin.Forms)::: 。BindableObject，system.string）方法可用來設定在目前頁面的任一端，應保留為閒置狀態的頁面數目。

結果是會啟用透過所顯示之頁面的滑動分頁功能 [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) ：

![透過 TabbedPage 滑動分頁](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
