---
title: IOS 上的 ListView 群組標題樣式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，控制 ListView 標頭資料格是否在滾動時浮動。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b8be4610a327fb9902d62efb061406bff16de484
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557474"
---
# <a name="listview-group-header-style-on-ios"></a>IOS 上的 ListView 群組標題樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺特定的控制 [`ListView`](xref:Xamarin.Forms.ListView) 標頭儲存格是否在滾動時浮動。 它是在 XAML 中使用，方法是將可系結 `ListView.GroupHeaderStyle` 屬性設定為 `GroupHeaderStyle` 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
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

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

`ListView.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 `ListView.SetGroupHeaderStyle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否要在 [`ListView`](xref:Xamarin.Forms.ListView) 滾動時將標頭資料格浮動。 `GroupHeaderStyle`列舉會提供兩個可能的值：

- `Plain` –指出當 (預設) 時，標頭資料格 [`ListView`](xref:Xamarin.Forms.ListView) 會浮動。
- `Grouped` –表示當滾動時，不會浮動標題儲存格 [`ListView`](xref:Xamarin.Forms.ListView) 。

此外， `ListView.GetGroupHeaderStyle` 方法可以用來傳回套用 `GroupHeaderStyle` 至的 [`ListView`](xref:Xamarin.Forms.ListView) 。

結果會將指定的 `GroupHeaderStyle` 值套用至 [`ListView`](xref:Xamarin.Forms.ListView) ，以控制標頭儲存格在滾動時是否為浮點數：

[![IOS 上浮動和非浮動 ListView 標頭儲存格的螢幕擷取畫面](listview-group-header-style-images/group-header-styles.png "具有浮動和非浮動標題儲存格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "具有浮動和非浮動標題儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)