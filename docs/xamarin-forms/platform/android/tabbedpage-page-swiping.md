---
title: Android 上的 TabbedPage Page 輕刷
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，在 TabbedPage 中的頁面之間啟用以水準手指手勢進行的輕量。
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f90fc5bdc29009584536cd6d4321ed8abfc8b878
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91563402"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Android 上的 TabbedPage Page 輕刷

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 Android 平臺特定的用途是在中的頁面之間，以水準手指手勢來啟用輕量 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它是在 XAML 中使用，方法是將 [`TabbedPage.IsSwipePagingEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) 附加屬性設定為 `boolean` 值：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

`TabbedPage.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 [ `TabbedPage.SetIsSwipePagingEnabled` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled (Xamarin.Forms 。命名空間中的 BindableObject、system.string) # A3 方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 可用來啟用中頁面之間的輕輕 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 此外， `TabbedPage` 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` 也有 [ `EnableSwipePaging` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage} ) # A3 方法，可啟用此平臺特定，而 [ `DisableSwipePaging` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Android、 Xamarin.Forms 。TabbedPage} ) # A7 方法，可停用此平臺特定。 [`TabbedPage.OffscreenPageLimit`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty)附加屬性和 [ `SetOffscreenPageLimit` ] (x： Xamarin.Forms 。PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit (Xamarin.Forms 。BindableObject，system.string) # A3 方法可用來設定在目前頁面任一側的閒置狀態下應保留的頁面數目。

結果是已啟用透過所顯示的頁面滑動分頁 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) ：

![透過 TabbedPage 滑動分頁](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)