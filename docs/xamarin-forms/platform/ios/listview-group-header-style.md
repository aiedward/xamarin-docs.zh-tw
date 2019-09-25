---
title: IOS 上的 ListView 群組標題樣式
description: 平台特性可讓您使用的功能只可在特定的平台，而不需要實作自訂轉譯器或影響。 本文說明如何使用 iOS 平臺特定的來控制是否在滾動期間浮動 ListView 標頭儲存格。
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "68648317"
---
# <a name="listview-group-header-style-on-ios"></a>IOS 上的 ListView 群組標題樣式

[![下載範例](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

此 iOS 平臺專屬控制在滾動[`ListView`](xref:Xamarin.Forms.ListView)期間，標頭儲存格是否浮動。 它會在 XAML 中使用，方法`ListView.GroupHeaderStyle`是將可系結屬性設定`GroupHeaderStyle`為列舉的值：

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

或者，它可以取用從 C# 使用 fluent API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

`ListView.On<iOS>`方法可讓您指定這個平台專屬只會在 iOS 上執行。 命名空間`ListView.SetGroupHeaderStyle`中的方法是用來控制是否[`ListView`](xref:Xamarin.Forms.ListView)在滾動期間將標頭儲存格浮動。 [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) `GroupHeaderStyle`列舉提供兩個可能的值：

- `Plain`–表示當[`ListView`](xref:Xamarin.Forms.ListView)為滾動時，標頭儲存格會浮動（預設值）。
- `Grouped`–表示當滾動時[`ListView`](xref:Xamarin.Forms.ListView) ，標頭儲存格不會浮動。

此外， `ListView.GetGroupHeaderStyle`方法可以用來`GroupHeaderStyle`傳回套用至[`ListView`](xref:Xamarin.Forms.ListView)的。

結果是指定`GroupHeaderStyle`的值會套用[`ListView`](xref:Xamarin.Forms.ListView)至，以控制在滾動期間是否將標頭儲存格浮動：

[![IOS 上浮動和非浮動 ListView 標頭儲存格的螢幕擷取畫面](listview-group-header-style-images/group-header-styles.png "具有浮動和非浮動標頭儲存格的 ListView")](listview-group-header-style-images/group-header-styles-large.png#lightbox "具有浮動和非浮動標頭儲存格的 ListView")

## <a name="related-links"></a>相關連結

- [PlatformSpecifics （範例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [建立平台特性](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
