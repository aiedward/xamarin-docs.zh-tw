---
title: IOS 上的 ListView 資料列動畫
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，控制在更新 ListView 專案集合時是否停用資料列動畫。
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f64dcd0bfcd68f7e5145ce8191dcd4a580fb2a6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372505"
---
# <a name="listview-row-animations-on-ios"></a>IOS 上的 ListView 資料列動畫

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定的控制在更新專案集合時是否停用資料列動畫 [`ListView`](xref:Xamarin.Forms.ListView) 。 它是在 XAML 中使用，方法是將可系結 `ListView.RowAnimationsEnabled` 屬性設定為 `false` ：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

或者，您也可以使用流暢的 API，從 c # 中使用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

`ListView.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 在 `ListView.SetRowAnimationsEnabled` 命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 可用來控制 [`ListView`](xref:Xamarin.Forms.ListView) 更新專案集合時，是否停用資料列動畫。 此外， `ListView.GetRowAnimationsEnabled` 方法也可以用來傳回是否停用資料列動畫 `ListView` 。

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) 預設會啟用資料列動畫。 因此，當新的資料列插入時，就會發生動畫 `ListView` 。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)