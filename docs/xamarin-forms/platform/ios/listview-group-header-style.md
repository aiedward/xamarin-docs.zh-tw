---
title: IOS 上的 ListView 群組標題樣式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來控制是否在滾動期間浮動 ListView 標頭儲存格。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648317"
---
# <a name="listview-group-header-style-on-ios"></a>IOS 上的 ListView 群組標題樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬控制是否[`ListView`](xref:Xamarin.Forms.ListView)標頭儲存格在滾動期間浮動。 它會在 XAML 中使用，方法是將 `ListView.GroupHeaderStyle` 可系結屬性設定為 `GroupHeaderStyle` 列舉的值：

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

或者，也可以C#使用 Fluent API 來取用：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

@No__t_0 方法指定此平臺特定的只會在 iOS 上執行。 [@No__t_2](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)命名空間中的 `ListView.SetGroupHeaderStyle` 方法是用來控制在滾動期間[`ListView`](xref:Xamarin.Forms.ListView)標頭儲存格是否浮動。 @No__t_0 列舉提供兩個可能的值：

- `Plain` –表示當[`ListView`](xref:Xamarin.Forms.ListView)滾動時，標頭儲存格會浮動（預設值）。
- `Grouped` –表示當[`ListView`](xref:Xamarin.Forms.ListView)滾動時，標頭儲存格不會浮動。

此外，`ListView.GetGroupHeaderStyle` 方法可以用來傳回套用至[`ListView`](xref:Xamarin.Forms.ListView)的 `GroupHeaderStyle`。

結果就是將指定的 `GroupHeaderStyle` 值套用至[`ListView`](xref:Xamarin.Forms.ListView)，以控制標頭儲存格在滾動期間是否浮動：

[![IOS 上浮動和非浮動 ListView 標頭儲存格的螢幕擷取畫面](listview-group-header-style-images/group-header-styles.png "具有浮動和非浮動標頭儲存格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "具有浮動和非浮動標頭儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
