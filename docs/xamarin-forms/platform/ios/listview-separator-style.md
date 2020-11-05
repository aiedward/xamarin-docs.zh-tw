---
title: IOS 上的 ListView 分隔符號樣式
description: 平臺專屬特性可讓您使用僅適用于特定平臺的功能，而不需要執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的，控制 ListView 中儲存格之間的分隔符號是否使用 ListView 的完整寬度。
ms.prod: xamarin
ms.assetid: A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 527ae4eee614e1b3c6225c6cd2b220030faa7f3e
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372479"
---
# <a name="listview-separator-style-on-ios"></a>IOS 上的 ListView 分隔符號樣式

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

這個 iOS 平臺特定的控制項，是在中的儲存格之間的分隔符號 [`ListView`](xref:Xamarin.Forms.ListView) 使用的全形 `ListView` 。 它是在 XAML 中使用，方法是將 [`ListView.SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) 附加屬性設定為 [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) 列舉值：

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
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

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

`ListView.On<iOS>`方法指定此平臺特定的只會在 iOS 上執行。 [ `ListView.SetSeparatorStyle` ] (x： Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SetSeparatorStyle (Xamarin.Forms 。IPlatformElementConfiguration { Xamarin.Forms 。PlatformConfiguration Xamarin.Forms 。ListView}、 Xamarin.Forms 。PlatformConfiguration. iOSSpecific. SeparatorStyle) # A3 方法（在 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 命名空間中）是用來控制中的資料格之間的分隔符號是否 [`ListView`](xref:Xamarin.Forms.ListView) 使用的完整寬度 `ListView` ，並 [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) 提供兩個可能值的列舉：

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) –表示預設的 iOS 分隔符號行為。 這是中的預設行為 Xamarin.Forms 。
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) –表示將從的某個邊緣將分隔符號繪製 `ListView` 到另一個邊緣。

結果會將指定的 [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) 值套用至 [`ListView`](xref:Xamarin.Forms.ListView) ，以控制儲存格之間分隔符號的寬度：

![ListView SeparatorStyle Platform-Specific](listview-separator-style-images/listview-separatorstyle.png)

> [!NOTE]
> 分隔符號樣式設定為之後 `FullWidth` ，就無法 `Default` 在執行時間變更回。

## <a name="related-links"></a>相關連結

- [PlatformSpecifics (範例) ](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)