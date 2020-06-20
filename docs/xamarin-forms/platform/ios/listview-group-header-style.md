---
title: IOS 上的 ListView 群組標題樣式
description: 平臺詳細資訊可讓您使用僅在特定平臺上提供的功能，而不需執行自訂轉譯器或效果。 本文說明如何使用 iOS 平臺特定的來控制是否在滾動期間浮動 ListView 標頭儲存格。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46e8dec3d5644defdeb8a2265a73815adfde92d8
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136028"
---
# <a name="listview-group-header-style-on-ios"></a>IOS 上的 ListView 群組標題樣式

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬控制 [`ListView`](xref:Xamarin.Forms.ListView) 在滾動期間，標頭儲存格是否浮動。 它會在 XAML 中使用，方法是將可系結 `ListView.GroupHeaderStyle` 屬性設定為列舉的值 `GroupHeaderStyle` ：

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

或者，您也可以使用 Fluent API，從 c # 取用它：

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

`ListView.On<iOS>`方法會指定此平臺特定只會在 iOS 上執行。 `ListView.SetGroupHeaderStyle`命名空間中的方法 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) 是用來控制是否 [`ListView`](xref:Xamarin.Forms.ListView) 在滾動期間將標頭儲存格浮動。 `GroupHeaderStyle`列舉提供兩個可能的值：

- `Plain`–表示當為滾動時，標頭儲存格 [`ListView`](xref:Xamarin.Forms.ListView) 會浮動（預設值）。
- `Grouped`–表示當滾動時，標頭儲存格不會浮動 [`ListView`](xref:Xamarin.Forms.ListView) 。

此外， `ListView.GetGroupHeaderStyle` 方法可以用來傳回套用 `GroupHeaderStyle` 至的 [`ListView`](xref:Xamarin.Forms.ListView) 。

結果是指定的 `GroupHeaderStyle` 值會套用至 [`ListView`](xref:Xamarin.Forms.ListView) ，以控制在滾動期間是否將標頭儲存格浮動：

[![IOS 上浮動和非浮動 ListView 標頭儲存格的螢幕擷取畫面](listview-group-header-style-images/group-header-styles.png "具有浮動和非浮動標頭儲存格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "具有浮動和非浮動標頭儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
