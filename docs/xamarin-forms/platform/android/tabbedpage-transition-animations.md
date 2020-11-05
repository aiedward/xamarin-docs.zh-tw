---
title: Android 上的 TabbedPage 頁面轉換動畫
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 Android 平臺特定的，以在流覽 TabbedPage 中的頁面時停用轉換動畫。
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4d59807c7bafe8d882777dcebc48292f77639539
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373870"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Android 上的 TabbedPage 頁面轉換動畫

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

當您透過程式設計方式或使用中的索引標籤列，在中流覽頁面時，會使用這個 Android 平臺特定的來停用轉換動畫 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 它是在 XAML 中使用，方法是將可系結 `TabbedPage.IsSmoothScrollEnabled` 屬性設定為 `false` ：

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

`TabbedPage.On<Android>`方法指定此平臺特定的只會在 Android 上執行。 `TabbedPage.SetIsSmoothScrollEnabled`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) 是用來控制在中的頁面之間流覽時，是否要顯示轉換動畫 [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) 。 此外， `TabbedPage` 命名空間中的類別 `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` 也有下列方法：

- `IsSmoothScrollEnabled`，用來抓取在中的頁面之間流覽時，是否會顯示轉換動畫 `TabbedPage` 。
- `EnableSmoothScroll`，在中的頁面之間流覽時，用來啟用轉換動畫 `TabbedPage` 。
- `DisableSmoothScroll`，用來在中的頁面之間流覽時，停用轉換動畫 `TabbedPage` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific. AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)